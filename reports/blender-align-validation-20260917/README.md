# Blender-align Windows 渲染验证（2026-09-17）

测试基线为 LongMarch `42d5d74cc5bf53c7e5aa9144844399a9a0753569`，assets 为 `07f7bd6670d836afe93e0a0f26caec913a19bca6`。**以下最终结果包含两项尚未提交／推送到 PR 的本地修复，不代表未修复 PR HEAD 的全部结果。** 本报告分支仅发布报告和图像。

环境：Windows、RTX 3090 Ti、NVIDIA 596.49、MSVC 19.44、Vulkan SDK 1.4.321.0，Release。构建使用 `CL=/D_USE_MATH_DEFINES` 适配现有 hair 代码的 `M_PI`。

## Sparkium 框架层面的修改汇总

以下按代码层次汇总，区分 **PR HEAD 已有实现** 与 **本轮验证的未提交本地改动**。PR 增量以 `cf2faf940f5eeeb014cacbf3ce9a403ab1ad41b6...42d5d74cc5bf53c7e5aa9144844399a9a0753569` 为准；已在 main 中的 Ray Query 基础设施不重复计作本 PR 新增。

| 层次 | PR 已有的修改 | 对框架行为的影响 |
|---|---|---|
| 后端能力与管线选择 | D3D12 按 DXR Tier 1.1 检测 Ray Query；Vulkan 接入设备的 Ray Query 支持检测。旧场景显式请求 RT、设备不支持完整 RT 时，优先转为 Ray Query | 保持旧场景可用，并尽可能采用原生遍历。Auto 的 RT → Ray Query → Fallback 顺序属于已有基础设施 |
| 场景与几何接口 | JSON 加载器扩展 Blender 场景数据，增加 `SPKMESH1` 二进制网格、`SPKHAIR1` 毛发数据读取与校验；增加 Hair 几何并三角化，传递顶点颜色等属性 | 将导出数据纳入统一的 Scene／Geometry／Material 对象体系，供不同追踪路径使用 |
| 材质图与编译组织 | 新增 Shader Graph 材质及节点到 HLSL 的生成；通过 `GraphSurface` 表达求值结果。计算追踪路径把各材质图求值与共享的 `SampleGraphSurface` BSDF 采样分离，无材质图时保留紧凑分派 | 避免每个材质图都复制完整 BSDF 采样实现，减少生成代码与编译负担；Ray Query 和 Fallback 复用该组织方式 |
| 着色与路径状态 | 扩展 Principled／材质图着色、透明阴影、薄壁透射、次表面随机游走，以及命中属性与射线类型信息 | 让 RT 与计算追踪路径表达相同的材质与路径语义；是否数值一致由下文矩阵验证，而非仅凭共享代码判断 |
| 光源、相机与成像 | 扩展光源参数与采样；Camera 增加光圈半径、焦距和光圈形状；Film 增加显示变换、曝光、gamma、对比度及 tone-mapping 参数缓冲 | 将 Blender 导出的光照、景深和显示设置接入渲染框架，区分线性辐亮度积累与最终显示输出 |

主要代码入口：[场景加载与材质图生成](https://github.com/LazyJazzDev/LongMarch/blob/42d5d74cc5bf53c7e5aa9144844399a9a0753569/code/sparkium/scene_io/json_scene.cpp)、[计算追踪材质编译](https://github.com/LazyJazzDev/LongMarch/blob/42d5d74cc5bf53c7e5aa9144844399a9a0753569/code/sparkium/pipelines/raytracing/core/software_pipeline.cpp)、[共享材质图采样](https://github.com/LazyJazzDev/LongMarch/blob/42d5d74cc5bf53c7e5aa9144844399a9a0753569/code/sparkium/shaders/material/shader_graph/surface_sampler.hlsli)、[次表面随机游走](https://github.com/LazyJazzDev/LongMarch/blob/42d5d74cc5bf53c7e5aa9144844399a9a0753569/code/sparkium/shaders/subsurface_random_walk.hlsli)。本节描述实现范围，不增加 Metal 或其他设备的 Windows 验证结论，也不声称 Ray Query 在任意场景都比完整 RT 更快。

| 本轮本地改动（尚未提交到 PR） | 修改位置与机制 | 验证用途 |
|---|---|---|
| 稳定实体注册顺序 | `core/scene.h/.cpp` 增加 `GetEntityOrder()` 和注册顺序数组；首次插入时追加，删除时同步移除。RT 与 raster 场景适配层按该顺序更新后端实体，保留原 map 的查询与缓存用途 | 消除地址顺序引起的实例／灯光排序及灯光 CDF 随机数映射变化；新增反转实体地址顺序的回归测试 |
| 非阻挡灯光的阴影语义 | 灯光 sampler 增加 `SAMPLE_SHADOW_ANY_HIT`／`SampleShadowOpacity`；RT core 编译缓存 `mesh_light_shadow_ahit`，实体层绑定到灯光 shadow hit group。非阻挡灯光在 any-hit 中继续遍历，阻挡命中将可见度归零 | 使完整 RT 正确发现非阻挡灯光后方的遮挡物，与计算路径已有 opacity 接口一致；使用遮挡、无遮挡及阻挡灯光三组控制场景验证 |
| 线性输出与回归工具 | CLI 增加 `--checkpoint-dir`，在 2 的幂次及最终帧输出显示变换前的线性 RGBA float32；GPU 测试增加后端／debug 环境选择；增加 `check_render_matrix.py` 与 `render_matrix_fixtures.py` | 检查实际管线、原生查询计数、有限像素、重复性和分批积累，并将执行成功与 HDR 收敛诊断分开记录 |

上述 raster 实体顺序适配属于已存在的本地一致性修复，不表示光栅化材质图崩溃已修复。Fallback 和光栅化失败继续按用户要求暂缓；本次报告更新仅发布文字，未提交或推送这些渲染器源码改动。

## 结果

| 范围 | 执行结果 |
|---|---:|
| 21 场景主矩阵：D3D12/Vulkan × RT/Ray Query | 84/84 |
| RT/Ray Query（含高采样、重复性等补测） | 136/136 |
| GPU 测试 | D3D12 16/16；Vulkan 16/16 |
| Fallback（含补测） | 67/68 |
| 光栅化补充检查 | 12/14 |
| 全部执行检查 | 215/218 |

215 个成功输出均为有限、非负像素；PNG 显示变换与 CPU 参考相差不超过一个 8-bit 色阶。203 个成功的追踪运行通过实际管线路径检查。六种追踪组合在重复启动、256 spp 单次提交与 16×16 spp 分批提交的对照中均逐位一致。

## 本地修复发现

1. 实体／灯光原先按指针地址遍历，内存分配顺序改变灯光采样 CDF。改为稳定注册顺序，并加入故意反转地址顺序的回归测试。
2. 完整 RT 的阴影射线遇到 `block_ray=false` 灯光面后提前结束，漏掉其后的遮挡物。改用灯光 shadow any-hit，让非阻挡灯光继续遍历。最小复现场景修复前 RT 错误产生平均 RGB 0.07009，Query/Fallback 正确为零；修复后六路均为零，并测试无后方遮挡物、灯光自身阻挡两个控制情况。

![Monster RT 阴影修复前后](shadow-fix.png)

Monster 1024 spp 下，D3D12 RT/Query 通道均值偏差从 1.698% 降至 0.025%；修复后 Vulkan 对应偏差为 0.078%。

## 图像与收敛

![三个 Blender 场景四个原生追踪组合](blender-native-comparison.png)

图像为 48×48、1024 spp 的结果，使用最近邻放大展示，不增加原始细节。Monster 保留 29 材质／49 实体；Classroom 保留 79 材质／930 实体；Junkshop 保留 46 材质／55 实体。

![最坏配对误差随采样下降](convergence-focus.png)

图中统计所有有完整输出的配对；Classroom 不包含失败的 D3D12 Fallback。分块误差是 8×8 像素均值的 MAE／对称平均辐亮度，RGB RMSE 使用对称 RMS 辐亮度归一化。

| 场景 | spp | 最坏通道均值偏差 | 最坏分块差异 |
|---|---:|---:|---:|
| Monster | 1024 | 0.613% | 1.396% |
| Classroom（5 个完成组合） | 1024 | 0.755% | 1.652% |
| Junkshop | 1024 | 1.153% | 2.384% |
| 材质图压力场景 | 32768 | 0.402% | 0.856% |

已测成功组合的误差曲线支持共同收敛目标；仍存在 Monte Carlo 噪声。材质图压力场景的分块差异由 4096 spp 的 2.650% 降到 32768 spp 的 0.856%。覆盖背景、点／面光源、PBR 与纹理、镜面、材质图、透明阴影、负／非均匀缩放、coat／anisotropy／sheen、毛发、薄透镜、薄壁透射与次表面随机游走，并补测关闭截断的情况。

## 按用户要求暂缓的问题

- Classroom / D3D12 / Fallback：退出码 2170，第一帧前退出，无图像；原因未确认，暂不继续排查或修复。
- graph_smoke / D3D12 与 Vulkan / 光栅化：均退出 3221225477；光栅化问题同样暂缓。

这些项目是失败项，未计入通过或收敛结论。有限的 48×48／64×64 验证不保证任意场景或生产分辨率已完全收敛，也不是 Blender Cycles 图像等价或其他 GPU 的验证。

证据：[分组数值](summary.json)、[基线与二进制／源文件哈希](manifest.json)、[GPU 测试记录](gpu-tests.json)。完整逐帧浮点数据、运行日志和本地补丁保存在工作区 `out/render-matrix/final/`。

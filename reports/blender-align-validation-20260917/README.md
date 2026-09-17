# Blender-align Windows 渲染验证（2026-09-17）

测试基线为 LongMarch `42d5d74cc5bf53c7e5aa9144844399a9a0753569`，assets 为 `07f7bd6670d836afe93e0a0f26caec913a19bca6`。**以下最终结果包含两项尚未提交／推送到 PR 的本地修复，不代表未修复 PR HEAD 的全部结果。** 本报告分支仅发布报告和图像。

环境：Windows、RTX 3090 Ti、NVIDIA 596.49、MSVC 19.44、Vulkan SDK 1.4.321.0，Release。构建使用 `CL=/D_USE_MATH_DEFINES` 适配现有 hair 代码的 `M_PI`。

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

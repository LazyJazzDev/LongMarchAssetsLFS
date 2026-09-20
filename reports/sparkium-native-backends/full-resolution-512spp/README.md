# 四后端全分辨率512 spp测试

源代码：`7e96be02dbcb1cfbdcabc7d86f4fa6ae8ef3538e`；素材：`3e75f4622d22cee5295fbabb831c5dc372bf1ad4`。

[测试设置与版本](provenance.json) · [数值JSON](results.json)

RTX 3090 Ti，Windows Ninja Release。Monster 1024×1024/32反弹；Classroom 1920×1080/16反弹；Junkshop 2000×1000/16反弹。完整场景，每幅64调度×8 spp＝512 spp。

![效率图](performance.png)

| 场景 | 后端 | 实际512spp渲染(s) | 首调度(s) | 后63调度/504spp(s) | 稳定512spp折算(s) | 百万相机样本/s | 吞吐/D3D12 | 进程总耗时约(s) |
|---|---|---|---|---|---|---|---|---|
| Monster | D3D12 Ray Query | 23.220 | 19.262 | 3.958 | 4.021 | 133.53 | 1.000x | 28.1 |
| Monster | Vulkan Ray Query | 98.229 | 92.182 | 6.047 | 6.143 | 87.39 | 0.654x | 104.2 |
| Monster | CUDA 软件 BVH | 242.188 | 67.970 | 174.218 | 176.983 | 3.03 | 0.023x | 248.4 |
| Monster | CUDA OptiX | 98.842 | 56.572 | 42.270 | 42.941 | 12.50 | 0.094x | 104.2 |
| Classroom | D3D12 Ray Query | 82.348 | 62.972 | 19.377 | 19.685 | 53.93 | 1.000x | 88.1 |
| Classroom | Vulkan Ray Query | 277.352 | 254.070 | 23.282 | 23.651 | 44.89 | 0.832x | 282.5 |
| Classroom | CUDA 软件 BVH | 615.158 | 371.935 | 243.223 | 247.083 | 4.30 | 0.080x | 620.9 |
| Classroom | CUDA OptiX | 438.423 | 334.257 | 104.167 | 105.820 | 10.03 | 0.186x | 442.6 |
| Junkshop | D3D12 Ray Query | 36.610 | 32.562 | 4.049 | 4.113 | 248.97 | 1.000x | 54.1 |
| Junkshop | Vulkan Ray Query | 159.521 | 154.768 | 4.753 | 4.828 | 212.09 | 0.852x | 174.2 |
| Junkshop | CUDA 软件 BVH | 288.177 | 91.951 | 196.225 | 199.340 | 5.14 | 0.021x | 306.5 |
| Junkshop | CUDA OptiX | 96.672 | 61.456 | 35.216 | 35.775 | 28.62 | 0.115x | 116.2 |

首帧包含首次编译/构建；稳定窗口为后63帧/504 spp，512 spp值为折算。进程耗时有0–2秒轮询余量。每组合一次独立进程，不是重复实验的统计置信区间。桌面负载与驱动缓存保留。

![画面总览](comparison.png)

| 场景 | 比较 | PNG RMSE | PSNR(dB) | 线性对称NRMSE | 线性均值差(b/a−1) | 32像素块平均RMSE |
|---|---|---|---|---|---|---|
| Monster | D3D12 Ray Query / Vulkan Ray Query | 0.068995 | 23.22 | 0.167194 | +0.029% | 0.002193 |
| Monster | D3D12 Ray Query / CUDA 软件 BVH | 0.068975 | 23.23 | 0.167607 | +0.033% | 0.002229 |
| Monster | D3D12 Ray Query / CUDA OptiX | 0.068982 | 23.23 | 0.167605 | +0.032% | 0.002228 |
| Monster | Vulkan Ray Query / CUDA 软件 BVH | 0.017477 | 35.15 | 0.043511 | +0.005% | 0.000598 |
| Monster | Vulkan Ray Query / CUDA OptiX | 0.017495 | 35.14 | 0.043511 | +0.003% | 0.000592 |
| Monster | CUDA 软件 BVH / CUDA OptiX | 0.005831 | 44.69 | 0.011305 | -0.002% | 0.000196 |
| Classroom | D3D12 Ray Query / Vulkan Ray Query | 0.058639 | 24.64 | 0.061282 | +0.084% | 0.005309 |
| Classroom | D3D12 Ray Query / CUDA 软件 BVH | 0.061142 | 24.27 | 0.066244 | -0.435% | 0.015341 |
| Classroom | D3D12 Ray Query / CUDA OptiX | 0.061137 | 24.27 | 0.066245 | -0.435% | 0.015338 |
| Classroom | Vulkan Ray Query / CUDA 软件 BVH | 0.025501 | 31.87 | 0.032584 | -0.518% | 0.016047 |
| Classroom | Vulkan Ray Query / CUDA OptiX | 0.025456 | 31.88 | 0.032574 | -0.518% | 0.016045 |
| Classroom | CUDA 软件 BVH / CUDA OptiX | 0.007178 | 42.88 | 0.005026 | +0.000% | 0.000229 |
| Junkshop | D3D12 Ray Query / Vulkan Ray Query | 0.035099 | 29.09 | 0.163663 | +0.028% | 0.001089 |
| Junkshop | D3D12 Ray Query / CUDA 软件 BVH | 0.035118 | 29.09 | 0.163737 | +0.031% | 0.001114 |
| Junkshop | D3D12 Ray Query / CUDA OptiX | 0.035104 | 29.09 | 0.163710 | +0.029% | 0.001108 |
| Junkshop | Vulkan Ray Query / CUDA 软件 BVH | 0.010325 | 39.72 | 0.042740 | +0.004% | 0.000336 |
| Junkshop | Vulkan Ray Query / CUDA OptiX | 0.010300 | 39.74 | 0.042575 | +0.002% | 0.000334 |
| Junkshop | CUDA 软件 BVH / CUDA OptiX | 0.006022 | 44.40 | 0.025396 | -0.002% | 0.000190 |

图像误差为后端间比较，不是对Blender/Cycles真值的误差。线性PFM检查有限值；块平均只用于辅助观察低频偏差。本目录发布原尺寸 PNG、逐调度 CSV、GPU 遥测和计算后的线性指标。线性 PFM 留在本地原始测试记录中，未随本次素材发布。

## 完整渲染与实际耗时

![实际512 spp耗时分解](render-cost.png)

![后端两两PNG误差](image-quality.png)

每个后端、场景组合独立启动一个进程，顺序运行，均实际完成 64 次调度。统一使用 `--profile-cpu-only`，以等待 GPU 完成后的 CPU `render_wall` 计时，不含 Film::Develop；首调度包含延迟编译和加速结构准备，但不包含此前的全部加载。进程总耗时另含加载、逐帧显影和导出。驱动缓存未清空，桌面负载未禁用，不能称为严格冷启动实验。

吞吐量为主相机样本，不是包含二次光线的总射线数。一次运行不能提供跨运行置信区间。所有 12 个线性图像均无 NaN/Inf；CUDA/OptiX 结果最接近，但 Classroom 相对 D3D12/Vulkan 仍有约 0.4–0.5% 的线性 RGB 算术均值差，32 像素块平均后仍存在低频差异，不能全部归因于噪声。本报告没有外部 Cycles 真值。

## 原尺寸结果

| 场景 | D3D12 Ray Query | Vulkan Ray Query | CUDA 软件 BVH | OptiX |
|---|---|---|---|---|
| Monster | [d3d12](monster-d3d12.png) | [vulkan](monster-vulkan.png) | [cuda](monster-cuda.png) | [optix](monster-optix.png) |
| Classroom | [d3d12](classroom-d3d12.png) | [vulkan](classroom-vulkan.png) | [cuda](classroom-cuda.png) | [optix](classroom-optix.png) |
| Junkshop | [d3d12](junkshop-d3d12.png) | [vulkan](junkshop-vulkan.png) | [cuda](junkshop-cuda.png) | [optix](junkshop-optix.png) |

## 复现参数

使用 provenance.json 中固定的代码、输入素材和工具版本；从原场景读取完整几何、材质、相机和 Film 设置，保留 8 samples/dispatch，将资源相对路径解析至原场景目录后传入 CLI。四种组合的参数为：

| 后端 | 参数 |
|---|---|
| D3D12 | `--backend d3d12 --pipeline ray_query` |
| Vulkan | `--backend vulkan --pipeline ray_query` |
| CUDA 软件 | `--backend cuda --pipeline rt_fallback` |
| OptiX | `--backend cuda --pipeline ray_tracing --require-hardware-rt` |

共同参数：`--frames 64 --profile-cpu-only --profile <timings.csv> -o <image.png> --linear-output <image.pfm>`。输入场景是位置参数。命令中的输出名是占位符；复现时为每个组合指定不同输出文件。D3D12/Vulkan 的全部 64 次调度均记录 native_ray_query=1，OptiX 全部记录 optix_hardware_traversal=1。

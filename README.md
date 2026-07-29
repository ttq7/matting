# 抠图工具

浏览器端批量抠图工具，基于 ONNX Runtime Web，完全在浏览器内运行。

## 技术栈

- **推理引擎**：ONNX Runtime Web 1.19.2（WASM 后端）
- **抠图模型**：
  - `isnet-anime` — 动漫/二次元插画（1024×1024，167MB）
  - `u2net` — 通用场景（320×320，167MB）
  - `u2netp` — 轻量快速（320×320，4.4MB）
- **前端**：原生 HTML/CSS/JS，无框架
- **依赖库**：
  - `onnxruntime-web` — ONNX 模型推理
  - `jszip` — 打包 ZIP 下载
  - Google Fonts — Noto Serif SC / Inter

## 核心机制

### 模型分卷
GitHub 单文件 100MB 限制，对 isnet-anime / u2net 按 90MB 切分，前端 `fetchPartsWithProgress` 顺序下载并拼接到预分配 `Uint8Array`。

### 浏览器缓存
模型首次下载后存入 IndexedDB，下次秒开。

### 处理流程
1. 图片 → Canvas 绘制 → ImageData 提取
2. 归一化（`np.max` 缩放）→ 减均值除标准差 → `[1,3,H,W]` Float32Array
3. ONNX 推理 → 输出 mask
4. mask 归一化到 0-255 → 缩放到原图尺寸 → 合成 alpha 通道 → PNG Blob

## 部署

GitHub Pages（`/docs` 目录），无需后端，无需构建。

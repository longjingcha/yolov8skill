<div align="center">

# YOLOv8 Skill
**Skill Design** · 将 YOLO 从视频采集、自动化标注、训练、预测到实时展示，
封装为一个完整的 Skill 技能包。通过 AI 自动化，一键生成目标检测工作流。

<img width="3547" height="1182" alt="image" src="https://github.com/user-attachments/assets/eb95bddb-7eb1-4cde-91e8-8d28abd24cdf" />

</div>

`yolov8skill` 是一套面向 Agent 的 YOLO 目标检测技能包，把传统 YOLO 项目中的“摄像头采集 → 数据标注 → 模型训练 → 推理预测 → 结果展示”全流程，封装成 AI 可以直接调用的 Skill。一次配置，处处复用。

---

## 📖 目录

- [核心能力](#-核心能力)
- [项目结构](#-项目结构)
- [环境要求](#-环境要求)
- [安装](#-安装)
- [快速开始](#-快速开始)
- [Skill 使用方式](#-skill-使用方式)
- [自定义模型](#-自定义模型)
- [数据集格式](#-数据集格式)
- [计划路线](#-计划路线)
- [常见问题](#-常见问题)
- [许可证](#-许可证)

---

## ✨ 核心能力

- **实时摄像头采集** — 直接从摄像头捕获画面，作为训练或推理的输入源
- **YOLOv8 目标检测** — 基于 Ultralytics YOLOv8，支持通用目标检测
- **FastSAM 分割** — 集成 FastSAM 快速分割能力，对画面进行像素级分析
- **自定义模型推理** — 支持加载自己训练好的 `best.pt` 模型权重
- **数据集管理** — 内置 `yolo_dataset` 目录，规范数据集存放结构

---

## 📁 项目结构
```text
yolov8skill/
├── skills/
│   └── yolo-camera-skill/      # Skill 定义文件夹（含 SKILL.md）
├── yolo_dataset/               # 数据集目录（images/ labels/ data.yaml）
├── capture_from_camera.py      # 摄像头采集 + 检测脚本
├── yolov8s.pt                  # YOLOv8s 预训练权重
├── FastSAM-s.pt                # FastSAM 分割权重
├── best.pt                     # 自定义训练的最佳模型
└── README.md
```

## 🔧 环境要求
Python 3.8 或更高版本

摄像头设备（用于实时采集）

支持的操作系统：Windows / macOS / Linux

## 📦 安装
### 克隆仓库

```text
git clone https://github.com/yourusername/yolov8skill.git
或者
git clone git@github.com:longjingcha/yolov8skill.git

cd yolov8skill
```

### 创建虚拟环境（推荐）

```
python -m venv venv
source venv/bin/activate   # Linux/macOS
venv\Scripts\activate      # Windows
```
### 安装依赖

```
pip install ultralytics opencv-python
### 下载预训练权重（如未自动下载）
	首次运行时会自动下载 yolov8s.pt，也可手动下载放到根目录
```

## 🚀 快速开始
### 摄像头实时检测
运行以下脚本，即可打开摄像头并进行实时目标检测：

```
python capture_from_camera.py
画面中会实时绘制检测框、类别标签和置信度。按 q 键退出。
```

### 使用自定义模型
将你自己训练好的 best.pt 复制到项目根目录，覆盖原文件，或修改 capture_from_camera.py 中的模型路径：

```
model = YOLO("path/to/your/best.pt")
```
### 分割模式（FastSAM）
如需使用分割，在脚本中加载 FastSAM-s.pt：

```
from ultralytics import FastSAM

model = FastSAM("FastSAM-s.pt")
results = model(source=0, show=True, conf=0.4, device="cpu")
```

## 🤖 Skill 使用方式
在支持 SKILL.md 格式的 Agent 工具（如 Claude Code、Codex、Trae 等）中，Agent 会自动读取 skills/yolo-camera-skill/ 目录下的 Skill 定义，理解项目结构并执行对应的目标检测任务。

你只需要对 Agent 说：

“**用摄像头检测一下我面前有什么东西**”
→ Agent 自动运行 capture_from_camera.py，调起摄像头开始检测。

“**帮我把 yolo_dataset 里的数据训练一轮 YOLOv8**”
→ Agent 会根据 Skill 中定义的训练流程启动训练（需预先配置训练脚本）。

“**用 FastSAM 分割当前画面**”
→ Agent 调用分割模型并返回结果。

### 典型工作流
摄像头采集画面
    │
    ▼
图像预处理
    │
    ▼
YOLOv8 / FastSAM 推理
    │
    ▼
结果可视化 + 实时展示
## 🎛️ 自定义模型
文件	说明
yolov8s.pt	YOLOv8 small 版本，平衡速度与精度
FastSAM-s.pt	Fast Segment Anything Model，快速实例分割
best.pt	用户在特定数据集上微调后的最佳模型
你可以将任何 YOLOv8 格式的权重（如 yolov8n.pt, yolov8m.pt）放入根目录并修改脚本加载。

## 📂 数据集格式
yolo_dataset/ 目录需遵循 YOLO 数据集规范：

```text
yolo_dataset/
├── images/
│   ├── train/
│   └── val/
├── labels/
│   ├── train/
│   └── val/
└── data.yaml
``` 
data.yaml 示例：

```python
path: ./yolo_dataset
train: images/train
val: images/val
nc: 80
names: ['person', 'bicycle', ...]
```

## 🗺️ 计划路线

 - 自动化标注 Script（半监督 + 主动学习） 
 - 一键训练 Skill（传入数据集路径即可启动训练）
 - 模型评估与指标展示（mAP、Precision、Recall）
 - Web 实时推流展示检测结果
 - 多摄像头支持

## 关于作者

| | |
|:---|:---|
| 🌐 官网 | [www.lscript.cn](http://www.lscript.cn)|
| 📺 B站 | [查老师并不渣](https://space.bilibili.com/642180359?spm_id_from=333.337.0.0) |
| 📕 小红书 | [查老师并不渣](https://www.xiaohongshu.com/user/profile/67698a55000000001802adbc) |
| 💬 公众号 | 微信搜「查哥聊AI」 |

关于作者

## ❓ 常见问题
**Q: 摄像头无法打开？**
   A: 检查摄像头是否被其他程序占用，或修改 capture_from_camera.py 中的 cv2.VideoCapture(0) 参数（如改为 1）。

**Q: 检测速度很慢怎么办？**
A: 尝试使用更小的模型如 yolov8n.pt，或降低输入分辨率（在脚本中设置 imgsz=320）。

**Q: 如何停止运行？**
A: 在终端中按 Ctrl+C，或在摄像头窗口中按 q 键。

**Q: 训练脚本在哪里？**
A: 当前版本未包含训练脚本，计划路线中的“一键训练 Skill”将提供该功能。目前可自行使用 ultralytics 的 YOLO.train() 方法。

📄 许可证
MIT License

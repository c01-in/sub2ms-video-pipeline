# Sub-2 ms Video Pipeline: Wi-Fi MAC-Level Bypass to Direct Display

# 亚 2 毫秒视频链路：从 Wi-Fi MAC 层旁路到直驱显示

This repository documents the open writing process of a technical paper proposing an **ultra-low-latency video pipeline** — achieving **< 2 ms** processing delay (from render trigger to first pixel) in controlled devices, excluding network transmission time.

本仓库记录了一篇探索**超低延迟视频链路**的技术论文的开源写作过程——在受控设备中实现**渲染触发到首像素显现低于 2 毫秒**的处理链路延迟（不含网络传输时间）。

---

## 🎯 Goals / 目标

- **MAC-Level Bypass from Wi-Fi to CPU**  
  从 Wi-Fi MAC 层直通 CPU 的旁路机制
- **Zero-Copy Delivery via AF_XDP**  
  使用 AF_XDP 实现用户态零拷贝传输
- **Line-Based Decoding & Direct Display**  
  基于行的解码与显示控制器直扫（GPU 仅在必要时参与）
- **Pipeline Parallelization**  
  将整帧处理拆分为流水执行，最小化首像素延迟
- **Latency ≤ 2 ms (Processing Only)**  
  处理链路延迟压缩至 2 ms 以内

---

## 📄 Paper Outline / 论文提纲

1. **Abstract / 摘要**
2. **Introduction / 引言**
3. **Related Work / 相关工作**
4. **Proposed Architecture / 方案架构**
5. **Experimental Design / 实验设计**
6. **Discussion / 讨论**
7. **Conclusion & Future Work / 结论与展望**
8. **References / 参考文献**

> Full bilingual outline is in [`outline.md`](outline.md)  
> 完整中英双语提纲见 [`outline.md`](outline.md)

---

## 🤝 How to Contribute / 如何参与

1. **Fork** this repo  
   Fork 本仓库
2. **Discuss** via Issues  
   在 Issues 中发起讨论
3. **Pull Requests** for writing, references, figures, or experiments  
   提交 Pull Request 补充写作、参考文献、图表或实验

---

## 📂 Repository Structure / 仓库结构

- `outline.md` — Current bilingual paper outline  
  当前论文中英双语大纲
- `drafts/` — Writing drafts (chronological)  
  写作草稿（按时间顺序）
- `experiments/` — Scripts & data for latency tests  
  实验脚本与延迟测试数据
- `figures/` — Architecture diagrams & charts  
  架构图与数据图表
- `refs/` — References (BibTeX + links)  
  参考文献（BibTeX + 链接）
- `arxiv/` — Submission-ready paper version  
  可投递的最终版本

---

## 📜 License / 许可证

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) for all text & figures.  
文字与图像内容采用 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) 授权。

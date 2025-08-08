# Outline: Sub-2 ms Video Pipeline: Wi‑Fi MAC‑Level Bypass to Direct Display
# 论文大纲：亚 2 毫秒视频链路——从 Wi‑Fi MAC 层旁路到直驱显示

---

## Abstract / 摘要
- 现有无线视频链路总延迟通常在 20–50 ms，有线链路也难低于 10 ms  
- 本文研究的目标是 **不计入网络传输时间**，在受控设备中将“发送端应用层触发渲染 → 接收端首像素显现”的处理链路延迟压缩到 2 ms 以内  
- 核心链路：Wi‑Fi MAC 层旁路 → AF_XDP / kernel bypass → 行级解码 → GPU 直驱显示  
- 涵盖现有研究综述、架构设计、实验路线与应用前景  
- 应用场景：云游戏、VR、工业控制、远程医疗等对延迟极敏感的领域  

---

## 1. Introduction / 引言

### 1.1 Background / 背景
- 云游戏、VR、工业控制等场景中，网络延迟和处理延迟叠加导致响应迟缓  
- 当前优化往往集中在协议或算法层，对 **渲染管线与显示路径** 的整体压缩不足  

### 1.2 Research Goal / 研究目标
- 在受控硬件环境下，去除网络传输时间的前提下，实现处理链路延迟 ≤ 2 ms  
- 重点关注应用层渲染触发、数据搬运、解码、显示等环节  

### 1.3 Contributions / 本文贡献
1. 提出覆盖无线接入、数据搬运、解码与显示全链路的低延迟架构（不含网络传输）  
2. 行级解码与显示直驱结合的流水输出机制  
3. 可复现的延迟测量与原型验证流程  

---

## 2. Related Work / 相关工作

### 2.1 AF_XDP and User‑Space Networking / AF_XDP 与用户态网络旁路
- 研究成果与延迟数据  
- 实测微秒级接收延迟案例  

### 2.2 Wi‑Fi Latency Optimization and TSN / Wi‑Fi 延迟优化与 TSN
- 队列调度优化、固件修改  
- Wi‑Fi 7 的低延迟特性（可作为物理层支持）  

### 2.3 Line‑Based Decode and Direct Display / 行级解码与直显技术（显示控制器直扫，GPU 可选）
- 绕过图形合成层的延迟  
- 显示控制器可直接扫描解码输出缓冲，GPU 仅在必要时参与格式转换  

### 2.4 Latency Analysis and Measurement Tools / 延迟分析与测量工具
- 往返延迟测量工具  
- 渲染管线处理延迟分解方法  

---

## 3. Proposed Architecture / 方案架构

### 3.1 System Overview / 系统总览
- 架构图：应用层渲染触发 → MAC → Zero‑copy → Decoder → Direct Display  
- 明确测量范围：起点为发送端应用层触发渲染，终点为接收端显示控制器缓冲区准备好首像素（不含网络传输时间）  
- 接收端需支持独立 UI plane（硬件 overlay），保证 UI 渲染与视频 plane 解码直显互不影响延迟  

### 3.2 MAC‑Level Bypass / MAC 层旁路
- 固件分流与旁路实现  
- Ethernet / FMUX 模拟验证  

### 3.3 Zero‑Copy in User Space / 用户态零拷贝
- AF_XDP buffer 管理  
- 行级调度策略  

### 3.4 Line‑Based Decode → Display Controller (GPU optional) / 行级解码 → 显示控制器直扫（GPU 可选）
- 避开完整图形合成路径，直接将解码输出缓冲导入显示控制器（KMS plane）  
- GPU 仅在像素格式 / 色域转换等必要场景下参与  
- UI plane 由 GPU 单独输入到 overlay 层，与视频 plane 在显示控制器层硬件合成，避免额外帧缓冲拷贝  

### 3.5 Pipeline Parallelization / 延迟流水化
- 将整帧等待拆分为流水执行，最小化首像素延迟  

---

## 4. Experimental Design / 实验设计

### 4.1 Latency Definition and Measurement / 延迟定义与测量
- 处理链路延迟 = 总延迟 − 网络传输时间  
- 网络传输时间通过 PHY 级或时间戳方法独立测量  
- 解码到显示阶段通过 dma‑buf import + atomic commit 提交，首像素时间测量需与 vblank 或 TE 信号对齐  
- 若存在 UI plane，需分别测量 UI 更新对视频 plane 延迟的影响  

### 4.2 Baseline Measurement / 基线测量
- AF_XDP RX → 用户态 ≤ 1 ms  

### 4.3 Module Verification / 模块验证
- MAC → CPU ≤ 0.5 ms  
- 解码首行 → 输出至显示控制器缓冲区 ≤ 0.5 ms（进入面板之前链路即可稳定 ≤ 2 ms，不依赖面板刷新率）  
- UI plane 更新延迟 ≤ 0.5 ms（单独测量，不影响视频链路）  

### 4.4 Full Pipeline Simulation / 全链路模拟
- Ethernet 原型  
- 延迟分布与瓶颈分析  
- 带 UI overlay 场景的延迟对比实验  

---

## 5. Discussion / 讨论

### 5.1 Technical Challenges / 技术挑战
- 固件闭源、驱动兼容、SoC 限制  
- 硬件 overlay 数量限制，UI plane 更新频率对合成性能的影响  

### 5.2 Potential Optimizations / 潜在优化
- 协议裁剪、协处理器、TSN 特性  
- UI 渲染与提交批处理以降低提交次数  

### 5.3 Application Priority Matrix / 应用优先级矩阵
- 按延迟敏感度排序应用场景  

---

## 6. Conclusion & Future Work / 结论与展望
- 总结创新点与可行性  
- 后续工作方向：Wi‑Fi 7 验证、OEM 合作  

---

## 7. References / 参考文献
- 论文引用（BibTeX）  
- 实验代码链接  

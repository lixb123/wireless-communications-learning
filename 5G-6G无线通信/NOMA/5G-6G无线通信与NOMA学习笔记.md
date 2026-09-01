# 无线通信与 6G 多址技术学习笔记

> 本文整理 FEC、LDPC、星座整形、脉冲成形、RACH、NOMA、SCMA、MUSA、IDMA、RDMA 等概念，并记录相关标准、会议资料和学习资源。

## 1. 总体关系

```text
比特
  ↓
FEC 信道编码
  ↓
调制与星座整形
  ↓
脉冲成形
  ↓
多用户接入 / NOMA
  ↓
RACH 随机接入
  ↓
无线信道
  ↓
匹配滤波 / 多用户检测 / SIC
  ↓
FEC 译码
  ↓
恢复比特
```

可以把无线资源理解为一条公路，把用户理解为车辆：

- 调制决定信息如何编码成信号；
- 脉冲成形决定信号在时间上的波形；
- FEC 给数据增加抗错误能力；
- 多址接入规定多个用户如何共享资源；
- 接收机负责从混合信号中识别各个用户。

## 2. FEC：前向纠错

FEC 是 **Forward Error Correction，前向纠错**。发送端在原始数据中加入冗余校验信息，接收端即使收到少量错误比特，也可以利用这些冗余恢复原始数据，而不必等待重传。

无线传输中的噪声、衰落、多径、干扰和遮挡都会导致比特错误。FEC 的代价是增加冗余和译码计算量，收益是降低误码率、减少重传、提高可靠性。

5G NR 中常见的信道编码包括：

- **LDPC**：主要用于数据信道；
- **Polar**：主要用于控制信道。

## 3. LDPC 与 BG1/BG2/BG3

LDPC 使用稀疏校验矩阵。实际生成大型校验矩阵前，通常先定义一个较小的结构，称为 **Base Graph，基础图**。

5G NR 标准中有两个基础图：

| 基础图 | 常见适用情况 |
|---|---|
| BG1 | 较大的传输块、较高码率、较高吞吐量 |
| BG2 | 较小的传输块、较低码率、较强纠错需求 |

系统会根据传输块大小、目标码率等条件选择 BG1 或 BG2，再通过提升因子扩展为实际校验矩阵。

**BG3** 不是当前 5G NR 的通用标准基础图。6G 论文可能提出 BG3 或更多基础图，用于短码、超低时延、太赫兹或低功耗场景，具体含义必须以论文定义为准。

## 4. 调制、星座图与星座整形

调制把比特映射成复数符号。例如：

- QPSK：每个符号携带 2 bit；
- 16-QAM：每个符号携带 4 bit；
- 64-QAM：每个符号携带 6 bit；
- 256-QAM：每个符号携带 8 bit。

把调制符号画在复平面上，就是星座图。

### 星座整形

**星座整形（Constellation Shaping）**优化星座点的位置或出现概率，使系统在给定平均发射功率下更接近信道容量。

常见方式：

- **概率星座整形 PCS**：星座点位置不变，内层低能量点出现得更频繁；
- **几何星座整形 GCS**：改变星座点的位置。

星座整形优化的是复平面上的符号分布，不是时间波形。

## 5. 脉冲成形

**脉冲成形（Pulse Shaping）**决定一个调制符号在时间上的波形。

如果直接使用矩形脉冲，频谱旁瓣较大，容易泄漏到相邻信道。脉冲成形滤波器可以：

- 限制带宽；
- 减少频谱泄漏；
- 满足奈奎斯特准则；
- 降低符号间干扰 ISI。

常见滤波器：

- 升余弦滤波器；
- 根升余弦滤波器 RRC；
- 高斯滤波器；
- OFDM 中的窗口化滤波。

工程中常见做法是发送端使用一个 RRC 滤波器，接收端使用匹配的 RRC 滤波器，两者级联后形成升余弦响应。

区别可以记为：

```text
星座整形：优化复平面上的点
脉冲成形：优化时间轴上的波形
```

## 6. 多址接入与 NOMA

多址接入解决的是多个用户如何共享时间、频率、码和功率资源。

传统正交多址中，用户尽量使用互不重叠的资源，例如不同时间片或不同频段。NOMA 是 **Non-Orthogonal Multiple Access，非正交多址**，允许多个用户在相同或重叠资源上发送，再由接收机分离。

### 功率域 NOMA

功率域 NOMA 让不同用户使用不同的发射功率。两个用户的信号可以写成：

```text
x = √a1 · x1 + √a2 · x2
```

接收端通常使用 SIC：

1. 先检测一个用户；
2. 重构该用户的信号；
3. 从接收信号中减掉它；
4. 再检测另一个用户。

优点是允许用户共享资源，潜在提高资源利用率。问题包括功率分配、信道估计、接收机复杂度和 SIC 错误传播。

DOCOMO 在 2013 年前后推动的 NOMA 研究，通常主要指这种功率域 NOMA。NOMA 是候选无线接入技术，不是 DOCOMO 私有的商业产品。

## 7. SIC 与 MUD

### SIC

SIC 是 **Successive Interference Cancellation，连续干扰消除**。它是顺序式检测：先识别一个用户，再消除该用户，接着识别下一个用户。

如果前面某个用户判断错误，错误信号会被错误地减掉，从而影响后续用户，这称为错误传播。

### MUD

MUD 是 **Multi-User Detection，多用户检测**。它把多个用户作为一个整体来检测，利用用户之间的功率、信道、码、交织器或资源图样差异，从混合信号中恢复各用户数据。

可以简单区分为：

```text
SIC：一个一个检测
MUD：联合考虑多个用户
```

## 8. RACH 与 PRACH

RACH 是 **Random Access Channel，随机接入信道**。终端在还没有正式上行资源时，先通过随机接入向基站“敲门”。常见用途包括：

- 手机开机入网；
- 小区切换；
- 连接恢复；
- 上行同步；
- 物联网终端接入。

PRACH 是 **Physical Random Access Channel，物理随机接入信道**，通常用于发送随机接入前导。

### 4-step RACH

```text
Msg1：UE 发送随机接入前导
Msg2：基站发送随机接入响应
Msg3：UE 发送正式上行消息
Msg4：基站进行竞争解决
```

### 2-step RACH

```text
MsgA：PRACH 前导 + PUSCH 小数据
MsgB：基站响应
```

“把 small data 塞进去”准确地说，是在与 MsgA 关联的 PUSCH 资源上顺便携带一小段上行数据，不是把数据直接塞进 PRACH 前导。

2-step RACH 适合状态上报、短控制消息、物联网小数据和连接恢复等场景。

2-step RACH 与 NOMA 不是同一个概念：

- 2-step RACH：优化接入流程，减少交互次数；
- NOMA：允许多个用户共享或叠加资源；
- MUD/SIC：在接收端分离叠加用户。

## 9. Small Data 与 Grant-free

Small Data 指数据量很小的业务，例如温度、门磁状态、设备心跳或简短控制请求。

如果每次发送几十个字节都完整建立连接，信令开销可能比数据本身还大，因此系统希望减少握手、调度和等待过程。

Grant-free 是免授权接入：终端使用预配置资源直接发送，不必每次先等待基站调度。

典型关系是：

```text
Grant-free：终端直接发送
NOMA：多个终端可能重叠发送
MUD/SIC：基站把终端分开
FEC：纠正残余传输错误
```

## 10. SCMA、MUSA、IDMA、RDMA

| 技术 | 全称 | 主要区分维度 | 常见接收方式 |
|---|---|---|---|
| SCMA | Sparse Code Multiple Access | 稀疏码本 | 消息传递算法 |
| MUSA | Multi-User Shared Access | 低互相关扩频序列 | SIC、MMSE、MUD |
| IDMA | Interleave Division Multiple Access | 用户专属交织器 | 迭代检测译码 |
| RDMA | 需看原文定义 | 资源模式或随机选择 | 多用户检测 |

### SCMA

SCMA 是稀疏码多址。一个用户只使用部分资源，例如 4 个资源中只占用其中 2 个，形成稀疏结构。用户数据被直接映射成多维稀疏码字，而不是简单的普通符号加扩频。

SCMA 常与华为/海思的 5G 候选方案联系，但不是华为专属的现行标准。

### MUSA

MUSA 是多用户共享接入。每个用户使用一条低互相关序列进行扩频，多个用户叠加后，接收端通过 SIC、MMSE 或其他多用户检测方法恢复数据。

MUSA 常与中兴提出的候选方案联系，但也不是现行 5G NR 的独占技术。

### IDMA

IDMA 是交织分多址。每个用户使用不同的交织器，把比特按照不同规则打乱。接收端知道这些交织规则，通过多用户检测和 FEC 译码反复交换软信息。

“IMDA”很可能是“IDMA”的笔误，但应以原始资料中的全称为准。

### RDMA

RDMA 在不同资料中的含义不统一，可能指：

- **Resource Division Multiple Access**：资源划分多址；
- **Random Division Multiple Access**：随机划分多址。

前者强调用户使用不同资源模式，后者强调用户随机选择签名、时频资源或发送机会。数据中心语境中的 RDMA 通常是 Remote Direct Memory Access，与无线多址无关。

## 11. 2006 年 RACH 多用户检测思路

如果早期论文和专利是在 RACH 中引入多用户检测，其基本思路可能是：

```text
传统 RACH：发生碰撞后重传
改进 RACH：尝试分离并同时译码多个碰撞用户
```

这在思想上与后来的 NOMA 随机接入、grant-free access、2-step RACH 和小数据传输存在联系。

但早期“RACH 多用户检测”不能直接等同于今天的 NR 2-step RACH 或标准化 NOMA。要核实郑黎明导师及论文、专利关系，需要具体论文题目、专利号或出处。

## 12. RAN1、主席和 Rapporteur

RAN1 是 3GPP Radio Access Network Working Group 1，主要负责无线物理层，包括调制、信道编码、MIMO、参考信号、随机接入和多址接入等。

- **主席 Chair**：主持整个工作组的会议和讨论流程；
- **Rapporteur**：某个研究项目或技术报告的报告人、协调人或牵头人；
- **提案人**：提出具体技术方案的公司或个人。

这三种角色可能不是同一个人。

根据 3GPP 公开会议纪要：

- RAN1#70（2012）和 RAN1#72（2013）：主席为 **Matthew Baker**；
- RAN1#78（2014）和 RAN1#83（2015）：主席为 **Satoshi Nagata**，来自 NTT DOCOMO；
- DOCOMO NOMA 技术线中常被视为核心报告人或牵头专家的是 **Yoshihisa Kishiyama**。

“哈工大某位、郑黎明师兄”的说法目前不能仅凭这些信息确认，不能把它直接归到上述人员身上。

## 13. 标准资料

### 3GPP 标准

- [3GPP 官方网站](https://www.3gpp.org/)
- [3GPP FTP 文档库](https://www.3gpp.org/ftp/)
- TS 38.211：Physical channels and modulation
- TS 38.212：Multiplexing and channel coding
- TS 38.213：Physical layer procedures
- TS 38.321：Medium Access Control protocol specification
- [TR 38.812：Study on Non-Orthogonal Multiple Access for NR](https://www.3gpp.org/ftp/Specs/archive/38_series/38.812/38812-g00.zip)

### RAN1 历史会议报告

- [RAN1#70，2012](https://www.3gpp.org/ftp/tsg_ran/WG1_RL1/TSGR1_70/Report/Final_ReportWG1%2370_v100.zip)
- [RAN1#72，2013](https://www.3gpp.org/ftp/tsg_ran/WG1_RL1/TSGR1_72/Report/Final_ReportWG1%2372_v100.zip)
- [RAN1#78，2014](https://www.3gpp.org/ftp/tsg_ran/WG1_RL1/TSGR1_78/Report/Final_Minutes_report_RAN1%2378_v100.zip)
- [RAN1#83，2015](https://www.3gpp.org/ftp/tsg_ran/WG1_RL1/TSGR1_83/Report/Final_Minutes_report_RAN1%2383_v100.zip)

## 14. 学习路线

建议按以下顺序学习：

```text
通信原理
→ 数字调制与解调
→ 无线信道
→ OFDM
→ LDPC/Polar
→ 脉冲成形与匹配滤波
→ RACH
→ SIC/MUD
→ 功率域 NOMA
→ SCMA/MUSA/IDMA
→ 3GPP 标准和 6G 论文
```

### 需要掌握的基础

- 信号与系统；
- 傅里叶变换；
- 随机信号；
- 采样定理；
- QPSK、QAM；
- AWGN 和衰落信道；
- BER、BLER 和 SNR；
- 线性代数和概率论。

### 推荐视频搜索关键词

- `通信原理 调制解调 QPSK QAM`
- `根升余弦滤波器 脉冲成形`
- `LDPC Polar码 5G`
- `5G NR 38.211 38.212`
- `5G NR RACH 随机接入`
- `2-step RACH MsgA MsgB`
- `NOMA 功率域 SIC`
- `SCMA MUSA IDMA`

### 推荐网站

- [ShareTechnote](https://www.sharetechnote.com/)：适合查 4G/5G 物理层和协议流程；
- [MIT OpenCourseWare](https://ocw.mit.edu/)：数字通信和通信理论课程；
- [NPTEL](https://nptel.ac.in/)：Digital Communications、Wireless Communications 课程；
- [IEEE Xplore](https://ieeexplore.ieee.org/)：论文和会议文献；
- [Google Scholar](https://scholar.google.com/)：学术检索；
- [arXiv](https://arxiv.org/)：开放论文预印本。

### 推荐书籍

入门：

- 《通信原理》，樊昌信；
- 《数字通信》相关高校教材。

进阶：

- Proakis，《Digital Communications》；
- Andrea Goldsmith，《Wireless Communications》；
- Tse and Viswanath，《Fundamentals of Wireless Communication》；
- Theodore Rappaport，《Wireless Communications: Principles and Practice》；
- Dahlman、Parkvall、Skold，《5G NR: The Next Generation Wireless Access Technology》；
- Sassan Ahmadi，《5G NR: Architecture, Technology, Implementation, and Operation》。

### 推荐论文

- *NOMA: From Concept to Standardization*；
- *Non-Orthogonal Multiple Access (NOMA) for Cellular Future Radio Access*；
- *A Survey of Non-Orthogonal Multiple Access for 5G*；
- 3GPP TR 38.812。

## 15. 一句话总结

终端通过 RACH 向基站请求接入，2-step RACH 可以在一次 MsgA 中顺便发送小数据；如果多个终端共享或叠加资源，就需要 NOMA；接收端通过 MUD 或 SIC 分离用户，最后通过 LDPC/Polar 等 FEC 译码纠正传输错误。

# Wi-Fi 与 CSI 学习资料

本目录整理 Wi-Fi 通信与信道状态信息（CSI）相关学习资料。目前重点是复数 CSI、相位校正、人体感知与可信深度学习。

## CSI 教程

建议按以下顺序阅读：

1. [CSI 复数信道感知应用](./CSI/CSI复数信道感知应用.md)
   - OFDM 信道估计、I/Q 与多径传播
   - 静态/动态信道、Doppler 与时频分析
   - ESP32 数据采集、标签、网络选择和跨域实验
2. [CSI 复数信道相位校正](./CSI/CSI复数信道相位校正.md)
   - CFO、SFO、PDD、AGC 与相位环绕
   - 去线性趋势、共轭相位差和圆统计
   - 校正验证、质量门控、伪代码与消融实验
3. [Wi-Fi CSI 深度学习路线](./CSI/WiFi_CSI深度学习路线.md)
   - 从信号表示到物理引导网络
   - 域泛化、质量建模和选择性预测

配套原理图位于 [`CSI/images`](./CSI/images/)；PNG 用于 Markdown 显示，SVG 是可缩放源图。

## 使用提示

- GitHub 可以直接显示 Markdown、LaTeX 公式和图片。
- Mermaid 图需要支持 Mermaid 的阅读器；GitHub 通常可以直接渲染。
- 使用 ESP32 CSI 时，应以实际芯片、ESP-IDF 版本、LTF 和固件定义为准。
- 单天线、非同步 ESP32 的原始相位不应直接称为绝对传播相位。


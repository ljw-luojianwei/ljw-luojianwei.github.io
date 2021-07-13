| BSP   | Board Support Packages                |                    |
| ----- | ------------------------------------- | ------------------ |
| UEFI  | Unified Extensible Fireware Interface | 统一可扩展固件接口 |
| SATA  | Serial Advanced Technology Attachment |                    |
|       |                                       |                    |
| COM   | Component Object Model                | 组件对象模型       |
|       |                                       |                    |
|       |                                       |                    |
|       |                                       |                    |
|       |                                       |                    |
| PCB   | Process Control Block                 | 程序控制块         |
| CPL   | Current Privilege Level               | 当前特权级别       |
| PSR   | Processor Status Register             | 处理器状态寄存器   |
| PSW   | Process Status Word                   | 程序状态字         |
| IRT   | Interrupt Return                      | 中断返回           |
|       |                                       |                    |
|       |                                       |                    |
| PTI   | Page Table Isolation                  | 内核页表隔离       |
|       |                                       |                    |
| HTTPS | Hypertext Transfer Protocol Secure    | 安全超文本传输协议 |
| TCP   |                                       |                    |
| IP    |                                       |                    |
| SSL   | Secure Sockets Layer                  | 安全套接层         |
| TLS   | Transport Layer Security              | 传输层安全         |
| VPN   | Virtual Private Network               | 虚拟专用网络       |
| DHCP  | Dynamic Host Configuration Protocol   | 动态主机配置协议   |
|       |                                       |                    |
| QA    | Quality Assurance                     | 质量保证           |



| 缩写       | 全称                                                         |                                                              |                                                              |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| CHI        | Camera Hardware Interface                                    |                                                              |                                                              |
| IFE        | Image Front-end Engine                                       | 图像前端引擎                                                 | Sensor输出的数据首先会到达IFE。仅做video/preview的Bayer处理，做些颜色校准(color correction)，HDR/demosaic，Down scaler，统计3A数据等。也可以直接输出Raw到RDI。 |
| IPE        | Image Processing Engine                                      | 图像处理引擎                                                 | 由NPS、PPS两部分组成，主要做些硬件降噪（MFNR、MFSR）、调整大小、噪声处理、颜色处理（色差校正、色度抑制）、细节增强（肤色增强）。 |
| BPS        | Bayer Processing Segment                                     | Bayer处理阶段                                                | Bayer格式是相机内部的原始图片，一般后缀名为.raw。仅做snapshot的噪点降低和Bayer处理，不良像素、PDAF(相位检测自动对焦)、LSC(镜头阴影校正)校正，绿色不平衡校正，黑色级别，通道增益，demosaic，Down scaler，HDR的合并与记录，Bayer混合降噪。 |
| RDI        | Raw Dump Interface                                           | 原始数据转储接口                                             | 直接从IFE吐出来用于capture的                                 |
| SNoC       | System NoC                                                   | NoC系统                                                      |                                                              |
| JPGE       |                                                              |                                                              | 打包jpge                                                     |
| STATS      |                                                              |                                                              | for 3A,ISP硬件给出的3A数据，用于后面的3A算法                 |
| IS         | Image Stabilization                                          | 图像稳定                                                     |                                                              |
| ROI AF     | AF Region Of Interest                                        | 感兴趣区域                                                   |                                                              |
| SOF        | Start Of Frame                                               | 帧开始                                                       |                                                              |
| ANR        | application not response                                     |                                                              |                                                              |
| MCC        | mutil camera control                                         |                                                              |                                                              |
| LPM        | low power manager                                            | 低功耗下运行                                                 |                                                              |
| CTS/ITS    | Android Camera Image Test Suite (ITS) is part of Android Compatibility Test Suite (CTS) | Android相机图像测试套件(ITS)是Android兼容性测试套件(CTS)的一部分验证程序，包括验证图像内容的测试。 | 从CTS 7.0_r8开始，CTS Verifier通过一体式摄像机ITS支持ITS测试自动化。继续支持手动测试，以确保涵盖所有Android设备尺寸。 |
| HAF        |                                                              | 混合自动变焦                                                 |                                                              |
| CRM        | camera request manager                                       |                                                              |                                                              |
| Sensor CRA |                                                              |                                                              | 从镜头的传感器一侧，可以聚焦到像素上的光线的最大角度被定义为一个参数，称为主光角(CRA,主光线角)。对于主光角的一般性定义是：此角度处的像素响应降低为零度角像素响应(此时，此像素是垂直于光线的)的80%,lens CRA与sensor 不配会使sensor 的pixel 出现在光检测区域周围，使pixel 曝光不足，亮度不够，会使整个画面造成亮度不均匀的情况。还有可能造成chroma shading 或局部色偏。局部色偏比较严重，无法用算法补偿。 |
| Sensor HDR |                                                              |                                                              | sensor在一幅图像里能够同时体现高光和阴影部分内容的能力       |
| lens fov   |                                                              |                                                              | 视场角,视场角与焦距的关系：一般情况下，视场角越大，焦距就越短. |
| PDPC       | Phase Detection Pixel Correction                             | 相位检测像素校正                                             |                                                              |
| ASD        | Auto Scene Detection                                         | 自动场景侦测                                                 |                                                              |
| CSIC       | Camera Serial Interface Coder                                | 摄像机串行接口编码器                                         |                                                              |
| CSID       | Camera Serial Interface Decoder                              | 摄像头串行接口解码器                                         |                                                              |
| ISP        | Image Signal Processor                                       |                                                              |                                                              |
| AFD        | Auto Flicker Detection                                       | 频闪自动检测                                                 |                                                              |
| ABF        | Adaptive Bayer Filter                                        | 自适应bayer滤波器                                            |                                                              |
| CCI        | Camera Control Interface                                     | 摄像头控制接口                                               |                                                              |
| GPU        | Graphics Processing Unit                                     |                                                              |                                                              |
| AEC        | Auto Exposure Control                                        | 自动曝光控制                                                 |                                                              |
| RANSAC     | RANdom SAmple Consensus                                      | 随机采样一致                                                 |                                                              |
| TFE        | Thin Front End                                               |                                                              |                                                              |
| OPE        | Offline Processing Engine                                    |                                                              |                                                              |
| RTB        | Real Time Bokeh                                              |                                                              |                                                              |
| MFNR       | Multi Frame Noise Reduction                                  |                                                              |                                                              |
| MFSR       | Multi-feature Fusion Sketch Face Recognition(多特征融合的素描人脸识别) |                                                              |                                                              |
| MCTF       | Motion Compensation Temporal Filtering                       |                                                              |                                                              |
| QCFA       | Quad (Bayer Coding) Color Filter Arrangement/Array           |                                                              |                                                              |
| CCM        | Color Correction Matrix                                      |                                                              |                                                              |
| AWB        | Automatic White Balance                                      | 自动白平衡                                                   |                                                              |
| IRQ        | Interrupt Request                                            | 中断请求                                                     |                                                              |
| UMD        | User Mode Driver                                             |                                                              |                                                              |
| KMD        | Kernal Mode Driver                                           |                                                              |                                                              |
| OEM        | Original Equipment 	Manufacturer                          | 原始设备制造商                                               |                                                              |
| ODM        | Original Design	Manufacture                               |                                                              |                                                              |
| OBM        | Orignal Brand Manufactuce                                    |                                                              |                                                              |
| VFE        | Video Front End                                              | 视觉前端                                                     |                                                              |
|            |                                                              |                                                              |                                                              |
| ADSP       | Advanced Digital Signal Processor                            | 高级数字信号处理器                                           |                                                              |

| csl  | camera service layer |
| ---- | -------------------- |
| hwl  | hardware layer       |
| swl  | software layer       |
|      |                      |



| 互斥     | mutual exclusion  |
| -------- | ----------------- |
| 死锁     | deadlock          |
| 临界区   | critical section  |
| 临界资源 | critical resource |


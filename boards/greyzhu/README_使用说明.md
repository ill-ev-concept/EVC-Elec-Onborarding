# Lights Microcontroller Backpack - 交付说明

版本：A，2026-10-07。编辑及验证环境：KiCad 10.0.6。

## 打开工程

解压完整工程 ZIP 后，打开 `Lights_Backpack.kicad_pro`，再双击原理图或 PCB。保留整个文件夹结构；`Backpack.kicad_sym`、`Backpack.pretty` 和两个 `lib-table` 文件是工程自带的符号、封装库，无需单独下载这些库。

- `Lights_Backpack.kicad_sch`：完整原理图。
- `Lights_Backpack.kicad_pcb`：已布局、布线和铺铜的双层 PCB。
- `Lights_Backpack.pdf`：矢量原理图，适合放大查看或提交作业。
- `BOM.csv`：逐位号物料表，共 48 个条目，其中 4 个为安装孔，并非采购元件。
- `Component_Positions_Reference.csv`：元件位置参考。使用 KiCad 绝对坐标、Y 向下、KiCad 角度；不是已适配某家贴片厂的下单坐标文件。
- `ERC_Passed.rpt` / `DRC_Passed.rpt`：KiCad 原生检查报告。
- `manufacturing`：9 个 Gerber、1 个 Gerber job、2 个 Excellon 钻孔文件。
- `Delivery_Checks.json` / `SHA256SUMS.txt`：文件检查摘要和文件校验值。

原桌面工程保留。此文件夹是完成后的独立版本。

## 已完成与验证范围

完成 STM32F103C8T6 最小系统、复位与启动配置、8 MHz 晶振、5 V 转 3.3 V 电源、团队调试接口、ISO1050 隔离 CAN、RJ45 电源/CAN 接口、两路 WS2812 数据电平转换与带保险丝的电源输出，以及四个 M4 安装孔。

KiCad 原生 ERC：0 错误、0 警告。PCB DRC：0 违规、0 未连接焊盘、0 封装错误；检查时启用了原理图与 PCB 一致性检查。48 个位号的元件值、MPN 和封装又经交付脚本核对一致。最终图纸只调整了文字位置和说明，没有改变电气连接。

检查采用工程当前规则；报告列出了未启用的项目，包括 SPICE 模型、单次出现的全局标签、四路连接点、符号封装过滤器，以及若干封装/调谐相关规则。0 项违规表示通过当前规则检查，不代表完成全部物理和系统验证。

**尚未制作、焊接或实测样板；不含固件。** 晶振启动、电源温升、LED 负载、CAN 波形与总线参数仍需在样板上验证。此设计用于课程中的低压 CAN 系统，没有进行汽车认证或高压安全隔离认证。

## 接口与接线

### J1：团队自定义 2×5 调试接口，间距 2.54 mm

| 针脚 | 信号 | 针脚 | 信号 |
|---|---|---|---|
| 1 | +5V | 2 | +5V |
| 3 | SWCLK | 4 | 目标板 +3.3V |
| 5 | SWO | 6 | 目标板 +3.3V |
| 7 | SWDIO | 8 | 不连接 |
| 9 | GND | 10 | GND |

这是课程自定义针序，不是标准 ARM 10 针接口。使用 ST-Link 等调试器时按信号制作转接线。4/6 脚用于目标电压参考，不要接调试器的 3.3 V 电源输出。板上 3.3 V 由 U3 产生。J1 的 +5V 与板上 +5V 相连，没有电源择优电路；避免同时从 J1 和 J2 接入两路独立 5 V 电源。

### J2：RJ45（54602-908LF）

| 针脚 | 信号 |
|---|---|
| 1 | CANH |
| 2 | CANL |
| 3、5 | +5V_IN，经 F1 给主板供电 |
| 4、6 | GND |
| 7 | 5V_CAN，外部隔离侧 5 V 供电 |
| 8 | GND_CAN |

**此 RJ45 用于 CAN 和电源，不是以太网或 PoE 接口。** 对接线束必须按针号核对。板上没有隔离 DC/DC 转换器，5V_CAN 必须由外部提供。要保持 CAN 隔离，外部也应保持 GND_CAN 与 GND、5V_CAN 与主板 +5V 分离。

JP1 默认不装短路帽。只有本板位于总线端点且需要本地终端时，才短接 JP1，接入 R9 的 120 Ω 终端电阻。整个 CAN 总线的终端数量、速率和线束由系统确定。

### J3 / J4：两路外接 LED 板（Molex 2157601003）

| 针脚 | J3 | J4 |
|---|---|---|
| 1 | 5V_LED_A | 5V_LED_B |
| 2 | GND | GND |
| 3 | DATA_A_OUT | DATA_B_OUT |

连接器使用 Micro-Fit+ 3 mm 单排三针直角封装。LED 板/线束的实际针序尚未提供，必须与上表核对。U4/U5 使用 5 V 供电的 SN74AHCT1G125，将 MCU 的 3.3 V 信号转换为 LED 数据电平。

## 电源与电流假设

- 输入为稳压 5 V。F1 为 Bourns MF-MSMF110/16X-2，1.1 A 保持电流；每路 LED 的 F2/F3 为 MF-MSMF050/16X-2，0.5 A 保持电流。额定保持电流受环境温度影响，保险丝也存在压降，不能当作精密限流器。
- 由于没有给出 LED 数量与负载，本版按 **每路 LED 不超过 0.4 A 的设计目标、整板 5 V 总负载不超过约 1 A** 整理。高温、保险丝压降和实际 LED 最低电压可能要求进一步降低负载；此数值不是实测保证。
- 3.3 V 电源预算 150 mA；LDO 在 5 V 输入、150 mA 输出时约消耗 0.255 W，需要样板温升验证。LED 电源直接来自 5 V，不经过 LDO。
- 更长灯带或更大电流应另做供电和保护设计，不应直接扩大本板负载。

## 制板与装配

制板压缩包 `Lights_Backpack_Manufacturing.zip` 仅包含 KiCad 原生 Gerber 和钻孔输出；不包含临时文件。

| 项目 | 本版参数 |
|---|---|
| 板框中心线尺寸 | 100 × 80 mm |
| 层数 / 板厚 | 2 层 / 1.6 mm |
| 基材 / 铜厚 | FR-4 / 外层 35 µm，约 1 oz |
| 最小布线宽度 / 规则间距 | 0.25 mm / 0.20 mm |
| 常用过孔 | 焊盘 0.70 mm、孔径 0.35 mm |
| 安装孔 | 四个 4.3 mm 非金属化孔，孔中心距相邻板边 5 mm |
| PTH / NPTH | 52 个金属化孔，8 个非金属化孔 |

板框在 KiCad 坐标 X=50..150、Y=50..130 mm。Gerber job 的包围框包含 0.05 mm 板框线宽，可能显示 100.05 × 80.05 mm；成品板框按线中心的 100 × 80 mm 解释。

钻孔使用公制绝对坐标，PTH 与 NPTH 分开。PTH 包含 22 个过孔和 30 个元件引脚孔。NPTH 包含 4 个安装孔、2 个 RJ45 定位孔、2 个 Micro-Fit+ 定位孔。背面没有元件或文字，所以背面锡膏和丝印 Gerber 为空层属于预期。

表面处理未指定，交由课程/制板厂选定常规工艺。全部元件位于正面。贴片厂若要求专用坐标格式，应从 KiCad 导出并按其旋转定义检查，不能把参考 CSV 直接当作已审核的贴片订单。

物料表中 MPN 为空的电阻、电容、按键、排针等仍需按封装和参数选定采购料号：电容至少 10 V，常规去耦采用 X7R/X5R，晶振电容 C15/C16 使用 C0G/NP0；电阻建议 1%，R9 为 0805、120 Ω。SW1 使用 6×6 mm 四脚通孔轻触按键的匹配脚距。采购时核对实际机械尺寸和引脚，不要仅根据外观替代。

## 固件引脚参考

| 功能 | MCU 引脚 |
|---|---|
| LED_A / LED_B | PA6 / PB1 |
| CAN_RX / CAN_TX | PA11 / PA12 |
| CAN 状态灯 | PB15 |
| SWDIO / SWCLK / SWO | PA13 / PA14 / PB3 |
| 外部 8 MHz 晶振 | PD0 / PD1 |

BOOT0、BOOT1 经 10 kΩ 下拉，默认从主 Flash 启动。复位按键将 NRST 拉低。晶振为 ABM3B-8.000MHZ-10-1-U-T，C15/C16 各 15 pF，基于 CL=10 pF 及约 2.5 pF 寄生电容的初始估算，最终需实板确认。

## 设计参考

- [ST STM32F103C8 数据手册](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf)
- [TI ISO1050](https://www.ti.com/lit/ds/symlink/iso1050.pdf)
- [TI TLV755P](https://www.ti.com/lit/ds/symlink/tlv755p.pdf)
- [TI SN74AHCT1G125](https://www.ti.com/lit/ds/symlink/sn74ahct1g125.pdf)
- [Abracon ABM3B](https://abracon.com/Resonators/abm3b.pdf)
- [Bourns MF-MSMF](https://www.bourns.com/docs/product-datasheets/mf-msmf.pdf)
- [Amphenol 54602-908LF](https://www.amphenol-cs.com/product/54602908lf.html)
- [Molex 2157601003 产品页](https://www.molex.com/en-us/products/part-detail/2157601003)
- [Molex 系列制造商尺寸图的分销商镜像，含 2157601003 表项](https://www.ic-components.com/files/23/2157601004.pdf)

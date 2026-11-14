# NOTICE

## 1. 没有固件、密钥或镜像

本仓库**不包含**：

- Nikon 固件（`.bin`、`.bi`、`.exe` 升级包等）；
- 密钥（`key_all.json`、XOR 表、K8 等）；
- 解密后的模块或完整镜像；
- 修改后/可刷写的固件；
- 尼康 L 失真控制数据样本（`NKLD*.BIN`）。

所有地址、偏移、CRC 值、代码片段与结论都是本地静态分析/实验记录。固件由
使用者自行从官方渠道合法取得。

## 2. 商标与关联

Nikon、EXPEED、D750、D7200、D810、D850、D5、D3400 等名称与商标属于
Nikon Corporation。本项目与 Nikon 没有任何关联，未获授权或背书。

## 3. 风险

修改、重打包、刷写固件可能导致相机损坏、变砖、失去保修、数据丢失，甚至
违反当地法律。**风险由使用者自行承担。** 本项目不提供刷机流程、不重签名、
不承诺真机兼容。

## 4. 引用与第三方工作

- **Nikon 下载中心** —— 官方固件与 Distortion Control Data 的合法来源；
- **Nikon-Patch / Nikon Hacker 社区** —— IDA 布局脚本与 D5300/D810 码率表
  对照，用于确认 E4 平台的表结构；
- **Expeed 4/5 解密/重打包工具链**（作者自用）—— 本仓库三条案例的模块级
  分析与重封包都依赖它；工具本身不随本仓库分发。

引用这些工作时请遵守其各自许可。

## 5. 许可

本仓库的**文本**采用 [CC BY 4.0](LICENSE) 许可。

如果未来加入代码，代码部分应单独标注许可；在加入之前，本仓库不包含代码。

## 6. 引用格式

```text
LuoStickmin. FAILED_RESEARCHING: three untenable Nikon firmware research lines.
2026. https://github.com/LuoStickmin/FAILED_RESEARCHING
```

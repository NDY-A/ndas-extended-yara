# NDAS Defender YARA 扩展规则包

由 Microsoft Defender 病毒定义（VDM，mpavbase + mpavdlta）经 defender2yara 1.0.4 离线转换得到的
YARA 规则分片，供 [NDAS 安全中心](https://github.com/NDY-A/ndas-security-center) 的
「增强型规则引擎 II」使用。

- **规则数量**：14 个分片，合计约 96,881 条规则
- **清单**：`manifest.json` 列出每个分片的文件名、字节数与 SHA-256；NDAS 更新器以清单为准逐片校验
- **生成时间**：2026-11-20（Defender VDM 1.463.386.0）

## 目录结构

```
manifest.json                     包清单（files: name / bytes / sha256）
defender-patterns-part01.yara     规则分片（14 个）
...
defender-patterns-part14.yara
```

## NDAS 如何使用本仓库

NDAS **0.0.14.0a 及以后版本**的更新器会从本仓库 `main` 分支根目录获取
`manifest.json` 与全部分片（优先 `raw.githubusercontent.com`，不可达时依次尝试
GitHub Raw Web 与 jsDelivr 镜像），校验 SHA-256 后安装到
`%LOCALAPPDATA%\NDAS\signatures\current\yara\`。本仓库不可用时，会回退到
`NDY-A/malware-egress-blocklist` 仓库的 `data/yara/defender/`（旧位置）。

「增强型规则引擎 II」在 NDAS 设置中默认**关闭**；启用并重启 NDAS 后才会把扩展规则
装入内存（加载全部规则时进程**至少占用 5 GB 内存**）。

## 来源与许可注意事项

- 数据源自 Microsoft Defender 的签名库，经 defender2yara 转换；与 Microsoft 无隶属或背书关系。
- 转换近似 Defender 的评分逻辑，**存在误报可能**，请结合哈希/启发式/沙箱等其他信号使用。
- 发布时已移除嵌入凭据类字面量的规则（见清单 `filtered_out` 字段）。
- 仓库作者不对再分发的许可适用性作出保证；使用或再分发前请自行评估 Microsoft 签名数据的再分发条款。

## 更新方式

NDAS 更新器在每次病毒库更新时会重新拉取本仓库的 `manifest.json`；清单内容与本地一致时
直接复用本地已校验分片，不重复下载。若规则包有更新，直接替换分片并同步更新
`manifest.json` 内的字节数与 SHA-256 即可。

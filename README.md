# 微信（WeChat）分流规则合集

> 生成于 2026-10-28。数据源均来自 GitHub 公开仓库，本地已存一份离线副本。

## 一、首选源：blackmatrix7/ios_rule_script（最全、最常用）

| 平台 | 直链 |
|---|---|
| Surge | https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/WeChat/WeChat.list |
| Loon | https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Loon/WeChat/WeChat.list |
| Clash | https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Clash/WeChat/WeChat.yaml |
| QuantumultX | https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/QuantumultX/WeChat/WeChat.list |
| Shadowrocket | https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Shadowrocket/WeChat/WeChat.list |

- 仓库：https://github.com/blackmatrix7/ios_rule_script
- Surge 全量 332 条：DOMAIN 7 / DOMAIN-SUFFIX 22 / DOMAIN-KEYWORD 233 / IP-ASN 1 / IP-CIDR 3 / IP-CIDR6 64 / USER-AGENT 2
- Loon 版只 33 条（官方裁剪版，去掉了「用 IP 当 SNI」的 233 条 keyword 和 IPv6 段，性能更友好）
- 注意：`DOMAIN-KEYWORD,101.226.129.` 这类是把 IP 串当 SNI 匹配（微信有时直接用 IP 建 TLS），不是打错字

## 二、极简版：ACL4SSR

https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/Ruleset/Wechat.list

- 仓库：https://github.com/ACL4SSR/ACL4SSR
- 只有 22 条核心域名 + 8 个微信服务器 IP，2019 年后基本没大改，够用但漏新域名

## 三、本地文件（本目录）

| 文件 | 说明 |
|---|---|
| `WeChat-surge.list` | blackmatrix7 Surge 原版全量（332 条） |
| `WeChat-loon.list` | blackmatrix7 Loon 官方精简版（33 条） |
| `WeChat-loon-full.list` | 我把 Surge 全量筛成 Loon 语法的版本（332 条，含 keyword/IPv6/IP-ASN） |
| `WeChat-clash.yaml` | blackmatrix7 Clash payload 格式 |
| `WeChat-acl4ssr-clash.list` | ACL4SSR 极简版 |

## 四、用法

### Loon
```
[Remote Rule]
https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Loon/WeChat/WeChat.list, policy=直连, tag=微信, enabled=true
```
或本地：`rule = WeChat-loon-full.list, 直连`（文件需放进 Loon 的配置文件同目录 / iCloud）

### Surge
```
RULE-SET,https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Surge/WeChat/WeChat.list,DIRECT
```

### Clash
```yaml
rule-providers:
  wechat:
    type: http
    behavior: classical
    url: "https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Clash/WeChat/WeChat.yaml"
    path: ./ruleset/wechat.yaml
    interval: 86400
rules:
  - RULE-SET,wechat,DIRECT
```

## 五、放哪个策略
- 想「微信不走代理、延迟最低」→ 指到 `DIRECT` / `直连`
- 想「微信走家宽/回国」→ 指到 ping 最低的国内节点组
- 规则顺序务必放在「国内IP/GEOSITE-CN/兜底代理」之前

---

# Stripchat 分流规则

> 生成于 2026-11-20。共 7 条 DOMAIN-SUFFIX，已去重、修正 `DOMAIN SUFFIX` 缺连字符与 `DOMAIN-` 跨行断裂问题。

| 文件 | 说明 | 直链 |
|---|---|---|
| `Stripchat-surge.list` | Surge 版 | https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-surge.list |
| `Stripchat-loon.list` | Loon 版 | https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-loon.list |
| `Stripchat-clash.yaml` | Clash payload | https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-clash.yaml |
| `Stripchat-acl4ssr-clash.list` | 极简 list | https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-acl4ssr-clash.list |

## 域名清单

| 组 | 域名 | 作用 |
|---|---|---|
| 主站 | stripchat.com | 站点主域名 |
| 主站 | stripchat.global | 国际化入口 |
| 主站 | mavrtracktor.com | 统计/追踪 |
| 边缘 | sacfedge.com | 边缘节点 |
| 边缘 | growcdnssedge.com | 边缘节点 |
| CDN | doppiocdn.com | 静态资源 |
| CDN | doppiocdn.org | 静态资源 |

## 用法

### Surge
```
RULE-SET,https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-surge.list,PROXY
```

### Loon
```
[Remote Rule]
https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-loon.list, policy=PROXY, tag=Stripchat, enabled=true
```

### Clash
```yaml
rule-providers:
  stripchat:
    type: http
    behavior: classical
    url: "https://raw.githubusercontent.com/ruiruizl/wechat-rules/main/Stripchat-clash.yaml"
    path: ./ruleset/stripchat.yaml
    interval: 86400
rules:
  - RULE-SET,stripchat,PROXY
```

## 备注
- 原稿里的 `V0000` 是客户端残留分组标记，不是规则，已丢弃。
- 全部为 `DOMAIN-SUFFIX`，会连带匹配子域（如 `*.stripchat.com`）。

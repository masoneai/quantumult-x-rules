# Quantumult X 域名节点分流规则

公开的规则与配置模板，不包含真实节点凭据或订阅地址。当前策略使用原用户的域名节点 `node.284021.xyz`，节点名称为 `CF-域名直连`。其他用户需替换节点 tag、策略组中的节点名称，以及对应的域名入口直连规则。

## 新建配置

导入 [template.conf](template.conf)，把 `https://example.com/your-subscription` 换成自己的私人节点订阅，核对节点名称后选择“分流”模式。填写私人订阅后的配置请保留在本地。

模板采用腾讯加密 DNS（`https://doh.pub/dns-query`），并通过 `no-system` 禁用系统 DNS。检测页面仍可能显示国内解析地址；此配置不保证所有 DNS 请求都经代理。

2026-10-09 更新：采用域名优先分流。节点入口与内网域名先直连，海外服务优先代理，明确的国内服务直连，然后用 `host-keyword, ., 节点选择` 处理未命中的域名。所有 IP 与 GeoIP 规则放在这条域名兜底之后，减少为了判断 IP 归属而触发的本地解析；这是 [Quantumult X 官方样例](https://github.com/crossutility/Quantumult-X/blob/master/sample.conf#L266) 支持的写法。

未收录的国内 `.com/.net` 域名也会使用代理；可在域名兜底前添加 `host-suffix, 服务域名, direct`。自定义内网域名同样需要前置直连，已有 `localhost`、`.local`、`.lan`、`home.arpa` 例外。GeoIP 仍为未命中域名规则的纯 IP 请求判断国内直连。这项优化不能接管浏览器自己发起的加密 DNS，也不构成 DNS 零泄漏保证。

节点订阅和分流规则订阅的更新不能修改主配置中的 DNS。应用此项变更时，请从远程完整配置下载／导入 [template.conf](https://raw.githubusercontent.com/masoneai/quantumult-x-rules/main/template.conf)，替换私人节点订阅地址后，将它应用为圈 X 主配置。

## 合并已有配置

打开 [merge.conf](merge.conf)，将 `[policy]` 和 `[filter_local]` 的内容分别合并到现有同名区段，保留自己的 `[server_remote]`。检查重复规则与 `final` 兜底策略；`节点选择` 策略使用 `CF-域名直连`。

## 远程更新规则

[rules.list](rules.list) 只含原生过滤规则。先从 `merge.conf` 合并 `[policy]` 策略组，再在自己的 `[filter_remote]` 区段加入：

```ini
https://raw.githubusercontent.com/masoneai/quantumult-x-rules/main/rules.list, tag=域名节点分流, enabled=true
```

不要加入 `force-policy`：它会覆盖列表中的 `direct`，导致原本直连的规则也使用指定策略。使用远程规则时，避免在 `[filter_local]` 重复加载同一套规则，并核对现有兜底规则。

公共仓库仅发布本目录，不应包含填入私人订阅后的配置。

## 参考仓库审查

本次借鉴服务分类、具体规则优先和最终代理的结构，补全 AI、媒体、检测网站以及常见国内服务域名；整理成原生规则并去重，没有加载整套外部规则。

- [Sve1r](https://github.com/sve1r/Rules-For-Quantumult-X)：样例默认启用普通 DNS；[Global.list](https://github.com/sve1r/Rules-For-Quantumult-X/blob/main/Rules/Region/Global.list) 存在重复项，`dytt8.net` 等与 China.list 的分类冲突。China.list 还将共享 `akadns.net` 整域直连。采用其排序思路与具体国内服务域名，不复制共享 CDN 或泛关键词规则。
- [Profiles4limbo](https://github.com/limbopro/Profiles4limbo/blob/main/full.conf)：完整配置使用普通国内 DNS、示例故障切换节点和远程重写。[AI_Platforms_qx.list](https://github.com/limbopro/Profiles4limbo/blob/main/AI_Platforms_qx.list) 依赖解析器转换 `DOMAIN` 语法。采用其 AI/媒体分类思路，使用明确的原生 `host-suffix`；保留现有单节点、DoH 和完整 IPv4/IPv6 内网规则。

本包未启用 HTTPS 解密、脚本、远程重写或资源解析器。规则分类只能控制流量走向，不能保证流媒体地区解锁或改变 Worker 的出站限制。

## 文件与来源

- `merge.conf`：当前策略组与分流规则片段。
- `rules.list`：`filter_local` 裸规则，无区段与策略组。
- `template.conf`：使用示例订阅地址的完整配置模板。
- `LICENSE`：GPL-2.0 许可证，与来源节点项目一致。

格式参考 [Quantumult X 官方 sample.conf](https://github.com/crossutility/Quantumult-X/blob/master/sample.conf)；来源节点实现参考 [zizifn/edgetunnel](https://github.com/zizifn/edgetunnel)。本包只提供分流配置，节点连接与速度需要单独验证。

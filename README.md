# Quantumult X 三入口分流规则

公开的规则与配置模板，不包含真实节点凭据或订阅地址。当前策略针对原用户的三个入口：`node.284021.xyz`、`183.223.134.233`、`223.160.186.152`。其他用户需替换节点 tag、策略组中的节点名称，以及对应的入口直连规则；两个 IP 是否可用取决于实际网络。

## 新建配置

导入 [template.conf](template.conf)，把 `https://example.com/your-subscription` 换成自己的私人节点订阅，核对节点名称后选择“分流”模式。填写私人订阅后的配置请保留在本地。

模板采用腾讯加密 DNS（`https://doh.pub/dns-query`），并通过 `no-system` 禁用系统 DNS。检测页面仍可能显示国内解析地址；此配置不保证所有 DNS 请求都经代理。

节点订阅和分流规则订阅的更新不能修改主配置中的 DNS。应用此项变更时，请从远程完整配置下载／导入 [template.conf](https://raw.githubusercontent.com/masoneai/quantumult-x-rules/main/template.conf)，替换私人节点订阅地址后，将它应用为圈 X 主配置。

## 合并已有配置

打开 [merge.conf](merge.conf)，将 `[policy]` 和 `[filter_local]` 的内容分别合并到现有同名区段，保留自己的 `[server_remote]`。检查重复规则与 `final` 兜底策略；默认选择域名入口，“自动测速”只比较配置中的三个入口。

## 远程更新规则

[rules.list](rules.list) 只含原生过滤规则。先从 `merge.conf` 合并 `[policy]` 策略组，再在自己的 `[filter_remote]` 区段加入：

```ini
https://raw.githubusercontent.com/masoneai/quantumult-x-rules/main/rules.list, tag=三入口分流, enabled=true
```

不要加入 `force-policy`：它会覆盖列表中的 `direct`，导致原本直连的规则也使用指定策略。使用远程规则时，避免在 `[filter_local]` 重复加载同一套规则，并核对现有兜底规则。

公共仓库仅发布本目录，不应包含填入私人订阅后的配置。

## 文件与来源

- `merge.conf`：当前策略组与分流规则片段。
- `rules.list`：`filter_local` 裸规则，无区段与策略组。
- `template.conf`：使用示例订阅地址的完整配置模板。
- `LICENSE`：GPL-2.0 许可证，与来源节点项目一致。

格式参考 [Quantumult X 官方 sample.conf](https://github.com/crossutility/Quantumult-X/blob/master/sample.conf)；来源节点实现参考 [zizifn/edgetunnel](https://github.com/zizifn/edgetunnel)。本包只提供分流配置，节点连接与速度需要单独验证。

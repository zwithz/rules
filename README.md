# rules

个人维护的分流规则，一份源文件自动生成 **Surge / Loon / Quantumult X / Clash (Mihomo)** 四种格式。

## 规则列表

| 名称 | 说明 | 建议策略 |
|---|---|---|
| [Xiaohongshu](source/Xiaohongshu.list) | 小红书 / rednote。走代理会被识别为海外 IP，跳转到 rednote.com，评论 IP 属地也会变成海外 | `DIRECT` |

## 订阅地址

```
https://raw.githubusercontent.com/zwithz/rules/main/rules/<平台>/<名称>.<后缀>
```

| 平台 | 路径 |
|---|---|
| Surge | `rules/surge/<名称>.list` |
| Loon | `rules/loon/<名称>.list` |
| Quantumult X | `rules/quanx/<名称>.list` |
| Clash / Mihomo | `rules/clash/<名称>.yaml` |

如果 raw.githubusercontent.com 访问不稳定，可以换成 jsDelivr：
`https://cdn.jsdelivr.net/gh/zwithz/rules@main/rules/<平台>/<名称>.<后缀>`

## 使用示例（以 Xiaohongshu 为例）

> 直连规则要放在其他代理规则、`GEOIP` 和 `FINAL` / `MATCH` 之前。

### Surge

```ini
[Rule]
RULE-SET,https://raw.githubusercontent.com/zwithz/rules/main/rules/surge/Xiaohongshu.list,DIRECT
```

### Loon

```ini
[Remote Rule]
https://raw.githubusercontent.com/zwithz/rules/main/rules/loon/Xiaohongshu.list, policy=DIRECT, tag=Xiaohongshu, enabled=true
```

### Quantumult X

规则文件里的策略列是规则名，需要用 `force-policy` 指定实际策略：

```ini
[filter_remote]
https://raw.githubusercontent.com/zwithz/rules/main/rules/quanx/Xiaohongshu.list, tag=Xiaohongshu, force-policy=direct, update-interval=86400, opt-parser=false, enabled=true
```

### Clash / Mihomo

```yaml
rule-providers:
  Xiaohongshu:
    type: http
    behavior: classical
    format: yaml
    url: https://raw.githubusercontent.com/zwithz/rules/main/rules/clash/Xiaohongshu.yaml
    path: ./ruleset/Xiaohongshu.yaml
    interval: 86400

rules:
  - RULE-SET,Xiaohongshu,DIRECT
```

## 新增 / 修改规则

1. 在 `source/` 下新建或编辑 `<名称>.list`，只写规则类型和值，不写策略：

   ```
   # NAME: Example
   # DESC: 说明
   # UPDATED: 2026-10-06

   DOMAIN-SUFFIX,example.com
   DOMAIN,api.example.com
   DOMAIN-KEYWORD,example
   IP-CIDR,1.2.3.0/24
   IP-CIDR6,2001:db8::/32
   ```

2. 推送到 `main`。GitHub Actions 会自动生成 `rules/` 下四个平台的文件并提交。本地也可以手动跑：

   ```bash
   python3 scripts/build.py          # 生成
   python3 scripts/build.py --check  # 检查生成文件是否最新
   ```

支持的类型：`DOMAIN`、`DOMAIN-SUFFIX`、`DOMAIN-KEYWORD`、`IP-CIDR`、`IP-CIDR6`。IP 类规则会自动加上 `no-resolve`（QuanX 除外）。

`rules/` 目录是生成产物，不要手动修改。

## 目录结构

```
source/            规则源文件（唯一需要手动维护的地方）
scripts/build.py   生成脚本，无第三方依赖
rules/             生成的各平台规则
  surge/  loon/  quanx/  clash/
```

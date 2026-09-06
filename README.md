# gtranslate-hosts

把「裸 IP 列表」（例如 Ponderfly/GoogleTranslateIpCheck 维护的 `ip.txt`）转换为
SwitchHosts 可拉取的 **hosts 格式**，并（可选）通过 GitHub Actions 每周自动同步一次。

## 为什么需要它

`https://.../ip.txt` 的内容是**一行一个 IP**，而 SwitchHosts 的远程源（Remote）
要求的是 hosts 内容：`IP 域名` 两列。本仓库做的事情就是格式转换：

```
# 输入 ip.txt
142.250.4.90
172.253.114.90
...

# 输出 hosts.txt（喂给 SwitchHosts）
142.250.4.90 translate.googleapis.com
172.253.114.90 translate.googleapis.com
...
```

## 手动使用

```bash
python3 convert.py ip.txt hosts.txt --domain translate.googleapis.com --limit 60
```

- `--limit N`：只取前 N 个 IP（hosts 解析会顺序尝试，几十个足够）
- 输出 `hosts.txt` 可直接复制进系统 hosts，或作为 SwitchHosts 远程源内容

## 配合 GitHub Actions 自动同步

1. 把这个目录推到一个 GitHub 仓库
2. 工作流 `.github/workflows/sync.yml` 每周一自动：
   - 拉取上游最新 `ip.txt`
   - 运行 `convert.py` 生成 `hosts.txt`
   - 有变化就提交
3. 在 SwitchHosts 里新增一个 **Remote** 源，URL 填：
   `https://raw.githubusercontent.com/<你的用户名>/<仓库名>/master/hosts.txt`
   （也可以换成 unpkg / jsDelivr 镜像）

> 上游：<https://github.com/Ponderfly/GoogleTranslateIpCheck>（其 `ip.txt` 保持更新）
> 备用裸 IP 源：<https://github.com/hcfyapp/google-translate-cn-ip>（`packages/google-translate-ip/ips.txt`，但自 2023-01 起基本未更新）

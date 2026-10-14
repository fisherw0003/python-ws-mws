# python-ws (MWS 适配版)

基于 [eooce/python-ws](https://github.com/eooce/python-ws) 修改，适配 MWS (cloud.m-ws.cc) 免费容器的网络环境。

## 与原版的区别

1. **HTTP 代理出站**：MWS 容器无直连出网，所有 TCP 经 `HTTP_PROXY`/`HTTPS_PROXY` (10.202.0.1:41500) 做 CONNECT 隧道。VLESS/Trojan/SS 的出站全部走代理，失败时回退直连。
2. **DNS 解析修复**：原版 DoH 直连 `dns.google` 在 MWS 被掐，解析失败回退成域名后代理的 DNS 也是坏的，导致 `CONNECT domain:80 OK` 但零数据。改为系统 DNS 优先 + 经代理的 DoH 兜底，解析成 IP 后再 CONNECT。
3. **硬编码 UUID/DOMAIN**：MWS 的 `.env.enc` 不好改，直接写死在代码里（部署前替换）。
4. **`/api/logs`**：内存日志缓冲接口，方便远程查日志不用进面板。
5. **协议级诊断日志**：WS 到达、首包长度/UUID 前缀、VLESS 解析、代理 CONNECT 结果。

## 部署

ZIP 打包 `app.py requirements.txt index.html`，经 MWS 面板「ZIP アップロード」上传，重启即可。

## 验证

- `wss://<domain>/<uuid[:8]>` VLESS 握手
- `https://<domain>/api/logs` 查日志
- Shadowrocket 连通性测试（谷歌）通过

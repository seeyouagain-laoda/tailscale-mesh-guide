# Tailscale 异地组网

> 用 [Tailscale](https://github.com/tailscale/tailscale) 把家里 PC、NAS、手机、平板组成一张加密网状 VPN，跨网络也能直连 NAS 上的服务。
> 所有 IP / auth key 均为占位符。引用：[Tailscale](https://github.com/tailscale/tailscale)

## 1. 架构

```
       Tailscale 控制面（云端协调，数据端到端加密）
   ┌──────────┬──────────┬──────────┬──────────┐
   │  PC     │  NAS     │  手机    │  平板    │   (各持一个 100.x.y.z 地址)
   └──────────┴──────────┴──────────┴──────────┘
        ▲           ▲
        │  子网路由  │
        └──── 暴露 NAS 内网段（如 192.168.x.0/24）给组网内其他设备
```

要点：

- 每个设备装 Tailscale 客户端，登录同一账号，获得 `100.x.y.z` 的 Mesh IP。
- NAS 上可开启 **子网路由（subnet routes）**，让组网内其他设备直接访问 NAS 的局域网段（不仅限 100.x）。
- 手机用流量也能通过 Tailscale 直连 NAS 上的反代/服务，绕开公网域名被运营商拦截的问题。

## 2. 关键配置

- **Auth Key**：在 Tailscale 后台生成「可复用 / 一次性」key，NAS 上用 `tailscale up --authkey=<KEY>` 登录（适合无头设备）。
- **子网路由**：`tailscale up --advertise-routes=192.168.x.0/24`，并在后台批准该路由。
- **标签（tags）**：给设备打 tag 做访问控制（ACL），避免所有设备互信。

## 3. 常见坑

- 开了子网路由但后台没「批准」，设备连得上 Tailscale 却访问不了内网段。
- NAS 上 Tailscale 与本地代理（Mihomo）混用时，注意路由优先级，避免回环。
- 手机切 WiFi/流量时 Tailscale 会自动重连，一般无需手动。

## 4. 参考

- Tailscale：https://github.com/tailscale/tailscale
- NAS 反代兜底访问见 [reverse-proxies.md](reverse-proxies.md)

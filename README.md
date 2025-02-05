# Rust 客户端重构

## 核心分析

- 客户端与服务端建立连接；
- 客户端发送认证请求：
  - 认证通过进入下一步；
  - 认证不通过退出程序；
- 客户端发送代理请求；
- 客户端接收到 Connect 时，建立本地代理目标的连接并存储连接信息（channelId，connect）
  - 建立成功，向服务端发送 Connect 消息；
  - 建立失败，向服务端发送 Disconnect 消息；
- 客户端接收到 Transfer 时，根据 ChannelId 获取对应的 connect，将数据发送给代理目标；
  - 监听来自代理目标的响应数据，将响应数据写回给服务端

## Channel 设计

&emsp;主要涉及三个 channel:

- `s_channel`: 客户端向服务端写回数据时用到的 channel；
- `r_channel`: 客户端从服务端读取数据时用到的 channel；
- `p_channel`: 本地代理请求时需要与目标程序建立网络连接，此时需要创建一个 process 任务。`p_channel` 是用于与 process 任务进行通信；

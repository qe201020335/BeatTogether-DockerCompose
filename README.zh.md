# BeatTogether-DockerCompose
[![en](https://img.shields.io/badge/lang-en-blue.svg?style=for-the-badge)](/README.md)
[![zh](https://img.shields.io/badge/语言-中文-red.svg?style=for-the-badge)](/README.zh.md)

使用 Docker Compose 运行 **可扩展** 的 [BeatTogether](https://github.com/BeatTogether) 服务器的简单指南。

如果你只是需要一个小型多人服务器，建议使用 [UnifiedServer](https://github.com/BeatTogether/BeatTogether.UnifiedServer)。

欢迎使用讨论版 [Discussions](https://github.com/qe201020335/BeatTogether-DockerCompose/discussions)

## 如何开服
  0. 确认你在使用Linux并且安装了Docker
  1. 克隆或者下载此仓库
  2. 按照你的需求修改json设置文件。 文档见[下方](#Json-设置文件).
  3. 确认你的当前工作目录(current working directory) 在这个仓库的根目录 
  4. `docker compose up -d`
     - 如果你在更新, 先 `docker compose down` 和 `docker compose pull` 再 `docker compose up -d`
  5. 将这个服务器添加进BeatTogether mod的设置中。 详见[下方](#BeatTogether-Mod-设置)

## Json 设置文件
 - 这里并不包含全部的选项，下方仅包含了和开服相关的最重要的选项 
 - 你需要去阅读服务器源代码来找到更多的可用选项

### 主服务器
`master.json`
```
{
  "Urls": "http://0.0.0.0:8989",         // 用于主服务器监听的ip和端口
  "ServerConfiguration": {
    "AuthenticateClients": true          // 客户端是否需要被认证
  },
  "Serilog": {
    "File": {
      "FileSizeLimitBytes": "33554432",  // 最大日志文件大小 (这里是32M)
      "RetainedFileCountLimit": 8        // 最大日志文件数量
    }
  }
}
```

### 专用服务器 
`dedicated.json`
```
{
  "ServerConfiguration": {
    "HostEndpoint": "192.168.1.250",     // 用于游戏客户端连接的服务器公网地址
    "BasePort": 30000,                   // 每个游戏房间的端口号从这里开始
    "MaximumSlots": 10000                // 最大房间数
  },
  "Serilog": {
    "File": {
      "FileSizeLimitBytes": "33554432",
      "RetainedFileCountLimit": 8
    }
  }
}
```

### 状态Api服务器
`api.json`
```
{
  "Urls": "http://0.0.0.0:23280",         // 用于api服务器监听的端口和地址  
  "Status": {
    "MinimumAppVersion": "1.37.0",        // 最低支持的游戏版本
    "ServerDisplayName": "My BeatTogether Server",
    "ServerDescription": "My Cross Platform Custom Song Multiplayer Server",
    "ServerImageUrl": "",
    "ServerSupportsPPModifiers": true,    // 服务器是否支持每个玩家不同的游戏修改项
    "ServerSupportsPPDifficulties": true, // 服务器是否支持每个玩家不同的难度
    "RequiredMods": [                     // 游戏mod的版本要求，如果安装了
      {
        "id": "MultiplayerCore",
        "version": "1.5.1"
      },
      {
        "id": "BeatTogether",
        "version": "2.0.0"
      },
      {
        "id": "BeatSaberPlus_SongOverlay",
        "version": "4.6.1"
      },
      {
        "id": "EditorEX",
        "version": "1.2.0"
      },
      {
        "id": "LeaderboardCore",
        "version": "1.2.2"
      }
    ]
  },
  "Serilog": {
    "File": {
      "FileSizeLimitBytes": "33554432",
      "RetainedFileCountLimit": 8
    }
  }
}
```

### BeatTogether Mod 设置
`BeatSaber/Userdata/`中的`BeatTogether.json` 
```
{
  ...
  "Servers": [
    ...
    {                                                       // 添加这一段
      "ServerName": "Some Server Name",
      "HostName": "192.168.1.250",                          // 主服务器的地址
      "ApiUrl": "http://192.168.1.250:8989",                // 将地址和端口替换为主服务器的
      "StatusUri": "http://192.168.1.250:23280/status",     // 将地址和端口替换为api服务器的
      "MaxPartySize": 10
    },
    ...
  ]
  ...
}
```

# BeatTogether-DockerCompose
[![en](https://img.shields.io/badge/lang-en-blue.svg?style=for-the-badge)](/README.md)
[![zh](https://img.shields.io/badge/语言-中文-red.svg?style=for-the-badge)](/README.zh.md)

A simple guide on hosting the **Scalable** [BeatTogether](https://github.com/BeatTogether) server using docker compose.

If you are hosting a small multiplayer server, consider using the [UnifiedServer](https://github.com/BeatTogether/BeatTogether.UnifiedServer) instead.

You are welcomed to use the discussion board. [Discussions](https://github.com/qe201020335/BeatTogether-DockerCompose/discussions)

## How to Host
### Fresh Start
  0. Make sure you are on Linux and have Docker installed
  1. Clone or download this repo
  2. Modify the json config files for your need. See the limited docs [below](#json-configs).
  3. Make sure your current working directory is the root of this repo
  4. `docker compose up -d` to start the servers
  5. Add this server to the BeatTogether mod's config. See [below](#beattogether-mod-configuration)

### Update Existing Server
  1. Stop the servers with `docker compose down` if not already
  2. `docker compose pull` to get the latest images
  3. IMPORTANT: Compare your config files with the ones from this repo and update yours if needed. The config structure may have changed according to server updates
  4. `docker compose up -d` to start the servers

## Json Configs
 - The options included here are not exhaustive but most likely what matters. 
 - You need to look into the servers' source code to find more available options
 - The config structure may change along BeatTogether server updates

### The Master Server 
`master.json`
```
{
  "Urls": "http://0.0.0.0:8989",         // ip and port that the master server listens on
  "ServerConfiguration": {
    "AuthenticateClients": true          // Whether clients should be authenticated
  },
  "Serilog": {
    "File": {
      "FileSizeLimitBytes": "33554432",  // max log file size limit (32M in this case)
      "RetainedFileCountLimit": 8        // max number of log files 
    }
  }
}
```

### The Dedicated Server 
`dedicated.json`
```
{
  "ServerConfiguration": {
    "HostEndpoint": "192.168.1.250",     // public ip for game clients to connect to
    "BasePort": 30000,                   // the port for individual lobbies starts here
    "MaximumSlots": 10000                // max number of lobbies 
  },
  "Serilog": {
    "File": {
      "FileSizeLimitBytes": "33554432",
      "RetainedFileCountLimit": 8
    }
  }
}
```

### The Status Api Server 
`api.json`
```
{
  "Urls": "http://0.0.0.0:23280",         // ip and port for the api server to listen on    
  "Status": {
    "MinimumAppVersion": "1.37.0",        // minimum supported game version
    "ServerDisplayName": "My BeatTogether Server",
    "ServerDescription": "My Cross Platform Custom Song Multiplayer Server",
    "ServerImageUrl": "",
    "ServerSupportsPPModifiers": true,    // Whether the server supports per-player gameplay modifiers
    "ServerSupportsPPDifficulties": true, // Whether the server supports per-player difficulties
    "RequiredMods": [                     // minimum required mod versions, if installed
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

### BeatTogether Mod Configuration
`BeatTogether.json` in `BeatSaber/Userdata/`
```
{
  ...
  "Servers": [
    ...
    {                                                       // add this
      "ServerName": "Some Server Name",
      "HostName": "192.168.1.250",                          // ip or host name of the master server
      "ApiUrl": "http://192.168.1.250:8989",                // replace the ip and port with the ones for MasterServer
      "StatusUri": "http://192.168.1.250:23280/status",     // replace the ip and port with the ones for status api server
      "MaxPartySize": 10
    },
    ...
  ]
  ...
}
```

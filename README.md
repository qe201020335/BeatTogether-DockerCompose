# BeatTogether-DockerCompose
A simple guide on hosting BeatTogether server using docker compose

## How to Host
  0. Make sure you are on Linux and have Docker installed
  1. Clone or download this repo
  2. Modify the json config files for your need. See the limited docs [below](#json-configs).
  3. **Block access to ports 5672 and 15672 from the Internet using firewall such as ufw**
  4. Make sure your current working directory and `PWD` both are the root of this repo 
  5. `docker compose up -d`
  6. Add this server to the BeatTogether mod's config. See [below](#beattogether-mod-configuration)

## Json Configs
### The Master Server 
`master.json`
```
{
  "MasterServer": {
    "EndPoint": "0.0.0.0:2328"           // ip and port for the master server to listen on
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
  "Node": {
    "HostName": "192.168.1.250",         // public ip or host name for game clients to connect to
    "BasePort": 30000,                   // the ports for individual lobbies start here
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
  "Urls": "http://0.0.0.0:23280",        // ip and port for the api server to listen on    
  "Status": {
    "MinimumAppVersion": "1.28.0",       // minimum supported game version
    "RequiredMods": [                    // minimum required mod versions, if installed
      {
        "id": "MultiplayerCore",
        "version": "1.2.0"
      },
      {
        "id": "BeatTogether",
        "version": "2.0.1"
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
      "ServerName": "Some Server Namer",
      "HostName": "192.168.1.250",                          // ip or host name of the master server
      "Port": 2328,                                         // port of the master server
      "StatusUri": "http://192.168.1.250:23280/status",     // replace the ip and port with the ones for status api server
      "MaxPartySize": 10
    },
    ...
  ]
  ...
}
```

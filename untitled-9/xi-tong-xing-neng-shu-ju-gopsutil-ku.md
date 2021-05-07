# 系统性能数据gopsutil库

## 1. 系统性能数据gopsutil库 <a id="&#x7CFB;&#x7EDF;&#x6027;&#x80FD;&#x6570;&#x636E;gopsutil&#x5E93;"></a>

### 1.1.1. gopsutil <a id="gopsutil"></a>

psutil是一个跨平台进程和系统监控的Python库，而gopsutil是其Go语言版本的实现。本文介绍了它的基本使用。

Go语言部署简单、性能好的特点非常适合做一些诸如采集系统信息和监控的服务，本文介绍的gopsutil库是知名Python库：psutil的一个Go语言版本的实现。

### 1.1.2. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get github.com/shirou/gopsutil
```

### 1.1.3. 使用 <a id="&#x4F7F;&#x7528;"></a>

#### CPU <a id="cpu"></a>

采集CPU相关信息。

```text
import "github.com/shirou/gopsutil/cpu"


func getCpuInfo() {
    cpuInfos, err := cpu.Info()
    if err != nil {
        fmt.Printf("get cpu info failed, err:%v", err)
    }
    for _, ci := range cpuInfos {
        fmt.Println(ci)
    }
    
    for {
        percent, _ := cpu.Percent(time.Second, false)
        fmt.Printf("cpu percent:%v\n", percent)
    }
}
```

获取CPU负载信息：

```text
import "github.com/shirou/gopsutil/load"

func getCpuLoad() {
    info, _ := load.Avg()
    fmt.Printf("%v\n", info)
}
```

#### Memory <a id="memory"></a>

```text
import "github.com/shirou/gopsutil/mem"


func getMemInfo() {
    memInfo, _ := mem.VirtualMemory()
    fmt.Printf("mem info:%v\n", memInfo)
}
```

#### Host <a id="host"></a>

```text
import "github.com/shirou/gopsutil/host"


func getHostInfo() {
    hInfo, _ := host.Info()
    fmt.Printf("host info:%v uptime:%v boottime:%v\n", hInfo, hInfo.Uptime, hInfo.BootTime)
}
```

#### Disk <a id="disk"></a>

```text
import "github.com/shirou/gopsutil/disk"


func getDiskInfo() {
    parts, err := disk.Partitions(true)
    if err != nil {
        fmt.Printf("get Partitions failed, err:%v\n", err)
        return
    }
    for _, part := range parts {
        fmt.Printf("part:%v\n", part.String())
        diskInfo, _ := disk.Usage(part.Mountpoint)
        fmt.Printf("disk info:used:%v free:%v\n", diskInfo.UsedPercent, diskInfo.Free)
    }

    ioStat, _ := disk.IOCounters()
    for k, v := range ioStat {
        fmt.Printf("%v:%v\n", k, v)
    }
}
```

#### net IO <a id="net-io"></a>

```text
import "github.com/shirou/gopsutil/net"

func getNetInfo() {
    info, _ := net.IOCounters(true)
    for index, v := range info {
        fmt.Printf("%v:%v send:%v recv:%v\n", index, v, v.BytesSent, v.BytesRecv)
    }
}
```

#### 获取本机IP的两种方式 <a id="&#x83B7;&#x53D6;&#x672C;&#x673A;ip&#x7684;&#x4E24;&#x79CD;&#x65B9;&#x5F0F;"></a>

```text
func GetLocalIP() (ip string, err error) {
    addrs, err := net.InterfaceAddrs()
    if err != nil {
        return
    }
    for _, addr := range addrs {
        ipAddr, ok := addr.(*net.IPNet)
        if !ok {
            continue
        }
        if ipAddr.IP.IsLoopback() {
            continue
        }
        if !ipAddr.IP.IsGlobalUnicast() {
            continue
        }
        return ipAddr.IP.String(), nil
    }
    return
}
```

或：

```text

func GetOutboundIP() string {
    conn, err := net.Dial("udp", "8.8.8.8:80")
    if err != nil {
        log.Fatal(err)
    }
    defer conn.Close()

    localAddr := conn.LocalAddr().(*net.UDPAddr)
    fmt.Println(localAddr.String())
    return localAddr.IP.String()
}
```


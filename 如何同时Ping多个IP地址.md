遇到些问题需要同时ping多个地址来检查网络状态，找了一圈都是用批处理实现的简单同网段批量ping

既然没有现成的那就自己写个吧。。。。

https://github.com/dawn-lc/Pings
# Pings

高效、轻量的实时网络监控工具，支持对多个目标的ping以及记录延迟波动、链接状态、丢包率等功能。

## 下载

Pings.exe 为.NET8原生AOT编译版本
[Github下载](https://github.com/dawn-lc/Pings/releases/download/latest/Pings.exe)

upx-Pings.exe 为使用UPX压缩后的版本
[Github下载](https://github.com/dawn-lc/Pings/releases/download/latest/upx-Pings.exe)

请根据需求自行选择使用的版本

## 使用方法
### 配置文件
Pings 使用一个简单的配置文件 config.txt 来定义监控的目标。配置文件的每一行代表一个监控规则，格式如下：
```
<名称> <IP地址或域名> <超时时间> <丢包率统计范围> <延迟波动的阈值>
```
 * <名称>: 监控规则的名称，可以是任何描述性的字符串。
 * <IP地址或域名>: 需要监控的目标，可以是 IP 地址或域名。
 * <超时时间>: （可选）超时时间，单位为毫秒。如果未指定，默认为 1000 毫秒。
 * <丢包率统计范围>: （可选）丢包率统计范围。如果未指定，默认为 255。例如，200 表示统计最近 200 个数据包的丢包率。
 * <延迟波动的阈值>: （可选）延迟波动的阈值，单位为毫秒。如果未指定，默认为 20 毫秒。例如，50 表示两个数据包的延迟差值超过 50 毫秒即为延迟波动。
### 示例 config.txt 文件:
```
example1 192.168.1.1 2000 150 25
example2 www.example.com 2500 100 30
example3 8.8.8.8
```
### 运行
配置完成后，可以直接启动，也可以通过以下命令启动 Pings:
```
Pings.exe <配置文件路径>
```
如果没有指定配置文件路径，将默认使用当前目录下的 config.txt 文件。

# 默认配置

- IP: `10.1.1.254`
- 用户名: `root`
- 密码: `无`
- 插件:
```
DockerMan
ShadowSocksR Plus+
PassWall
Openclash
Netdata
Autotimeset
AdGuardHome
Mosdns
Lucky
ZeroTier
Openvpn
软件仓库(iStore)
```

---
# 使用说明

## Fork 本项目  ==>  Actions  ==>  选择项目名称yml文件  ==>  Run Workflow

|项目名称|编译目标|编译状态|说明|
|-----------------|------------------|-------------------------|---------------------------------------------------------------------------------------|
|LEDE|x86_64|[![LEDE_x86_64](https://github.com/lmxslpc/OpenWrt-Build-System/actions/workflows/LEDE_x86_64.yml/badge.svg)](https://github.com/lmxslpc/OpenWrt-Build-System/actions/workflows/LEDE_x86_64.yml)|内核版本编译出来的是6.6.46，内核比较新，流行插件支持新内核的少，需要注意。另外，ISO安装约占硬盘空间700M，Docker需要额外手动挂载添加的硬盘作为工作目录。|
|ImmortalWrt|x86_64|[![immortalwrt_x86_64_23.05](https://github.com/lmxslpc/OpenWrt-Build-System/actions/workflows/immortalwrt_x86_64.yml/badge.svg)](https://github.com/lmxslpc/OpenWrt-Build-System/actions/workflows/immortalwrt_x86_64.yml)|待加入说明|
|iStoreOS|x86_64|[![iStoreOS_x86_64_22.03](https://github.com/YingziWo/OpenWrt-Build-System/actions/workflows/istoreos_x86_64_22.03.yml/badge.svg)](https://github.com/YingziWo/OpenWrt-Build-System/actions/workflows/istoreos_x86_64_22.03.yml)|内核版本是5.10.221，内核支持大多数插件，ISO编译安装到机器上占约3GB硬盘空间，2GB自动分配给了Docker使用|
   
---

# 手动选择编译项目名称，编译过程会暂停，通过SSH接入进行干预

## [![Manually with SSH](https://github.com/lmxslpc/OpenWrt-Build-System/actions/workflows/Manually%20with%20SSH.yml/badge.svg?branch=master&event=workflow_dispatch)](https://github.com/lmxslpc/OpenWrt-Build-System/actions/workflows/Manually%20with%20SSH.yml)
可用于修改编译内容，注意必须得frpc服务穿透成功，且要可用的情况下，SSH终端能连得上穿透出来的编译服务器，才支持在云编译服务器暂停编译的情况下修改编译内容。
用这个yml文件直接Run Workflow，可以手动选择上面项目名称的任何一个，不过也意味着也选择了编译过程中会暂停，等待连接SSH终端。
  
   

---
# 使用SSH连接Action

#### SSH连接命令

在工作流的Setup ssh for debug步骤中会显示
```
============frpc启动成功!===========
==========以下是SSH连接命令==========
ssh root@xxx -p xxxx
```
密码为SSH_PW定义的密码,不设置则为123
登陆后使用su runner命令切换到runner用户进行编译
   
---
#### 自定义选项(可选)
```
点击仓库的Settings  ==> Actions secrets and variables ==> Actions ==> New repository secret ==> 填写Secret 信息
```
|Name |Secret值说明|缺省支持不设置|
|----------|--------------------------------------------------|--------|
|SSH_PW|   用于定义ssh访问的root密码,不设置默认123|可不设置|
| FRPC_CONFIG|  用于定义frpc的配置文件,不设置将自动尝试使用公共frp服务器并生成SSH连接命令|可不设置|
| IYUU_TOKEN| 爱语飞飞Token,用于通知编译结果,前往[官网](https://iyuu.cn/)申请|可不设置|
| SERVERCHAN_SCKEY| Server酱SendKey,用于通知编译结果,前往[官网](https://sct.ftqq.com/)申请|可不设置|
###### 爱语飞飞,Server酱二选一即可，也支持都不用。
  

---
#### FRPC_CONFIG示例
##### frp.freefrps.com是个公共服务器,所以可能会与他人配置冲突,默认会随机生成端口并尝试连接
```
[common]
server_addr = frp.freefrps.com
server_port = 7100
token = freefrps.com

[ssh2action]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 22222  
```
##### `备选公共服务器frp1.freefrp.net;frp2.freefrp.net;www.freefrps.com`
##### 若使用公共服务器只需要修改frpc.ini.example中  [common]  部分的内容,
   
---

# 添加新项目
   

#### 辅助配置文件说明
| 目录         |         作用        |文件名格式                   |
| ------------| --------------------| --------------------|
| config      | 放置.config配置文件   |   项目名称_编译目标.config              |
| customize   | 放置自定义脚本及文件    |   项目名称_编译目标.sh             |
| feeds       | 放置feeds源           |    项目名称_编译目标            |
| patches     | 放置补丁文件           |                |
   
---   
#### yml文件中环境变量说明
|变量 |说明|
|----------|--------------------------------------------------|
|BD_PROJECT|   项目名称|
|  BD_TARGET|  编译目标|
| REPO_URL| 项目地址|
| REPO_BRANCH|  项目分支|
| TARGET_PLATFORM|  平台架构(amd64/arm64)|
| SSH_DEBUG| 是否开启SSH功能(true/false)|
| SSH_TIME|    设置开始编译前暂停时间,可用gogogo命令提前继续工作流|
|SSH_TIME2|   设置编译报错后暂停时间,可用gogogo命令提前继续工作流|
| CACHE_CCACHE  |    是否开启ccache缓存功能,不开启则只缓存工具链(true/false)|
| CACHE_CLEAN  |    是否清除缓存(true/false)|
| UPLOAD_ARTIFACT|   是否上传到ARTIFACT(true/false)|
| UPLOAD_RELEASE|    是否上传到RELEASE(true/false)|

---

# 云编译的ISO下载安装后的样式

Lede OpenWrt
---
<img src="https://github.com/YingziWo/OpenWrt-Build-System/blob/master/ass/LedeOP%E7%B3%BB%E7%BB%9F.JPG" width="700">
---
<img src="https://github.com/YingziWo/OpenWrt-Build-System/blob/master/ass/LedeOP%E7%B3%BB%E7%BB%9F1.JPG" width="700">
---
iStoreOS
---
<img src="https://github.com/YingziWo/OpenWrt-Build-System/blob/master/ass/iStore%E7%B3%BB%E7%BB%9F01.JPG" width="700">
---
<img src="https://github.com/YingziWo/OpenWrt-Build-System/blob/master/ass/iStore%E7%B3%BB%E7%BB%9F02.JPG" width="700">
---

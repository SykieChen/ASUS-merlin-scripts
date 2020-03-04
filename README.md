# ASUS-merlin-scripts

Some scripts running on router

## 华硕路由修改区域

必须安装 Merlin 固件。

控制国家码的部分在 CFE 不能修改，且 nvram 修改后重启系统/服务即失效。
因此加到系统启动和服务启动的脚本即可。

```shell
echo "#!/bin/sh" > /jffs/scripts/init-start
echo "nvram unset territory_code" >> /jffs/scripts/init-start
echo "nvram set location_code=AA" >> /jffs/scripts/init-start
ln -s /jffs/scripts/init-start /jffs/scripts/service-event
chmod a+rx /jffs/scripts/*
```

并在 web 设置中 `Administration -> System` 启用用户脚本。
其中 AA 为亚洲区，信号功率25，频段不全。需要其他频段时可设为 JP 或 TW。

[User scripts · RMerl/asuswrt-merlin Wiki · GitHub](https://github.com/RMerl/asuswrt-merlin/wiki/User-scripts)

## 华硕路由添加 DDNS 服务

以 dockdns 为例，必须安装 Merlin 固件。

```shell
vi /jffs/scripts/ddns-start
```

``` bash
# register a subdomain at https://www.duckdns.org/ to get your token
SUBDOMAIN="your_subdomain"
TOKEN="your-token"

# no modification below needed
curl --silent "https://www.duckdns.org/update?domains=$SUBDOMAIN&token=$TOKEN&ip=$1" >/dev/null 2>&1
if [ $? -eq 0 ];
then
    /sbin/ddns_custom_updated 1
else
    /sbin/ddns_custom_updated 0
```

```shell
chmod a+rx /jffs/scripts/*
```

并在 web 设置中 `Administration -> System` 启用用户脚本，在 `WAN -> DDNS -> DDNS Service` 中设置为`custom` 即可。
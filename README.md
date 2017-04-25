该监控基于iostat,然后iostat 命令用来监视系统输入/输出设备负载，原作者地址:https://github.com/z-engine/zabbix-iostat,做了模板的汉化，和部分调整
### 1.安装IOSTAT
yum install sysstat
测试iostat 查看所有硬盘io
```
[root@ZQLC-1 zabbix-iostat-master]# iostat 
Linux 2.6.32-696.1.1.el6.x86_64 (ZQLC-1)        2017年04月25日  _x86_64_        (16 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.04    0.00    0.04    0.13    0.00   99.79

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
sda               8.82         0.34       384.29     315294  360110112
dm-0             47.99         0.32       383.80     303970  359655464
dm-1              0.00         0.00         0.00       2400          0
dm-2              0.06         0.00         0.49       2250     454576
```
### 2.部署脚本
```
git clone https://github.com/bluetom520/DISK-IO
# 添加脚本权限
mv DISK-IO/zabbix-iostat.sh /usr/local/bin/
chmod +x /usr/local/bin/zabbix-iostat.sh
# 设置参数
echo 'UserParameter=iostat[*],/usr/local/bin/zabbix-iostat.sh "$1" "$2"' > /etc/zabbix/zabbix_agentd.d/iostat.conf
# 或者
# mv zabbix-iostat/iostat.conf /etc/zabbix/zabbix_agentd.d/iostat.conf
#重启zabbix_agentd
systemctl restart zabbix-agentd
# 测试自动发现
zabbix_agentd -t iostat[discovery]
```
### 3 加入crontab
crontab -e
```
* * * * * ( sleep 10 && iostat -dxk 1 20 > /tmp/iostat.tmp && mv /tmp/iostat.tmp /tmp/iostat.log )
* * * * * ( sleep 40 && iostat -dxk 1 20 > /tmp/iostat.tmp && mv /tmp/iostat.tmp /tmp/iostat.log )

```
测试监控项
```
[root@ZQLC-1 zabbix-iostat-master]# zabbix_agentd -t iostat[sda,rkb/s]
iostat[sda,rkb/s]                             [t|0.0085]
```
### 4 链接模板（林妹妹翻译）
监控项如图
![](leanote://file/getImage?fileId=58fee20a3210052f10000000)



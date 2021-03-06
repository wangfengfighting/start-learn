# nmcli基本使用
```bash
" 命令帮助
nmcli help
" 显示NetworkManager总体状态
nmcli general status
" 显示所有链接
nmcli connection show
" 显示当前活动链接
nmcli con show -a
" 显示NetworkManager识别到的设备状态
nmcli device status
" nmcli互动链接编辑器
nmcli con edit
```
## 实例
### 通过nmcli增加dns-search
```bash
" 直接命令行增加dns-search,逗号空格均可以
$ nmcli con modify ens33 ipv4.dns-search aaa.start-learn.win,bbb.start-learn.win
$ systemctl restart NetworkManager
```
### 增加一个静态IP到允许DHCP的NetworkManager链接上
```bash
" 这里假设网卡名字为eth0
nmcli con edit eth0
" tab可以命令补全，便于操作
" 显示ipv4的信息
> print ipv4
" 设置两个静态IP到链接上
> set ipv4.addresses 192.168.1.100/24,192.168.1.127/24
" 会有如下提示：
" Do you also want to set 'ipv4.method' to 'manual'? [yes]: no
" 设置为no，保持链接为DHCP-enabled状态，既可以动态获取IP，也能让你配置的静态IP生效
> save

cat /etc/sysconfig/network-scripts/ifcfg-eth0
```
### 增加一个以太配置
``` bash
> nmcli con con-name eth0 ifname eth0 type ethernet ip4 192.168.100.100/24 gw4 192.168.100.2
> nmcli con edit eth0
" 增加DNS search的域
> set ipv4.dns-search aaa.start-learn.win,bbb.start-learn.win
> save
" con-name 后的名字将在/etc/sysconfig/network-scripts/ifcfg-${con-name}
```

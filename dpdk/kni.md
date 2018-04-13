# KNI学习
```
DPDK的KNI允许用户空间程序访问Linux控制平面，好处如下：
    * 比现有的Linux TUN/TAP接口更快（消除了系统调用copy_to_user/copy_from_user）
    * 允许使用标准Linux网络工具(ethtool,ifconfig,tcpdump...)来管理DPDK端口
    * 提供了标准网络协议栈的接口
```
## DPDK KNI内核模块
```
此模块为两种类型的设备提供支持
1. 杂项设备(/dev/kni)：
2. 网络设备：
```

## KNI的创建和删除
```
KNI接口是由DPDK应用程序动态创建的。接口名称和FIFO详细信息由应用程序通过使用rte_kni_device_info结构的ioctl调用提供，其中包含：
    * 接口名称
    * 相关FIFO的相应memzones的物理地址
    * mbuf内存池的详细信息，包括物理和虚拟（用于计算mbuf指针的偏移）
    * PCI信息
    * CPU亲和
rte_kni_common.h中可以参见更多详情

KNI接口创建后可以被DPDK应用程序动态删除。所有未删除的KNI接口将会在其他设备的释放操作中删除。
```

## 示例KNI程序说明
```
程序主体结构：
    1. EAL初始化
    2. 传参分析
    3. 创建mbuf内存池(rte_pktmbuf_pool_create)
    4. kni子系统的初始化(rte_kni_init)
    5. 初始化每个端口（port统一说成端口，端口从属以太设备）
        a. rte_eth_dev_configure获取端口配置信息
        b. rte_eth_rx_queue_setup/rte_eth_tx_queue_setup初始化RX/TX队列
        c. rte_eth_dev_start启动端口
        d. rte_eth_promiscuous_enable，若有需要的，可以设置混杂模式
    6. 于各核上启动功能线程
    7. 程序清理，资源释放
```
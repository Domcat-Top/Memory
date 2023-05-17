## Linux问题随笔

### 1. 本地Linux虚拟机联网

- 虚拟机Vmware设置为`NAT`模式

- 修改网络配置文件：

  ```shell
  # ONBOOT修改为yes
  /etc/sysconfig/network-scripts/ifcfg-ens33
  ```

- 重启Linux网络：

  ```shell
  service network restart
  ```

### 2. vi编辑器卡死解决办法

- 情况：使用`vi`编辑器时，有时可能出现编辑器卡死的状态，不能输入不能退出。其实这不是`vi`编辑器卡死，而是不小心把`vi`编辑器锁定了，编辑器处于假死状态

- 原因：windows下习惯使用 `ctrl + s`来保存文件，但是在vi时，一按`ctrl + s`就会卡死，什么都动不了

- 解决方式：

  ```shell
  # ctrl + s 进入假死状态
  # ctrl + q 解除假死状态
  ```

  

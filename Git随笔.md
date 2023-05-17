## Git随笔

### 1. 本地Git绑定Github

- 设置用户全局签名：会记录在`C:\users\用户\.gitconfig`里面

- 具体操作：

  ```shell
  git config --global user.name "账户名" # 账户名随便写几个字母
  git config --global user.email "邮箱地址" # 邮箱随便写，乱写都可以
  git config --global --list # 检查之前写的是否正确
  ```

- 配置Git绑定GitHub：

  - ```shell
    # 生成ssh
    ssh-keygen -t rsa # 然后一顿回车
    ```

  - 在`C:\User\用户\.ssh`下，有一个rsa后缀的私钥文件，需要严密保管。有一个pub后缀的公钥文件，复制公钥的内容，绑定到GitHub中自己的账号下即可
















































# 在 Ubuntu 上安装 Clash

## 准备

1. 查看当前主机的硬件架构类型：`arch`
2. 查找当前版本所需的 `Clash`：[https://github.com/Dreamacro/clash/releases](https://github.com/Dreamacro/clash/releases)
3. 下载指定 `Clash`，eg：`wget https://github.com/Dreamacro/clash/releases/download/v1.10.6/clash-linux-amd64-v1.10.6.gz`
4. 解压 `CLash` 包，eg：`gunzip clash-linux-amd64-v1.10.6.gz`
5. 使用管理员权限移动到指定目录并重新命名，eg：`sudo mv clash-linux-amd64-v1.10.6 /etc/clash`
6. 使用管理员权限赋予文件执行权限：`sudo chmod +x /etc/clash`
7. 进入 `Clash`目录：`cd /etc`，运行 `Clash`：`./clash`，如果报错一般是网络问题，继续重试此命令
8. 此时会在自动生成 `config.yaml` 和 `Country.mmdb` 两个文件，回到根目录：`cd /`，查找文件位置：，`find . -name config.yaml`，如果找不到，请切换成管理员 `root`
9. 找到路径 `./root/.config/clash/config.yaml`（不一定一样）

## 开始配置

1. 导入您的配置文件，并覆盖原来配置在：`./root/.config/clash/config.yaml`(名字任意)
2. 配置开机自启动 `Clash`: `sudo vi /lib/systemd/system/clash.service`，输入一下内容：

    ```linux
    [Unit]
    Description=Clash Daemon

    [Service]
    ExecStart=/usr/local/bin/clash -d /etc/clash/
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    启用 clash service:
    ```

3. 启动 `Clash`：`./etc/clash`，如果没成功可能是配置文件问题
4. 根据配置文件端口不同，配置端口不一样，指定全局代理：`export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890`
5. 检测是否FQ成功：`wget www.google.com`

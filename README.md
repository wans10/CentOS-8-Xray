# CentOS手动安装Xray

## 一、准备工作
### 设定root密码
### 切换root
```
sudo -i
```
### 设置root密码
```
passwd
```
```
sed -i 's/PermitRootLogin no/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
reboot
```
### 修改en_US.UTF-8
```
echo "export LC_ALL=en_US.UTF-8"  >>  /etc/profile
source /etc/profile
```

## 二、安装并执行更新
```
sudo yum check-update
sudo yum update
sudo reboot
```

## 三、安装CentOS新内核
```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install kernel-ml
```

## 四、安装必要组件
```
yum install -y curl tar socat wget epel-release 
```
## 五、安装并启动Nginx
```
yum install -y nginx
systemctl start nginx
```

## 六、防火墙设置(可选）
VPS开启防火墙，放行指定端口
```
firewall-cmd --state                   # 查看防火墙状态
firewall-cmd --zone=public --add-port=80/tcp --permanent                # 开启防火墙80端口状态
firewall-cmd --zone=public --add-port=443/tcp --permanent               # 开启防火墙443端口状态
firewall-cmd --reload
```
## 或关闭防火墙
```
firewall-cmd --state                   # 查看防火墙状态
systemctl stop firewalld.service       # 停止防火墙
systemctl disable firewalld.service    # 禁止防火墙开机自启
reboot
```

## 七、安装Xray
```
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install -u root
xray uuid           # UUID
```

## 八、安装acme.sh
```
systemctl stop nginx                    # 停止Nginx
curl  https://get.acme.sh | sh -s email=my@example.com         # 替换my@example.com为自己的邮箱地址
~/.acme.sh/acme.sh  --issue -d www.mydomain.com   --standalone   # 替换www.mydomain.com为自己的域名地址
~/.acme.sh/acme.sh --install-cert -d www.mydomain.com --key-file /root/private.key --fullchain-file /root/cert.crt
~/.acme.sh/acme.sh  --upgrade  --auto-upgrade
systemctl enable nginx
systemctl restart nginx
systemctl restart xray
systemctl status xray
```

## 九、安装GOOGLE BBR
```
echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
sudo sysctl net.ipv4.tcp_available_congestion_control
sudo sysctl -n net.ipv4.tcp_congestion_control
lsmod | grep bbr
```

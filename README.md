CentOS 8手动安装Xray

#echo "export LC_ALL=en_US.UTF-8"  >>  /etc/profile
#source /etc/profile

安装更新
#sudo yum check-update
#sudo yum update
sudo reboot

安装新内核
#rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
#yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
#yum --enablerepo=elrepo-kernel install kernel-ml

#yum install -y curl tar socat wget epel-release 
#yum install -y nginx
#systemctl start nginx

（可选）开启防火墙80，443端口
#firewall-cmd --state                   # 查看防火墙状态
#firewall-cmd --zone=public --add-port=80/tcp --permanent
#firewall-cmd --zone=public --add-port=443/tcp --permanent
或
#systemctl stop firewalld.service       # 停止防火墙
#systemctl disable firewalld.service    # 禁止防火墙开机自启
#firewall-cmd --reload
#reboot

#bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install -u root
#xray uuid

#systemctl stop nginx

# 安装acme.sh
curl  https://get.acme.sh | sh -s email=wans10@hotmail.com
~/.acme.sh/acme.sh  --issue -d www.wans10.ga   --standalone
~/.acme.sh/acme.sh --install-cert -d www.wans10.ga --key-file /root/private.key --fullchain-file /root/cert.crt
~/.acme.sh/acme.sh  --upgrade  --auto-upgrade
systemctl enable nginx
systemctl restart nginx
systemctl restart xray
systemctl status xray
rm -rf /usr/share/nginx/html/*
cd /usr/share/nginx/html/
wget https://github.com/V2RaySSR/Trojan/raw/master/web.zip
unzip web.zip
systemctl restart nginx

# 安装GOOGLE BBR
echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
sudo sysctl net.ipv4.tcp_available_congestion_control
sudo sysctl -n net.ipv4.tcp_congestion_control
lsmod | grep bbr

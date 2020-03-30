# -*- mode: ruby -*-
# vi: set ft=ruby :

$scriptvm1 = <<-SCRIPT
sudo yum update -y
sudo dnf -y install epel-release
sudo dnf --enablerepo=epel -y install netdata
curl -O -L "https://github.com/grafana/loki/releases/download/v1.3.0/loki-linux-amd64.zip"
sudo yum install unzip -y
unzip "loki-linux-amd64.zip"
chmod a+x "loki-linux-amd64"
sudo dnf -y install grafana
sudo systemctl enable --now grafana-server.service
sudo useradd -m -s /bin/false prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown prometheus /var/lib/prometheus/
sudo dnf install wget -y
sudo wget https://github.com/prometheus/prometheus/releases/download/v2.14.0/prometheus-2.14.0.linux-amd64.tar.gz -P /tmp
cd /tmp
tar -zxpvf prometheus-2.14.0.linux-amd64.tar.gz
cd /etc/prometheus/
touch /etc/prometheus/prometheus.yml
systemctl start firewalld
sudo firewall-cmd --add-port=3000/tcp --permanent
firewall-cmd --add-port=9090/tcp --permanent
firewall-cmd --add-port=19999/tcp --permanent
firewall-cmd --reload
SCRIPT

$scriptvm2 = <<-SCRIPT
sudo yum update -y
sudo dnf -y install epel-release
sudo dnf --enablerepo=epel -y install netdata
curl -O -L "https://github.com/grafana/loki/releases/download/v1.3.0/promtail-linux-amd64.zip"
sudo yum install unzip -y
sudo unzip "promtail-linux-amd64.zip"
sudo chmod a+x "promtail-linux-amd64"
systemctl start firewalld
firewall-cmd --add-port=19999/tcp --permanent
firewall-cmd --reload
SCRIPT

$scriptvm3 = <<-SCRIPT
sudo yum update -y
yum install httpd -y
sudo dnf -y install epel-release
sudo dnf --enablerepo=epel -y install netdata
systemctl start firewalld
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --add-port=19999/tcp --permanent
firewall-cmd --reload
systemctl start httpd
SCRIPT

vm1 = 0
vm2 = 0
vm3 = 0

servers=[
  {
    :hostname => "vm1",
    :ip => "192.168.59.30",
    :box => "centos/8",
    :ram => 2048,
    :cpu => 1
  },
  {
    :hostname => "vm2",
    :ip => "192.168.59.10",
    :box => "centos/8",
    :ram => 2048,
    :cpu => 1
  },
  {
    :hostname => "vm3",
    :ip => "192.168.59.40",
    :box => "centos/8",
    :ram => 2048,
    :cpu => 1
  }
]

Vagrant.configure(2) do |config|
    servers.each do |machine|
        config.vm.define machine[:hostname] do |node|
            node.vm.box = machine[:box]
            node.vm.hostname = machine[:hostname]
            node.vm.network "private_network", ip: machine[:ip]
            node.vm.provider "virtualbox" do |vb|
            if machine[:hostname] == "vm1"
              if vm1 == 0
                node.vm.provision "shell", inline: $scriptvm1
#              node.vm.provision "ansible" do |ansible|
#                ansible.verbose = "v"
#                ansible.playbook = "playbook.yml"
#                ansible.force_remote_user = tru e
                vm1 = vm1 +1
              end
              end
            end
            if machine[:hostname] == "vm2"
              if vm2 == 0
                node.vm.provision "shell", inline: $scriptvm2
                vm2 = vm2 +1
              end
            end
            if machine[:hostname] == "vm3"
              if vm3 == 0
                node.vm.provision "shell", inline: $scriptvm3
                vm3 = vm3 +1
              end
#            end
            end
        end
    end
end

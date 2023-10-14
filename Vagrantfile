Vagrant.configure("2") do |config|

    config.vm.define "master" do |master|
      master.vm.box = "ubuntu/bionic64"
      master.vm.hostname = "master"
      master.vm.network "private_network", ip: "192.168.56.101"
    end
  
    config.vm.define "backup" do |backup|
      backup.vm.box = "ubuntu/bionic64"
      backup.vm.hostname = "backup"
      backup.vm.network "private_network", ip: "192.168.56.102"
    end
  
    # Provision the VMs with Keepalived
    config.vm.provision "shell", inline: <<-SHELL
apt-get update
apt-get install -y keepalived

# Configure Keepalived on Master
cat > /etc/keepalived/keepalived.conf <<-EOL
vrrp_instance VI_1 {
    state MASTER
    interface enp0s8
    virtual_router_id 51
    priority 101
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.56.200
    }
}
EOL

# Configure Keepalived on Backup
if [[ "$(hostname)" == "backup" ]]; then
    sed -i 's/state MASTER/state BACKUP/g' /etc/keepalived/keepalived.conf
    sed -i 's/priority 101/priority 100/g' /etc/keepalived/keepalived.conf
fi

systemctl enable keepalived
systemctl start keepalived
SHELL
  
end

  
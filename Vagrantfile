Vagrant.configure(2) do |config|
  config.vm.box = "centos7x64"
  config.vm.hostname = "openstack"

  config.vm.network "forwarded_port", guest: 80, host: 8080

  # config.vm.network "private_network", ip: "192.168.33.10"

  # config.vm.network "public_network"

  # config.vm.synced_folder "../data", "/vagrant_data"

  config.vm.provider "virtualbox" do |vb|
  #   vb.gui = true
  #   # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end
  
  config.vm.provision "shell", inline: <<-SHELL
    sudo yum install -y https://www.rdoproject.org/repos/rdo-release.rpm
    sudo yum update -y
    sudo yum install -y openstack-packstack
    sudo systemctl disable NetworkManager
    sudo systemctl enable network    
    sudo systemctl stop NetworkManager
    sudo rm /etc/sysconfig/network-scripts/ifcfg-enp0s3
    sudo cp /vagrant/ifcfg-eth0 /etc/sysconfig/network-scripts/
    sudo packstack --allinone --provision-demo=n --os-neutron-ovs-bridge-mappings=extnet:br-ex --os-neutron-ovs-bridge-interfaces=br-ex:eth0 --os-neutron-ml2-type-drivers=vxlan,flat --os-ceilometer-install=n --nagios-install=n --os-cinder-install=n
    sudo echo 'DNS1=10.0.2.3' >> /etc/sysconfig/network-scripts/ifcfg-br-ex
    #sudo sed -i 's/ServerAlias localhost/ServerAlias localhost\n  ServerAlias 127\.0\.0\.1/g' /etc/httpd/conf.d/15-horizon_vhost.conf
    #sudo httpd -k restart
    #sudo cp /vagrant/ifcfg-eth0-new /etc/sysconfig/network-scripts/ifcfg-eth0
    #sudo cp /vagrant/ifcfg-br-ex /etc/sysconfig/network-scripts/ifcfg-br-ex
    sudo su
    source /root/keystonerc_admin
    curl http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img | glance image-create --name='cirros image' --visibility=public --container-format=bare --disk-format=qcow2
    systemctl restart network
    echo '----------' && echo 'http://localhost:8080' && grep -e 'password' -e 'user' -i /root/keystonerc_admin && echo '----------'
  SHELL
end

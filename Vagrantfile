# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"
  
  #Setup foreman server
   config.vm.define :foreman do |foreman|
	foreman.vm.hostname = "foreman"
	foreman.vm.box = "centos/7"
	#foreman.vm.network "private_network", type:"dhcp"
	foreman.vm.network "private_network", ip: "192.168.2.3"
	foreman.vm.network "forwarded_port",guest:443, host:8443
    foreman.vm.provider :libvirt do |domain|
        domain.cpu_mode = "host-passthrough"
        domain.memory = 8192
        domain.cpus = 4
        domain.driver = "kvm"
        domain.connect_via_ssh = false
        domain.username = "tmilam"
    end
    #foreman.vm.memory "8192"
    #foreman.vm.cpus "4"
  	foreman.vm.provision "shell", inline: <<-SHELL 
		yum update -y
        rpm -ivh https://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
        yum -y install epel-release https://yum.theforeman.org/releases/1.12/el7/x86_64/foreman-release.rpm
        yum -y install foreman-installer
        hostnamectl set-hostname foreman.tryharder.io
        echo "192.168.2.3  foreman.tryharder.io foreman" >> /etc/hosts
        systemctl stop firewalld
        systemctl disable firewalld
        foreman-installer --enable-foreman-plugin-salt --enable-foreman-plugin-bootdisk --enable-foreman-plugin-dhcp-browser --enable-foreman-plugin-digitalocean --enable-foreman-plugin-discovery --enable-foreman-plugin-docker --enable-foreman-plugin-hooks --enable-foreman-plugin-memcache --enable-foreman-plugin-openscap --enable-foreman-plugin-remote-execution --enable-foreman-plugin-tasks --enable-foreman-plugin-templates --enable-foreman-compute-ec2 --enable-foreman-compute-libvirt --enable-foreman-compute-openstack --enable-foreman-compute-ovirt --enable-foreman-compute-vmware --enable-foreman-proxy-plugin-abrt --enable-foreman-plugin-openscap --enable-foreman-proxy-plugin-openscap --enable-foreman-proxy-plugin-remote-execution-ssh --foreman-locations-enabled 
        mkdir -p /usr/share/foreman/.ssh
        chmod 700 /usr/share/foreman/.ssh
        chown foreman:foreman /usr/share/foreman/.ssh
        sudo -u foreman ssh-keygen -t rsa -N '' -f /usr/share/foreman/.ssh/id_rsa
        echo "To setup foreman to add your libvirt hypervisor as a compute resource, follow " 
        echo "these directions: https://www.theforeman.org/manuals/1.12/index.html#5.2.5LibvirtNotes"
	SHELL
   end
  #Setup salt master server
   config.vm.define :salt do |salt|
        salt.vm.hostname = "salt"
        salt.vm.box = "centos/7"
        salt.vm.network "private_network", ip:"192.168.2.2"
        salt.vm.provider :libvirt do |domain|
            domain.cpu_mode = "host-passthrough"
            domain.memory = 4096
            domain.cpus = 2
            domain.driver = "kvm"
            domain.connect_via_ssh = false
            domain.username = "username"
        end
        salt.vm.provision "shell", inline: <<-SHELL
		yum update -y
        rpm -ivh https://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
        yum -y install epel-release https://yum.theforeman.org/releases/1.12/el7/x86_64/foreman-release.rpm
        yum -y install foreman-installer
        hostnamectl set-hostname salt.tryharder.io
        echo "192.168.2.2  salt.tryharder.io foreman" >> /etc/hosts
        systemctl stop firewalld
        systemctl disable firewalld
        foreman-installer --no-enable-foreman --enable-foreman-plugin-salt --enable-foreman-proxy-plugin-salt
	SHELL
   end
end

$script = <<-'SCRIPT'
	apt install curl vim net-tools openssh-server -y
	swapoff -a
	apt update -y
	apt install docker.io -y
	curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
	apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
	apt update -y
	apt install kubeadm kubelet kubectl -y
	apt-mark hold kubelet kubeadm kubectl
	echo '192.168.56.100 master.nono.lan master' >> /etc/hosts
	echo '192.168.56.101 worker-1.nono.lan worker-1' >> /etc/hosts
	echo '192.168.56.102 worker-2.nono.lan worker-2' >> /etc/hosts
	echo '192.168.56.103 worker-3.nono.lan worker-3' >> /etc/hosts
	echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
	sysctl -p
	echo "root:root" | chpasswd 

SCRIPT

Vagrant.configure("2") do |config|
    config.vm.define "master" do |master|
    master.vm.box = "generic/ubuntu1804"
    master.vm.hostname = "master.nono.lan"
    #master.vm.network "public_network", ip: "192.168.25.100"
    master.vm.network "private_network", ip: "192.168.56.100"
    master.vm.provision "shell", inline: "echo hello from Master"
    master.vm.provision "shell", inline: $script
    master.vm.box_download_insecure=true
    master.vm.provider "virtualbox" do |vb|
	vb.customize ['modifyvm', :id, '--natnet1', '192.168.222.0/24']
    	# vb.gui = true
   	vb.memory = 2048
    	vb.cpus = 2
   end
end
 (1..2).each do |i|
    config.vm.define "worker-#{i}" do |worker|
      worker.vm.box = "generic/ubuntu1804"
      worker.vm.hostname = "worker#{i}.nono.lan"
      #worker.vm.network "public_network", ip: "192.168.25.#{i}"
      worker.vm.network "private_network", ip: "192.168.56.10#{i}"
      worker.vm.provision "shell", inline: "echo hello from node #{i}"
      worker.vm.provision "shell", inline: $script
      worker.vm.box_download_insecure=true
      worker.vm.provider "virtualbox" do |vb|
        vb.customize ['modifyvm', :id, '--macaddress1', "080027b04f7#{i}"]
	vb.customize ['modifyvm', :id, '--natnet1', '192.168.222.0/24']
      	# vb.gui = true
      	vb.memory = 1024
      	vb.cpus = 1
    end
  end

  
end
end

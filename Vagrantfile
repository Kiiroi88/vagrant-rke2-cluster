# -*- mode: ruby -*-
# vi: set ft=ruby :

$install_rke2_script = <<SCRIPT
curl -sfL https://get.rke2.io | sh -
echo Installing 'RKE2'...
systemctl enable rke2-server.service
systemctl start rke2-server.service
cat <<EOF > /vagrant/worker_token
server: https://192.168.51.100:9345
token: $(cat /var/lib/rancher/rke2/server/agent-token)
EOF
cp /etc/rancher/rke2/rke2.yaml /vagrant/
SCRIPT

$install_rke2_worker = <<SCRIPT
curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE="agent" sh -
echo Installing 'RKE2'...
systemctl enable rke2-server.service
sudo mkdir -p /etc//rancher/rke2/
until [ -s /vagrant/worker_token ]; do
  echo "Waiting for token..."
  sleep 1
  ls -la /vagrant/ > /dev/null 2>&1
done
echo "Token found!"
cp /vagrant/worker_token /etc/rancher/rke2/config.yaml
systemctl start rke2-agent.service
rm -f /vagrant/worker_token
SCRIPT


Vagrant.configure("2") do |config|

  vm_box = 'generic/ubuntu2204'

  config.vm.provider :libvirt do |libvirt|
    libvirt.qemu_use_session = false
  end

  config.vm.define :manager, primary: true  do |manager|
    manager.vm.box = vm_box
    manager.vm.box_check_update = false
    manager.vm.network :private_network, :ip => "192.168.51.100"
    manager.vm.hostname = "manager"
    manager.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_udp: false
    manager.vm.provision "shell", inline: $install_rke2_script, privileged: true
    manager.vm.provider "libvirt" do |v|
      v.cpus = 4
      v.memory = "8192"
    end
  end
  
  (1..2).each do |i|
    config.vm.define "worker0#{i}" do |worker|
      worker.vm.box = vm_box
      worker.vm.box_check_update = false
      worker.vm.network :private_network, :ip => "192.168.51.10#{i}"
      worker.vm.hostname = "worker0#{i}"
      worker.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_udp: false
      worker.vm.provision "shell", inline: $install_rke2_worker, privileged: true
      worker.vm.provider "libvirt" do |v|
        v.cpus = 4
        v.memory = "8192"
      end
    end
  end
end
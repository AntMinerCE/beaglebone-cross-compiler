Vagrant.configure("2") do |config|
  config.vm.hostname = "beaglebone-cross-compiler"

  config.vm.box = "trusty64"
  is_vmware = !!(defined? HashiCorp)

  RAM = 4096
  CPUS = `#{RbConfig::CONFIG['host_os'] =~ /darwin/ ? 'sysctl -n hw.ncpu' : 'nproc'}`.strip

  config.vm.network "private_network", ip: "192.168.49.7"

  config.vm.provider :vmware_fusion do |v|
    v.vmx['memsize'] = RAM
    v.vmx['numvcpus'] = CPUS
  end

  config.vm.provider :virtualbox do |v|
    v.customize ['modifyvm', :id, '--memory', RAM]
    v.customize ['modifyvm', :id, '--cpus', CPUS]
  end

  config.ssh.forward_agent = true

  config.vm.provision "shell", inline: <<-SCRIPT
  set -e
  apt-get update
  apt-get -fy install
  apt-get -y install git-core make gawk diffstat texinfo chrpath build-essential
  SCRIPT

  config.vm.provision "shell", privileged: false, inline: <<-SCRIPT
  git clone git://github.com/jameshilliard/setup-A8.git
  cd setup-A8
  sed -i.bak '/INHERIT.*rm_work/d' conf/local.conf
  MACHINE=beaglebone ./oebb.sh config beaglebone
  MACHINE=beaglebone ./oebb.sh update
  MACHINE=beaglebone ./oebb.sh bitbake virtual/kernel
  SCRIPT
end

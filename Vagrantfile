# -*- mode: ruby -*-
ROOT = File.dirname(File.absolute_path(__FILE__))

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Every Vagrant development environment requires a box. You can search for
# boxes at https://atlas.hashicorp.com/search.
VAGRANT_BOX = ENV["VAGRANT_BOX"] || "debian/stretch64"

$script = <<SCRIPT
apt-get update
apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
apt-get update -qq
apt-get install -q -y --force-yes docker-ce qemu-user-static
usermod -aG docker vagrant
docker version
SCRIPT

# For a complete reference, please see the online documentation at
# https://docs.vagrantup.com.
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = VAGRANT_BOX
  config.ssh.forward_agent = true
  config.vm.provider "virtualbox" do |v|
    host = RbConfig::CONFIG["host_os"]
    if host =~ /darwin/
      cpus = `sysctl -n hw.ncpu`.to_i
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
    elsif host =~ /linux/
      cpus = `nproc`.to_i
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
    # TODO: Find out how to get CPU and memory on Windows, these hardcoded values should work for now.
    else
      cpus = 2
      mem = 1024
    end
    # Don't use more than 4 cpus
    v.cpus = [cpus, 4].min
    # Don't use more than 2GB of RAM
    v.memory = [mem, 2048].min
  end
  if Dir.glob("#{File.dirname(__FILE__)}/.vagrant/machines/default/*/id").empty?
    config.vm.provision :shell, :inline => $script
  end
end
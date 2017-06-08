# -*- mode: ruby -*-
# vi: set ft=ruby :

GUEST_IP = "192.168.44.101"
HOST_MEMORY_USAGE_PERCENT = 50

HOST_OS = RbConfig::CONFIG['host_os']
if HOST_OS =~ /darwin/
  CPUS = `sysctl -n hw.ncpu`.to_i
  # sysctl returns Bytes and we need to convert to MB
  MEMORY = `sysctl -n hw.memsize`.to_i / 1024 / 1024 * HOST_MEMORY_USAGE_PERCENT / 100
  USER_HOME_DIR = Dir.home
elsif HOST_OS =~ /linux/
  CPUS = `nproc`.to_i
  # meminfo shows KB and we need to convert to MB
  MEMORY = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 * HOST_MEMORY_USAGE_PERCENT / 100
else # Windows
  CPUS = `wmic cpu get NumberOfLogicalProcessors`.split("\n")[2].to_i
  # wmic shows KB and we need to convert to MB
  MEMORY = `wmic OS get TotalVisibleMemorySize`.split("\n")[2].to_i / 1024 * HOST_MEMORY_USAGE_PERCENT / 100
  USER_HOME_DIR = `echo %userprofile%`.strip
end

Vagrant.configure(2) do |config|
  config.nfs.map_uid = Process.uid
  config.nfs.map_gid = Process.gid
  config.vm.define "puzzle15" do |puzzle15|
    puzzle15.vm.box = "ubuntu/xenial64"
	puzzle15.vm.box_check_update = false

	puzzle15.vm.hostname = "puzzle15.local"

	puzzle15.vm.network "private_network", ip: GUEST_IP
	puzzle15.vm.synced_folder ".", "/puzzle15/",
	  id: "vagrant-root",
	  type: "nfs"

	puzzle15.vm.provider "virtualbox" do |vb|
	  vb.gui = false
	  vb.name = "puzzle15"
      vb.cpus = CPUS
      vb.memory = MEMORY
	end
  end
end

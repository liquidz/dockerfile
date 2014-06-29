# -*- mode: ruby -*-
# vi: set ft=ruby :

# c.f. https://vagrantcloud.com/yungsang/boot2docker/versions

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 1.6.3"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "boot2docker"
  config.vm.box = "yungsang/boot2docker"

  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.synced_folder "~/src", "/vagrant", type: "nfs"

  # Uncomment below to use more than one instance at once
  # config.vm.network :forwarded_port, guest: 2375, host: 2375, auto_correct: true

  # Uncomment below not to use the old port 4243
  # config.vm.network :forwarded_port, guest: 2375, host: 4243, disabled: true

  # Fix busybox/udhcpc issue
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      if ! grep -qs ^nameserver /etc/resolv.conf; then
        sudo /sbin/udhcpc
      fi
      cat /etc/resolv.conf
    EOT
  end

  # Adjust datetime after suspend and resume
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      sudo /usr/local/bin/ntpclient -s -h pool.ntp.org
      date
    EOT
  end

  config.vm.provision :docker do |d|
    d.pull_images "yungsang/busybox"
    d.run "simple-echo",
      image: "yungsang/busybox",
      args: "-p 8080:8080",
      cmd: "nc -p 8080 -l -l -e echo hello world!"
  end

  config.vm.network :forwarded_port, guest: 8080, host: 8080
end

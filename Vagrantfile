# -*- mode: ruby -*-
# vi: set ft=ruby :

g_bridge = "Intel(R) I211 Gigabit Network Connection"

servers = [
  {
    :type => "master",
    :hostname => "master0",
    :ip => "192.168.76.150",
    :box => "generic/alpine312",
    :ram => 1024,
    :cpus => 4,
    :gui => false,
    :shared_folder_local => "./data/master0",
    :shared_folder_remote => "/etc/hashicorp.d",
    :provision => <<-SHELL
    apk add nginx
    mkdir -p /run/nginx
    chown vagrant:vagrant /run/nginx
    echo "If needed, start nginx with /etc/hashicorp.d/nginx.d/"
    SHELL
  },
  {
    :type => "master",
    :hostname => "master1",
    :ip => "192.168.76.151",
    :box => "generic/alpine312",
    :ram => 1024,
    :cpus => 4,
    :gui => false,
    :shared_folder_local => "./data/master1",
    :shared_folder_remote => "/etc/hashicorp.d",
    :provision => ""
  },
  {
    :type => "master",
    :hostname => "master2",
    :ip => "192.168.76.152",
    :box => "generic/alpine312",
    :ram => 1024,
    :cpus => 4,
    :gui => false,
    :shared_folder_local => "./data/master2",
    :shared_folder_remote => "/etc/hashicorp.d",
    :provision => ""
  },
  {
    :type => "slave",
    :hostname => "slave0",
    :ip => "192.168.76.160",
    :box => "generic/alpine312",
    :ram => 512,
    :cpus => 2,
    :gui => false,
    :shared_folder_local => "./data/slave0",
    :shared_folder_remote => "/etc/hashicorp.d",
    :provision => "",
  },
  {
    :type => "slave",
    :hostname => "slave1",
    :ip => "192.168.76.161",
    :box => "generic/alpine312",
    :ram => 512,
    :cpus => 2,
    :gui => false,
    :shared_folder_local => "./data/slave1",
    :shared_folder_remote => "/etc/hashicorp.d",
    :provision => ""
  },
  {
    :type => "slave",
    :hostname => "slave2",
    :ip => "192.168.76.162",
    :box => "generic/alpine312",
    :ram => 512,
    :cpus => 2,
    :gui => false,
    :shared_folder_local => "./data/slave2",
    :shared_folder_remote => "/etc/hashicorp.d",
    :provision => ""
  }
]

Vagrant.configure("2") do |config|
  # A common shared folder
  # XXX todo: all a per-machine shared folder too
  config.vm.synced_folder "./data/common", "/vagrant_data"

  config.vm.provision "shell", inline: <<-SHELL
    # echo "Global Provisioning goes here..."
    apk update && apk upgrade
    apk upgrade virtulbox-guest-additions --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community
    # https://wiki.alpinelinux.org/wiki/Docker
    apk add docker
    addgroup vagrant docker
    rc-update add docker boot
    service docker start
    # https://stackoverflow.com/questions/63080980/how-to-install-terraform-0-12-in-an-alpine-container-with-apk
    apk add consul vault nomad terraform --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community
    addgroup vagrant consul
    chown --recursive vagrant:vagrant /var/consul
  SHELL

  servers.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.box = machine[:box]
      node.vm.network "public_network", bridge: g_bridge, ip: machine[:ip], auto_config: true
      node.vm.provision "shell", inline: <<-SHELL
        echo "#{machine[:hostname]}" > /etc/hostname
        #{machine[:provision]}
      SHELL
      
      node.vm.synced_folder machine[:shared_folder_local], machine[:shared_folder_remote]

      node.vm.provider "virtualbox" do |vb|
        vb.gui = machine[:gui]
        vb.memory = machine[:ram]
        vb.cpus = machine[:cpus]
      end
    end
  end
end

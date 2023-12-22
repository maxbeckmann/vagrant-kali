# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "kalilinux/rolling"

  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessable to the vagrant box.
  config.vm.synced_folder ".", "/vagrant", disabled: true
  
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./data", "/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true
      # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end

  # Enable provisioning with a shell script
  config.vm.provision "shell" do |s|
    s.inline = <<-SHELL
    DEBIAN_FRONTEND=noninteractive apt-get update
    DEBIAN_FRONTEND=noninteractive apt-get upgrade -yq
      
      # setup Gnome Desktop
      DEBIAN_FRONTEND=noninteractive apt install -y kali-desktop-gnome crudini
      echo "/usr/sbin/gdm3" > /etc/X11/default-display-manager
      DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true dpkg-reconfigure gdm3
      echo set shared/default-x-display-manager gdm3 | debconf-communicate
      update-alternatives --set x-session-manager /usr/bin/gnome-session
    SHELL
    s.reboot = true
  end
  config.vm.provision "shell" do |s|
    s.inline = <<-SHELL
      # setup autologin
      crudini --set /etc/gdm3/daemon.conf daemon AutomaticLogin vagrant
      crudini --set /etc/gdm3/daemon.conf daemon AutomaticLoginEnable true
      sudo -H -u vagrant DISPLAY=:0 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus gsettings set org.gnome.desktop.lockdown disable-lock-screen true
      
      # set German keyboard layout
      sudo -H -u vagrant DISPLAY=:0 DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'de')]"
      
      # install Gnome System Monitor Widget
      DEBIAN_FRONTEND=noninteractive apt install -y gnome-system-monitor
      
      # enable NetworkManager
      apt install -y network-manager-openvpn-gnome openvpn-systemd-resolved
      
      # Prevent Debian interface management
      rm /etc/network/interfaces
    SHELL
    s.reboot = true
  end
end

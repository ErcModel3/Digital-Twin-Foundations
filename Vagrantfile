# https://discuss.hashicorp.com/t/vagrantfile-that-works-on-both-arm64-amd64/67121
# https://steve-jansen.github.io/blog/2014/03/14/configuring-vagrant-to-dynamically-match-guest-and-host-cpu-architectures/
# https://developer.hashicorp.com/vagrant/docs/vagrantfile/machine_settings

Vagrant.configure("2") do |config|
  host_arch = `uname -m`.strip

  # Select box and provider based on architecture
  if host_arch == "x86_64" || host_arch == "amd64"
    # x86_64 architecture - use libvirt provider with cloud-image Ubuntu box
    box_name = "cloud-image/ubuntu-24.04"
    provider_name = "libvirt"
    puts "Detected x86_64 architecture - using #{box_name} with #{provider_name} provider"
  elsif host_arch == "aarch64" || host_arch == "arm64"
    # ARM64 architecture - use VirtualBox provider with ARM64 box
    box_name = "net9/ubuntu-24.04-arm64"
    provider_name = "virtualbox"
    puts "Detected ARM64 architecture - using #{box_name} with #{provider_name} provider"
  else
    puts "Warning: Unknown architecture '#{host_arch}', defaulting to x86_64 settings"
    box_name = "cloud-image/ubuntu-24.04"
    provider_name = "libvirt"
  end

  # VM 1: mqtt1-u
  config.vm.define "mqtt1-u" do |rasta|
    rasta.vm.box = box_name
    rasta.vm.network "private_network", ip: "10.0.1.10"
    rasta.vm.provider provider_name do |v|
      v.memory = 4096
      v.cpus = 2
      if provider_name == "virtualbox"
        v.name = "mqtt1-u"
      end
    end
    rasta.vm.provision "ansible" do |ansible|
      ansible.playbook = "provision.yml"
    end
  end

  # VM 2: mqtt2-u
  config.vm.define "mqtt2-u" do |rasta|
    rasta.vm.box = box_name
    rasta.vm.network "private_network", ip: "10.0.1.11"
    rasta.vm.provider provider_name do |v|
      v.memory = 4096
      v.cpus = 2
      if provider_name == "virtualbox"
        v.name = "mqtt2-u"
      end
    end
    rasta.vm.provision "ansible" do |ansible|
      ansible.playbook = "provision.yml"
    end
  end

  if host_arch == "x86_64" || host_arch == "amd64"
    config.vm.define "attack1-k" do |kali|
      kali.vm.box = "kalilinux/rolling"
      kali.vm.network "private_network", ip: "10.0.1.20"
      
      kali.vm.provider "libvirt" do |v|
        v.memory = 4096
        v.cpus = 2

        # Enable GUI for libvirt/KVM -> DO NOT TOUCH THIS
        v.graphics_type = "spice"
        v.graphics_autoport = "yes"
        v.video_type = "qxl"
        v.sound_type = "ich6"
      end
      
      # Need to check if the GUI is actually there
      kali.vm.provision "shell", inline: <<-SHELL
        export DEBIAN_FRONTEND=noninteractive
        apt update && apt upgrade -y #To replace with a provisioning playbook

        # Ensure desktop environment is installed (usually comes with Kali)
        apt install -y kali-desktop-xfce

        # Enable graphical target
        systemctl set-default graphical.target

        # Install additional tools (and display manager)
        apt install -y firefox-esr
        systemctl enable lightdm
      SHELL
    end
  else
    puts "No kali vm on macos"
  end
end
Vagrant.configure("2") do |config|
    config.vm.define "mqtt1-u" do |rasta|
      rasta.vm.box = "net9/ubuntu-24.04-arm64"
      rasta.vm.network "private_network", ip: "10.0.1.10" 
      rasta.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
        v.name = "mqtt1-u" 
      end
    end

  config.vm.define "mqtt2-u" do |rasta|
    rasta.vm.box = "net9/ubuntu-24.04-arm64"
    rasta.vm.network "private_network", ip: "10.0.1.11"
    rasta.vm.provider "virtualbox" do |v|
      v.memory = 4096
      v.cpus = 2
      v.name = "mqtt2-u"
    end
  end
end
#Define the list of machines
slurm_cluster = {
    :header => {
        :hostname => "header",
        :ipaddress => "192.168.236.10"
    },
    :worker1 => {
        :hostname => "worker1",
        :ipaddress => "192.168.236.11"
    },
    :worker2 => {
        :hostname => "worker2",
        :ipaddress => "192.168.236.12"
    }
}

##Provisioning inline script
$script = <<SCRIPT
yum install -y portmap nfs-utils
yum install -y openmpi openmpi-devel
echo "192.168.236.10    header" >> /etc/hosts
echo "192.168.236.11    worker1" >> /etc/hosts
echo "192.168.236.12    worker2" >> /etc/hosts
service iptables stop
chkconfig --del iptables
mkdir /EDISON
SCRIPT
#
Vagrant.configure("2") do |global_config|
    slurm_cluster.each_pair do |name, options|
        global_config.vm.define name do |config|
            #VM configurations
            config.vm.box = "centos6m"
            config.vm.hostname = "#{name}"
            config.vm.network :private_network, ip: options[:ipaddress]

            #VM specifications
            config.vm.provider :virtualbox do |v|
                # http://vstone.eu/my-improved-vagrantfile/
                if "#{name}" == "header"
                    v.customize ["modifyvm", :id, "--memory", "1024"]
                    v.customize ["modifyvm", :id, "--cpus", "1"]
                end
                if "#{name}" == "worker1"
                    v.customize ["modifyvm", :id, "--memory", "512"]
                    v.customize ["modifyvm", :id, "--cpus", "2"]
                end
                if "#{name}" == "worker2"
                    v.customize ["modifyvm", :id, "--memory", "512"]
                    v.customize ["modifyvm", :id, "--cpus", "2"]
                end
            end

            #VM provisioning
            config.vm.provision :shell,
                :inline => $script
        end
    end
end

# Instalando Docker and compose
$script_docker_and_compose = <<-SCRIPT
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common gnupg-agent && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt update -y && \
sudo apt-cache policy docker-ce && \
sudo apt install docker-ce -y && \
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
sudo chmod +x /usr/local/bin/docker-compose
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "virtualizandoaju/for_ansible_awx"
  config.vm.box_version = "0.1"

  config.vm.define "ansible_awx" do |awx|
  awx.vm.network "forwarded_port", guest: 80, host: 8089
  awx.vm.network "public_network" , ip: "192.168.79.100"
  
  #Definindo virtualizado tipo 2 virtual box
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 6144
    vb.cpus = 4
    vb.name = "ubuntu_with_ansibleawx"
  end

  #Renomeando o nome do servidor
  awx.vm.provision "shell", inline: "hostnamectl set-hostname awx"

  #Instalando ansible e atualizando SO
  awx.vm.provision "shell", inline: "apt-get update && apt-get install -y ansible"

  #Variavel para receber blocos de comandos
  awx.vm.provision "shell", inline: $script_docker_and_compose

  #Execute o docker sem o 'sudo'.
  awx.vm.provision "shell", inline: "sudo usermod -aG docker vagrant"

  #Install docker-py python module, git, pwgen and vim
  awx.vm.provision "shell", inline: "sudo apt install -y python3-pip git pwgen vim unzip"

  #Install request which allows to send HTTP/1.1 requests
  awx.vm.provision "shell", inline: "sudo pip3 install requests==2.22.0"

  #Install pip3
  awx.vm.provision "shell", inline: "sudo pip3 install docker-compose==1.29.2"
  
  #Baixa e descompacta
  awx.vm.provision "shell", inline: "wget https://github.com/ansible/awx/archive/17.1.0.zip"

  #Descompacta arquivo zip
  awx.vm.provision "shell", inline: "sudo unzip 17.1.0.zip"
    
  #Copia arquivo inventory
  awx.vm.provision "shell", inline: "cp /vagrant/inventory /home/vagrant/ && \
  sudo cat /home/vagrant/inventory >> /home/vagrant/awx-17.1.0/installer/inventory"
  
  #Instala AWX em docker com cluster kubernetes
  awx.vm.provision "shell", inline: "cd /home/vagrant/awx-17.1.0/installer && \  
  ansible-playbook -i inventory install.yml -vvv"
  end
end



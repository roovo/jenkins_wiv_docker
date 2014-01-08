# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_NAME  = ENV.fetch("BOX_NAME", "ubuntu")
BOX_URI   = ENV.fetch("BOX_URI",  "http://files.vagrantup.com/precise64.box")

Vagrant.configure("2") do |config|
  config.vm.box     = BOX_NAME
  config.vm.box_url = BOX_URI

  config.vm.network "forwarded_port", guest: 8080, host: 8080

  provision_jenkins = [
    %{if [[ ! -z $(type -p docker) ]]; then},
      %{apt-get install -q -y openjdk-7-jre-headless git-core},
      %{useradd jenkins -s /bin/bash -m -G docker && passwd -l jenkins},
      %{echo 'eval \"$(/usr/local/src/dockerize/bin/dockerize init -)\"' >> /home/jenkins/.profile},
      %{cd /home/jenkins},
      %{sudo -Hu jenkins git config --global user.name Jenkins},
      %{sudo -Hu jenkins git config --global user.email jenkins@$(hostname)},
      %{[[ ! -f "/home/jenkins/jenkins.war" ]] && wget -q -O - http://mirrors.jenkins-ci.org/war-stable/latest/jenkins.war > /home/jenkins/jenkins.war},
      %{cat << EOF > /etc/init/jenkins.conf
description "Jenkins Server"

start on filesystem
stop on runlevel [!2345]

respawn limit 10 5

script
  sudo -Hu jenkins java -jar /home/jenkins/jenkins.war -Djava.awt.headless=true --httpPort=8080
end script
EOF},
      %{start jenkins},
      %{echo -e "\nVM ready - if you want the vagrant and/or jenkins users to have to roovo setup:\n"},
      %{echo    "  * add the user to the docker group \\`sudo usermod -a -G docker <user>\\`"},
      %{echo    "  * set up dotfiles \\`bash <(curl -sSL https://raw.github.com/roovo/dotfiles/master/scripts/bootstrap)\\`"},
      %{echo -e "  * and log out and in again to pick up the new permissions and dotfile stuff\n"},
    %{fi},
  ]

  provisioning_script = ["export DEBIAN_FRONTEND=noninteractive"]
  provisioning_script += provision_jenkins

  config.vm.provision :shell, :path   => 'https://raw.github.com/roovo/bearded-octo-batman/master/provision_dev_essentials'
  config.vm.provision :shell, :path   => 'https://raw.github.com/roovo/bearded-octo-batman/master/provision_dockerize'
  config.vm.provision :shell, :inline => provisioning_script.join("\n")
end

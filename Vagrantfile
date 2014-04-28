# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"



Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "ris"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 3000, host: 3000

  # RIS provisioning
  env_pass = ENV['rispass']
  
  # two scripts will be executed 
  # this root_script will be executed as root
  $root_script = <<RSCRIPT

  echo 'installing libcurl development libraries'
  yum -y install libcurl-devel.x86_64
  echo 'done installing libcurl development libraries'

  echo 'installing unixODBC development libraries'
  yum -y install unixODBC-devel.x86_64
  echo 'done installing unixODBC development libraries'

  echo 'installing sqlite development libraries'
  yum -y install sqlite-devel.x86_64
  echo 'done installing sqlite development libraries'

  echo 'installing freetds development libraries'
  yum -y install freetds-devel.x86_64
  echo 'done installing freetds development libraries'

  echo 'installing firefox for testing'
  yum -y install firefox
  echo 'done installing firefox'

  echo 'installing virtual frame buffer for testing'
  yum -y install xorg-x11-server-Xvfb
  echo 'done installing virtual frame buffer'

RSCRIPT

  # this script will be executed as the vagrant user
  $script = <<SCRIPT
  echo "setting up default rails based on ruby-version and gemset"
  if test -f ~/.rvm/scripts/rvm; then
    [ "$(type -t rvm)" = "function" ] || source ~/.rvm/scripts/rvm
  fi
  cd /vagrant
  ruby_version=$(<.ruby-version)
  ruby_gemset=$(<.ruby-gemset)
  echo "calling rvm use"
  rvm use --install --create $ruby_version@global --default
  echo "done with rvm setup"

  echo "Setting up risuiowa password to be #{env_pass}"
  echo "export rispass='#{env_pass}'" >> ~/.bash_profile
  echo "export rispass='#{env_pass}'" >> ~/.profile

  echo "bundle installing, cause, why not?"
  source ~/.profile
  bundle install
  echo "all done, check bundle install output"

SCRIPT
  
  #execute the root script
  config.vm.provision :shell, inline: $root_script

  #execute the other script as the vagrant user (privileged false)
  config.vm.provision :shell, inline: $script, :privileged => false

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
  end

  # end of RIS provisioning

end

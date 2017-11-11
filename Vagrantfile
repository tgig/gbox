Vagrant.configure("2") do |config|

  # Box declaration
  config.vm.box = "ubuntu/trusty64"
  #config.vm.box = "hashicorp/precise64"

  # Configurations
  config.vm.define "gbox"  # define vagrant machine
  config.vm.hostname = "gbox"
  config.vm.provider "virtualbox" do |vb|
    vb.name = "gbox"  # set virtual box name
    vb.memory = 2048
    vb.cpus = 1  # 1 CPU optimizes vm performance

    # enable audio drivers on VM settings
    vb.customize [
      "modifyvm", :id,
      "--audio", "coreaudio",
      "--audiocontroller", "hda"
    ]
  end

  config.ssh.forward_x11 = true
  config.ssh.forward_agent = true  
  
  config.vm.network "forwarded_port", guest: 5000, host: 5000, auto_correct: true
  config.vm.network "forwarded_port", guest: 3306, host: 3306, auto_correct: true
  #config.vm.network "forwarded_port", guest: 5432, host: 5432, auto_correct: true
  # for react server
  config.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
  config.vm.network "forwarded_port", guest: 8888, host: 8888, auto_correct: true

  config.vm.synced_folder "/Users/travis/Documents", "/code"
  #config.vm.provision "file", source: "~/.aws", destination: "~/"
  config.vm.synced_folder "/Users/travis/.aws", "/home/vagrant/.aws"

  config.vm.provision "shell", inline: <<-SHELL

    echo -e ""
    echo -e "------------------------- Beginning setup --------------------------"
    echo -e ""

    # Define script path variables
    HOME_PATH="/home/vagrant"
    PYTHON_BASEPATH="/usr/local/lib/python2.7.13"
    PIP_PATH="$PYTHON_BASEPATH/bin/pip"
    PYTHON_PATH="$PYTHON_BASEPATH/bin/python2.7"

    # Suppress non-fatal warning (dpkg-preconfigure: unable to re-open std-in)
    export DEBIAN_FRONTEND=noninteractive

    echo -e "----------------------------- Update -------------------------------"

    apt-get -q update

    echo -e "----------- Satisfy build dependencies for Python 2.7.13 -----------"

    apt-get -q -y install build-essential
    apt-get -q -y build-dep python2.7

    echo -e "-------- Downloading and compiling Python 2.7.13 from source -------"

    wget -q -O $HOME_PATH/Python-2.7.13.tgz https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz
    tar xfz $HOME_PATH/Python-2.7.13.tgz -C $HOME_PATH/
    cd $HOME_PATH/Python-2.7.13/
    ./configure --prefix $PYTHON_BASEPATH --enable-ipv6 --quiet
    mkdir $HOME_PATH/setup
    make &>> $HOME_PATH/setup/python_build.log

    echo -e "---------------- Installing optimized Python 2.7.13 ----------------"

    make altinstall &>> $HOME_PATH/setup/python_build.log
    cd  # move out of directory to be deleted
    rm -rf $HOME_PATH/Python-2.7.13*

    # Add path to Python 2.7.13 binary in user's bashrc file
    echo -e "# Adding path to Python 2.7.13 binary" >> $HOME_PATH/.bashrc
    echo "PATH=$PATH:/usr/local/lib/python2.7.13/bin" >> $HOME_PATH/.bashrc
    echo "export PATH" >> $HOME_PATH/.bashrc

    echo -e "----------- Installing pip, setuptools from Python 2.7.13 ----------"

    $PYTHON_PATH -m ensurepip
    $PIP_PATH install --upgrade pip setuptools

    $PIP_PATH install awscli
    $PIP_PATH install httpie
    $PIP_PATH install -U pyopenssl==0.13.1 pyasn1 ndg-httpsclient
    $PIP_PATH install virtualenv
    $PIP_PATH install virtualenvwrapper

    echo -e "------------- Setup virtualenv and virtualenvwrapper ---------------"

    mkdir -m775 $HOME_PATH/.virtualenvs  # default virtualenv directory
    chown vagrant:vagrant $HOME_PATH/.virtualenvs/  # script runs as root, change owner to vagrant
    echo -e "# Set Python for Virtualenv" >> $HOME_PATH/.bashrc
    echo "export VIRTUALENV_PYTHON=$PYTHON_PATH" >> $HOME_PATH/.bashrc
    echo -e "# Set variables for Virtualenvwrapper" >> $HOME_PATH/.bashrc
    echo "export WORKON_HOME=$HOME_PATH/.virtualenvs" >> $HOME_PATH/.bashrc
    echo "export VIRTUALENVWRAPPER_PYTHON=$PYTHON_PATH" >> $HOME_PATH/.bashrc
    echo "export VIRTUALENVWRAPPER_VIRTUALENV=$PYTHON_BASEPATH/bin/virtualenv" >> $HOME_PATH/.bashrc
    echo "export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--system-site-packages'" >> $HOME_PATH/.bashrc
    echo "source $PYTHON_BASEPATH/bin/virtualenvwrapper.sh" >> $HOME_PATH/.bashrc
    
    echo -e "------------------------ Installing NodeJS -------------------------"

    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    apt-get -q -y install nodejs
    apt-get -q -y install build-essential  # install native add-ons from npm

    echo -e "----------------- Install global Linux utilities -------------------"

    # this should be installed via python2.7.13 above
    #apt-get install -y python-pip 
    
    apt-get install -y python-dev libssl-dev libffi-dev
    apt-get install -y curl
    apt-get install -y vim
    apt-get install -y npm
    apt-get install -y build-essential
    apt-get install -y zip
    apt-get install -y python-psycopg2
    apt-get install -y ntp
    service ntp restart && date -u

    #echo -e "------------------- Install Sound utilities -----------------------"

    #wget https://code.launchpad.net/~ubuntu-audio-dev/+archive/ubuntu/alsa-daily/+files/oem-audio-hda-daily-dkms_0.201708070416~ubuntu14.04.1_all.deb
    #sudo dpkg -i oem-audio-hda-daily-dkms_0.201708070416~ubuntu14.04.1_all.deb
    #rm -f oem-audio-hda-daily-dkms_0.201708070416~ubuntu14.04.1_all.deb

    #apt-get -y install dkms
    
    #apt-get install -y pulseaudio
    #apt-get install -y alsa-base

    # put vagrant user in audio group
    #usermod -a -G audio vagrant 
    # associate the audio card
    #modprobe -v snd-hda-intel 

    #echo "###################################################################"
    #echo "DON'T FORGET TO RUN alsamixer TO UNMUTE & RAISE VOLUME FOR SPEAKERS"
    #echo "MAY NEED TO REBOOT FOR DRIVERS TO KICK IN"
    #echo "###################################################################"

    
    echo -e "------------------- Install Google Cloud? -----------------------"
    echo -e "Instructions here: https://cloud.google.com/sdk/docs/quickstart-linux"
    
  SHELL

end

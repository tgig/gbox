Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
  config.vm.define :gbox do |t|
  end

  config.vm.network "forwarded_port", guest: 5000, host: 5000, auto_correct: true
  config.vm.network "forwarded_port", guest: 3306, host: 3306, auto_correct: true
  #config.vm.network "forwarded_port", guest: 5432, host: 5432, auto_correct: true
  # for react server
  config.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
  config.vm.network "forwarded_port", guest: 8888, host: 8888, auto_correct: true

  config.vm.synced_folder "/Users/travisgiggy/Documents/Travis/code", "/code"

  config.vm.provision "shell", inline: <<-SHELL

    apt-get update

    #install latest version of python2.7
    apt-get install -y software-properties-common python-software-properties
    add-apt-repository ppa:fkrull/deadsnakes-python2.7
    apt-get update
    apt-get install -y python2.7    

    apt-get install -y python-pip
    apt-get install -y python-dev libssl-dev libffi-dev
    apt-get install -y curl
    apt-get install -y vim
    apt-get install -y npm
    apt-get install -y build-essential
    apt-get install -y zip
    apt-get install -y python-psycopg2
    apt-get install -y ntp

    # for the data science thing:
    # apt-get install -y python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    # postgres
    #apt-get install -y postgresql postgresql-contrib
    
    pip install awscli
    #pip install chalice
    pip install httpie
    pip install -U pyopenssl==0.13.1 pyasn1 ndg-httpsclient
    pip install Flask
    pip install flask-login
    pip install gunicorn
    pip install boto3
    pip install virtualenv
    pip install jupyter

    # data science
    # pip install -U scikit-learn

   #install npm
    apt-get install -y nodejs
    apt-get install -y npm
    npm install npm@latest -g

    # react create app
    # npm install -g create-react-app

    # in case I continue to develop w/ serverless
    # npm install serverless -g
    # npm install webpack -g

    # in case I continue using React w/ Babel
    # yarn installation seems to remove npm, so this is last, and should maybe be removed in future
    # curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    # echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
    # apt-get update
    # apt-get install -y yarn
    
  SHELL

  config.vm.provision "file", source: "~/.aws", destination: "~/"

end

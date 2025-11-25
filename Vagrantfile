Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/noble64"

  config.vm.provider "virtualbox" do |v|
    v.memory = 8192
    v.cpus = 4
  end

  config.vm.hostname = "yourapp.local"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "private_network", ip: "192.168.56.10"

  config.vm.synced_folder ".", "/var/www/html",
    owner: "www-data",
    group: "www-data",
    mount_options: ['dmode=755', 'fmode=644']

  config.vm.provision "shell", inline: <<-SHELL
    set -e
    export DEBIAN_FRONTEND=noninteractive

    sudo apt-get update
    sudo apt-get -y full-upgrade
    sudo apt-get install -y \
      apache2 mysql-server \
      php libapache2-mod-php php-cli php-mysql php-curl php-imagick php-mbstring \
      php-xml php-zip php-bcmath php-intl php-gd unzip curl npm

    sudo systemctl enable --now apache2 mysql

    sudo mysql -e "CREATE USER IF NOT EXISTS 'yourapp'@'localhost' IDENTIFIED WITH mysql_native_password BY 'admin'";
    sudo mysql -e "GRANT ALL PRIVILEGES ON *.* TO 'yourapp'@'localhost' WITH GRANT OPTION";
    sudo mysql -e "FLUSH PRIVILEGES";

    curl -fsSL -o wp-cli.phar https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
    chmod +x wp-cli.phar
    sudo install -m 0755 wp-cli.phar /usr/local/bin/wp

    sudo a2enmod rewrite headers

    cd /var/www/html
    sudo wp core download --allow-root
    sudo wp config create --dbname=yourapp --dbuser=yourapp --dbpass=admin --allow-root
    sudo wp db create --allow-root
    sudo cp 000-default.conf /etc/apache2/sites-available/000-default.conf
    sudo rm -f index.html
    sudo systemctl reload apache2
  SHELL
end

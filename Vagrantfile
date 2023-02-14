Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.provider "virtualbox" do |v|
    v.memory = 8192
    v.cpus = 4
  end
  config.vm.hostname = "yourapp.local"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "private_network", ip: "192.168.56.10"
  config.vm.synced_folder ".", "/var/www/html",
    :owner => 'www-data',
    :group => 'www-data',
    :mount_options => ['dmode=755', 'fmode=644']
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update
    sudo apt upgrade -y
    sudo apt install apache2 -y
    sudo apt install mysql-server -y
    sudo mysql -e "CREATE USER 'yourapp'@'localhost' IDENTIFIED WITH mysql_native_password BY 'admin'";
    sudo mysql -e "GRANT ALL PRIVILEGES ON *.* TO 'yourapp'@'localhost' WITH GRANT OPTION";
    sudo mysql -e "FLUSH PRIVILEGES";
    sudo apt install php libapache2-mod-php php-mysql php-curl php-imagick php-mbstring php-dom php-zip php-bcmath php-intl npm php-cli unzip -y
    curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
    chmod +x wp-cli.phar
    sudo mv wp-cli.phar /usr/local/bin/wp
    sudo a2enmod rewrite
    cd /var/www/html
    sudo wp core download --allow-root
    sudo wp config create --dbname=yourapp --dbuser=yourapp --dbpass=admin --allow-root
    sudo wp db create --allow-root
    sudo mv 000-default.conf /etc/apache2/sites-available/000-default.conf
    rm index.html
    sudo systemctl restart apache2
  SHELL
end

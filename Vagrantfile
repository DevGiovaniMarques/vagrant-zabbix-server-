Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/bionic64"
  config.vm.define:app do |app_config|
    app_config.vm.network "public_network", ip: "192.168.0.77"
    
    app_config.vm.provision "shell", inline: <<-SHELL
    wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu18.04_all.deb
    dpkg -i zabbix-release_6.4-1+ubuntu18.04_all.deb;
    apt-get update;
    apt-get install zabbix-server-mysql zabbix-frontend-php php-bcmath php-mbstring php-xml -y;
    sed 's@;date.timezone =@date.timezone=Europe/Warsaw@' -i /etc/php/7.0/apache2/php.ini;
    echo -e "[mysqld]\ndefault-storage-engine = innodb" | sudo tee /etc/mysql/conf.d/mysqld.conf;
    service mysql restart;
    mysql -e "CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'zabbix';";
    mysql -e "create database zabbix character set utf8 collate utf8_bin;"
    mysql -e "grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';";
    zcat /usr/share/doc/zabbix-server-mysql/create.sql.gz | mysql -uzabbix -p"zabbix" zabbix;
    sed 's/# DBPassword=/DBPassword=zabbix/g' /etc/zabbix/zabbix_server.conf -i;
    service zabbix-server restart;
    service apache2 restart;
  SHELL
    
    app_config.vm.provider:virtualbox do |v|
      v.name = "VM Vagrant"
    end
  end
end

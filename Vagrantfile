Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64"

  config.vm.network "private_network", ip: "10.10.10.11"

 # config.vm.synced_folder "./webroot", "/webroot"

  config.vm.provider "virtualbox" do |vb|

  #   vb.gui = true

      vb.memory = "1024"
  end

  config.vbguest.auto_update = false

  config.vm.post_up_message = "Modsecurity test environment. To get started visit http://10.10.10.11\n
  DVWA: http://10.10.10.11/DVWA/setup.php, setup DB and login admin:password\n Mutillidae: http://10.10.10.11/mutillidae
  ModSecurity Logs: sudo tail -f /var/log/apache2/modsec_audit.log\n"

  if Vagrant.has_plugin?("vagrant-cachier")
    # Configure cached packages to be shared between instances of the same base box.
    # More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
    config.cache.scope = :box

    # OPTIONAL: If you are using VirtualBox, you might want to use that to enable
    # NFS for shared folders. This is also very useful for vagrant-libvirt if you
    # want bi-directional sync
    config.cache.synced_folder_opts = {
      type: :nfs,
      # The nolock option can be useful for an NFSv3 client that wants to avoid the
      # NLM sideband protocol. Without this option, apt-get might hang if it tries
      # to lock files needed for /var/cache/* operations. All of this can be avoided
      # by using NFSv4 everywhere. Please note that the tcp option is not the default.
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
    }
    # For more information please check http://docs.vagrantup.com/v2/synced-folders/basic_usage.html
  end

  # Spin up Apache2 with modsecurity
  # and enable sample rules
  config.vm.provision "shell", inline: <<-SHELL
    sudo sed -i 's/AcceptEnv/\#AcceptEnv/g' /etc/ssh/sshd_config
    sudo /etc/init.d/ssh reload
    sudo apt-get install -y apache2
    sudo apt-get install -y php5-curl
    sudo apt-get install -y libapache2-modsecurity
    cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
    sed -i 's/DetectionOnly/On/' /etc/modsecurity/modsecurity.conf
    echo 'Include "/usr/share/modsecurity-crs/*.conf"' >> /etc/modsecurity/modsecurity.conf
    echo 'Include "/usr/share/modsecurity-crs/activated_rules/*.conf"' >> /etc/modsecurity/modsecurity.conf
    ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_41_sql_injection_attacks.conf /usr/share/modsecurity-crs/activated_rules/
    ln -s /usr/share/modsecurity-crs/base_rules/modsecurity_crs_41_xss_attacks.conf /usr/share/modsecurity-crs/activated_rules/
    sudo a2enmod security2
    sudo /etc/init.d/apache2 force-reload

    # DVWA
    # setting mysql password: password
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password password'
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password password'

    sudo apt-get install -y php5-gd
    sudo apt-get install -y apache2 mysql-server php5 unzip php5-mysql php-pear*
    sudo apt-get install -y git
    sudo git clone https://github.com/RandomStorm/DVWA.git /var/www/html/DVWA
    sudo sed -i 's/p@ssw0rd/password/g' /var/www/html/DVWA/config/config.inc.php
    sudo sed -i 's/allow_url_include = Off/allow_url_include = On/g' /etc/php5/apache2/php.ini
    sudo chmod -R 777 /var/www/html/DVWA/hackable/uploads/
    sudo /etc/init.d/apache2 restart

    # XVWA
    #sudo git clone https://github.com/s4n7h0/xvwa.git /var/www/html/xvwa
    #sudo sed -i 's/$pass.*/$pass = \x27password\x27;/g' /var/www/html/xvwa/config.php

    # Mutillidae
    sudo git clone git://git.code.sf.net/p/mutillidae/git /var/www/html/mutillidae
    sudo sed -i 's/$mMySQLDatabasePassword = \"\"/$mMySQLDatabasePassword = \"password\"/g' /var/www/html/mutillidae/classes/MySQLHandler.php

  SHELL
end

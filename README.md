Quick Start
-----

* Add an approriate vagrant box

```      
      vagrant box add wordpress-precise32 http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-i386-vagrant-disk1.box
```

* clone this repo as ~/YourVagrantDir/provisioning

```
      git clone https://github.com/Aricg/Ansible-Vagrant-AWS-Wordpress.git provisioning/
```

* Edit your vagrant file for openbox

```
Vagrant.configure("2") do |config|
  config.vm.box = "wordpress-precise32"

  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :private_network, ip: "192.168.100.10"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    ansible.inventory_path = "provisioning/hosts"
  end
end
```

* Launch! 

```
      vagrant up
```
      
* if you make any changes to ansible's configs and want to run ansible again
    
```
      vagrant provision

```

Important Steps that you just missed
------------------------------------

* edit ./provisioning/vars/settings.yml 
* edit ./provisioning/hosts to reflect your network settings in your Vagrantfile 
* replace aric with your desired username in playbook.yml
* add your public key to ./provisioning/files/$yourname.pub

Optional: after the machine is provisioned
------------------------------------------
* manually put the the old dump of your wordpress database in place (if applicable)
* manually untar your wp-uploads into /srv/www/wp-uploads/yoursite.name/

What exactly gets provisioned?
------------------------------
* Unattended security updates
* Your user, with sudo and public key access
* My bashrc (ha!)
* Screenfetch https://github.com/KittyKatt/screenFetch (call it in .profile to enable)
* Vim with vundle, my favorite modules and the solorized theme
* My finger script (finger foo@aricgardner.com for an example) 
* Varish http accelerator
* nginx
* php-fpm
* mysql
* git
* screen
* subversion
* htop
* curl
* a cron.daily that blocks the tor exit nodes of the day
* wordpress 3.7.1
  * Lockdown WP
  * Disquis
  * RSS Includes Pages
  * Syntax Highlighter Evolved
  * Embed GitHub Gist


Part2 Pushing your vagrant image to ec2
---------------------------------------- 

* Install vagrant aws plugin

```
      vagrant plugin install vagrant-aws
      vagrant plugin list
```

* Add the Dummy box

```
      vagrant box add aws-precise-32 https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box
```

* Get a suitable ami-id

      [[http://cloud-images.ubuntu.com/locator/ec2/]]

* Edit your Vagrant file

```
                Vagrant.configure("2") do |config|
                  config.vm.box = "aws-precise-32"
                  config.vm.hostname = "aricgardner.com"
                  config.vm.boot_timeout = 120
                  config.vm.provider :aws do |aws, override|
                    aws.access_key_id = "YOURKEY"
                    aws.secret_access_key = "YOUR_SECRET_KEY"
                    override.ssh.username = "ubuntu"
                    override.ssh.private_key_path = "/Users/aric/.ssh/id_rsa"
                    aws.keypair_name = "fookeypair"
                    aws.region = "us-east-1"
                    aws.ami = "ami-c5a98cac"
                    aws.instance_type = "t1.micro"
                    aws.tags = {
                      'Name' => 'Personal Server',
                               }
                end

          config.vm.provision "ansible" do |ansible|
            ansible.playbook = "provisioning/playbook.yml"
            ansible.inventory_path = "provisioning/hosts"
          end

        end
```


* Launch your ami with vagrant  

```
        vagrant up --provider=aws
        Bringing machine 'default' up with 'aws' provider...
        [default] Launching an instance with the following settings...
        [default]  -- Type: t1.micro
        [default]  -- AMI: ami-c5a98cac
        [default]  -- Region: us-east-1
        [default]  -- Keypair: fookeypair
        [default]  -- Block Device Mapping: []
        [default]  -- Terminate On Shutdown: false
        [default] Waiting for instance to become "ready"...
        [default] Waiting for SSH to become available...
        [default] Machine is booted and ready for use!
        [default] Rsyncing folder: /Users/aric/vagrant_getting_started/ => /vagrant
```

Provision launched ec2 machine with ansible
-------------------------------------------

Grab the hostname add it to provisioning/hosts and then run vagrant provision

```
      vim provisioning/hosts
      vagrant provision
```


Thats all folks

License
-------
First of all there are certainly somethings you dont want, like the finger script I wrote and the cronjob that blocks tor exit nodes. So please modify this before you do anything serious with it
That said: The usage of the works is permitted provided that this instrument is retained with the works, so that any entity that uses the works is notified of this instrument.
DISCLAIMER: THE WORKS ARE WITHOUT WARRANTY.


# vagrant-yml
This Vagrantfile works with an external data file (a YAML file, named vagrant.yml) to create multiple Vagrant boxes easily. The vagrant.yml file contains all the specifics and can be easily edited to change the number and type of boxes to create.

You can view examples in the vagrant.yml.example file

You can override your Vagrant config with a vagrant.local.yml file.

## Requirements

* [Vagrant](https://www.vagrantup.com/downloads.html) (version >= 1.6)
* [VBGuest](https://github.com/dotless-de/vagrant-vbguest)
* [Hostmanager](https://github.com/smdahlen/vagrant-hostmanager)

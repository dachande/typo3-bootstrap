# encoding: utf-8
# vim: ft=ruby expandtab shiftwidth=2 tabstop=2

require 'yaml'

Vagrant.require_version '>= 2.2.9'

Vagrant.configure(2) do |config|

  _conf = YAML.load(
    File.open(
      File.join(File.dirname(__FILE__), 'configuration.yml'),
      File::RDONLY
    ).read
  )

  # forcing config variables
  _conf["vagrant_dir"] = "/vagrant"

  config.vm.define _conf['hostname'] do |v|
  end

  config.vm.box = ENV['box'] || _conf['box']
  #config.vm.box_version = "<= 20161209"
  config.ssh.forward_agent = true

  config.vm.box_check_update = true

  config.vm.hostname = _conf['hostname']
  config.vm.network :private_network, ip: _conf['ip']

  config.vm.synced_folder _conf['synced_folder'],
      _conf['document_root'], :create => "true", :owner => _conf['www_user'], :group => _conf['www_group']

  if Vagrant.has_plugin?('vagrant-hostmanager')
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
  end

  if Vagrant.has_plugin?('vagrant-vbguest')
    config.vbguest.auto_update = _conf['vbguest_auto_update']
  end

  if File.exists?(File.join(File.dirname(__FILE__), 'provision/provision-pre.sh')) then
    config.vm.provision :shell do |shell|
      shell.path = File.join( File.dirname(__FILE__), 'provision/provision-pre.sh' )
      shell.env = {'t3bs_typo3_version' => _conf['typo3_version']}
    end
  end

  config.vm.provider :virtualbox do |vb|
    vb.linked_clone = _conf['linked_clone']
    vb.name = _conf['hostname']
    vb.memory = _conf['memory'].to_i
    vb.cpus = _conf['cpus'].to_i
    if 1 < _conf['cpus'].to_i
      vb.customize ['modifyvm', :id, '--ioapic', 'on']
    end
    vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    vb.customize ['modifyvm', :id, '--uart1', '0x3F8', '4']
    vb.customize ['modifyvm', :id, '--uartmode1', 'file', File::NULL]
  end

  config.vm.provision "ansible_local" do |ansible|
    ansible.extra_vars = {
      t3bs: _conf,
      ansible_python_interpreter: "/usr/bin/python3"
    }
    ansible.install_mode = "pip"
    ansible.playbook = "provision/playbook.yml"
    ansible.compatibility_mode = "2.0"
  end

  if File.exists?(File.join(File.dirname(__FILE__), 'provision/provision-post.sh')) then
    config.vm.provision :shell do |shell|
      shell.path = File.join( File.dirname(__FILE__), 'provision/provision-post.sh' )
      shell.env = {
        't3bs_typo3_version' => _conf['typo3_version'],
        't3bs_hostname' => _conf['hostname'],
        't3bs_ip' => _conf['ip'],
        't3bs_use_tls' => _conf['use_tls'],
        't3bs_typo3_admin_user' => _conf['typo3_admin_user'],
        't3bs_typo3_admin_pass' => _conf['typo3_admin_pass'],
        't3bs_mailhog_auth_user' => _conf['mailhog_auth_user'],
        't3bs_mailhog_auth_pass' => _conf['mailhog_auth_pass']
      }
    end
  end
end

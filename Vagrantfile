# vi: ft=ruby

VAGRANTFILE_API_VERSION = '2'

hosts = [
  { maintainer: 'bento', distro: 'centos-7.6', ip: '192.168.56.7' },
  { maintainer: 'bento', distro: 'centos-8', ip: '192.168.56.8' },
  { maintainer: 'bento', distro: 'fedora-32', ip: '192.168.56.32' }
]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  hosts.each do |host|
    distro_name = host[:distro].delete('.-')
    host_name = distro_name

    config.vm.define host_name do |node|
      node.vm.box = host[:maintainer] + '/' + host[:distro]
      node.vm.hostname = host_name
      node.vm.network :private_network, ip: host[:ip]
      node.vm.provision 'ansible' do |ansible|
        ansible.compatibility_mode = '2.0'
        ansible.host_vars = {
          'fedora30' => { ansible_python_interpreter: '/usr/bin/python3' }
        }
        ansible.playbook = 'test.yml'
      end
    end
  end
end

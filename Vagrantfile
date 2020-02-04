Vagrant.configure("2") do |config|

  config.vm.define "fancybox" do |fancybox|
    fancybox.vm.box = "StefanScherer/windows_2016"
    fancybox.windows.halt_timeout = 20
    fancybox.vm.boot_timeout = 600
    fancybox.winrm.username = "vagrant"
    fancybox.winrm.password = "vagrant"
    fancybox.vm.guest = :windows
    fancybox.vm.communicator = "winrm"

    fancybox.vm.hostname = "fancybox"
    fancybox.windows.set_work_network = true

    fancybox.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true, host_ip: "127.0.0.1"
    fancybox.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true, host_ip: "127.0.0.1"
    fancybox.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true, host_ip: "127.0.0.1"

    #Sync folders
    #fancybox.vm.synced_folder "../packages", "/packages"
    #fancybox.vm.synced_folder "../license", "/license"

    fancybox.vm.provider :virtualbox do |v, override|
      override.vm.network :private_network, ip: "10.10.13.14"
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.gui = false
      v.customize ["modifyvm", :id, "--vram", 32]
      v.customize ["modifyvm", :id, "--memory", "1024"]
      v.customize ["modifyvm", :id, "--audio", "none"]
      v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      v.customize ["modifyvm", :id, "--draganddrop", "hosttoguest"]
      v.customize ["modifyvm", :id, "--usb", "off"]
      # linked clones for speed and size
      v.linked_clone = true if Vagrant::VERSION >= '1.8.0'
    end

    # privileged:false - https://github.com/hashicorp/vagrant/issues/9138
    # Auto Login issue corrected, as per discussion here: https://twitter.com/stefscherer/status/1011120268222304256
    fancybox.vm.provision "shell", privileged: false, inline: <<-SHELL
      Set-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name AutoAdminLogon -Value 1
      Set-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name DefaultUserName -Value "vagrant"
      Set-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name DefaultPassword -Value "vagrant"
      Remove-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name AutoAdminLogonCount -Confirm -ErrorAction SilentlyContinue
    SHELL
    fancybox.vm.provision "reload"
    fancybox.vm.provision :shell, :path => "shell/PrepareWindows.ps1", privileged: false
    fancybox.vm.provision :shell, :path => "shell/InstallChocolatey.ps1", privileged: false
    fancybox.vm.provision :shell, :path => "shell/NotifyGuiAppsOfEnvironmentChanges.ps1", privileged: false
  end

  config.vm.define "secondhost" do |secondhost|
    secondhost.vm.box = "StefanScherer/windows_2016"
    secondhost.windows.halt_timeout = 20
    secondhost.vm.boot_timeout = 600
    secondhost.winrm.username = "vagrant"
    secondhost.winrm.password = "vagrant"
    secondhost.vm.guest = :windows
    secondhost.vm.communicator = "winrm"

    secondhost.vm.hostname = "secondhost"
    secondhost.windows.set_work_network = true

    secondhost.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true, host_ip: "127.0.0.1"
    secondhost.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true, host_ip: "127.0.0.1"
    secondhost.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true, host_ip: "127.0.0.1"
    secondhost.vm.network :forwarded_port, guest: 80, host: 8090, id: 'web', auto_correct: true, host_ip: "127.0.0.1"

    #Sync folders
    #secondhost.vm.synced_folder "../packages", "/packages"
    #secondhost.vm.synced_folder "../license", "/license"

    secondhost.vm.provider :virtualbox do |v, override|
      override.vm.network :private_network, ip: "10.10.13.13"
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      # linked clones for speed and size
      v.linked_clone = true if Vagrant::VERSION >= '1.8.0'
      v.gui = false
      v.customize ["modifyvm", :id, "--memory", "3072"]
      v.customize ["modifyvm", :id, "--audio", "none"]
      v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      v.customize ["modifyvm", :id, "--draganddrop", "hosttoguest"]
      v.customize ["modifyvm", :id, "--usb", "off"]
    end

    # privileged:false - https://github.com/hashicorp/vagrant/issues/9138
    # Auto Login issue corrected, as per discussion here: https://twitter.com/stefscherer/status/1011120268222304256
    secondhost.vm.provision "shell", privileged: false, inline: <<-SHELL
      Set-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name AutoAdminLogon -Value 1
      Set-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name DefaultUserName -Value "vagrant"
      Set-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name DefaultPassword -Value "vagrant"
      Remove-ItemProperty "HKLM:\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon" -Name AutoAdminLogonCount -Confirm -ErrorAction SilentlyContinue
    SHELL
    secondhost.vm.provision "reload"
    secondhost.vm.provision :shell, :path => "shell/PrepareWindows.ps1", privileged: false
    secondhost.vm.provision :shell, :path => "shell/InstallChocolatey.ps1", privileged: false
    secondhost.vm.provision :shell, :path => "shell/NotifyGuiAppsOfEnvironmentChanges.ps1", privileged: false
  end
end
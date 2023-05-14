# Miniprojekti


Projektin tarktoituksena on Salt-työkalua hyöduntäen asentaa kolmen virtuaalikoneen verkkoon seuraavat hyödylliset ohjelmat:

 * Micro - Tekstieditori (https://github.com/zyedidia/micro)
 * Thunderbird - Mozillan avoimen lähdekoodin sähköpostiohjelma (https://www.thunderbird.net/fi/)
 * Timeshift - Järjestelmän palautustyökalu (https://github.com/teejee2008/timeshift)
 * Vim - Tekstieditori (https://github.com/vim/vim)

 
### Laitteisto
 
* Käyttöjärjestelmä	Microsoft Windows 10 Enterprise LTSC 64 bit
* Prosessori i5-6600
* RAM 16 GB





## Debian 11:sta asennus Vagrantilla (23:00)

Asennetaan Debian 11 Vagrantin avulla.

Aloitin lataamalla Vagrantin AMD64 version Windowsille sivulta: (https://developer.hashicorp.com/vagrant/downloads).

![image](https://user-images.githubusercontent.com/106889187/229376447-58057622-04f6-46cd-b913-72ad2a85a37a.png)


Asennusohjelman suoritin oletusasetuksilla. Asennuksen jälkeen käynnistin tietokoneen uudestaan.

Seuraavaksi seurasin Valkamon 2022 vuonna tehtyä raporttia: Create Virtual Machines with Vagrant (https://tuomasvalkamo.com/CMS-course/week-6/)

ja avasin Windows PowerShellin pääkäyttäjänä.

Seuraavaksi loin uuden hakemiston komennolla:

    > mkdir C:\users\Roi\vagrant\debian
    
Vaihdoin luomaani hakemistoon komennolla:

    > cd C:\users\Roi\vagrant\debian
    
 Seuraavaksi loin uuden Vagrant-tiedoston komennolla:
 
     > vagrant init debian/bullseye64
     
 Ja loin ja käynnistin uuden virtuaalikoneen komenolla:
 
     > vagrant up

Virtuaalikoneen luotua yhdistin siihen SSH:n avulla käyttäen komentoa:

     > vagrant ssh

![image](https://user-images.githubusercontent.com/106889187/229377927-7fdbfe5e-960a-482f-a43b-476b935d4cfe.png)


## Kolmen koneen verkko (23:42)

Asennetaan kolmen koneen verkko Vagranttiin.

Aloitin luomalla uuden kansion komennolla

    > mkdir C:\users\Roi\vagrant\twohost
    
Seuraavaksi siirryin kansion sisälle ja loin sinne Vagrant-tiedoston komennoilla:

    > cd C:\users\Roi\vagrant\twohost
    
ja

     > vagrant init
     
Muokkasin seuraavaksi Vagrant tiedostoa hyödyntäen Tero Karvisen artikkelia 'Salt Vagrant - automatically provision one master and two slaves'.

    # -*- mode: ruby -*-
    # vi: set ft=ruby :
    # Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com

    $minion = <<MINION
    sudo apt-get update
    sudo apt-get -qy install salt-minion
    echo "master: 192.168.12.3">/etc/salt/minion
    sudo service salt-minion restart
    echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
    MINION

    $master = <<MASTER
    sudo apt-get update
    sudo apt-get -qy install salt-master
    echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
    MASTER

    Vagrant.configure("2") do |config|
   	config.vm.box = "debian/bullseye64"

	config.vm.define "t001" do |t001|
		t001.vm.provision :shell, inline: $minion
		t001.vm.network "private_network", ip: "192.168.12.100"
		t001.vm.hostname = "t001"
	end

	config.vm.define "t002" do |t002|
		t002.vm.provision :shell, inline: $minion
		t002.vm.network "private_network", ip: "192.168.12.102"
		t002.vm.hostname = "t002"
	end

	config.vm.define "tmaster", primary: true do |tmaster|
		tmaster.vm.provision :shell, inline: $master
		tmaster.vm.network "private_network", ip: "192.168.12.3"
		tmaster.vm.hostname = "tmaster"
	end
    end
 
 Seuraavaksi loin ja käynnistin kolmen virtuaalikoneen verkon komennolla:
 
     > vagrant up
     
  Koneiden luomisessa meni hetken aikaa (n. 10 min)
  
## Komennettavat koneet (0:00)

Kirjaudutaan komentokoneelle ja hyväksytään, sekä testataan komennettavien koneiden yhteys.

Aloitin kirjautumalla komentokoneelle komennolla:

    > vagrant ssh tmaster
    
Seuraavaksi hyväksyin orja-koneiden lähettämät avaimet komenolla:

    $ sudo salt-key -A
    
![image](https://user-images.githubusercontent.com/106889187/229379341-8728431f-a68e-40a5-a206-aba80b326580.png)
    
 Lopuksi testasin yhteyden komennolla:
 
    $ sudo salt '*' test.ping

![image](https://user-images.githubusercontent.com/106889187/229379388-1fb8dd06-0a2b-4eed-bddf-f191113824ee.png)

Yhteys toimi.

## Package, file, service, user, cmd.run

Kokeillaan eri tiloja idempotenssi-komentoja käyttäen.

Aloitin asentamalla Apache-palvelimen ohjattaville koneille komennolla:

    $ sudo salt '*' state.single pkg.installed apache2
    
 ![image](https://user-images.githubusercontent.com/106889187/229379792-9cd9686b-b38e-4592-a6c1-1dd5473ae204.png)

    
 ![image](https://user-images.githubusercontent.com/106889187/229379771-8469603a-bf41-4052-94cd-dc6a4172fa0f.png)
 
 
 Seuraavaksi tarkistin, että apache-demoni oli päällä komennolla:
 
    $ sudo salt '*' state.single service.running apache2

![image](https://user-images.githubusercontent.com/106889187/229379853-ccbd3a1c-456a-4049-b66d-0774319901db.png)


Seuraavaksi kokeilin, että onnistuuko uuden käyttäjän luominen komennolla:

    $ sudo salt '*' state.single user.present terote01

![image](https://user-images.githubusercontent.com/106889187/229379970-f209eee6-db79-420d-a010-8f81dd7e8b5d.png)

Lopuksi testasin luoda väliaikaisen tiedoston cmd.run-tilalla komennolla:

    $  sudo salt '*' state.single cmd.run 'touch /tmp/tero' creates="/tmp/tero"
    
![image](https://user-images.githubusercontent.com/106889187/229380094-2c16ea69-ddc4-49ba-a30a-ebe609b76cee.png)
   

## Infraa koodina (0:33)

Luodaan 'Hello World' 

Aloitin luomalla hakemiston ja sen sisään tiedoston komennoilla:

    $ sudo mkdir -p /srv/salt/hello
    
    $ sudoedit /srv/salt/hello/init.sls
    
Lisäsin tiedoston sisään:

    /tmp/infra-as-code:
      file.managed
 
 Kokeilin onnistuiko tiedoston luominen komennolla:
 
    $ sudo salt '*' state.apply hello
    
    
![image](https://user-images.githubusercontent.com/106889187/229380407-2f2220b8-5ad9-4432-bb33-437ae696b683.png)

Tiedoston luominen onnistui, vaikka huomasin, että olin tehnyt kirjoitusvirheen 'infra' -> 'ifra'.
 
 ## Lopuksi (0:41)
 
 Tässä harjoituksessa asensin Vagrantin avulla virtuaalikoneita ja harjoittelin komentojen antamista usealla eri koneelle käyttäen Salt-ohjelmaa.
 
## Lähteet



Infra as Code course 2023 Kevät, Tero Karvinen (https://terokarvinen.com/2023/palvelinten-hallinta-2023-kevat/)



Salt Vagrant - automatically provision one master and two slaves, Tero Karvinen (28.3.2023) (https://terokarvinen.com/2023/salt-vagrant/)

Create Virtual Machines with Vagrant, Tuomas Valkamo (5.12.2022) (https://tuomasvalkamo.com/CMS-course/week-6/)



#### Tehnyt Roi Partanen 2.4.2023-3.4.2023


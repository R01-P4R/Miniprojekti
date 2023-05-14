# Miniprojekti


Projektin tarktoituksena on Salt-työkalua hyödyntäen asentaa kolmen virtuaalikoneen verkkoon seuraavat hyödylliset ohjelmat:

 * Micro - Tekstieditori (https://github.com/zyedidia/micro)
 * Thunderbird - Mozillan avoimen lähdekoodin sähköpostiohjelma (https://www.thunderbird.net/fi/)
 * Timeshift - Järjestelmän palautustyökalu (https://github.com/teejee2008/timeshift)
 * Vim - Tekstieditori (https://github.com/vim/vim)

 
### Laitteisto
 
* Käyttöjärjestelmä: Microsoft Windows 10 Enterprise LTSC 64 bit
* Prosessori: i5-6600
* RAM: 16 GB





## Alustus

Ohjeet Vagrantin asennukseen Windowsille ja kolmen koneen verkon luomisesta löytyvät aikaisemmasta raportista: H1 (https://github.com/R01-P4R/Palvelinten-Hallinta-2023-kev-t/blob/main/H1.md).

Aloitetaan siirtymällä Windows Powershellin kautta Vagrantin avulla luotuun kolmen koneen verkkoon komennoilla:

	> cd C:\users\Roi\vagrant\twohost

	> vagrant up

	> vagrant ssh tmaster

Seuraavaksi hyväksytään orja-koneiden avaimet ja sen jälkeen on hyvä vielä testata yhteyttä käyttäen komentoja:

    $ sudo salt-key -A
      Y
    
   ja
   
    $ sudo salt '*' test.ping
  
  ![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/f273158e-ed78-4745-9ea5-9bf84f28dd38)
  

   

## Ohjelmien asennus herra-koneelle

Asennetaan Micro, Thunderbird, Timeshift ja Vim manuaalisesti Herra-koneelle.

Suoritetaan komennot:

    $ sudo-apt get update
    
    $ sudo apt-get -y install micro
    
    $ sudo apt-get -y install thunderbird
    
    $ sudo apt-get -y install timeshift
    
    $ sudo apt-get -y install vim

On hyvä tarkistaa asennusten onnistuneisuus, joko avaamalla ohjelman tai tarkastamalla version komennolla:

    $ micro --version
    
![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/364b7070-c11d-49d9-956d-abf328652a27)


## Salt

Luodaan ensiksi uusi hakemistopolku komennolla:

    $ sudo mkdir -p /srv/salt/paketit
  
 Siirytään uuteen hakemistoon ja luodaan Saltin tarvitseman init-tiedosto kommennoilla:
 
    $ cd /srv/salt/paketit
    
    $ sudoedit init.sls
    
 ![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/1836d03c-9327-4d30-83fe-0cb01948515c)


## Uusi tila 
 
 Nyt voimme puskea uuden tilan, joka asentaa ohjelmia orjille komennolla:
 
    $ sudo salt '*' state.apply paketit


![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/bc2a052e-f465-488d-82b0-0983cdc6ef90)

![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/0043dde9-f0b1-490c-8222-ba4b6e9da7de)

Uuden tilan ajaminen onnistunut orjille. On hyvä myös tarkistaa idempotenssin toimivuus suorittamalla komento:

    $ sudo salt '*' state.apply paketit 
    
Uudestaan ja tarkkailla, muuttuuko tilat.

![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/52b5118c-5ed4-4fa3-a818-8cc239110725)

Testissä tilat eivät olleet muuttuneet (changed).

Seuraavaksi on hyvä käydä katsomassa orja-koneella vielä, että ohjelmat on tosiaan asennettu.

![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/1429575e-b280-40ec-b26a-c6e1d547a866)

![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/8d758627-6ee5-454f-96d6-cfaa52ee3fd7)

![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/b7f78bd5-be17-4545-92ae-2d95c4df20a3)

![image](https://github.com/R01-P4R/Miniprojekti/assets/106889187/2348f573-4bb3-443e-80e6-953144625b11)

Ohjelmat toimivat odotetulla tavalla.



## Lähteet




Infra as Code course 2023 Kevät, Tero Karvinen (https://terokarvinen.com/2023/palvelinten-hallinta-2023-kevat/)

Salt Vagrant - automatically provision one master and two slaves, Tero Karvinen (28.3.2023) (https://terokarvinen.com/2023/salt-vagrant/)

Kurssin Palvelinten Hallinta raportit: (https://github.com/R01-P4R/Palvelinten-Hallinta-2023-kev-t)

Micro (https://github.com/zyedidia/micro)

Thunderbird (https://www.thunderbird.net/fi/)

Timeshift (https://github.com/teejee2008/timeshift)

Vim (https://github.com/vim/vim)


#### Tehnyt Roi Partanen 15.5.2023


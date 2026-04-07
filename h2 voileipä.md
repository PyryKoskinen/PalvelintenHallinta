# h2 voileipä

1. Sudo without password
   
   Ansible tarvitsee root oikeudet orjakoneella, yksi tapa on tehdä käyttäjälle salasanaton sudo (NOPASSWD).

   Rooli luo ryhmän sudoless, lisää käyttäjän tähän ryhmään ja asentaa sudoers tiedoston /etc/sudoers.d/sudoless

   Avaa varmuuden vuoksi root-shell komennolla sudo -i, jos muokkaat sudo-asetuksia. Älä kuitenkaan työskentele root-shellissa, vaan pidä se auki varalla ja jatka työskentelyä normaalissa käyttäjäikkunassa.

   Sudoers‑rivi on: %sudoless ALL=(ALL) NOPASSWD: ALL, mikä tarkoittaa, että ryhmän jäsenet voivat ajaa mitä tahansa komentoja sudolla kysymättä salasanaa.
   
   Oma huomio: Hyvä käytäntö on aina käyttää /etc/sudoers.d/–hakemistoa eikä koskea itse pääsudoers-tiedostoon, jotta virheet ovat helpommin korjattavissa.

3. Ilman sudoa pyyntö ohitetaan (“tee itse”), mutta kun komento annetaan sudon kanssa, se toteutetaan heti.
   
5. Passwordless sudo with ansible
   
   Ennen automaatiota kannattaa testata passwordless sudo käsin (“Manual before auto”)

   Avataan sudoers‑tiedosto turvallisesti visudo‑komennolla (tarkistaa syntaksin).

   Aluksi sudo ilman salasanaa ei ole vielä käytössä. Pitää lisätä site.yml-tiedostoon become: true, jotta Ansible yrittää käyttää sudoa. Ensimmäisellä ajolla Ansible pyytää sudo salasanaa, joten suoritettava playbook komennolla --ask-become-password
   Jos salasanaa ei kysytä  passwordless sudo toimii.

   Oma kysymys: Voidaanko rajoittaa, että tietyllä ryhmällä on NOPASSWD‑oikeudet vain tiettyihin komentoihin, eikä kaikkiin?

# a) Sudo without password

Aloitin tehtävän varmistamalla, että SSH-yhteys sekä Ansible toimivat oikein. Tämän jälkeen aloitin tehtävän teon, toteutus sisälsi useita vaiheita. Ensiksi loin uuden käyttäjäryhmän nimeltä sudoless. Tämän jälkeen loin käyttäjän nimeltä anteroo ja lisäsin hänet ryhmään sudoless. Tehtävässä harjoiteltiin myös virhetilanteeseen varautumista jossa sudo-toiminnallisuus rikkoutuu. 

On tärkeää pitää mielessä että jos sudoa käytetään järjestelmän hallintaan, sen virheellinen konfigurointi voi estää kokonaan pääsyn korjaustoimenpiteisiin normaalin käyttäjän kautta. Tämän riskin ehkäisemiseksi avasin uuden terminaali-ikkunan ja muodostettiin SSH-yhteys kohdekoneeseen. Tämän jälkeen avattiin erillinen pääkäyttäjän (root) komennolla: sudo -i.
Seuraavaksi tavoitteena oli luoda uusi sudoers-sääntö, jonka avulla sudoless-ryhmän jäsenet voivat käyttää sudo-komentoja ilman salasanan syöttämistä. 
Tiedostojen muokkaamiseen käytettiin komentoa visudo: sudo visudo /etc/sudoers.d/sudoless 
Tiedostoon lisättiin seuraava rivi: %sudoless ALL = (ALL) NOPASSWD: ALL
Tämä mahdollistaa sen, että kaikki sudoless-ryhmään kuuluvat käyttäjät voivat suorittaa mitä tahansa komentoja sudo-oikeuksilla ilman salasanan kyselyä.

Lopuksi suoritettiin tehtävän testaus. Testauksessa huomioitiin, että sudo säilyttää käyttäjän tunnistautumistiedot välimuistissa jonkin aikaa, mikä voi vaikuttaa testituloksiin. Tämän vuoksi välimuisti tyhjennettiin komennolla:sudo -k
Tämän jälkeen muodostettiin uusi SSH-yhteys ja testattiin sudo-oikeuksien toimivuus seuraavasti: 
ssh antero@localhost 
sudo -k sudo echo 
"See you at TeroKarvinen.com"
Komennon suoritus onnistui ilman salasanakyselyä, ja tuloste näkyi odotetusti

<img width="684" height="80" alt="image" src="https://github.com/user-attachments/assets/d9890e18-3d11-45d4-9fe0-2de61b2076bd" />

# b) Passwordless Sudo with Ansible 

Tavoite Ansible tarvitsee root-oikeudet etäpalvelimella. Ratkaisuna luodaan käyttäjä, joka voi käyttää sudoa ilman salasanan pyyntöä.
Ensimmäinen vaihe oli luoda Ansible‑projektille selkeä hakemistorakenne. <img width="517" height="264" alt="image" src="https://github.com/user-attachments/assets/8d965190-741e-4ace-9c61-3841a73312ba" />

Tämän jälkeen aloin tekemään tarvittavat konfiguraatiot.Aloitin tekemällä yksinkertaisen ansible.cfg‑tiedoston, joka ohjaa Ansiblea käyttämään projektin omaa hosts.ini‑tiedostoa ja estää host key ‑varmistukset.

<img width="491" height="78" alt="image" src="https://github.com/user-attachments/assets/52e17257-7f0e-4fdd-89c6-70a2ae9e6bf7" /> 

Seuraavaksi määrittelin kohdekoneen hosts.ini‑tiedostoon; tässä tapauksessa käytin pelkkää localhost, jotta roolia voi testata omalla koneella.
Seuraavaksi tein site.yml‑playbookin. Tähän lisättiin tärkeä rivi become: true, koska tehtävät vaativat root‑oikeuksia. Ilman tätä Ansible ei pystyisi luomaan käyttäjiä, ryhmiä eikä muokkaamaan /etc/sudoers.d/‑hakemistoa.
Viimeiseksi loin projektin ytimen eli roolin antero, jonka sisällä on neljä peräkkäistä tehtävää: <img width="826" height="460" alt="image" src="https://github.com/user-attachments/assets/58811637-6a7a-4449-bc93-0e4b7fa1535d" />

ansible lopputulostus 
<img width="1267" height="656" alt="image" src="https://github.com/user-attachments/assets/83857ed1-9303-41d3-a25d-a40685ef91ad" />
huomasin myös että Ansible ei tue komentoa:
--ask-become-password.
Toimiva muoto on:
--ask-become-pass

# c) Pakettien asennus

Tehtävässä oli tarkoitus asentaa kaksi pakettia Ansiblella. Lisäsin nämä tehtävät rooliin "world" tiedostoon roles/world/tasks/main.yml. Käytin Ansible package moduulia, joka toimii eri Linux-jakeluissa 
riippumatta käytettävästä pakettienhallinnasta. Asensin paketit curl ja htop seuraavasti: 

<img width="818" height="462" alt="image" src="https://github.com/user-attachments/assets/60fe7fc4-b4c9-45ab-b7e4-4aaeb8c8a40e" />


Playbook ajettiin komennolla ansible-playbook site.yml --ask-become-pass, jotta Ansible pystyi asentamaan paketit root-oikeuksilla.

# d) File

tarkoituksena oli kirjoittaa kohdekoneelle monirivinen tiedosto 
sekä määritellä sen omistaja, ryhmä ja käyttöoikeudet oktalimuodossa. 
Loin tiedoston copy-moduulilla seuraavasti:

<img width="412" height="182" alt="image" src="https://github.com/user-attachments/assets/e0855112-b88b-4488-ba08-dca1f8874b6b" />

Oktalimuoto '0640' vastaa symbolisesti '-rw-r-----'. 
Tämä tarkoittaa, että omistaja (anteroo) saa lukea ja kirjoittaa tiedostoa, 
ryhmä (sudoless) saa ainoastaan lukea sen, ja muilla käyttäjillä 
ei ole lainkaan oikeuksia tiedostoon.

<img width="696" height="42" alt="image" src="https://github.com/user-attachments/assets/731ea797-aaf6-485c-b1b5-fab89adab7f3" />

# e) Mitä vaan

Tarkoituksena oli testata ja näyttää jokin uusi Ansible käsky tai moduuli. Löysin esimerkiksi service moduulin, jolla voidaan hallita Linux palveluita. 
Alla oleva esimerkki varmistaa että cron palvelu on käynnissä ja käynnistyy automaattisesti tietokoneen boottauksen yhteydessä:

<img width="550" height="117" alt="image" src="https://github.com/user-attachments/assets/4aaa5399-3d92-4db3-821e-824e8c950875" />

# Lähteet

Karvinen, Tero. 2026. SSH public key - Login without password. https://terokarvinen.com/ssh-public-key-login-without-password/

Karvinen, Tero. 2026. Hello Ansible. https://terokarvinen.com/hello-ansible/

Karvinen, Tero. 2026. Sudo without password. https://terokarvinen.com/passwordless-sudo/

Karvinen, Tero. 2026. Passwordless Sudo with Ansible. https://terokarvinen.com/passwordless-sudo-with-ansible/

#h2 voileipä

a) Aloitin tehtävän varmistamalla, että SSH-yhteys sekä Ansible toimivat oikein. Tämän jälkeen aloitin tehtävän teon, toteutus sisälsi useita vaiheita. Ensiksi loin uuden käyttäjäryhmän nimeltä sudoless. Tämän jälkeen loin käyttäjän nimeltä anteroo ja lisäsin hänet ryhmään sudoless. Tehtävässä harjoiteltiin myös virhetilanteeseen varautumista jossa sudo-toiminnallisuus rikkoutuu. 

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

b)  Tavoite Ansible tarvitsee root-oikeudet etäpalvelimella. Ratkaisuna luodaan käyttäjä, joka voi käyttää sudoa ilman salasanan pyyntöä.
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

c) Tehtävässä oli tarkoitus asentaa kaksi pakettia Ansiblella. Lisäsin nämä tehtävät rooliin "world" tiedostoon roles/world/tasks/main.yml. Käytin Ansible package moduulia, joka toimii eri Linux-jakeluissa 
riippumatta käytettävästä pakettienhallinnasta. Asensin paketit curl ja htop seuraavasti: 

<img width="813" height="460" alt="image" src="https://github.com/user-attachments/assets/c1a49e15-1444-4c53-b4a2-eadfca7988e8" />

Playbook ajettiin komennolla ansible-playbook site.yml --ask-become-pass, jotta Ansible pystyi asentamaan paketit root-oikeuksilla.

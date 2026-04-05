#h2 voileipä

a) Aloitin tehtävän varmistamalla, että SSH-yhteys sekä Ansible toimivat oikein. Tämän jälkeen aloitin tehtävän teon, toteutus sisälsi useita vaiheita. Ensiksi loin uuden käyttäjäryhmän nimeltä sudoless. Tämän jälkeen loin käyttäjän nimeltä anteroo ja lisäsin hänet ryhmään sudoless. Tehtävässä harjoiteltiin myös virhetilanteeseen varautumista jossa sudo-toiminnallisuus rikkoutuu. On tärkeää pitää mielessä että jos sudoa käytetään järjestelmän hallintaan, sen virheellinen konfigurointi voi estää kokonaan pääsyn korjaustoimenpiteisiin normaalin käyttäjän kautta. Tämän riskin ehkäisemiseksi avattiin uusi terminaali-ikkuna ja muodostettiin SSH-yhteys kohdekoneeseen. Tämän jälkeen avattiin erillinen pääkäyttäjän (root) komennolla: sudo -i.
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


ansible lopputulostus <img width="1267" height="656" alt="image" src="https://github.com/user-attachments/assets/83857ed1-9303-41d3-a25d-a40685ef91ad" />


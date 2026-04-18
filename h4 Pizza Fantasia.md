# h4 Pizza Fantasia

## 4.12.1 Size and Complexity of Some DSLs

* Domain Specific Languaget (DSL) voivat kasvaa hyvin monimutkaisiksi ja laajoiksi.
* Saltin DSL analysoitiin sen virallisen dokumentaation perusteella, joka generoidaan suoraan lähdekoodista.
* Saltin dokumentaatio on erittäin laaja: yli 20 000 riviä ja 75 000 sanaa ilman ohjausrakentei
* Puppet tarjoaa huomattavasti vähemmän, 113 toimintoa, eikä sisällä suoraan perinteisiä ohjausrakenteita.
* Puppet käyttää omia käsitteitään, kuten virtual resources, resurssien ja niiden suhteiden hallintaan.
* Salt ja Puppet edustavat erilaisia lähestymistapoja DSL‑suunnitteluun: Salt korostaa laajuutta, Puppet rakennetta.

## 4.12.2 Use of DSL Functions in Case Configuration

* Vaikka CM-työkalujen DSL:t sisältävät paljon toimintoja ja sääntöjä, oman resurssiabstraktion rakentaminen ei välttämättä ole niin iso tai monimutkainen juttu kuin aluksi voisi ajatella. Monissa tuotantoympäristöissä toistuvat samat ohjelmalliset mallit, erityisesti pakettien, tiedostojen ja palveluiden määrittelyssä. 

- Puppet-DSL:n käyttöä tarkasteltiin kahdessa laajasti käytössä olevassa tuotantokonfiguraatiossa:
   - Mozilla Release Engineering Puppet Manifests
   - United States Government Configuration Baseline (USGCB)


* Mozilla‑konfiguraatioissa suurin osa käytöstä keskittyi pieneen määrään toimintoja, kuten file, package, service ja exec.
* Yli 1 % käytetyistä komennoista kattoi noin 87 % kaikesta käytöstä, mikä osoittaa, että vain harvoja toimintoja tarvitaan käytännössä.
* Tulokset osoittavat, että vaikka CM‑työkalut tarjoavat paljon toimintoja, pieni joukko peruskomentoja kattaa suurimman osan todellisista käyttötapauksista.
* Havainnot vastaavat kirjoittajan käytännön kokemuksia Puppet‑ ja Salt‑konfiguraation hallinnan opetuksesta.

## 4.12.3.1 Dependencies Between Main Functions

* Luku käsittelee konfiguraationhallintajärjestelmien funktioiden välisiä riippuvuuksia kehittäjän näkökulmasta.
* Yleisimmät toiminnot konfiguraationhallinnassa ovat package‑file‑service, sekä käyttäjien ja ryhmien hallinta (user, group) ja komennot (exec).
* Tyypillisiä käyttötapauksia ovat daemonien ja sovellusten asennus sekä tarvittavien käyttäjien ja ryhmien luonti teknisiä tarkoituksia varten.
* Järjestelmän tulee olla idempotentti, eli se tekee muutoksia vain silloin, kun järjestelmä ei ole jo halutussa tilassa.
* Idempotenttius voidaan toteuttaa tarkistuksilla, esimerkiksi luomalla tiedosto vain, jos sitä ei vielä ole, tai korjaamalla sisältö vain jos se poikkeaa tavoitetilasta.
* exec ja file ovat järjestelmän perustoiminnot, joiden varaan muut funktiot kuten package, service, user ja group voidaan rakentaa.
* Korkeamman tason toiminnallisuuksia, kuten cron‑ajastuksia, voidaan toteuttaa näiden perusfunktioiden avulla.

## Tehtävä a) Räpylä – MariaDB‑demonin asennus käsin

Tehtävässä tarkoituksena oli asentaa itse valitsemani demoni käsin sekä varmistaa sen toiminta testaamalla lopputulos. Valitsin demoniksi MariaDB‑tietokantapalvelun, koska se on yleisesti käytetty tuotantoympäristöissä ja toimii jatkuvasti taustalla palveluna useille sovelluksille.

Aloitin tehtävän huolehtimalla tietoturvasta ennen itse tietokannan asennusta. Koska tietokantapalvelu ei saa olla avoin verkkoon, otin käyttöön palomuurin ja varmistin, että SSH yhteys säilyy mahdollisena etähallintaa varten. Tämä tehtiin sallimalla SSH liikenne ja aktivoimalla palomuuri seuraavilla komennoilla:
```bash
sudo ufw allow 22/tcp
sudo ufw enable
```
Seuraavaksi asensin MariaDB‑palvelimen ja asiakasohjelman käsin Teron ohjeiden mukaan käyttäen järjestelmän paketinhallintaa.

Asennus onnistui seuraavilla komennoilla:
`sudo apt-get update`
`sudo apt-get -y install mariadb-client mariadb-server`

Asennuksen jälkeen MariaDB‑demoni käynnistyi automaattisesti. Varmistin demonin toiminnan tarkistamalla sen tilan: `systemctl status mariadb`

Tämän jälkeen suoritin tietokannan perusturvallisuusasetukset käyttäen valmista työkalua. Seurasin ohjeita eli asetettiin root käyttäjälle salasana, poistettiin anonyymit käyttäjät, estettiin etäkirjautuminen root käyttäjänä ja poistettiin testitietokanta:

<img width="466" height="128" alt="image" src="https://github.com/user-attachments/assets/24eb9617-3066-4040-9e9e-dd48c976078a" />

Kuva on suuntaa-antava. Komento `sudo mysql_secure_installation` koskee vanhempaa versiota, joten käytin sen sijaan komentoa `sudo mariadb-secure-installation`. Kuva on peräisin Teron ohjeista

Kun MariaDB oli suojattu, testasin demonin toiminnan kirjautumalla tietokantaan paikallisesti. Kirjauduin root käyttäjänä MariaDB komentotulkkiin:

<img width="598" height="77" alt="image" src="https://github.com/user-attachments/assets/858a37f4-8990-47f2-9d24-113304a42977" />

Tästä nähdään että demoni vastasi pyyntöihin oikein. Testasin vielä palvelua ajamalla: `SELECT VERSION();`

<img width="423" height="179" alt="image" src="https://github.com/user-attachments/assets/8955e699-307f-4436-a7ad-6a04588fd273" />

Kysely palautti MariaDB:n versionumeron, mikä vahvisti, että palvelu toimii odotetulla tavalla.

# Lopputulos

Tehtävän päätteeksi MariaDB oli asennettu käsin, demoni oli käynnissä ja vastasi pyyntöihin. Palomuuri oli käytössä, ja tietokannan perusasetukset oli kovennettu. Demonin toiminta varmistettiin käytännön testillä, ja se toimi vaatimusten mukaisesti.

Mieleen jäi että demonin asentaminen ei yksin riitä, vaan tietoturva on otettava huomioon heti alusta alkaen. Palomuuri ja root-käyttäjän rajoittaminen ovat olennaisia perusasioita.

## b) Automaatti – MariaDB‑demonin asennuksen automatisointi Ansiblella

Käytin tehtävässä pohjana aiemmissa tehtävissä luotua Ansible projektia, jossa oli valmiina perusrakenne (ansible.cfg, hosts.ini, site.yml ja roles‑hakemisto). Työ tehtiin samassa SSH yhteydessä ja samalla kohdekoneella kuin aiemmat automaatiotehtävät, jotta tehtävät muodostavat yhtenäisen kokonaisuuden.

Aloitin luomalla uuden Ansible roolin MariaDB:tä varten. Roolin tarkoituksena oli pitää MariaDB asennus selkeästi erillään muista automaatioista. 
- Roolin hakemistorakenne luotiin komennolla: `mkdir -p roles/mariadb/{tasks,handlers,files}`

Seuraavaksi määrittelin MariaDB asennuksen roolin tehtävätiedostossa roles/mariadb/tasks/main.yml. `nano roles/mariadb/tasks/main.yml` Ja siihen lisättiin seuraavat tehtävät:

<img width="588" height="260" alt="image" src="https://github.com/user-attachments/assets/0c55703c-8b08-4715-bdb7-8f4b240d0830" />

Ensimmäinen tehtävä huolehtii siitä, että paketit ovat asennettuina. Toinen tehtävä varmistaa, että demoni on käynnissä ja että se käynnistyy automaattisesti järjestelmän käynnistyessä.

Sitten lisäsin roolin mariadb aiemmin luotuun site.yml‑playbookiin. Taas koska kyse on ohjelmistojen asennuksesta ja palveluiden hallinnasta käytetään become: true asetusta.

<img width="513" height="178" alt="image" src="https://github.com/user-attachments/assets/d86fcde5-57dc-45f3-904d-baf1331b4b31" />

Muut roolit oltiin muutettu kommenteiksi playbook ajon nopeuttamiseksi

Kun rooli oli liitetty playbookiin, ajoin automaation tutulla komennolla: `ansible-playbook site.yml --ask-become-pass`

<img width="820" height="256" alt="image" src="https://github.com/user-attachments/assets/1e24a60e-b5de-41fa-8709-333ff1c1183f" />

Ajon jälkeen testasin automaation onnistumisen. Ensimmäiseksi tarkistin MariaDB palvelun tilan: `systemctl status mariadb`

<img width="806" height="318" alt="image" src="https://github.com/user-attachments/assets/0b52c190-1dd9-4c58-9117-5a8fd17b0b84" />

Palvelun tila oli active (running), mikä osoitti demonin olevan käynnissä. Tämän jälkeen varmistin, että MariaDB vastaa pyyntöihin kirjautumalla komentotulkkiin: `sudo mariadb`

<img width="805" height="137" alt="image" src="https://github.com/user-attachments/assets/89741711-567f-4b0a-bea5-3dc3fe38aac6" />

kehote avautui onnistuneesti, mikä vahvisti, että demoni oli toiminnassa myös automaation jälkeen.

# Lopputulos 

MariaDB‑demoni oli asennettuna ja käynnissä täysin Ansible‑automaation avulla. Sama lopputulos, joka aiemmin saavutettiin käsin, voitiin nyt tuottaa toistettavasti yhdellä playbookin ajolla.
Manuaalinen tekeminen toimii hyvänä oppimiskeinona, mutta automaatio on välttämätöntä useiden tai uudelleen rakennettavien järjestelmien hallinnassa.

## c) Asetus – MariaDB‑asetuksen muuttaminen Ansiblella

- Tehtävänä oli muuttaa MariaDB:n asetusta ohjauskoneella, ajaa Ansible uudelleen ja osoittaa, että muutos tuli voimaan kohdejärjestelmässä.
- Valitsin muutettavaksi asetukseksi max_connections, joka määrittää sallittujen samanaikaisten tietokantayhteyksien määrän. Asetus on helppo todentaa SQL kyselyllä eikä riko järjestelmää.

Loin oman asetustiedoston MariaDB roolin files‑hakemistoon: `nano roles/mariadb/files/99-custom.cnf`

<img width="611" height="65" alt="image" src="https://github.com/user-attachments/assets/3fc5639e-9c97-4a1e-9c37-ddb66a6bd31a" />

Lisäsin roolin tehtäviin asetustiedoston kopioinnin ja määrittelin handlerin käynnistämään MariaDB:n uudelleen:

<img width="541" height="154" alt="image" src="https://github.com/user-attachments/assets/4ecf5cf8-14c8-48ab-bc2d-6f0b0b31dd39" />

Handler: 

<img width="602" height="98" alt="image" src="https://github.com/user-attachments/assets/3147b657-c5e3-4f19-8491-3d0e65fc896b" />

Ajoin playbookin uudelleen `ansible-playbook site.yml --ask-become-pass`

Viimeiseksi tarkistin muutokset kirjautumalla tietokantaan: `sudo mariadb` ja tarkistin asetuksen komennolla: `SHOW VARIABLES LIKE 'max_connections';`

<img width="820" height="364" alt="image" src="https://github.com/user-attachments/assets/28b7508a-55cd-4f45-a3e0-45269bf9d710" />

Tuloksena näkyi arvo 50, mikä osoitti asetuksen tulleen voimaan.

Tämän työn tekemisessä hyödynnettiin tekoälyä (Copilot) tukena tehtävien ymmärtämisessä ja komentojen rakenteen hahmottamisessa. Kaikki komennot suoritettiin itse ja lopputulokset testattiin käytännössä järjestelmässä.

## d) Paikka remonttiin – Riko tila ja korjaa Ansiblella

Ennen järjestelmän rikkomista varmistin, että MariaDB oli asennettuna ja demoni käynnissä. Lisäksi tarkistin, että aiemmin määritelty asetus (max_connections) oli voimassa.

<img width="1061" height="327" alt="image" src="https://github.com/user-attachments/assets/f1902dcb-7df3-4dbe-9ce5-03ce5e8aaef5" />

Tuloste osoitti, että palvelun tila oli active (running).

<img width="1076" height="360" alt="image" src="https://github.com/user-attachments/assets/9a1b7f47-94c8-4733-bfa5-e25d2c0c14ac" />

Tuloksena näkyi arvo 50, mikä varmisti, että äsken tehty asetus oli käytössä ennen rikkomista.

Seuraavaksi rikotaan järjestelmä tilan tarkoituksella poistamalla MariaDB palvelin ja sen asetukset. Käytin purgea, jotta myös asetustiedostot poistuvat. `sudo apt-get purge mariadb-server mariadb-client`

<img width="822" height="456" alt="image" src="https://github.com/user-attachments/assets/239c05f2-eeae-470d-a173-2403067b8f1f" />

Poiston jälkeen varmistin, että MariaDB‑demoni ei ollut enää järjestelmässä. Tarkistin palvelun tilan: `systemctl status mariadb`

<img width="517" height="32" alt="image" src="https://github.com/user-attachments/assets/d6adc09f-5682-4ffe-b01f-b51a0506984a" />

Tämä osoitti, että palvelu oli poistettu.

Playbook korjaus
<img width="1073" height="321" alt="image" src="https://github.com/user-attachments/assets/6576fe44-5d7c-4f6c-9075-04771a416096" />


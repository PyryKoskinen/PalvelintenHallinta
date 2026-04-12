## H3 Demoni

# 1. Apache installed with ansible

Apache2-webpalvelin asennetaan ja konfiguroidaan automaattisesti Ansiblella.

Käytetään package–file–service‑mallia:
package: asennetaan apache2
file: kopioidaan ja linkitetään konfiguraatiotiedostot
service: käynnistetään tai uudelleenkäynnistetään palvelu

Konfiguraatiomuutokset tehdään tiedostoihin, ja ne aktivoituvat vasta, kun Apache käynnistetään uudelleen.

Sivuston DocumentRoot voidaan asettaa käyttäjän kotihakemistoon, jolloin sivuja voi muokata ilman sudo‑oikeuksia.

Oma huomio: Package file service malli tekee palveluiden hallinnasta selkeää ja toistettavaa, koska asennus, konfigurointi ja käynnistys on erotettu omiin vaiheisiinsa.

# 2. Handlers: running operations on change

Joissain tilanteissa tehtävä halutaan suorittaa vain silloin, kun järjestelmään tulee muutos.
Esimerkiksi palvelu kannattaa käynnistää uudelleen vain jos sen konfiguraatiotiedosto muuttui.
Ansible ratkaisee tämän käyttämällä handlereita.
Handlerit ovat tehtäviä, jotka suoritetaan vain jos ne saavat notify‑ilmoituksen.
Jos muutosta ei tapahdu, handleria ei ajeta ollenkaan.

Notifying handlers:
  notify‑avainsanalla tehtävä voi ilmoittaa yhden tai useamman handlerin.
  Handlerit suoritetaan vain jos tehtävä aiheuttaa muutoksen (changed).
  Yksi tehtävä voi ilmoittaa useita handlereita.
  Jos sama handler ilmoitetaan useita kertoja, se suoritetaan vain kerran playbook‑ajon aikana.
  Tämä estää turhat palveluiden uudelleenkäynnistykset (esim. Apache restart vain kerran).

# a) Apassi – Apache 2:n asennus käsin

Apache2 asennettiin käsin apt paketinhallinnalla. Oletussivun sijaan Apache konfiguroitiin käyttämään käyttäjän kotihakemistossa olevaa
publicsite hakemistoa DocumentRootina. Tämän ansiosta verkkosivunsisältöä voi muokata tavallisena käyttäjänä ilman sudo oikeuksia.

Aloitin tehtävän asentamalla apache2:n komennoilla: sudo apt update, sudo apt install apache2
Asennuksen jälkeen käynnistin sen vielä komennolla: sudo systemctl start apache2
jonka jälkeen varmistin Toimivuuden selaimessa avaamalla osoitteen http://localhost

Seuraavaksi oli tavoitteena tehdä sivuston muokkaaminen mahdolliseksi ilman sudo oikeuksia.
Apachen oletus DocumentRoot on /var/www/html, jota ei voi muokata tavallisena käyttäjänä.
Tämän vuoksi luotiin käyttäjän anteroo kotihakemistoon oma hakemisto verkkosivuja varten:

`mkdir -p /home/anteroo/publicsite`

Sitten hakemistolle testisivu jonka sisältö näytti tältä: `nano /home/anteroo/publicsite/index.html`

<img width="657" height="67" alt="image" src="https://github.com/user-attachments/assets/6468472b-6bd7-4fde-9ab7-ab6758bc7ce6" />

Apache konfigurattion muokkausta varten luotiin uusi Virtualhost konfiguraatio

<img width="575" height="16" alt="image" src="https://github.com/user-attachments/assets/0022789c-6721-4a6d-888b-ac6d58edffc6" />
<img width="668" height="207" alt="image" src="https://github.com/user-attachments/assets/58577da8-a488-40dd-9a79-f4b5ea9b1c40" />

Otin sivuston käyttöön ja poistin oletussivuston käytöstä komennoilla:

```bash
sudo a2ensite publicsite.conf (ottaa käyttöön sivuston konfiguraation publicsite.conf)
sudo a2dissite 000-default.conf (Poistaa oletussivuston 000-default.conf käytöstä)
sudo systemctl restart apache2 (Käynnistää Apache-palvelimen uudelleen, jotta muutokset tulevat voimaan)
```

## **Pikku ongelma ja sen ratkaisu**

Aluksi selain näytti virheen 403 Forbidden.

Syynä oli se, että Apachella ei ollut suoritusoikeutta käyttäjän kotihakemistoon.
Apache tarvitsee execute oikeuden (x) kaikkiin hakemistopolun osiin päästäkseen DocumentRootiin.

Ongelma korjattiin helposti komennoilla:

```bash
chmod o+x /home
chmod o+x /home/anteroo
chmod o+rx /home/anteroo/publicsite
```

# b) Moottorix - Nginx webpalvelimen asennus käsin

Aloitin tehtävän varmistamalla, että järjestelmä toimi normaalisti ja että aiemmin käytetty Apache‑webpalvelin ei ollut käynnissä. Koska Apache ja Nginx käyttävät oletuksena samaa porttia (80), Apache pysäytettiin ja poistettiin käytöstä ennen Nginxin asentamista. Tämä varmistaa sen, ettei palveluiden välille synny porttiristiriitaa.

Seuraavaksi asensin Nginx webpalvelimen käsin käyttäen järjestelmän paketinhallintaa: `sudo apt install nginx 

Asennuksen jälkeen tarkistin, että Nginx saatiin käynnistettyä ja että se pystyy kuuntelemaan porttia 80. Testasin myös, että Apache ei enää vastannut pyyntöihin ja että Nginx oli ainoa porttia käyttävä palvelu.

Jotta verkkosivua voisi muokata tavallisena käyttäjänä ilman sudo‑oikeuksia, sijoitetin sivuston käyttäjän(pyryk) kotihakemistoon. Loin käyttäjän kotihakemistoon erillisen publicsite hakemiston ja lisäsin sinne index.html‑tiedoston. Jolla pystyi varmistamaan, että oikea sivu latautuu selaimessa. 

`nano /home/pyryk/publicsite/index.html`

Hakemiston ja tiedostojen omistajaksi asetettiin käyttäjä pyryk, jolloin muokkaus onnistuu ilman sudoa.

<img width="818" height="424" alt="image" src="https://github.com/user-attachments/assets/f346491b-ee85-47bc-a906-e4963a08d834" />





Tämän jälkeen loin Nginxille oman server block konfiguraation. Konfiguraatiossa määriteltiin DocumentRoot osoittamaan käyttäjän kotihakemistossa olevaan publicsite‑hakemistoon sekä asetettiin index.html oletussivuksi. 

`sudo nano /etc/nginx/sites-available/publicsite`

<img width="819" height="465" alt="image" src="https://github.com/user-attachments/assets/cdcd6758-53b9-44de-a5f4-7898d8f0da5b" />

Lisäksi poistettiin Nginxin oletussivusto käytöstä, jotta selain ei enää näyttäisi järjestelmän mukana tullutta oletussivua.

`sudo rm /etc/nginx/sites_enabled/default`
`sudo ln -s /etc/nginx/sites-available/publicsite /etc/nginx/sites-enabled/`

## Testaus
Konfiguraatio testattiin: 

<img width="716" height="53" alt="image" src="https://github.com/user-attachments/assets/8fcb065d-7225-4030-bae4-a6738912cb04" />

kun testaus onnistui, Nginx käynnistettiin uudelleen: `sudo systemctl restart nginx`

Lopuksi testasin lopputuloksen avaamalla selaimessa osoitteen http://localhost. Sivusto latautui oikein ja näytti käyttäjän kotihakemistossa olevan sivun sisällön. 

<img width="1283" height="771" alt="image" src="https://github.com/user-attachments/assets/b403c125-34e8-4d6b-bc65-9fbe6ed7c094" />
    
# c) Automoottorix - Nginx‑webpalvelimen automatisointi Ansiblella

Varmistin ensin, että yhteys kohdekoneeseen toimi. Hyödynsin työn pohjana aiemmin tekemääni Ansible-projektia enkä aloittanut alusta. Käytössäni oli valmiiksi luotu perusrakenne (ansible.cfg, hosts.ini, site.yml sekä roles-hakemisto), mikä nopeutti työn käynnistämistä. Tämä vastaa myös käytännön työskentelytapaa, jossa automaatiota kehitetään vaiheittain ja aiempaa työtä hyödyntäen. Lisäksi varmistin, että Apache‑webpalvelin oli sammutettu, jotta se ei varaisi porttia 80.

Lisäsin projektiin uuden roolin nimeltä nginx. Roolille luotiin tarvittavat alihakemistot (tasks, handlers ja files), jotta Nginxin asennus, konfiguraatio ja palvelun hallinta saatiin eroteltua selkeästi toisistaan.
`mkdir -p roles/nginx/tasks
mkdir -p roles/nginx/handlers
mkdir -p roles/nginx/files`
tarkistin rakenteen: `tree roles/nginx`

<img width="450" height="191" alt="image" src="https://github.com/user-attachments/assets/0ca431e4-7827-4a11-9b45-e5f8cb736185" />

Seuraavaksi loin server block konfiguraation jota ansible käyttää lähdetiedostona. `nano roles/nginx/files/publicsite.conf` Konfiguraatiossa määriteltiin, että Nginx kuuntelee porttia 80 ja käyttää DocumentRootina käyttäjän kotihakemistossa olevaa hakemistoa. Näin HTML sivut voidaan muokata tavallisena käyttäjänä ilman sudo oikeuksia.

<img width="952" height="614" alt="image" src="https://github.com/user-attachments/assets/c3f70b7a-a4bc-4574-94fc-6c2f7eb215ce" />

Sitten luotiin handleri Nginx palvelun uudelleenkäynnistämistä vasten jos konfiguraatio muuttuu: `nano roles/nginx/handlers/main.yml`

<img width="642" height="118" alt="image" src="https://github.com/user-attachments/assets/e9e9fb07-e229-4f48-96d3-e09ccb49aa8b" />

Nginx roolin varsinaiset tehtävät sijoitettiin tiedostoon: `nano roles/nginx/tasks/main.yml`

<img width="950" height="617" alt="image" src="https://github.com/user-attachments/assets/1449876b-9b27-4992-9bc9-e71fcc98f9f7" />

Tehtävät huolehtivat Nginxin asentamisesta, oletussivuston poistamisesta, uuden konfiguraation kopioinnista sekä sivuston käyttöönotosta.

Käytin aiemmin tehtyä site.yml tiedostoa (playbookia) hyväksi ja päivitin sinne siten, että uusi nginx rooli ajetaan: `nano site.yml`

<img width="590" height="163" alt="image" src="https://github.com/user-attachments/assets/07a74746-7193-4889-a322-6548ed4dbeea" />
Tämäkin rooli suoritetaan become: true asetuksella, koska palvelimen hallinta vaatii ylläpitäjän oikeuksia.

Loin käyttäjän kotihakemistoon hakemiston ja sivun käsin: 
```bash
mkdir -p /home/pyryk/publicsite
nano /home/pyryk/publicsite/index.html
```
Ajoin playbookin ja seurasin mahdollisia virheilmoituksia
´ansible-playbook site.yml --ask-become-pass´

<img width="954" height="390" alt="image" src="https://github.com/user-attachments/assets/17c66c94-1646-4101-8ef6-a5152dbcb43b" />

syntaxi erroreilta selvitty tällä kertaa

Kun playbook oli suoritunut onnistuneesti varmistin vielä että nginx kuunteli porttia 80 ja sivusto aukesi oikein
`sudo ss -ltnp | grep :80`

<img width="921" height="97" alt="image" src="https://github.com/user-attachments/assets/e2edf345-6f93-489d-8efd-0dffa4f233d9" />

<img width="1283" height="799" alt="image" src="https://github.com/user-attachments/assets/dcc37361-1fb5-4189-bac7-9f28b57d19f2" />

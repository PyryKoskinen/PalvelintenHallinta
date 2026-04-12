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

mkdir -p /home/anteroo/publicsite

Sitten hakemistolle testisivu jonka sisältö näytti tältä: nano /home/anteroo/publicsite/index.html

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





    

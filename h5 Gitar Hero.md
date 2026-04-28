# h5 Gitar Hero

## Tiivistelmä

* Git on versionhallintajärjestelmä, joka tallentaa projektin tilan “kuvina” (snapshotteina), ei pelkkinä muutoksina
* Jokainen commit on ikään kuin kuva koko projektista sillä hetkellä
* Suurin osa toiminnoista toimii paikallisesti, joten Git on nopea ja toimii myös ilman nettiä
* Git varmistaa datan eheyden tarkistussummien (hashien) avulla
* Dataa ei yleensä poisteta, vaan uutta lisätään – joten vanhat versiot säilyvät
* Tiedostot kulkevat kolmessa tilassa: modified (muokattu), staged (valittu commitiin) ja committed (tallennettu pysyvästi)

Yhden lauseen supertiivistys:
Git on työkalu, joka tallentaa projektin eri versiot turvallisesti snapshotteina ja mahdollistaa muutosten hallinnan tehokkaasti, myös ilman verkkoyhteyttä.

* git add --all lisää kaikki muutokset staging-alueelle
* && suorittaa seuraavan komennon vain, jos edellinen onnistuu
* git commit tallentaa muutokset paikallisesti
* ; suorittaa seuraavan komennon riippumatta edellisen onnistumisesta
* git pull hakee ja yhdistää etärepositorion muutokset
* git push lähettää paikalliset commitit etärepositorioon
  
## a) Online

Tehtävässä loin uuden versionhallintavaraston GitHub‑palveluun. Varaston nimi ja lyhyt kuvaus sisältävät sanan sunshine. Varasto luotiin täysin uutena, eikä aiemmin tehtyä varastoa käytetty.
Varasto alustettiin luontivaiheessa lisäämällä siihen README.md tiedosto sekä GNU General Public License v3 lisenssi. Näin varastossa oli heti alusta alkaen sisältöä ja lisenssiehto. 
Lopuksi tarkistin GitHubin web‑käyttöliittymästä, että varasto näkyy oikein, README ja lisenssi ovat mukana ja että nimi sekä kuvaus täyttävät tehtävän vaatimukset.

<img width="1342" height="690" alt="image" src="https://github.com/user-attachments/assets/472bc627-c125-459d-a317-a6a28ebd779e" />

## b) Dolly

Aloitin tehtävän kopioimalla GitHubin web‑käyttöliittymästä repositorion osoitteen. Käytin repositorion SSH‑osoitetta, jotta GitHub‑autentikointi toimii SSH‑avainten avulla. Kloonaus tehtiin VirtualBox‑virtuaalikoneessa seuraavilla komennoilla: `git clone git@github.com:PyryKoskinen/sunshine-palvelinhallinta.git`

<img width="804" height="153" alt="image" src="https://github.com/user-attachments/assets/38b40e5b-c55a-492b-b611-8a288c85432a" />

Kloonauksen jälkeen siirryin repositorion hakemistoon: `cd sunshine-palvelinhallinta`

<img width="591" height="53" alt="image" src="https://github.com/user-attachments/assets/7b311b60-1ec4-4da9-b9f2-b9106ee3122a" />

Tarkistin että kloonaus onnistui ja että repon sisältö näkyi paikallisesti 

Seuraavaksi tein pienen muutoksen paikallisesti README.md‑tiedostoon. Muokkaus tehtiin tekstieditorilla: `nano README.md`

<img width="823" height="518" alt="image" src="https://github.com/user-attachments/assets/e488fd9b-d1f3-460e-b972-8b6f2b07a6ed" />

Lisäsin tiedostoon uuden rivin, joka kuvaa repositorion käyttötarkoitusta. Tämän jälkeen tallensin muutokset ja suljin editorin.

Ennen commitointia tarkistin Gitin tilan: `git status`

<img width="738" height="187" alt="image" src="https://github.com/user-attachments/assets/295295cc-18f9-4d85-8883-50f0498147b0" />

Git ilmoitti, että README.md‑tiedostoa oli muokattu. Tarkistin vielä tarkemmin, mitä muutoksia tiedostoon oli tehty: `git diff`

<img width="675" height="169" alt="image" src="https://github.com/user-attachments/assets/bcc6a6d3-0770-4c11-bf33-08f9c215e7b8" />

Kun olin varma muutoksista, lisäsin tiedoston versionhallintaan ja tein commitin:
```bash
git add README.md git 
commit -m "Update README for sunshine assignment"
```

Seuraavaksi puskin commitin etävarastoon GitHubiin. GitHub‑autentikointi oli toteutettu SSH‑avaimilla, joten pushaus ei vaatinut käyttäjätunnusta tai salasanaa: `git push`

Lopuksi palasin GitHubin web‑käyttöliittymään ja tarkistin repositorion sisällön. README.md‑tiedostossa näkyi tekemäni muutos, ja commit näkyi repositorion commit‑historiassa.

## d) Tukki

Aloitin tehtävän tarkastelemalla repositorion commit‑historiaa. Varmistin ensin, että olin oikeassa git repositoriossa (sunshine), ja ajoin seuraavan komennon: `git log`

<img width="784" height="357" alt="image" src="https://github.com/user-attachments/assets/6a33629b-45bc-459f-b4da-b3867c5f454d" />

Komento näytti repositorion koko historian aikajärjestyksessä. Lokista näkyivät commit tunniste (hash), tekijän nimi ja sähköposti, päivämäärä sekä commit‑viesti.

Seuraavaksi tarkastelin commit‑historiaa tiivistetyssä muodossa, jotta yleiskuva historiasta olisi helpompi hahmottaa: `git log --oneline`

<img width="783" height="95" alt="image" src="https://github.com/user-attachments/assets/4ffb490d-419f-4fe9-ab91-82017febce38" />

Tämä komento näytti jokaisesta commitista vain lyhennetyn tunnisteen ja commit‑viestin. Näkymä osoitti selkeästi repositorion peräkkäiset muutokset.

Tarkastelin vielä tarkemmin, mitä muutoksia viimeisimmässä commitissa oli tehty: `git show`

<img width="788" height="323" alt="image" src="https://github.com/user-attachments/assets/9504d311-0505-46e1-baab-3c98e43f234c" />

Komento näytti, mihin tiedostoihin commit vaikutti ja mitkä rivit oli lisätty tai muutettu. Tämä vahvisti, että muutokset koskivat README.md‑tiedostoa ja vastasivat edellisessä tehtävässä tehtyjä muokkauksia.

Seuraavaksi tarkistin, että commit‑historiassa näkyvät tekijätiedot olivat haluamallani tavalla. Käytin komentoa: `git log --pretty=full`

<img width="781" height="359" alt="image" src="https://github.com/user-attachments/assets/3f5f4470-d54f-4925-8b2c-36633daba1f2" />

Lokista näkyi selkeästi commitin tekijän nimi ja sähköpostiosoite. Lisäksi tarkistin Gitin nykyiset asetukset: 
``` bash 
git config user.name
git config user.email
```

<img width="783" height="80" alt="image" src="https://github.com/user-attachments/assets/5068d2a3-b517-494a-8193-eb398981cbf6" />

Sekä globaalit asetukset: 
``` bash 
git config --global user.name
git config --global user.email
```
<img width="787" height="109" alt="image" src="https://github.com/user-attachments/assets/4327db8b-32c7-42e4-8d8b-e13a6d60b1d2" />

Näiden perusteella varmistin, että nimi ja sähköpostiosoite olivat oikein määritettyjä tulevia committeja varten.

## e) Gitanbile

Aiemmin tehty Ansible‑kokoonpano sijaitsi eri käyttäjän (anteroo) kotihakemistossa. Kopioin Ansible projektin keskeiset tiedostot sunshinerepositorioon, joka sijaitsi käyttäjän pyryk kotihakemistossa. Kopioitavat tiedostot olivat Ansible projektin ydin: 
```bash 
cp /home/anteroo/ansible.cfg .
cp /home/anteroo/hosts.ini .
cp /home/anteroo/site.yml .
cp -r /home/anteroo/roles .
```
Koska tiedostot kopioitiin toiselta käyttäjältä, korjasin niiden omistajuuden: `sudo chown -R pyryk:pyryk ansible.cfg hosts.ini site.yml roles`

Tämän jälkeen tarkistin gitin tilan: `git status`

<img width="787" height="228" alt="image" src="https://github.com/user-attachments/assets/47a53575-8c7f-4fa0-8ba1-5c865f6e7671" />

Git tunnisti Ansible‑tiedostot uusina (untracked), mikä osoitti, että ne eivät vielä olleet versionhallinnassa.

Lisäsin Ansible‑tiedostot gitin hallintaan ja tein ensimmäisen commitin: 
``` bash 
git add ansible.cfg hosts.ini site.yml roles
git commit -m "Lisää Ansible configuraatio sunshine repositoryyn"
```

<img width="673" height="337" alt="image" src="https://github.com/user-attachments/assets/9fbbbe61-7983-4024-bbce-01cb4af2ecbf" />
<img width="780" height="254" alt="image" src="https://github.com/user-attachments/assets/91459c56-9d7b-4a01-afa2-ce6184840f43" />

Tämän commitin jälkeen Ansible‑kokoonpano oli osa sunshine‑repositiota.

Seuraavaksi tein pienen ja hallitun muutoksen Ansible‑rooliin. Muutin taskin nimeä tiedostossa: `roles/antero/tasks/main.yml`

<img width="804" height="605" alt="image" src="https://github.com/user-attachments/assets/082c5523-5aa0-4a56-b6b4-5503ff23644f" />

Muutos ei muuttanut tehtävän toiminnallisuutta. Tämän jälkeen ajoin Ansible‑playbookin paikallisesti:

`ansible-playbook site.yml --ask-become-pass`

<img width="794" height="103" alt="image" src="https://github.com/user-attachments/assets/2fa04c35-b366-4812-922e-94e7e4df0050" />

Playbook suorittui onnistuneesti, mikä osoitti, että muutos ei rikkonut automaatiota.

`git commit -m "Pieni päivitys ansible rooliin"`

<img width="784" height="66" alt="image" src="https://github.com/user-attachments/assets/318a0a19-0324-4446-99d3-bdb71e7057fe" />

Kun muutos oli testattu käytännössä, tallensin sen versionhallintaan: 

```bash
git add roles/antero/tasks/main.yml
git commit -m "Improve task naming in anteroo role"
```
Lopuksi puskin commitit GitHubiin: `git push`

<img width="574" height="188" alt="image" src="https://github.com/user-attachments/assets/30f80893-da12-426e-9922-5813dd9c2849" />

<img width="937" height="654" alt="image" src="https://github.com/user-attachments/assets/7470d889-205a-4c85-befa-58209e7e0b46" />

## f) Pari hankittu

## lähteet 

Chacon, S & Straub, B. 2014. Pro Git. 1.3 Getting Started - What is Git? Git. Luettavissa: https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F. Luettu: 28.4.2026.

Git. Reference. s.a. Luettavissa: https://git-scm.com/docs. Luettu: 28.4.2026.

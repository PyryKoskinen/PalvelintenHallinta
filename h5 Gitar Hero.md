# h5 Gitar Hero

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

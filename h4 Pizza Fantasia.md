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

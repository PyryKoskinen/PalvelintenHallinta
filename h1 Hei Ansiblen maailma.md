# h1 Hei Ansiblen maailma

a) Sshecrets. Asenna SSH-demonin asennus ja testaus
Tarkoituksena on asentaa OpenSSH‑server paketti komennoilla:sudo apt-get update ja sudo apt-get -y install ssh. 
SSH‑palvelun käynnistys ja automaattinen käynnistyminen hoituu komennolla: sudo systemctl enable --now ssh.
Tämän jälkeen tärkein vaihe eli yhteyden testaaminen. Testaus onnistuu komennolla: ssh user@osoite tai paikallisesti ssh localhost.
Tämä on minulle tuttu tapa varmistaa, että SSH toimii oikein.

b) Pubkey. Julkisen avaimen automatisoitu kirjautuminen
Tarkoituksena on ottaa käyttöön SSH‑kirjautuminen ilman salasanaa. Ensin luodaan avainpari komennolla ssh-keygen, jolloin järjestelmä tekee sekä yksityisen että julkisen avaimen. 
Oletusasetusten hyväksyminen riittää. Seuraavaksi julkinen avain kopioidaan kohdekoneelle komennolla ssh-copy-id localhost. Tämä lisää avaimen automaattisesti ~/.ssh/authorized_keys‑tiedostoon.
Tämän jälkeen kirjautuminen komennolla ssh localhost tapahtuu ilman salasanaa, mikä nopeuttaa työskentelyä ja helpottaa automatisointia.

c) Hei Ansible
Tarkoituksena on tehdä yksinkertainen “Hei maailma” Ansiblella SSH:n kautta. Tässä vaiheessa on hyvä huomata, että aiemmat askeleet b) ja c) helpottavat Ansiblen käyttöä merkittävästi. 
Ansible toimii SSH:n kautta, joten ennen sen käyttöä pitää varmistaa, että kirjautuminen onnistuu ja mieluiten ilman salasanaa. 
Ansible on “agentless”, eli kohdekoneelle ei tarvitse asentaa mitään erillistä demonia riittää että siellä on SSH ja Python.
Ansible asennetaan komennolla sudo apt-get install ansible. Sen jälkeen luodaan oma ansible hakemisto,
hosts.ini tiedosto. Tiedostoa voi ajatella “puhelinluettelona” Ansiblelle, se kertoo mitä koneita hallita.
Yhteys testataan komennolla ansible all -a 'uptime' -i hosts.ini. 
Lopuksi tehdään ensimmäinen rooli, joka luo tiedoston /tmp/hello-ansible ja ajetaan se playbookilla. Kun tiedosto ilmestyy koneelle, tiedetään että Ansible toimii onnistuneesti SSH:n yli.

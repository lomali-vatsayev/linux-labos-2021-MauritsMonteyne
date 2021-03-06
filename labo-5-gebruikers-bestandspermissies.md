# Labo 5: Bestandspermissies

Als je gebruik maakt van andere bronnen (bv. blog-artikel of HOWTO die je op het Internet vond), voeg die dan toe aan het einde van dit document. Zo kan je het later terug vinden.

Maak ter voorbereiding zeker de oefeningen in Linux Fundamentals (Paul Cobbaut) over dit onderwerp (pp. 222 en 228).


## Gebruikers en groepen aanmaken

Het doel van deze opgave is om de opdrachten en de begrippen met betrekking tot  gebruikers en groepen te bestuderen, binnen de context van Linux als een multi-user-systeem.

Tussen de vragen is ruimte voorzien om je antwoorden in te vullen. Het gaat telkens om zgn. codeblokken in Markdown, die starten en eindigen met drie backquotes. Binnen elk codeblok geef je telkens het commando dat je hebt ingetikt en de uitvoer die je krijgt.

1. Je hebt al een gebruiker aangemaakt voor jezelf. Log in als deze gebruiker. Geef hieronder telkens het commando en de uitvoer
    - Wat is het commando om de huidige directory op te vragen? Welke uitvoer toont het commando?

        ```
        $ pwd
        ```

    - Wat is het UID van deze gebruiker?

        ```
        $ id
        ```

    - Wat is het GID van deze gebruiker?

        ```
        $ id
        ```

2. Log in als de `root`-gebruiker met het commando `su -` (let op de spatie!)
    - Wat is de home-directory van `root`?

        ```
        $ pwd
        ```

    - Wat is het UID van deze gebruiker?

        ```
        $ id
        ```

    - Wat is het GID van deze gebruiker?

        ```
        $ id
        ```

3. Maak een nieuwe gebruiker aan met de naam `alice`, zonder specifieke opties
    - Geef het gebruikte commando:

        ```
        $ useradd alice
        ```

    - Voorzie een geschikt wachtwoord voor deze gebruiker (en vergeet het niet! Noteer het eventueel hier in je verslag of in de beschrijving van je VM)

        ```
        $ passwd alice
        Changing password for user alice.
        New password: <password>
        Retype new password: <password>
        passwd: all authentication tokens updated successfully.
        ```

4. Configuratiebestanden voor gebruikersbeheer:
    - In welk bestand kan je de UID, gebruikersnaam, homedirectory, enz. van alle gebruikers terugvinden?

        ```
        /etc/passwd
        ```

    - In welk configuratiebestand kan je al de bestaande gebruikersgroepen nakijken, en ook de gebruikers die lid zijn van elke groep?

        ```
        /etc/group
        ```

    - In welk configuratiebestand vind je de *wachtwoorden* van alle gebruikers?

        ```
        /etc/shadow
        ```

5. Gebruikersgroepen aanmaken
    - Maak een groep aan met de naam `sporten`

        ```
        $ groupadd sporten
        ```

    - In welk configuratiebestand vind je het GID van deze groep terug?

        ```
        /etc/group
        ```

    - Wat zal het GID zijn van de groepen `zwemmen` en `judo` als je deze nu onmiddellijk zou aanmaken? Maak ze aan en controleer!

        ```
        $ groupadd zwemmen
        $ groupadd judo
        ```
        **Antwoord:** 1003 en 1004

    - Voeg de gebruiker `alice` toe aan de groepen `sporten` en `zwemmen`

        ```
        $ usermod -aG sporten,zwemmen alice
        ```

    - Log in als `alice` door in een terminal het commando `su - alice` (let op de spaties!) uit te voeren

        ```
        $ su - alice
        ```

    - Zorg er nu voor dat de groep `sporten` de primaire groep wordt van `alice`.

        ```
        $ usermod -g sporten alice
        ```

    - Zorg er voor dat `alice` uitgelogd is, ga terug naar `root`

        ```
        $ exit
        $ su -
        ```

6. Maak nu de gebruikers in onderstaande tabel aan. Zorg er voor dat ze al meteen bij aanmaken tot de aangegeven groepen behoren. Kies zelf geschikte wachtwoorden voor deze gebruikers en vergeet ze niet (vul eventueel een kolom toe aan de tabel).

    | Gebruikersnaam | Primaire groep | Secundaire groep |
    | :---           | :---           | :---             |
    | `bob`          | `sporten`      | `judo`           |
    | `carol`        | `sporten`      | `zwemmen`        |
    | `daniel`       | `sporten`      | `judo`           |
    | `eva`          | `sporten`      | `zwemmen`        |

    - Geef de gebruikte commando's om de gebruikers aan te maken en ook om te verifiëren of dit correct gebeurd is:

        ```
        $ useradd bob -g sporten -G judo
        $ useradd carol -g sporten -G zwemmen
        $ useradd daniel -g sporten -G judo
        $ useradd eva -g sporten -G zwemmen
        ```

    - Verwijder nu de *groep* `alice` en controleer.

        ```
        $ groupdel alice
        ```

    - Gebruiker `daniel` gaat een tijdje niet meer sporten. Zorg er voor dat deze gebruiker tot nader order geen toegang meer kan hebben tot het systeem (zonder het wachtwoord of de gebruiker te verwijderen!).

        ```
        $ passwd -l daniel
        ```

    - Hoe kan je controleren dat `daniel` inderdaad geen toegang meer heeft tot het systeem? In welk bestand kan dat en hoe zie je daar dan dat het account afgesloten is?

        ```
        $ grep daniel /etc/shadow
        ```
        **Antwoord:** wachtwoord wordt vooraf gegaan door "!!" => wachtwoord locked

    - Gebruiker `daniel` komt terug naar de sportclub. Geef hem opnieuw toegang tot het systeem.

        ```
        $ passwd -u daniel 
        ```

    - Gebruiker `eva` stopt helemaal met sporten. Verwijder deze gebruiker, maar doe dit zorgvuldig: zorg er in het bijzonder voor dat ook haar homedirectory verwijderd wordt.

        ```
        $ userdel -r eva
        ```

7. Log aan als de gebruiker `carol`

    ```
    $ su - carol
    ```

    - Controleer of je in de “thuismap” bent van deze gebruiker. Maak onder deze map een bestand `test` aan door middel van het commando `touch`.

        ```
        $ pwd
        $ touch test
        ```

    - Probeer nu als gebruiker `carol` je te verplaatsen naar de “thuismap” van `alice`.

        ```
        $ cd /home/alice
        bash: cd: /home/alice/: Permission denied
        ```

    - Kan je de inhoud van de mappen binnen de thuismap van `alice` bekijken?

        ```
        $ ls /home/alice/
        ls: cannot open directory '/home/alice/': Permission denied
        ```
        **Antwoord:** Nee, geen toegang to Alice haar home directory

    - Probeer nu als `carol` onder de “thuismap” van `alice` ook een bestand `test` te maken. Lukt dit? Kan je dit verklaren?

        ```
        $ touch /home/alice/test
        touch: cannot touch '/home/alice/test': Permission denied
        ```
        **Antwoord:** Nee, geen toegang to Alice haar home directory

## Algemene permissies

Geef in de volgende oefeningen telkens het commando dat nodig is om de taak uit te voeren en ook het resultaat.

1. Log in als gewone gebruiker. Wat is de waarde van umask?

    ```
    $ umask
    0002
    ```

2. Maak een directory `oefenenMetPermissies` aan. Welke octale permissies verwacht je voor deze nieuwe directory?

    ```
    $ mkdir oefenenMetPermissies
    ```
    **Antwoord:** 775 = (111|111|101) = (rwx|rwx|r-x)

3. Controleer dit door de *symbolische* permissies op te vragen.

    ```
    $ stat -c '%A' oefenenMetPermissies/
    drwxrwxr-x
    ```

4. Controleer of de symbolische waarden en octale waarden overeen komen!

    ```
    $ stat -c '%a' oefenenMetPermissies/
    775
    ```

5. Stel nu de umask waarde zo in dat niemand permissies heeft op de bestanden en directories die je aanmaakt, behalve jijzelf.

    ```
    $ umask 077
    ```

6. Maak nu een bestand aan met de naam `vanmij`, in de directory `oefenenMetPermissies` met als inhoud de tekst: `echo "Waarschuwing: eigendom van ${USER}!"` Schrijf neer hoe je dit bestand gemaakt hebt. Controleer de permissies; schrijf ze neer en was dit wat je verwachtte? Verklaar!

    ```
    $ cd oefenenMetPermissies/
    $ echo 'echo "Waarschuwing: eigendom van ${USER}!"' > vanmij
    ```

7. Verander nu de permissies van het bestand `vanmij` zodat je zelf het bestand kan uitvoeren. Geef het gebruikte commando (op de octale manier) en test het resultaat.

    ```
    $ chmod 700 vanmij
    $ bash vanmij
    ```

8. Log in als de gebruiker `alice` (als je deze niet meer hebt, maak je die aan en voorzie de gebruiker van een eenvoudig paswoord), maar zorg dat je niet verandert van directory. Kan `alice` het bestand `vanmij` uitvoeren? Verklaar!

    ```
    $ su alice                                                  # zonder '-' !
    $ bash vanmij
    bash: vanmij: Permission denied
    ```
    **Antwoord:** Alice kan het bestand niet uitvoeren

9. Verander nu de permissies van het bestand vanmij zodat iedereen het bestand kan uitvoeren. Geef het gebruikte commando (op de symbolische manier) en test het resultaat.

    ```
    $ exit                                             # terug naar eigen account
    $ chmod go+x vanmij
    ```
    **Antwoord:** Alice kan het nog steeds niet uitvoeren omdat ze geen read permission heeft (?)

## Geavanceerde permissies

1. Zoek alle bestanden in het systeem waar de SUID-permissie op ingesteld staat. Schrijf het resultaat in een bestand met de naam `suidBestanden`. Schrijf de fouten weg naar een `foutenBestand`. Doe dit in één commandolijn. (Tip: gebruik het commando `find` en zoek in de manpages naar de geschikte opties).

    ```
    $ find / -perm /u=s > suidBestanden 2> foutenBestand
    ```

2. Controleer het resultaat door een bestand te nemen uit `suidBestanden` en de permissies op te vragen van dat bestand.

    ```
    $ ls -l /usr/bin/su
    rwsr-xr-x. 1 root root 75736 May 20  2020 /usr/bin/su
    ```

3. Check of het programmabestand `/usr/bin/passwd` in het bestand `suidBestanden` aanwezig is. Schrijf het gebruikte commando op. Is het aanwezig?

    ```
    $ grep "/usr/bin/passwd" suidBestanden
    ```
    **Antwoord:** Ja

## Geavanceerde permissies: setGID en de *sticky bit*

1. Ga naar de directory `oefenenMetPermissies`, als gewone gebruiker (dus niet als `root`) en kopieer het bestand `/etc/hosts` daar naartoe.

    ```
    $ cp /etc/hosts .
    ```

2. Verander nu de permissies van het bestand `hosts` in directory `oefenenMetPermissies` als volgt: SGID aan, `rx` voor *others*, `rw` voor *group* en geen permissies voor de eigenaar (merk op dat deze combinatie van permissies geen praktisch nut heeft!). Geef het gebruikte commando en ook het commando om te controleren of de permissies juist ingesteld zijn.

    ```
    $ chmod u-rw,g+rws,o+rx hosts
    $ stat -c '%A' hosts
    ----rwSr-x
    ```

3. Kan de eigenaar nu het bestand bekijken? Waarom wel of niet? Noteer hoe je dit controleert:

    ```
    $ cat hosts 
    cat: hosts: Permission denied
    ```

4. Kan de eigenaar de permissies wijzigen? Controleer.

    ```
    $ chmod u+rwx hosts
    ```
    **Antwoord:** Ja

5. Kan de eigenaar het bestand verwijderen? Controleer.

    ```
    $ rm hosts
    ```
    **Antwoord:** Ja

Kopieer als je eigen gebruiker (niet als root!) nu opnieuw het bestand `/etc/hosts` in het directory `oefenenMetPermissies` en pas de permissies opnieuw aan zoals eerder voorgeschreven. Zorg dat gebruiker `alice` lid is van de groep die groepseigenaar is van het bestand `hosts` in directory `oefenenMetPermissies`. Switch nu naar gebruiker alice.

1. Kan alice nu het bestand bekijken? Controleer.

    ```
    $ su alice
    $ cat hosts
    ```
    **Antwoord:** Ja

2. Kan alice de permissies wijzigen? Controleer.

    ```
    $ chmod u+r hosts
    chmod: changing permissions of 'hosts': Operation not permitted
    ```

3. Kan alice het bestand verwijderen? Is dit de bedoeling? Controleer.

    ```
    $ rm hosts
    ```
    **Antwoord:** Ja, het zou niet mogen aangezien Alice geen read en execute permissies heeft op de parent directory. Dit wordt omzeild door in te loggen in de directory zelf (`su alice`) 

4. Stel nu de sticky-bit in op het directory oefenenMetPermissies. Geef het geschikte commando en controleer het resultaat.

    ```
    $ chmod +t oefenenMetPermissies/
    ```

## Eigenaars en groepseigenaars veranderen

1. Maak als `root` onder `/srv/` twee directories aan met de naam `groep/verkoop` en `groep/inkoop`. Maak ook 2 groepen aan met de namen `verkoop` en `inkoop`. Maak twee gebruikers aan, `margriet` met primaire groep `verkoop` en `roza`, die als primaire groep `inkoop` heeft. Zorg dat de groepen eigenaar zijn van de overeenkomstige directories en dat `margriet` eigenaar is van directory `verkoop` en `roza` van het directory `inkoop`. Geef de gebruikte commando’s en controleer:

    ```
    $ mkdir -p group/{verkoop,inkoop}
    ```
    ```
    $ groupadd verkoop
    $ groupadd inkoop
    ```
    ```
    $ useradd -g verkoop margriet
    $ useradd -g inkoop roza
    ```
    ```
    $ chown -R margriet:verkoop group/verkoop               # Recursie optioneel
    $ chown -R roza:inkoop group/inkoop
    ```
    ```
    # ls -l groep/
    total 0
    drwxr-xr-x. 2 roza     inkoop  6 Sep 22 22:23 inkoop
    drwxr-xr-x. 2 margriet verkoop 6 Sep 22 22:23 verkoop
    ```

2. Zorg ervoor dat gebruikers en groepen uit de vorige stap alle permissies hebben. Geef het geschikte commando en controleer.

    ```
    $ chmod ug+rwx inkoop
    $ chmod ug+rwx verkoop
    ```

3. Voeg een gebruiker, vb `alice`, toe aan de groep `inkoop` en `verkoop` en controleer. Geen van beide groepen zijn primair.

    ```
    $ usermod -aG inkoop alice
    $ usermod -aG verkoop alice
    ```
    ```
    $ groups alice
    alice : sporten inkoop zwemmen verkoop
    ```

4. Log in als `alice` en ga naar de directory verkoop. Laat de gebruiker hier een leeg bestand, `bestand1`, aanmaken in de directory verkoop. (Indien je hier problemen ondervindt, log dan in via een andere terminalvenster).

    ```
    $ touch bestand1
    ```

5. Wie is nu eigenaar van `bestand1` en wie de groepseigenaar?

    ```
    $ ls -l
    ```
    **Antwoord:** Alice

6. Zorg er nu voor dat de groepseigenaar van de directory `verkoop` automatisch de groepseigenaar wordt van alle bestanden en directories die onder `verkoop` gemaakt worden. Geef de gebruikte commando’s.

    ```
    $ chmod g+s verkoop/
    ```

7. Doe hetzelfde voor de directory `inkoop`. Geef de gebruikte commando’s:

    ```
    $ chmod g+s inkoop/
    ```

8. Verander opnieuw naar gebruiker `alice` en laat deze gebruiker een leeg `bestand2` aanmaken in de directory `verkoop`. Geef de gebruikte commando’s:

    ```
    $ touch bestand2
    ```

9. Wie is nu eigenaar van `bestand2` en wie groepseigenaar?

    ```
    $ ls -l
    ```
    **Antwoord:** verkoop 

10. Laat nu gebruiker `margriet` een leeg bestand `bestand3` aanmaken. Controleer de eigenaar van `bestand3` en de groepseigenaar.

    ```
    $ touch bestand3
    $ ls -l
    ```

11. Laat nu gebruiker `alice` `bestand3` verwijderen. Lukt dit?

    ```
    $ rm bestand3
    ```
    **Antwoord:** Alice hoort bij verkoop en verkoop heeft alle permissies voor die directory

12. Zorg er nu voor dat de gebruikers elkaars bestanden niet kunnen verwijderen. Als de gebruiker echter eigenaar is van het betreffende directory mag dit wel. Leg uit hoe je dit doet en controleer. Schrijf je gevolgde procedure op.

    ```
    $ chmod +t verkoop/
    ```
    **Antwoord:** Sticky bit zetten op een directory zorgt dat gebruikers elkaar bestanden kunnen lezen en uitvoeren, maar enkel hun eigen bestanden kunnen verwijderen

## Gebruikte bronnen

# Handleiding: BMS uitlezen in Home Assistant met een LilyGO T-CAN485 via de RS485 poort

Het is mogelijk om de BMS van elke individuele accu uit te lezen in Home Assistant. Hiervoor heb je een ESP32 nodig die wordt aangesloten op de RS485-poort van de ‘Master’-accu.

Voor de LilyGO T-CAN485 is inmiddels, dankzij @fenrir en @paQ , een kant-en-klare oplossing beschikbaar. Deze vormt de basis voor deze handleiding. Het resultaat ziet er als volgt uit:

<img width="1193" height="1177" alt="image" src="https://github.com/user-attachments/assets/da40cebd-7fa4-49e8-9855-ff421a3db5ef" />

## Vereisten
- Een Home Assistant-installatie met HACS. Deze handleiding gaat uit van HA OS. Bij Home Assistant Container zijn extra stappen nodig; die vallen buiten deze handleiding.
- LilyGO T-CAN485. Deze is verkrijgbaar via AliExpress, maar ook voor een schappelijke prijs bij een Nederlandse webshop:
https://www.tinytronics.n...pment-board-can-bus-rs485
- Ethernetkabel die kapot geknipt mag worden, bij voorkeur met vaste kern/solid copper.
- USB-adapter en USB-C-kabel voor de voeding van de LilyGO.
- USB-kabel die data ondersteunt, dus niet alleen laden.

## Stap 1: Voorbereiding
1. Verbind de LilyGO T-CAN485 met een USB-kabel met je computer. Let erop dat de kabel ook data ondersteunt en niet alleen stroom.
2. Gebruik Google Chrome of Microsoft Edge. Deze browsers ondersteunen WebUSB, wat nodig is om direct vanuit de browser te flashen. Gebruik dus geen Firefox.
3. Ga in Home Assistant naar Instellingen --> Apps en installeer de app ESPHome Device Builder.
4. Ga in Home Assistant naar HACS en installeer Mushroom en apexcharts-card.

## Stap 2: De configuratie downloaden en klaarzetten
1. Ga naar de GitHub-pagina van het project en download het bestand `LilyGo_TCAN485.yaml`:
https://github.com/www0xx.../main/LilyGo_TCAN485.yaml

2. Ga in Home Assistant naar ESPHome Builder en klik rechtsboven op Secrets.

3. Pas onderstaande tekst aan, plak deze in de Secrets-editor en klik daarna op Save:


```
wifi_ssid: "JOUW_WIFI"
wifi_password: "JOUW_WIFI_WACHTWOORD"
api_encryption_key: "32-byte Base64-sleutel"
ota_password: "VERZIN_IETS_LEUKS"
```

Je kunt een willekeurige 32-byte Base64-sleutel genereren met het commando:

```
openssl rand -base64 32
```

Dit kan bijvoorbeeld via deze tool:
https://www.cryptool.org/en/cto/openssl/

## Stap 3: Configuratie flashen
1. Ga in Home Assistant naar ESPHome Builder en klik rechtsonder op New Device.
2. Klik op Import from file en upload het bestand `LilyGo_TCAN485.yaml`.
3. Kies daarna voor de optie Plug into this computer.
4. Selecteer in het pop-upmenu linksboven de juiste COM-/serialpoort. Vaak is er maar één beschikbaar; in dat geval is de standaardkeuze meestal de juiste.
5. Klik op Install. Dit duurt ongeveer 2 tot 5 minuten, omdat er nog verschillende packages gedownload moeten worden.

Tip: tijdens het flashen zie je een gekleurde LED branden. Nadat het flashen klaar is, gaat de LED uit en blijft deze uit. De LED wordt in de huidige firmware niet gebruikt.

## Stap 4: LilyGO toevoegen aan Home Assistant
1. Ga in Home Assistant naar Instellingen --> Integraties. Als het goed is, staat daar nu een ontdekte ESPHome-integratie met de naam pace_bms. Voeg deze toe met de standaardnaam.
2. Ga in Home Assistant naar Instellingen --> Dashboards en maak een nieuw dashboard aan. Kies voor Leeg nieuw dashboard.
3. Ga naar het zojuist aangemaakte lege dashboard. Klik rechtsboven op het potloodje, daarna op de drie puntjes en kies Ruwe configuratie-editor.
4. Plak hierin de inhoud van dit bestand en sla het dashboard op:
https://github.com/www0xx/balans-pace/blob/main/LilyGo_TCAN485.yaml

## Stap 5: LilyGO aansluiten op de ‘Master’-accu
1. Pak een ethernetkabel en knip aan één kant de stekker eraf.
2. Strip de kabel. Zorg dat je het oranje aderpaar, dus oranje en oranje-wit, én groen-wit overhoudt. De overige aders mag je afknippen.
3. Maak deze drie aders blank en schroef ze in het connectorblok van de LilyGO op de RS485-poort. Let op: gebruik dus niet de CAN-poort. Sluit ze aan in deze volgorde:

<img width="1568" height="502" alt="image" src="https://github.com/user-attachments/assets/6977c9e6-fba2-4235-b2dd-e768ce2bab41" />

4. Sluit de ethernetstekker aan de andere kant van de kabel aan op de RS485-poort van de Master-accu van je Balans Batterij.

De Master-accu is te herkennen aan de gele netwerkkabel die naar de Deye gaat. Dit is vaak de bovenste accu van de stack:

<img width="880" height="1269" alt="image" src="https://github.com/user-attachments/assets/9ae0d6b7-4cc7-402a-9ea7-9f7393b71e5b" />

Plug de kabel direct naast de al aangesloten gele ethernetkabel in:
<img width="1048" height="571" alt="image" src="https://github.com/user-attachments/assets/b3f8ef42-9e83-4948-b1e4-b651bf46e846" />

5. Voorzie de LilyGO nu van stroom via de USB-C-poort. Na een paar seconden zou de LilyGO verbinding moeten maken met je wifi-netwerk.

De data zou nu binnen moeten komen op zowel de interne webserver van de LilyGO als in het dashboard dat je zojuist hebt aangemaakt. De interne webserver is bereikbaar via http://pace_bms.local/

# Troubleshooting

## Ik mis data van één of meerdere accu’s

Bovenstaand package is ontwikkeld voor de standaard stack van 3 accu’s van Balans Energie en gaat ervan uit dat de dipswitches op de accu’s zelf in oplopende volgorde zijn ingesteld.

Heb je meer accu’s dan de standaard stack van 3, of staan je dipswitches anders, dan moet je dit aanpassen in de code. Dit doe je als volgt:

1. Lees de stand van de dipswitches af op de accu. Deze zitten aan de achterzijde:
<img width="965" height="530" alt="image" src="https://github.com/user-attachments/assets/08404d1a-9a52-409a-85e6-3bc15d8048b4" />

2. De dipswitches werken binair, volgens onderstaand schema:
<img width="735" height="596" alt="image" src="https://github.com/user-attachments/assets/6ef22011-4ad2-4034-8acc-8dd82a8c92e9" />


3. Ga in Home Assistant naar ESPHome Builder en klik op Edit bij pace_bms.
4. Controleer vanaf regel 81 het `address` van elke accu. Deze adressen zijn ‘off-by-one’. Staat je dipswitch binair gezien bijvoorbeeld op 4, dan is het `address` in de YAML 5.
5. Heb je meer dan 3 accu’s, voeg deze dan toe volgens dezelfde naming convention. Kopieer/plak hiervoor de bestaande code en pas de `id` en het `address` aan.
6. Klik daarna op Save en vervolgens op Install.
7. Indien je meer dan 3 accu's hebt, zul je ook de YAML van het Dashboard op dezelfde wijze moeten aanpassen. Ik heb er helaas maar 3, dus ik kan je niet exact vertellen hoe dat moet, maar het komt neer op wederom wat kopieer en plak werk met aanpassing van de IDs ;)

## Home Assistant kan geen sensoren/entiteiten vinden in het dashboard

Controleer eerst of je data ziet binnenkomen op de interne webserver van de LilyGO: http://pace_bms.local/

Als daar wel data binnenkomt, heb je waarschijnlijk de naam van pace_bms aangepast bij het toevoegen van de ESPHome-integratie aan Home Assistant, zie stap 4.

Het dashboard gebruikt pace_bms als vaste naam. Je kunt dit op twee manieren oplossen:

1. Doe in de YAML van het dashboard een find/replace waarbij je `pace_bms` vervangt door de naam die jij hebt gekozen.
2. Verwijder de ESPHome-integratie uit Home Assistant en voeg deze opnieuw toe met de standaardnaam pace_bms.

# Credits
[Het balans energie ervaring topic op GoT](https://gathering.tweakers.net/forum/list_messages/2306694)

en met name:
[PaQ](https://tweakers.net/gallery/33665/),
[Speedy-Andre](https://tweakers.net/gallery/50867/),
[Wolly](https://tweakers.net/gallery/19172/),
[Rick Astley](https://tweakers.net/gallery/298667/) &
[fenrir](https://tweakers.net/gallery/45748/)


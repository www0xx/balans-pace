Uitlezen multistack Pace ex BMS van Balans Energie via ESP32 op RS485 poort van de master BMS.
RS485 protocol van de stack dient op PYLON te staan.

Installatie van de firmware via ESPhome. 
Voor gebruik van een LilyGo Tcan485 kan je direct gebruik maken van de yaml file "LilyGo_TCAN485.yaml" 

Aansluiten van de modbus kabel is als volgt: 
![modbud kabel](https://tweakers.net/i/17zXgunYMo-IamWqczEtk-f2Uoc=/fit-in/4920x3264/filters:max_bytes(3145728):no_upscale():strip_icc():strip_exif()/f/image/46l4K53Jw3f4McbzbSARJdL8.jpg)
Sluit de andere kant van de netwerrk kabel aan op de RS485 poort aan de achterzijde van je hoofd accu (die vanwaar er een netwerkkabel naar de Deye gaat).

Voor het LilyGo bordje staat de interne webserver aan, hierdoor kan je zien of er daadwerklijk data binnenkomt. Als dit niet zo is, dan moet je of je kabel controleren, danwel de pinout in de yaml aanpassen (is voor de LilyGo Tcan485 niet van toepassing).

Hierna kan je het dashboard toevoegen in Home Assistant. Mocht je de naam van de esp hebben aangepast in de .yaml dan zal je deze aanpassing ook in het HA dachboard moeten doen.

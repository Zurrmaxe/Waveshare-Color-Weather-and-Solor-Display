# Waveshare-Color-Weather-and-Solor-Display
Waveshare ESP32S3 Touch 7 Display for Weather and Solar

Hier mein Color-Wetter-Display mit Anzeige von Solardaten.

https://github.com/Zurrmaxe/Waveshare-Color-Weather-and-Solor-Display/blob/main/main.jpg?raw=true
<img width="1101" height="498" alt="Image" src="https://github.com/Zurrmaxe/Waveshare-Color-Weather-and-Solor-Display/blob/main/main.jpg?raw=true" />

Achtung ! Achtung ! Ganz wichtig !

Es ist keine fertige Plug-and-Play Software ! 

Nur eine Grundlage für eigene Entwichlungen und Anpassungen ! 

Der Code muss an die jeweilige Hardware vor Ort angepasst werden. 
Zu anderer Hardware, die ich nicht kenne, kann ich auch nicht weiterhelfen.

Der Code läuft prinzipiell auch auf dem Suntour 7-Zoll Display. Es müssen nur die Display-Dateien angepasst  werden.

Zur Inbetriebnahme sind gute Programmier-Erfahrungen und Grundkenntnisse im Umgang mit der Arduino IDE, den Libarys und der Display-Hardware unbedingt erforderlich !

Der Code ist definitiv nichts für Anfänger und Neueinsteiger, da hier teilweise umfangreiche Änderungen an der Hardware, dem Code und ggf. Anpassung an den Libarys erforderlich sind.


Der geniale Wettercode des Displays stammt nicht von mir, sondern  kommt, von diesem Entwickler:
https://github.com/G6EJD/ESP32-e-Paper-Weather-Display/tree/master

All Credits go to him 

Hier gibt’s  auch weitere Infos zum Wetterdisplay und auch zusätzliche Sprachdateien.

Ich habe den Code lediglich für das Farbdisplay umgeschrieben und um weitere Sensoren und Hardware erweitert.

Mein Color-Wetter Display mit Abfragen von Wechselrichten und Stromzählern ist noch in der Entwicklung und hat dementsprechend noch einige Fehler, welche gelegentlich zu einem Absturz führen. 

Insbesondere dann , wenn die Sensoren nicht richtig erkannt wurden. Gutes WLAN ist daher sehr wichtig. Alle Sensoren müssen im gleichen WLAN-Netz sein und immer die selbe WLAN-Adresse haben.

Das Display ist ein Waveshare ESP32S3 Touch 7 Display :
https://www.waveshare.com/esp32-s3-touch-lcd-7.htm
Dazu gibt es ein Wiki:
https://www.waveshare.com/wiki/ESP32-S3-Touch-LCD-7

Im Unterschied zu Suntours-_Displays hat es  keine „freien“ GPIO-Ports.  Alles wird durch eine Port-Expander geregelt. Daher läuft auch nicht jede Libary auf dem Board fehlerfrei. 

Wire.h macht Probleme weil es den Port-Expander umgeht und führt ggf. zu Störungen. Ebenso Libarys, welche direkt die GPIOs Hardware ansprechen. Try and ErrorPinzip.

Ich habe zwei verschiedene Versionen des Displays. Diese Board haben eine leicht geränderte Hardware. Insbesondere der Touch-Screen ist bei der Version 1.1 um 180 Grad gedreht !!

Daher kann es sein, dass die eigentlichen (unsichtbaren) Touchbuttons nicht mehr unten, sondern um 180 Grad gedreht und oben auf dem Display sind ! 

Falls also unten nichts passiert, mal oben drücken !

Dann muss  Datei wave.h anpepasst werden :

cfg.offset_rotation = 2;  // bei Version 1.1 
cfg.offset_rotation = 0;  // bei Version 1.2 

Ebenso muss die Helligkeitssteuerung des Display durch Einbau einer Drahtbrücke angepasst werden. Hierzu folgende Anleitung:
https://community.home-assistant.io/t/esp32-s3-7inch-capacitive-touch-display-adjust-brightness/771030
<img width="1101" height="498" alt="Image" src="https://github.com/user-attachments/assets/e6bba7ae-b194-457a-9680-aa4d82769302" />

Oder man lässt die Helligkeitsregelung weg indem man den Code anpasst. 

Diese Änderung dient auch nur dazu, dass man denn Code für die Helligkeitsregelung für das Waveshare und Suntor -Display übernehmen kann.

In den Dateien  wave.h oder sunton.h stehen die verwendeten Ports für Beleuchtung und I2C Bus.

Hardware:

Außerdem wird für die Abfrage meiner Hoymiles-Wechselrichter eine OpenDTU benötigt:
https://github.com/hoylabs/OpenDTU-OnBattery

Für die Abfrage des Stromzählers wird eine SHELLY EM und einen ISKRA-Sensor ( je nach Zähler benötigt:
https://wiki.volkszaehler.org/hardware/channels/meters/power/edl-ehz/iskraemeco_mt175

Für die Wetterdaten braucht ihr ein kostenlose Zugriff bei OpenWeatherMap  2.5 :
https://openweathermap.org/appid

Dort bei kommt ihr den Wetter-API-Key

In der Datei → owm_credentials.h  müssen dieser API-Key sowie die WiFi-Daten und Wlan- Adressen der verwendeten Sensoren eingetragen werden. Ebenso müssen auch dort die Standortdaten und Zeitzonen angepasst werden.

Wetterabfragen bleiben kostenlos, wenn eine bestimmte Anzahl pro Tag nicht überschritten wird. Daher wird nur alle 30 Minuten das Wetter aktualisiert.

Für die Abfrage der Innentemperatur benutze ich einen BME 280 per I2C-Bus. 

Die Messung der Außentemperatur erfolgt über einen ESP32 mit BME280 .Diese Daten werden per JSON (HTTP/GET) übertragen. Dieser Außensensor macht bei mir manchmal Probleme und führte zu ungewolltem Systemneustart und ist ggf. auszukommentieren.

Je nach verwendeter Hardware sind die entsprechenden Codes den jeweiligen Sensoren (Shelly, IskraM175, OpenDtu,  Hoymiles pp. anzupassen.

Der läuft im Multitasking mit zwei Kernen. Der Core = ist für den WatchDog und Timer zuständig, während im Core 1 das eigentliche Programm läuft. 

Core 0 bitte nicht mit zusätzlichem Code überlasten, da dort auch WLAN läuft !

Das Programm macht etwa alle 30 Sekunden eine Aktualisierung der Wetterdaten per Reset. Und ca. alle 15 Minuten ein Reset der Loop um die Anzeigen zu aktualisieren.

Nach etwa 30 Sekunden werden die Touch-Buttons wieder zurückgesetzt.


Zwischen 23.00 Uhr  und 06.00 Uhr wird das Display per Timer abgeschaltet.

Wenn keine Solarleistung vorhanden ist, wir die Mondphase angezeigt.

Alle Serial.print() habe ich deaktiviert. Zur Fehlersuche können sie wieder aktiviert werden.

Verwendet wird die Arduino in der Version IDE 2.3.6. Es sind naturgemäß noch einige Libarys hinzuzufügen und bzw, zu aktualisieren.

Ich habe bewusst den Code sehr sehr einfach geschrieben und gehalten.
 
Man könnte ihn verbessern und  Zeilen zu „Funktionsblöcken“ zusammenschreiben.

Dann verliert man aber als Nicht-Profi schnell den Überblick.

Insbesondere die Abfrage der Hoymiles-Inverter und Sensoren mache ihn prinzipiell immer nach dem gleichen Prinzip auf Basis von ArduinoJson  und „http/get“. 
Hierzu gibt es im Netz genug Anleitungen und den Code dieses Displays an seiner Hardware anpassen zu können.

Da jede Hardware anders ist und ich diese Hardware auch nicht kenne, kann ich dafürauch keine Hilfe anbieten. Dazu müsst ihr euch selber im Netz informieren, wie euere Hardware benutzt werden kann.

Voraussetzung bei mir ist eine OpenDTU mit Hoymiles Wechselrichter. 
Habe ihr etwas ganz anderes, z.B. AHOY-DTU oder Growatt müsst ihr den Code selbst umschreiben. Von AHOY und Growatt habe ich keine Ahnung.


Bekannte Fehler sind:

Die Touch-Tastenabfrage reagiert  noch etwas Zeitverzögert und bedarf noch einer Überarbeitung
Es sind natürlich noch in den Grafiken kleine Pixelfehler vorhanden.
Der Code stopt manchmal und der WatchDog resetet dann das Display. 
Die genaue Ursache habe ich noch nicht gefunden. 
Hat Vermutlich etwas mit der Abfrage meines Außensensors zu tun.


Code und Bilder folgen Morgen.....


  







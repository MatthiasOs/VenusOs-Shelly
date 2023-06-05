# VenusOS Installation auf einem Raspberry PI 4
## VenusOS installieren
- VenusOS Image herunterladen: https://updates.victronenergy.com/feeds/venus/release/images/raspberrypi4/
- auf eine SD Karte flashen (zB mit `balenaEtcher` oder dem `Raspberry Pi Imager`
- SSH im VenusOS aktivieren (direkt auch gleich `Remote Zugriff` aktivieren)
- admin/superuser PW erstellt ([Victron Anleitung "Root access"](https://www.victronenergy.com/live/ccgx:root_access))

## ShellyPro3EM Integration
- [Anleitung & Skript](https://github.com/fabian-lauer/dbus-shelly-3em-smartmeter/), dass die Daten vom VenusOS aus dem ShellyPro3EM abgefragt werden
  - siehe Diskussion im [Forum](https://community.victronenergy.com/questions/125793/shelly-3em-smartmeter-with-venusos-cerbo-gx.html)
  - funktioniert aber **NUR** für den Shelly 3EM und **NICHT für die Pro Variante!!!**
  - jemand hat einen [Fix](https://github.com/funkmaster86/dbus-shelly-pro-3em-smartmeter) für die **Pro Variante** gemacht
  - aber [Issue #1](https://github.com/funkmaster86/dbus-shelly-pro-3em-smartmeter/issues/1) beachten, es war noch ein Fehler im run Skript

&rarr;  Der ShellyPro3EM taucht nun in der `Device List` in der `Remote Console` auf
![ShellyPro3EM in Device List der Remote Console](https://github.com/CommentSectionScientist/VenusOs/blob/main/RemoteConsole.png?raw=true)

## Useability
### Automatisch Skript starten
- Nach einem Neustart lief das Skript im VenusOS welches die Daten vom ShellyPro3EM abfrägt nicht automatisch los
- mittels `crontab` automatisch bei Neustart das install.sh Skript ausführen (eigentlich müsste man nur den Service starten, aber kA wie das geht, install.sh macht das auch)
- `crontab -e` &rarr; Zeile `@reboot sh /data/dbus-shelly-pro-3em-smartmeter/install.sh` einfügen und speichern (SRTG+C &rarr; `:wq` &rarr; ENTER)
### Zugriff von VRM (Internet) auf das VenusOS im Netzwerk ermöglichen
- in der `Remote Console`: `Settings` &rarr; `VRM online portal` &rarr; `VRM Portal ID` notieren
- in [VRM Online](https://vrm.victronenergy.com) einloggen und neue Installation hinzufügen
  - `Color Control GX` auswählen und die `VRM Portal ID` eingeben (In der `Remote Console` muss vorher `Remote Zugriff` aktiviert sein, siehe oben)

![VRM Protal Übersicht der Anlage](https://github.com/CommentSectionScientist/VenusOs/blob/main/VRM.png?raw=true)

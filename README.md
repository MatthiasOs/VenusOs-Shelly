# VenusOS Installation auf einem Raspberry PI 4
## VenusOS installieren
- VenusOS Image herunterladen: [raspberrypi4](https://updates.victronenergy.com/feeds/venus/release/images/raspberrypi4/)
- auf eine SD Karte flashen (zB mit `balenaEtcher` oder dem `Raspberry Pi Imager`)
- SSH im VenusOS aktivieren (direkt auch gleich `Remote Zugriff` aktivieren)
- admin/superuser PW erstellt [Victron Anleitung "Root access"](https://www.victronenergy.com/live/ccgx:root_access)
- MQTT Treiber installieren: [freakent/dbus-mqtt-devices](https://github.com/freakent/dbus-mqtt-devices)
  - ggf im [Log kontrollieren](https://github.com/freakent/dbus-mqtt-devices/tree/main#troubleshooting) ob der Treiber korrekt läuft
- in VenusOS MQTT aktivieren: `Settings > Services > MQTT`
## PV Inverter: Shelly Plus 1PM Integration (via Node Red)
- auf dem Shelly MQTT aktivieren: `Settings > MQTT`
- Shelly als PV-Inverter registrieren: ![Flow](https://github.com/CommentSectionScientist/VenusOs/blob/main/SetupShellyPvInverter.json) (DeviceInstance muss ggf angepasst werden)
- Daten von Shelly holen und Daten nach VenusOS per MQTT schreiben: ![Flow](https://github.com/CommentSectionScientist/VenusOs/blob/main/DataShellyPvInverter.json)
- benötigte Nodes:
  - node-red (mqtt)
  - node-red-contrib-shelly (könnte man theoretisch auch mit plain mqtt von node-red machen)
## Grid Meter: Shelly Pro 3EM Integration (via Node Red)
- auf dem Shelly MQTT aktivieren: `Settings > MQTT`
- _Eventuell nicht nötig:_ Role in MQTT Treiber aktivieren (services.xml)
  - per SSH auf VenusOs: `nano /data/drivers/dbus-mqtt-devices-0.6.3/services.yml`
  - Rolle bei Grid hinzufügen:
        `Role:
          description: "grid, pvinverter, genset, acload"
          persist: true
          default: "grid"`
- Falls die DeviceInstance (zB 42) nicht im DBUS bekannt ist, kann man sie manuell anlegen
  - per SSH auf VenusOS: `dbus -y com.victronenergy.settings /Settings/Devices GetValue` oder über `dbus-spy` suchen
  - falls es keinen Eintrag mit ClassAndVrmInstance zu dem Device gibt, muss dieser angelegt werden:
    - `dbus -y com.victronenergy.settings /Settings/Devices AddSetting mqtt_fe004_grid ClassAndVrmInstance grid:42 s "" ""`
    - bei einem erneuter Aufruf von GetValue (s.u.) sieht man dann ``'mqtt_fe004_grid/ClassAndVrmInstance': 'grid:42',``
- Reboot
- Shelly als Grid Meter registrieren: ![Flow](https://github.com/CommentSectionScientist/VenusOs/blob/main/SetupShellyGridMeter.json) (DeviceInstance muss ggf angepasst werden)
- Daten von Shelly holen und Daten nach VenusOS per MQTT schreiben: ![Flow](https://github.com/CommentSectionScientist/VenusOs/blob/main/DataShellyGridMeter.json)
- benötigte Nodes:
  - node-red (mqtt)
  - node-red-contrib-shelly (könnte man theoretisch auch mit plain mqtt von node-red machen)
## Dashboard
![VRM Protal mit Speicher](https://github.com/CommentSectionScientist/VenusOs/blob/main/VRM_mit_Speicher.png)
 
## OpenWb Ladenstation Integration per Node Red ([Quelle](https://openwb.de/forum/viewtopic.php?p=85030&sid=4fa25e6eacd715ca001b10b43cc97e54#p85030))
TODO
- in der services.yml muss ein Eintrag für den [EV-Charger](https://openwb.de/forum/viewtopic.php?p=85205#p85205) **hinzugefügt** werden, damit der Eintrag im DBUS angelegt werden kann:
  - `nano /data/drivers/dbus-mqtt-devices-0.6.3/services.yml`: ![services_evcharger.yml](https://github.com/CommentSectionScientist/VenusOs/blob/main/services_evcharger.yml)
- Reboot

## Fehlerpotential
- Die Werte des PV-Inverters und Grid Meters müssen als Zahl(!) und dürfen nicht formatiert als String übermittelt werden, sonst werden die Zahlen von VRM nicht angenommen und das Gerät nicht erkannt (in der Remote Console hingegen wird alles korrekt anzeigt)

## Useability
### Zugriff von VRM (Internet) auf das VenusOS im Netzwerk ermöglichen
- in der `Remote Console`: `Settings > VRM online portal > VRM Portal ID` notieren
- in [VRM Online](https://vrm.victronenergy.com) einloggen und neue Installation hinzufügen
  - `Color Control GX` auswählen und die `VRM Portal ID` eingeben (In der `Remote Console` muss vorher `Remote Zugriff` aktiviert sein, siehe oben)



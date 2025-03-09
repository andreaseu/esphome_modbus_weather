# ESPHome - Elsner Elektronik Wetterstation (P03/3) Modbus Converter Projekt

Dieses Projekt implementiert eine Wetterstation mit ESPHome, die über Modbus mit einem Wettersensor von Elsner Elektronik (P03/3) GPS kommuniziert und die Daten in Home Assistant integriert.

## Überblick

Die Wetterstation erfasst folgende Wetterdaten:
- Temperatur
- Helligkeit (Süd, West, Ost)
- Licht
- Windgeschwindigkeit
- Regen
- Sonnenposition (Azimut und Elevation)
- GPS-Koordinaten (Wenn GPS Variante)
- Datum und Uhrzeit UTC (Wenn GPS Variante)

## Hardware

- ESP8266 (ESP-01 mit 1MB Flash)
- Elsner Elektronik P03/3 Wetterstation
- EI-OT Tasmota RS485 ESP-01 WLAN WiFi Bridge Modul
- LM2596 DC-DC Step-Down Spannungsregler (2-3A)

## Elektrische Schaltung

Die elektrische Versorgung des Systems wird wie folgt realisiert:

1. Ein 24V Netzteil dient als primäre Stromquelle
2. Der LM2596 Step-Down Converter wandelt die 24V in 12V um, um den ESP-01 zu versorgen
3. Parallel wird die 24V Spannung direkt zum Ausgang durchgeschleift

Der LM2596 Spannungsregler kann Eingangsspannungen von 4,5V bis 40V verarbeiten und liefert eine stabile Ausgangsspannung mit bis zu 3A (1,5A kontinuierlich). Die Effizienz liegt bei bis zu 92%, was für eine geringe Wärmeentwicklung sorgt.

Das EI-OT RS485 WiFi Bridge Modul unterstützt Eingangsspannungen von 5-12V über die VIN Schraubklemme. Es basiert auf dem SP3485EN in Kombination mit dem 74HC04D zur Umsetzung des RS485-Signals auf die UART-Schnittstelle des ESP-01+.

### Schaltplan (vereinfacht)

```
+----------+     +------------+     +---------+
|          |     |            |     |         |
| 24V      +-----+ LM2596     +-----+ ESP-01  |
| Netzteil |     | Step-Down  |     | RS485   |
|          |     | (12V Out)  |     | Modul   |
+----------+     +------------+     +---------+
      |                                  |
      |                                  |
      v                                  v
+----------+                       +---------+
|          |                       |         |
| 24V      |                       | RS485   |
| Ausgang  |                       | Ausgang |
|          |                       |         |
+----------+                       +---------+
```

## Wetterstation Elsner Elektronik P03/3

Die P03/3 ist eine Wetterstation von Elsner Elektronik mit folgenden Eigenschaften:
- Helligkeitsmessung mit drei separaten Sensoren für Ost, Süd und West
- Elektronische Windmessung (0-35 m/s)
- Temperaturmessung (-40 bis +80°C)
- Beheizter Niederschlagssensor
- Integrierter GPS-Empfänger
- Betriebsspannung: 24V DC
- Schutzgrad: IP 44
- Maße: ca. 96 × 77 × 118 mm (B × H × T)

Weitere Informationen: [Elsner Elektronik P03/3](https://www.elsner-elektronik.de/de/p03-3.html)

## Funktionen

### Wetterdaten
- **Temperatur**: Erfassung der Umgebungstemperatur in °C
- **Helligkeit**: Messung in drei Richtungen (Süd, West, Ost) in Lux
- **Licht**: Allgemeine Lichtmessung in Lux
- **Wind**: Erfassung der Windgeschwindigkeit in m/s und Berechnung der Beaufort-Skala
- **Regen**: Binärer Sensor zur Regenerkennung
- **Sonnenposition**: Berechnung von Azimut und Elevation der Sonne

### Zusätzliche Funktionen
- Tag/Nacht-Erkennung basierend auf Sonnenelevation
- Berechnung der maximalen Helligkeit aus allen Richtungen
- GPS/RTC-Statusanzeige
- Datum- und Zeitanzeige (UTC)

### Diagnose
- WLAN-Signalstärke und -qualität
- WLAN-Kanal und Verbindungsinformationen
- Uptime-Tracking
- Neustart-Button für den ESP

## Installation

1. Installiere ESPHome auf deinem System
2. Kopiere die YAML-Konfiguration in eine neue Datei
3. Erstelle eine `secrets.yaml` Datei mit den WLAN- und MQTT-Zugangsdaten
4. Kompiliere und flashe die Firmware auf deinen ESP

## Modbus-Konfiguration

Die Wetterstation kommuniziert über Modbus mit dem Sensor mit folgenden Parametern:
- Baudrate: 19200
- Datenbits: 8
- Stoppbits: 1
- Parität: EVEN
- Modbus-Adresse: 0x01
- Flow Control Pin: GPIO2
- TX Pin: GPIO1
- RX Pin: GPIO3

## Home Assistant Integration

Die Wetterstation wird automatisch in Home Assistant über MQTT Discovery integriert. Alle Sensoren werden mit passenden Geräteklassen und Messeinheiten konfiguriert.

## 3D-Druck Gehäuse

Für dieses Projekt werden passende 3D-Druckdateien für ein Gehäuse zur Verfügung gestellt. Diese ermöglichen eine wetterfeste und ästhetische Montage der Elektronikkomponenten.

## Anpassung

Du kannst die Konfiguration an deine Bedürfnisse anpassen:
- Ändere die Update-Intervalle für häufigere oder seltenere Aktualisierungen
- Passe die Modbus-Parameter an, wenn du einen anderen Sensor verwendest
- Füge weitere berechnete Sensoren hinzu, um zusätzliche Wetterdaten zu erhalten

## Verwendung für andere Modbus-Projekte

Diese Implementierung kann als Grundlage für andere Modbus-basierte Projekte dienen. Die Modbus-Controller-Konfiguration ist flexibel und kann leicht an andere Modbus-Geräte angepasst werden. Die Struktur der Sensoren und die Verarbeitung der Daten bieten ein solides Fundament für ähnliche Projekte.

## Fehlerbehebung

Bei Kommunikationsproblemen mit dem Modbus-Sensor:
1. Erhöhe das Log-Level auf `VERY_VERBOSE` für detaillierte Modbus-Logs
2. Überprüfe die Verkabelung und die Modbus-Parameter
3. Stelle sicher, dass der Flow-Control-Pin korrekt konfiguriert ist

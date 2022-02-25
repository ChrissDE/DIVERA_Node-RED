# DIVERA_Node-RED
## DIVERA Integration in Node-RED Automatisierung

Ein eingehender Alarm soll bestimmte Aktionen auslösen? Eine smarte Steckdose schalten, um Einsatzmittel wie Drohnen und Lampen zu laden?

Für eine Node-RED Konfiguration ist dies einfach möglich!

### Einführung
Wir nutzen in unserer Einheit einen Raspberry Pi ähnlichen Server, der verschiedene Automatisierungen übernimmt. Eine Aufgabe dabei ist das Schalten und somit Laden der Drohnenakkus bei auflaufendem Alarm. Wir möchten den Akkuverschleiß durch permanentes Laden umgehen und laden somit nur bedarfsgerecht. Normalerweise werden die Akkus auf einer Lagerspannung gehalten. Im Einsatzfall wird dann über DIVERA eine schaltbare Steckdose angesteuert, die die Restladung bis zum Eintreffen an den Fahrzeughallen sicherstellt.

Im Produktivbetrieb passieren noch ein paar andere Dinge und man kann die Steckdose und weitere Funktionen auch per Telegram bedienen, dies würde aber diesen Rahmen sprengen.

### Übersicht
Für die Nutzung von DIVERA in Node-RED werden zwei Subflows bereitgestellt:

1) DIVERA (autologin)
    Hierbei erfolgt der Login über den Autologin-Monitorbenutzer. Der Monitorbenutzer kann erweiterte Rechte zum Lesen von Alarmierungen erhalten und auf die benötigten Daten (accesskey) kann nur der Admin zugreifen.

2) DIVERA (user login)
    Hier wird für den einzelnen Benutzer die Alarmierung abgefragt. Es sind eMail-Adresse und Passwort erforderlich. Es werden nur Alarmierungen empfangen, die auch für diesen Benutzer relevant sind.

### Import & Nutzung
Mit dem Import der Datei [DIVERA_Beispiel.json](/flows/DIVERA_Beispiel.json) werden zwei Subflows und ein Beispiel-Flow erstellt.
![Bild:DIVERA_Beispiel.png](/images/DIVERA_Beispiel.png)

Die Subflows müssen nur einmalig gestartet werden, etwa über einen Start-Trigger, der den Flow einmalig bei Start des Servers startet.

Beide Subflows haben 3 Ausgänge:
    Ausgang 1 wird angesprochen, wenn eine neue Alarmierung erkannt wurde.
    Ausgang 2 wird periodisch angesprochen, dies kann man für Auswertungen oder einen Watchdog nutzen, um die Funktion sicher zu stellen
    Ausgang 3 wird im Fehlerfall ausgegeben, etwa, wenn die Logindaten nicht stimmen

Bereits abgearbeitete Alarmierungen werden unter 'globals.DIVERA.alarms' gespeichert, so wird verhindert, dass bei Statusupdates Ausgang 1 erneut angesprochen wird. Eine Funktion zum Zurücksetzen des Alarmausganges ist nicht vorgesehen!

Ausgang 1 gibt ein msg-Objekt zurück, welches alle erkannten Alarmierungen als array enthält. Der Aufbau des Objektes ist unter [https://api.divera247.com/#/Einsatzinformationen%20abrufen/get_api_last_alarm](https://api.divera247.com/#/Einsatzinformationen%20abrufen/get_api_last_alarm) zu finden.

Eine Fehlerbehandlung wurde nicht in den Subflows realisiert und sollte im jeweiligen Flow Berücksichtigung finden. Der ERROR-Ausgang reagiert lediglich auf fehlerhafte Logins!

### DIVERA (autologin) konfigurieren
Zunächst muss ein Monitorbenutzer unter "Verwaltung => Setup => Monitore => Monitor-Benutzer" angelegt werden. Unter "AUTOLOGIN" muss diese Funktion aktiviert werden. Anschließend sieht man den Autologin-Key.

Dieser wird im Subflow unter den Umgebungsvariablen angegeben:
![Bild:DIVERA_autologin_config.png](/images/DIVERA_autologin_config.png)

### DIVERA (user login) konfigurieren
Hier muss lediglich die eMail-Adresse und das Passwort des gewünschten Benutzers in den Umgebungsvariablen eingetragen werden:
![Bild:DIVERA (user login) config.png](/images/DIVERA (user login) config.png)

> Es sollten nicht mehrere Subflow-Instanzen parallel gestertet werden, um die Anfragelast auf dem Server gering zu halten!

### Dateien
DIVERA (autologin)
[Datei:DIVERA_autologin.json](/flows/DIVERA_autologin.json)
![Bild:DIVERA_autologin.png](/images/DIVERA_autologin.png)

DIVERA (user login)
[Datei:DIVERA_userlogin.json](/flows/DIVERA_userlogin.json)
![Bild:DIVERA_userlogin.png](/images/DIVERA_userlogin.png)

## Aufgabe 1: Konzepte

### a) Sliding-Window, TCP Tahoe, TCP Reno und TCP Vegas

Sliding-Window ist ein Verfahren, bei dem ein Sender mehrere Pakete senden darf, ohne nach jedem einzelnen Paket direkt auf eine Bestätigung warten zu müssen. Die Fenstergröße bestimmt, wie viele Pakete gleichzeitig unbestätigt unterwegs sein dürfen. Sobald ACKs ankommen, verschiebt sich das Fenster weiter.

Beispiel für Sliding-Window:

    Vorher:
    
    [1] [2] [3] [4]  5   6   7
     ^^^^^^^^^^^^^
     Sendefenster
    
    ACK für Paket 1 kommt an.
    
    Nachher:
    
     1  [2] [3] [4] [5]  6   7
        ^^^^^^^^^^^^^
        Sendefenster

TCP Tahoe ist ein Verfahren zur Staukontrolle. Es startet mit Slow Start und erhöht die Sendemenge zunächst schnell. Wenn Paketverlust erkannt wird, wird das Congestion Window stark reduziert und Slow Start beginnt erneut.

TCP Reno erweitert TCP Tahoe. Bei drei doppelten ACKs wird das verlorene Paket neu gesendet. Durch Fast Recovery wird die Übertragungsrate aber nicht so stark zurückgesetzt wie bei Tahoe.

TCP Vegas erkennt Überlastung früher. Es wartet nicht erst auf Paketverlust, sondern beobachtet die Verzögerung der Pakete. Wenn die RTT steigt, wird angenommen, dass sich Stau im Netzwerk bildet, und die Sendemenge wird angepasst.

Diese Verfahren werden gebraucht, damit TCP effizient sendet, aber das Netzwerk nicht überlastet.

### b) Protokolle und OSI-Schichten

| Protokoll | OSI-Schicht | Begründung |
|---|---:|---|
| ARP | Schicht 2/3 | ARP ordnet IP-Adressen MAC-Adressen zu. Es verbindet damit die Vermittlungsschicht mit der Sicherungsschicht. |
| IP | Schicht 3 | IP ist für logische Adressierung und Routing zwischen Netzwerken zuständig. |
| TCP | Schicht 4 | TCP sorgt für verbindungsorientierten und zuverlässigen Transport von Daten. |
| DHCP | Schicht 7 | DHCP vergibt automatisch IP-Adressen und weitere Netzwerkinformationen. |
| DNS | Schicht 7 | DNS übersetzt Domainnamen in IP-Adressen. |


## Aufgabe 2: Nmap

### a) Hosts im lokalen Klasse-C-Netz

Verwendeter Befehl:

```bash
sudo nmap -sn 192.168.2.0/24
```

Bedeutung der Befehlselemente:

- `sudo`: führt den Befehl mit erhöhten Rechten aus
- `nmap`: startet das Scan-Tool Nmap
- `-sn`: führt nur eine Host-Erkennung durch, aber keinen Portscan
- `192.168.2.0/24`: scannt das lokale Klasse-C-Netz von `192.168.2.0` bis `192.168.2.255`

Ergebnis:

```text
...
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.19 seconds
```

Es wurden 4 aktive Hosts im lokalen Netz gefunden.

### b) Betriebssystem von scanme.nmap.org

Verwendeter Befehl:

```bash
sudo nmap -O scanme.nmap.org
```

Bedeutung der Befehlselemente:

- `sudo`: führt Nmap mit erhöhten Rechten aus
- `nmap`: startet das Scan-Tool
- `-O`: aktiviert die Betriebssystem-Erkennung
- `scanme.nmap.org`: Zielhost

Ergebnis:

```text
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite

Aggressive OS guesses:
Linux 5.0 - 5.14 (96%)
MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (96%)
Linux 4.15 - 5.19 (94%)

No exact OS matches for host.
Network Distance: 24 hops
```

Nmap erkennt kein exaktes Betriebssystem, vermutet aber mit hoher Wahrscheinlichkeit ein Linux-System. Die beste Schätzung ist `Linux 5.0 - 5.14` mit 96%.

### c) Registrierungsdatum von nmap.org

Verwendeter Befehl:

```bash
whois nmap.org
```

Bedeutung der Befehlselemente:

- `whois`: fragt Registrierungsinformationen zu einer Domain ab
- `nmap.org`: die abgefragte Domain

Ergebnis:

```text
Domain Name: nmap.org
Creation Date: 1999-01-18T05:00:00Z
Registrar: Dynadot Inc
```

Die Webseite bzw. Domain `nmap.org` wurde am `18.01.1999` registriert.

### d) Effizienter Scan vieler Adressen nach offenen TCP-Ports

Verwendeter Beispielbefehl:

```bash
sudo nmap -sS -T4 --top-ports 100 --open 192.168.2.0/24
```

Bedeutung der Befehlselemente:

- `sudo`: führt Nmap mit erhöhten Rechten aus
- `nmap`: startet das Scan-Tool
- `-sS`: führt einen SYN-Scan aus
- `-T4`: verwendet ein schnelleres Timing
- `--top-ports 100`: scannt nur die 100 häufigsten Ports
- `--open`: zeigt nur Hosts mit offenen Ports an
- `192.168.2.0/24`: scannt das angegebene Netz

Damit kann man größere Adressbereiche effizient scannen, weil nicht alle 65535 Ports geprüft werden, sondern nur die häufigsten Ports. Außerdem ist der SYN-Scan schneller als ein vollständiger TCP-Verbindungsaufbau.

In Wireshark sieht man dabei viele TCP-SYN-Pakete von meinem Rechner an verschiedene Zieladressen und Ports. Antwortet ein Ziel mit SYN/ACK, ist der Port offen. Antwortet es mit RST, ist der Port geschlossen.

### e) SYN-Scan

Ein SYN-Scan wird mit `-sS` durchgeführt. Dabei baut Nmap keine vollständige TCP-Verbindung auf.

Ablauf bei offenem Port:

```text
Nmap  -> SYN
Ziel  -> SYN/ACK
Nmap  -> RST
```

### f) Häufig auftretende offene Ports

Bei meinen bisherigen Nmap-Scans sind unter anderem folgende offene TCP-Ports aufgetreten:

| Port | Dienst | Verwendung |
|---|---|---|
| 22/tcp | SSH | Verschlüsselte Remote-Verbindung zu einem Rechner |
| 53/tcp/udp | DNS | Namensauflösung von Domains zu IP-Adressen |
| 80/tcp | HTTP | Unverschlüsselte Webseitenübertragung |
| 443/tcp | HTTPS | Verschlüsselte Webseitenübertragung |

## Aufgabe 3: DHCP

### a) Verwendung von DHCP

DHCP wird verwendet, damit ein Gerät automatisch eine Netzwerkkonfiguration erhält. Wenn sich ein Rechner mit einem Netzwerk verbindet, bekommt er über DHCP zum Beispiel eine IP-Adresse, Subnetzmaske, Gateway und DNS-Server. Dadurch müssen diese Werte nicht manuell eingetragen werden.

### b) DHCP-Pakete in Wireshark

Verwendeter Capture-Filter:

```text
udp port 67 or udp port 68
```

DHCP verwendet UDP-Port 67 für den Server und UDP-Port 68 für den Client. Mit diesem Capture-Filter wurden nur DHCP-Pakete aufgezeichnet.

Um DHCP-Pakete zu erzeugen, wurde die Netzwerkverbindung in Kali Linux getrennt und erneut verbunden:

```bash
sudo nmcli device disconnect eth0; sudo ip addr flush dev eth0; sudo rm -f /var/lib/NetworkManager/*.lease /var/lib/dhcp/* /var/lib/dhclient/* 2>/dev/null; sleep 3; sudo nmcli device connect eth0
```

Aufgezeichnet wurden die vier typischen DHCP-Pakete:

- DHCP Discover
- DHCP Offer
- DHCP Request
- DHCP ACK

### c) Funktionsweise von DHCP

Normalerweise läuft DHCP in vier Schritten ab:

1. DHCP Discover  
   Der Client hat noch keine gültige IP-Adresse und sucht per Broadcast nach einem DHCP-Server im Netzwerk.

2. DHCP Offer  
   Der DHCP-Server antwortet mit einem Angebot. Darin stehen zum Beispiel eine mögliche IP-Adresse und weitere Netzwerkinformationen.

3. DHCP Request  
   Der Client nimmt das Angebot an und fordert die angebotene IP-Adresse offiziell an.

4. DHCP ACK  
   Der DHCP-Server bestätigt die Vergabe der IP-Adresse. Danach darf der Client die Netzwerkkonfiguration verwenden.

In den DHCP-Paketen werden unter anderem Informationen wie IP-Adresse, MAC-Adresse des Clients, Subnetzmaske, Gateway, DNS-Server und Lease Time übertragen.


## Aufgabe 4: Routing

### a) Distanz-Vektor und Bellman-Ford

Beim Distanz-Vektor-Verfahren kennt jeder Router zuerst nur die Kosten zu seinen direkten Nachbarn. Danach tauschen die Router ihre bekannten Distanzen mit den Nachbarn aus.

Mit dem Bellman-Ford-Algorithmus prüft ein Router für jedes Ziel:

Kosten zum Nachbarn + Kosten vom Nachbarn zum Ziel

Wenn dieser neue Weg günstiger ist als der bisher bekannte Weg, wird die Routingtabelle aktualisiert. Dieser Vorgang wird wiederholt, bis sich keine Einträge mehr ändern.

Im Beispiel sieht man, dass A den Weg zu C nicht direkt nehmen sollte:

- A → C direkt: Kosten 23
- A → B → C: Kosten 3 + 2 = 5

Der Weg über B ist also günstiger. Für D gilt:

- A → B → C → D: Kosten 3 + 2 + 5 = 10
- A → C → D: Kosten 23 + 5 = 28

Der günstigste Weg von A nach D ist also A → B → C → D mit Kosten 10.

Beim Link-State-Verfahren kennt jeder Router die gesamte Netzwerktopologie und berechnet daraus selbst die kürzesten Wege, zum Beispiel mit Dijkstra. Beim Distanz-Vektor-Verfahren kennt ein Router dagegen nur die Informationen seiner direkten Nachbarn.

Distanz-Vektor ist einfacher, kann aber langsamer konvergieren. Link-State ist schneller und genauer, benötigt aber mehr Informationen über das gesamte Netzwerk.

Eine hybride Nutzung kann sinnvoll sein, wenn man Vorteile beider Verfahren kombinieren möchte, zum Beispiel in größeren Netzwerken mit verschiedenen Bereichen.

### b) Routingtabellen für x, y und z

Die Routingtabellen wurden direkt in der PDF ausgefüllt.

Der kostengünstigste Weg von Router z zu Router x ist:

z → y → x

Kosten:

1 + 3 = 4

### c) Änderung der Kosten zwischen x und y

Die neuen Routingtabellen wurden direkt in der PDF ausgefüllt.

Nach der Änderung steigen die Kosten zwischen x und y von 3 auf 7.

Der kostengünstigste Weg von z nach x ist danach:

z → x

Kosten:

7

Ja, der günstigste Pfad ändert sich. Vorher war der beste Weg z → y → x mit Kosten 4. Danach ist der direkte Weg z → x mit Kosten 7 am günstigsten.

### d) Ausfall von Router D

Wenn Router D ausfällt, merken das zuerst die direkt verbundenen Router B, C und E.

Beim Distanz-Vektor-Verfahren wird diese Information schrittweise über die Nachbarn weitergegeben. Dadurch kann es dauern, bis alle Router wissen, dass D nicht mehr erreichbar ist. Es kann außerdem vorübergehend zu falschen Routinginformationen kommen, weil Router nur die Informationen ihrer Nachbarn kennen.

Beim Link-State-Verfahren melden die direkt betroffenen Router den Ausfall an alle anderen Router. Danach berechnet jeder Router seine Routingtabelle neu. Dadurch wird der Ausfall normalerweise schneller und eindeutiger erkannt.

Zusammengefasst:

| Verfahren | Verhalten bei Ausfall von D |
|---|---|
| Distanz-Vektor | Information verbreitet sich schrittweise über Nachbarn |
| Link-State | Ausfall wird an alle Router verteilt und Routen werden neu berechnet |

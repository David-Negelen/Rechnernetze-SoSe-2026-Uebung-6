# Rechnernetze-SoSe-2026-Uebung-6

# Aufgabe 1 

# a
Sliding-Window: Dient der Flusskontrolle (Schutz des Empfängers). Der Empfänger gibt ein Fenster (rwnd) vor, welches bestimmt, wie viele Daten der Sender unbestätigt "in-flight" haben darf. Das Fenster wandert (slidet) mit jeder Bestätigung weiter.

TCP Tahoe: Eine frühe Staukontrolle (Schutz des Netzes). Nutzt Slow Start und Congestion Avoidance. Bei jedem erkannten Verlust (ob Timeout oder 3 doppelte ACKs) fällt das Staufenster (cwnd) radikal zurück auf 1 MSS.

TCP Reno: Verbessert Tahoe durch Fast Recovery. Bei 3 doppelten ACKs wird das Fenster nur halbiert und direkt linear weiter gewachsen (Congestion Avoidance), da das Netz offensichtlich noch Daten transportiert. Nur bei einem harten Timeout fällt Reno auf 1 MSS zurück.

TCP Vegas: Arbeitet im Gegensatz zu Tahoe/Reno proaktiv (delay-based statt loss-based). Es misst die RTT (Round-Trip-Time). Steigt diese an, erkennt Vegas den beginnenden Stau in den Router-Warteschlangen und drosselt das Tempo, bevor Pakete verloren gehen.

# b
7. Application	HTTP, DNS, SMTP, DHCP	Direkte Schnittstelle zu den Endanwendungen (Web, Mail, Namensauflösung).
6. Presentation	TLS / SSL	Formatiert, komprimiert und verschlüsselt Daten für die Anwendung.
5. Session	RPC	Steuert und verwaltet die logischen Verbindungen (Sitzungen) zwischen Prozessen.
4. Transport	TCP, UDP	Regelt die Ende-zu-Ende-Übertragung (TCP: zuverlässig / UDP: schnell).
3. Network	IP (IPv4/v6), ICMP	Zuständig für logische Adressierung und weltweite Wegfindung (Routing).
2. Data Link	Ethernet, WLAN	Regelt den Medienzugriff und fehlerfreien Rahmen-Transport im lokalen Netz (LAN).
1. Physical	 DSL, Glasfaser, Überseekabel	Definiert die physikalischen Stecker, Kabel und elektrischen/optischen Signale.

# Aufgabe 2

# a
nmap -sn -v -T4 89.244.123.151

Nmap done: 256 IP addresses (256 hosts up) scanned in 1.00 seconds, siehe hosts.txt

# b
sudo nmap -O -v -T4 scanme.nmap.org

Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4
OS details: Linux 4.19 - 5.15

# c
whois nmap.org

Creation Date: 1999-01-18T05:00:00Z

# d
nmap -sS -F --top-ports 100 -T4 -n --open 192.168.0.0/16


# e
Der SYN-Scan (-sS) ist der Standard-Scan von Nmap, wenn man über Administratorrechte verfügt. Er wird auch "halboffener" Scan genannt, weil der vollständige TCP-Handshake niemals vollendet wird.

# f
- Port 80 (TCP) - HTTP: Wird für unverschlüsselten Webverkehr und die Übertragung von Webseiten genutzt.
- Port 443 (TCP) - HTTPS: Wird für verschlüsselten und sicheren Webverkehr verwendet.
- Port 22 (TCP) - SSH: Ermöglicht den sicheren Fernzugriff auf Server über die Kommandozeile.
- Port 53 (TCP/UDP) - DNS: Zuständig für die Namensauflösung im Netzwerk.
- Port 445 (TCP) - Microsoft-DS (SMB): Wird für die Windows-Dateifreigabe und Druckerfreigabe im lokalen Netz genutzt.
- Port 3389 (TCP) - RDP: Ermöglicht die grafische Fernsteuerung von Windows-Systemen (Remote Desktop).

# Aufgabe 3
# a
Sei DHCP wie die Rezeption in einem Hotel

Wenn ein Hotel betreten wird (ein neues Gerät verbindet sich mit dem Netzwerk), weiß man nicht, welches Zimmer frei ist. Man darf sich nicht ein beliebiges zimmer aussuchen,weil ein Zimmer dann doppelt belegt sein könnte.
Stattdessen gehet man zur Rezeption ( DHCP-Server) und fragt nach einem freien Zimmer.
Der Rezeptionist schaut in sein System, wählt ein freies Zimmer aus und gibt den Schlüssel.
Auf dem Schlüsselanhänger stehen nicht nur die Zimmernummer (IP-Adresse), sondern auch der Weg zum Ausgang (Gateway) und die Nummer der Auskunft (DNS-Server).
Solange man im Hotel ist darf man das Zimmer nutzen (Lease-Time), sobald man abreist wird das zimmer wieder frei und ein anderer Gast kann es beziehen

# b

Filter:port 67 or port 68

# c 
1. DHCP Discover (Frame 2)
Aufgabe der Nachricht: Der Client besitzt keine IP-Adresse und sucht per Broadcast im gesamten Netzwerk nach verfügbaren DHCP-Servern.

Informationen & Nutzung :
Message type: Boot Request (1): Signalisiert, dass die Nachricht vom Client ausgeht.
Client IP address: 0.0.0.0: Da der Client noch unkonfiguriert ist, besitzt er keine eigene Quell-IP.
Option (50) Requested IP Address: 192.168.2.212: Der PC schlägt dem Server aktiv seine zuvor genutzte IP vor.
Option (55) Parameter Request List: Der PC fordert hierüber wichtige Zusatzinfos an (z. B. Subnetzmaske, Router-IP, DNS-Server).

2. DHCP Offer (Frame 3)
Aufgabe der Nachricht: Der DHCP-Server antwortet auf die Suche und bietet dem Client eine freie IP-Adresse aus seinem Pool an.

Informationen & Nutzung :
Message type: Boot Reply (2): Es handelt sich um eine Server-Antwort.
Your (client) IP address: 192.168.2.212: Die konkret angebotene IP-Adresse des Servers für diesen Client.
Server host name: Der antwortende Server identifiziert sich als Speedport_Smart_4_Typ_A.
Option (54) DHCP Server Identifier: 192.168.2.1: Die IP-Adresse des DHCP-Servers.
Option (1) Subnet Mask: Liefert direkt die Netzwerkmaske (255.255.255.0) mit.
Option (51) IP Address Lease Time: Gibt an, wie lange der PC diese IP-Adresse behalten darf.

3. DHCP Request (Frame 4)
Aufgabe der Nachricht: Der Client nimmt das Angebot des spezifischen Servers offiziell an. Das Paket wird als Broadcast gesendet, damit eventuelle andere DHCP-Server im Netz ihr Angebot verwerfen.

Informationen & Nutzung :
Message type: Boot Request (1): Erneute Anfrage durch den Client.
Option (50) Requested IP Address: 192.168.2.212: Der Client bestätigt dem Server.
Option (54) DHCP Server Identifier: 192.168.2.1: Der Client adressiert direkt den Router, um dessen Angebot anzunehmen.

4. DHCP ACK (Frame 5)
Aufgabe der Nachricht: Die finale Bestätigung (Acknowledge) des Servers. Er trägt die Bindung der IP zur MAC-Adresse fest in seine Tabelle ein.
Informationen & Nutzung :
Message type: Boot Reply (2): Finale Antwort des Servers.
Your (client) IP address: 192.168.2.212: Bestätigt dem Client die Zuweisung.
Option (3) Router, Option (6) Domain Name Server: Übergibt die Netzwerkparameter final an das Betriebssystem des PCs. Erst nach dem Erhalt dieses Pakets speichert der PC die Konfiguration lokal ab und stellt die Internetverbindung her.

# Aufgabe 4
# a
 Distanzvektor-Verfahren (Bellman-Ford) am BeispielnetzGrundidee: Jeder Knoten kennt zu Beginn nur die Kosten zu seinen direkten Nachbarn. Er kennt weder die Topologie des Gesamtnetzes noch die Kosten zu entfernten Knoten. Um trotzdem globale Routen zu berechnen, tauschen sich Nachbarn periodisch über ihre komplette Distanzvektor-Tabelle aus ("Ich erreiche X mit Kosten Y"). Jeder Knoten nutzt diese Information, um über die Bellman-Ford-Gleichung zu prüfen, ob ein Umweg über einen Nachbarn günstiger ist als der bisher bekannte Weg:D_x(y) = min { c(x,v) + D_v(y) }  für alle Nachbarn v von xD.h.: Kosten von x nach y = minimale Summe aus Linkkosten zum Nachbarn v plus den Kosten, die v selbst bis y meldet.Ablauf am Beispiel (A–B=3, A–C=23, B–C=2, C–D=5):
Initialisierung: Jeder Knoten kennt nur direkte Links. A kennt B (3) und C (23), B kennt A (3) und C (2), C kennt A (23), B (2), D (5), D kennt nur C (5).
Austausch: Jeder Knoten schickt seinen Vektor an alle Nachbarn.
Aktualisierung (Bellman-Ford-Schritt): Jeder Knoten berechnet für jedes mögliche Ziel die Summe "eigene Linkkosten zum Nachbarn + vom Nachbarn gemeldete Kosten" und übernimmt das Minimum.

Beispiel A → C: direkt 23, aber über B: 3 + 2 = 5 → günstiger, also übernimmt A den Weg über B.
Beispiel A → D: kein direkter Link, über B: 3 + (Kosten B→D=7) = 10.

Wiederholung, bis sich keine Tabelle mehr ändert (Konvergenz).

Unterschiede Distanzvektor vs. Link-State
Beim Distanzvektor-Verfahren kennt jeder Knoten nur seine direkten Nachbarn und deren gemeldete Distanzvektoren – ein globales Bild der Topologie existiert nirgendwo. Die Berechnung erfolgt verteilt und iterativ über die Bellman-Ford-Gleichung, wobei jeder Knoten nur lokal rechnet und sukzessive bessere Wege von seinen Nachbarn übernimmt. Das macht die Konvergenz langsamer und anfällig für das Count-to-Infinity-Problem, wenn Links ausfallen. Der Nachrichten-Overhead pro Austausch ist gering, dafür aber periodisch an alle Nachbarn.
Beim Link-State-Verfahren dagegen flutet jeder Knoten den Zustand seiner direkten Links ins gesamte Netz, sodass am Ende jeder Knoten die komplette Topologie kennt. Jeder Knoten berechnet daraus selbst mit Dijkstra den kürzesten-Wege-Baum zu allen Zielen. Dadurch konvergiert das Verfahren schneller, da nach dem Flooding sofort alle nötigen Informationen vorliegen. Der Overhead ist initial höher (Flooding der gesamten Topologie), danach aber nur bei tatsächlichen Änderungen nötig. Link-State skaliert außerdem besser in großen, komplexen Netzen, während Distanzvektor eher für kleinere, einfachere Netze geeignet ist.

Hybride Nutzung
Ja, das ist in der Praxis üblich. Beispiel: BGP (Border Gateway Protocol) im Internet-Backbone ist im Kern ein Distanzvektor-/Path-Vector-Verfahren (skaliert gut über sehr viele autonome Systeme, keine vollständige globale Topologie nötig), während innerhalb eines autonomen Systems oft OSPF (Link-State) läuft, um schnelle Konvergenz und optimale Pfade in einem überschaubaren, gut bekannten Netz zu erreichen. So kombiniert man die Skalierbarkeit von Distanzvektor-Ansätzen zwischen Domänen mit der Präzision und Geschwindigkeit von Link-State-Verfahren innerhalb einer Domäne.

# c
Ändert sich der Pfad z→x?
Nein. Direkter Weg z-x kostet weiterhin 7, der Umweg über y kostet 1+7=8. Der günstigste Pfad bleibt z → x direkt (Kosten 7) – vorher war es via y (1+3=4), jetzt lohnt sich der Umweg nicht mehr.

# d
Ausfall von Router D: Distanzvektor vs. Link-State
Distanzvektor: Nur die direkten Nachbarn von D (B und E) merken den Ausfall sofort, da ihre direkte Verbindung wegfällt. Alle anderen Router (A, C) merken es erst indirekt und verzögert, wenn B bzw. E ihre aktualisierten Vektoren weiterreichen – das kann mehrere Iterationen dauern (abhängig vom Update-Intervall). Es besteht zusätzlich das Risiko des Count-to-Infinity-Problems: B und E könnten sich gegenseitig veraltete Wege über den jeweils anderen als Ersatz für D melden, bevor die Information sauber propagiert ist.
Link-State: B und E bemerken den Ausfall der Verbindung zu D ebenfalls zuerst (z.B. durch ausbleibende Hello-Pakete). Sie fluten diese Information aber sofort als neues Link-State-Paket an das gesamte Netz. Dadurch wissen A, C und alle anderen Router nahezu gleichzeitig und sehr schnell, dass D nicht mehr erreichbar ist, und berechnen direkt einen neuen kürzesten-Wege-Baum ohne D.
Fazit: Link-State konvergiert bei Knotenausfällen deutlich schneller und zuverlässiger im gesamten Netz, während Distanzvektor die Information nur schrittweise über Nachbarn weiterreicht und dabei anfälliger für temporäre Inkonsistenzen (Routing-Loops, Count-to-Infinity) ist.
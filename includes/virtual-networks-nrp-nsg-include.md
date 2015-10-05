## Netzwerksicherheitsgruppen (NSG)
Eine NSG-Ressource ermöglicht das Erstellen einer Sicherheitsbegrenzung für Arbeitsauslastungen durch Implementieren von Zulassungs- und Ablehnungsregeln. Solche Regeln können auf NIC-Ebene (VM-Instanzebene) oder auf Subnetzebene (VM-Gruppe) angewendet werden.

Zu den Schlüsseleigenschaften einer NSG-Ressource zählen:

- **Sicherheitsregeln** - Eine NSG kann mehrere Sicherheitsregeln definieren. Jede Regel kann verschiedene Datenverkehrstypen zulassen oder ablehnen.

### Sicherheitsregel
Eine Sicherheitsregel ist eine der NSG untergeordnete Ressource.

Zu den Schlüsseleigenschaften einer Sicherheitsregel zählen:

- **Protokoll** – Das Netzwerkprotokoll, für das diese Regel gilt.
- **Quellportbereich** - Quellport, oder Bereich, von 0 bis 65535. Zur Übereinstimmung aller Ports kann ein Platzhalter verwendet werden. 
- **Zielportbereich** - Zielport, oder Bereich, von 0 bis 65535. Zur Übereinstimmung aller Ports kann ein Platzhalter verwendet werden.
- **Quelladresspräfix** - Quell-IP-Adressbereich. 
- **Zieladresspräfix** - Ziel-IP-Adressbereich.
- **Zugriff** – *Zulassen* oder *Ablehnen* von Datenverkehr.
- **Priorität** - ein Wert zwischen 100 und 4096. Die Prioritätsnummer muss für jede Regel in der Sammlung von Sicherheitsregeln eindeutig sein. Je niedrigere die Prioritätsnummer ist, desto höher ist die Priorität der Regel.
- **Richtung** – gibt an, ob die Regel für Datenverkehr in *eingehende* oder *ausgehende* Richtung angewendet wird. 

<!---HONumber=Sept15_HO4-->
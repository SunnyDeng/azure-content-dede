<BR>
## Häufig gestellte Fragen 
### Was kosten Reverse-DNS-Einträge?
Sie sind kostenlos! Es fallen keine zusätzlichen Kosten für Reverse-DNS-Einträge oder -Abfragen an.
### Werden meine Reverse-DNS-Einträge vom Internet aufgelöst?
Ja. Sobald Sie die Reverse-DNS-Eigenschaft für Ihre öffentliche IP-Adresse festlegen, verwaltet Azure alle DNS-Delegierungen und DNS-Zonen, die erforderlich sind, um sicherzustellen, dass Reverse-DNS-Einträge für alle Internetbenutzer auflöst.
### Wird für meine öffentlichen IP-Adressen ein Standard-Reverse-DNS-Eintrag erstellt?
Nein. Reverse-DNS ist eine optionale Funktion. Es werden keine Standard-DNS-Einträge erstellt, wenn Sie keine konfigurieren.
### Wie sieht das Format für den vollqualifizierten Domänennamen (FQDN) aus?
FQDNs werden aufsteigend angegeben und müssen einen Punkt am Ende haben (z. B. „app1.contoso.com.“).
### Was passiert bei Fehlern bei den Überprüfungen der Reverse-DNS, die ich angegeben habe?
Wo ein Fehler bei der Überprüfung der Reverse-DNS auftaucht, erfolgt ein Fehler beim Dienstverwaltungsvorgang. Korrigieren Sie bitte den Reverse-DNS-Wert entsprechend den Anforderungen, und versuchen Sie es erneut.
### Kann ich für meine Azure-Website Reverse-DNS verwalten?
Reverse-DNS wird für Azure Websites nicht unterstützt. Reverse-DNS wird für Azure Virtual Machines unterstützt.
### Kann ich für meine öffentliche IP-Adresse mehrere Reverse-DNS-Einträge konfigurieren?
Nein. Azure unterstützt für jede öffentliche IP-Adresse nur einen Reverse-DNS-Eintrag. Jedoch kann jede öffentliche IP-Adresse ihren eigenen Reverse-DNS-Eintrag besitzen.
### Kann ich einen umgekehrten DNS-Eintrag für meine öffentliche IP-Adresse konfigurieren ohne ein DomainNameLabel anzugeben?
Nein. Um umgekehrte DNS-Einträge für Ihre öffentlichen IP-Adressen zu verwenden, müssen Sie die Eigenschaft „DomainNameLabel“ angeben.
### Kann ich die ARPA-Zonen für meine Azure-zugewiesenen IP-Adressen in Azure DNS innerhalb meines eigenen Abonnements oder auf meinem eigenen autoritativen DNS-Server hosten?
Nein. Azure unterstützt das Weiterdelegieren von ARPA-Zonen nicht. Azure hostet die ARPA-Zonen für alle verfügbaren IP-Adressen und ermöglicht es Kunden, innerhalb dieser ARPA-Zonen Reverse-DNS-Einträge zu erstellen.
### Kann ich ARPA-Zonen für meine ISP-zugewiesenen IP-Blöcke in Azure DNS hosten?
Nein. Azure DNS unterstützt derzeit keine Reverse-DNS-Einträge in den DNS-Zonen von Kunden.

<!---HONumber=AcomDC_0316_2016-->
<BR>
## Häufig gestellte Fragen 
### Was kosten Reverse-DNS-Einträge?
Sie sind kostenlos! Es fallen keine zusätzlichen Kosten für Reverse-DNS-Einträge oder -Abfragen an.
### Werden meine Reverse-DNS-Einträge vom Internet aufgelöst?
Ja. Sobald Sie die Reverse-DNS-Eigenschaft für Ihren Clouddienst festlegen, verwaltet Azure alle DNS-Delegierungen und DNS-Zonen, die erforderlich sind, um sicherzustellen, dass Reverse-DNS-Einträge für alle Internetbenutzer auflöst.
### Wird für meine Clouddienste ein Standard-Reverse-DNS-Eintrag erstellt?
Nein. Reverse-DNS wird eine optionale Funktion sein. Es wird kein Standard-DNS-Eintrag erstellt, wenn Sie keinen konfigurieren. Wie sieht das Format für den vollqualifizierten Domänennamen (FQDN) aus? FQDNs werden aufsteigend angegeben und müssen einen Punkt am Ende haben (z. B. „app1.contoso.com.“).
### Was passiert bei Fehlern bei den Überprüfungen der Reverse-DNS, die ich angegeben habe?
Wo ein Fehler bei der Überprüfung der Reverse-DNS auftaucht, erfolgt ein Fehler beim Dienstverwaltungsvorgang. Korrigieren Sie bitte den Reverse-DNS-Wert entsprechend den Anforderungen, und versuchen Sie es erneut.
### Kann ich für meine Azure-Website Reverse-DNS verwalten?
Reverse-DNS wird für Azure Websites nicht unterstützt. Reverse-DNS wird für Azure-PaaS-Rollen und virtuelle IaaS-Computer unterstützt.
### Kann ich für meinen Clouddienst mehrere Reverse-DNS-Einträge konfigurieren?
Nein. Azure unterstützt für jeden Azure-Clouddienst nur einen Reverse-DNS-Eintrag. Jedoch kann jeder Azure-Clouddienst seinen eigenen Reverse-DNS-Eintrag haben.
### Kann ich die ARPA-Zonen für meine Azure-zugewiesenen IP-Adressen in Azure DNS innerhalb meines eigenen Abonnements oder auf meinem eigenen autoritativen DNS-Server hosten?
Nein. Azure unterstützt das Weiterdelegieren von ARPA-Zonen nicht. Azure hostet die ARPA-Zonen für alle verfügbaren IP-Adressen und ermöglicht es Kunden, innerhalb dieser ARPA-Zonen Reverse-DNS-Einträge zu erstellen.
### Kann ich ARPA-Zonen für meine ISP-zugewiesenen IP-Blöcke in Azure DNS hosten?
Nein. Azure DNS unterstützt derzeit keine Reverse-DNS-Einträge in den DNS-Zonen von Kunden.

<!---HONumber=AcomDC_0309_2016-->
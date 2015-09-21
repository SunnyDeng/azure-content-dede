<properties 
	pageTitle="Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung" 
	description="Arbeiten mit unterschiedlichen Ressourcentypen bei der rollenbasierten Zugriffssteuerung." 
	services="azure-portal"
	documentationCenter="na" 
	authors="stepsic-microsoft-com" 
	writer="" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung

## Einführung

Die [rollenbasierte Zugriffssteuerung](../role-based-access-control-configure.md) ist eine leistungsstarke Funktion, mit der Sie Zugriffsberechtigungen für Azure-Ressourcen differenziert steuern können. Gewähren Sie einem bestimmten Benutzer einfach und sicher Zugriff auf genau die Ressourcen, die er benötigt. In einigen Fällen kann das Ressourcen-Modell für Azure-Ressourcen jedoch kompliziert sein, und es ist nicht immer leicht ersichtlich, wofür Sie Berechtigungen vergeben.

In diesem Dokument erhalten Sie ausführliche Informationen über die Nutzung einiger der Rollen im Azure-Portal. Es sind drei allgemeine Rollen enthalten, die alle Ressourcentypen abdecken: * Owner * Contributor * Reader

Besitzer und Mitwirkende haben Vollzugriff auf alle Verwaltungsfunktionen, mit dem Unterschied, dass Mitwirkende anderen Benutzern oder Gruppen keinen Zugriff gewähren können. Die Leserolle werden wir aufgrund ihrer umfassenden Eigenschaften ausführlicher beschreiben. [In diesem Artikel](../role-based-access-control-configure.md) finden Sie genaue Informationen zum Gewähren von Zugriffen.

## App Service-Workloads

### Nur Lesezugriff 

Wenn Sie selbst nur über Lesezugriff auf eine Web-App verfügen oder einem Benutzer nur Lesezugriff auf eine Web-App gewähren, sind eventuell einige Funktionen für Sie unerwartet deaktiviert. Die folgenden Verwaltungsfunktionen erfordern **Schreibzugriff** auf eine Web-App (entweder als Mitwirkender oder Besitzer) und stehen nicht zur Verfügung, wenn der Benutzer nur über Lesezugriff für die Web-App verfügt.
 
1. Befehle (z. B. starten, anhalten)
2. Änderung von Einstellungen, wie allgemeine Konfigurationen, Skalierungseinstellungen, Sicherungseinstellungen und Überwachungseinstellungen
3. Zugriff auf Anmeldedaten für die Veröffentlichung oder andere geheime Schlüssel wie App- und Verbindungseinstellungen
4. Streamingprotokolle
5. Konfiguration von Diagnoseprotokollen
6. Konsole (Eingabeaufforderung)
7. Aktive und kürzlich vorgenommene Bereitstellungen (für fortlaufende Bereitstellungen lokaler Gits)
8. Geschätzte Ausgaben
9. Webtests
10. Virtuelles Netzwerk (für Leser nur sichtbar, wenn ein virtuelles Netzwerk zuvor von einem Benutzer mit Schreibzugriff konfiguriert wurde)
 
Wenn Sie auf keine dieser Kacheln zugreifen können, benötigen Sie Zugriff als Mitwirkender auf diese Web-App.

### Umgang mit zugehörigen Ressourcen
 
Web-Apps können aufgrund verschiedener miteinander verknüpfter Ressourcen kompliziert sein. Im Folgenden ist eine typische Ressourcengruppe mit einigen Websites dargestellt:

![Web-App-Ressourcengruppe](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Wenn Sie daher lediglich auf die Website Zugriff gewähren, sind viele Funktionen des Blatts "Website" vollständig deaktiviert.
 
1. Die folgenden Elemente erfordern Zugriff auf den **App Service-Plan** für Ihre Website:  
    * Anzeigen des Tarifs für die Web-App (z. B. Free oder Standard)
    * Skalierungskonfiguration (d. h. Anzahl der Instanzen, Größe des virtuellen Computers, Einstellungen für automatische Skalierung)
    * Kontingente (z. B. Speicher, Bandbreite, CPU)
2. Die folgenden Elemente erfordern Zugriff auf die gesamte **Ressourcengruppe**, die Ihre Website umfasst.  
    * SSL-Zertifikate und -Bindungen (Der Grund dafür ist, dass SSL-Zertifikate von Websites derselben Ressourcengruppe und desselben geografischen Standorts gemeinsam genutzt werden können).
    * Warnregeln
    * Einstellungen für automatische Skalierung
    * Application Insights-Komponenten
    * Webtests

## Workloads virtueller Computer

Ähnlich wie bei Web-Apps erfordern einige Funktionen auf dem Blatt "Virtueller Computer" Schreibzugriff auf den virtuellen Computer oder auf andere Ressourcen in der Ressourcengruppe.

Für virtuelle Computer gibt es diese verwandten Ressourcen: Domänennamen, virtuelle Netzwerke, Speicherkonten und Warnungsregeln

1. Die folgenden Elemente erfordern **Schreibzugriff** auf den virtuellen Computer:  
    * Endpunkte
    * IP-Adressen
    * Datenträger
    * Erweiterungen
2. Die folgenden Elemente erfordern Schreibzugriff auf den virtuellen Computer und die **Ressourcengruppe** (zusammen mit dem Domänennamen), in der sich der virtuelle Computer befindet:  
    * Verfügbarkeitsgruppe
    * Satz mit Lastenausgleich
    * Warnungsregeln
    
Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf diese Ressourcengruppe.

<!---HONumber=Sept15_HO2-->
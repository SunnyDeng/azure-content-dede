<properties 
	pageTitle="Rollenbasierte Zugriffssteuerung - Problembehandlung" 
	description="Arbeiten mit unterschiedlichen Ressourcentypen für die rollenbasierte Zugriffssteuerung." 
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
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# Rollenbasierte Zugriffssteuerung - Problembehandlung

## Einführung

[Rollenbasierte Zugriffssteuerung](../role-based-access-control-configure.md) ist ein leistungsstarkes Feature, welches das Delegieren eines differenzierten Zugriffs auf Ressourcen in Azure ermöglicht. Gewähren Sie einem bestimmten Benutzer einfach und sicher Zugriff auf genau die Ressourcen, die er benötigt. Mitunter kann das Ressourcenmodell für Azure-Ressourcen jedoch kompliziert sein, weshalb es nicht immer leicht ersichtlich ist, wofür genau Sie Berechtigungen erteilen.

In diesem Dokument erhalten Sie ausführliche Informationen zur Nutzung einiger der neuen Rollen im Azure-Portal.  Diese Version von Azure umfasst drei integrierte Rollen:
* Besitzer
* Mitwirkender
* Leser

Besitzer und Mitwirkende haben Vollzugriff auf alle Verwaltungsfunktionen, mit dem Unterschied, dass Mitwirkende anderen Benutzern oder Gruppen keinen Zugriff gewähren können. Die Leserolle werden wir aufgrund ihrer umfassenden Eigenschaften ausführlicher beschreiben. [In diesem Artikel](../role-based-access-control-configure.md) finden Sie genaue Informationen zum Gewähren des Zugriffs.

## App Services-Arbeitsauslastungen

### Nur Lesezugriff 

Wenn Sie selbst nur über Lesezugriff auf eine einzelne Web-App verfügen oder einem Benutzer nur Lesezugriff darauf gewähren, sind eventuell einige Features für Sie unerwarteterweise deaktiviert. Die folgenden Verwaltungsfunktionen erfordern **Schreibzugriff** auf eine Web-App (entweder als Mitwirkender oder Besitzer) und stehen nicht zur Verfügung, wenn der Benutzer nur über Lesezugriff verfügt. 
 
1. Befehle (z. B. Starten, Anhalten usw.)
2. Änderung von Einstellungen wie allgemeine Konfigurations-, Skalierungs-, Sicherungs- und Überwachungseinstellungen
3. Zugriff auf Anmeldedaten für die Veröffentlichung oder andere geheime Schlüssel wie App-Einstellungen und Verbindungszeichenfolgen
4. Streamingprotokolle
5. Konfiguration von Diagnoseprotokollen
6. Konsole (Eingabeaufforderung)
7. Aktive und kürzlich vorgenommene Bereitstellungen (für die lokale fortlaufende Git-Bereitstellung)
8. Geschätzte Ausgaben
9. Webtests
10. Virtuelles Netzwerk (für Leser nur sichtbar, wenn ein virtuelles Netzwerk zuvor von einem Benutzer mit Schreibzugriff konfiguriert wurde)
 
Wenn Sie auf keine dieser Kacheln zugreifen können, benötigen Sie Zugriff als Mitwirkender auf die Web-App. 

### Umgang mit zugehörigen Ressourcen
 
Web-Apps können aufgrund verschiedener miteinander verknüpfter Ressourcen kompliziert sein. Im Folgenden ist eine typische Ressourcengruppe mit einigen Websites dargestellt:

![Web-App-Ressourcengruppe](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Wenn Sie daher lediglich auf die Website Zugriff gewähren, sind viele Funktionen des Blatts "Website" vollständig deaktiviert. 
 
1. Diese Elemente erfordern Zugriff auf die **App-Diensteeinstellungen**, die Ihrer Website entsprechen:  
    * Anzeigen des Tarifs der Web-App (z. B. Kostenlos oder Standard)
    * Skalierungskonfiguration (d. h. Anzahl der Instanzen, Größe der virtuellen Computer, Einstellungen für automatische Skalierung)
    * Kontingente (z. B. Speicher, Bandbreite, CPU)
2. Die folgenden Elemente erfordern Zugriff auf die gesamte **Ressourcengruppe**, die Ihre Website umfasst:  
    * SSL-Zertifikate und Bindungen (Der Grund dafür ist, dass SSL-Zertifikate von Websites derselben Ressourcengruppe und desselben geografischen Standorts gemeinsam genutzt werden können).
    * Warnungsregeln
    * Einstellungen für automatische Skalierung
    * Application Insights-Komponenten
    * Webtests

## Arbeitsauslastungen virtueller Computer

Ähnlich wie bei Web-Apps erfordern einige Features auf dem Blatt "Virtueller Computer" Schreibzugriff auf den virtuellen Computer oder auf andere Ressourcen in der Ressourcengruppe.

Virtuelle Computer verfügen über folgende zugehörige Ressourcen:
* Domänennamen
* Virtuelle Netzwerke
* Speicherkonten
* Warnungsregeln

1. Die folgenden Elemente erfordern **Schreibzugriff** auf den virtuellen Computer:  
    * Endpunkte
    * IP-Adressen
    * Datenträger
    * Erweiterungen
2. Die folgenden Elemente erfordern Schreibzugriff auf den virtuellen Computer und die **Ressourcengruppe** (neben dem Domänennamen), in der sich der virtuelle Computer befindet:  
    * Verfügbarkeitsgruppe
    * Lastenausgleichsgruppe
    * Warnungsregeln
    
Wenn Sie auf keine dieser Kacheln zugreifen können, bitten Sie den Administrator um den Zugriff als Mitwirkender auf die Ressourcengruppe.

<!--HONumber=54--> 
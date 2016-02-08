<properties
	pageTitle="Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung"
	description="Arbeiten mit unterschiedlichen Ressourcentypen bei der rollenbasierten Zugriffssteuerung."
	services="azure-portal"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung

## Einführung

Die [rollenbasierte Zugriffssteuerung](../role-based-access-control-configure.md) ist eine leistungsstarke Funktion, mit der Sie Zugriffsberechtigungen für Azure-Ressourcen differenziert steuern können. Gewähren Sie einem bestimmten Benutzer einfach und sicher Zugriff auf genau die Ressourcen, die er benötigt. In einigen Fällen kann das Ressourcen-Modell für Azure-Ressourcen jedoch kompliziert sein, und es ist nicht immer leicht ersichtlich, wofür Sie Berechtigungen vergeben.

In diesem Dokument erhalten Sie ausführliche Informationen über die Nutzung einiger der Rollen im Azure-Portal. Es sind drei allgemeine Rollen enthalten, die alle Ressourcentypen abdecken:

- Besitzer  
- Mitwirkender  
- Leser  

Sowohl Besitzer als auch Mitwirkende haben Vollzugriff auf alle Verwaltungsfunktionen, Mitwirkende können jedoch anderen Benutzern oder Gruppen keinen Zugriff gewähren. Die Leserolle werden wir aufgrund ihrer umfassenden Eigenschaften ausführlicher beschreiben. Ausführliche Informationen zum Gewähren von Zugriff finden Sie im Artikel zu den ersten Schritten mit der [rollenbasierten Zugriffssteuerung](../role-based-access-control-configure.md).

## App Service-Workloads

### Schreibzugriff

Wenn Sie einem Benutzer schreibgeschützten Zugriff für eine einzelne Web-App gewähren, sind einige Features deaktiviert, von denen Sie das unter Umständen nicht erwartet haben. Die folgenden Verwaltungsfunktionen erfordern **Schreibzugriff** auf eine Web-App (entweder als Mitwirkender oder Besitzer) und stehen nicht zur Verfügung, wenn der Benutzer nur über Lesezugriff für die Web-App verfügt.

1. Befehle (z. B. starten, anhalten)
2. Änderung von Einstellungen wie allgemeine Konfigurationen, Skalierungseinstellungen, Sicherungseinstellungen und Überwachungseinstellungen
3. Zugriff auf Anmeldedaten für die Veröffentlichung oder andere geheime Schlüssel wie App- und Verbindungseinstellungen
4. Streamingprotokolle
5. Konfiguration von Diagnoseprotokollen
6. Konsole (Eingabeaufforderung)
7. Aktive und kürzlich vorgenommene Bereitstellungen (für fortlaufende Bereitstellungen lokaler Gits)
8. Geschätzte Ausgaben
9. Webtests
10. Virtuelles Netzwerk (für Leser nur sichtbar, wenn ein virtuelles Netzwerk zuvor von einem Benutzer mit Schreibzugriff konfiguriert wurde)

Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf die Web-App.

### Umgang mit zugehörigen Ressourcen

Web-Apps können aufgrund verschiedener miteinander verknüpfter Ressourcen kompliziert sein. Im Folgenden ist eine typische Ressourcengruppe mit einigen Websites dargestellt:

![Web-App-Ressourcengruppe](./media/role-based-access-control-troubleshooting/website-resource-model.png)

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

Virtuelle Computer verfügen über folgende zugehörige Ressourcen:

- Domänennamen
- Virtuelle Netzwerke
- Speicherkonten
- Warnregeln


1. Die folgenden Elemente erfordern **Schreibzugriff** auf den virtuellen Computer:  
    * Endpunkte
    * IP-Adressen
    * Datenträger
    * Erweiterungen
2. Die folgenden Elemente erfordern **Schreibzugriff** auf die virtuelle Maschine und die **Ressourcengruppe** (zusammen mit dem Domänennamen), in der sich die virtuelle Maschine befindet:  
    * Verfügbarkeitsgruppe
    * Satz mit Lastenausgleich
    * Warnungsregeln

Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf diese Ressourcengruppe.

<!---HONumber=AcomDC_0128_2016-->
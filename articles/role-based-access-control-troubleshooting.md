<properties urlDisplayName="" pageTitle="Role based access control troubleshooting" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Role based access control troubleshooting" authors="Stephen Siciliano"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Stephen="" Siciliano" />

# Behandlung von Problemen bei der rollenbasierten Zugriffssteuerung

## Einführung

Die [rollenbasierte Zugriffssteuerung](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-configure/) ist eine leistungsstarke Funktion, mit der Sie Zugriffsberechtigungen für Azure-Ressourcen differenziert steuern können. Gewähren Sie einem bestimmten Benutzer einfach und sicher Zugriff auf genau die Ressourcen, die er benötigt. In einigen Fällen kann das Ressourcen-Modell für Azure-Ressourcen jedoch kompliziert sein, und es ist nicht immer leicht ersichtlich, wofür Sie Berechtigungen vergeben.

In diesem Dokument erhalten Sie ausführliche Informationen über die Nutzung einiger der neuen Rollen im Vorschauportal. Diese Version von Azure umfasst drei integrierte Rollen:

-   Besitzer
-   Mitwirkender
-   Leser

Besitzer und Mitwirkende haben Vollzugriff auf alle Verwaltungsfunktionen, mit dem Unterschied, dass Mitwirkende anderen Benutzern oder Gruppen keinen Zugriff gewähren können. Die Leserolle werden wir aufgrund ihrer umfassenden Eigenschaften ausführlicher beschreiben. [In diesem Artikel](http://azure.microsoft.com/de-de/documentation/articles/role-based-access-control-configure/) finden Sie genaue Informationen zum Gewähren von Zugriffen.

## Website-Arbeitsauslastung

### Nur Lesezugriff

Wenn Sie selbst nur über Lesezugriff auf eine Website verfügen oder einem Benutzer nur Lesezugriff auf eine Website gewähren, sind eventuell einige Funktionen für Sie unerwartet deaktiviert. Die folgenden Verwaltungsfunktionen erfordern **Schreibzugriff** auf eine Website (entweder als Mitwirkender oder Besitzer) und stehen nicht zur Verfügung, wenn der Benutzer nur über Lesezugriff für die Website verfügt.

1.  Befehle (z. B. starten, anhalten)
2.  Änderung von Einstellungen, wie allgemeine Konfigurationen, Skalierungseinstellungen, Sicherungseinstellungen und Überwachungseinstellungen
3.  Zugriff auf Anmeldedaten für die Veröffentlichung oder andere geheime Schlüssel wie App- und Verbindungseinstellungen
4.  Streamingprotokolle
5.  Konfiguration von Diagnoseprotokollen
6.  Website-Konsole (Eingabeaufforderung)
7.  Aktive und kürzlich vorgenommene Bereitstellungen (für fortlaufende Bereitstellungen lokaler Gits)
8.  Geschätzte Ausgaben
9.  Webtests
10. Virtuelles Netzwerk (für Leser nur sichtbar, wenn ein virtuelles Netzwerk zuvor von einem Benutzer mit Schreibzugriff konfiguriert wurde)

Wenn Sie auf keine dieser Funktionen zugreifen können, benötigen Sie Zugriff als Mitwirkender auf diese Website.

### Umgang mit zugehörigen Ressourcen

Websites können aufgrund verschiedener miteinander verknüpfter Ressourcen kompliziert sein. Im Folgenden ist eine typische Ressourcengruppe mit einigen Websites dargestellt:

![Website-Ressourcengruppe](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Wenn Sie daher lediglich auf die Website Zugriff gewähren, sind viele Funktionen des Website-Fensters vollständig deaktiviert.

1.  Die folgenden Elemente erfordern Zugriff auf den **Webhostingplan** für Ihre Website.

    -   Anzeigen der Preisstufe der Website (z. B. Free, Shared, Basic und Standard)
    -   Skalierungskonfiguration (d. h. Anzahl der Instanzen, Größe des virtuellen Computers, Einstellungen für automatische Skalierung)
    -   Kontingente (z. B. Speicher, Bandbreite, CPU)

2.  Die folgenden Elemente erfordern Zugriff auf die gesamte **Ressourcengruppe**, die Ihre Website umfasst.

    -   SSL-Zertifikate und -Bindungen (Der Grund dafür ist, dass SSL-Zertifikate von Websites derselben Ressourcengruppe und desselben geografischen Standorts gemeinsam genutzt werden können).
    -   Warnregeln
    -   Einstellungen für automatische Skalierung
    -   Application Insights-Komponenten
    -   Webtests

## Arbeitsauslastung virtueller Computer

Ähnlich wie bei Websites erfordern einige Funktionen im Fenster "Virtueller Computer" Schreibzugriff auf den virtuellen Computer oder auf andere Ressourcen in der Ressourcengruppe.

Virtuelle Computer verfügen über folgende zugehörige Ressourcen:

-   Domänennamen
-   Virtuelle Netzwerke
-   Speicherkonten
-   Warnregeln

1.  Die folgenden Elemente erfordern **Schreibzugriff** auf den virtuellen Computer:

    -   Endpunkte
    -   IP-Adressen
    -   Datenträger
    -   Erweiterungen

2.  Die folgenden Elemente erfordern Schreibzugriff auf den virtuellen Computer und die **Ressourcengruppe** (zusammen mit dem Domänennamen), in der sich der virtuelle Computer befindet:

    -   Verfügbarkeitsgruppe
    -   Satz mit Lastenausgleich
    -   Warnregeln

Wenn Sie auf keines dieser Elemente zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf diese Ressourcengruppe.


<properties title="Sicherheitsangebote f&uuml;r Azure Virtual Machines" pageTitle="Sicherheitsangebote f&uuml;r Azure Virtual Machines" description="Schneller &Uuml;berblick &uuml;ber die wichtigsten Sicherheitsfeatures f&uuml;r virtuelle Azure-Computer zu Links zu Details" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# Sicherheitsangebote für Azure Virtual Machines

Die Sicherheitsaufgaben und die Konfiguration für die virtuellen Azure-Computer entsprechen weitestgehend denen, die für andere virtuelle oder physische Computer angewendet werden. Dazu zählen derartige Grundlagen wie die Verwendung sicherer Kennwörter, die Nichtverwendung bekannter Benutzernamen und das Herstellen einer Richtlinie und eines Plans für die Aktualisierung des Betriebssystems und von Anwendungen. Zusätzlich empfehlen wir die folgenden Verfahren, und wir stellen Features bereit, die Sie zum Implementieren jener verwenden können:

-   Ausführen von Virenschutzsoftware/Antischadsoftware

-   Verwenden von Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) auf Endpunkten

## Ausführen von Virenschutzsoftware/Antischadsoftware

Azure bietet verschiedene Optionen für Virenschutz-/Antischadsoftwarelösungen. Ihre Verwaltung ist jedoch Ihnen überlassen. Beispielsweise müssen Sie sich entscheiden, wann Sie Überprüfungen vornehmen und Updates installieren. Sie können beim Erstellen des virtuellen Computers oder später eine Virenschutzsoftware-Unterstützung hinzufügen. Zurzeit werden drei Lösungen als Sicherheitserweiterungen angeboten, die sowohl auf neuen als auch auf vorhandenen virtuellen Computern installiert werden können:

-   [Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer][Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer]
-   [Gewusst wie: Installieren und Konfigurieren von Trend Micro Deep Security-as-a-Service auf einem virtuellen Azure-Computer][Gewusst wie: Installieren und Konfigurieren von Trend Micro Deep Security-as-a-Service auf einem virtuellen Azure-Computer]
-   [Deploying Antimalware Solutions on Azure Virtual Machines (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern) (in englischer Sprache)][Deploying Antimalware Solutions on Azure Virtual Machines (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern) (in englischer Sprache)]

## Verwenden von Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) auf Endpunkten virtueller Computer

Mit Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) können Sie den eingehenden Datenverkehr zu einem Endpunkt eines virtuellen Computers wahlweise zulassen oder verweigern. Diese Paketfilterungsfunktion bietet eine zusätzliche Schutzebene. Details in Bezug auf die Funktionsweise sowie Links zu Anweisungen finden Sie unter [Informationen zu Netzwerk-Zugriffssteuerungslisten (ACLs)][Informationen zu Netzwerk-Zugriffssteuerungslisten (ACLs)].

## Zusätzliche Ressourcen

[Ressourcen][Ressourcen] im Microsoft Azure-Vertrauenscenter

  [Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer]: http://go.microsoft.com/fwlink/p/?LinkId=404207
  [Gewusst wie: Installieren und Konfigurieren von Trend Micro Deep Security-as-a-Service auf einem virtuellen Azure-Computer]: http://go.microsoft.com/fwlink/p/?LinkId=404206
  [Deploying Antimalware Solutions on Azure Virtual Machines (Bereitstellen von Antischadsoftware-Lösungen auf virtuellen Azure-Computern) (in englischer Sprache)]: http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/
  [Informationen zu Netzwerk-Zugriffssteuerungslisten (ACLs)]: http://go.microsoft.com/fwlink/?LinkId=506655
  [Ressourcen]: http://azure.microsoft.com/de-de/support/trust-center/resources/

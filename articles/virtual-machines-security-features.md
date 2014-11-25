<properties title="Security offerings for Azure Virtual Machines" pageTitle="Security offerings for Azure Virtual Machines" description="Quick overview of key security features for Azure VMs and links to details" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Sicherheitsangebote für virtuelle Azure-Computer

Die Sicherheitsaufgaben und Konfiguration für virtuelle Azure-Computer sind größtenteils dieselben wie für alle anderen virtuellen oder physischen Computer. Dazu gehören Grundlagen wie Verwenden starker Passwörter, keine weithin bekannten Benutzernamen verwenden und das Einrichten von Richtlinien und Plänen für die Aktualisierung von Betriebssystem und Anwendungen. Außerdem empfehlen wird die folgenden Praktiken und stellen Funktionen bereit, die sie für deren Implementierung verwenden können:

-   Einsatz von Anti-Viren/-Malware-Software

-   Verwenden von Netzwerk-Zugriffssteuerungslisten (ACLs) an Endpunkten

## Einsatz von Anti-Viren/-Malware-Software

Azure bietet eine Reihe von Optionen für Anti-Viren/-Malware-Lösungen. Die Verwaltung liegt allerdings in Ihrer Hand. Sie müssen beispielsweise entscheiden, wann Sie Scans durchführen und Aktualisierungen installieren. Sie können Anti-Virus-Support beim Erstellen eines virtuellen Computers hinzufügen oder zu einem späteren Zeitpunkt. Aktuell werden drei Lösungen als Sicherheitserweiterung angeboten, die sich sowohl auf neuen als auch existierenden virtuellen Computern installieren lassen:

-   [So installieren und konfigurieren Sie Symantec Endpoint Protection auf einem Azure VM][So installieren und konfigurieren Sie Symantec Endpoint Protection auf einem Azure VM]
-   [So installieren und konfigurieren Sie Trend Micro Deep Security als Dienst auf einem Azure VM][So installieren und konfigurieren Sie Trend Micro Deep Security als Dienst auf einem Azure VM]
-   [Bereitstellen von Anti-Malware-Lösungen auf virtuellen Azure-Computern][Bereitstellen von Anti-Malware-Lösungen auf virtuellen Azure-Computern]

## Verwenden von Netzwerk-Zugriffssteuerungslisten (ACLs) an Endpunkten virtueller Computer

Mit Netzwerk-Zugriffssteuerungslisten (ACLs) können Sie eingehenden Traffic an Endpunkten virtueller Computer selektiv zulassen oder ablehnen. Diese Möglichkeit zur Paketfilterung sorgt für eine zusätzliche Sicherheitsebene. Details dazu, wie das funktioniert, sowie Links zu Anleitungen finden Sie unter [Über Netzwerk-Zugriffssteuerungslisten (ACLs)][Über Netzwerk-Zugriffssteuerungslisten (ACLs)].

  [So installieren und konfigurieren Sie Symantec Endpoint Protection auf einem Azure VM]: http://go.microsoft.com/fwlink/p/?LinkId=404207
  [So installieren und konfigurieren Sie Trend Micro Deep Security als Dienst auf einem Azure VM]: http://go.microsoft.com/fwlink/p/?LinkId=404206
  [Bereitstellen von Anti-Malware-Lösungen auf virtuellen Azure-Computern]: http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/

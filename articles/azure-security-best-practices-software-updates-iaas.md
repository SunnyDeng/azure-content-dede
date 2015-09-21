<properties
   pageTitle="Best Practices für Softwareupdates in Microsoft Azure IaaS | Microsoft Azure"
   description="In diesem Artikel werden verschiedene Best Practices für Softwareupdates in einer Microsoft Azure IaaS-Umgebung beschrieben. Er richtet sich an IT-Experten und Sicherheitsanalysten, deren tägliche Aufgaben das Änderungsmanagement, Softwareupdates und Assetverwaltung umfassen, oder die für Sicherheit und Compliance in ihrer Organisation verantwortlich sind."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriD"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/03/2015"
   ms.author="yurid"/>

#Best Practices für Softwareupdates in Microsoft Azure IaaS


Bevor wir hier näher auf die bewährten Methoden für eine Azure IaaS-Umgebung eingehen, soll zunächst erläutert werden, welche Szenarien für die Verwaltung von Softwareupdates es gibt. Unterstützung hierbei bietet das nachstehende Diagramm:

![Cloudmodelle und Verantwortlichkeiten](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

Im traditionellen Datencentermodell ist die gesamte Infrastruktur lokal aufgebaut, und die Verantwortung für die Verwaltung von Updates für Betriebssysteme, Anwendungen, Netzwerkgeräte (Router, Switches usw.) und Hardware (Firmware) liegt vollständig bei Ihnen. In einem IaaS-Szenario müssen Sie weiterhin die Updates für Betriebssysteme und Anwendungen verwalten. Die gesamte zugrunde liegende Infrastruktur für Betriebssysteme und Anwendungen jedoch wird von Microsoft verwaltet. In allen Modellen ist der Kunde weiterhin der Besitzer seiner Daten und verantwortlich für deren Schutz auf Endpunktebene.

In einem PaaS-Szenario haben Sie sogar noch weniger Verantwortung für Softwareupdates, da die Updateverwaltung für das Betriebssystem in der Verantwortung von Microsoft liegt. In einem IaaS-Szenario liegt die Verantwortung für Softwareupdates für den gesamten Stack bei Microsoft.

Dieselben Prinzipien gelten in einem Hybridszenario, bei der Ihr Unternehmen Azure IaaS-VMs verwendet, die mit lokalen Ressourcen kommunizieren (siehe nachstehendes Diagramm).

![Typisches Hybridszenario mit Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## Anfängliche Bewertung

Selbst wenn Ihr Unternehmen bereits ein System zur Updateverwaltung verwendet und Sie über Richtlinien zur Softwareaktualisierung verfügen, müssen Sie zuvor durchgeführte Richtlinienbewertungen regelmäßig überprüfen und basierend auf Ihren aktuellen Anforderungen aktualisieren. Dies bedeutet, dass Sie dem aktuellen Status der Ressourcen in Ihrem Unternehmen kennen müssen. Um dies zu erreichen, müssen Sie Folgendes kennen:

-   Die physischen und virtuellen Computer in Ihrem Unternehmen.

-   Betriebssysteme und Versionen, die auf jedem dieser physischen und virtuellen Computer ausgeführt werden.

-   Die aktuell auf jedem Computer installierten Softwareupdates (Service Pack-Versionen, Softwareupdate und weitere Änderungen).

-   Die Funktion, die jeder Computer in Ihrem Unternehmen erfüllt.

-   Die Anwendungen und Programme, die auf jedem Computer ausgeführt werden.

-   Informationen zu Besitzer und Kontakt für jeden Computer.

-   Die vorhandenen Assets in Ihrer Umgebung und ihr relativer Wert, um zu bestimmen, welche Bereiche die meiste Aufmerksamkeit und den höchsten Schutz erfordern.

-   Bekannte Sicherheitsprobleme und die Prozesse, die in Ihrem Unternehmen implementiert sind, um neue Sicherheitsprobleme zu erkennen oder die Sicherheitsstufe zu ändern.

-   Maßnahmen, die zum Schutz Ihrer Umgebung ergriffen wurden.

Sie sollten diese Informationen regelmäßig aktualisieren, und die Informationen sollten allen Personen, die an Ihrem Prozess zur Softwareupdateverwaltung beteiligt sind, jederzeit zur Verfügung stehen.

## Einrichten einer Baseline

Ein wichtiger Aspekt bei der Verwaltung von Softwareupdates ist das Erstellen anfänglicher Standardinstallationen für Betriebssystemversionen, Anwendungen und Hardware für Computer in Ihrem Unternehmen. Diese Standardinstallationen werden als Baselines bezeichnet. Eine Baseline ist die Konfiguration eines Produkts oder Systems, die zu einem bestimmten Zeitpunkt eingerichtet wurde. Eine Baseline für eine Anwendung oder ein Betriebssystem beispielsweise bietet die Möglichkeit, einen Computer oder Dienst in einem bestimmten Zustand wiederherzustellen.

Baselines bilden die Grundlage zum Ermitteln und Beheben potenzieller Probleme und zur Vereinfachung des Verwaltungsprozesses von Softwareupdates. Beides wird dadurch erreicht, dass einerseits die Anzahl von Softwareupdates verringert wird, die Sie in Ihrem Unternehmen bereitstellen müssen, und andererseits die Möglichkeiten verbessert werden, die Einhaltung der Vorgaben zu überwachen.

Nachdem Sie eine anfängliche Überprüfung Ihres Unternehmens durchgeführt haben, sollten Sie die hierbei gewonnenen Informationen zum Definieren einer operativen Baseline für die IT-Komponenten innerhalb Ihrer Produktionsumgebung verwenden. Es können verschiedene Baselines erforderlich sein, je nach verwendeten Hardwaretypen und der Software, die in der Produktion bereitgestellt wird.

Beispielsweise ist für Windows Server 2012-Server ein Softwareupdate erforderlich, um ein Aufhängen beim Herunterfahren des Servers zu verhindern. Eine Baseline für diese Server sollte dieses Softwareupdate umfassen.

In großen Organisationen ist es häufig sinnvoll, die Computer in Assetkategorien zu unterteilen und für jede Kategorie eine Standardbaseline einzuhalten, indem die selben Versionen von Software und Softwareupdates verwendet werden. Sie können diese Assetkategorien anschließend bei der Priorisierung einer Softwareupdateverteilung nutzen.

## Abonnieren von Benachrichtigungsdiensten für geeignete Softwareupdates

Nachdem Sie eine anfängliche Überprüfung der Software in Ihrem Unternehmen durchgeführt haben, sollten Sie die beste Methode zum Erhalt von Benachrichtigungen über neue Softwareupdates für alle Softwareprodukte und -versionen ermitteln. Je nach Softwareprodukt kann die Benachrichtigung per E-Mail, auf Websites oder in Form von Computerveröffentlichungen erfolgen.

Beispielsweise reagiert das Microsoft Security Response Center (MSRC) auf alle sicherheitsbezogenen Bedenken zu Microsoft-Produkten und stellt den Microsoft Security Bulletin Service bereit, einen kostenlosen Dienst, bei dem Sie per E-Mail über neu identifizierte Sicherheitsrisiken und Softwareupdates benachrichtigt werden, die diese Sicherheitsrisiken beheben. Sie können diesen Dienst unter <http://www.microsoft.com/technet/security/bulletin/notify.mspx> abonnieren.

## Überlegungen zu Softwareupdates

Nachdem Sie eine anfängliche Überprüfung der Software in Ihrem Unternehmen durchgeführt haben, sollten Sie die Anforderungen zum Einrichten Ihres Systems zur Softwareupdateverwaltung ermitteln. Diese hängen davon ab, welches System für die Softwareupdateverwaltung Sie verwenden. Wenn Sie WSUS einsetzen, lesen Sie [Best Practices für Windows Server Update Services](https://technet.microsoft.com/de-DE/library/Cc708536), bei Verwendung von System Center finden Sie entsprechende Informationen unter [Planen von Softwareupdates in Configuration Manager](https://technet.microsoft.com/de-DE/library/gg712696).

Es gibt jedoch einige allgemeine Überlegungen und Best Practices, die Sie unabhängig von der eingesetzten Lösung anwenden können. Diese werden in den nächsten Abschnitten beschrieben.

### Einrichten der Umgebung

Berücksichtigen Sie die folgenden Best Practices, wenn Sie die Einrichtung einer Umgebung zum Verwalten von Softwareupdates planen:

-   **Stellen Sie Updatesammlungen für Produktionssoftware basierend auf festen Kriterien zusammen**: Im Allgemeinen sollten feste Kriterien bei der Auswahl Ihres Softwareupdatebestands und dessen Verteilung gelten, um alle Phasen im Prozess der Softwareupdateverwaltung zu vereinfachen. Zu diesen festen Kriterien können die installierte Clientbetriebssystemversion und die Service Pack-Stufe, Systemrolle oder Zielorganisation gehören.

-   **Stellen Sie Präproduktionssammlungen zusammen, die Referenzcomputer enthalten**: Die Präproduktionssammlung sollte repräsentative Konfigurationen von Betriebssystemversionen, Branchenanwendungen und anderen Softwarekomponenten enthalten, die in Ihrem Unternehmen ausgeführt wird.

Sie sollten außerdem berücksichtigen, wo der Softwareupdateserver platziert wird – beispielsweise in der Azure IaaS-Infrastruktur in der Cloud oder lokal. Dies ist eine wichtige Entscheidung, da Sie die Menge des Datenverkehrs zwischen lokalen Ressourcen und Azure-Infrastruktur auswerten müssen. Weitere Informationen zur Verbindung Ihrer lokalen Infrastruktur mit Azure finden Sie unter [Verbinden eines lokalen Netzwerks mit einem virtuellen Microsoft Azure-Netzwerk](https://technet.microsoft.com/de-DE/library/Dn786406.aspx).

Die Entwurfsoptionen zur Platzierung des Updateservers richten sich auch nach Ihrer aktuellen Infrastruktur und dem Softwareupdatesystem, das Sie derzeit verwenden. Wenn Sie WSUS verwenden, lesen Sie [Bereitstellen von Windows Server Update Services in Ihrer Organisation](https://technet.microsoft.com/de-DE/library/hh852340.aspx), bei Verwendung von System Center Configuration Manager finden Sie weitere Informationen unter [Planen von Standorten und Hierarchien in Configuration Manager](https://technet.microsoft.com/de-DE/library/Gg712681.aspx).

### Sicherung

Regelmäßige Sicherungen sind nicht nur für die Plattform zur Softwareupdateverwaltung selbst wichtig, sondern auch für die Server, die aktualisiert werden. Für Organisationen, die einen [Änderungsmangementprozess](https://technet.microsoft.com/de-DE/library/cc543216.aspx) einsetzen, ist es erforderlich, dass die IT Gründe für die Notwendigkeit von Serverupdates, die geschätzte Ausfallzeit und mögliche Auswirkungen benennt. Um sicherzustellen, dass Sie über eine Rollbackkonfiguration für den Fall verfügen, dass ein Update nicht erfolgreich durchgeführt werden kann, müssen Sie das System regelmäßig sichern.

Für Azure IaaS stehen folgende Sicherungsoptionen zur Verfügung:

-   [Azure IaaS workload protection using Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/) (in englischer Sprache)

-   [Sichern von virtuellen Azure-Computern](https://azure.microsoft.com/de-DE/documentation/articles/backup-azure-vms/)

### Überwachung

Sie sollten regelmäßige Berichte für jedes autorisierte Softwareupdate durchführen, um die Anzahl von fehlenden oder installierten Updates sowie von Updates mit unvollständigem Status zu überwachen. Ähnlich sollten Berichte zu Softwareupdates erstellt werden, die noch nicht autorisiert sind, um spätere Bereitstellungsentscheidungen zu erleichtern.

Sie sollen außerdem erwägen, die folgenden Aufgaben auszuführen:

-   Durchführen und Überwachen von anwendbaren und installierten Sicherheitsupdates für alle Computer in Ihrem Unternehmen.

-   Autorisieren und Bereitstellen der Updates auf geeigneten Computern.

-   Nachverfolgen des Bestands und des Updateinstallationsstatus und -fortschritts für alle Computer in Ihrem Unternehmen.

Zusätzlich zu den allgemeinen Überlegungen, die in diesem Artikel vorgestellt wurden, sollten Sie auch die Best Practices für jedes Produkt berücksichtigen. Beispiel: Wenn Sie über einen virtuellen SQL Server-Computer in Azure verfügen, sollten Sie sicherstellen, dass die Empfehlungen für Softwareupdates für dieses Produkt eingehalten werden.

## Nächste Schritte

Verwenden Sie die in diesem Artikel beschriebenen Richtlinien, um die optimalen Optionen für Softwareupdates für virtuelle Computer in Azure IaaS zu ermitteln. Es gibt viele Ähnlichkeiten zwischen den Best Practices für Softwareupdates in einem traditionellen Datencenter und in Azure IaaS. Deshalb wird empfohlen, dass Sie Azure-VMs in Ihre aktuellen Richtlinien für Softwareupdates einbeziehen und die relevanten Best Practices aus diesem Artikel in Ihren allgemeinen Softwareupdateprozess einbinden.

<!---HONumber=Sept15_HO2-->
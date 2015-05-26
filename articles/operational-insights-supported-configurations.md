<properties 
   pageTitle="Unterstützte Konfigurationen für Operational Insights"
   description="Erfahren Sie mehr über die Konfigurationen, die für Operational Insights erforderlich sind"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Unterstützte Konfigurationen für Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../includes/operational-insights-note-moms.md)]

Was ist für die Nutzung von Operational Insights erforderlich? Lesen Sie zur Vorbereitung auf Operational Insights die folgenden Informationen.


## Konfiguration für System Center 2012 Operations Manager

In System Center 2012 R2 oder System Center 2012 SP1 R2 können Sie Operational Insights als in Operations Manager zugeordneten Dienst verwenden. Dadurch können Sie in Operations Manager die Betriebskonsole verwenden, um Operational Insights-Warnungen und Konfigurationsinformationen anzuzeigen. Wenn Sie Operational Insights als zugeordneten Dienst in Operations Manager verwenden, kommuniziert der Agent direkt mit dem Verwaltungsserver, der wiederum mit dem Operational Insights-Dienst kommuniziert.

Für das Verwenden von Operational Insights als zugeordneten Dienst gelten die folgenden Voraussetzungen:


- Die Integration zwischen System Center 2012 SP1 Operations Manager und Operational Insights erfordert aktualisierte Management Packs, die im [Operational Insights-Connector für Operations Manager](https://www.microsoft.com/de-de/download/details.aspx?id=38199) enthalten sind. Sie können die Management Packs aus dem [Operational Insights-Connector für Operations Manager](https://www.microsoft.com/de-de/download/details.aspx?id=38199) herunterladen und installieren.

- System Center 2012 SP1: Operations Manager Updaterollup 6, wenngleich Updaterollup 7 empfohlen wird. Dieses Update muss für das Szenario mit Operational Insights als zugeordneten Dienst auf den Verwaltungsserver, Agents und die Betriebskonsole angewendet werden.

- System Center 2012 R2: Operations Manager-Updaterollup 2, wenngleich Updaterollup 3 empfohlen wird. Dieses Update muss für das Szenario mit Operational Insights als zugeordneten Dienst auf den Verwaltungsserver, Agents und die Betriebskonsole angewendet werden.

- Um Kapazitätsverwaltungsdaten anzuzeigen, müssen Sie die Operations Manager-Verbindung mit Virtual Machine Manager (VMM) aktivieren. Zusätzliche Informationen zum Verbinden der Systeme finden Sie unter [Verbinden von VMM mit Operations Manager](https://technet.microsoft.com/de-de/library/hh882396.aspx).

Unter [Anzeigen von Operational Insights-Warnungen](http://go.microsoft.com/fwlink/?LinkID=293793) finden Sie Anweisungen zur Installation und Konfiguration.

Wenn Sie Operational Insights-Warnungen zu SharePoint Server 2010, Lync Server 2013, Lync Server 2010 oder System Center 2012 SP1 - Virtual Machine Manager anzeigen möchten, müssen Sie ein ausführendes Konto für diese Arbeitsauslastungen konfigurieren. Siehe die folgenden Informationen:


- [Festlegen des ausführenden Kontos für SharePoint](operational-insights-run-as.md)

- [Festlegen des ausführenden Kontos für Lync Server](operative-insights-run-as.md)

- [Festlegen des ausführenden Kontos für Virtual Machine Manager (VMM)](operative-insights-run-as.md)

### Operations Manager-Betriebssysteme

Operations Manager-Agents werden auf einer Vielzahl von Computern unterstützt. Details zur Unterstützung von Agents finden Sie unter [Systemanforderungen: System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx).

### Erforderliche Software für Operations Manager

Um Kapazitätsverwaltungsdaten anzuzeigen, müssen Sie die Operations Manager-Verbindung mit Virtual Machine Manager (VMM) aktivieren. Zusätzliche Informationen zum Verbinden der Systeme finden Sie unter [Verbinden von VMM mit Operations Manager](https://technet.microsoft.com/de-de/library/hh882396.aspx).

## Agents mit direkter Verbindung mit Operational Insights

Der Agent für eine direkte Verbindung mit dem Dienst ist Microsoft Monitoring Agent. Seine Systemanforderungen sind auf der Seite [Microsoft Download Center](https://www.microsoft.com/de-de/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True) aufgeführt.

## Browser

Sie können einen der folgenden Browser mit Operational Insights verwenden:

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 oder Internet Explorer 7

- Mozilla Firefox 3.5 oder höher

Unabhängig vom Browser, den Sie verwenden, müssen Sie auch Microsoft Silverlight 4 installieren.

## Analysierbare Technologien

Die Operational Insights-Funktion zur Konfigurationsbewertung analysiert die folgenden Arbeitsauslastungen:

- WindowsServer 2012 und Microsoft Hyper-V Server 2012

- Windows Server 2008 und Windows Server 2008 R2, einschließlich:
    - Active Directory
	- Hyper-V-Host
	- Allgemeines Betriebssystem

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - SQL Server-Datenbankmodul

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 und Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 und Lync Server 2010

- System Center 2012 SP1 - Virtual Machine Manager

Für SQL Server werden die folgenden 32-Bit- und 64-Bit-Editionen für die Analyse unterstützt:

- SQL Server 2008 und 2008 R2 Enterprise

- SQL Server 2008 und 2008 R2 Standard

- SQL Server 2008 und 2008 R2 Workgroup

- SQL Server 2008 und 2008 R2 Web

- SQL Server 2008 und 2008 R2 Express

Darüber hinaus wird bei Ausführung in der WOW64-Implementierung die 32-Bit-Edition von SQL Server unterstützt.


<!--HONumber=54-->
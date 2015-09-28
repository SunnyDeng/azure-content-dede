<properties
   pageTitle="Unterstützte Konfigurationen für Operational Insights"
   description="Weitere Informationen zu den erforderlichen Konfigurationen für Operational Insights"
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
   ms.date="09/10/2015"
   ms.author="banders" />

# Unterstützte Konfigurationen für Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Was benötigen Sie für die Nutzung von Operational Insights? Sehen Sie sich die folgenden Informationen an, um sich auf die Nutzung von Operational Insights vorzubereiten.


## Konfiguration für System Center 2012 Operations Manager

Sie können Operational Insights als angefügten Dienst von Operations Manager in System Center 2012 R2 oder System Center 2012 SP1 R2 verwenden. So können Sie die Betriebskonsole in Operations Manager zum Anzeigen von Operational Insights-Warnungen und -Konfigurationsinformationen verwenden. Wenn Sie Operational Insights als angefügten Dienst in Operations Manager verwenden, kommuniziert der Agent direkt mit dem Verwaltungsserver, der wiederum mit dem Operational Insights-Dienst kommuniziert.

Für die Nutzung von Operational Insights als angefügten Dienst gelten die folgenden Voraussetzungen:


- Für die Integration von System Center 2012 SP1 Operations Manager und Operational Insights sind aktualisierte Management Packs erforderlich, die im [Operational Insights Connector für Operations Manager](https://www.microsoft.com/de-DE/download/details.aspx?id=38199) enthalten sind. Sie können die Management Packs auch unter [Operational Insights-Connector für Operations Manager](https://www.microsoft.com/de-DE/download/details.aspx?id=38199) herunterladen und installieren.

- System Center 2012 SP1: Operations Manager-Updaterollup 6 (Updaterollup 7 bevorzugt). Dieses Update muss im Rahmen eines angefügten Diensts auf den Verwaltungsserver, Agents und die Betriebskonsole für Operational Insights angewendet werden.

- System Center 2012 R2: Operations Manager-Updaterollup 2 (Updaterollup 3 bevorzugt). Dieses Update muss im Rahmen eines angefügten Diensts auf den Verwaltungsserver, Agents und die Betriebskonsole für Operational Insights angewendet werden.

- Um Kapazitätsverwaltungsdaten anzuzeigen, müssen Sie die Operations Manager-Verbindung mit Virtual Machine Manager (VMM) aktivieren. Zusätzliche Informationen zum Verbinden der Systeme finden Sie unter [Verbinden von VMM mit Operations Manager](https://technet.microsoft.com/de-DE/library/hh882396.aspx).

Anleitungen zur Installation und Konfiguration finden Sie unter [Anzeigen von Operational Insights-Warnungen](http://go.microsoft.com/fwlink/?LinkID=293793).

Wenn Sie Operational Insights-Warnungen zu SharePoint Server 2010, Lync Server 2013, Lync Server 2010 oder System Center 2012 SP1 – Virtual Machine Manager anzeigen möchten, müssen Sie für diese Workloads ein „Ausführendes Konto“ konfigurieren. Weitere Informationen zum Festlegen von ausführenden Konten finden Sie unter [Überlegungen zu Operations Manager mit Operational Insights](operational-insights-operations-manager.md).


### Operations Manager-Betriebssysteme

Operations Manager-Agents werden auf vielen verschiedenen Computern unterstützt. Ausführliche Informationen zur Unterstützung von Agents finden Sie unter [Systemanforderungen: System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx).

### Erforderliche Software für Operations Manager

Zum Anzeigen von Kapazitätsverwaltungsdaten müssen Sie die Operations Manager-Verbindung mit VMM aktivieren. Zusätzliche Informationen zum Verbinden der Systeme finden Sie unter [Verbinden von VMM mit Operations Manager](https://technet.microsoft.com/de-DE/library/hh882396.aspx).

## Agents mit direkter Operational Insights-Verbindung

Der Agent, der zum Herstellen der direkten Verbindung mit dem Dienst verwendet wird, ist der Microsoft Monitoring Agent. Die dazugehörigen Systemanforderungen sind auf der Seite [Microsoft Download Center](https://www.microsoft.com/de-DE/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True) aufgeführt.

## Browser

Sie können für Operational Insights folgende Browser verwenden:

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 oder Internet Explorer 7

- Mozilla Firefox 3.5 oder höher

Unabhängig vom verwendeten Browser müssen Sie auch Microsoft Silverlight 4 installieren.

## Technologien, die analysiert werden können

Mit der Konfigurationsbewertung von Operational Insights werden die folgenden Workloads analysiert:

- Windows Server 2012 und Microsoft Hyper-V Server 2012

- Windows Server 2008 und Windows Server 2008 R2, einschließlich:
    - Active Directory
	- Hyper-V-Host
	- Allgemeines Betriebssystem

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - SQL Server-Datenbankmodul

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 und Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 und Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

Für SQL Server werden die folgenden 32-Bit- und 64-Bit-Editionen für Analysezwecke unterstützt:

- SQL Server 2008 und 2008 R2 Enterprise

- SQL Server 2008 und 2008 R2 Standard

- SQL Server 2008 und 2008 R2 Workgroup

- SQL Server 2008 und 2008 R2 Web

- SQL Server 2008 und 2008 R2 Express

Darüber hinaus wird bei der Ausführung in der WOW64-Implementierung die 32-Bit-Edition von SQL Server unterstützt.

<!---HONumber=Sept15_HO3-->
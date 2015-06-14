<properties 
	pageTitle="Optionen für die Übertragung von gespeicherten Daten in die Cloud | Azure" 
	description="Hilfestellung bei der Auswahl der besten Option für die Übertragung von Daten aus einer lokalen Bereitstellung oder anderen Cloudquellen in Microsoft Azure für eine erweiterte Analyse." 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="01/07/2014" 
	ms.author="cgronlun"/>

# Optionen für die Übertragung von gespeicherten Daten in die Cloud

Dieser Artikel enthält Anleitungen zur Auswahl der geeigneten Option zum Übertragen von Daten aus einer lokalen Bereitstellung oder anderen Cloudquellen in Microsoft Azure für eine erweiterte Analyse. Das Übertragen von Daten kann sehr viel Zeit beanspruchen und erfordert geeignete Sicherheitsmaßnahmen. 

Themen in diesem Artikel:

* [Azure Import/Export-Dienst für Blob-Speicher](#blob)
* [AZCopy-Hilfsprogramm](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Azure Data Factory (Vorschau)](#data-factory)
* [Azure SQL-Datenbankmigrationstools](#tools)
* [Azure SQL-Datensynchronisierung (Vorschau)](#data-sync)
* [Azure Event Hubs](#event-hubs)
* [Weitere Optionen für die Datenübertragung](#other)
* [Auswählen der geeigneten Option für die Datenübertragung](#choose)


## Azure Import/Export-Dienst für Blob-Speicher

Mithilfe des Azure Import-/Export-Diensts können Sie große Mengen an Dateidaten an Azure Blob-Speicher übertragen, beispielsweise wenn das Hoch- oder Herunterladen über das Netzwerk unverhältnismäßig teuer oder nicht realisierbar ist. Der Upload oder Download großer Datensätze über das Netzwerk kann sehr viel Zeit in Anspruch nehmen. Beispielsweise dauert es einen Monat, 10 TB Daten über eine T3-Leitung (44,7 MBit/s) zu übertragen. Mit dem Microsoft Azure Import/Export-Dienst können Kunden die Festplatte einsenden, um die erforderliche Zeit für den Datenupload- oder -download zu verkürzen. Planen Sie mehrere Tage einschließlich Versand ein. 

Sie können eine oder mehrere Festplatten mit Daten an ein Azure-Datacenter schicken, um eine große Menge an Dateidaten auf Blob-Speicher zu übertragen. In diesem Rechenzentrum werden Ihre Daten in Ihr Speicherkonto hochgeladen. Ähnlich funktioniert das Exportieren von Daten auf Blob-Speicher: Sie können leere Festplatten an ein Azure-Datacenter schicken. Dort werden Blob-Daten von Ihrem Speicherkonto auf Ihre Festplatten kopiert und an Sie zurückgesendet. Bevor Sie ein Laufwerk einsenden, das Daten enthält, verschlüsseln Sie die Daten auf dem Laufwerk. Wenn der Import-/Export-Dienst Ihre Daten exportiert, um Ihnen diese zu senden, werden die Daten vor dem Versand ebenfalls verschlüsselt.

Weitere Informationen finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in Blob-Speicher][import-export].


## AZCopy-Hilfsprogramm

AzCopy ist ein Befehlszeilenprogramm, das zum leistungsstarken Hochladen, Herunterladen und Kopieren von Daten zu und vom Microsoft Azure Blob-, Datei- und Tabellenspeicher konzipiert wurde. Dieses Hilfsprogramm eignet sich für das einmalige Verschieben von Daten zwischen Azure Storage und lokalen Bereitstellungen, wenn die Übertragung per Netzwerk praktikabel ist. Weitere Informationen finden Sie unter [Erste Schritte mit dem AzCopy-Befehlszeilenprogramm][azcopy].

> [AZURE.NOTE] Linux-Benutzer: [Download von ACP, AzCopy für Linux](http://www.paratools.com/acp)


## Azure PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Sie können mithilfe von Azure PowerShell Daten in Blob-Speicher hochladen, um die Daten einer erweiterten Analyse zu unterziehen oder MapReduce-Aufträge auszuführen.

Siehe auch:

* [Hochladen von Daten in Blob-Speicher mit Azure PowerShell][upload]
* [Installieren und Konfigurieren von Azure PowerShell][install]


## Azure Data Factory (Vorschau)

Azure Data Factory ist ein vollständig verwalteter Dienst für das Kombinieren von Diensten zur Datenspeicherung, -verschiebung und Verarbeitung in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines.

Entwickler können datengesteuerte Workflows erstellen, die semi-strukturierte, unstrukturierte und strukturierte Daten aus lokalen Diensten (über Data Management Gateway), cloudbasierten und Internetdiensten zusammenführen, aggregieren und transformieren. Darüber hinaus kann eine komplexe Datenverarbeitung über einfache JSON-Skripts eingerichtet werden. Die Ergebnisdaten können für eine erweiterte Analyse in Azure Storage oder Azure SQL-Datenbank gespeichert werden.

Insbesondere können Entwickler regelmäßige Kopieraktivitäten zwischen verschiedenen Quellen und Zielen konfigurieren, wie gezeigt im Abschnitt "[Unterstützte Eingaben und Ausgaben](data-factory-copy-activity.md#SupportedSourcesAndSinks)" im Thema [Kopieren von Daten mit Azure Data Factory](data-factory-copy-activity.md), In diesem Thema werden auch Eigenschaften für unterschiedliche Arten von Datenspeichern, Spaltenzuordnungen, Serialisierungsformaten und Typenverarbeitungen behandelt.

Der Dienst kann Fehler beim automatischen Start beheben und ermöglicht beim Verschieben von Daten die Formatierung von einem Format in ein anderes. Informationen zum Definieren einer Kopieraktivität finden Sie unter [Erste Schritte mit Data Factory][start]. Das Registrieren von Datenspeicher und die Gatewayinstallation werden unter [Aktivieren Ihrer Pipelines zum Arbeiten mit lokalen Daten][pipelines] beschrieben. 

Siehe auch:

* [Einführung in Data Factory][intro]

## Azure SQL-Datenbankmigrationstools

Es stehen viele Tools für die erfolgreiche Migration von lokalen SQL Server- und Nicht-SQL Server-Datenbanken zu Azure SQL-Datenbank zur Verfügung. Welches Tool am besten für Ihr Szenario geeignet ist, hängt von Art, Größe und Komplexität der zu migrierenden Datenbank ab:

* Sie können sowohl das Schema als auch die Daten einer vorhandenen Azure SQL-Datenbank migrieren, indem Sie die Datenbank exportieren, die Datei in ein Azure Blob-Speicherkonto exportieren und anschließend als neue Azure SQL-Datenbank importieren. Datei bei diesem Export erstellte Datei wird als BACPAC-Datei bezeichnet. Weitere Informationen finden Sie unter [Vorgehensweise: Verwenden des Import/Export-Diensts in Azure SQL-Datenbank)][sql-import].
* Mithilfe der Datenbankkopierfunktion wird eine neue Datenbank in Azure erstellt, bei der es sich um eine transaktional konsistente Kopie der vorhandenen Azure SQL-Datenbank handelt. Weitere Informationen finden Sie unter [Kopieren von Datenbanken in Azure SQL-Datenbank][sql-copy].
* Für komplexe Transaktionen von Daten können die SQL Server Integration Services (SSIS) eingesetzt werden. SSIS kann zum Verschieben von Daten in die und aus der Azure SQL-Datenbank verwendet werden. Weitere Informationen finden Sie unter [Vorgehensweise: Migrieren einer Datenbank mithilfe von Integration Services in eine Azure SQL-Datenbank][integrate] und [SSIS für die Azure- und Hybriddatenverschiebung][SSIS].
* Der Import/Export-Assistent von SQL Server bietet eine einfache Möglichkeit zum Erstellen eines SSIS-Pakets zum Migrieren von Daten. Nachdem die Quelle und das Ziel konfiguriert wurden, können Sie grundlegende Datentransformationen angeben. Diese Pakete können gespeichert, geändert, ausgeführt und als Auftrag geplant werden. Weitere Informationen finden Sie unter [Vorgehensweise: Migrieren einer Datenbank mithilfe des Import/Export-Assistenten in eine Azure SQL-Datenbank][wizard].
* Der SQL-Datenbankmigrations-Assistent ist ein Tool zur Migration von Schema und Daten zwischen lokalen SQL Server- und Azure SQL-Datenbanken sowie zwischen Azure SQL-Datenbankservern. Das Tool untersucht außerdem Ablaufverfolgungsdateien und Skripts auf Kompatibilitätsprobleme mit Azure SQL-Datenbank. Weitere Informationen finden Sie unter [Vorgehensweise: Verwenden des SQL-Datenbankmigrations-Assistenten][use-wizard].
* Das Hilfsprogramm "bpc" kann zum Importieren großer Mengen an neuen Zielen in SQL Server-Tabellen oder zum Exportieren von Daten aus Tabellen in Datendateien verwendet werden. Weitere Informationen finden Sie unter [Vorgehensweise: Verwenden von "bcp" zum Migrieren einer Datenbank zu einer Azure SQL-Datenbank][bcp].

Siehe auch:

* [Migrieren von Datenbanken zur Azure SQL-Datenbank][migrate]
 

## Azure SQL-Datensynchronisierung (Vorschau)

SQL-Datensynchronisierung (Vorschau) ermöglicht das Erstellen und Planen regelmäßiger Synchronisierungen zwischen Azure SQL-Datenbank und Datenbanken, die in SQL Server oder Azure SQL-Datenbank gehostet werden.

SQL-Datensynchronisierung eignet sich für Entwickler, die Deltaänderungen zwischen lokalen und Azure-Clouddatenbanken synchronisieren möchten. Siehe [SQL-Datensynchronisierung][sync].

##	Azure Event Hubs

Microsoft Azure Event Hubs ist ein Dienst zur Ereignisverarbeitung, der riesige Mengen an Ereignis- und Telemetriedaten erfassen kann und gleichzeitig eine niedrige Latenz und hohe Zuverlässigkeit bietet. Dieser Dienst ist, in Kombination mit anderen Downstreamdiensten, besonders für Anwendungsinstrumentierung, Benutzeroberflächen- oder Workflowverarbeitung sowie für Internet der Dinge-Szenarien geeignet.

Entwickler können Code zum Senden von Daten an Event Hub schreiben, die Daten verarbeiten und in Azure Blob speichern, oder die Daten zur späteren Verarbeitung in Azure SQL-Datenbank speichern.

Alternativ können Entwickler Azure Stream Analytics nutzen, einen vollständig verwalteten Dienst, der eine skalierbare Verarbeitung komplexer Ereignisse durch das Streamen von Daten für die Ausgabe bereitstellt. Entwickler können einen Datenstrom in Azure Event Hub auswählen, geben das Format zum Serialisieren von eingehenden Ereignissen an (z. B. JSON, CSV oder Avro) und fügen dann den Ausgabespeicherort hinzu, darunter Azure-Blob oder Azure SQL-Datenbank.

Siehe:

* [Event Hubs - Dienstinformationen](/services/event-hubs/)
* [Übersicht über Event Hubs][overview]
* [Einführung in Azure Stream Analytics][stream]

## Weitere Optionen für die Datenübertragung

Hybridverbindungen bieten eine einfache und praktische Möglichkeit, um Azure-Websites und Azure Mobile Services mit lokalen Ressourcen zu verbinden. Entwickler können eine Website zum Verschieben von Daten aus lokalen Bereitstellungen nach Azure erstellen. Weitere Informationen finden Sie unter [Übersicht über Hybridverbindungen][hybrid].

Mit [Virtual Network](/services/virtual-network/)können Sie Tools zur Datenintegration in virtuellen Azure-Computern ausführen, um eine sichere Verbindung mit lokalen SQL Server-Datenbanken in Ihrem lokalen Datencenter herzustellen. Nur virtuelle Computer und Dienste innerhalb desselben virtuellen Netzwerks können sich gegenseitig identifizieren oder sich miteinander verbinden. Falls gewünscht, können Sie mit [ExpressRoute](/services/expressroute/) über Ihren Netzwerkdienstanbieter eine direkte Verbindung mit Azure herstellen und das öffentliche Internet vollständig umgehen.

[Azure Marketplace](?../source=datamarket.md) bietet Partnerlösungen, die das Verschieben von Daten nach Azure ermöglichen, z. B. Storm Managed File Transfer.

## Auswählen der geeigneten Option für die Datenübertragung

### Entscheidungsbaum

![Entscheidungshilfe bei Auswahl der geeigneten Cloudoption für die Datenübertragung][decision]

Hinweise zum Entscheidungsbaum:

* SQL-Datensynchronisierung (Vorschau) löst eine Synchronisierung geänderter Daten aus.
* Data Factory (Vorschau) unterstützt das Kopieren von Daten zwischen Azure Storage, Azure SQL-Datenbank und lokalen SQL Server-Datenbanken. 
* Neben der Verwendung von Data Factory können Sie vorhandene SSIS-Pakete erweitern, um Daten nach einem Zeitplan in die Cloud zu verschieben.

### Verschieben von Daten im GB-Bereich

<table border="1">
<tr>
<th>Verschieben von Daten</th>
<th>Einmalig</th>
<th>Geplant</th>
<th>Überlegungen</th>
</tr>

<tr>
<td><p>Datei nach Azure Storage</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Azure Import/Export</a></li>
</ul>
</td>
<td><p>-</p>
</td>
<td><p>Verwenden Sie anstelle von AzCopy, acp und Azure PowerShell den Azure Import/Export-Dienst, wenn eine Datenübertragung über das Netzwerk nicht praktikabel ist. Zusätzlich zum Versand der Festplatte an das Datencenter kann der Dienst mehrere Tage in Anspruch nehmen.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server nach Azure SQL-Datenbank</li>
<li>Azure SQL-Datenbank nach SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">SQL-Datenbankmigrationstools</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>Es wird empfohlen, anstelle der Migrationstools Azure Data Factory (Vorschau) zu verwenden, das in Ausnahmefällen eine Formatkonvertierung und eine automatische Auftragswiederherstellung bietet. Wenn SQL Server lokal verwendet wird, ist Data Management Gateway zur Verbindungsherstellung mit SQL Server erforderlich.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Azure Storage nach Azure SQL-Datenbank</li>
<li>Azure SQL-Datenbank nach Azure Storage</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>Azure Data Factory (Vorschau) bietet in Ausnahmefällen eine Formatkonvertierung und eine automatische Auftragswiederherstellung. Wenn SQL Server lokal verwendet wird, ist Data Management Gateway zur Verbindungsherstellung mit SQL Server erforderlich.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server nach SQL-Datensynchronisierung</li>
<li>SQL-Datensynchronisierung nach Azure SQL-Datenbank</li>
</ul>
</td>
<td><p>-</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">SQL-Datensynchronisierung</a></p>
</td>
<td><p>SQL-Datensynchronisierung (Vorschau) synchronisiert die Daten nach Synchronisierungsgruppen, die die Datenbanken, Tabellen und Spalten zur Synchronisierung sowie den Synchronisierungszeitplan definieren.</p>
</td>
</tr>

</table>



### Verschieben von Daten im TB-Bereich

<table border="1">
<tr>
<th>Verschieben von Daten</th>
<th>Einmalig</th>
<th>Geplant</th>
<th>Überlegungen</th>
</tr>

<tr>
<td><p>Datei nach Azure Storage</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Azure Import/Export</a></p>
</td>
<td><p>-</p>
</td>
<td><p>Zusätzlich zum Versand der Festplatte an das Datencenter kann der Dienst mehrere Tage in Anspruch nehmen.</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Azure Import/Export-Dienst für Blob-Speicher]: #blob
[AZCopy-Hilfsprogramm]: #azcopy-utility
[Azure PowerShell]: #ps
[Azure Data Factory (Vorschau)]: #data-factory
[Azure SQL-Datenbankmigrationstools]: #tools
[Azure SQL-Datensynchronisierung (Vorschau)]: #data-sync
[Azure Event Hubs]: #event-hubs
[Weitere Optionen für die Datenübertragung]: #other
[Auswählen der geeigneten Option für die Datenübertragung]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: ../storage-import-export-service.md
[azcopy]: ../storage-use-azcopy.md
[upload]: ../hdinsight-upload-data.md#powershell
[install]: ../install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: ../stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: ../integration-hybrid-connection-overview.md

<!--HONumber=52--> 
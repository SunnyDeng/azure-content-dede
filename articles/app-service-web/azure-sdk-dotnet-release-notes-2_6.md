<properties 
   pageTitle="Versionshinweise zu Azure SDK für .NET 2.6" 
   description="Versionshinweise zu Azure SDK für .NET 2.6" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="12/02/2015"
   ms.author="juliako"/>


# Versionshinweise zu Azure SDK für .NET 2.6

Dieses Dokument enthält die Versionshinweise zu Azure SDK für .NET 2.6-Version.

Mit Azure SDK 2.6 können Sie Cloud-Dienst-Anwendungen (PaaS) entwickeln, die auf .NET 4.5.2 oder .NET 4.6 gedacht sind. Voraussetzung ist, dass Sie das .NET Ziel-Framework manuell auf der Cloud-Dienst-Rolle installieren. Siehe [Installieren Sie .NET auf einer Cloud-Dienst-Rolle](http://go.microsoft.com/fwlink/?LinkID=309796).


##Service Bus Updates

- Event Hubs: 

	- Ermöglicht jetzt durch die Zurverfügungstellung zusätzlicher Publisher-Endpunkte für Event Hubs gezielte Zugriffssteuerung beim Senden von Ereignissen .
	- Zusätzliche Stabilität und Verbesserung der Event Hubs-Funktion.
	- Hinzufügen der Unterstützung des Amqp-Protokolls über WebSocket für Messaging und Event Hubs.

##HDInsight Tools für Visual Studio Updates

- **IntelliSense-Erweiterung**: Remote-Metadatenvorschlag

	HDInsight Tools für Visual Studio unterstützt das Abrufen von Remote-Metadaten, wenn Sie Ihr Hive-Skript bearbeiten. Sie können z. B. **WÄHLEN* AUS** eingeben, um alle Tabellennamen anzeigen zu lassen. Darüber hinaus werden die Spaltennamen nach der Angabe einer Tabelle angezeigt.

- **HDInsight Emulator-Support**

	HDInsight Tools für Visual Studio unterstützt jetzt das Herstellen einer Verbindung mit HDInsight Emulator, damit Sie Ihre Hive-Skripte Hive ohne Kosten lokal entwickeln und dann diese Skripte gegen Ihren HDInsight-Cluster ausführen können.

	Weitere Informationen finden Sie in [diesem Handbuch](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **HDInsight Tools für Visual Studio-Unterstützung für generische Hadoop-Cluster** (Vorschau)

	HDInsight-Tools für Visual Studio unterstützt jetzt generische Hadoop-Cluster, wodurch Sie HDInsight Tools für Visual Studio verwenden können, um folgende Aufgaben auszuführen:

	- Verbinden mit Ihrem Cluster, 
	- Schreiben einer Hive-Abfrage mit erweiterter Unterstützung von IntelliSense/Autokomplettierung, 
	- Anzeigen aller Aufträge in Ihrem Cluster mit einer intuitiven Benutzeroberfläche. 

	Weitere Informationen finden Sie in [diesem Handbuch](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##In-Role Cache Updates

- **In-Role Cache** wurde aktualisiert, um **Microsoft Azure Storage SDK** Version 4.3 zu verwenden. Bis jetzt hat **In-Role Cache ** Azure Storage SDK, Version 1.7 verwendet.

	Kunden, die Azure SDK 2.5 oder niedriger verwenden, sollten auf Azure SDK 2.6 aktualisieren und die neue Version von Azure Storage-SDK verwenden.

	Gegenwärtig ist geplant, Azure Storage Version 2011-08-18 am 1. August 2016 zu entfernen. Alle Migrationen von In-Role Cache von Azure SDK 2.5 oder niedriger auf die Version 2.6 müssen bis dahin abgeschlossen sein. Weitere Informationen zum Auslaufen von Azure Storage Version 2011-08-18 finden Sie unter [Aktualisierung bezüglich der Entfernung der Dienstversion Microsoft Azure Storage: Verlängerung bis 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Wir geben die Deaktivierung der Dienste Azure Managed Cache Service und Azure In-Role Cache zum 30. November 2016 bekannt. Es wird empfohlen, als Vorbereitung für diese Einstellung dieser Dienste zu Azure Redis Cache zu migrieren. Weitere Informationen zu Daten und Hinweise zur Migration finden Sie unter [Welches Azure Cache-Angebot ist für mich das richtige?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##Azure App Service Tools

Die folgenden Elemente wurden in Version 2.6 von Azure SDK aktualisiert.

- Erweiterte Azure-Veröffentlichung zur Einbeziehung von Azure API-Apps als Bereitstellungsziel.
- API-Apps Bereitstellungsfunktion, um Benutzern API-App-Erstellung und Bereitstellungsfunktion zu eröffnen.
- Server-Explorer geändert, um neuen App Service Knoten mit Web-, Mobil- und API-Apps, gruppiert nach Ressourcengruppe, widerzuspiegeln.
- Hinzufügen von Azure API-App-Client-Bewegung, die für die meisten C#-Projekte ergänzt wird. Diese ermöglicht die automatische Generierung von Swagger-fähigen API-Apps, die im Azure-Abonnement des Benutzers ausgeführt werden können.
- API-Apps Tools und App Service Knoten im Server-Explorer sind nur in Visual Studio 2013 verfügbar. 

##Azure Ressourcen-Manager Tools Updates

Die Azure Ressourcen-Manager-Tools wurden mit Vorlagen für virtuelle Computer, Netzwerke und Speicher aktualisiert. Die JSON-Bearbeitung enthält nach der Aktualisierung nun eine neue Gliederungsansicht für Vorlagen und die Möglichkeit, die Vorlagen mit JSON-Ausschnitte zu bearbeiten. In Visual Studio bereitgestellte Vorlagen verwenden ein PowerShell-Skript, das mit dem Projekt bereitgestellt wird, sodass alle im Skript vorgenommenen Änderungen von Visual Studio verwendet werden.

##Verbesserungen bei der Diagnose für Cloud-Dienste

Azure SDK 2.6 hat nun wieder Unterstützung für das Sammeln von Diagnoseprotokollen in Azure-Serveremulator und deren Übertragung in den Entwicklungsspeicher. Alle Diagnoseprotokolle (einschließlich Anwendungsrückverfolgungsprotokollen, Ereignisverfolgungsprotokollen für Windows (ETW), Leistungsindikatoren, Infrastrukturprotokollen und Windows-Ereignisprotokollen) die generiert werden, wenn die Anwendung im Emulator ausgeführt wird, können an den Entwicklungsspeicher übertragen werden, um zu überprüfen, ob die Diagnoseprotokollierung auf Ihrem lokalen Computer funktioniert.

Das Speicherkonto für die Diagnose kann jetzt in der Dienstkonfigurationsdatei (.csfg) angegeben werden, wodurch die Nutzung verschiedener Speicherkonten für die Diagnose unterschiedliche Umgebungen vereinfacht wird. Es gibt einige wichtige Unterschiede zwischen der Funktion der Verbindungszeichenfolge in Azure SDK 2.4 und Azure SDK 2.6. Weitere Informationen zur Verwendung der Verbindungszeichenfolge des Diagnosespeichers und Auswirkungen auf Ihre Projekte finden Sie unter [Konfigurieren der Diagnose für Azure Cloud Services](http://go.microsoft.com/fwlink/?LinkID=532784).

##Wichtige Änderungen

###Azure Resource Manager Tools 

- Die Projekttypen **Cloud-Bereitstellungsprojekte**, die in Azure SDK 2.5 verfügbar sind, wurde in **Azure Ressourcengruppe** umbenannt.
- Projekte, die mit Projekttyp **Cloud-Bereitstellungsprojekte** in Azure SDK 2.5 erstellt wurden, können in Version 2.6 verwendet werden, die Bereitstellung der Vorlage aus Visual Studio wird jedoch fehlschlagen. Bereitstellen mit PowerShell-Skript funktioniert jedoch weiterhin wie zuvor. Weitere Informationen zur Verwendung von **Cloud-Bereitstellungsprojekten** in Version 2.6 finden Sie in diesem [Beitrag](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##Bekannte Probleme

- Für das Sammeln von Diagnoseprotokollen im Emulator ist ein 64-Bit-Betriebssystem erforderlich. Bei der Ausführung auf einem 32-Bit-Betriebssystem werden keine Diagnoseprotokolle gesammelt. Dies wirkt sich nicht auf andere Emulatorfunktionen aus. 

- Azure SDK 2.6, veröffentlicht am 29.04.2015, hatte zwei Probleme:

	- Universal konnte nicht in Visual Studio 2015 geladen werden, wenn Azure SDK 2.6 auf dem Computer installiert war.
	- Das Debuggen eines Clouddienstprojekts schlägt in Visual Studio 2013 und Visual Studio 2015 fehl. Visual Studio reagiert nicht mehr und stürzt ab, während ein Dialogfeld mit der Meldung "Konfigurieren der Diagnose für den Emulator" angezeigt wird.
	
	Ein Update für Azure SDK 2.6 wurde am 18.05.2015 veröffentlicht. Die aktualisierte Version ist 2.6.30508.1601. Sie enthält Korrekturen für die beiden oben beschriebenen Probleme. Sie können die Version des SDK identifizieren über: Systemsteuerung -> Programme und Funktionen -> Microsoft Azure-Tools für Microsoft Visual Studio 2013 – V 2.6. In der Versionsspalte wird die Nummer des Builds angezeigt.

	Wenn Sie weiterhin die oben genannten Probleme haben, installieren Sie die neueste Version des Azure SDK 2.6 für [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) oder [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##Weitere Informationen

[Unterstützungs- und Deaktivierungsinformationen zum Azure SDK für .NET und APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

<!---HONumber=AcomDC_1210_2015-->
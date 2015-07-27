<properties 
	pageTitle="Azure Data Factory – Häufig gestellte Fragen" 
	description="Häufig gestellte Fragen zu Azure Data Factory" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="spelluru"/>

# Azure Data Factory – Häufig gestellte Fragen

## Allgemeine Fragen

### F: Was ist Azure Data Factory?

Data Factory ist ein vollständig verwalteter Dienst für Entwickler zum Erstellen von Diensten für Datenspeicher, Transfer und Verarbeitung in fehlertoleranten Datenpipelines mit hoher Verfügbarkeit. Data Factory arbeitet mit lokalem und Cloud-Datenspeicher. Eine Pipeline ist ein Satz von Dateneingaben, Verarbeitungsaktivitäten und Datenausgaben und wird mit einfachen JSON-Skripts definiert und über PowerShell-Befehle aktiviert. Nach der Aktivierung organisiert und plant die Data Factory Pipelines für die Ausführung in HDInsight (Hadoop) mit Optionen für die automatische Clusterverwaltung im Auftrag des Benutzers. Data Factory bietet zudem eine visuelle Verwaltung und Überwachung über das Azure-Vorschauportal, um alle Pipelines mit umfassenden Informationen zum Betrieb und zur Dienstintegrität über ein Dashboard zu überwachen.
 
### F: Welche Kundenherausforderung lässt sich mit Data Factory meistern?

Azure Data Factory verteilt die Flexibilität der Nutzung verschiedener Dienste für Datenspeicherung, Verarbeitung und Datentransfers auf traditionelle relationale Speicher auf Basis unstrukturierter Daten mit den Steuerungs- und Überwachungsfunktionen eines vollständig verwalteten Diensts.

### F: Für welche Zielgruppen ist Data Factory konzipiert?


- Datenentwickler: die für das Erstellen von Integrationsdiensten zwischen Hadoop und anderen Systemen zuständig sind:
	- müssen mit ständig wechselnder und wachsender Datenstruktur Schritt halten und entsprechend integrieren
	- müssen für Informationserzeugung benutzerdefinierten Code schreiben, der teuer sowie schwer zu verwalten ist weder hohe Verfügbarkeit noch Fehlertoleranz aufweist

- IT-Experten: die versuchen, vielfältigere Daten innerhalb ihrer IT-Infrastruktur zu integrieren:
	- müssen sämtliche Daten einer Organisation einbeziehen, um vielfältige Einblicke in die Geschäftsabläufe zu erhalten
	- müssen Rechen- und Speicherressourcen verwalten, um Kosten und Größe auf lokale und Cloud-Lösungen zu verteilen
	- müssen schnell diverse Quellen und Verarbeitungsmöglichkeiten hinzufügen, um neue Geschäftsanforderungen zu behandeln, während der Überblick über alle Rechen- und Speicherressourcen erhalten bleibt

###  F: Wo finde ich Preisinformationen zu Azure Data Factory?

Preisinformationen zu Azure Data Factory finden Sie auf der Seite [Data Factory – Preisübersicht][adf-pricing-details].

### F: F: Was sind die ersten Schritte mit Azure Data Factory?

- Eine Übersicht über Azure Data Factory finden Sie unter [Einführung in Azure Data Factory][adf-introduction].
- Ein kurzes Lernprogramm finden Sie unter [Erste Schritte mit Azure Data Factory][adfgetstarted].
- Die umfassende Dokumentation finden Sie in der [Dokumentation zu Azure Data Factory][adf-documentation-landingpage].

  
### F: Wie können Kunden auf Data Factory zugreifen?

Kunden können über das [Azure-Vorschauportal][azure-preview-portal] auf Data Factory zugreifen.

### F: In welchen Regionen ist Data Factory verfügbar?

Als öffentliche Vorschau wird Data Factory nur in der Region „USA West“ verfügbar sein. Die von Data Factory verwendeten Rechen- und Speicherdienste können in anderen Regionen verfügbar sein.
 
### F: Welche Grenzwerte gelten hinsichtlich der Anzahl von Data Factorys/Pipelines/Aktivitäten/Datasets? 


- Anzahl der Data Factorys innerhalb eines Abonnements: 50
- Anzahl von Pipelines innerhalb einer Data Factory: 100
- Anzahl der Aktivitäten in einer Pipeline: 10
- Anzahl von Datasets in einer Data Factory: 100

### F: Was sieht die Erstellung-/Entwicklungsumgebung im Azure Data Factory-Dienst aus?

Sie können Data Factorys mithilfe einer der folgenden Methoden erstellen:

- **Azure-Vorschauportal** Die Data Factory-Blätter im Azure-Vorschauportal bieten eine umfassende Benutzeroberfläche zum Erstellen von Factorys und zugehöriger Dienste. Der **Data Factory-Editor**, der auch Teil des Portals ist, ermöglicht Ihnen die einfache Erstellung von verknüpften Diensten, Tabellen, Datasets und Pipelines durch Angabe von JSON-Definitionen für diese Artefakte. Unter [Data Factory-Editor][data-factory-editor] finden Sie eine Übersicht über den Editor und unter [Erste Schritte mit Data Factory][datafactory-getstarted] ein Beispiel für das Verwenden des Portals/Editors zum Erstellen und Bereitstellen einer Data Factory.   
- **Azure PowerShell**. Wenn Sie ein PowerShell-Benutzer sind und PowerShell anstelle der Portalbenutzeroberfläche nutzen möchten, können Sie Azure Data Factory-Cmdlets einsetzen, die als Teil von Azure PowerShell zum Erstellen und Bereitstellen von Data Factorys dienen. Unter [Erstellen und Überwachen von Azure Data Factory mit Azure PowerShell][create-data-factory-using-powershell] finden Sie ein einfaches Beispiel. Unter [Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mithilfe von Data Factory][adf-tutorial] finden Sie ein erweitertes Beispiel der Verwendung von PowerShell-Cmdlets zum Erstellen und Bereitstellen einer Data Factory. In der [Data Factory-Cmdlet-Referenz][adf-powershell-reference] in der MSDN-Bibliothek finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.  
- **Klassenbibliothek von .NET**. Sie können Daten Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen. Unter [Erstellen, Überwachen und Verwalten von Daten Factorys mit dem .NET SDK][create-factory-using-dotnet-sdk] finden Sie eine exemplarische Vorgehensweise zum Erstellen einer Data Factory mit dem .NET SDK. Unter [Data Factory-Klassenbibliotheksreferenz][msdn-class-library-reference] finden Sie eine umfassende Dokumentation zum Data Factory .NET SDK.  
- **REST-API**. Sie können auch die vom Azure-Data Factory-Dienst verfügbar gemachte REST-API zum Erstellen und Bereitstellen von Data Factorys nutzen. Unter [Data Factory-REST-API][msdn-rest-api-reference] finden Sie eine umfassende Dokumentation zur Data Factory-REST-API. 

### F: Können Data Factorys umbenannt werden?
Nein. Wie bei anderen Azure-Ressourcen auch kann der Name einer Azure Data Factory nicht geändert werden.

## Aktivitäten – Häufig gestellte Fragen
### F: Welche Datenquellen und Aktivitäten werden unterstützt?

- **Unterstützte Datenquellen**: 
	- Azure Storage (Blob und Tabellen)
	- Azure SQL
	- Azure DocumentDB
	- Lokaler SQL Server
	- Lokale Oracle-Datenbank 
	- Lokales Dateisystem
	- Lokale MySQL-Datenbank
	- Lokale DB2-Datenbank
	- Lokale Teradata-Datenbank
	- Lokale Sybase-Datenbank
	- Lokale PostgreSQL-Datenbank  
- **Unterstützte Aktivitäten**: 
	- Kopieraktivität (lokal nach Cloud und Cloud nach lokal)
	- HDInsight-Aktivität (Pig, Hive, MapReduce, Hadoop-Streamingtransformationen)
	- Azure Machine Learning-Batch-Bewertungsaktivität
	- Azure SQL-Aktivität "Gespeicherte Prozedur"
	- Benutzerdefinierte .NET-Aktivitäten

### Wann wird eine Aktivität ausgeführt?
Die Konfigurationseinstellung **availability** in der Ausgabedatentabelle bestimmt, wann die Aktivität erfolgt. Die Aktivität prüft, ob alle Eingabedatenabhängigkeiten erfüllt sind (den Status **ready** haben), bevor die Ausführung beginnt.

## Kopieraktivität – Häufig gestellte Fragen
### F: In welchen Regionen wird die Kopieraktivität unterstützt?

Die Kopieraktivität zum Kopieren von Daten wird in den folgenden Regionen unterstützt: USA (Ost), USA (Ost) 2, USA (West), USA (Mitte), USA (Mitte/Süden), Nordeuropa, Westeuropa, Südostasien, Japan (Ost), Brasilien (Süd)

Das Kopieren von Daten in andere Regionen wird ebenfalls unterstützt, wenn eine der oben genannten fünf Regionen zur Weiterleitung der Daten genutzt wird. Der Kopiervorgang wird auf Basis der Region gemessen, über die die Daten weitergeleitet werden.

Region des Kopierziels | Für die Weiterleitung genutzte Region
-------------------------- | -----------------------
Ostasien | Südostasien
Japan (Westen) | Japan Ost

### Wie kann ich Daten in mehrere Ausgabetabellen kopieren?
Wie im folgenden Beispiel gezeigt, kann eine Pipeline mehrere Ausgabetabellen enthalten.

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### Ist es besser, eine Pipeline mit mehreren Aktivitäten oder eine separate Pipeline für jede Aktivität einzurichten? 
Pipelines dienen zum Bündeln verwandter Aktivitäten. Natürlich können Sie die Aktivitäten in einer Pipeline halten, wenn die Tabellen, die diese verbinden, nicht von anderen Aktivitäten außerhalb der Pipeline genutzt werden. Auf diese Weise müssen Sie Pipelineaktivitäten nicht verknüpfen, damit diese sich aneinander ausrichten. Darüber hinaus kann die Datenintegrität in den Tabellen, die für die Pipeline intern sind, beim Aktualisieren der Pipeline besser beibehalten werden. Bei einer Pipelineaktualisierung werden alle Aktivitäten in der Pipeline beendet, entfernt und neu erstellt. Aus Erstellungssicht kann es auch einfacher sein, den Datenfluss innerhalb der zugehörigen Aktivitäten in einer JSON-Datei für die Pipeline nachzuverfolgen.

## HDInsight-Aktivität – Häufig gestellte Fragen

### F: In welchen Regionen wird HDInsight unterstützt?

Weitere Informationen finden Sie im Abschnitt zur geografischen Verfügbarkeit des folgenden Artikels oder unter [HDInsight – Preisübersicht][hdinsight-supported-regions].

### F: Welche Region wird von einem bedarfsgesteuerten HDInsight-Cluster verwendet?

Der bedarfsgesteuerte HDInsight-Cluster wird in derselben Region erstellt, in der sich der Speicher befindet, den Sie für die Verwendung mit dem Cluster angegeben haben.

### F: Wie können weitere Speicherkonten mit Ihrem HDInsight-Cluster verknüpft werden?

Wenn Sie Ihren eigenen HDInsight-Cluster (BYOC - Bring Your Own Cluster) verwenden, lesen Sie die folgenden Themen:

- [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores][hdinsight-alternate-storage]
- [Verwenden zusätzlicher Speicherkonten mit HDInsight Hive][hdinsight-alternate-storage-2]

Wenn Sie einen bedarfsgesteuerten Cluster verwenden, der vom Data Factory-Dienst erstellt wird, müssen Sie zusätzliche Speicherkonten für den verknüpften- HDInsight-Dienst angeben, damit der Data Factory-Dienst diese in Ihrem Auftrag registrieren kann. Verwenden Sie in der JSON-Definition des bedarfsgesteuerten verknüpften Diensts die **additionalLinkedServiceNames**-Eigenschaft, um alternative Speicherkonten anzugeben, wie im folgenden JSON-Codeausschnitt gezeigt:
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

Im obigen Beispiel stellen "otherLinkedServiceName1" und "otherLinkedServiceName2" verknüpfte Dienste dar, deren Definitionen Anmeldeinformationen enthalten, die der HDInsight-Cluster für den Zugriff auf alternative Speicherkonten benötigt.

## Aktivität "Gespeicherte Prozedur" – Häufig gestellte Fragen
### Welche Datenquellen unterstützt die Aktivität "Gespeicherte Prozedur"?
Die Aktivität "Gespeicherte Prozedur" unterstützt derzeit nur Azure SQL-Datenbank.

## Slices – Häufig gestellte Fragen

### Wie kann ich einen Slice erneut ausführen?
Sie können einen Slice auf eine der folgenden Arten erneut ausführen:

- Klicken Sie im Portal auf der Befehlsleiste für den Slice auf dem Blatt **DATENSLICE** auf **Ausführen**. 
- Führen Sie das Cmdlet **Set AzureDataFactorySliceStatus** mit auf **PendingExecution** festgelegtem Status für den Slice aus.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Unter [Set AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] finden Sie ausführliche Informationen zum Cmdlet.

### Wie lange hat die Verarbeitung eines Slices gedauert?
1. Klicken Sie auf der Kachel **Datasets** auf das Blatt **DATA FACTORY** für Ihre Data Factory.
2. Klicken Sie auf dem Blatt **Datasets** auf das gewünschte Dataset.
3. Wählen Sie auf dem Blatt **TABELLE** in der Liste **Zuletzt verwendete Slices** den gewünschten Slice aus.
4. Klicken Sie auf dem Blatt **DATENSLICE** in der Liste **Aktivitätsausführungen** auf die Aktivitätsausführung. 
5. Klicken Sie auf der Kachel **Eigenschaften** auf das Blatt **DETAILS ZUR AKTIVITÄTSAUSFÜHRUNG**. 
6. Daraufhin sollte das Feld **Dauer** mit einem Wert angezeigt werden. Dies ist die Verarbeitungszeit des Slices.   

### Wie wird ein ausgeführter Slice beendet?
Wenn Sie die Ausführung der Pipeline beenden müssen, können Sie das Cmdlet [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) verwenden. Derzeit werden laufende Sliceausführungen bei Anhalten der Pipeline nicht beendet. Sobald die laufenden Ausführungen abgeschlossen sind, wird kein zusätzlicher Slice ausgewählt.

Wenn Sie alle Ausführungen wirklich sofort beenden möchten, ist die einzige Möglichkeit das Löschen und erneute Erstellen der Pipeline. Wenn Sie die Pipeline löschen, müssen Sie keine Tabellen und verknüpften Dienste löschen, die von der Pipeline verwendet werden.



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=July15_HO3-->
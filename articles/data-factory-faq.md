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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Azure Data Factory – Häufig gestellte Fragen

## Allgemeine Fragen

### F: Was ist Azure Data Factory?

Data Factory ist ein vollständig verwalteter Dienst für Entwickler zum Erstellen von Diensten für Datenspeicher, Transfer und Verarbeitung in fehlertoleranten Datenpipelines mit hoher Verfügbarkeit. Data Factory arbeitet mit lokalem und Cloud-Datenspeicher. Eine Pipeline ist ein Satz von Dateneingaben, Verarbeitungsaktivitäten und Datenausgaben und wird mit einfachen JSON-Skripts definiert und über PowerShell-Befehle aktiviert. Nach der Aktivierung organisiert und plant die Data Factory Pipelines für die Ausführung in HDInsight (Hadoop) mit Optionen für die automatische Clusterverwaltung im Auftrag des Benutzers. Data Factory bietet zudem eine visuelle Verwaltung und Überwachung über das Azure-Vorschauportal, um alle Pipelines mit umfassenden Informationen zum Betrieb und zur Dienstintegrität über ein Dashboard zu überwachen.
 
### F: welche Kundenanfragen Data Factory ist zu lösen?

Azure Data Factory verteilt die Flexibilität der Nutzung verschiedener Dienste für Datenspeicherung, Verarbeitung und Datentransfers auf traditionelle relationale Speicher auf Basis unstrukturierter Daten mit den Steuerungs- und Überwachungsfunktionen eines vollständig verwalteten Diensts.

### F: Wer sind die Zielgruppe für Daten Factory?


- Datenentwickler: die für das Erstellen von Integrationsdiensten zwischen Hadoop und anderen Systemen zuständig sind:
	- müssen mit ständig wechselnder und wachsender Datenstruktur Schritt halten und entsprechend integrieren
	- müssen für Informationserzeugung benutzerdefinierten Code schreiben, der teuer sowie schwer zu verwalten ist weder hohe Verfügbarkeit noch Fehlertoleranz aufweist

- IT-Experten: die versuchen, vielfältigere Daten innerhalb ihrer IT-Infrastruktur zu integrieren:
	- müssen sämtliche Daten einer Organisation einbeziehen, um vielfältige Einblicke in die Geschäftsabläufe zu erhalten
	- müssen Rechen- und Speicherressourcen verwalten, um Kosten und Größe auf lokale und Cloud-Lösungen zu verteilen
	- müssen schnell diverse Quellen und Verarbeitungsmöglichkeiten hinzufügen, um neue Geschäftsanforderungen zu behandeln, während der Überblick über alle Rechen- und Speicherressourcen erhalten bleibt

###  Details f: wo Preise finde ich für Azure Data Factory?

Finden Sie unter [Daten Factory-Preisdetails Seite][adf-pricing-details] für die Preisdetails für Azure-Daten-Factory.

### F: Wie mit Azure Data Factory fange ich?

- Einen Überblick über die Azure Data Factory finden Sie unter [Einführung in Azure Data Factory][adf-introduction].
- Ein kurzes Lernprogramm, finden Sie unter [Erste Schritte mit Azure Data Factory][adfgetstarted].
- Umfassende Dokumentation finden Sie unter [Azure Data Factory-Dokumentation][adf-documentation-landingpage].

  
### F: wie zugreifen Kunden Daten Factory?

Kunden erhalten Zugriff auf Daten Factory über die [Azure Preview Portal][azure-preview-portal].

### F: Was ist der Daten-Factory Region Verfügbarkeit?

Als öffentliche Vorschau wird Data Factory nur in der Region „USA West“ verfügbar sein. Die von Data Factory verwendeten Rechen- und Speicherdienste können in anderen Regionen verfügbar sein.
 
### F: Was sind die Grenzwerte für die Anzahl der Daten Factorys/Pipelines/Aktivitäten/Datasets? 


- Anzahl der Daten Factorys innerhalb eines Abonnements: 50
- Anzahl von Pipelines innerhalb einer Factory Daten: 100
- Anzahl der Aktivitäten in einer Pipeline: 10
- Anzahl von Datensätzen mit in einer Fabrik Daten: 100

### F: Was ist die Erstellung/Entwicklererfahrung mit Azure Data Factory-Dienst?

Sie können Data Factorys mithilfe einer der folgenden Methoden erstellen:

- **Azure Preview Portal**. Die Data Factory-Blätter im Azure-Vorschauportal bieten eine umfassende Benutzeroberfläche zum Erstellen von Factorys und zugehöriger Dienste. Die **Data Factory-Editor**, die auch Teil des Portals ist ermöglicht es Ihnen die einfache Erstellung von verknüpften Dienste, Tabellen, Datasets und Pipelines durch Angabe von JSON-Definitionen für diese Artefakte. Finden Sie unter [Data Factory-Editor][data-factory-editor] eine Übersicht über den Editor und [Erste Schritte mit Daten Factory][datafactory-getstarted] für ein Beispiel für das Portal/Editor zum Erstellen und Bereitstellen einer Factory Daten verwenden.   
- **Azure PowerShell**. Wenn Sie ein PowerShell-Benutzer sind und PowerShell anstelle der Portalbenutzeroberfläche nutzen möchten, können Sie Azure Data Factory-Cmdlets einsetzen, die als Teil von Azure PowerShell zum Erstellen und Bereitstellen von Data Factorys dienen. Finden Sie unter [Erstellen und Überwachen von Azure Data Factory mit Azure PowerShell][create-data-factory-using-powershell] für ein einfaches Beispiel und [Lernprogramm: verschieben und Verarbeiten von Protokolldateien, die mit Daten Factory][adf-tutorial] für erweiterte Beispiel zeigt die Verwendung von PowerShell Cmdles zum Erstellen von Ad eine Factory Daten bereitstellen. Finden Sie unter [Daten Factory-Cmdlet-Referenz][adf-powershell-reference] Content auf der MSDN-Bibliothek für eine umfassende Dokumentation Data Factory-Cmdlets.  
- **Klassenbibliothek von .NET**. Sie können Daten Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen. Finden Sie unter [erstellen, überwachen und Verwalten von Daten Factorys mit .NET SDK][create-factory-using-dotnet-sdk] eine exemplarische Vorgehensweise zum Erstellen einer Daten-Factory, die mit .NET SDK. Finden Sie unter [Daten Factory Class Library Reference][msdn-class-library-reference] für eine umfassende Data Factory .NET SDK-Dokumentation.  
- **REST-API**. Sie können auch die vom Azure-Data Factory-Dienst verfügbar gemachte REST-API zum Erstellen und Bereitstellen von Data Factorys nutzen. Finden Sie unter [Daten Factory REST-API-Referenz][msdn-rest-api-reference] für eine umfassende Data Factory REST-API-Dokumentation. 

## Aktivitäten – häufig gestellte Fragen
### F: Was sind die unterstützten Datenquellen und Aktivitäten?

- **Unterstützten Datenquellen:** Azure-Speicher (BLOBs und Tabellen), SQL Server, Azure SQL-Datenbank, Dateisystem, Oracle-Datenbank.
- **Aktivitäten unterstützt:**: Kopie-Aktivität (lokale zur Cloud und die Cloud zu lokalem), HDInsight-Aktivität (Pig, Hive, MapReduce, Hadoop-Streaming Transformationen), Azure Machine Learning Bewertung Aktivität, gespeicherte Prozedur-Aktivität und benutzerdefinierten C#-Aktivitäten.

### Wann werden eine Aktivität ausgeführt?
Die **Verfügbarkeit** Konfigurationseinstellung in der Datentabelle Ausgabe bestimmt, wenn die Aktivität ausgeführt wird. Die Aktivität wird überprüft, ob alle Eingabedaten Abhängigkeiten erfüllt sind (d. h. **bereit** State) vor dem Starten der Ausführung.

## Kopieren der Aktivität – häufig gestellte Fragen
### F: unterstützt welche Bereiche werden von der Aktivität kopieren?

Der Kopie Aktivität unterstützt das Kopieren von Daten in die folgenden Bereiche: Osten der USA OSTEN 2, Westen der USA, zentral-USA, Norden, Süden, Nordeuropa, Westeuropa und Südostasien.

Kopieren von Daten in anderen Regionen wird auch unterstützt mithilfe einer der Regionen über für die Weiterleitung der Daten. Der Kopiervorgang wird auf Basis der Region gemessen, über die die Daten weitergeleitet werden.

Bereich des Kopierziel | Region für das routing verwendet
-------------------------- | -----------------------
Ostasien | Südostasien
Japan Ost | USA (West)
Japan West | USA (West)
Brasilien Süd | USA (Ost) 2

### Wie kann ich auf mehrere Ausgabetabellen kopieren?
Sie können mehrere Output-Tabellen in einer Pipeline haben, wie im folgenden Beispiel gezeigt:

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### Ist es besser, wenn eine Pipeline mit mehreren Aktivitäten oder eine separate Pipeline für jede Aktivität? 
Pipelines werden sollte, um verwandte Aktivitäten zu bündeln. Logisch, können Sie die Aktivitäten in einer Pipeline halten, wenn die Tabellen, die diese Verbinden von jede andere Aktivität außerhalb der Pipeline nicht genutzt werden. Auf diese Weise keine Kette Pipeline aktiven Zeitraums müssten Sie aneinander ausgerichtet. Darüber hinaus wird die Datenintegrität in den Tabellen in der Pipeline interne beim Aktualisieren der Pipeline besser beibehalten. Pipeline-Update im Grunde beendet alle Aktivitäten in der Pipeline, entfernt und erneut erstellt. Von der Erstellung der Sicht, kann es auch einfacher, den Fluss der Daten innerhalb der zugehörigen Aktivitäten in einem JSON-Datei für die Pipeline finden Sie unter sein.

## HDInsight-Aktivität – häufig gestellte Fragen

### F: unterstützt welche Bereiche werden von HDInsight?

Finden Sie im Abschnitt "geografische Verfügbarkeit" im folgenden Artikel: oder [HDInsight-Preisdetails][hdinsight-supported-regions].

### F: werden welche Region wird durch ein HDInsight-Cluster bei Bedarf verwendet?

Der bedarfsgesteuerte HDInsight-Cluster wird in derselben Region erstellt, in der sich der Speicher befindet, den Sie für die Verwendung mit dem Cluster angegeben haben.

### F: wie weitere Speicherkonten verfügen, um den HDInsight-Cluster verknüpft?

Wenn Sie Ihren eigenen HDInsight-Cluster (BYOC - eigenen Cluster zu bringen) verwenden, finden Sie unter den folgenden Themen:

- [Verwenden ein HDInsight-Cluster mit alternativen Speicherkonten und Metastores][hdinsight-alternate-storage]
- [Verwenden Sie zusätzlichen Speicher-Konten mit HDInsight-Struktur][hdinsight-alternate-storage-2]

Wenn Sie einen Anforderungs-Cluster verwenden, der vom Dienst Daten Factory erstellt wird, müssen Sie angeben, dass die zusätzlichen Speicherkonten für das HDInsight Service verknüpft, sodass der Service Factory für die Daten in Ihrem Namen registrieren kann. Verwenden Sie in der JSON-Definition für den verknüpften on Demand-Dienst **AdditionalLinkedServiceNames** -Eigenschaft alternativen Speicherkonten an, wie im folgenden JSON-Ausschnitt gezeigt:
 
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

Im obigen Beispiel darstellen otherLinkedServiceName1 und otherLinkedServiceName2 verknüpften Dienste, deren Definitionen Anmeldeinformationen enthalten, die das HDInsight-Cluster alternative Speicherkonten zugreifen muss.

## Gespeicherte Prozedur Aktivität – häufig gestellte Fragen
### Unterstützt auf welche Datenquellen die gespeicherte Prozedur für die Aktivität?
Die gespeicherte Prozedur für die Aktivität unterstützt nur SQL Azure-Datenbank zu diesem Zeitpunkt.

## Segmente – häufig gestellte Fragen

### Wie kann ich ein Segment erneut ausführen?
Sie können ein Segment erneut ausführen, in einem der folgenden Arten:

- Klicken Sie auf **Ausführen** auf der Befehlsleiste auf die **DATENSLICE** Blade für das Segment im Portal. 
- Führen Sie **Set AzureDataFactorySliceStatus** Cmdlet mit dem Status festgelegt **PendingExecution** für das Segment.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Finden Sie unter [Set AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] ausführliche Informationen über das Cmdlet.

### Wie lange dauerte es um ein Segment zu verarbeiten?
1. Klicken Sie auf **Datasets** Kachel auf die **DATA FACTORY** Blade für Ihre Daten-Factory.
2. Klicken Sie auf das bestimmte Dataset auf die **Datasets** Blade.
3. Wählen Sie das Segment aus, die Sie von Interesse sind die **aktuelle Slices** auf in der Liste der **Tabelle** Blade.
4. Klicken Sie auf die Aktivität ausgeführt wird, von der **Aktivität ausgeführt wird** auf in der Liste der **DATENSLICE** Blade. 
5. Klicken Sie auf **Eigenschaften** Kachel auf die **AKTIVITÄTSDETAILS ausführen** Blade. 
6. Daraufhin sollte die **Dauer** Feld mit einem Wert. Dies ist die Verarbeitungszeit für das Segment.   

### Wie eine laufende Slice beenden?
Wenn Sie die Pipeline aus der Ausführung beenden müssen, können Sie [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) Cmdlet. Derzeit hält das Anhalten der Pipeline nicht die Slice-Ausführungen an, die ausgeführt werden. Wenn die Ausführungen in Bearbeitung fertig sind, wird keine zusätzliche Slice übernommen.

Wenn Sie alle Ausführungen sofort beenden möchten, wäre die einzige Möglichkeit die Pipeline zu löschen und neu erstellen. Wenn Sie die Pipeline löschen, müssen Sie nicht zum Löschen von Tabellen und verknüpften Dienste, die von der Pipeline verwendet.



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

<!---HONumber=GIT-SubDir-->
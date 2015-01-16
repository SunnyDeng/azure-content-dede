<properties title="Azure Data Factory - Frequently Asked Questions" pageTitle="Azure Data Factory - Häufig gestellte Fragen" description="Häufig gestellte Fragen zu Azure Data Factory" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure Data Factory - Häufig gestellte Fragen

**F: Was ist Azure Data Factory?**

Data Factory ist ein vollständig verwalteter Dienst für Entwickler zum Erstellen von Diensten für Datenspeicher, Transfer und Verarbeitung in fehlertoleranten Datenpipelines mit hoher Verfügbarkeit. Data Factory arbeitet mit lokalem und Cloud-Datenspeicher. Eine Pipeline ist ein Satz von Dateneingaben, Verarbeitungsaktivitäten und Datenausgaben und wird mit einfachen JSON-Skripts definiert und über PowerShell-Befehle aktiviert. Nach der Aktivierung organisiert und plant die Data Factory Pipelines für die Ausführung in HDInsight (Hadoop) mit Optionen für die automatische Clusterverwaltung im Auftrag des Benutzers. Data Factory bietet zudem eine visuelle Verwaltung und Überwachung über das Azure-Vorschauportal, um alle Pipelines mit umfassenden Informationen zum Betrieb und zur Dienstintegrität über ein Dashboard zu überwachen.
 
**F: Für die Lösung welcher Kundenherausforderung ist Data Factory konzipiert?**

Azure Data Factory verteilt die Flexibilität der Nutzung verschiedener Dienste für Datenspeicherung, Verarbeitung und Datentransfers auf traditionelle relationale Speicher auf Basis unstrukturierter Daten mit den Steuerungs- und Überwachungsfunktionen eines vollständig verwalteten Diensts.

**F: Für welche Zielgruppen ist Data Factory konzipiert?**


- Datenentwickler: die für das Erstellen von Integrationsdiensten zwischen Hadoop und anderen Systemen zuständig sind:
	- müssen mit ständig wechselnder und wachsender Datenstruktur Schritt halten und entsprechend integrieren
	- müssen für Informationserzeugung benutzerdefinierten Code schreiben, der teuer sowie schwer zu verwalten ist weder hohe Verfügbarkeit noch Fehlertoleranz aufweist

- IT-Experten: die versuchen, vielfältigere Daten innerhalb ihrer IT-Infrastruktur zu integrieren:
	- müssen sämtliche Daten einer Organisation einbeziehen, um vielfältige Einblicke in die Geschäftsabläufe zu erhalten
	- müssen Rechen- und Speicherressourcen verwalten, um Kosten und Größe auf lokale und Cloud-Lösungen zu verteilen
	- müssen schnell diverse Quellen und Verarbeitungsmöglichkeiten hinzufügen, um neue Geschäftsanforderungen zu behandeln, während der Überblick über alle Rechen- und Speicherressourcen erhalten bleibt

**F: Wo finde ich Preisinformationen zu Azure Data Factory?**

Preisinformationen zu Azure Data Factory finden Sie auf der Seite [Data Factory - Preisdetails][adf-pricing-details].  

**F: Wie starte ich mit Azure Data Factory?**

- Einen Überblick über Azure Data Factory finden Sie unter [Einführung in Azure Data Factory][adf-introduction].
- Ein kurzes Lernprogramm finden Sie unter [Erste Schritte mit Azure Data Factory][adfgetstarted].
- Die umfassende Dokumentation finden Sie in der [Dokumentation zu Azure Data Factory][adf-documentation-landingpage].
 
**F: Welche Datenquellen und Aktivitäten werden unterstützt?**

- **Unterstützte Datenquellen:** Azure-Speicher (Blob und Tabellen), SQL Server, Azure SQL-Datenbank. 
- **Unterstützte Aktivitäten:**: Kopieraktivität (lokal-zu-Cloud und Cloud-zu-lokal), HDInsight-Aktivität (Pig- und Hive-Transformationen) und benutzerdefinierte C#-Aktivitäten.
  
**F: Wie können Kunden auf Data Factory zugreifen?**

Kunden können über das [Azure-Vorschauportal][azure-preview-portal] auf Data Factory zugreifen.

**F: In welchen Regionen ist Data Factory verfügbar?**

Als öffentliche Vorschau wird Data Factory nur in der Region "USA West" verfügbar sein.  Die von Data Factory verwendeten Rechen- und Speicherdienste können in anderen Regionen verfügbar sein.
 
**F: Welche Grenzwerte sind hinsichtlich der Anzahl der Data Factorys/Pipelines/Aktivitäten/Datasets gegeben?** 


- Anzahl der Data Factorys innerhalb eines Abonnements: 50
- Anzahl von Pipelines innerhalb einer Data Factory: 100
- Anzahl der Aktivitäten in einer Pipeline: 10
- Anzahl von Datasets in einer Data Factory: 100

**F: Welche Regionen werden von der Kopieraktivität unterstützt?**

Die Kopieraktivität unterstützt das Kopieren von Daten in die folgenden Regionen: USA Ost 2, USA West, Nordeuropa, Westeuropa und Südostasien.

Das Kopieren von Daten in andere Regionen wird unter Verwendung einer der oben genannten fünf Regionen zur Weiterleitung der Daten ebenfalls unterstützt.  Der Kopiervorgang wird auf Basis der Region gemessen, über die die Daten weitergeleitet werden.

Kopierzielregion | Zur Weiterleitung verwendete Region
-------------------------- | -----------------------
USA Ost | USA Ost 2
USA Mitte | USA Ost 2
USA Nord/Mitte | USA Ost 2
USA Süd/Mitte | USA West
Ostasien | Südostasien
Japan Ost | USA West
Japan West | USA West
Brasilien Süd | USA Ost 2

**F: Welche Regionen werden von HDInsight bei der Verwendung eines HDInsight-Clusters unterstützt?**

Weitere Informationen finden Sie im Abschnitt zur geografischen Verfügbarkeit des folgenden Artikels: oder [HDInsight - Preisdetails][hdinsight-supported-regions].

**F: Welche Region wird von einem bedarfsgesteuerten HDInsight-Cluster verwendet?**

Der bedarfsgesteuerte HDInsight-Cluster wird in derselben Region erstellt, in der sich der Speicher befindet, den Sie für die Verwendung mit dem Cluster angegeben haben.    

[adfgetstarted]: ../data-factory-get-started

[adf-introduction]: ../data-factory-introduction
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/de-de/pricing/details/hdinsight/

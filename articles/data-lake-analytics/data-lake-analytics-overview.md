<properties 
   pageTitle="Übersicht über Microsoft Azure Data Lake Analytics | Azure" 
   description="Data Lake Analytics ist ein Azure Big Data-Berechnungsdienst zur Nutzung von Daten für die Betriebsführung auf Basis der aus Ihren Clouddaten ermittelten Erkenntnisse, unabhängig von Speicherort oder Datenvolumen. Data Lake Analytics ermöglicht dies auf die einfachste, am höchsten skalierbare und wirtschaftlichste Weise." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Übersicht über Microsoft Azure Data Lake Analytics

## Was ist Azure Data Lake Analytics?

Azure Data Lake Analytics ist ein neuer Dienst zur Vereinfachung von Big Data-Analysen. Mit diesem Dienst können Sie sich auf das Schreiben, Ausführen und Verwalten von Aufträgen konzentrieren, statt zusätzlich den Betrieb der verteilten Infrastruktur übernehmen zu müssen. Anstatt sich der Bereitstellung, Konfiguration und Optimierung von Hardware zu widmen, schreiben Sie Abfragen, mit denen Sie Ihre Daten transformieren und nützliche Einblicke erhalten. Unser Analysedienst ist in der Lage, umgehend Aufträge jeglicher Größenordnung zu verarbeiten. Wählen Sie dazu ganz einfach die jeweils erforderliche Ressourcenkapazität aus. Da Sie nur für die Leistung bezahlen, die während der Ausführung Ihres Auftrags tatsächlich in Anspruch genommen wurde, ist Data Lake eine überaus kosteneffektive Lösung. Durch die Unterstützung von Azure Active Directory kann Data Lake zur Verwaltung von Zugriffsberechtigungen und Rollen in Ihr lokales Identitätssystem integriert werden. Darüber hinaus umfasst dieser Dienst U-SQL, eine Sprache, bei der die Vorteile von SQL mit den Ausdrücken von Benutzercode kombiniert werden. Die skalierbare verteilte Laufzeit von U-SQL ermöglicht eine effiziente Analyse von Daten im Speicher sowie auf SQL Server-Instanzen in Azure, Azure SQL-Datenbank und Azure SQL Data Warehouse.


## Wichtige Funktionen

- **Dynamische Skalierung** 

    Data Lake Analytics wurde von Grund auf für eine hohe Leistung in der Cloud entwickelt und optimiert. Profitieren Sie von einer dynamischen Ressourcenbereitstellung und der Möglichkeit, Daten im Terabyte- oder sogar Exabytebereich zu analysieren. Sobald ein Auftrag abgeschlossen ist, werden die Ressourcen automatisch herunterskaliert, und Sie bezahlen nur für die tatsächlich in Anspruch genommene Verarbeitungsleistung. Außerdem sind keine Codeänderungen notwendig, wenn Sie die Menge an gespeicherten Daten oder die genutzte Computekapazität erhöhen oder verringern. So können Sie sich ausschließlich auf Ihre Geschäftslogik konzentrieren und müssen sich keinerlei Gedanken darüber machen, wie die großen Datenmengen verarbeitet oder gespeichert werden.

- **Entwickeln, debuggen und optimieren Sie Ihren Code schneller mithilfe vertrauter Tools**

    Data Lake Analytics ist nahtlos in Visual Studio integriert – dadurch können Sie für die Ausführung, das Debugging und die Optimierung Ihres Codes vertraute Tools nutzen. Mithilfe von Visualisierungen Ihrer U-SQL-Aufträge können Sie ermitteln, wie Ihr Code bei der Skalierung ausgeführt wird. Anhand dieser Einblicke lassen sich Leistungsengpässe leicht ermitteln und Kosten optimieren.

- **U-SQL: einfach und vertraut, problemlos erweiterbar**

    Data Lake Analytics enthält U-SQL, eine Abfragesprache, bei der die bekannte, einfache deklarative SQL-Sprache mit den Ausdrücken von C# kombiniert wird. U-SQL basiert auf derselben verteilten Laufzeit, die auch den Big Data-Systemen bei Microsoft zugrunde liegt. Millionen von SQL- und .NET-Entwickler können ihre Daten nun anhand bereits angeeigneter Fähigkeiten verarbeiten und analysieren.

- **Nahtlose Integration in Ihre vorhandenen IT-Systeme**

    Data Lake Analytics kann vorhandene IT-Systeme für Identitäten, Verwaltung, Sicherheit und Data Warehousing verwenden. Dies trägt zur Vereinfachung von Datengovernance und zur problemlosen Erweiterung aktueller Datenanwendungen dar. Data Lake Analytics ist für die Benutzerverwaltung und Berechtigungsvergabe in Active Directory integriert und enthält Überwachungs- und Auditierungsfunktionen.

- **Erschwinglich und kosteneffektiv**

    Data Lake Analytics ist eine kosteneffektive Lösung für die Ausführung von Big Data-Workloads. Die Bezahlung erfolgt bei Datenverarbeitung pro Auftrag. Sie benötigen weder Hardware noch Lizenzen oder dienstspezifische Supportvereinbarungen. Wenn Aufträge gestartet oder beendet werden, wird das System automatisch zentral hoch- oder herunterskaliert. So bezahlen Sie stets nur für die tatsächlich in Anspruch genommene Ressourcenmenge.

- **Funktioniert mit allen Ihren Daten in Azure**

    Data Lake Analytics arbeitet mit einer ganzen Reihe von Azure-Datenquellen zusammen, z. B. Azure-Blobspeicher und Azure SQL-Datenbanken, und ist natürlich speziell für Azure Data Lake-Speicher optimiert. So erreichen Sie höchste Ergebnisse bei Leistungsfähigkeit, Durchsatz und Parallelisierung für Big Data-Workloads.

## Weitere Informationen

- Erste Schritte
    - [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
    - [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Erste Schritte mit Data Lake Analytics mithilfe des Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL und Entwicklung
    - [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    - [Verwenden von U-SQL-Funktionen für Azure Data Lake Analytics-Aufträge](data-lake-analytics-use-window-functions.md)
    - [Entwickeln von benutzerdefinierten U-SQL-Operatoren für Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Verwaltung
    - [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md)
    - [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
    - [Überwachen und Problembehandeln von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Komplettes Lernprogramm
    - [Verwenden interaktiver Lernprogramme zu Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

- Teilen Sie uns Ihre Meinung mit
    - [Kommentieren Sie unseren Dokumentationsrückstand mit Vorschlägen für weitere Dokumentation](data-lake-analytics-documentation-backlog.md)
    - [Feature anfordern](http://aka.ms/adlafeedback)
    - [Hilfe in Foren](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0302_2016-->
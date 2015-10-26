<properties 
	pageTitle="Erste Schritte mit Tools für elastische Datenbanken" 
	description="Grundlegende Erläuterung der Funktion „Tools für elastische Datenbanken“ von Azure SQL, einschließlich einer einfachen Beispiel-App." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh@microsoft.com"/>

#Erste Schritte mit Tools für elastische Datenbanken

Die Anpassung der Kapazität an den Bedarf ist eines der zentralen Versprechen des Cloud-Computings. Ein wichtiges Verfahren für Datenbank-Anwendungen zum Erstellen von solchen skalierbaren Lösungen ist die als Sharding bezeichnete Methode, bei der Daten physisch über eine Anzahl von identisch strukturierten Datenbanken partitioniert werden. Bisher war jedoch für das Erstellen und Verwalten von Anwendungen, die Sharding verwenden, ein erheblicher Codierungsaufwand außerhalb der Geschäftslogik der Anwendung erforderlich.

Tools für elastische Datenbanken vereinfachen das Erstellen und Verwalten von Anwendungen, die Datenbank-Sharding in Azure SQL-Datenbank verwenden. Die Tools umfassen die Clientbibliothek für elastische Datenbanken und das Split-Merge-Tool. Zusammen implementieren sie die Infrastrukturaspekte des Sharding, sodass Sie sich auf die Geschäftslogik Ihrer Anwendung konzentrieren können.

In diesem Dokument werden die Entwicklerfunktionen mit der Clientbibliothek für elastische Datenbanken vorgestellt.

Weitere Informationen zur Funktionsweise der Tools für elastische Datenbanken finden Sie unter [Übersicht über die Tools für elastische Datenbanken](sql-database-elastic-scale-introduction.md).

Eine Liste aller Themen zu Tools für elastische Datenbanken finden Sie im [Lernkonzept](sql-database-elastic-scale-documentation-map.md).

## Die elastische Datenbank – Beispielanwendung

Mit dem Beispiel wird eine einfache Sharding-Anwendung erstellt, und es werden die wichtigsten Funktionen von Tools für elastische Datenbanken erkundet. Um die Anwendung herunterzuladen und auszuführen, führen Sie die unten oder im Video [Elastische Skalierung – Erste Schritte](http://channel9.msdn.com/Blogs/Windows-Azure/Elastic-Scale-with-Azure-SQL-Database-Getting-Started) angegebenen Schritte aus.

## Voraussetzungen
Zum Ausführen der Beispiel-App müssen Sie Visual Studio verwenden, und Sie müssen Zugriff auf eine Azure SQL-Datenbank haben, die in Azure ausgeführt wird. Wenn Sie noch nicht über ein Azure-Abonnement verfügen, können Sie sich für ein [kostenloses Abonnement](http://azure.microsoft.com/pricing/free-trial/) registrieren.
### Visual Studio und NuGet

1. Visual Studio 2012 oder höher mit C# wird benötigt. Laden Sie eine kostenlose Version unter [Visual Studio-Downloads](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) herunter.
2. NuGet 2.7 oder höher. Die aktuelle Version finden Sie unter [Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget) (Installieren von NuGet - in englischer Sprache).
### Erstellen einer Azure SQL-Datenbank

* Weitere Informationen finden Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md).

## Herunterladen und Ausführen der Beispiel-App

Die Beispielanwendung unter **Elastische Datenbank mit Azure SQL – Erste Schritte** veranschaulicht die wichtigsten Aspekte der Entwicklungsoberfläche für Sharding-Anwendungen, die Tools für elastische Datenbanken in Azure SQL verwenden. Dabei stehen die wichtigsten Anwendungsfälle für [Shard-Zuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md), [datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md) und [Abfragen mehrerer Shards](sql-database-elastic-scale-multishard-querying.md) im Mittelpunkt. Gehen Sie folgendermaßen vor, um das Beispiel herunterzuladen und auszuführen:

1. Öffnen Sie Visual Studio, und wählen Sie **Datei -> Neu -> Projekt** aus.
2. Klicken Sie im Dialogfeld auf **Online**.

    ![Neues Projekt > Online][2]
3. Klicken Sie anschließend unter **Beispiele** auf **Visual C#**.

    ![Auf Visual C# klicken][3]
4. Geben Sie in das Suchfeld **elastische Datenbank** ein, um nach dem Beispiel zu suchen. Der Titel **Tools für elastische Datenbanken für Azure SQL – Erste Schritte** wird angezeigt.

    ![Suchfeld][1]
 
5. Wählen Sie das Beispiel aus, wählen Sie einen Namen und einen Speicherort für das neue Projekt, und drücken Sie **OK**, um das Projekt zu erstellen.
6. Öffnen Sie die Datei **app.config** in der Lösung des Beispielprojekts, und folgen Sie den Anweisungen in der Datei, um den Namen des Azure SQL-Datenbankservers und Ihre Anmeldeinformationen (Benutzername und Kennwort) hinzuzufügen.
7. Erstellen Sie die Anwendung, und führen Sie sie aus. Gestatten Sie Visual Studio bei der entsprechenden Aufforderung, die NuGet-Pakete der Lösung wiederherzustellen. Dadurch wird die aktuelle Version der Clientbibliothek für elastische Datenbanken von NuGet heruntergeladen.
8. Spielen Sie mit den verschiedenen Optionen, um mehr über die Funktionen der Clientbibliothek zu erfahren. Beachten Sie die Schritte, welche die Anwendung in der Konsolenausgabe ausführt, und erkunden Sie den zugrunde liegenden Code.

    ![Fortschritt][4]

Herzlichen Glückwunsch! Sie haben Ihre erste Sharding-Anwendung mit den Tools für elastische Datenbanken in Azure SQL-Datenbank erfolgreich erstellt und ausgeführt. Werfen Sie einen Blick auf die Shards, die im Beispiel durch Herstellen einer Verbindung zwischen Visual Studio oder SQL-Server Management Studio und Ihrem Azure-Datenbankserver erstellt wurden. Sie sehen, dass mit dem Beispiel neue Beispiel-Shard-Datenbanken und eine Shard-Map-Managerdatenbank erstellt wurden.

**Hinweis** Wenn Sie nicht über SQL Server Management Studio verfügen, finden Sie unter [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio](sql-database-manage-azure-ssms.md) Anweisungen dazu, wie Sie dieses Tool erhalten.

### Zentrale Elemente des Codebeispiels

1. **Verwalten von Shards und Shard Maps**: Der Code in der Datei **ShardMapManagerSample.cs** veranschaulicht, wie Sie mit Shards, Bereichen und Zuordnungen arbeiten. Weitere Informationen über dieses Thema finden Sie hier: [Shard Map-Verwaltung](http://go.microsoft.com/?linkid=9862595).  
2. **Datenabhängiges Routing**: Das Routing von Transaktionen zum richtigen Shard wird in **DataDependentRoutingSample.cs** dargestellt. Weitere Einzelheiten finden Sie unter [Datenabhängiges Routing](http://go.microsoft.com/?linkid=9862596). 
3. **Abfragen mehrerer Shards**: Shard-übergreifendes Abfragen wird in der Datei **MultiShardQuerySample.cs** veranschaulicht. Weitere Einzelheiten finden Sie unter [Abfragen mehrerer Shards](http://go.microsoft.com/?linkid=9862597).
4. **Hinzufügen neuer Shards**: Das iterative Hinzufügen neuer leerer Shards wird mit dem Code in der Datei **AddNewShardsSample.cs** durchgeführt. Einzelheiten zu diesem Thema werden hier behandelt: [Shard Map-Verwaltung](http://go.microsoft.com/?linkid=9862595).

### Weitere Elastic Scale-Operationen

1. **Aufteilen eines vorhandenen Shards**: Die Möglichkeit zum Aufteilen von Shards wird über das **Split-Merge-Tool** bereitgestellt. Weitere Informationen zu diesem Tool finden Sie hier: [Übersicht über das Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Zusammenführen vorhandener Shards**: Shardzusammenführungen werden ebenfalls mit dem **Split-Merge-Tool** durchgeführt. Weitere Informationen finden Sie unter: [Übersicht über das Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge).   


## Kosten

Die Tools für elastische Datenbanken sind kostenlos. Durch Tools für elastische Datenbanken entstehen keine zusätzlichen Gebühren zu den Kosten der Azure-Nutzung.

Die Beispielanwendung erstellt z. B. neue Datenbanken. Die Kosten richten sich nach der Azure SQL DB-Datenbankedition, die Sie wählen, sowie nach der Azure-Nutzung Ihrer Anwendung.

Preisinformationen finden Sie in der [SQL-Datenbank – Preisdetails](http://azure.microsoft.com/pricing/details/sql-database/).

## Nächste Schritte
Weitere Informationen zu den Tools für elastische Datenbanken finden Sie unter:

* [Dokumentationsübersicht zu Tools für elastische Datenbanken](sql-database-elastic-scale-documentation-map.md) 
-    Codebeispiele: 
    -    [Elastische Datenbank mit Azure SQL – Erste Schritte](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Elastische Datenbank mit Azure SQL – Integration in Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Shard-Elastizität im Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [Elastic Scale-Ankündigung](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Channel 9: [Elastic Scale – Übersichtsvideo](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Diskussionsforum: [Azure SQL-Datenbank – Forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=Oct15_HO3-->
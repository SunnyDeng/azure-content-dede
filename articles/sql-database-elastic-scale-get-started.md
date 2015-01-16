<properties title="Get Started with  Azure SQL Database Elastic Scale" pageTitle="Erste Schritte mit Elastic Scale für die Azure SQL-Datenbank" description="Grundlegende Erklärung des Elastic Scale-Features der Azure SQL-Datenbank, einschließlich einer einfach auszuführenden Beispiel-App." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Erste Schritte mit der Vorschauversion von Elastic Scale für die Azure SQL-Datenbank

Die Anpassung der Kapazität an den Bedarf ist eines der zentralen Versprechen des Cloud-Computings. Die Einlösung dieses Versprechens war historisch gesehen für die Datenbankebene von Cloudanwendungen mühsam und komplex. In den letzten Jahren hat sich die Branche auf bewährte Entwurfsmuster geeinigt, die unter der Bezeichnung Sharding bekannt sind. Zwar ist das allgemeine Sharding-Muster auf die Herausforderung zugeschnitten, doch erfordert das Erstellen und Verwalten von Anwendungen mithilfe von Sharding bedeutende Infrastrukturinvestitionen, die von der Geschäftslogik der Anwendung unabhängig sind. 

Elastic Scale (Vorschauversion) für Azure SQL-Datenbank ermöglicht das Hoch- und Herunterskalieren der Datenebene einer Anwendung über Sharding-Verfahren nach Industriestandard und sorgt zugleich für eine enorme Optimierung der Entwicklung und Verwaltung Ihrer per Sharding bereitgestellten Cloud-Anwendungen. Elastic Scale bietet sowohl Entwickler- als auch Verwaltungsfunktionen, die über einen Satz von .NET-Bibliotheken und über Azure-Dienstvorlagen bereitgestellt werden. Diese Elemente können Sie in Ihrem eigenen Azure-Abonnement hosten, um Ihre hoch skalierbaren Anwendungen zu verwalten. Azure DB Elastic Scale implementiert die Infrastrukturaspekte des Sharding, sodass Sie sich auf die Geschäftslogik Ihrer Anwendung konzentrieren können. 

In diesem Dokument werden die Entwicklerfunktionen zu Elastic Scale für Azure SQL-Datenbank vorgestellt. 

Weitere Informationen zur Funktionsweise von Elastic Scale stehen unter [Übersicht über Elastic Scale](http://go.microsoft.com/?linkid=9862592)zur Verfügung.

Eine Lister aller Themen zu Elastic Scale finden Sie in der [Dokumentationsübersicht zu Elastic Scale.](./sql-database-elastic-scale-documentation-map.md)

## Die Elastic Scale-Beispielanwendung

Mit dem Beispiel wird eine einfache Sharding-Anwendung erstellt, und es werden die wichtigsten Funktionen von Elastic Scale erkundet. Führen Sie zum Herunterladen und Ausführen der Anwendung die unten oder im Video [Elastic Scale - Erste Schritte](http://go.microsoft.com/?linkid=9862983)angegebenen Schritte aus. 

###Voraussetzungen
Zum Ausführen der Beispiel-App müssen Sie Visual Studio verwenden, und Sie müssen Zugriff auf eine Azure SQL-Datenbank haben, die in Azure ausgeführt wird. Wenn Sie noch nicht über ein Azure-Abonnement verfügen, können Sie sich für ein [Testabonnement](http://azure.microsoft.com/de-de/pricing/free-trial/)registrieren.
####Visual Studio und NuGet

1. Visual Studio 2012 oder höher mit C# wird benötigt. Laden Sie eine kostenlose Version unter [Visual Studio-Downloads](http://www.visualstudio.com/de-de/downloads/download-visual-studio-vs.aspx)herunter.
2. NuGet 2.7 oder höher. Die aktuelle Version finden Sie unter [Installieren von NuGet.](http://docs.nuget.org/docs/start-here/installing-nuget)
####Erstellen einer Azure SQL-Datenbank

* Weitere Informationen stehen unter [Erste Schritte mit der Microsoft Azure SQL-Datenbank](http://azure.microsoft.com/de-de/documentation/articles/sql-database-get-started/)zur Verfügung.

##Herunterladen und Ausführen der Beispiel-App
Die Beispielanwendung unter **Elastic Scale mit Azure SQL-Datenbank - Erste Schritte** veranschaulicht die wichtigsten Aspekte der Entwicklungsoberfläche für Sharding-Anwendungen, die Azure SQL DB Elastic Scale verwenden. Der Schwerpunkt liegt auf wichtigen Anwendungsbeispielen für [Shard Map Management,](http://go.microsoft.com/?linkid=9862595)[datenabhängiges Routing](http://go.microsoft.com/?linkid=9862596) und [Abfragen mehrerer Shards](http://go.microsoft.com/?linkid=9862597). Gehen Sie folgendermaßen vor, um das Beispiel herunterzuladen und auszuführen: 

1. Öffnen Sie Visual Studio, und wählen Sie **Datei -> Neu -> Projekt** aus.
2. Klicken Sie im Dialogfeld auf **Online**.

    ![New Project>Online][2]
3. Klicken Sie anschließend unter **Beispiele** auf **Visual C#**.

    ![Click Visual C#][3]
4. Geben Sie in das Suchfeld **Elastic Scale** ein, um nach dem Beispiel zu suchen. Der Titel **Elastic Scale with Azure SQL Database-Getting Started** wird angezeigt.

    ![Search Box][1]
 
5. Wählen Sie das Beispiel aus, wählen Sie einen Namen und einen Speicherort für das neue Projekt aus, und klicken Sie auf **OK**, um das Projekt zu erstellen.
6. Öffnen Sie die Datei **app.config** in der Lösung des Beispielprojekts, und folgen Sie den Anweisungen in der Datei, um den Namen des Azure SQL-Datenbankservers und Ihre Anmeldeinformationen (Benutzername und Kennwort) hinzuzufügen.
7. Erstellen Sie die Anwendung, und führen Sie sie aus. Gestatten Sie Visual Studio bei der entsprechenden Aufforderung, die NuGet-Pakete der Lösung wiederherzustellen. Damit wird die aktuelle Version der Elastic Scale-Clientbibliotheken von NuGet heruntergeladen.
8. Spielen Sie mit den verschiedenen Optionen, um mehr über die Möglichkeiten von Elastic Scale zu erfahren. Beachten Sie die Schritte, welche die Anwendung in der Konsolenausgabe ausführt, und erkunden Sie den zugrunde liegenden Code.

    ![progress][4]

Herzlichen Glückwunsch! Sie haben Ihre erste Elastic Scale-Anwendung in Azure SQL-Datenbank erfolgreich erstellt und ausgeführt. Werfen Sie einen Blick auf die Shards, die im Beispiel durch Herstellen einer Verbindung zwischen SQL Server Management Studio und Ihrem Azure-Datenbankserver erstellt wurden. Sie sehen, dass mit dem Beispiel neue Beispiel-Shard-Datenbanken und eine Shard-Map-Managerdatenbank erstellt wurden.

**Hinweis** Wenn Sie nicht über SQL Server Management Studio verfügen, finden Sie unter [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio](http://azure.microsoft.com/de-de/documentation/articles/sql-database-manage-azure-ssms/)weitere Informationen, darunter Anweisungen, wie Sie dieses Tool erhalten können.  

### Zentrale Elemente des Codebeispiels

1. **Verwalten von Shards und Shard Maps**: Der in der Datei **ShardMapManagerSample.cs** veranschaulicht, wie Sie mit Shards, Bereichen und Zuordnungen arbeiten. Weitere Informationen über dieses Thema finden Sie hier: [Shard Map-Verwaltung](http://go.microsoft.com/?linkid=9862595).  
2. **Datenabhängiges Routing**: Das Routing von Transaktionen zum richtigen Shard wird in **DataDependentRoutingSample.cs** dargestellt. Ausführlichere Informationen stehen unter [Datenabhängiges Routing](http://go.microsoft.com/?linkid=9862596)zur Verfügung. 
3. **Abfragen mehrerer Shards**: Das Abfragen von Shards wird in der Datei **MultiShardQuerySample.cs** veranschaulicht. Ausführlichere Informationen stehen unter [Abfragen mehrerer Shards](http://go.microsoft.com/?linkid=9862597)zur Verfügung.
4. **Hinzufügen leerer Shards**: Das iterative Hinzufügen neuer leerer Shards wird mit dem Code in
der Datei **AddNewShardsSample.cs** durchgeführt. Einzelheiten zu diesem Thema werden hier behandelt: [Shard Map-Verwaltung](http://go.microsoft.com/?linkid=9862595).

### Weitere Elastic Scale-Operationen

1. **Aufteilen eines vorhandenen Shards**: Die Möglichkeit zum Aufteilen von Shards wird über den **Aufteilungs-/Zusammenführungsdienst** bereitgestellt. Weitere Informationen über diesen Dienst finden Sie hier: [Aufteilungs-/Zusammenführungsdienst](http://go.microsoft.com/?linkid=9862795).
2. **Zusammenführen vorhandener Shards**: Shardzusammenführungen werden ebenfalls mit dem **Aufteilungs-/Zusammenführungsdienst** durchgeführt. Weitere Informationen finden Sie unter: [Aufteilungs-/Zusammenführungsdienst](http://go.microsoft.com/?linkid=9862795).   


## Kosten

Die Elastic Scale-Bibliotheken und Dienstvorlagen sind kostenlos. Mit Elastic Scale entstehen keine zusätzlichen Gebühren zu den Kosten der Azure-Nutzung. 

Die Beispielanwendung erstellt z. B. neue Datenbanken. Die Kosten richten sich nach der Azure SQL DB-Datenbankedition, die Sie wählen, sowie nach der Azure-Nutzung Ihrer Anwendung.

Preisinformationen stehen unter [SQL-Datenbank - Preisdetails](http://azure.microsoft.com/de-de/pricing/details/sql-database/)zur Verfügung.

## Nächste Schritte
Weitere Informationen über die Elastic Scale-Funktion finden Sie unter:

* [Elastic Scale-Lernseite](./sql-database-elastic-scale-documentation-map.md) 
-    Codebeispiele: 
    -    [Elastic Scale mit Azure SQL-Datenbank - Erste Schritte](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Elastic Scale mit Azure SQL-Datenbank - Integration in Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Shard-Elastizität im Script Center](http://go.microsoft.com/?linkid=9862617)
-    Blog: [Elastic Scale-Ankündigung](http://go.microsoft.com/?linkid=9862608)
-    Channel 9: [Elastic Scale - Übersichtsvideo](http://go.microsoft.com/?linkid=9862609)
-    Diskussionsforum: [Azure SQL-Datenbank - Forum](http://social.msdn.microsoft.com/forums/azure/de-de/home?forum=ssdsgetstarted)


<!--Anchors-->
[Elastic Scale-Beispielanwendung]: #The-Elastic-Scale-Sample-Application
[Herunterladen und Ausführen der Beispiel-App]: #Download-and-Run-the-Sample-App
[Kosten]: #Cost
[Nächste Schritte]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=35.1-->

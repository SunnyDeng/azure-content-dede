<properties title="Erste Schritte mit Elastic Scale f&uuml;r Azure SQL-Datenbank" pageTitle="Erste Schritte mit Elastic Scale f&uuml;r Azure SQL-Datenbank" description="Grundlegende Erl&auml;uterung von Elastic Scale f&uuml;r Azure SQL-Datenbank einschlie&szlig;lich einer einfachen Beispiel-App." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Erste Schritte mit der Vorschauversion von Elastic Scale für Azure SQL-Datenbank

Die Anpassung der Kapazität an den Bedarf ist eines der zentralen Versprechen des Cloud-Computings. Die Einlösung dieses Versprechens war historisch gesehen für die Datenbankebene von Cloudanwendungen mühsam und komplex. In den letzten Jahren hat sich die Branche auf bewährte Entwurfsmuster geeinigt, die unter der Bezeichnung Sharding bekannt sind. Zwar ist das allgemeine Sharding-Muster auf die Herausforderung zugeschnitten, doch erfordert das Erstellen und Verwalten von Anwendungen mithilfe von Sharding bedeutende Infrastrukturinvestitionen, die von der Geschäftslogik der Anwendung unabhängig sind.

Elastic Scale (Vorschauversion) für Azure SQL-Datenbank ermöglicht das Hoch- und Herunterskalieren der Datenebene einer Anwendung über Sharding-Verfahren nach Industriestandard und sorgt zugleich für eine enorme Optimierung der Entwicklung und Verwaltung Ihrer per Sharding bereitgestellten Cloud-Anwendungen. Elastic Scale bietet sowohl Entwickler- als auch Verwaltungsfunktionen, die über einen Satz von .NET-Bibliotheken und über Azure-Dienstvorlagen bereitgestellt werden. Diese Elemente können Sie in Ihrem eigenen Azure-Abonnement hosten, um Ihre hoch skalierbaren Anwendungen zu verwalten. Azure DB Elastic Scale implementiert die Infrastrukturaspekte des Sharding, sodass Sie sich auf die Geschäftslogik Ihrer Anwendung konzentrieren können.

In diesem Dokument werden die Entwicklerfunktionen zu Elastic Scale für Azure SQL-Datenbank vorgestellt.

Weitere Informationen über die Funktionsweise von Elastic Scale finden Sie unter [Übersicht über Elastic Scale][Übersicht über Elastic Scale].

Eine Liste aller Themen zu Elastic Scale finden Sie unter [Dokumentationsübersicht zu Elastic Scale][Dokumentationsübersicht zu Elastic Scale]

## Die Elastic Scale-Beispielanwendung

Mit dem Beispiel wird eine einfache Sharding-Anwendung erstellt, und es werden die wichtigsten Funktionen von Elastic Scale erkundet. Um die Anwendung herunterzuladen und auszuführen, führen Sie die unten oder im Video [Elastic Scale - Erste Schritte][Elastic Scale - Erste Schritte] angegebenen Schritte aus.

### Voraussetzungen

Zum Ausführen der Beispiel-App müssen Sie Visual Studio verwenden, und Sie müssen Zugriff auf eine Azure SQL-Datenbank haben, die in Azure ausgeführt wird. Wenn Sie noch nicht über ein Azure-Abonnement verfügen, können Sie sich für ein [kostenloses Abonnement][kostenloses Abonnement] registrieren.

#### Visual Studio und NuGet

1.  Visual Studio 2012 oder höher mit C# wird benötigt. Laden Sie eine kostenlose Version unter [Visual Studio-Downloads][Visual Studio-Downloads] herunter.
2.  NuGet 2.7 oder höher. Die aktuelle Version finden Sie unter [Installing NuGet][Installing NuGet] (Installieren von NuGet - in englischer Sprache).

    #### Erstellen einer Azure SQL-Datenbank

-   Weitere Informationen finden Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank][Erste Schritte mit Microsoft Azure SQL-Datenbank].

## Herunterladen und Ausführen der Beispiel-App

Die Beispielanwendung unter **Elastic Scale mit Azure SQL-Datenbank - Erste Schritte** veranschaulicht die wichtigsten Aspekte der Entwicklungsoberfläche für Sharding-Anwendungen, die Elastic Scale von Azure SQL-Datenbank verwenden. Dabei stehen die wichtigsten Anwendungsfälle für [Shard Map-Verwaltung][Shard Map-Verwaltung], [datenabhängiges Routing][datenabhängiges Routing] und [Abfragen mehrerer Shards][Abfragen mehrerer Shards] im Mittelpunkt. Gehen Sie folgendermaßen vor, um das Beispiel herunterzuladen und auszuführen:

1.  Öffnen Sie Visual Studio, und wählen Sie **Datei -\> Neu -\> Projekt** aus.
2.  Klicken Sie im Dialogfeld auf **Online**.

    ![Neues Projekt \> Online][Neues Projekt \> Online]

3.  Klicken Sie anschließend unter **Beispiele** auf **Visual C#**.

    ![Auf Visual C# klicken][Auf Visual C# klicken]

4.  Geben Sie in das Suchfeld **Elastic Scale** ein, um nach dem Beispiel zu suchen. Der Titel **Elastic Scale with Azure SQL Database-Getting Started** wird angezeigt.

    ![Suchfeld][Suchfeld]

5.  Wählen Sie das Beispiel aus, wählen Sie einen Namen und einen Speicherort für das neue Projekt, und drücken Sie **OK**, um das Projekt zu erstellen.
6.  Öffnen Sie die Datei **app.config** in der Lösung des Beispielprojekts, und folgen Sie den Anweisungen in der Datei, um den Namen des Azure SQL-Datenbankservers und Ihre Anmeldeinformationen (Benutzername und Kennwort) hinzuzufügen.
7.  Erstellen Sie die Anwendung, und führen Sie sie aus. Gestatten Sie Visual Studio bei der entsprechenden Aufforderung, die NuGet-Pakete der Lösung wiederherzustellen. Damit wird die aktuelle Version der Elastic Scale-Clientbibliotheken von NuGet heruntergeladen.
8.  Spielen Sie mit den verschiedenen Optionen, um mehr über die Möglichkeiten von Elastic Scale zu erfahren. Beachten Sie die Schritte, welche die Anwendung in der Konsolenausgabe ausführt, und erkunden Sie den zugrunde liegenden Code.

    ![Fortschritt][Fortschritt]

Herzlichen Glückwunsch! Sie haben Ihre erste Elastic Scale-Anwendung in Azure SQL-Datenbank erfolgreich erstellt und ausgeführt. Werfen Sie einen Blick auf die Shards, die im Beispiel durch Herstellen einer Verbindung zwischen SQL Server Management Studio und Ihrem Azure-Datenbankserver erstellt wurden. Sie sehen, dass mit dem Beispiel neue Beispiel-Shard-Datenbanken und eine Shard-Map-Managerdatenbank erstellt wurden.

**Hinweis** Wenn Sie nicht über SQL Server Management Studio verfügen, finden Sie unter [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio][Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio] Anweisungen dazu, wie Sie dieses Tool erhalten.

### Zentrale Elemente des Codebeispiels

1.  **Verwalten von Shards und Shard-Maps**: Der Code in der Datei **ShardMapManagerSample.cs** veranschaulicht, wie Sie mit Shards, Bereichen und Zuordnungen arbeiten. Weitere Informationen über dieses Thema finden Sie hier: [Shard Map-Verwaltung][Shard Map-Verwaltung].
2.  **Datenabhängiges Routing**: Das Routing von Transaktionen zum richtigen Shard wird in **DataDependentRoutingSample.cs** dargestellt. Weitere Einzelheiten finden Sie unter [Datenabhängiges Routing][datenabhängiges Routing].
3.  **Abfragen mehrerer Shards**: Das Abfragen von Shards wird in der Datei **MultiShardQuerySample.cs** veranschaulicht. Weitere Einzelheiten finden Sie unter [Abfragen mehrerer Shards][Abfragen mehrerer Shards].
4.  **Hinzufügen leerer Shards**: Das iterative Hinzufügen neuer leerer Shards wird mit dem Code in der
    Datei **AddNewShardsSample.cs** ausgeführt. Einzelheiten zu diesem Thema werden hier behandelt: [Shard Map-Verwaltung][Shard Map-Verwaltung].

### Weitere Elastic Scale-Operationen

1.  **Aufteilen eines vorhandenen Shards**: Die Möglichkeit zum Aufteilen von Shards wird vom **Aufteilungs-/Zusammenführungsdienst** bereitgestellt. Weitere Informationen über diesen Dienst finden Sie hier: [Aufteilungs-/Zusammenführungsdienst][Aufteilungs-/Zusammenführungsdienst].
2.  **Zusammenführen vorhandener Shards**: Shard-Zusammenführungen werden ebenfalls mit dem **Aufteilungs-/Zusammenführungsdienst** ausgeführt. Weitere Informationen finden Sie unter: [Aufteilungs-/Zusammenführungsdienst][Aufteilungs-/Zusammenführungsdienst].

## Kosten

Die Elastic Scale-Bibliotheken und Dienstvorlagen sind kostenlos. Mit Elastic Scale entstehen keine zusätzlichen Gebühren zu den Kosten der Azure-Nutzung.

Die Beispielanwendung erstellt z. B. neue Datenbanken. Die Kosten richten sich nach der Azure SQL DB-Datenbankedition, die Sie wählen, sowie nach der Azure-Nutzung Ihrer Anwendung.

Preisinformationen finden Sie in der [SQL-Datenbank – Preisdetails][SQL-Datenbank – Preisdetails].

## Nächste Schritte

Weitere Informationen über die Elastic Scale-Funktion finden Sie unter:

-   [Elastic Scale-Lernseite][Dokumentationsübersicht zu Elastic Scale]
-   Codebeispiele:

    -   [Elastic Scale mit Azure SQL-Datenbank - Erste Schritte][Elastic Scale mit Azure SQL-Datenbank - Erste Schritte]
    -   [Elastic Scale mit Azure SQL-Datenbank - Integration in Entity Framework][Elastic Scale mit Azure SQL-Datenbank - Integration in Entity Framework]
    -   [Shard-Elastizität im Script Center][Shard-Elastizität im Script Center]
-   Blog: [Elastic Scale-Ankündigung][Elastic Scale-Ankündigung]
-   Channel 9: [Übersicht über Elastic Scale][1]
-   Diskussionsforum: [Azure SQL-Datenbank-Forum][Azure SQL-Datenbank-Forum]

<!--Anchors-->
<!--Image references-->

  [Übersicht über Elastic Scale]: http://go.microsoft.com/?linkid=9862592
  [Dokumentationsübersicht zu Elastic Scale]: ./sql-database-elastic-scale-documentation-map.md
  [Elastic Scale - Erste Schritte]: http://go.microsoft.com/?linkid=9862983
  [kostenloses Abonnement]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Visual Studio-Downloads]: http://www.visualstudio.com/de-de/downloads/download-visual-studio-vs.aspx
  [Installing NuGet]: http://docs.nuget.org/docs/start-here/installing-nuget
  [Erste Schritte mit Microsoft Azure SQL-Datenbank]: http://azure.microsoft.com/de-de/documentation/articles/sql-database-get-started/
  [Shard Map-Verwaltung]: http://go.microsoft.com/?linkid=9862595
  [datenabhängiges Routing]: http://go.microsoft.com/?linkid=9862596
  [Abfragen mehrerer Shards]: http://go.microsoft.com/?linkid=9862597
  [Neues Projekt \> Online]: ./media/sql-database-elastic-scale-get-started/click-online.png
  [Auf Visual C# klicken]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
  [Suchfeld]: ./media/sql-database-elastic-scale-get-started/newProject.png
  [Fortschritt]: ./media/sql-database-elastic-scale-get-started/output2.png
  [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio]: http://azure.microsoft.com/de-de/documentation/articles/sql-database-manage-azure-ssms/
  [Aufteilungs-/Zusammenführungsdienst]: http://go.microsoft.com/?linkid=9862795
  [SQL-Datenbank – Preisdetails]: http://azure.microsoft.com/de-de/pricing/details/sql-database/
  [Elastic Scale mit Azure SQL-Datenbank - Erste Schritte]: http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE
  [Elastic Scale mit Azure SQL-Datenbank - Integration in Entity Framework]: http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE
  [Shard-Elastizität im Script Center]: http://go.microsoft.com/?linkid=9862617
  [Elastic Scale-Ankündigung]: http://go.microsoft.com/?linkid=9862608
  [1]: http://go.microsoft.com/?linkid=9862609
  [Azure SQL-Datenbank-Forum]: http://social.msdn.microsoft.com/forums/azure/de-de/home?forum=ssdsgetstarted

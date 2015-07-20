<properties 
	pageTitle="Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen - Azure Mobile Services" 
	description="Erfahren Sie, wie Sie über Hybridverbindungen eine Verbindung mit einem lokalen SQL-Server von einem mobilen Azure-Dienst herstellen." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="glenga"/>

  
# Verbinden mit einem lokalen SQL Server über Azure Mobile Services mithilfe von Hybridverbindungen 

Wenn Ihr Unternehmen auf die Cloud umstellt, können Sie möglicherweise nicht alle Ihre Ressourcen sofort zu Azure migrieren. Mit Hybridverbindungen können Azure Mobile Services sichere Verbindungen mit Ihren lokalen Ressourcen herstellen. Auf diese Weise können Sie Ihre lokalen Daten mithilfe von Azure für Ihre mobilen Clients zugänglich machen. Unterstützt werden unter anderem alle Ressourcen, die auf einem statischen TCP-Port laufen, inklusive Microsoft SQL Server, MySQL, HTTP Web-APIs und die meisten selbst entwickelten Webdienste. Hybridverbindungen verwenden SAS-Autorisierung (Shared Access Signature) zum Sichern der Verbindungen vom mobilen Dienst und dem lokalen Hybrid Connection Manager zur Hybridverbindung. Weitere Informationen finden Sie unter [Übersicht über Hybridverbindungen](../integration-hybrid-connection-overview.md).

In diesem Lernprogramm erfahren Sie, wie Sie einen mobilen .NET Back-End-Dienst so anpassen können, dass er eine lokale SQL Server-Datenbank anstatt der standardmäßig mit Ihrem Dienst bereitgestellten Azure SQL-Datenbank verwendet. Hybridverbindungen werden auch für einen mobilen JavaScript-Back-End-Dienst unterstützt, wie in [diesem Artikel](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx) beschrieben.

##Voraussetzungen##

Für dieses Lernprogramm müssen Sie über Folgendes verfügen:

- **Einen vorhandenen mobilen .NET-Back-End-Dienst** <br/>Führen Sie die Schritte im Lernprogramm [Erste Schritte mit Mobile Services] aus, um über das [Azure-Verwaltungsportal] einen neuen .NET-Back-End-Dienst zu erstellen und herunterzuladen.

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Erstellen einer Hybridverbindung

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## Konfigurieren des Projekts mit dem mobilen Dienst zum Herstellen einer Verbindung mit der SQL Server-Datenbank

In diesem Schritt definieren Sie eine Verbindungszeichenfolge für die lokale Datenbank und ändern den mobilen Dienst, um diese Verbindung zu verwenden.

1. Öffnen Sie in Visual Studio 2013 das Projekt, in dem Ihr mobiler .NET-Back-End-Dienst definiert wird. 

	Informationen zum Herunterladen Ihres .NET Back-End-Projekts finden Sie unter [Erste Schritte mit Mobile Services](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md).

2. Öffnen Sie im Projektmappen-Explorer die Datei "Web.config", suchen Sie den Abschnitt **connectionStrings**, fügen Sie einen neuen SqlClient-Eintrag, der auf die lokale SQL Server-Datenbank verweist, wie folgt hinzu:
	
	    <add name="OnPremisesDBConnection" 
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	Denken Sie daran, `<**secure_password**>` in dieser Zeichenfolge durch das Kennwort zu ersetzen, das Sie für *HybridConnectionLogin* erstellt haben.
	
3. Klicken Sie in Visual Studio auf **Speichern**, um die Datei "Web.config" zu speichern.

	> [AZURE.NOTE]Diese Verbindungseinstellung wird bei Ausführung auf dem lokalen Computer verwendet. Bei der Ausführung in Azure wird diese Einstellung durch die Verbindungseinstellung überschrieben, die im Portal definiert wird.

4. Erweitern Sie im den Ordner **Modelle**, und öffnen Sie die Datenmodelldatei, deren Name mit *Context.cs* endet.

6. Ändern Sie den **DbContext**-Instanzkonstruktor zum Übergeben des Werts `OnPremisesDBConnection` an den **DbContext**-Basiskonstruktor (ähnlich wie im folgenden Codeausschnitt):

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	Der Dienst verwendet jetzt die neue Verbindung zur SQL Server-Datenbank.
 
##Lokales Testen der Datenbankverbindung

Vor dem Veröffentlichen in Azure und dem Verwenden der Hybridverbindung sollten Sie sicherstellen, dass die Datenbankverbindung funktioniert, wenn sie lokal ausgeführt wird. Auf diese Weise können Sie leichter Verbindungsprobleme diagnostizieren und beheben, bevor Sie erneut veröffentlichen und die Hybridverbindung verwenden.

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## Aktualisieren von Azure, um die lokale Verbindungszeichenfolge zu verwenden

Da Sie jetzt die Datenbankverbindung überprüft haben, müssen Sie eine App-Einstellung für diese neue Verbindungszeichenfolge hinzufügen, damit sie von Azure verwendet werden kann, und den mobilen Dienst in Azure veröffentlichen.

1. Navigieren Sie im [Azure-Verwaltungsportal] zu dem mobilen Dienst.
  
1. Klicken Sie auf die Registerkarte **Konfigurieren**, und suchen Sie den Abschnitt **Verbindungszeichenfolgen**.

	![Verbindungszeichenfolge für die lokale Datenbank](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. Fügen Sie eine neue **SQL Server**-Verbindungszeichenfolge mit dem Namen `OnPremisesDBConnection` und dem folgenden Wert hinzu:

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	Ersetzen Sie `<**secure_password**>` durch das sichere Kennwort für *HybridConnectionLogin*.

2. Klicken Sie auf **Speichern**, um die Hybridverbindung und die soeben erstellte Verbindungszeichenfolge zu speichern.

3. Veröffentlichen Sie mithilfe von Visual Studio das aktualisierte Projekt mit dem mobilen Dienst in Azure.

	Die Startseite des Diensts wird angezeigt.

4. Verwenden Sie entweder wie zuvor die Schaltfläche **Ausprobieren** auf der Startseite, oder rufen Sie mithilfe einer Client-App, die mit dem mobilen Dienst verbunden ist, einige Vorgänge auf, die Datenbankänderungen generieren.

	>[AZURE.NOTE]Bei Verwendung der Schaltfläche **Ausprobieren** zum Starten der Hilfe-API-Seiten, denken Sie daran, Ihren Anwendungsschlüssel als Kennwort (mit einem leeren Benutzernamen) anzugeben.

4. Stellen Sie in SQL Server Management Studio eine Verbindung mit der SQL Server-Instanz her, öffnen Sie den Objekt-Explorer, erweitern Sie die **OnPremisesDB**-Datenbank, und erweitern Sie **Tabellen**.

5. Klicken Sie mit der rechten Maustaste auf die Tabelle **hybridService1.TodoItems**, und wählen Sie **Top 1000 Zeilen auswählen** aus, um die Ergebnisse anzuzeigen.

	Beachten Sie, dass in der App generierte Änderungen vom mobilen Dienst unter Verwendung der Hybridverbindung in der lokalen Datenbank gespeichert wurden.

##Siehe auch##
 
+ [Website zu Hybridverbindungen](../../services/biztalk-services/)
+ [Überblick über Hybridverbindungen](../integration-hybrid-connection-overview.md)
+ [BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Skalieren", "Konfigurieren" und "Hybridverbindungen"](../biztalk-dashboard-monitor-scale-tabs.md)
+ [Ändern des Datenmodells eines mobilen .NET-Back-End-Diensts](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->

<!-- Links -->
[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=July15_HO2-->
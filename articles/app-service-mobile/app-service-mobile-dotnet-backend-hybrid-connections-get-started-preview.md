<properties 
	pageTitle="Verbinden Sie Ihre Azure-Mobile-Anwendung mit einer lokalen SQL Server-hybridverbindungen" 
	description="Erfahren Sie, wie mit einer App Service Mobile App hybridverbindungen eine Verbindung mit einer lokalen SQL Server" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

  
# Eine Verbindung mit einer lokalen SQL Server Mobile-Anwendungen hybridverbindungen 

Wenn Ihr Unternehmen Übergänge in die Cloud, Sie alle Ihre Ressourcen sofort zu Azure migrieren möglicherweise nicht. Hybridverbindungen können Mobile-Anwendungen Azure App Dienst sichere Verbindungen mit Ihrer lokalen Ressourcen herstellen. Auf diese Weise können Sie Ihre lokalen Daten Ihre mobilen Clients zugänglich machen, mithilfe von Azure. Unterstützt werden unter anderem alle Ressourcen, die auf einem statischen TCP-Port laufen, inklusive Microsoft SQL Server, MySQL, HTTP Web-APIs und die meisten selbst entwickelten Webdienste. Hybridverbindungen verwenden Sie Shared Access Signature (SAS)-Autorisierung zum Sichern der Verbindungen aus Ihrem mobilen Dienst und den lokalen Hybrid Connection Manager der Hybrid-Verbindung. Weitere Informationen finden Sie unter [Übersicht über Hybridverbindungen](../integration-hybrid-connection-overview.md).

In diesem Lernprogramm erfahren Sie, wie eine Mobile App .NET Back-End-Verwendung eine lokalen SQL Server-Datenbank statt der standardmäßigen Azure SQL-Datenbank, die mit Ihrem Dienst bereitgestellten geändert werden.

##Voraussetzungen##

In diesem Lernprogramm müssen Sie über Folgendes verfügen:

- **Einer vorhandenen Mobile App-Back-End-** <br/>Führen Sie die [Schnellstart-Lernprogramm](app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md) erstellen und Herunterladen einer neuen .NET Back-End-Mobile-Anwendung aus der [Azure Portal].

[AZURE.INCLUDE [Hybrid-Verbindungen-Voraussetzungen](../../includes/hybrid-connections-prerequisites.md)]

## Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank

[AZURE.INCLUDE [Hybrid-Connections-Create-On-Premises-Database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Erstellen einer Hybridverbindung

Sie müssen eine neue Hybridverbindung und BizTalk-Dienst für den Codeteil Ihrer mobilen Anwendung Back-End einer Webanwendung handelt.

1. In der [Azure Portal], navigieren Sie zu Ihrer Mobile-Anwendung, und klicken Sie auf die Schaltfläche im Web-app-Back-End.

	![Navigieren Sie zur Webanwendung](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started-preview/mobile-app-link-to-web-app-backend.png)

	Dadurch gelangen Sie auf die Webanwendung, die implementiert die Back-End-code für die Mobile App, also den Namen der mobilen Anwendung gefolgt von `-code`.

2. Bildlauf nach unten Blade und klicken Sie auf der Webanwendung **hybridverbindungen**.
	
	![Hybridverbindungen](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started-preview/start-hybrid-connection.png)
	
2. Klicken Sie auf das Hybrid-Verbindungen-Blade auf **Hinzufügen** dann **neue hybridverbindung**.
	
3. Auf der **Erstellen Hybrid Connection Blade**, bieten ein **Name** und **Hostnamen** für die hybridverbindung und **Port** auf `1433`.
	
	![Hybridverbindung erstellen](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started-preview/create-hybrid-connection.png)

4. Klicken Sie auf **Biz sprechen Service** und geben Sie einen Namen für den BizTalk-Dienst, und klicken Sie auf **OK** zweimal.

	Dieses Lernprogramm verwendet **mobile1**. Sie müssen einen eindeutigen Namen für Ihren neuen BizTalk Service angeben.

	Nach Abschluss der der **Benachrichtigungen** Bereich blinkt die grüne **Erfolg** und **hybridverbindung** Blade zeigt die neue hybridverbindung mit dem Status als **nicht verbunden**.
	
	![Eine Hybridverbindung wurde erstellt](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started-preview/hybrid-connection-created.png)
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

## Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung

[AZURE.INCLUDE [App-Service-Hybrid-Connections-Manager-Install](../../includes/app-service-hybrid-connections-manager-install.md)]

## Konfigurieren Sie das Mobile App-Back-End-Projekt zum Herstellen einer SQL Server-Datenbank

In diesem Schritt definieren eine Verbindungszeichenfolge für die lokale Datenbank vorhanden sein, und ändern das Mobile App-Back-End für diese Verwendung wird.

1. Öffnen Sie das Projekt, das Ihre Mobile App-Back-End definiert, in Visual Studio 2013. 

	Weitere Informationen zum Herunterladen von .NET Back-End-Projekt finden Sie unter [Schnellstart-Lernprogramm](app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md).

2. Öffnen Sie im Projektmappen-Explorer die Datei "Web.config", suchen Sie die **ConnectionStrings** Abschnitt, fügen Sie einen neuen SqlClient-Eintrag wie folgt aus, die auf der lokalen SQL Server-Datenbank verweist:
	
	    <add name="OnPremisesDBConnection" 
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	Denken Sie daran, ersetzen Sie `<**secure_password**>` in dieser Zeichenfolge mit dem Kennwort, das Sie erstellt, für die haben *HbyridConnectionLogin*.
	
3. Klicken Sie in Visual Studio auf **Speichern**, um die Datei "Web.config" zu speichern.

	> [AZURE.NOTE]Diese Verbindungseinstellung wird verwendet, wenn auf dem lokalen Computer ausgeführt. In Azure ausgeführt wird, wird diese Einstellung von der Verbindungseinstellung im Portal definiert.

4. Erweitern Sie die **Modelle** Ordner, und öffnen Sie den Modell-Datendatei, die Enden *Context.cs*.

6. Ändern der **DbContext** Instanzkonstruktor übergeben Sie den Wert `OnPremisesDBConnection` auf der Basis **DbContext** Konstruktor, ähnlich wie im folgenden Codeausschnitt:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	Der Dienst wird jetzt die neue Verbindung mit SQL Server-Datenbank verwenden.
 
##Testen der Verbindung mit der Datenbank lokal

Vor dem Veröffentlichen in Azure und die hybridverbindung verwenden, ist es eine gute Idee, sicherzustellen, dass die Datenbankverbindung funktioniert, wenn lokal ausgeführt. Auf diese Weise können leichter diagnostizieren und Verbindungsprobleme zu beheben, bevor Sie erneut veröffentlichen und nutzen Sie die hybridverbindung.

[AZURE.INCLUDE [App-Service-Mobile-dotnet-Backend-Test-Local-Service-API-Documentation](../../includes/app-service-mobile-dotnet-backend-test-local-service-api-documentation.md)]

## Aktualisieren von Azure, um die lokale Verbindungszeichenfolge zu verwenden.

Nun, dass Sie die Datenbankverbindung überprüft haben, müssen Sie eine Anwendungseinstellung für diese neue Verbindungszeichenfolge hinzufügen, damit sie von Azure verwendet werden kann.

1. In der [Azure Portal] klicken Sie in der Web app-Back-End-Code für die Mobile Anwendung auf **alle Einstellungen**, dann **Anwendungseinstellungen**. 

3. In der **Web-app-Einstellungen** Blade, führen Sie einen Bildlauf nach unten zum **Verbindungszeichenfolgen** und Hinzufügen einer neuen **SQL Server** Verbindungszeichenfolge mit dem Namen `OnPremisesDBConnection` mit einem Wert wie `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.

	Ersetzen Sie `<**secure_password**>` durch das sichere Kennwort für Ihre lokale Datenbank.

	![Verbindungszeichenfolge für die lokale Datenbank](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started-preview/set-sql-server-database-connection.png)

2. Drücken Sie **Speichern** zum Speichern des hybrides und Verbindung Zeichenfolge Sie gerade erstellt haben.

## Veröffentlichen Sie und Testen Sie das Mobile App-Back-End in Azure 

Abschließend müssen Sie das Mobile App-Back-End auf Azure veröffentlichen, und stellen Sie sicher, dass die hybridverbindung zum Speichern von Daten in der lokalen Datenbank verwendet wird.

3. Im Visual Studio mit der rechten Maustaste, klicken Sie auf **Veröffentlichen**, klicken Sie dann im **Web veröffentlichen** klicken Sie auf **Microsoft Azure-Websites**. 

	Anstelle von Visual Studio können Sie auch [Verwenden Sie Git zum Veröffentlichen von Ihrem Back-End-](mobile-services-dotnet-backend-store-code-source-control.md).

2. Melden Sie sich mit Ihrer Azure-Anmeldeinformationen, und wählen Sie den Dienst aus **vorhandene Websites auswählen**.

	Visual Studio lädt die Veröffentlichungseinstellungen direkt von Azure herunter.

3. Klicken Sie abschließend auf **Veröffentlichen**.

	Nach der Veröffentlichung abgeschlossen ist, wird der Dienst neu gestartet wird und die Back-End-Startseite angezeigt.

4. Verwenden entweder die **Probieren Sie es jetzt** wie zuvor auf der Startseite Schaltfläche oder mithilfe einer Clientanwendung, die mit der Mobile-Anwendung verbunden sind, rufen Sie einige Vorgänge, die Datenbankänderungen generieren.

	>[AZURE.NOTE]Bei Verwendung der **jetzt testen** Schaltfläche zum Starten der Hilfe-API-Seiten, denken Sie daran, Ihre Anwendungsschlüssel als das Kennwort (mit einem leeren Benutzernamen) angeben.

4. In SQL Server Management Studio eine Verbindung mit SQL Server-Instanz herstellen, öffnen Sie den Objekt-Explorer, erweitern Sie die **OnPremisesDB** Datenbank und erweitern Sie **Tabellen**.

5. Klicken Sie mit der rechten Maustaste auf die Tabelle **hybridService1.TodoItems** und wählen Sie **Top 1000 Zeilen auswählen** aus, um die Ergebnisse anzuzeigen.

	Beachten Sie, dass Änderungen in Ihrer Clientanwendung generiert durch Ihre Mobile App-Back-End zur lokalen Datenbank unter Verwendung der hybridverbindung gespeichert wurden.

##Siehe auch##
 
+ [Website zu Hybridverbindungen](../../services/biztalk-services/)
+ [Überblick über Hybridverbindungen](../integration-hybrid-connection-overview.md)
+ [BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Skalieren", "Konfigurieren" und "Hybridverbindungen"](../biztalk-dashboard-monitor-scale-tabs.md)
+ [Erläutert, wie Sie Änderungen des Datenmodells an einen mobilen .NET-Back-End-Dienst](../mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->

<!-- Links -->
[Azure Portal]: https://portal.azure.com/
[Azure Management Portal]: http://go.microsoft.com/fwlink/p/?linkid=213885
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->
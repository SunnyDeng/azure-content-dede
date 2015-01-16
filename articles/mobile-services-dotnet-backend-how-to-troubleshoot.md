<properties urlDisplayName="Troubleshoot the Mobile Services .NET Backend" pageTitle="Problembehandlung beim Mobile Services .NET-Back-End - Azure Mobile Services" metaKeywords="" description="Erfahren Sie, wie Sie Probleme mit Ihren mobilen Diensten mithilfe des .NET-Back-Ends diagnostizieren und beheben können." metaCanonical="" services="" documentationCenter="Mobile" title="Troubleshoot the Mobile Services .NET Backend" authors="mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender" />
# Problembehandlung beim Mobile Services .NET-Back-End

Entwickeln mit Mobile Services ist normalerweise kurz und schmerzlos, doch selbst dann kann etwas schieflaufen. Dieses Lernprogramm behandelt einige Techniken, mit denen Sie häufige Probleme behandeln können, die mit dem Mobile Services .NET-Back-End auftreten können. 

1. [HTTP-Debugging](#HttpDebugging)
2. [Laufzeit-Debugging](#RuntimeDebugging)
3. [Analysieren der Diagnoseprotokolle](#Logs)
4. [Debuggen der Cloud Assembly-Auflösung](#AssemblyResolution)
5. [Problembehandlung bei Entity Framework-Migrationen](#EFMigrations)

<a name="HttpDebugging"></a>
## HTTP-Debugging

Beim Entwickeln von Apps mit Mobile Services nutzt man normalerweise das Mobile Services-Client-SDK für die verwendete Plattform (Windows Store, iOS, Android usw.). Manchmal ist es allerdings hilfreich, sich auf HTTP-Ebene zu begeben und den Ablauf der reinen Aufrufe im Netzwerk zu beobachten. Dieser Ansatz ist besonders nützlich, wenn das Debuggen von Konnektivitäts- und Serialisierungsproblemen ansteht. Mit dem Mobile Services .NET-Back-End können Sie diesen Ansatz in Kombination mit Lokal- und Remotedebuggen mit Visual Studio verwenden (mehr dazu im nächsten Abschnitt) und so eine vollständige Vorstellung des Pfades einer HTTP-Anforderung bekommen, ehe diese Ihren Dienstcode aufruft.  

Sie können einen belieben HTTP-Debugger verwenden und HTTP-Traffic senden und inspizieren. [Fiddler](http://www.telerik.com/fiddler) ist ein beliebtes Tool, das Entwickler häufig zu diesem Zweck einsetzen. Um das Leben von Entwicklern einfacher zu machen, bündelt Mobile Services einen webbasierten HTTP-Debugger (auch als "Testclient" bezeichnet) direkt mit Ihrem mobilen Dienst, was ein externes Tool unnötig macht. Wenn Sie Ihren mobilen Dienst lokal hosten, steht er unter einer URI im Stil von [http://localhost:59233](http://localhost:59233) bereit. Beim Hosting in der Cloud hat die URI die Form von [http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net). Die folgenden Schritte funktionieren gleich, unabhängig davon, wo der Dienst gehostet wird:

1. 	Beginnen Sie mit einem Mobile Services-Serverprojekt in **Visual Studio 2013 Update 2** oder höher. Falls Sie keines zur Hand haben, können Sie ein neues erstellen, indem Sie **Datei**, **Neu**, **Projekt**und dann den **Cloud**-Knoten auswählen und anschließend die **Windows Azure Mobile Services**-Vorlage.
2. Drücken Sie **F5**, wodurch das Projekt erstellt und ausgeführt wird. Wählen Sie auf der Startseite **Ausprobieren**. 

    >[WACOM.NOTE] 
    > Wenn der Dienst lokal gehostet wird, werden Sie beim Klicken auf den Link direkt auf die nächste Seite weitergeleitet. Beim Hosting in der Cloud hingegen werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Damit soll sichergestellt werden, dass nicht authentifizierte Benutzer keinen Zugriff auf die Daten über ihre API und Nutzlasten haben. Um die Seite sehen zu können, müssen Sie sich mit einem **leeren Benutzernamen** und Ihrem **Anwendungsschlüssel** als Kennwort anmelden. Ihren Anwendungsschlüssel für Ihren mobilen Dienst finden Sie im **Azure-Verwaltungsportal** auf der Registerkarte **Dashboard**, wenn Sie **Schlüssel verwalten** auswählen.
    >
    > ![Authentication prompt to access help page][HelpPageAuth]

3. 	Die Seite, die Sie sehen (als "Hilfeseite" bezeichnet) zeigt eine Liste aller HTTP-APIs, die Ihr mobiler Dienst verfügbar macht. Wählen Sie eine der APIs aus (wenn Sie mit der Mobile Services-Projektvorlage in Visual Studio begonnen haben, sollten Sie **GET tables/TodoItem** sehen).

    ![Help page][HelpPage]

4. Die  	resultierende Seite zeigt eine etwaige Dokumentation und JSON-Beispiele der Anfrage und die von der API erwartete Antwort. Wählen Sie die Schaltfläche **Ausprobieren**.

    ![Test page for an API][HelpPageApi]

5. Das ist der "Testclient", mit dem Sie eine HTTP-Anforderung zum Testen Ihrer API senden können. Wählen Sie **Senden**.

    ![Test client][TestClient]

6. Sie sehen direkt im Browserfenster die HTTP-Antwort, die von Ihrem mobilen Dienst zurückgegeben wird. 

    ![Test client with HTTP response][TestClientResponse]

Jetzt sind Sie soweit, dass Sie die unterschiedlichen HTTP-APIs erkunden können, die je nach Webbrowser von Ihrem mobilen Dienst bereitgestellt werden.

<a name="RuntimeDebugging"></a>
## Laufzeit-Debugging

Eine der Schlüsselfunktionen des .NET-Back-Ends ist die Möglichkeit, den Dienstcode lokal zu debuggen, aber auch den live in der Cloudumgebung ausgeführten. Folgen Sie diesen Schritten:

1. 	Öffnen Sie das mobile Dienstprojekt, das Sie debuggen möchten, in **Visual Studio 2013 Update 2** oder höher.
2. Konfigurieren Sie das Laden von Symbolen. Navigieren Sie zum Menü **Debug** und wählen Sie **Optionen und Einstellungen**. Stellen Sie sicher, dass **Nur eigenen Code** nicht aktiviert und **Quellserversupport aktivieren** aktiviert ist.

    ![Configure symbol loading][SymbolLoading]

3. Wählen Sie den Knoten **Symbole** links, und fügen Sie eine Referenz zum (SymbolSource)[http://symbolsource.org]-Server mithilfe des URIs [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public) hinzu. Symbole für das Mobile Services .NET-Back-End werden dort mit jeder neuen Version verfügbar gemacht.
 
    ![Configure symbol server][SymbolServer]

4. 	Legen Sie einen Haltepunkt in der Codepassage fest, die Sie debuggen möchten. Legen Sie zum Beispiel einen Haltepunkt in der Methode **GetAllTodoItems()** des **TodoItemController** fest, der in der Mobile Services-Projektvorlage in Visual Studio enthalten ist.
5. Debuggen Sie den Dienst lokal durch Drücken von **F5**. Beim erstmaligen Laden kann das langsam sein, da Visual Studio die Symbole für das Mobile Services .NET-Back-End herunterlädt.
6. Wie im vorherigen Abschnitt zum HTTP-Debuggen beschrieben, verwenden Sie den Testclient zum Senden einer HTTP-Anforderung zu der Methode, bei der Sie den Haltepunkt gesetzt haben. Sie können beispielsweise eine Anforderung an die Methode **GetAllTodoItems()** senden, indem Sie **GET tables/TodoItem** auf der Hilfeseite aufrufen und dann **Ausprobieren** auswählen und anschließend **Senden**.
7. Visual Studio sollte an dem von Ihnen festgelegten Haltepunkt anhalten und es sollte eine volle Stapelüberwachung mit Quellcode im Fenster **Aufrufliste** in Visual Studio verfügbar sein.

    ![Hitting a breakpoint][Breakpoint]

8. Sie können den Dienst jetzt auf Azure veröffentlichen und können dann Debuggen genau so verwenden, wie wir das oben getan haben. Veröffentlichen Sie das Projekt, indem Sie mit der rechten Maustaste im **Projektmappen-Explorer** darauf klicken und **Veröffentlichen** auswählen.
9. 	Wählen Sie auf der Registerkarte **Einstellungen** des Veröffentlichungsassistenten die Konfiguration **Debug**. Damit wird sichergestellt, dass die relevanten Debuggingsymbole mit Ihrem Code veröffentlicht werden.

    ![Publish debug][PublishDebug]

10. 	Nachdem der Dienst erfolgreich veröffentlicht wurde, öffnen Sie **Server-Explorer** und erweitern die Knoten **Windows Azure** und **Mobile Services**. Melden Sie sich wenn nötig an.
11. Klicken Sie mit der rechten Maustaste den mobilen Dienst, auf dem Sie gerade veröffentlicht haben und wählen Sie **Debugger anfügen**.

    ![Attach debugger][AttachDebugger]

12. Senden Sie so wie in Schritt 6 eine HTTP-Anforderung zum Aufrufen der Methode, für die Sie den Haltepunkt gesetzt haben. Verwenden Sie diesmal die Hilfeseite und den Testclient für die in Azure gehosteten mobilen Dienst.
13. Visual Studio unterbricht am Haltepunkt.

Sie haben jetzt Zugriff auf die ganze Leistung des Visual Studio-Debuggers, sowohl bei der lokalen Entwicklung als auch live mit dem mobilen Dienst in Azure.

<a name="Logs"></a>
## Analysieren der Diagnoseprotokolle

Da der mobile Dienst Anfragen Ihrer Kunden bearbeitet, generiert er eine Reihe nützlicher diagnostischer Informationen und erfasst sämtliche auftretenden Ausnahmen. Außerdem können Sie den Controllercode mit weiteren Protokollen instrumentieren, indem Sie die Eigenschaft [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) nutzen, die in der Eigenschaft [**Dienste**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) jedes [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) verfügbar ist.

Beim lokalen Debugging werden die Protokolle im Visual Studio-Fenster **Ausgabe**.

![Logs in Visual Studio Output window][LogsOutputWindow]

Nach dem Veröffentlichen Ihres Diensts auf Azure sind die Protokolle für die in der Cloud laufenden Dienstinstanz verfügbar, indem Sie mit der rechten Maustaste den mobilen Dienst im **Server-Explorer** von Visual Studio anklicken und dann **Protokolle anzeigen** auswählen.

![Logs in Visual Studio Server Explorer][LogsServerExplorer]

	Dieselben Protokolle sind für Ihren mobilen Dienst auch im **Azure-Verwaltungsportal** auf der Registerkarte **Protokolle** verfügbar.

![Logs in Azure Management Portal][LogsPortal]

<a name="AssemblyResolution"></a>
## Debuggen der Cloud Assembly-Auflösung

	Wenn Sie Ihren mobilen Dienst auf Azure veröffentlichen, wird er von der Mobile Services-Hostingumgebung geladen. Damit werden reibungslose Upgrades und Patches der HTTP-Pipeline sichergestellt, auf denen Ihr Controllercode gehostet wird. Dazu gehören alle Assemblys, auf die durch die [.NET-Back-End NuGet-Pakete](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22) verwiesen wird: Das Team aktualisiert den Dienst ständig, sodass die neuesten Versionen dieser Assemblys verwendet werden. 

	Es ist manchmal möglich, dass es durch das Verweisen auf *unterschiedliche Hauptversionen* benötigter Assemblys zu Versionskonflikten kommt (unterschiedliche *Nebenversionen* sind zulässig). Das passiert häufig dann, wenn NuGet Sie zum Upgrade auf die neueste Version eines der Pakete auffordert, die vom Mobile Services .NET-Back-End verwendet werden. 

>[WACOM.NOTE] Mobile Services ist derzeit nur mit ASP.NET 5.1 kompatibel. ASP.NET 5.2 wird derzeit nicht unterstützt. Eine Aktualisierung Ihrer ASP.NET NuGet-Pakete auf 5.2.* kann nach der Bereitstellung zu einem Fehler führen.

Wenn Sie ein solches Paket aktualisieren und dann den aktualisierten Dienst auf Azure veröffentlichen, sehen Sie eine Warnseite, die auf den Konflikt hinweist:

![Help page indicating assembly loading conflict][HelpConflict]

Damit geht eine Ausnahmemeldung einher, die in Ihren Dienstprotokollen aufgezeichnet wird. Sie ist mit der Folgenden vergleichbar:

    Konflikte zwischen verschiedenen Versionen der gleichen abhängigen Assembly 'Microsoft.ServiceBus' gefunden: 2.2.0.0, 2.3.0.0. Ändern Sie das Projekt so, dass Version "2.2.0.0" verwendet wird, die derzeit von der Hostumgebung unterstützt wird.

Dieses Problem ist einfach zu beheben: Kehren Sie einfach zu einer Version zurück, die von der benötigten Assembly unterstützt wird, und veröffentlichen Sie den Dienst neu.

<a name="EFMigrations"></a>
## Problembehandlung bei Entity Framework-Migrationen

Wenn Sie das Mobile Services .NET-Back-End mit einer SQL-Datenbank verwenden, wird ein Entity Framework (EF) als Datenzugriffstechnologie verwendet, mit der Sie die Datenbank abfragen und Objekte dort speichern können. Ein wichtiger Aspekt, den EF für den Entwickler übernimmt, ist die Art und Weise, wie die Datenbankspalten (auch als *Schema* bekannt) mit Änderung der im Code festgelegten Modellklassen verändert werden. Dieser Prozess ist unter der Bezeichnung [Code First Migrations](http://msdn.microsoft.com/de-de/data/jj591621) bekannt.

Migrationen können komplex sein und erfordern, dass der Zustand der Datenbank synchron mit dem EF-Modell gehalten wird, damit sie erfolgreich sind. Eine Anleitung für das Durchführen von Migrationen mit Ihrem mobilen Dienst und die Fehler, die dabei auftreten können, finden Sie unter [Ändern des Datenmodells eines mobilen .NET-Back-End-Diensts](/de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/).

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png

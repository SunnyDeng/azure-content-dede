<properties linkid="mobile-services-dotnet-backend-how-to-troubleshoot" urlDisplayName="Troubleshoot the Mobile Services .NET Backend" pageTitle="Troubleshoot the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn how to diagnose and fix issues with your mobile services using the .NET backend" metaCanonical="" services="" documentationCenter="Mobile" title="Troubleshoot the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>
# Problembehandlung beim Mobile Services .NET-Back-End

Entwickeln mit Mobile Services ist normalerweise kurz und schmerzlos, doch selbst dann kann etwas schieflaufen. Dieses Lernprogramm behandelt einige Techniken, mit denen Sie häufige Probleme behandeln können, die mit dem Mobile Services .NET-Back-End auftreten können.

1. [HTTP-Debugging][HTTP-Debugging]
2. [Laufzeit-Debugging][Laufzeit-Debugging]
3. [Analysieren der Diagnoseprotokolle][Analysieren der Diagnoseprotokolle]
4. [Debuggen der Cloud Assembly-Auflösung][Debuggen der Cloud Assembly-Auflösung]
5. [Problembehandlung bei Entity Framework-Migrationen][Problembehandlung bei Entity Framework-Migrationen]

<a name="HttpDebugging"></a>
## HTTP-Debugging

Beim Entwickeln von Apps mit Mobile Services nutzt man normalerweise das Mobile Services-Client-SDK für die verwendete Plattform (Windows Store, iOS, Android usw.). Manchmal ist es allerdings hilfreich, sich auf HTTP-Ebene zu begeben und den Ablauf der reinen Aufrufe im Netzwerk zu beobachten. Dieser Ansatz ist besonders nützlich, wenn das Debuggen von Konnektivitäts- und Serialisierungsproblemen ansteht. Mit dem Mobile Services .NET-Back-End können Sie diesen Ansatz in Kombination mit Lokal- und Remotedebuggen mit Visual Studio verwenden (mehr dazu im nächsten Abschnitt) und so eine vollständige Vorstellung des Pfades einer HTTP-Anforderung bekommen, ehe diese Ihren Dienstcode aufruft.

Sie können einen belieben HTTP-Debugger verwenden und HTTP-Traffic senden und inspizieren. [Fiddler][Fiddler] ist ein beliebtes Tool, das Entwickler häufig zu diesem Zweck einsetzen. Um das Leben von Entwicklern einfacher zu machen, bündelt Mobile Services einen webbasierten HTTP-Debugger (auch als „Testclient“ bezeichnet) direkt mit Ihrem mobilen Dienst, was ein externes Tool unnötig macht. Wenn Sie Ihren mobilen Dienst lokal hosten, steht er unter einer URI im Stil von [][]<http://localhost:59233></a> bereit. Beim Hosting in der Cloud hat die URI die Form von [][1]<http://todo-list.azure-mobile.net></a>. Die folgenden Schritte funktionieren gleich, unabhängig davon, wo der Dienst gehostet wird:

1. Beginnen Sie mit einem Mobile Services-Serverprojekt in **Visual Studio 2013 Update 2** oder neuer. Falls Sie keines zur Hand haben, können Sie ein neues erstellen, indem Sie **Datei**, **Neu**, **Projekt**und dann den **Cloud**-Knoten auswählen und anschließend die **Windows Azure Mobile Services**-Vorlage.
2. Drücken Sie **F5**, wodurch das Projekt erstellt und ausgeführt wird. Wählen Sie auf der Startseite **Ausprobieren**.

    > [WACOM.NOTE]
    > Wenn der Service lokal gehostet wird, werden Sie beim Klicken auf den Link direkt auf die nächste Seite weitergeleitet. Beim Hosting in der Cloud hingegen werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Damit soll sichergestellt werden, dass nicht authentifizierte Benutzer keinen Zugriff auf die Daten über ihre API und Nutzlasten haben. Um die Seite sehen zu können, müssen Sie sich mit einem **leeren Benutzernamen** und Ihrem **Anwendungsschlüssel** als Kennwort anmelden. Ihren Anwendungsschlüssel für Ihren mobilen Dienst finden Sie im **Azure-Verwaltungsportal** auf der Registerkarte **Dashboard**, wenn Sie **Schlüssel verwalten** auswählen.
    >
    > ![Authentifizierungsaufforderung für den Zugriff auf die Hilfeseite][Authentifizierungsaufforderung für den Zugriff auf die Hilfeseite]

3. Die Seite, die Sie sehen (als „Hilfeseite“ bezeichnet) zeigt eine Liste aller HTTP-APIs, die Ihr mobiler Dienst verfügbar macht. Wählen Sie eine der APIs aus (wenn Sie mit der Mobile Services-Projektvorlage in Visual Studio begonnen haben, sollten Sie **GET tables/TodoItem** sehen).

    ![Hilfeseite][Hilfeseite]

4. Der resultierende Seite zeigt eine etwaige Dokumentation und JSON-Beispiele der Anfrage und die von der API erwartete Antwort. Wählen Sie die Schaltfläche **Ausprobieren**.

    ![Testseite für eine API][Testseite für eine API]

5. Das ist der „Testclient“, mit dem Sie eine HTTP-Anforderung zum Testen Ihrer API senden können. Wählen Sie **Senden**.

    ![Testen des Clients][Testen des Clients]

6. Sie sehen direkt im Browserfenster die HTTP-Antwort, die von Ihrem mobilen Dienst zurückgegeben wird.

    ![Testen des Clients mit der HTTP-Antwort.][Testen des Clients mit der HTTP-Antwort.]

Jetzt sind Sie soweit, dass Sie die unterschiedlichen HTTP-APIs erkunden können, die je nach Webbrowser von Ihrem mobilen Dienst bereitgestellt werden.

<a name="RuntimeDebugging"></a>
## Laufzeit-Debugging

Eine der Schlüsselfunktionen des .NET-Back-Ends ist die Möglichkeit, den Dienstcode lokal zu debuggen, aber auch den live in der Cloudumgebung ausgeführten. Folgen Sie diesen Schritten:

1. Öffnen Sie das mobile Dienstprojekt, das Sie debuggen möchten, in **Visual Studio 2013 Update 2** oder neuer.
2. Konfigurieren Sie das Laden von Symbolen. Navigieren Sie zum Menü **Debug** und wählen Sie **Optionen und Einstellungen**. Stellen Sie sicher, dass **Nur eigenen Code** nicht aktiviert und **Quellserversupport aktivieren** aktiviert ist.

    ![Konfigurieren des Ladens von Symbolen][Konfigurieren des Ladens von Symbolen]

3. Wählen Sie den Knoten **Symbole** links und fügen Sie eine Referenz zum (SymbolSource)[<http://symbolsource.org>]-Server mithilfe der URI [][2]<http://srv.symbolsource.org/pdb/Public></a> hinzu. Symbole für das Mobile Services .NET-Back-End werden dort mit jeder neuen Version verfügbar gemacht.

    ![Konfigurieren des Symbolservers][Konfigurieren des Symbolservers]

4. Legen Sie einen Haltepunkt in der Codepassage fest, die Sie debuggen möchten. Legen Sie zum Beispiel einen Haltepunkt in der Methode **GetAllTodoItems()** des **TodoItemController** fest, der in der Mobile Services-Projektvorlage in Visual Studio enthalten ist.
5. Debuggen Sie den Dienst lokal durch Drücken von **F5**. Beim erstmaligen Laden kann das langsam sein, da Visual Studio die Symbole für das Mobile Services .NET-Back-End herunterlädt.
6. Wie im vorherigen Abschnitt zum HTTP-Debuggen beschrieben, verwenden Sie den Testclient zum Senden einer HTTP-Anforderung zu der Methode, bei der Sie den Haltepunkt gesetzt haben. Sie können beispielsweise eine Anforderung an die Methode **GetAllTodoItems()** senden, indem Sie **GET tables/TodoItem** auf der Hilfeseite aufrufen und dann **Ausprobieren** auswählen und anschließend **Senden**.
7. Visual Studio sollte an dem von Ihnen festgelegten Haltepunkt anhalten und es sollte eine volle Stapelüberwachung mit Quellcode im Fenster **Aufrufliste** in Visual Studio verfügbar sein.

    ![Treffen eines Haltepunkts][Treffen eines Haltepunkts]

8. Sie können den Dienst jetzt auf Azure veröffentlichen und können dann Debuggen genau so verwenden, wie wir das oben getan haben. Veröffentlichen Sie das Projekt, indem Sie mit der rechten Maustaste im **Projektmappen-Explorer** darauf klicken und **Veröffentlichen** auswählen.
9. Wählen Sie auf der Registerkarte **Einstellungen** des Veröffentlichungsassistenten die Konfiguration **Debug**. Damit wird sichergestellt, dass die relevanten Debuggingsymbole mit Ihrem Code veröffentlicht werden.

    ![Debug veröffentlichen][Debug veröffentlichen]

10. Nachdem der Dienst erfolgreich veröffentlicht wurde, öffnen Sie **Server-Explorer** und erweitern die Knoten **Windows Azure** und **Mobile Services**. Melden Sie sich wenn nötig an.
11. Klicken Sie mit der rechten Maustaste den mobilen Dienst, auf dem Sie gerade veröffentlicht haben und wählen Sie **Debugger anfügen**.

    ![Debugger anfügen][Debugger anfügen]

12. Senden Sie so wie in Schritt 6 eine HTTP-Anforderung zum Aufrufen der Methode, für die Sie den Haltepunkt gesetzt haben. Verwenden Sie diesmal die Hilfeseite und den Testclient für die in Azure gehosteten mobilen Dienst.
13. Visual Studio unterbricht am Haltepunkt.

Sie haben jetzt Zugriff auf die ganze Leistung des Visual Studio-Debuggers, sowohl bei der lokalen Entwicklung als auch live mit dem mobilen Dienst in Azure.

<a name="Logs"></a>
## Analysieren der Diagnoseprotokolle

Da der mobile Dienst Anfragen Ihrer Kunden bearbeitet, generiert er eine Reihe nützlicher diagnostischer Informationen und erfasst sämtliche auftretenden Ausnahmen. Außerdem können Sie den Controllercode mit weiteren Protokollen instrumentieren, indem Sie die Eigenschaft [**Log**][**Log**] nutzen, die in der Eigenschaft [**Dienste**][**Dienste**] jedes [**TableController**][**TableController**] verfügbar ist.

Beim lokalen Debugging werden die Protokolle im Visual Studio-Fenster **Ausgabe** angezeigt.

![Protokolle im Visual Studio-Ausgabefenster][Protokolle im Visual Studio-Ausgabefenster]

Nach dem Veröffentlichen Ihres Diensts auf Azure sind die Protokolle für die in der Cloud laufenden Dienstinstanz verfügbar, indem Sie mit der rechten Maustaste den mobilen Dienst im **Server-Explorer** von Visual Studio anklicken und dann **Protokolle anzeigen** auswählen.

![Protokolle im Visual Studio Server-Explorer][Protokolle im Visual Studio Server-Explorer]

Dieselben Protokolle sind für Ihren mobilen Dienst auch im **Azure-Verwaltungsportal** auf der Registerkarte **Protokolle** verfügbar.

![Protokolle im Azure-Verwaltungsportal][Protokolle im Azure-Verwaltungsportal]

<a name="AssemblyResolution"></a>
## Debuggen der Cloud Assembly-Auflösung

Wenn Sie Ihren mobilen Dienst auf Azure veröffentlichen, wird er von der Mobile Services-Hostingumgebung geladen. Damit werden reibungslose Upgrades und Patches der HTTP-Pipeline sichergestellt, auf denen Ihr Controllercode gehostet wird. Dazu gehören alle Assemblys, auf die durch die [.NET-Back-End NuGet-Pakete][.NET-Back-End NuGet-Pakete] verwiesen wird: Das Team aktualisiert den Dienst ständig, sodass die neuesten Versionen dieser Assemblys verwendet werden.

Es ist manchmal möglich, dass es durch das Verweisen auf *unterschiedliche Hauptversionen* benötigter Assemblys zu Versionskonflikten kommt (unterschiedliche *Nebenversionen* sind zulässig). Das passiert häufig dann, wenn NuGet Sie zum Upgrade auf die neueste Version eines der Pakete auffordert, die vom Mobile Services .NET-Back-End verwendet werden.

    > [WACOM.NOTE] Mobile Services is currently compatible only with ASP.NET 5.1; ASP.NET 5.2 is not presently supported. Upgrading your ASP.NET NuGet packages to 5.2.* may result in an error after deployment.

Wenn Sie ein solches Paket aktualisieren und dann den aktualisierten Dienst auf Azure veröffentlichen, sehen Sie eine Warnseite, die auf den Konflikt hinweist:

![Hilfeseite, die einen Konflikt beim Laden eines Assemblys hinweist][Hilfeseite, die einen Konflikt beim Laden eines Assemblys hinweist]

Damit geht eine Ausnahmemeldung einher, die in Ihren Dienstprotokollen aufgezeichnet wird. Sie ist mit der Folgenden vergleichbar:

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Dieses Problem ist einfach zu beheben: Kehren Sie einfach zu einer Version zurück, die vom benötigten Assembly unterstützt wird, und veröffentlichen Sie den Dienst neu.

<a name="EFMigrations"></a>
## Problembehandlung bei Entity Framework-Migrationen

Wenn Sie das Mobile Services .NET-Back-End mit einer SQL-Datenbank verwenden, wird ein Entity Framework (EF) als Datenzugriffstechnologie verwendet, mit der Sie die Datenbank abfragen und Objekte dort speichern können. Ein wichtiger Aspekt, den EF für den Entwickler übernimmt, ist die Art und Weise, wie die Datenbankspalten (auch als *Schema* bekannt) mit Änderung der im Code festgelegten Modellklassen verändert werden. Dieser Prozess ist unter der Bezeichnung [Code First Migrations][Code First Migrations] bekannt.

Migrationen können komplex sein und erfordern, dass der Zustand der Datenbank synchron mit dem EF-Modell gehalten wird, damit sie erfolgreich sind. Eine Anleitung für das Durchführen von Migrationen mit Ihrem mobilen Dienst und die Fehler, die dabei auftreten können, finden Sie unter [Ändern des Datenmodells eines mobilen .NET-Backend-Diensts][Ändern des Datenmodells eines mobilen .NET-Backend-Diensts].

<!-- IMAGES -->

[HTTP-Debugging]: #HttpDebugging
[Laufzeit-Debugging]: #RuntimeDebugging
[Analysieren der Diagnoseprotokolle]: #Logs
[Debuggen der Cloud Assembly-Auflösung]: #AssemblyResolution
[Problembehandlung bei Entity Framework-Migrationen]: #EFMigrations
[Fiddler]: http://www.telerik.com/fiddler
[]: http://localhost:59233
[1]: http://todo-list.azure-mobile.net
[Authentifizierungsaufforderung für den Zugriff auf die Hilfeseite]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[Hilfeseite]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[Testseite für eine API]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[Testen des Clients]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[Testen des Clients mit der HTTP-Antwort.]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[Konfigurieren des Ladens von Symbolen]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[2]: http://srv.symbolsource.org/pdb/Public
[Konfigurieren des Symbolservers]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Treffen eines Haltepunkts]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[Debug veröffentlichen]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[Debugger anfügen]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[**Log**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx
[**Dienste**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx
[**TableController**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx
[Protokolle im Visual Studio-Ausgabefenster]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[Protokolle im Visual Studio Server-Explorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[Protokolle im Azure-Verwaltungsportal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[.NET-Back-End NuGet-Pakete]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
[Hilfeseite, die einen Konflikt beim Laden eines Assemblys hinweist]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png
[Code First Migrations]: http://msdn.microsoft.com/de-de/data/jj591621
[Ändern des Datenmodells eines mobilen .NET-Backend-Diensts]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/

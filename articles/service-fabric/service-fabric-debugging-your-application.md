<properties
   pageTitle="Debuggen einer Service Fabric-Anwendung in Visual Studio mit F5"
   description="Verbessern Sie die Zuverlässigkeit und Leistung Ihrer Dienste mithilfe von Visual Studio und einem lokalen Entwicklungscluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2015"
   ms.author="jesseb"/>

# Debuggen einer Service Fabric-Anwendung in Visual Studio mit F5

Sie können Zeit und Geld sparen, indem Sie Ihre Service Fabric-Anwendung in einem Cluster für die Entwicklung auf einem lokalen Computer bereitstellen und debuggen. Visual Studio kann die Anwendung im lokalen Cluster bereitstellen und den Debugger automatisch mit allen Instanzen der Anwendung verbinden.

1. Um einen lokalen Cluster für die Entwicklung zu erstellen, folgen Sie den Schritten unter [Einrichten der Service Fabric-Entwicklungsumgebung](service-fabric-get-started.md).

2. Drücken Sie **F5** oder klicken Sie auf **Debuggen** > **Debugging starten**

    ![Anwendung debuggen][startdebugging]

3. Legen Sie Haltepunkte im Code fest und durchlaufen Sie die Anwendung mit den Befehlen im Menü **Debuggen**.

    > [AZURE.NOTE]Visual Studio wird an alle Instanzen der Anwendung angefügt. Beim Durchlaufen des Codes können Haltepunkte von mehreren Prozessen gleichzeitig erreicht werden, sodass es zu gleichzeitigen Sitzungen kommt. Versuchen Sie, die Haltepunkte nach dem Erreichen zu deaktivieren, indem Sie den Haltepunkt von der Thread-ID abhängig machen, oder verwenden Sie Diagnoseereignisse.

4. Das Fenster **Diagnoseereignisse** wird automatisch geöffnet, um die Diagnoseereignisse in Echtzeit anzuzeigen.

    ![Diagnoseereignisse in Echtzeit anzeigen][diagnosticevents]

5. Sie können das Fenster **Diagnoseereignisse** auch im Server-Explorer öffnen. Klicken Sie in **Azure** mit der rechten Maustaste auf **Service Fabric-Cluster** > **Diagnoseereignisse anzeigen...**.

    ![Fenster mit Diagnoseereignissen öffnen][viewdiagnosticevents]

6. Sie können die Diagnoseereignisse in der automatisch generierten Datei **ServiceEventSource.cs** anzeigen und im Anwendungscode aufrufen.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. Im Fenster **Diagnoseereignisse** können Ereignisse in Echtzeit gefiltert, angehalten und geprüft werden. Der Filter ist eine einfache Zeichenfolgensuche in der Ereignismeldung und ihrem Inhalt.

    ![Ereignisse in Echtzeit filtern, anhalten und fortsetzen oder prüfen][diagnosticeventsactions]

8. Das Debuggen von Diensten ist vergleichbar mit dem Debuggen von Anwendungen. Um das Debuggen zu vereinfachen, können Haltepunkte auf übliche Weise in Visual Studio festgelegt werden. Obwohl zuverlässige Auflistungen in mehreren Knoten repliziert werden, wird dennoch IEnumerable implementiert. Das heißt, Sie können beim Debuggen die Ergebnisansicht in Visual Studio verwenden, um den darin gespeicherten Inhalt anzuzeigen. Legen Sie einfach einen Haltepunkt an einer beliebigen Stelle im Code fest.

    ![Anwendung debuggen][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

- [Testen eines Service Fabric-Diensts](service-fabric-test-your-service-index.md)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
 

<!---HONumber=July15_HO2-->
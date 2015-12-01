<properties
   pageTitle="Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio | Microsoft Azure"
   description="Erstellen, Bereitstellen und Debuggen einer Service Fabric-Anwendung in Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="seanmck"/>

# Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio

Das Service Fabric-SDK enthält ein Add-In für Visual Studio, das Vorlagen und Tools zum Erstellen, Bereitstellen und Debuggen von Service Fabric-Anwendungen bietet. In diesem Thema werden Sie durch den Prozess der Erstellung Ihrer ersten Anwendung in Visual Studio geführt.

## Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass [Ihre Entwicklungsumgebung eingerichtet ist](service-fabric-get-started.md).

## Erstellen der Anwendung

Eine Service Fabric-Anwendung kann einen oder mehrere Dienste enthalten, die jeweils eine bestimmte Rolle bei der Bereitstellung von Funktionen der Anwendung haben. Mit dem Assistenten für neue Projekte können Sie ein Anwendungsprojekt zusammen mit Ihrem ersten Dienstprojekt erstellen. Sie können später weitere Dienste hinzufügen.

1. Starten Sie Visual Studio als Administrator.

2. Klicken Sie auf **Datei > Neues Projekt > Cloud > Service Fabric-Anwendung**.

3. Geben Sie der Anwendung einen Namen, und klicken Sie auf „OK“.

	![Dialogfeld „Neues Projekt“ in Visual Studio][1]

4. Im nächsten Dialogfeld werden Sie aufgefordert, den ersten Diensttyp auszuwählen, den Sie in Ihre Anwendung einbinden möchten. Für dieses Tutorial wählen wir als Diensttyp „Zustandsbehaftet“ aus. Geben Sie ihm einen Namen, und klicken Sie auf „OK“.

	![Dialogfeld „Neuer Dienst“ in Visual Studio][2]

	>[AZURE.NOTE]Weitere Informationen zu den Optionen finden Sie unter [Auswählen eines Frameworks](service-fabric-choose-framework.md).

	Visual Studio erstellt das Anwendungsprojekt und das Projekt für den zustandsbehafteten Dienst und zeigt sie im Projektmappen-Explorer an.

	![Projektmappen-Explorer nach der Erstellung der Anwendung mit einem zustandsbehafteten Dienst][3]

	Das Anwendungsprojekt enthält Code nicht direkt. Stattdessen verweist es auf eine Reihe von Dienstprojekten. Darüber hinaus enthält es drei Arten von Inhalt:

	- **Veröffentlichungsprofile**: Werden zum Verwalten von Tooleinstellungen für unterschiedliche Umgebungen verwendet.

	- **Skripts**: Ein PowerShell-Skript für Bereitstellung/Upgrade der Anwendung. Dieses Skript wird von Visual Studio im Hintergrund verwendet und kann direkt an der Befehlszeile aufgerufen werden.

	- **Anwendungsdefinition**: Das Anwendungsmanifest und zugehörige Anwendungsparameterdateien definieren die Anwendung und ermöglichen es Ihnen, sie speziell für eine bestimmte Umgebung zu konfigurieren.

    Einen Überblick über den Inhalt des Dienstprojekts finden Sie unter [Erste Schritte mit Reliable Services](service-fabric-reliable-services-quick-start.md).

## Bereitstellen und Debuggen der Anwendung

Jetzt ist eine Anwendung vorhanden, die Sie ausführen können.

1. Drücken Sie in Visual Studio F5, um die Anwendung für das Debuggen bereitzustellen.

	>[AZURE.NOTE]Dies dauert beim ersten Mal eine Weile, da Visual Studio einen lokalen Cluster für die Entwicklung erstellt. Ein lokaler Cluster führt den gleichen Plattformcode aus, den Sie in einem Cluster mit mehreren Computern erstellen, aber auf einem einzelnen Computer. Der Status der Clustererstellung wird im Ausgabefenster von Visual Studio angezeigt.

	Wenn der Cluster bereit ist, erhalten Sie eine Benachrichtigung von der Taskleistenanwendung Local Cluster Manager, die im SDK enthalten ist.

	![Benachrichtigung in der Taskleiste zum lokalen Cluster][4]

2. Sobald die Anwendung gestartet wird, startet Visual Studio automatisch die Diagnoseereignisanzeige, in der die Ausgabe der Ablaufverfolgung des Diensts angezeigt wird.

	![Diagnoseereignisanzeige][5]

	Bei der Vorlage für einen zustandsbehafteten Dienst zeigen die Meldungen einfach den Zählerwert an, der in der `RunAsync`-Methode von „MyStatefulService.cs“ erhöht wird.

3. Erweitern Sie eines der Ereignisse, um weitere Details anzuzeigen, einschließlich des Knotens, auf dem der Code ausgeführt wird. In diesem Fall Knoten 2, obwohl es auf Ihrem Computer ein anderer Knoten sein kann.

	![Detail der Diagnoseereignisanzeige][6]

	Der lokale Cluster besteht aus fünf Knoten, die auf einem einzelnen Computer gehostet werden. Damit wird ein Cluster mit fünf Knoten imitiert, bei dem sich Knoten auf unterschiedlichen Computern befinden. Deaktivieren Sie einen der Knoten im lokalen Cluster, um den Verlust eines Computers zu simulieren, und führen Sie gleichzeitig den Visual Studio-Debugger aus.

    >[AZURE.NOTE]Von der Projektvorlage ausgegebene Anwendungsdiagnoseereignisse verwenden die `ServiceEventSource`-Klasse. Weitere Informationen finden Sie unter [Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally).

4. Suchen Sie in Ihrem Dienstprojekt die Klasse, die von „StatefulService“ abgeleitet ist (z. B. „MyStatefulService“), und legen Sie in der ersten Zeile der `RunAsync`-Methode einen Haltepunkt fest.

	![Haltepunkt in der RunAsync-Methode des zustandsbehafteten Diensts][7]

5. Klicken Sie mit der rechten Maustaste auf die Taskleisten-App Local Cluster Manager, und wählen Sie die Option zum Verwalten des lokalen Clusters aus, um den Service Fabric-Explorer zu starten.

    ![Starten des Service Fabric-Explorers über den Manager für den lokalen Cluster][systray-launch-sfx]

    Der Service Fabric-Explorer bietet eine visuelle Darstellung eines Clusters, einschließlich der darin bereitgestellten Anwendungen und der physischen Knoten, aus denen er besteht. Weitere Informationen zu Service Fabric Explorer finden Sie unter [Visualisieren des Clusters](service-fabric-visualizing-your-cluster).

6. Erweitern Sie im linken Bereich **Cluster > Knoten**, und suchen Sie den Knoten, auf dem der Code ausgeführt wird.

7. Klicken Sie auf **Aktionen > Deaktivieren (Neustarten)**, um einen Neustart des Computers zu simulieren.

	![Beenden eines Knotens im Service Fabric-Explorer][sfx-stop-node]

	Sofort sollten Sie den Haltepunkt in Visual Studio sehen, während die Berechnung, die Sie auf einem Knoten durchgeführt haben, nahtlos auf einem anderen fortgeführt wird.

8. Kehren Sie zur Diagnoseereignisanzeige zurück, und beobachten Sie die Meldungen. Beachten Sie, dass der Zähler weiter erhöht wurde, obwohl die Ereignisse tatsächlich von einem anderen Knoten stammen.

    ![Diagnoseereignisanzeige nach einem Failover][diagnostic-events-viewer-detail-post-failover]

### Bereinigen

  Bevor Sie Ihre Arbeit abschließen, sollten Sie bedenken, dass der lokale Cluster sehr real ist. Auch nach dem Beenden des Debuggers und dem Schließen von Visual Studio werden Ihre Anwendungen weiterhin im Hintergrund ausgeführt. Je nach Art Ihrer Apps kann diese Hintergrundaktivität erhebliche Ressourcen auf dem Computer beanspruchen. Sie haben mehrere Möglichkeiten, hier steuernd einzugreifen:

  1. Zum Entfernen einer einzelnen Anwendung und all ihrer Daten verwenden Sie in Service Fabric Explorer die Aktion **Anwendung entfernen**.

  2. Zum Beenden des Clusters bei Beibehaltung der Anwendungsdaten und Ablaufverfolgungen klicken Sie in der Infobereichs-App auf **Cluster beenden**.

  3. Zum vollständigen Entfernen des Clusters klicken Sie in der Infobereichs-App auf **Cluster entfernen**. Beachten Sie: Diese Option bewirkt eine weitere langsame Bereitstellung, wenn Sie das nächste Mal in Visual Studio F5 drücken. Verwenden Sie sie darum nur, wenn Sie den lokalen Cluster für einige Zeit nicht benötigen oder unbedingt Ressourcen wiedergewinnen müssen.



## Nächste Schritte

- [Finden Sie heraus, wie Sie Ihre Dienste über WebAPI im Internet verfügbar machen können](service-fabric-add-a-web-frontend.md)
- [Erfahren Sie, wie Sie einen Cluster in Azure erstellen](service-fabric-cluster-creation-via-portal.md)
- [Erfahren Sie mehr über das Erstellen von Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Erstellen Sie einen Dienst mit dem Reliable Actor-Programmiermodell](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

<!---HONumber=AcomDC_1125_2015-->
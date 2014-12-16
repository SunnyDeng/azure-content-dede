<properties pageTitle="Erste Schritte mit Ereignis-Hubs" metaKeywords="Azure Service Bus, Ereignis-Hub, Erste Schritte mit Ereignis-Hubs" description="Follow this tutorial to get started using Azure Event Hubs sending events with Java and receiving them in an Apache Storm cluster" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="java" ms.devlang="java" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Erste Schritte mit Ereignis-Hubs

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Ereignis-Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Ereignis-Hubs können Sie die Daten mithilfe beliebiger Echtzeitanalyseanbieter oder Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Ereignis-Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in Java aufgenommen werden können und wie Sie diese parallel mit Apache Storm abrufen können.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Eine Java-Entwicklungsumgebung, die zum Ausführen von [Maven](http://maven.apache.org/) konfiguriert ist. In diesem Lernprogramm wird von [Eclipse](https://www.eclipse.org/) ausgegangen.

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

## Erstellen eines Ereignis-Hubs

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Ereignis-Hub** und dann auf **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für den Ereignis-Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Create a new Event Hub**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ***Name des Ereignis-Hubs*-ns**).

   	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Ereignis-Hubs**, und klicken Sie dann auf den soeben erstellten Ereignis-Hub.

   	![][4]

6. Klicken Sie am oberen Seitenrand auf die Registerkarte **Konfigurieren**, und fügen Sie eine Regel namens **SendRule** mit *Send*-Berechtigung hinzu. Fügen Sie eine weitere Regel namens **ReceiveRule** mit der Berechtigung *Listen* hinzu, und klicken Sie dann auf **Speichern**.

   	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **SendRule** und **ReceiveRule**.

   	![][6c]

Ihr Ereignis-Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-java](../includes/service-bus-event-hubs-get-started-send-java.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ausführen der Anwendungen

Jetzt können Sie die Anwendungen ausführen.

1.	Führen Sie die Klasse **LogTopology** aus Eclipse aus, und warten Sie dann, bis sie die Empfänger für alle Partitionen gestartet hat.

2.	Führen Sie das Programm **Sender** aus, drücken Sie im Konsolenfenster die **Eingabetaste**, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

   	![][22]

> [AZURE.NOTE] Verwenden Sie Storm in diesem Lernprogramm zu Entwicklungszwecken ausschließlich im lokalen Modus. Weitere Informationen zu Storm-Bereitstellungen und -Mustern finden Sie in der [Übersicht zu HDInsight Storm] und in der offiziellen Dokumentation zu [Apache Storm].

## Nächste Schritte
Die folgenden Ressourcen sind für die Entwicklung von Anwendungen verfügbar, die Ereignis-Hubs und Storm integrieren.

- [Analysieren von Sensordaten mit Storm und HDInsight] ist ein vollständiges Szenariolernprogramm, das Ereignis-Hubs, Storm und HBase zum Aufnehmen von Sensordaten in einen Hadoop-Cluster verwendet.
- [Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm und HDInsight] ist ein Lernprogramm zum Schreiben von Storm-Pipelines mithilfe von C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-getstarted/receive-storm2.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/de-de/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Übersicht über HDInsight Storm]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-overview/
[Analysieren von Sensordaten mit Storm und HDInsight]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm und HDInsight]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/

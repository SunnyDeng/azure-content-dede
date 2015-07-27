<properties
	pageTitle="Erste Schritte mit Event Hubs"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs für das Senden von Ereignissen mit C und das Empfangen in einem Apache Storm-Cluster."
	services="event-hubs,service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article" 
	ms.date="07/06/2015"
	ms.author="sethm"/>

# Erste Schritte mit Event Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Einführung

Event Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Event Hubs können Sie Daten über einen beliebigen Echtzeit-Analyseanbieter oder ein Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Event Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in C aufgenommen werden können und wie Sie diese parallel mit Apache Storm abrufen können.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Eine C-Entwicklungsumgebung. In diesem Lernprogramm wird vom GCC-Stack auf einem [virtuellen Linux-Computer mit Azure](../virtual-machines/virtual-machines-linux-tutorial.md) mit Ubuntu 14.04 ausgegangen. Anweisungen für andere Umgebungen werden in externen Links bereitgestellt.

+ Eine Java-Entwicklungsumgebung, die zum Ausführen von [Maven](http://maven.apache.org/) konfiguriert ist. In diesem Lernprogramm wird von [Eclipse](https://www.eclipse.org/) ausgegangen.

+ Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

## Erstellen eines Ereignis-Hubs

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **NEU**.

2. Klicken Sie auf **App Services** und anschließend nacheinander auf **Service Bus**, **Event Hub** und **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für Ihren Event Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Neuen Event Hub erstellen**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (üblicherweise ***Name_des_Event_Hubs*-ns**).

   	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Event Hubs**, und klicken Sie dann auf den soeben erstellten Event Hub.

   	![][4]

6. Klicken Sie am oberen Seitenrand auf die Registerkarte **Konfigurieren**, und fügen Sie eine Regel namens **SendRule** mit *Send*-Berechtigung hinzu. Fügen Sie eine weitere Regel namens **ReceiveRule** mit der Berechtigung *Listen* hinzu, und klicken Sie dann auf **Speichern**.

   	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **SendRule** und **ReceiveRule**.

   	![][6c]

Ihr Event Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie die **LogTopology**-Klasse aus Eclipse aus, und warten Sie dann, bis sie die Empfänger für alle Partitionen gestartet hat.

2.	Führen Sie das Programm **sender** aus, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

   	![][23]

> [AZURE.NOTE]Verwenden Sie nur in diesem Lernprogramm Storm im lokalen Modus zu Entwicklungszwecken. Weitere Informationen zu Storm-Bereitstellungen und -Mustern finden Sie in der [Übersicht zu HDInsight Storm] und in der offiziellen Dokumentation zu [Apache Storm].

## Nächste Schritte

Die folgenden Ressourcen sind für die Entwicklung von Anwendungen verfügbar, die Event Hubs und Storm integrieren.

- [Analysieren von Sensordaten mit Storm und HDInsight] ist ein Lernprogramm mit einem umfassenden Szenario, das Event Hubs, Storm und HBase zum Erfassen von Sensordaten in einen Hadoop-Cluster verwendet.
- [Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm und HDInsight] ist ein Lernprogramm zum Schreiben von Storm-Pipelines mithilfe von C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-c-storm-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-c-storm-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-c-storm-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-c-storm-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-c-storm-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-c-storm-getstarted/create-event-hub6c.png

[23]: ./media/service-bus-event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Event Hubs]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Übersicht zu HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md/
[Analysieren von Sensordaten mit Storm und HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm und HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 

<!---HONumber=July15_HO3-->
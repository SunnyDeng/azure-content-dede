<properties 
	pageTitle="Erste Schritte mit Ereignis-Hubs" 
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs für das Senden von Ereignissen mit C und das Empfangen in einem Apache Storm-Cluster." 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="c" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Erste Schritte mit Ereignis-Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## Einführung

Ereignis-Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Ereignis-Hubs können Sie Daten über einen beliebigen Echtzeit-Analyseanbieter oder ein Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Ereignis-Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in C aufgenommen werden können und wie Sie diese parallel mit Apache Storm abrufen können.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Eine C-Entwicklungsumgebung. In diesem Lernprogramm wird vom GCC-Stack auf einem [virtuellen Azure-Computer mit Linux](virtual-machines-linux-tutorial.md) mit Ubuntu 14.04 ausgegangen. Anweisungen für andere Umgebungen werden in externen Links bereitgestellt.

+ Eine Java-Entwicklungsumgebung, die zum Ausführen von [Maven](http://maven.apache.org/) konfiguriert ist. In diesem Lernprogramm wird von [Eclipse](https://www.eclipse.org/) ausgegangen.

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">1 Monat kostenlose Testversion</a>.

## Erstellen eines Ereignis-Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann am unteren Bildschirmrand auf **+NEU**.

2. Klicken Sie auf **App Services**, dann **Service Bus**, dann **Event Hub**, dann **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für den Ereignis-Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Create a new Event Hub**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ***Name des Ereignis-Hubs*-ns**).

   	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Ereignis-Hubs**, und klicken Sie dann auf den soeben erstellten Ereignis-Hub.

   	![][4]

6. Klicken Sie oben auf der Seite auf die Registerkarte **Konfigurieren**, fügen Sie eine Regel mit dem Namen **SendRule** mit *Send*-Rechten und eine weitere Regel mit dem Namen **ReceiveRule** mit *Listen*-Rechten hinzu, und klicken Sie dann auf **Speichern**.

   	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **SendRule** und **ReceiveRule**.

   	![][6c]

Ihr Ereignis-Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie die Klasse **LogTopology** aus Eclipse aus, und warten Sie dann, bis sie die Empfänger für alle Partitionen gestartet hat.

2.	Führen Sie das Programm **Sender** aus, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

   	![][23]

> [AZURE.NOTE] Verwenden Sie nur in diesem Lernprogramm Storm im lokalen Modus zu Entwicklungszwecken. Weitere Informationen zu Storm-Bereitstellungen und -Mustern finden Sie in der [Übersicht über HDInsight Storm] und in der offiziellen Dokumentation zu [Apache Storm].

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

[23]: ./media/service-bus-event-hubs-getstarted/receive-storm3.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Übersicht über HDInsight Storm]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/
[Analysieren von Sensordaten mit Storm und HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm und HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/

<!--HONumber=52-->
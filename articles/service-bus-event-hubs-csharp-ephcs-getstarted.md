<properties pageTitle="Erste Schritte mit Event Hubs" metaKeywords="Azure Service Bus, Event Hub, getting started Event Hubs" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Erste Schritte mit Event Hubs

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Event Hubs ist ein hoch skalierbares Erfassungssystem, das Millionen von Ereignissen pro Sekunde verarbeiten kann und so Ihrer Anwendung ermöglicht, die massiven Datenmengen zu verarbeiten und zu analysieren, die von verbundenen Geräten und Anwendungen erzeugt werden. Nach der Erfassung in Event Hubs können Sie Daten über einen beliebigen Echtzeit-Analyseanbieter oder ein Speichercluster transformieren und speichern. Weitere Informationen zu Event Hubs finden Sie im [Event Hubs-Entwicklerhandbuch]. 

Weitere Informationen finden Sie unter [Übersicht über Event Hubs].

In diesem Lernprogramm erfahren Sie, wie sie Nachrichten über eine Konsolenanwendung in C# in einem Event Hub erfassen und sie parallel über die C#-[Ereignisprozessorhost]-Bibliothek abrufen.

Zum Abschließen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio Express 2013 für Windows

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten stehen unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>zur Verfügung.

## Erstellen eines Event Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann im unteren Bereich des Bildschirms auf **NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Event Hub** und dann auf **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für den Event Hub ein, wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Neuen Event Hub erstellen**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise **"Name des Event Hub"-ns**).

   	![][3]

5. Klicken Sie oben auf die Registerkarte **Event Hubs** und dann auf den soeben erstellten Event Hub.

   	![][4]

6. Klicken Sie oben auf die Registerkarte **Konfigurieren**, fügen Sie eine Regel namens **SendRule** mit "Send"-Rechten hinzu, fügen Sie eine weitere Regel mit dem Namen **ReceiveRule** mit "Manage"-, "Send"- und "Listen"-Rechten hinzu, und klicken Sie auf **Speichern**.

   	![][5]

7. Klicken Sie im oberen Bereich der Seite auf die Registerkarte **Dashboard**, und klicken Sie dann auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][6]

Ihr Event Hub wird jetzt erstellt, und Sie erhalten die Verbindungszeichenfolgen, die Sie zum Senden und Empfangen von Ereignissen benötigen.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie in Visual Studio das Projekt **Receiver** aus, und warten Sie dann darauf, dass die Empfänger für alle Partitionen gestartet werden.

   	![][21]

2.	Führen Sie das Projekt **Sender** aus, drücken Sie die **EINGABETASTE** im Konsolenfenster, und beobachten Sie, wie die Ereignisse im Empfängerfenster angezeigt werden.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Event Hubs]: http://msdn.microsoft.com/de-de/library/azure/dn836025.aspx

<!--HONumber=35.1-->

<!--HONumber=35.1-->

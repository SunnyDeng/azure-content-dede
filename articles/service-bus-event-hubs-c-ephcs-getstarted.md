<properties pageTitle="Erste Schritte mit Ereignis-Hubs" metaKeywords="Azure Service Bus, Ereignis-Hub, Erste Schritte mit Ereignis-Hubs" description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs für das Senden von Ereignissen mit C und das Empfangen in C# mithilfe von EventProcessorHost." metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="c" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Erste Schritte mit Ereignis-Hubs

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Ereignis-Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Ereignis-Hubs können Sie die Daten mithilfe beliebiger Echtzeitanalyseanbieter oder Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Ereignis-Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in C aufgenommen werden können und wie Sie diese parallel mit der C#-[Ereignisprozessorhost]-Bibliothek abrufen.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Eine C-Entwicklungsumgebung. In diesem Lernprogramm wird vom GCC-Stack auf einem [virtuellen Linux-Computer mit Azure](http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-tutorial/) mit Ubuntu 14.04 ausgegangen. Anweisungen für andere Umgebungen werden in externen Links bereitgestellt.

+ Microsoft Visual Studio Express 2013 für Windows

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

6. Klicken Sie am oberen Seitenrand auf die Registerkarte **Konfigurieren**, und fügen Sie eine Regel namens **SendRule** mit *Send*-Berechtigung hinzu. Fügen Sie eine weitere Regel namens **ReceiveRule** mit den Berechtigungen *Manage, Send, Listen* hinzu, und klicken Sie dann auf **Speichern**.

   	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **SendRule**.

   	![][6b]

8. Klicken Sie am oberen Seitenrand auf die Registerkarte **Dashboard**, und klicken Sie dann auf Verbindungsinformationen.**Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][6]

Ihr Ereignis-Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ausführen der Anwendungen

Jetzt können Sie die Anwendungen ausführen.

1.	Führen Sie das Projekt **Receiver** aus Visual Studio aus, und warten Sie dann, bis es die Empfänger für alle Partitionen gestartet hat.

   	![][21]

2.	Führen Sie das Programm **Sender** aus, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

   	![][24]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/service-bus-event-hubs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/de-de/library/azure/dn836025.aspx

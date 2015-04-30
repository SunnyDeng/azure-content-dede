<properties 
	pageTitle="Erste Schritte mit Ereignis-Hubs" 
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs für das Senden von Ereignissen mit Java und das Empfangen in C# mithilfe von EventProcessorHost." 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="csharp" 
	ms.devlang="csharp" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Erste Schritte mit Ereignis-Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## Einführung

Ereignis-Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Ereignis-Hubs können Sie Daten über einen beliebigen Echtzeit-Analyseanbieter oder ein Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Ereignis-Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in Java aufgenommen werden können und wie Sie diese parallel mit der C#-[Ereignisprozessorhost]-Bibliothek abrufen.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Eine Java-Entwicklungsumgebung. In diesem Lernprogramm wird von [Eclipse](https://www.eclipse.org/) ausgegangen.

+ Microsoft Visual Studio Express 2013 für Windows

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">1 Monat kostenlose Testversion</a>.

## Erstellen eines Ereignis-Hubs

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann am unteren Bildschirmrand auf **+NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Ereignis-Hub** und dann auf **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für den Ereignis-Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Create a new Event Hub**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ***Name des Ereignis-Hubs*-ns**).

   	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Ereignis-Hubs**, und klicken Sie dann auf den soeben erstellten Ereignis-Hub.

   	![][4]

6. Klicken Sie oben auf der Seite auf die Registerkarte **Konfigurieren**, fügen Sie eine Regel mit dem Namen **SendRule** mit *Send*-Rechten und eine weitere Regel mit dem Namen **ReceiveRule** mit den Rechten *Manage, Send, Listen* hinzu, und klicken Sie dann auf **Speichern**.

   	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **SendRule**.

   	![][6b]

8. Klicken Sie am oberen Seitenrand auf die Registerkarte **Dashboard**, und klicken Sie dann auf Verbindungsinformationen.**Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][6]

Ihr Ereignis-Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie das Projekt **Receiver** aus Visual Studio aus, und warten Sie dann, bis es die Empfänger für alle Partitionen gestartet hat.

   	![][21]

2.	Führen Sie das Programm **Sender** aus, drücken Sie im Konsolenfenster die **Eingabetaste**, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/library/azure/dn836025.aspx

<!--HONumber=52-->
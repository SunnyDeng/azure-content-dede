<properties 
   pageTitle="Benachrichtigen von Benutzern über Daten von Sensoren oder anderen Systemen | Microsoft Azure"
   description="Beschreibt, wie Benutzer mithilfe von Event Hubs über Sensordaten informiert werden können."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="spyros;sethm" />

# Benachrichtigen von Benutzern über Daten von Sensoren oder anderen Systemen

Angenommen, Sie haben eine Anwendung, die in Echtzeit Daten überwacht oder nach einem Zeitplan Berichte generiert. Bei einem Blick auf die Website mit den entsprechenden Echtzeitdiagrammen oder Berichten sehen Sie unter Umständen etwas, worauf Sie reagieren müssen. Was, wenn Sie sich nicht darauf verlassen können, an die Prüfung der Website zu denken, sondern auf eine solche Situation aufmerksam gemacht werden müssen? Ein Beispiel: Sie haben ein Gewächshaus mit einer Wachstumslampe und müssen bei einem Ausfall der Lampe umgehend informiert werden. Zu diesem Zweck können Sie beispielsweise einen Lichtsensor im Gewächshaus installieren, sodass Sie eine E-Mail erhalten, wenn die Lampe aus ist.

![][1]

Anderes Szenario: Stellen Sie sich vor, Sie betreiben eine Tierpension und müssen über zur Neige gehende Bestände informiert werden. In diesem Fall können Sie beispielsweise dafür sorgen, dass Sie eine Textnachricht von Ihrem ERP-System erhalten, wenn Ihr Hundefuttervorrat einen kritischen Stand erreicht.

![][2]

Das Problem: Sie möchten wichtige Informationen nicht erst erhalten, wenn Sie dazu kommen, einen statischen Bericht zu prüfen, sondern sobald bestimmte Bedingungen erfüllt sind. Wenn Sie Daten von Geräten oder aus Unternehmensanwendungen wie [Dynamics AX][] über einen [Azure Event Hub][] oder [IoT Hub][] beziehen, haben Sie verschiedene Möglichkeiten. Die Daten können auf einer Website angezeigt, analysiert, gespeichert und als Auslöser für Befehle verwendet werden. Hierzu stehen Ihnen leistungsfähige Tools wie [Azure Websites][], [SQL Azure][], [HDInsight][], [Cortana Analytics Suite][], [IoT Suite][], [Logik-Apps][] oder [Azure Notification Hubs][] zur Verfügung. Manchmal möchten Sie die Daten jedoch einfach nur mit möglichst geringem Aufwand jemandem zukommen lassen. Unser neues Beispiel [AppToNotifyUsers][] zeigt, wie Sie genau das mit ganz wenig Code erreichen. Zu den verfügbaren Optionen zählen E-Mail (SMTP), SMS und Telefon.

## Anwendungsstruktur

Die Anwendung ist in C# geschrieben. In der enthaltenen Infodatei finden Sie alle Informationen, die Sie zum Ändern, Erstellen und Veröffentlichen der Anwendung benötigen. In den folgenden Abschnitten können Sie sich einen allgemeinen Überblick über die Funktionsweise der Anwendung verschaffen.

Wir gehen davon aus, dass wichtige Ereignisse per Push an einen Azure Event Hub oder IoT Hub übermittelt werden. Welcher Hub verwendet wird, spielt keine Rolle. Sie müssen lediglich darauf zugreifen können und die Verbindungszeichenfolge kennen.

Wenn Sie noch nicht über einen Event Hub oder IoT Hub verfügen, können Sie problemlos eine Testeinrichtung mit einem Arduino-Shield und einem Raspberry Pi erstellen, wie im Projekt [Connect The Dots](https://github.com/Azure/connectthedots) beschrieben. Der Lichtsensor des Arduino-Shields übermittelt die Lichtintensität über den Pi an einen [Azure Event Hub][] (**ehdevices**), und ein [Azure Stream Analytics-Auftrag](https://azure.microsoft.com/services/stream-analytics/) übermittelt Warnungen per Push an einen zweiten Event Hub (**ehalerts**), falls die Lichtintensität ein bestimmtes Niveau unterschreitet.

Beim Start von **AppToNotify** werden aus einer Konfigurationsdatei (App.config) die URL und die Anmeldeinformationen für den Event Hub abgerufen, der die Warnungen empfängt. Anschließend wird ein Prozess erzeugt, der den Event Hub kontinuierlich auf Nachrichten überwacht. Solange Sie auf die URL für den Event Hub oder IoT Hub zugreifen können und über gültige Anmeldeinformationen verfügen, liest der Event Hubs-Code kontinuierlich sämtliche eingehende Daten. Beim Start liest die Anwendung auch die URL und die Anmeldeinformationen für den zu verwendenden Messaging-Dienst (E-Mail, SMS, Telefon) sowie Name/Adresse des Absenders und eine Liste von Empfängern.

Sobald die Event Hub-Überwachung eine Nachricht erkennt, löst sie einen Prozess aus, der diese Nachricht mithilfe der in der Konfigurationsdatei angegebenen Methode sendet. Beachten Sie, dass dabei jede erkannte Nachricht gesendet wird. Wenn Sie also auf einen Event Hub verweisen, bei dem pro Sekunde zehn Nachrichten eingehen, werden auch zehn Nachrichten pro Sekunde gesendet – zehn E-Mails pro Sekunde, zehn SMS-Nachrichten pro Sekunde, zehn Anrufe pro Sekunde. Überwachen Sie daher keinen Event Hub, bei dem sämtliche Rohdaten Ihrer Sensoren oder Anwendungen eingehen, sondern einen, der nur relevante Warnungen erhält.

## Anwendbarkeit

Der Code in diesem Beispiel zeigt lediglich, wie Sie Event Hubs überwachen und externe Messaging-Dienste aufrufen, falls Sie Ihrer Anwendung diese Funktion hinzufügen möchten. Diese Lösung ist als Beispiel für Entwickler konzipiert. Unternehmensanforderungen wie Redundanz, Failover, Neustart bei Ausfall usw. werden hier nicht berücksichtigt. Umfassendere Lösungen für Produktionsumgebungen finden Sie hier:

- Verwenden von Connectors oder Pushbenachrichtigungen mit [Azure Logik-Apps](../app-service-logic/app-service-logic-connectors-list.md)
- Verwenden von [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx) gemäß Beschreibung im Blog [Broadcast push notifications to millions of mobile devices using Azure Notification Hubs](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs) 

## Nächste Schritte

Die Erstellung eines Benachrichtigungsdiensts, der E-Mails oder SMS-Nachrichten an Empfänger sendet oder sie telefonisch kontaktiert, um Daten von einem Event Hub oder IoT Hub weiterzugeben, ist nicht schwer. Besuchen Sie [AppToNotifyUsers][], um die Lösung bereitzustellen und Benutzer auf der Grundlage der von den Hubs empfangenen Daten zu benachrichtigen.

Weitere Informationen zu den Hubs finden Sie in den folgenden Artikeln:

- [Azure Event Hubs]
- [Azure IoT Hub]
- Beginnen Sie mit einem [Event Hubs-Lernprogramm].
- Eine vollständige [Beispielanwendung mit Verwendung von Ereignis-Hubs].
- Eine [Messaginglösung mit Warteschlange] unter Verwendung von Service Bus-Warteschlangen.

Besuchen Sie die folgende Seite, um die Lösung bereitzustellen und Benutzer auf der Grundlage der von den Hubs empfangenen Daten zu benachrichtigen:

- [AppToNotifyUsers][]

[Event Hubs-Lernprogramm]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT Hub]: https://azure.microsoft.com/services/iot-hub/
[IoT Hub]: https://azure.microsoft.com/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/services/event-hubs/
[Azure Event Hub]: https://azure.microsoft.com/services/event-hubs/
[Beispielanwendung mit Verwendung von Ereignis-Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[Messaginglösung mit Warteschlange]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/de-DE/dynamics/erp-ax-overview.aspx
[Azure Websites]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Analytics Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Logik-Apps]: https://azure.microsoft.com/services/app-service/logic/
[Azure Notification Hubs]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_1223_2015-->
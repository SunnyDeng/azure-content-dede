<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Gewusst wie: Überwachen von Service Bus-Nachrichtenentitäten

Dieses Thema beschreibt die Verwaltung und Überwachung Ihrer Service Bus-Entitäten im [Azure-Verwaltungsportal][Azure-Verwaltungsportal]. Im Portal finden Sie eine umfassende Statusansicht für Ihre Warteschlangen und Themen. Außerdem können Sie deren Verwendung überwachen.

## Überwachen von Service Bus-Warteschlangen

Führen Sie die folgenden Schritte aus, um eine Service Bus-Warteschlange zu überwachen:

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie in der linken Navigationsleiste auf **Service Bus**, um eine Liste der Dienstnamespaces abzurufen.
3.  Klicken Sie den Namespace an, der die gewünschte Warteschlange enthält.
4.  Klicken Sie in der Menüleiste am oberen Seitenrand auf **Queues**.
5.  Klicken Sie auf den Namen der Warteschlange, die Sie überwachen möchten. Das Dashboard der Warteschlange wird angezeigt.
6.  Dieses Dashboard enthält Aktivitäten der von Ihnen erstellten Warteschlangen. Sie können diese Informationen für mehrere Zeitfenster anzeigen. Der Standardwert ist 1 Stunde. In der Dropdownliste neben der Zeit können Sie jedoch auch andere Zeitfenster auswählen: die letzten 24 Stunden, 7 Tage oder 30 Tage. Für die Anzeige der Daten kann eine Genauigkeit von bis zu 5 Minuten im 1-Stunden-Fenster, 1 Stunde im 24-Stunden-Fenster und 1 Tag im 7- und 30-Tage-Fenster eingestellt werden.

Zu jede Warteschlange finden Sie Grafiken für folgende Informationen:

-   **Eingehende Nachrichten**: Anzahl der im Zeitintervall in die Warteschlange eingefügten Nachrichten.
-   **Ausgehende Nachrichten**: Anzahl der im Zeitintervall aus der Warteschlange entfernten Nachrichten.
-   **Länge**: Anzahl der Nachrichten in der Entität zum Ende des Zeitintervalls.
-   **Größe**: Speicherplatz (in MB), den diese Entität zum Ende des Zeitintervalls verbraucht.

### Auf einen Blick

**Auf einen Blick** im Dashboard zeigt die aktuelle Länge der Warteschlange unter **Queue Length** an. Außerdem werden weitere Eigenschaften der Warteschlange bzw. des Themas angezeigt. Die Informationen werden alle 10 Sekunden aktualisiert.

![][0]

## Überwachen der Aktivität in Service Bus-Themen

Führen Sie die folgenden Schritte aus, um ein Service Bus-Thema zu überwachen:

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie in der linken Navigationsleiste auf **Service Bus**, um eine Liste der Dienstnamespaces abzurufen.
3.  Klicken Sie den Namespace an, der das gewünschte Thema enthält.
4.  Klicken Sie in der Menüleiste am oberen Seitenrand auf **Topics**.
5.  Klicken Sie auf den Namen des Themas, das Sie überwachen möchten. Das Dashboard des Themas wird angezeigt.

Themen-Dashboards ähneln den Warteschlangen-Dashboards, mit Ausnahme der Nutzungsmetriken. Ausgehende Nachrichten und Länge werden im Themen-Dashboard nicht angezeigt, da diese Informationen für jedes Abonnement eines Themas unterschiedlich sind. In der Registerkarte **Monitor** können Sie Nutzungsmetriken (Anzahl ausgehender Nachrichten und Länge) pro Themen-Abonnement hinzufügen. Klicken Sie auf die Registerkarte **Monitor**, um diese Metriken hinzuzufügen. Klicken Sie anschließend auf **Add Metrics** am unteren Seitenrand und wählen Sie die gewünschten Abonnements des Themas aus.

![][1]

  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [0]: ./media/service-bus-monitor-message-entities/QueueDashboard.png
  [1]: ./media/service-bus-monitor-message-entities/AddMetrics.png

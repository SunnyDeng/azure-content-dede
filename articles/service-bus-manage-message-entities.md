<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Gewusst wie: Verwalten von Service Bus-Nachrichtenentitäten

Dieses Thema beschreibt die Erstellung und Verwaltung Ihrer Service Bus-Entitäten im [Azure-Verwaltungsportal][Azure-Verwaltungsportal]. Sie können das Portal verwenden, um neue Dienstnamespaces oder Nachrichtenentitäten (Warteschlangen, Themen oder Abonnements) zu erstellen. Außerdem können Sie Entitäten löschen oder deren Status ändern.

## Inhaltsverzeichnis

-   [Gewusst wie: Erstellen einer Service Bus-Entität][Gewusst wie: Erstellen einer Service Bus-Entität]
-   [Gewusst wie: Löschen einer Service Bus-Entität][Gewusst wie: Löschen einer Service Bus-Entität]
-   [Gewusst wie: Deaktivieren oder Aktivieren einer Service Bus-Entität][Gewusst wie: Deaktivieren oder Aktivieren einer Service Bus-Entität]
-   [Zusätzliche Ressourcen][Zusätzliche Ressourcen]

## <span id="create"></span></a>Gewusst wie: Erstellen einer Service Bus-Entität

Das Azure-Verwaltungsportal unterstützt zwei Methoden zum Erstellen einer Service Bus-Entität: *Quick Create* oder *Custom Create*.

### Schnellerfassung

Mit Quick Create können Sie Service Bus-Warteschlangen, Themen oder Relaydienst-Namespaces in einem einzigen einfachen Schritt erstellen. Führen Sie die folgenden Schritte aus, um eine Service Bus-Entität zu erstellen.

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links im Verwaltungsportal auf das Symbol **New**.
3.  Klicken Sie auf das Symbol **App Services** und anschließend auf **Service Bus Queue** (Thema oder Relay). Klicken Sie auf **Quick Create** und geben Sie Name, Region und Azure-Abonnement-ID für die Warteschlange ein.

    a. Falls dies Ihr erster Namespace in der ausgewählten Region ist, schlägt das Portal eine Namespace-Warteschlange vor: [Ihr Entitätsname]-ns. Sie können diesen Wert ändern.

    b. Falls Sie bereits einen Dienstnamespace in dieser Region erstellt haben, wird ein Namespace automatisch ausgewählt. Sie können diesen ausgewählten Namespace ändern.

4.  Klicken Sie auf das Häkchen neben **create a new queue** (bzw. Topic).
5.  Nach der Erstellung von Warteschlange bzw. Thema wird die Nachricht **Creation of Queue '[Queue Name]' Completed** angezeigt.

    a. Falls Sie keinen Namespace in der jeweiligen Region bzw. in Ihrem Azure-Abonnement haben, wird ein neuer Namespace automatisch für Sie erstellt. In diesem Fall erhalten Sie zwei Erfolgsmeldungen: eine für die Namespace- und eine für die Entitätserstellung.

    ![][]

Klicken Sie in der linken Navigationsleiste auf **Service Bus**, um eine Liste der Namespaces abzurufen. Dort finden Sie den Namespace, den Sie soeben erstellt haben. Klicken Sie in der Liste auf den Namespace. Sie sehen die soeben erstellte Entität unter dem entsprechenden Namespace.

**Hinweis** Unter Umständen wird der Namespace nicht sofort angezeigt. Die Erstellung des Namespace und Aktualisierung der Portaloberfläche dauert einige Sekunden.

**Hinweis** Wenn Sie ein Relay mit **Quick Create** erstellen, wird kein neuer Relay-Endpunkt erstellt. In diesem Fall wird nur ein Namespace erstellt, unter dem Sie einen Relay-Endpunkt programmgesteuert erstellen können. Weitere Informationen finden Sie in der [Service Bus-Dokumentation][Service Bus-Dokumentation].

### Benutzerdefiniert erstellen

**Custom Create** ist die ausführlichere Erstellungsoption, mit der Sie die Standardwerte der Entitätseigenschaften (Warteschlange oder Thema) bei der Erstellung ändern können. Führen Sie die folgenden Schritte aus, um ein Thema oder eine Entität mit **Custom Create** zu erstellen:

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links im Verwaltungsportal auf **New**.
3.  Klicken Sie auf das Symbol **App Services** und anschließend auf **Service Bus Queue** (Thema oder Relay). Klicken Sie dann auf Custom Create.
4.  Beben Sie im ersten Dialogfeld Name, Region und Azure-Abonnement-ID für die Warteschlange ein.

    a. Falls dies Ihr erster Dienstnamespace in der ausgewählten Region ist, schlägt das Portal eine Namespace-Warteschlange vor: [Ihr Entitätsname]-ns. Sie können diesen Wert ändern.

    b. Falls Sie bereits einen Namespace in dieser Region erstellt haben, wird ein Namespace automatisch ausgewählt. Sie können diesen ausgewählten Namespace ändern.

5.  Klicken Sie auf **Next**, um die verbleibenden Eigenschaften auszufüllen.

    ![][1]

6.  Aktivieren Sie das Kontrollkästchen, um die Warteschlange zu erstellen.

    ![][2]

Klicken Sie in der linken Navigationsleiste auf **Service Bus**, um eine Liste der Dienstnamespaces abzurufen. Dort finden Sie den Namespace, den Sie soeben erstellt haben. Klicken Sie in der Liste auf den Namespace. Sie sehen die soeben erstellte Entität unter dem entsprechenden Namespace.

## <span id="delete"></span></a>Gewusst wie: Löschen einer Service Bus-Entität

Im Portal haben Sie die Möglichkeit, Service Bus-Nachrichtenentitäten zu löschen. Dies gilt für Warteschlangen, Themen und Themenabonnements. Führen Sie die folgenden Schritte aus, um eine Warteschlange oder ein Thema zu löschen:

1.  Navigieren Sie zur Listenansicht der Dienstnamespaces und klicken Sie auf den Namespace, unter dem Sie die Entität (Warteschlange oder Thema) erstellt haben.
2.  Klicken Sie unten auf der Seite auf das **Delete**-Symbol, um den Löschvorgang zu bestätigen.

    ![][3]

**Hinweis** Die Löschung von Entitäten kann nicht rückgängig gemacht werden. Gelöschte Entitäten können nicht wiederhergestellt werden. Sie können jedoch eine andere Entität mit demselben Namen erstellen.

Führen Sie die folgenden Schritte aus, um ein Themenabonnement zu löschen:

1.  Navigieren Sie zur Listenansicht der Namespaces und klicken Sie auf den Namespace, unter dem Sie das Thema erstellt haben.
2.  Klicken Sie auf das Thema, unter dem Sie das Abonnement erstellt haben.
3.  Klicken Sie auf die Registerkarte **Subscriptions** und wählen Sie das Abonnement aus, das Sie löschen möchten.
4.  Klicken Sie unten auf der Seite auf das **Delete**-Symbol, um den Löschvorgang zu bestätigen.

## <span id="disableenable"></span></a>Gewusst wie: Deaktivieren oder Aktivieren einer Service Bus-Entität

Sie können das Portal verwenden, um den Status einer Service Bus-Entität zu ändern. Dies gilt für Warteschlangen und Themen. Führen Sie die folgenden Schritte aus, um eine Warteschlange oder ein Thema zu aktivieren oder zu deaktivieren:

1.  Navigieren Sie zur Listenansicht der Dienstnamespaces und klicken Sie auf den Namespace, unter dem Sie die Entität (Warteschlange oder Thema) erstellt haben.
2.  Klicken Sie unten auf der Seite auf **Disable** (oder **Enable**).

    ![][4]

## <span id="seealso"></span></a>Zusätzliche Ressourcen

[Azure-Servicebus][Azure-Servicebus]

[.NET Developer Center][.NET Developer Center] auf der Azure-Website

[Creating Applications that Use Service Bus Topics and Subscriptions][Creating Applications that Use Service Bus Topics and Subscriptions]

[Warteschlangen, Themen und Abonnements][Warteschlangen, Themen und Abonnements]

  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Gewusst wie: Erstellen einer Service Bus-Entität]: #create
  [Gewusst wie: Löschen einer Service Bus-Entität]: #delete
  [Gewusst wie: Deaktivieren oder Aktivieren einer Service Bus-Entität]: #disableenable
  [Zusätzliche Ressourcen]: #seealso
  []: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [Service Bus-Dokumentation]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/service-bus-relay/
  [1]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [2]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [3]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [4]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Azure-Servicebus]: http://go.microsoft.com/fwlink/?LinkId=266834
  [.NET Developer Center]: http://go.microsoft.com/fwlink/?LinkID=262187
  [Creating Applications that Use Service Bus Topics and Subscriptions]: http://go.microsoft.com/fwlink/?LinkId=264293
  [Warteschlangen, Themen und Abonnements]: http://go.microsoft.com/fwlink/?LinkId=264291

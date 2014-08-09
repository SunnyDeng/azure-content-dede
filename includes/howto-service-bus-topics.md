<h2><a name="what-are-service-bus-topics"></a>Was sind Servicebus-Themen und -Abonnements?</h2>


Servicebus-Themen und -Abonnements unterstützen ein Modell zum **Veröffentlichen/Abonnieren von Messaging-Kommunikationen**. Bei der Verwendung von Themen und Abonnements kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über ein Thema aus, das als Zwischenstufe fungiert.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Anders als bei Servicebus-Warteschlangen, wo jede Nachricht von einem einzelnen Consumer verarbeitet wird, bieten Themen und Abonnements eine **1:n**-Kommunikationsform mit einem Veröffentlichungs- und Abonnementsmuster. Es ist möglich, mehrere Abonnements zu einem Thema anzumelden. Wenn eine Nachricht an ein Thema gesendet wird, steht sie in jedem Abonnement zur Verfügung, wo sie unabhängig von den anderen Abonnements verarbeitet wird.

Ein Themenabonnement ähnelt einer virtuellen Warteschlange, die Kopien der Nachrichten enthält, die an das Thema gesendet wurden. Sie können optional auch Filterregeln für einzelne Abonnements eines Themas anmelden. Auf diese Weise können Sie filtern/einschränken, welche Nachrichten an ein Thema von welchen Themenabonnements empfangen werden.

Mit Servicebus-Themen und -Abonnements können Sie sehr viele Nachrichten an sehr viele Benutzer und Anwendungen verarbeiten.

<h2><a name="create-a-service-namespace"></a>Erstellen eines Dienstnamespace</h2>


Um mit der Verwendung von Servicebus-Themen und -Abonnements in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit zur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][1] an.

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf **Servicebus**.

3.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Erstellen**.   
     ![](./media/howto-service-bus-topics/sb-queues-13.png)

4.  Geben Sie im Dialogfeld **Add a new namespace** einen Namen für den Namespace ein. Das System prüft sofort, ob dieser Name verfügbar ist.   
     ![](./media/howto-service-bus-topics/sb-queues-04.png)

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der sie Ihre Rechnerressourcen einsetzen.)
    
    WICHTIG: Wählen Sie **dieselbe Region**, in der Sie auch Ihre Anwendung einsetzen möchten. Dies sorgt für die beste Leistung.

6.  Aktivieren Sie das Kontrollkästchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.
    
    ![](./media/howto-service-bus-topics/getting-started-multi-tier-27.png)

<h2><a name="obtain-default-credentials"></a>Abrufen der Standard-Anmeldeinformationen für den Namespace</h2>


Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung eines Themas oder Abonnements im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen. Sie finden diese Anmeldeinformationen entweder im Verwaltungsportal oder im Visual Studio Server-Explorer.

### So rufen Sie die Anmeldeinformationen im Verwaltungsportal ab

1.  Klicken Sie im linken Navigationsbereich auf den Knoten **Servicebus**, um die Liste verfügbarer Namespaces anzuzeigen:   
     ![](./media/howto-service-bus-topics/sb-queues-13.png)

2.  Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben:   
     ![](./media/howto-service-bus-topics/sb-queues-09.png)

3.  Klicken Sie auf **Verbindungsinformationen**.   
     ![](./media/howto-service-bus-topics/sb-queues-06.png)

4.  Suchen Sie im Dialogfeld **Access connection information** nach den Einträgen **Default Issuer** und **Standardschlüssel**. Notieren Sie sich diese Werte, da Sie diese Informationen später benötigen, um Operationen mit dem Namespace durchzuführen.

### So rufen Sie die Anmeldeinformationen im Server-Explorer ab

Führen Sie die [hier][2] im Abschnitt **Verbinden mit Windows Azure in Visual Studio** beschriebenen Schritte aus, um Verbindungsinformationen über Visual Studio anstatt aus dem Verwaltungsportal abzurufen. Wenn Sie sich bei Azure anmelden, wird der Knoten **Service Bus** unterhalb der **Microsoft Azure**-Baumdarstellung im Server-Explorer automatisch mit allen Namespaces ausgefüllt, die Sie bereits erstellt haben. Klicken Sie mit der rechten Maustaste auf einen Namespace und klicken Sie anschließend auf **Eigenschaften**, um Verbindungszeichenfolge und andere Metadaten dieses Namespace im Bereich **Eigenschaften** in Visual Studio anzuzeigen.

Notieren Sie den Wert **SharedAccessKey** oder kopieren Sie ihn in die Zwischenablage:

![](./media/howto-service-bus-topics/VSProperties.png)



[1]: http://manage.windowsazure.com
[2]: http://http://msdn.microsoft.com/de-de/library/windowsazure/ff687127.aspx
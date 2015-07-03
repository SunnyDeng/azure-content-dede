<a id="what-are-service-bus-queues"></a>
## Was sind Service Bus-Warteschlangen?

Service Bus-Warteschlangen unterstützen ein Modell für die **Brokermessagingkommunikation** m. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht cdirekt miteinander, sondern tauschen Nachrichten über eine a Warteschlange aus, die als Zwischenstufe (Broker) fungiert. Ein Nachrichtenproducer (Absender) hübergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort.
Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der qWarteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom fConsumer warten, um seine Funktion fortzusetzen und weitere mNachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)**-Verfahren tan einen oder mehrere Consumer. Die Nachrichten werden also normalerweise rin der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, awie sie in die Warteschlange übergeben wurden, und jede Nachricht owird nur von einem Consumer verarbeitet.
 
Service Bus-Warteschlangen sind eine Allzwecktechnologie a für viele unterschiedliche Szenarien:

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

-   Kommunikation zwischen Web- und Workerrollen in 
    Azure-Anwendungen mit mehreren Ebenen 
-   Kommunikation zwischen lokalen Anwendungen und Azure-Anwendungen in
    einer Hybridlösung
-   Kommunikation zwischen Komponenten einer verteilten lokalen
    Anwendung, die in verschiedenen Organisationen oder Abteilungen einer
    Organisation laufen

Warteschlangen helfen bei der Skalierung Ihrer Anwendungen eund führen zu einer robusteren Architektur.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Service Bus-Features in Azure zu beginnen, müssen Sie zuerst ceinen Dienstnamespace erstellen. Ein Dienstnamespace ist ein Bereichscontainer czur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace:

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][] an.

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf
    **Servicebus**.

3.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Erstellen**.   
	![](./media/howto-service-bus-queues/sb-queues-03.png)
4.  Geben Sie im Dialogfeld **Neuen Namespace hinzufügen** einen Namen für den Namespace ein.
    Das System überprüft sofort, ob dieser Name verfügbar ist.   
	![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Nachdem Sie sich vergewissert haben, dass der Namespacename verfügbar ist, das
    Land oder die Region aus, in dem bzw. der der Namespace gehostet werden soll. (Stellen
    Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der Sie Ihre
    Computerressourcen bereitstellen.)

	WICHTIG: Wählen Sie **dieselbe Region** aus, in der
    Sie auch Ihre Anwendung bereitstellen möchten. Dies sorgt für die beste Leistung.

6. 	Lassen Sie die anderen Felder im Dialog bei ihren Standardwerten (**Messaging** und **Standardebene**), und klicken Sie anschließend auf das Häkchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

	![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

Der neue Namespace wird anschließend im Verwaltungsportal angezeigt und tin kurzer Zeit aktiviert. Warten Sie, bis der Status **Aktiv** angezeigt wird, bevor cSie fortfahren.

## Abrufen der Standard-Anmeldeinformationen für den Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im tneuen Namespace, müssen Sie die Anmeldeinformationen für den nNamespace abrufen. Sie finden diese Anmeldeinformationen entweder im Verwaltungsportal oder im Visual Studio Server-Explorer.

### So rufen Sie die Anmeldeinformationen im Verwaltungsportal ab
1.  Klicken Sie im linken Navigationsbereich auf den Knoten **Service Bus**, um die
    Liste verfügbarer Namespaces anzuzeigen:   
	![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben:   
	![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Klicken Sie auf **Verbindungsinformationen**.   
	![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Im Bereich **Zugriff auf die Verbindungsinformationen** finden Sie die Verbindungszeichenfolge, die den SAS-Schlüssel und den Schlüsselnamen enthält.   

	![](./media/howto-service-bus-queues/multi-web-45.png)
    

4.  Notieren Sie den Schlüssel oder kopieren Sie ihn in die Zwischenablage.

### So rufen Sie die Anmeldeinformationen im Server-Explorer ab

Führen Sie die [hier](http://msdn.microsoft.com/library/windowsazure/ff687127.aspx) im Abschnitt **Verbinden mit Azure in Visual Studio** beschriebenen Schritte aus, um Verbindungsinformationen über Visual Studio anstatt aus dem Verwaltungsportal abzurufen. Wenn Sie sich bei Azure anmelden, wird der Knoten **Service Bus** unterhalb der **Microsoft Azure**-Baumdarstellung im Server-Explorer automatisch mit allen Namespaces ausgefüllt, die Sie bereits erstellt haben. Klicken Sie mit der rechten Maustaste auf einen Namespace und klicken Sie anschließend auf **Eigenschaften**, um die Verbindungszeichenfolge und andere Metadaten dieses Namespace im Bereich **Eigenschaften** in Visual Studio anzuzeigen. 

Notieren Sie sich den Wert **SharedAccessKey**, oder kopieren Sie ihn in die Zwischenablage:

![][34]

  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com

  [34]: ./media/howto-service-bus-queues/VSProperties.png

<!---HONumber=47-->

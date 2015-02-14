<a id="what-are-service-bus-queues"></a>
##Was sind Service Bus-Warteschlangen?

Service Bus-Warteschlangen unterstützen ein Modell für **verwaltete Messaging-Kommunikation**. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort.
Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)-Verfahren** an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

-   Kommunikation zwischen Web- und Workerrollen in Azure-Anwendungen mit mehreren Ebenen
-   Kommunikation zwischen lokalen Anwendungen und Azure-Anwendungen in einer Hybridlösung
-   Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen helfen bei der Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.

<a id="create-a-service-namespace"></a>
<h2>Erstellen eines Dienstnamespaces</h2>

Um mit der Verwendung von Service Bus-Warteschlangen in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit zur Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace:

1.  Melden Sie sich am [Azure-Verwaltungsportal][] an.

2.  Klicken Sie im linken Navigationsbereich des Verwaltungsportals auf
    **Service Bus**.

3.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Erstellen**.   
	![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Geben Sie im Dialogfeld **Neuen Namespace hinzufügen** einen Namen für den Namespace ein.
    Das System überprüft sofort, ob dieser Name verfügbar ist.   
	![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Wählen Sie nach der Bestätigung, dass der Name für den Namespace verfügbar ist, das Land oder die Region, wo dieser Namespace gehostet werden soll. (Stellen Sie sicher, dass dies dasselbe Land/dieselbe Region ist, in dem/der Sie Ihre Rechnerressourcen einsetzen.)

	WICHTIG: Wählen Sie **dieselbe Region** aus, in der Sie auch Ihre Anwendung bereitstellen möchten. Dies sorgt für die beste Leistung.

6. 	Lassen Sie die anderen Felder im Dialog bei ihren Standardwerten (**Messaging** und **Standardebene**), und klicken Sie anschließend auf das Häkchen. Ihr Dienstnamespace wird nun erstellt und aktiviert. Eventuell müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto bereitgestellt werden.

	![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

Der neue Namespace wird anschließend im Verwaltungsportal angezeigt und in kurzer Zeit aktiviert. Fahren Sie erst fort, wenn der Status als **Aktiv** angezeigt wird.

<a id="obtain-default-credentials"></a>
<h2>Abrufen der Standardanmeldeinformationen für den Namespace</h2>

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen. Sie finden diese Anmeldeinformationen entweder im Verwaltungsportal oder im Visual Studio Server-Explorer.

###So rufen Sie die Anmeldeinformationen im Verwaltungsportal ab

1.  Klicken Sie im linken Navigationsbereich auf den Knoten **Service Bus**, um die Liste verfügbarer Namespaces anzuzeigen:   
	![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben:   
	![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Klicken Sie auf **Verbindungsinformationen**.   
	![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Im Bereich **Zugriff auf die Verbindungsinformationen** finden Sie die Verbindungszeichenfolge, die den SAS-Schlüssel und den Schlüsselnamen enthält.   

	![](./media/howto-service-bus-queues/multi-web-45.png)
    

4.  Notieren Sie den Schlüssel oder kopieren Sie ihn in die Zwischenablage.

###So rufen Sie die Anmeldeinformationen im Server-Explorer ab

Um die Verbindungsinformationen über Visual Studio anstatt über das Verwaltungsportal zu erhalten, verwenden Sie das [hier](http://http://msdn.microsoft.com/de-de/library/windowsazure/ff687127.aspx), im Abschnitt **Verbinden mit Azure aus Visual Studio** beschriebene Verfahren. Wenn Sie sich bei Azure anmelden, wird der Knoten **Service Bus** unterhalb der **Microsoft Azure**-Baumdarstellung im Server-Explorer automatisch mit allen Namespaces ausgefüllt, die Sie bereits erstellt haben. Klicken Sie mit der rechten Maustaste auf einen Namespace und klicken Sie anschließend auf **Eigenschaften**, um die Verbindungszeichenfolge und andere Metadaten dieses Namespace im Bereich **Eigenschaften** in Visual Studio anzuzeigen. 

Notieren Sie sich den Wert **SharedAccessKey**, oder kopieren Sie ihn in die Zwischenablage:

![][34]

  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com

  [34]: ./media/howto-service-bus-queues/VSProperties.png

<!--HONumber=42-->

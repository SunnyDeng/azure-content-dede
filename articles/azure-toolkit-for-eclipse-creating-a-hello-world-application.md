<properties
    pageTitle="Erstellen einer Hello World-Anwendung für Azure in Eclipse"
    description="Erfahren Sie, wie Sie eine einfache Hello World-Anwendung mit dem Azure-Toolkit für Eclipse erstellen."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Erstellen einer Hello World-Anwendung für Azure in Eclipse #

In den folgenden Schritten wird gezeigt, wie Sie mit dem Azure-Toolkit für Eclipse eine einfache JSP-Anwendung erstellen und in Azure bereitstellen. Aus Gründen der Einfachheit wird ein JSP-Beispiel verwendet, aber mit einer ganz ähnlichen Vorgehensweise können Sie auch ein Java-Servlet in Azure bereitstellen.

Die Anwendung sieht etwa wie folgt aus:

![][ic600360]

## Voraussetzungen ##

* Ein Java Developer Kit (JDK), Version 1.7 oder höher.
* Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden.
* Eine Distribution eines Java-basierten Webservers oder Anwendungsservers wie Apache Tomcat, GlassFish, JBoss Application Server, Jetty oder IBM® WebSphere® Application Server Liberty Core.
* Ein Azure-Abonnement, das von <http://azure.microsoft.com/pricing/purchase-options/> bezogen werden kann.
* Das Azure-Toolkit für Eclipse. Weitere Informationen finden Sie unter [Installieren des Azure-Toolkits für Eclipse][].

## So erstellen Sie eine Hello World-Anwendung ##

Zunächst beginnen wir mit der Erstellung eines Java-Projekts.

*  Starten Sie Eclipse, klicken Sie im Menü auf **File**, auf **New** und anschließend auf **Dynamic Web Project**. (Wenn **Dynamic Web Project** nach Klicken auf **File** und **New** nicht als verfügbares Projekt aufgeführt ist, gehen Sie wie folgt vor: Klicken Sie auf **File**, anschließend auf **New** und dann auf **Project**. Erweitern Sie die Option **Web**, klicken Sie auf **Dynamic Web Project** und dann auf **Next**.)
*  Nennen Sie das Projekt für die Zwecke dieses Tutorials **MyHelloWorld**. (Verwenden Sie diesen Namen unbedingt, weil in den weiteren Schritten dieses Tutorials erwartet wird, dass Ihre WAR-Datei „MyHelloWorld“ heißt). Ihr Bildschirm sieht dann in etwa wie folgt aus:
	![][ic589576]
* Klicken Sie auf **Fertig stellen**.
* Erweitern Sie in der Projektexplorer-Ansicht von Eclipse die Option **MyHelloWorld**. Klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **New** sowie auf **JSP File**.
* Geben Sie der Datei im Dialogfeld **New JSP File** den Namen **index.jsp**. Nennen Sie den übergeordneten Ordner wie im Folgenden gezeigt **MyHelloWorld/WebContent**:

	![][ic659262]
* Wählen Sie im Dialogfeld **Select JSP Template** für die Zwecke dieses Tutorials **New JSP File (html)**, und klicken Sie dann auf **Finish**.
* Wenn in Eclipse die Datei „index.jsp“ geöffnet wird, geben Sie den Text **Hello World!** ein, damit er im vorhandenen `<body>`-Element angezeigt wird. Der aktualisierte `<body>`-Inhalt sollte wie folgt aussehen:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Speichern Sie die Datei „index.jsp“.

## So stellen Sie die Anwendung schnell und einfach in Azure bereit ##

Wenn Ihre Java-Webanwendung zum Testen bereit steht, können Sie sie auf einem abgekürzten Weg direkt in der Azure-Cloud ausprobieren.

1. Klicken Sie im Projektexplorer von Eclipse auf **MyHelloWorld**.
1. Klicken Sie auf der Eclipse-Symbolleiste auf die Schaltfläche **Publish to Azure Cloud**
    ![][ic710882]
1. Wenn Sie diese Anwendung zum ersten Mal in Azure veröffentlichen und Sie zuvor noch kein Azure-Bereitstellungsprojekt für diese Anwendung erstellt haben, wird automatisch ein Azure-Bereitstellungsprojekt für Sie erstellt. In der Regel wird Ihnen die folgende Eingabeaufforderung angezeigt, die auch das JDK-Paket und den Anwendungsserver auflistet. Beide werden automatisch bereitgestellt, um Ihre Anwendung auszuführen.
    ![][ic789598]
    Auf diesem abgekürzten Weg können Sie Ihre Anwendung schnell und einfach in Azure testen, ohne einen bestimmten Server oder ein JDK zu konfigurieren und ohne dabei Standardeinstellungen zu verändern. Wenn Sie mit den Standardeinstellungen zufrieden sind, klicken Sie auf **OK**, um die folgenden Schritte auszuführen.
    Falls Sie jedoch das JDK oder den Anwendungsserver für Ihre Anwendung ändern möchten, ist dies später möglich. Dazu bearbeiten Sie das automatisch erstellte Azure-Bereitstellungsprojekt, oder Sie klicken jetzt auf **Cancel** und lesen in diesem Tutorial den Abschnitt **Informationen zu Azure-Bereitstellungsprojekten**.
1. Führen Sie im Dialogfeld **Publish to Azure** folgende Schritte aus:
    1. Wenn in der Liste **Subscription** noch keine Abonnements aufgeführt sind, importieren Sie Ihre Abonnementinformationen wie folgt:
        1. Klicken Sie auf **Import from PUBLISH-SETTINGS file**.
        1. Klicken Sie im Dialogfeld **Import Subscription Information** auf **Download PUBLISH-SETTINGS File**. Wenn Sie noch nicht bei Ihrem Azure-Konto angemeldet sind, werden Sie zur Anmeldung aufgefordert. Anschließend werden Sie aufgefordert, eine Datei mit Azure-Veröffentlichungseinstellungen zu speichern. Speichern Sie sie auf Ihrem lokalen Computer.
        1. Klicken Sie im Dialogfeld **Import Subscription Information** auf die Schaltfläche **Browse**, wählen Sie die im vorherigen Schritt lokal gespeicherte Datei mit den Veröffentlichungseinstellungen aus, und klicken Sie dann auf **Open**. Ihr Bildschirm sollte ähnlich dem folgenden aussehen:

			![][ic644267]
        1. Klicken Sie auf **OK**.
    1. Wählen Sie bei **Subscription** das Abonnement aus, das Sie für Ihre Bereitstellung verwenden möchten.
    1. Bei **Storage account** wählen Sie das zu verwendende Speicherkonto aus, oder klicken Sie auf **New**, um ein neues Speicherkonto zu erstellen.
    1. Bei **Service name** wählen Sie den zu verwendenden Clouddienst aus, oder klicken Sie auf **New**, um einen neuen Clouddienst zu erstellen.
    1. Bei **Target OS** wählen Sie die Version des Betriebssystems aus, die Sie für Ihre Bereitstellung verwenden möchten.
    1. Bei **Target environment** wählen Sie für die Zwecke dieses Tutorials **Staging** aus. (Wenn Sie Ihre Website für den Produktivbetrieb bereitstellen möchten, ändern Sie diese Einstellung in **Production**.)
    1. Optional: Wenn die neue Bereitstellung die vorherige automatisch überschreiben soll, aktivieren Sie **Overwrite previous deployment**. Falls die Veröffentlichung am gleichen Speicherort erfolgt, können Sie durch Aktivieren dieser Option Probleme vom Typ „409 Konflikt“ vermeiden. 
        Beachten Sie, dass das Dialogfeld **Publish to Azure** einen Abschnitt **Remote Access** für den Remotezugriff enthält. In der Standardeinstellung ist der Remotezugriff nicht aktiviert, und er wird für dieses Beispiel auch nicht aktiviert. Zum Aktivieren des Remotezugriffs müssten Sie einen Benutzernamen und ein Kennwort angeben, das bei der Remoteanmeldung verwendet wird. Weitere Informationen zum Remotezugriff finden Sie unter [Aktivieren des Remotezugriffs für Azure-Bereitstellungen in Eclipse][].
        Das bei Ihnen angezeigte Dialogfeld **Publish to Azure** sieht etwa wie folgt aus:
        ![][ic719488]
1. Klicken Sie auf **Publish**, um die Veröffentlichung in einer Stagingumgebung anzustoßen.
    Wenn Sie zum Ausführen eines vollständigen Builds aufgefordert werden, klicken Sie auf **Yes**. Das Erstellen des ersten Builds kann mehrere Minuten in Anspruch nehmen.
    In Eclipse wird eine Registerkarte **Azure Activity Log** angezeigt.
    ![][ic719489]
    In diesem Protokoll oder in der Ansicht **Console** können Sie den Fortschritt bei Ihrer Bereitstellung verfolgen. Alternativ dazu können Sie sich beim [Azure-Verwaltungsportal][] anmelden und den Abschnitt **Cloud Services** verwenden, um den Status zu überwachen.
1. Wenn die Bereitstellung erfolgreich abgeschlossen wurde, wird im **Azure Activity Log** der Status **Published** angezeigt. Klicken Sie wie in der folgenden Abbildung gezeigt auf **Published**. Daraufhin öffnet Ihr Browser eine Instanz Ihrer Bereitstellung.
    ![][ic719490]

Da die Bereitstellung in einer Stagingumgebung erfolgt ist, hat der DNS-Name die Form http://&lt;*guid*&gt;.cloudapp.net, und die URL enthält den DNS-Namen sowie ein Suffix für Ihre Anwendung. Beispiel: http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (Bei dem Teil **MyHelloWorld** muss die Groß-/Kleinschreibung beachtet werden.) Sie können den DNS-Namen auch anzeigen, wenn Sie im Azure-Verwaltungsportal im Bereich „Cloud Services“ auf den Namen der Bereitstellung klicken.

In dieser exemplarischen Vorgehensweise wurde die Bereitstellung in einer Stagingumgebung ausgeführt. Bei einer Bereitstellung für den Produktivbetrieb führen Sie jedoch die gleichen Schritte aus. Der einzige Unterschied besteht darin, dass Sie im Dialogfeld **Publish to Azure** bei **Target environment** nicht **Staging**, sondern **Production** auswählen. Eine Bereitstellung für den Produktivbetrieb führt zu einer URL, die auf einem DNS-Namen Ihrer Wahl basiert und nicht auf einer GUID wie beim Staging.

>[AZURE.WARNING]Sie haben jetzt Ihre Azure-Anwendung in der Cloud bereitgestellt. Bevor Sie fortfahren, sollten Sie sich jedoch klarmachen, dass für eine bereitgestellte Anwendung – auch wenn sie nicht ausgeführt wird – weiterhin auf Ihr Abonnement angerechnete fakturierbare Zeiten entstehen. Es ist daher äußerst wichtig, dass Sie nicht gewünschte Bereitstellungen aus Ihrem Azure-Abonnement löschen.

## Informationen zu Azure-Bereitstellungsprojekten ##

Zum Bereitstellen von Java-Anwendungen in Azure benötigen Sie ein Azure-Bereitstellungsprojekt. Dieses fungiert als Paket, in das Ihre Anwendungen für die Veröffentlichung in Azure „eingepackt“ werden müssen.

Neben den Informationen über Ihre Anwendungen enthält ein Azure-Bereitstellungsprojekt auch Informationen über andere wichtige Komponenten der Bereitstellung, insbesondere über den Anwendungsservercontainer, in dem die Web-App ausgeführt wird, und die Java-Runtime, auf der sie ausgeführt wird. Azure unterstützt eine große Zahl von Java-Runtimes und Java-Anwendungsservern, unter denen Sie auswählen können.

Das hier verwendete Beispiel wurde zu Lernzwecken erheblich vereinfacht. Ein Azure-Bereitstellungsprojekt kann jedoch auch andere wichtige Konfigurationsinformationen enthalten. Dies ermöglicht es Ihnen, mit Ihren Anwendungen nahezu beliebig komplexe, skalierbare, hochverfügbare Mehrebenen-Clouddienste zu erstellen. Sie können z. B. **Sitzungsaffinität (persistente Sitzungen)**, **schnelles Zwischenspeichern**, **Remotedebuggen**, **SSL-Abladung**, **Firewall-/Portrourting**, **Remotezugriff** und eine Reihe von weiteren leistungsfähigen Funktionen aktivieren.

Wenn Sie die Schritte im vorherigen Abschnitt dieses Tutorials („So stellen Sie die Anwendung schnell und einfach in Azure bereit“) vollständig ausgeführt haben, sehen Sie jetzt im Projektexplorer ein für Sie neu generiertes Azure-Bereitstellungsprojekt mit dem Namen „**MyHelloWorld\_onAzure**“

Sie hätten auch zu Beginn dieses Tutorials selbst ein leeres Azure-Bereitstellungsprojekt erstellen und diesem anschließend Ihre Anwendung(en) hinzufügen können. Diese Vorgehensweise ist langwieriger, gibt Ihnen aber von Anfang an mehr Kontrolle über die Erstkonfiguration.

Um ein Azure-Bereitstellungsprojekt von Grund auf neu zu erstellen, klicken Sie auf die Schaltfläche **New Azure Deployment Project** ![][ic710876].

Unabhängig davon, ob Sie mit einem bereits vorhandenen Azure-Bereitstellungsprojekt arbeiten oder eines von Grund auf neu erstellen, können Sie dessen Konfigurationseinstellungen und Komponenten jederzeit problemlos ändern, z. B. das JDK oder den Anwendungsserver.

So ändern Sie das JDK, den Anwendungsserver oder die Anwendungsliste in einem vorhandenen Azure-Bereitstellungsprojekt:

1. Erweitern Sie im Projektexplorer den Projektknoten (z. B. **MyHelloWorld\_onAzure**).
2. Klicken Sie mit der rechten Maustaste auf **WorkerRole1**.
3. Erweitern Sie im Kontextmenü das Untermenü **Azure**.
4. Klicken Sie auf **Server Configuration**.

Unabhängig davon, ob Sie diese Serverkonfigurationsschritte durch Bearbeiten eines vorhandenen Azure-Bereitstellungsprojekts oder durch eine vollkommene Neuerstellung gestartet haben, werden Ihnen gleichartige Dialogfelder angezeigt, in denen Sie Ihre JDK-, Server- und Anwendungskomponenten konfigurieren können. Ausführliche Informationen zum Ändern der Einstellungen in diesen Dialogfeldern, z. B. zum Ändern des JDKs und des Anwendungsservers sowie zum Hinzufügen oder Entfernen von Anwendungen in einer Bereitstellung, finden Sie im Artikel [Eigenschaften für die Serverkonfiguration][].

## Nur für Windows: So stellen Sie die Anwendung im Serveremulator bereit ##

>[AZURE.NOTE]Der Azure-Emulator ist nur unter Windows verfügbar. Überspringen Sie diesen Abschnitt, wenn Sie ein anderes Betriebssystem als Windows verwenden.

Wenn Sie ein neues Azure-Bereitstellungsprojekt gemäß den zuvor beschriebenen Schritten erstellt haben, d. h. implizit, indem Sie Ihre Anwendung in Azure veröffentlicht haben, wurden das JDK und die Anwendungsserver für die Cloud konfiguriert, aber nicht für eine lokale Emulation. Ihr Projekt muss daher zum Testen im lokalen Emulator vorbereitet werden. Führen Sie dazu folgende Schritte aus:

1. Klicken Sie im Projektexplorer von Eclipse auf **MyHelloWorld\_onAzure**.
1. Klicken Sie mit der rechten Maustaste auf **WorkerRole1**.
1. Erweitern Sie im Kontextmenü das Untermenü **Azure**.
1. Klicken Sie auf **Server Configuration**.
1. Überprüfen Sie auf der Registerkarte **JDK**, ob das Toolkit ein lokales Standard-JDK für Sie konfiguriert hat. Wenn das nicht der Fall ist oder wenn Sie die angenommenen Standardwerte ändern möchten, stellen Sie sicher, dass das Kontrollkästchen **Use the JDK from this file path for testing locally** aktiviert ist und dass der gewünschte Installationsspeicherort für das JDK angegeben ist. Falls Sie diesen ändern möchten, klicken Sie auf die Schaltfläche **Browse**, und wählen Sie im Durchsuchen-Steuerelement den für das JDK zu verwendenden Verzeichnispfad aus.
1. Klicken Sie auf die Registerkarte **Server**.
1. Geben Sie unten im Dialogfeld im Textfeld **Local server path** den Pfad eines lokal installierten Servers ein, der in Typ und Hauptversionsnummer mit dem Server übereinstimmt, der oben im Dialogfeld unter dem Kontrollkästchen **Deploy a server of this type** ausgewählt wurde. Wenn Sie einen anderen Typ oder eine andere Hauptversion des Anwendungsservers verwenden möchten, müssen Sie zuerst die Auswahl unter diesem Kontrollkästchen ändern.
1. Klicken Sie auf **OK**.
1. Klicken Sie auf der Eclipse-Symbolleiste auf die Schaltfläche **Run in Azure Emulator** ![][ic710879]. Wenn die Schaltfläche **Run in Azure Emulator** nicht aktiviert ist, stellen Sie sicher, dass **MyHelloWorld\_onAzure** im Projektexplorer von Eclipse ausgewählt ist und dass der Projektexplorer von Eclipse als aktuelles Fenster den Fokus hat. Dadurch wird zunächst eine vollständige Erstellung Ihres Projekts gestartet, und anschließend wird Ihre Java-Webanwendung im Serveremulator aufgerufen. (Beachten Sie, dass die erste Erstellung je nach Leistungscharakteristik Ihres Computers einige Sekunden oder auch einige Minuten in Anspruch nehmen kann. Nachfolgende Erstellungsvorgänge werden schneller ausgeführt.) Nach Abschluss des ersten Erstellungsschritts werden Sie von der Windows-Benutzerkontensteuerung (UAC, User Account Control) zu einer Genehmigung aufgefordert, dass dieser Befehl an Ihrem Computer Änderungen vornehmen darf. Klicken Sie auf **Ja**.

>[AZURE.IMPORTANT]Wenn Ihnen die Eingabeaufforderung der Benutzerkontensteuerung nicht angezeigt wird, prüfen Sie, ob auf der Windows-Taskleiste ein Symbol für die Benutzerkontensteuerung angezeigt wird. Mitunter wird die Eingabeaufforderung der Benutzerkontensteuerung nicht als oberstes Fenster angezeigt und ist nur als Taskleistensymbol sichtbar.

1. Untersuchen Sie die Ausgabe in der Benutzeroberfläche des Serveremulators und prüfen Sie, ob bei Ihrem Projekt Probleme vorhanden sind. Abhängig vom Inhalt Ihrer Bereitstellung kann es einige Minuten dauern, bis Ihre Anwendung im Serveremulator vollständig gestartet ist.
1. Rufen Sie einen Browser auf, und verwenden Sie als Adresse die URL `http://localhost:8080/MyHelloWorld` (bei dem Teil `MyHelloWorld` der URL muss die Groß-/Kleinschreibung beachtet werden). Daraufhin sollte die Anwendung „MyHelloWorld“ angezeigt werden (die Ausgabe von „index.jsp“), ähnlich wie in der folgenden Abbildung:
    ![][ic589579]

Wenn Sie bereit sind, die im Serveremulator ausgeführte Anwendung zu beenden, klicken Sie auf der Eclipse-Symbolleiste auf die Schaltfläche **Reset Azure Emulator** ![][ic710880].

## So löschen Sie die Bereitstellung ##

Zum Löschen Ihrer Bereitstellung im Azure-Toolkit für Eclipse stellen Sie zunächst sicher, dass **MyHelloWorld\_onAzure** im Projektexplorer von Eclipse ausgewählt ist und dass der Projektexplorer als aktuelles Fenster den Fokus hat. Klicken Sie dann auf der Eclipse-Symbolleiste auf die Schaltfläche **Unpublish** ![][ic710883]. (Sie können den gleichen Vorgang auch ausführen, indem Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf **MyHelloWorld\_onAzure** klicken. Anschließend klicken Sie auf **Azure** und dann auf **Undeploy from Azure Cloud**.) Daraufhin wird das Dialogfeld **Unpublish Azure Project** angezeigt.

![][ic719491]

Wählen Sie das Abonnement und den Clouddienst aus, der Ihre Bereitstellung enthält, wählen Sie die zu löschende Bereitstellung aus, und klicken Sie dann auf **Unpublish**.

(Alternativ zum Toolkit können Sie auch im Azure-Verwaltungsportal den Abschnitt **Cloud Services** verwenden, um die Bereitstellung zu löschen. Navigieren Sie dort zu Ihrer Bereitstellung, wählen Sie sie aus, und klicken Sie auf die Schaltfläche **Löschen**.) Dadurch wird Ihre Bereitstellung zunächst beendet und dann gelöscht. Wenn Sie die Bereitstellung nur beenden und nicht löschen möchten, klicken Sie nicht auf die Schaltfläche **Löschen**, sondern auf die Schaltfläche **Beenden**. Beachten Sie jedoch: Wenn Sie die Bereitstellung nicht löschen, fallen wie oben erwähnt weiterhin fakturierbare Gebühren an (selbst bei nicht mehr ausgeführter Bereitstellung).

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Installieren des Azure-Toolkits für Eclipse][]

[Neuerungen im Azure-Toolkit für Eclipse][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Aktivieren des Remotezugriffs für Azure-Bereitstellungen in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Eigenschaften für die Serverkonfiguration]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Neuerungen im Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png

<!-----HONumber=AcomDC_1210_2015-->
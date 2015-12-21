<properties
    pageTitle="Debuggen von Azure-Anwendungen in Eclipse"
    description="Erfahren Sie, wie Sie Azure-Anwendungen mit dem Azure-Toolkit für Eclipse debuggen."
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# Debuggen von Azure-Anwendungen in Eclipse #

Mit dem Azure-Toolkit für Eclipse können Sie Ihre Anwendungen debuggen, und zwar unabhängig davon, ob sie in Azure oder im Serveremulator oder auf einem Windows-Betriebssystem ausgeführt werden. Die folgende Abbildung zeigt das Eigenschaftendialogfeld **Debuggen**, in dem das Remotedebuggen aktiviert wird:

![][ic719504]

In diesem Tutorial wird davon ausgegangen, dass Sie bereits über eine erfolgreich erstellte Anwendung verfügen und mit dem Serveremulator sowie mit dem Bereitstellen in Azure vertraut sind.

Wir verwenden die Anwendung aus dem Tutorial [Verwenden der Azure-Dienstlaufzeitbibliothek in JSP][] als Ausgangspunkt für dieses Thema. Erstellen Sie jetzt diese Anwendung, falls Sie es noch nicht getan haben. Erst danach können Sie die Schritte in diesem Tutorial ausführen.

## So debuggen Sie die in Azure ausgeführte Anwendung ##

>[AZURE.WARNING]Das Toolkit unterstützt das Java-Remotedebuggen in erster Linie für Bereitstellungen, die im Azure-Serveremulator ausgeführt werden. Da die Debugverbindung nicht sicher ist, sollten Sie das Remotedebuggen nicht für Produktionsbereitstellungen aktivieren. Wenn Sie eine bereits in der Azure-Cloud ausgeführte Anwendung debuggen müssen, verwenden Sie eine Stagingbereitstellung. Aber selbst bei einer Stagingbereitstellung müssen Sie wissen, dass ein unberechtigter Zugriff möglich ist, wenn jemand die IP-Adresse Ihrer Cloudbereitstellung kennt.

1. Erstellen Sie Ihr Projekt für das Testen im Emulator: Klicken Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf **MyAzureProject**, klicken Sie auf **Properties** und anschließend auf **Azure**, und legen Sie bei **Build for** die Einstellung **Deployment to cloud** fest.
1. Erstellen Sie Ihr Projekt neu: Klicken Sie im Eclipse-Menü auf **Project** und dann auf **Build All**.
1. Stellen Sie Ihre Anwendung unter *Staging* in Azure bereit.
    >[AZURE.IMPORTANT]Wie oben erwähnt: Es wird dringend empfohlen, dass Sie das Debuggen in den allermeisten Fällen im Serveremulator ausführen und nur dann in der Stagingumgebung debuggen, wenn ein zusätzliches Debuggen erforderlich ist. Es wird empfohlen, nicht in der Produktionsumgebung zu debuggen.
1. Wenn die Bereitstellung in Azure abgeschlossen ist, suchen und notieren Sie den DNS-Namen im [Azure-Verwaltungsportal][]. Eine Stagingbereitstellung hat einen DNS-Namen in der Form http://*&lt;guid&gt;*.cloudapp.net, wobei *&lt;guid&gt;* ein von Azure zugeordneter GUID-Wert ist.
1. Klicken Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf **WorkerRole1**, klicken Sie auf **Azure** und dann auf **Debugging**.
1. Gehen Sie im Dialogfeld **Properties for WorkerRole1 Debugging** wie folgt vor:
    1. Aktivieren Sie das Kontrollkästchen **Enable remote debugging for this role**.
    1. Verwenden Sie für **Input endpoint to use** den Wert **Debugging (public:8090, private:8090)**.
    1. Stellen Sie sicher, dass die Option **Start JVM in suspended mode, waiting for a debugger connection** nicht aktiviert ist.
        >[AZURE.IMPORTANT]Die Option **Start JVM in suspended mode, waiting for a debugger connection** ist für komplexere Debugszenarien im Serveremulator vorgesehen (nicht für Cloudbereitstellungen). Beim Verwenden der Option **Start JVM in suspended mode, waiting for a debugger connection** wird der Startprozess des Servers solange angehalten, bis der Eclipse-Debugger mit seiner JVM verbunden ist. Sie können diese Option zwar für eine Debugsitzung mit dem Serveremulator nutzen, aber für eine Debugsitzung in einer Cloudbereitstellung dürfen Sie sie nicht verwenden. Im Zuge einer Startaufgabe von Azure wird ein Server initialisiert, und die Azure-Cloud macht öffentliche Endpunkte erst verfügbar, wenn die Startaufgabe abgeschlossen ist. Bei Aktivierung dieser Option in einer Cloudbereitstellung wird ein Startprozess nicht erfolgreich abgeschlossen, weil er keine Verbindung von einem externen Eclipse-Client empfangen kann.
    1. Klicken Sie auf **Create Debug Configurations**.
1. Gehen Sie im Dialogfeld **Azure Debug Configuration** wie folgt vor:
    1. Wählen Sie bei **Java project to debug** das Projekt **MyHelloWorld** aus.
    1. Bei **Configure debugging for** aktivieren Sie **Azure cloud (staging)**.
    1. Vergewissern Sie sich, dass **Azure compute emulator** deaktiviert ist.
    1. Geben Sie bei **Host**, den DNS-Namen Ihrer Stagingbereitstellung ein, aber ohne das vorangestellte ****http://**. Beispiel (Ersetzen Sie die hier gezeigte GUID durch Ihre eigene GUID.): **4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
1. Klicken Sie auf **OK**, um das Dialogfeld **Azure Debug Configuration** zu schließen.
1. Klicken Sie auf **OK**, um das Dialogfeld **Properties for WorkerRole1 Debugging** zu schließen.
1. Wenn Sie in „index.jsp“ noch keinen Haltepunkt festgelegt haben, legen Sie ihn jetzt fest:
    1. Erweitern Sie im Projektexplorer von Eclipse **MyHelloWorld**, erweitern Sie **WebContent**, und doppelklicken Sie auf **index.jsp**.
    1. Klicken Sie in „index.jsp“ mit der rechten Maustaste auf den blauen Balken links vom Java-Code, und klicken Sie auf **Toggle Breakpoints**, wie in der folgenden Abbildung gezeigt: ![][ic551537]
1. Klicken Sie im Eclipse-Menü auf **Run** und dann auf **Debug Configurations**.
1. Erweitern Sie im Dialogfeld **Debug Configurations** im linken Bereich den Eintrag **Remote Java Application**, wählen Sie **Azure Cloud (WorkerRole1)** aus, und klicken Sie auf **Debug**.
1. Öffnen Sie einen Browser, und führen Sie darin die Staginganwendung ****http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld** aus, wobei Sie *&lt;guid&gt;* durch die GUID aus Ihrem DNS-Namen ersetzen müssen. Wenn Sie im Dialogfeld **Confirm Perspective Switch** zum Bestätigen des Perspektivwechsels aufgefordert werden, klicken Sie auf **Yes**. Ihre Debugsitzung sollte nun bis zu der Codezeile ausgeführt werden, für die der Haltepunkt festgelegt wurde.

>[AZURE.NOTE]Wenn Sie versuchen, eine Remotedebugverbindung mit einer Bereitstellung herzustellen, von der mehrere Instanzen ausgeführt werden, können Sie derzeit nicht steuern, mit welcher Instanz der Debugger zuerst verbunden wird. Der Azure Load Balancer wählt die Instanz nach dem Zufallsprinzip aus. Wenn die Verbindung mit einer Instanz hergestellt ist, wird das Debuggen jedoch bei dieser Instanz fortgesetzt. Allerdings beendet Azure die Verbindung möglicherweise, wenn eine Inaktivität länger als 4 Minuten anhält (z. B. wenn die Unterbrechung an einem Haltepunkt zu lange dauert).

## Debuggen einer bestimmten Rolleninstanz in einer Bereitstellung mit mehreren Instanzen ##

Wenn Ihre Bereitstellung in der Cloud ausgeführt wird, wird sie sehr wahrscheinlich in mehreren Compute-Instanzen oder Rolleninstanzen ausgeführt. Dadurch profitieren Sie von der garantierten Azure-Verfügbarkeit von 99,95 % und können Ihre Anwendung skalieren.

In einem solchen Szenario müssen Sie möglicherweise Ihre Java-Anwendung remote debuggen und dafür eine bestimmte Rolleninstanz verwenden. Wenn Sie jedoch nur einen regulären Eingabeendpunkt für das Debuggen aktivieren, macht es der Azure Load Balancer nahezu unmöglich, den Debugger mit einer bestimmten Rolleninstanz zu verbinden. Stattdessen stellt er eine Verbindung mit einer zufällig ausgewählten Rolleninstanz her.

In einem Szenario dieser Art können Sie die Vorteile von Instanzeingabe-Endpunkten nutzen, um auf einfachere Weise eine bestimmte Rolleninstanz zu debuggen.

Angenommen, Sie möchten bis zu 5 Rolleninstanzen Ihrer Bereitstellung ausführen. Dann erstellen Sie im Dialogfeld „Role Properties“ auf der Eigenschaftenseite **Endpoints** einen Instanzeingabe-Endpunkt und weisen ihm nicht nur eine einzige Portnummer, sondern einen Bereich von öffentlichen Ports zu. Geben Sie z. B. im Eingabefeld **Public port** den Wert **81-85** ein.

Nach dem Bereitstellen der Anwendung mit diesem Instanzendpunkt weist Azure jeder Rolleninstanz eine eindeutige Portnummer aus diesem Bereich zu. Um herauszufinden, welcher Instanz welche Portnummer zuwiesen wurde, verwenden Sie die automatisch vom Toolkit in Ihrer Bereitstellung konfigurierte Umgebungsvariable *InstanceEndpointName***\_PUBLICPORT** (wobei *InstanceEndpointName* der Name ist, den Sie beim Erstellen des Instanzendpunkts zugewiesen haben). Sie können z. B. den Wert dieser Variablen in der Fußzeile einer Webseite zurückgeben, sodass Sie ihn beim Anzeigen der Seite im Browser lesen können.

Wenn Sie die Nummer des öffentlichen Ports kennen, die der Instanz zugeordnet wurde, können Sie in der Debugkonfiguration in Eclipse darauf verweisen. Dazu fügen Sie sie an den Hostnamen Ihres Diensts an. Dadurch kann der Eclipse-Debugger eine Verbindung mit dieser bestimmten Instanz herstellen, ohne die anderen Instanzen zu berücksichtigen.

## Nur für Windows: So debuggen Sie die im Serveremulator ausgeführte Anwendung ##

>[AZURE.NOTE]Der Azure-Emulator ist nur unter Windows verfügbar. Überspringen Sie diesen Abschnitt, wenn Sie ein anderes Betriebssystem als Windows verwenden.

1. Erstellen Sie Ihr Projekt für das Testen im Emulator: Klicken Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf **MyAzureProject**, klicken Sie auf **Properties** und anschließend auf **Azure**, und legen Sie bei **Build for** die Einstellung **Testing in emulator** fest.
1. Erstellen Sie Ihr Projekt neu: Klicken Sie im Eclipse-Menü auf **Project** und dann auf **Build All**.
1. Klicken Sie im Projektexplorer von Eclipse mit der rechten Maustaste auf **WorkerRole1**, klicken Sie auf **Azure** und dann auf **Debugging**.
1. Gehen Sie im Dialogfeld **Properties for WorkerRole1 Debugging** wie folgt vor:
    1. Aktivieren Sie das Kontrollkästchen **Enable remote debugging for this role**.
    1. Bei **Input endpoint to use** verwenden Sie den automatisch vom Toolkit generierten Endpunkt, der als **Debugging (public:8090, private:8090)** aufgeführt ist.
    1. Stellen Sie sicher, dass die Option **Start JVM in suspended mode, waiting for a debugger connection** nicht aktiviert ist.
        >[AZURE.IMPORTANT]Die Option **Start JVM in suspended mode, waiting for a debugger connection** ist für komplexere Debugszenarien im Serveremulator vorgesehen (nicht für Cloudbereitstellungen). Beim Verwenden der Option **Start JVM in suspended mode, waiting for a debugger connection** wird der Startprozess des Servers solange angehalten, bis der Eclipse-Debugger mit seiner JVM verbunden ist. Sie können diese Option zwar für eine Debugsitzung mit dem Serveremulator nutzen, aber für eine Debugsitzung in einer Cloudbereitstellung dürfen Sie sie nicht verwenden. Im Zuge einer Startaufgabe von Azure wird ein Server initialisiert, und die Azure-Cloud macht öffentliche Endpunkte erst verfügbar, wenn die Startaufgabe abgeschlossen ist. Bei Aktivierung dieser Option in einer Cloudbereitstellung wird ein Startprozess nicht erfolgreich abgeschlossen, weil er keine Verbindung von einem externen Eclipse-Client empfangen kann.
    1. Klicken Sie auf **Create Debug Configurations**.
1. Gehen Sie im Dialogfeld **Azure Debug Configuration** wie folgt vor:
    1. Wählen Sie bei **Java project to debug** das Projekt **MyHelloWorld** aus.
    1. Bei **Configure debugging for** aktivieren Sie **Azure compute emulator**.
1. Klicken Sie auf **OK**, um das Dialogfeld **Azure Debug Configuration** zu schließen.
1. Klicken Sie auf **OK**, um das Dialogfeld **Properties for WorkerRole1 Debugging** zu schließen.
1. Legen Sie einen Haltepunkt in „index.jsp“ fest:
    1. Erweitern Sie im Projektexplorer von Eclipse **MyHelloWorld**, erweitern Sie **WebContent**, und doppelklicken Sie auf **index.jsp**.
    1. Klicken Sie in „index.jsp“ mit der rechten Maustaste auf den blauen Balken links vom Java-Code, und klicken Sie auf **Toggle Breakpoints**, wie in der folgenden Abbildung gezeigt: ![][ic551537] Ein Haltepunkt ist festgelegt, wenn im blauen Balken links vom Java-Code ein Haltepunktsymbol angezeigt wird.
1. Starten Sie die Anwendung im Serveremulator, indem Sie in der Azure-Symbolleiste auf die Schaltfläche **Run in Azure Emulator** klicken.
1. Klicken Sie im Eclipse-Menü auf **Run** und dann auf **Debug Configurations**.
1. Erweitern Sie im Dialogfeld **Debug Configurations** im linken Bereich den Eintrag **Remote Java Application**, wählen Sie **Azure Emulator (WorkerRole1)** aus, und klicken Sie auf **Debug**.
1. Wenn der Serveremulator anzeigt, dass Ihre Anwendung ausgeführt wird, führen Sie in einem Browser ****http://localhost:8080/MyHelloWorld** aus. Wenn Sie im Dialogfeld **Confirm Perspective Switch** zum Bestätigen des Perspektivwechsels aufgefordert werden, klicken Sie auf **Yes**. Ihre Debugsitzung sollte nun bis zu der Codezeile ausgeführt werden, für die der Haltepunkt festgelegt wurde.

Hier wurde veranschaulicht, wie Sie beim Debuggen im Serveremulator vorgehen. Der folgende Abschnitt veranschaulicht das Debuggen einer in Azure bereitgestellten Anwendung.

## Hinweise zum Debuggen ##

* Nach dem Debuggen können Sie die Perspektive wechseln und von **Debug** auf **Java** umschalten. Dazu klicken Sie im Eclipse-Menü auf **Window**, anschließend auf **Open Perspective**, und dann wählen Sie die gewünschte Perspektive.
* Wenn Sie das Remotedebuggen in GlassFish aktivieren möchten, dürfen Sie das Konfigurationsfeature für das Remotedebuggen im Azure-Toolkit für Eclipse nicht verwenden. Konfigurieren Sie GlassFish stattdessen manuell. GlassFish behandelt in Umgebungsvariablen vordefinierte Java-Optionen auf eine Art und Weise, die eine ordnungsgemäße Funktion des Toolkit-Konfigurationsfeatures für das Remotedebuggen bei GlassFish verhindert. Wenn das Konfigurationsfeature für das Remotedebuggen im Toolkit aktiviert ist, lässt sich GlassFish unter Umständen nicht starten.

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Erstellen einer Hello World-Anwendung für Azure in Eclipse][]

[Installieren des Azure-Toolkits für Eclipse][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Erstellen einer Hello World-Anwendung für Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Verwenden der Azure-Dienstlaufzeitbibliothek in JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!---HONumber=AcomDC_1210_2015-->
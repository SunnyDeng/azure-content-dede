<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Python-Web- und Workerrollen mit Python-Tools 2.1 f&uuml;r Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="&Uuml;bersicht &uuml;ber die Verwendung von Python-Tools f&uuml;r Visual Studio f&uuml;r die Erstellung von Azure-Cloud-Diensten, einschlie&szlig;lich Webrollen und Workerrollen." metaCanonical="" services="" documentationCenter="Python" title="Python-Web- und Workerrollen mit Python-Tools 2.1 f&uuml;r Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Python-Web- und Workerrollen mit Python-Tools 2.1 für Visual Studio

Dieser Leitfaden bietet eine Übersicht über die Verwendung von Python-Web- und Workerrollen mit [Python-Tools für Visual Studio][Python-Tools für Visual Studio].

-   [Voraussetzungen][Voraussetzungen]
-   [Was sind Python-Web- und Workerrollen?][Was sind Python-Web- und Workerrollen?]
-   [Projekterstellung][Projekterstellung]
-   [Lokales Ausführen][Lokales Ausführen]
-   [Veröffentlichen in Azure][Veröffentlichen in Azure]
-   [Nächste Schritte][Nächste Schritte]

## <a name="prerequisites"></a>Voraussetzungen

-   Visual Studio 2012 oder 2013
-   [Python Tools 2,1 für Visual Studio][Python Tools 2,1 für Visual Studio]
-   [Azure-SDK-Tools für VS 2013][Azure-SDK-Tools für VS 2013] oder [Azure-SDK-Tools für VS 2012][Azure-SDK-Tools für VS 2012]
-   [Python 2.7 32-Bit][Python 2.7 32-Bit] oder [Python 3.4 32-Bit][Python 3.4 32-Bit]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Was sind Python-Web- und Workerrollen?

Azure bietet drei Modelle für die Ausführung von Anwendungen: [Azure-Websites][Azure-Websites], [Azure Virtual Machines][Azure Virtual Machines] und [Azure Cloud Services][Azure Cloud Services]. Alle drei Modelle unterstützen Python. Cloud Services, welche Web- und Workerrollen enthalten, bieten *Platform as a Service (PaaS)*. In einem Cloud-Dienst bietet eine Webrolle einen speziellen IIS-Webserver (Internet Information Services), um Front-End-Webanwendungen zu hosten, während eine Workerrolle asynchrone, langfristige oder fortwährende Aufgaben ausführen kann, die unabhängig von einer Benutzerinteraktion oder -eingabe sind.

Weitere Informationen finden Sie unter [Was ist ein Cloud-Dienst?][Was ist ein Cloud-Dienst?].

<div class="dev-callout"><strong>M&ouml;chten Sie eine einfache Website erstellen?</strong>
<p>Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie die Verwendung einer einfachen Azure-Website ins Auge fassen. Sie k&ouml;nnen einen Cloud-Dienst leicht upgraden, wenn die Website gr&ouml;&szlig;er wird und sich Ihre Anforderungen &auml;ndern. Im <a href="/de-de/develop/python/">Python Developer Center</a> finden Sie Artikel zur Entwicklung von Azure-Websites.</p>
</div>

## <a name="project-creation"></a>Projekterstellung

In Visual Studio können Sie **Azure-Cloud-Dienst** unter **Python** im Dialogfeld **Neues Projekt** auswählen.

![Dialogfeld "Neues Projekt"][Dialogfeld "Neues Projekt"]

Im Azure-Cloud-Dienst-Assistent können Sie neue Web- und Workerrollen erstellen.

![Dialogfeld „Azure-Cloud-Dienst“][Dialogfeld „Azure-Cloud-Dienst“]

Die Workerrollenvorlage enthält Codebausteine für eine Verbindung mit einem Azure-Speicherkonto oder -Servicebus.

![Cloud-Dienstlösung][Cloud-Dienstlösung]

Sie können Web- oder Workerrollen jederzeit zu einem vorhandenen Cloud-Dienst hinzufügen. Sie können vorhandene Projekte zu Ihrer Lösung hinzufügen oder neue erstellen.

![Befehl „Rolle hinzufügen“][Befehl „Rolle hinzufügen“]

Ihr Cloud-Dienst kann Rollen enthalten, die in verschiedenen Sprachen implementiert wurden. Es ist zum Beispiel möglich, dass Sie eine Python-Webrolle haben, die mit Django implementiert wurde, zusammen mit Python- und C#-Workerrollen. Sie können mit Servicebus-Warteschlangen oder Speicherwarteschlangen einfach zwischen Ihren Rollen kommunizieren.

## <a name="run-locally"></a>Lokales Ausführen

Wenn Sie Ihr Cloud-Dienstprojekt als Startprojekt festlegen und F5 drücken, wird der Cloud-Dienst im lokalen Azure-Emulator ausgeführt.

Auch wenn PTVS den Start im Emulator unterstützt, wird Debugging (Breakpoints, etc) nicht funktionieren.

Um Web- und Workerrollen zu debuggen, können Sie das Rollenprojekt als Startprojekt festlegen und dieses stattdessen debuggen. Sie können auch mehrere Startprojekte festlegen. Klicken Sie mit der rechten Maustaste auf die Lösung, und wählen Sie **Startprojekte festlegen** aus.

![Startprojekteigenschaften der Lösung][Startprojekteigenschaften der Lösung]

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

Klicken Sie für die Veröffentlichung mit der rechten Maustaste auf das Cloud-Dienstprojekt in der Lösung, und wählen Sie **Veröffentlichen** aus.

![Anmeldung für Microsoft Azure-Veröffentlichung][Anmeldung für Microsoft Azure-Veröffentlichung]

Wählen Sie in der Einstellungsseite den Cloud-Dienst aus, den Sie veröffentlichen möchten.

![Einstellungen für Microsoft Azure-Veröffentlichung][Einstellungen für Microsoft Azure-Veröffentlichung]

Sie können einen neuen Cloud-Dienst erstellen, wenn Sie keinen zur Verfügung haben.

![Dialogfeld „Cloud-Dienst erstellen“][Dialogfeld „Cloud-Dienst erstellen“]

Es ist zudem hilfreich, für Debuggingfehler Remotedesktopverbindungen zu den Computern zu aktivieren.

![Dialogfeld „Remotedesktopkonfiguration“][Dialogfeld „Remotedesktopkonfiguration“]

Klicken Sie auf **Veröffentlichen**, wenn Sie mit den Konfigurationseinstellungen fertig sind.

Der Fortschritt wird im Ausgabefenster angezeigt, anschließend wird das Fenster mit dem Microsoft Azure-Aktivitätsprotokoll angezeigt.

![Fenster mit Microsoft Azure-Aktivitätsprotokoll][Fenster mit Microsoft Azure-Aktivitätsprotokoll]

Der Abschluss der Bereitstellung dauert einige Minuten, anschließend werden Ihre Web- und/oder Workerrollen in Azure ausgeführt!

## <a name="next-steps"></a>Nächste Schritte

Genauere Informationen zur Arbeit mit Web- und Workerrollen in Python-Tools für Visual Studio finden Sie in der PTVS-Dokumentation:

-   [Cloud-Dienstprojekte][Cloud-Dienstprojekte]

Weitere Details zur Verwendung von Azure-Diensten Ihrer Web- und Workerrollen, etwa zur Verwendung von Azure-Speicher oder Servicebus, finden Sie in folgenden Anleitungen:

-   [Blob-Dienst][Blob-Dienst]
-   [Tabellendienst][Tabellendienst]
-   [Warteschlangendienst][Warteschlangendienst]
-   [Servicebus-Warteschlangen][Servicebus-Warteschlangen]
-   [Servicebus-Themen][Servicebus-Themen]

<!--Link references-->
<!--External Link references-->

  [Python-Tools für Visual Studio]: http://pytools.codeplex.com
  [Voraussetzungen]: #prerequisites
  [Was sind Python-Web- und Workerrollen?]: #what-are-python-web-and-worker-roles
  [Projekterstellung]: #project-creation
  [Lokales Ausführen]: #run-locally
  [Veröffentlichen in Azure]: #publish-to-azure
  [Nächste Schritte]: #next-steps
  [Python Tools 2,1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Azure-SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Azure-SDK-Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 32-Bit]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 32-Bit]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Azure-Websites]: /de-de/documentation/articles/fundamentals-application-models/#WebSites
  [Azure Virtual Machines]: /de-de/documentation/articles/fundamentals-application-models/#VMachine
  [Azure Cloud Services]: /de-de/documentation/articles/fundamentals-application-models/#CloudServices
  [Was ist ein Cloud-Dienst?]: /de-de/manage/services/cloud-services/what-is-a-cloud-service/
  [Dialogfeld "Neues Projekt"]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Dialogfeld „Azure-Cloud-Dienst“]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Cloud-Dienstlösung]: ./media/cloud-services-python-ptvs/worker.png
  [Befehl „Rolle hinzufügen“]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Startprojekteigenschaften der Lösung]: ./media/cloud-services-python-ptvs/startup.png
  [Anmeldung für Microsoft Azure-Veröffentlichung]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Einstellungen für Microsoft Azure-Veröffentlichung]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Dialogfeld „Cloud-Dienst erstellen“]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Dialogfeld „Remotedesktopkonfiguration“]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Fenster mit Microsoft Azure-Aktivitätsprotokoll]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Blob-Dienst]: /de-de/documentation/articles/storage-python-how-to-use-blob-storage/
  [Tabellendienst]: /de-de/documentation/articles/storage-python-how-to-use-table-storage/
  [Warteschlangendienst]: /de-de/documentation/articles/storage-python-how-to-use-queue-storage/
  [Servicebus-Warteschlangen]: /de-de/documentation/articles/service-bus-python-how-to-use-queues/
  [Servicebus-Themen]: /de-de/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/

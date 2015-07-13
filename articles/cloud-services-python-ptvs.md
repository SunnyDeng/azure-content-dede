<properties
	pageTitle="Python-Web- und Workerrollen mit Python-Tools 2.1 für Visual Studio"
	description="Übersicht über die Verwendung von Python-Tools für Visual Studio für die Erstellung von Azure-Cloud-Diensten, einschließlich Webrollen und Workerrollen."
	services=""
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="get-started-article" 
	ms.date="02/09/2015"
	ms.author="huvalo"/>




# Python-Web- und Workerrollen mit Python-Tools 2.1 für Visual Studio

Dieser Leitfaden bietet eine Übersicht über die Verwendung von Python-Web- und Workerrollen mit [Python-Tools für Visual Studio][].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio][]
 - [Azure-SDK-Tools für VS 2013][] oder [Azure-SDK-Tools für VS 2012][]
 - [Python 2.7 32-Bit][] oder [Python 3.4 32-Bit][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Was sind Python-Web- und Workerrollen?

Azure bietet drei Computingmodelle für das Ausführen von Anwendungen: [Azure Websites][execution model-web sites], [Azure Virtual Machines][execution model-vms] und [Azure Cloud Services][execution model-cloud services]. Alle drei Modelle unterstützen Python. Cloud Services, welche Web- und Workerrollen enthalten, bieten *Platform as a Service (PaaS)*. In einem Cloud-Dienst bietet eine Webrolle einen speziellen IIS-Webserver (Internet Information Services), um Front-End-Webanwendungen zu hosten, während eine Workerrolle asynchrone, langfristige oder fortwährende Aufgaben ausführen kann, die unabhängig von einer Benutzerinteraktion oder -eingabe sind.

Weitere Informationen finden Sie unter [Was ist ein Cloud-Dienst?].

> [AZURE.NOTE]**Möchten Sie eine einfache Website erstellen?** Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie die Verwendung einer einfachen Azure-Website ins Auge fassen. Sie können einen Cloud-Dienst leicht upgraden, wenn die Website größer wird und sich Ihre Anforderungen ändern. Im <a href="/develop/python/">Python Developer Center</a> finden Sie Artikel zur Entwicklung von Azure-Websites. <br />


## Projekterstellung

In Visual Studio können Sie **Azure-Cloud-Dienst** unter **Python** im Dialogfeld **Neues Projekt** auswählen.

![Dialogfeld "Neues Projekt"](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Im Azure-Cloud-Dienst-Assistent können Sie neue Web- und Workerrollen erstellen.

![Dialogfeld "Azure Cloud Service"](./media/cloud-services-python-ptvs/new-service-wizard.png)

Die Workerrollenvorlage enthält Codebausteine für eine Verbindung mit einem Azure-Speicherkonto oder -Servicebus.

![Cloud-Dienstlösung](./media/cloud-services-python-ptvs/worker.png)

Sie können Web- oder Workerrollen jederzeit zu einem vorhandenen Cloud-Dienst hinzufügen. Sie können vorhandene Projekte zu Ihrer Lösung hinzufügen oder neue erstellen.

![Befehl "Rolle hinzufügen"](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Ihr Cloud-Dienst kann Rollen enthalten, die in verschiedenen Sprachen implementiert wurden. Es ist zum Beispiel möglich, dass Sie eine Python-Webrolle haben, die mit Django implementiert wurde, zusammen mit Python- und C#-Workerrollen. Sie können mit Servicebus-Warteschlangen oder Speicherwarteschlangen einfach zwischen Ihren Rollen kommunizieren.

## Lokales Ausführen

Wenn Sie Ihr Cloud-Dienstprojekt als Startprojekt festlegen und F5 drücken, wird der Cloud-Dienst im lokalen Azure-Emulator ausgeführt.

Auch wenn PTVS den Start im Emulator unterstützt, wird Debugging (Breakpoints, etc) nicht funktionieren.

Um Web- und Workerrollen zu debuggen, können Sie das Rollenprojekt als Startprojekt festlegen und dieses stattdessen debuggen. Sie können auch mehrere Startprojekte festlegen. Klicken Sie mit der rechten Maustaste auf die Lösung, und wählen Sie **Startprojekte festlegen** aus.

![Startprojekteigenschaften der Lösung](./media/cloud-services-python-ptvs/startup.png)

## Veröffentlichen in Azure

Klicken Sie für die Veröffentlichung mit der rechten Maustaste auf das Cloud-Dienstprojekt in der Lösung, und wählen Sie **Veröffentlichen** aus.

![Anmeldung für Microsoft Azure-Veröffentlichung](./media/cloud-services-python-ptvs/publish-sign-in.png)

Wählen Sie in der Einstellungsseite den Cloud-Dienst aus, den Sie veröffentlichen möchten.

![Einstellungen für Microsoft Azure-Veröffentlichung](./media/cloud-services-python-ptvs/publish-settings.png)

Sie können einen neuen Cloud-Dienst erstellen, wenn Sie keinen zur Verfügung haben.

![Dialogfeld "Cloud Service erstellen"](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Es ist zudem hilfreich, für Debuggingfehler Remotedesktopverbindungen zu den Computern zu aktivieren.

![Dialogfeld "Remotedesktopkonfiguration"](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Klicken Sie auf **Veröffentlichen**, wenn Sie mit den Konfigurationseinstellungen fertig sind.

Der Fortschritt wird im Ausgabefenster angezeigt, anschließend wird das Fenster mit dem Microsoft Azure-Aktivitätsprotokoll angezeigt.

![Fenster mit Microsoft Azure-Aktivitätsprotokoll](./media/cloud-services-python-ptvs/publish-activity-log.png)

Der Abschluss der Bereitstellung dauert einige Minuten, anschließend werden Ihre Web- und/oder Workerrollen in Azure ausgeführt!

## Nächste Schritte

Genauere Informationen zur Arbeit mit Web- und Workerrollen in Python-Tools für Visual Studio finden Sie in der PTVS-Dokumentation:

- [Cloud Service-Projekte][]

Weitere Details zur Verwendung von Azure-Diensten Ihrer Web- und Workerrollen, etwa zur Verwendung von Azure-Speicher oder Servicebus, finden Sie in folgenden Anleitungen:

- [Blobdienst][]
- [Tabellendienst][]
- [Warteschlangendienst][]
- [Service Bus-Warteschlangen][]
- [Service Bus-Themen][]


<!--Link references-->

[Was ist ein Cloud-Dienst?]: /manage/services/cloud-services/what-is-a-cloud-service/
[execution model-web sites]: fundamentals-application-models.md#WebSites
[execution model-vms]: fundamentals-application-models.md#VMachine
[execution model-cloud services]: fundamentals-application-models.md#CloudServices
[Python Developer Center]: /develop/python/

[Blobdienst]: storage-python-how-to-use-blob-storage.md
[Warteschlangendienst]: storage-python-how-to-use-queue-storage.md
[Tabellendienst]: storage-python-how-to-use-table-storage.md
[Service Bus-Warteschlangen]: service-bus-python-how-to-use-queues.md
[Service Bus-Themen]: service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://pytools.codeplex.com/documentation
[Cloud Service-Projekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure-SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure-SDK-Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191

<!---HONumber=July15_HO1-->
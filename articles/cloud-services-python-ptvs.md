<properties
	pageTitle="Python-Web- und Workerrollen mit Python Tools 2.2 für Visual Studio | Microsoft Azure"
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
	ms.topic="hero-article"
	ms.date="08/30/2015"
	ms.author="huvalo"/>




# Python-Web- und Workerrollen mit Python Tools 2.2 für Visual Studio

Dieser Artikel enthält eine Übersicht über die Verwendung von Python-Web- und Workerrollen mit [Python-Tools für Visual Studio][].

## Voraussetzungen

 - Visual Studio 2013 oder 2015
 - [Python Tools 2.2 für Visual Studio][] (PTVS)
 - [Azure SDK-Tools für VS 2013][] oder [Azure SDK-Tools für VS 2015][]
 - [Python 2.7 32-Bit][] oder [Python 3.4 32-Bit][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Was sind Python-Web- und Workerrollen?

Azure bietet drei Computemodelle für das Ausführen von Anwendungen: [Web-Apps-Feature in Azure App Service][execution model-web sites], [Azure Virtual Machines][execution model-vms] und [Azure Cloud Services][execution model-cloud services]. Alle drei Modelle unterstützen Python. Cloud Services, welche Web- und Workerrollen enthalten, bieten *Platform as a Service (PaaS)*. In einem Cloud-Dienst bietet eine Webrolle einen speziellen IIS-Webserver (Internet Information Services), um Front-End-Webanwendungen zu hosten, während eine Workerrolle asynchrone, langfristige oder fortwährende Aufgaben ausführen kann, die unabhängig von einer Benutzerinteraktion oder -eingabe sind.

Weitere Informationen finden Sie unter [Was ist ein Cloud-Dienst?].

> [AZURE.NOTE]*Möchten Sie eine einfache Website erstellen?* Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie die Verwendung des einfachen Web-Apps-Features von Azure App Service in Betracht ziehen. Sie können einen Cloud-Dienst leicht upgraden, wenn die Website größer wird und sich Ihre Anforderungen ändern. Im <a href="/develop/python/">Python Developer Center</a> finden Sie Artikel zur Entwicklung des Web-Apps-Features von Azure App Service. <br />


## Projekterstellung

In Visual Studio können Sie im Dialogfeld **Neues Projekt** unter **Python** die Option **Azure-Clouddienst** auswählen.

![Dialogfeld "Neues Projekt"](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Im Assistenten für Azure-Clouddienste können Sie neue Web- und Workerrollen erstellen.

![Dialogfeld "Azure Cloud Service"](./media/cloud-services-python-ptvs/new-service-wizard.png)

Die Workerrollenvorlage enthält Codebausteine für eine Verbindung mit einem Azure-Speicherkonto oder einem Azure Service Bus.

![Cloud-Dienstlösung](./media/cloud-services-python-ptvs/worker.png)

Sie können Web- oder Workerrollen jederzeit zu einem vorhandenen Cloud-Dienst hinzufügen. Sie können vorhandene Projekte zu Ihrer Lösung hinzufügen oder neue erstellen.

![Befehl "Rolle hinzufügen"](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Ihr Cloud-Dienst kann Rollen enthalten, die in verschiedenen Sprachen implementiert wurden. Sie können beispielsweise eine Python-Webrolle mithilfe von Django in Python- oder C#-Workerrollen implementieren. Sie können mit Service Bus-Warteschlangen oder Speicherwarteschlangen einfach zwischen Ihren Rollen kommunizieren.

## Lokales Ausführen

Wenn Sie Ihr Cloud-Dienstprojekt als Startprojekt festlegen und F5 drücken, wird der Cloud-Dienst im lokalen Azure-Emulator ausgeführt.

Auch wenn PTVS den Start im Emulator unterstützt, funktioniert das Debuggen (etwa Haltepunkte) nicht.

Um Web- und Workerrollen zu debuggen, können Sie das Rollenprojekt als Startprojekt festlegen und dieses stattdessen debuggen. Sie können auch mehrere Startprojekte festlegen. Klicken Sie mit der rechten Maustaste auf die Lösung, und wählen Sie **Startprojekte festlegen** aus.

![Startprojekteigenschaften der Lösung](./media/cloud-services-python-ptvs/startup.png)

## Veröffentlichen in Azure

Klicken Sie für die Veröffentlichung mit der rechten Maustaste auf das Clouddienstprojekt in der Lösung, und wählen Sie **Veröffentlichen** aus.

![Anmeldung für Microsoft Azure-Veröffentlichung](./media/cloud-services-python-ptvs/publish-sign-in.png)

Wählen Sie auf der Einstellungsseite den Clouddienst aus, in dem Sie veröffentlichen möchten.

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

Weitere Details zur Verwendung von Azure-Diensten aus Ihren Web- und Workerrollen, etwa zur Verwendung von Azure-Speicher oder Azure Service Bus, finden Sie in folgenden Artikeln:

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
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Cloud Service-Projekte]: http://go.microsoft.com/fwlink/?LinkId=624028
[Python Tools 2.2 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-Tools für VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191

<!---HONumber=September15_HO1-->
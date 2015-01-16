<properties urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Bereitstellen von Azure-Webaufträgen auf Azure-Websites" metaKeywords="Azure-Websites, WebJobs, Hintergrundaufgaben" description="Erfahren Sie, wie Sie Azure WebJobs mithilfe von Visual Studio an Azure-Websites bereitstellen." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="11/12/2014" ms.author="tdykstra" />

# Bereitstellen von Azure-Webaufträgen auf Azure-Websites

Unter diesem Thema wird erklärt, wie mithilfe von Visual Studio ein Konsolenanwendungsprojekt als [Azure-Webauftrag](http://go.microsoft.com/fwlink/?LinkId=390226) auf einer Azure-Website bereitgestellt wird. Eine Alternative zur Bereitstellung von Webaufträgen stellt das Azure-Verwaltungsportal dar. Weitere Informationen zu dieser Methode finden Sie unter [Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites](/en-us/documentation/articles/web-sites-create-web-jobs/).

## Inhaltsverzeichnis

- [Übersicht](#overview)
- [Voraussetzungen](#prerequisites)
- [Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt](#convert)
- [Erstellen eines neuen webauftragsfähigen Projekts](#create)
- [Das Dialogfeld "Azure-Webauftrag hinzufügen"](#configure)
- [Die Datei "webjob-publish-settings.json"](#publishsettings)
- [Die Datei "webjobs-list.json"](#webjobslist)
- [Bereitstellen eines Webauftragsprojekts](#deploy)
- [Nächste Schritte](#next-steps)

## <a id="overview"></a>Übersicht

Wenn mithilfe von Visual Studio ein webauftragsfähiges Konsolenanwendungsprojekt bereitgestellt wird, werden zwei Aufgaben ausgeführt:

* Laufzeitdateien werden in den entsprechenden Ordner der Azure-Website (*App_Data/jobs/continuous* für fortlaufende Webaufträge, *App_Data/jobs/triggered* für geplante und bedarfsabhängige Webaufträge) kopiert.
* [Azure Scheduler-Aufträge](#scheduler) für Webaufträge, die für die Ausführung zu bestimmten Zeiten geplant sind, werden eingerichtet. (Dies ist für fortlaufende Webaufträge nicht erforderlich.)

Einem webauftragsfähigen Projekt werden die folgenden Elemente hinzugefügt:

* Das NuGet-Paket [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)
* Die Datei [webjob-publish-settings.json](#publishsettings) mit Bereitstellungs- und Zeitplanungseinstellungen 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Sie können diese Elemente einem vorhandenen Konsolenanwendungsprojekt hinzufügen oder eine Vorlage nutzen, um ein neues webauftragsfähiges Konsolenanwendungsprojekt zu erstellen. 

Sie können ein Projekt als eigenständigen Webauftrag bereitstellen oder es mit einem Webprojekt verknüpfen, sodass es automatisch bereitgestellt wird, wenn Sie das Webprojekt bereitstellen. Zum Verknüpfen von Projekten fügt Visual Studio den Namen des webauftragsfähigen Projekts der Datei [webjobs-list.json](#webjobslist) im Webprojekt hinzu.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Voraussetzungen

WebJobs-Bereitstellungsfeatures stehen in Visual Studio 2013 zur Verfügung, wenn Sie die Azure SDK-Version 2.4 oder höher installieren:

* [Azure SDK für Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Die Funktionen zur Bereitstellung von Webaufträgen sind ebenfalls in [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) und höher enthalten.

## <a id="convert"></a>Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt

Sie haben zwei Möglichkeiten:

* [Aktivieren der automatischen Bereitstellung mit einem Webprojekt](#convertlink).

	Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt so, dass es automatisch als Webauftrag bereitgestellt wird, wenn Sie ein Webprojekt bereitstellen. Wählen Sie diese Option, wenn Sie Ihren Webauftrag auf derselben Website ausführen möchten, auf der die dazugehörige Webanwendung ausgeführt wird.

* [Ermöglichen der Bereitstellung ohne Webprojekt](#convertnolink).

	Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung auf der Website ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

### <a id="convertlink"></a> Ermöglichen der automatischen Bereitstellung von Webaufträgen mit einem Webprojekt
  
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt. Klicken Sie auf 1. Hinzufügen** und danach auf **Vorhandenes Projekt als Azure-Webauftrag**.

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.

1. Wählen Sie in der Dropdownliste 1. Projektname** das Konsolenanwendungsprojekt aus, das als Webauftrag hinzugefügt werden soll.

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure), und klicken Sie dann auf **OK**. 

### <a id="convertnolink"></a> Ermöglichen der Bereitstellung von Webaufträgen ohne Webprojekt
  
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Konsolenanwendungsprojekt und dann auf 1. Hinzufügen** und dann auf **Als Azure-Webauftrag veröffentlichen**. 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird mit dem im Feld **Projektname** ausgewählten Projekt angezeigt.

2.  Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure), und klicken Sie dann auf **OK**.

	Der Assistent **Web veröffentlichen** wird geöffnet.  Wenn Sie keine sofortige Veröffentlichung wünschen, schließen Sie den Assistenten. Die eingegebenen Einstellungen werden für den Zeitpunkt der gewünschten [Bereitstellung des Projekts](#deploy) gespeichert.

## <a id="create"></a>Erstellen eines neuen webauftragsfähigen Projekts

Zum Erstellen eines neuen webauftragsfähigen Projekts können Sie die Vorlage für Konsolenanwendungsprojekte verwenden und die Bereitstellung von Webaufträgen gemäß den Erläuterungen im [vorherigen Abschnitt](#convert) ermöglichen. Alternativ können Sie die Webauftragsvorlage "new-project" nutzen:

* [Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag](#createnolink)

	Erstellen Sie ein Projekt, und konfigurieren Sie es für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung auf der Website ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

* [Verwenden der Webauftragsvorlage "new-project" für ein mit einem Webprojekt verknüpftes Webprojekt](#createlink)

	Erstellen Sie ein Projekt, das für eine automatische Bereitstellung als Webauftrag konfiguriert ist, wenn in derselben Projektmappe ein Webprojekt bereitgestellt wird. Wählen Sie diese Option, wenn Sie Ihren Webauftrag auf derselben Website ausführen möchten, auf der die dazugehörige Webanwendung ausgeführt wird.

In der SDK-Version 2.4 bietet die Webauftragsvorlage "new-project" keine wesentliche Erleichterung im Vergleich zum Erstellen eines Konsolenanwendungsprojekts und Aktivieren der Bereitstellung von Webaufträgen. Künftig wird die Webauftragsvorlage "new-project" die [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)-Bereitstellung vereinfachen, da sie automatisch die entsprechenden WebJobs SDK NuGet-Pakete installiert. Bis dahin können Sie ein Projekt für die Verwendung des WebJobs SDK konfigurieren, indem Sie die Pakete manuell installieren (Anweisungen finden Sie im [Lernprogramm zum WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). 


### <a id="createnolink"></a> Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag
  
1. Klicken Sie auf 1. Datei** > **Neues Projekt** und anschließend im Dialogfeld **Neues Projekt** auf **Cloud** > **Microsoft Azure-Webauftrag**.

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Befolgen Sie die zuvor gezeigten Anweisungen, um [das Konsolenanwendungsprojekt als unabhängiges Webauftragsprojekt zu erstellen](#convertnolink).

### <a id="createlink"></a> Verwenden der Webauftragsvorlage "new-project" für ein mit einem Webprojekt verknüpftes Webprojekt

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt. Klicken Sie auf 1. Hinzufügen** und danach auf **Neues Azure-Webauftragsprojekt**.

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.

2. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure), und klicken Sie dann auf **OK**.

## <a id="configure"></a>Das Dialogfeld "Azure-Webauftrag hinzufügen"

Im Dialogfeld **Azure-Webauftrag hinzufügen** können Sie den Namen des Webauftrags eingeben und Einstellungen für Ihren Webauftrag planen. 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Die Felder in diesem Dialogfeld entsprechen den Feldern im Dialogfeld **Neuer Auftrag** im Azure-Verwaltungsportal. Weitere Informationen finden Sie unter [Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites](/en-us/documentation/articles/web-sites-create-web-jobs/).

Für geplante Webaufträge (nicht für fortlaufende Webaufträge) erstellt Visual Studio eine [Azure Scheduler](/en-us/services/scheduler/)-Auftragssammlung, sofern noch nicht vorhanden, und einen Auftrag in der Sammlung:

* Die Azure Scheduler-Auftragssammlung heißt *WebJobs-{Regionsname}*, wobei *{Regionsname}* auf die Region verweist, in der die Website gehostet wird. Beispiel: WebJobs-WestUS.
* Der Scheduler-Auftrag heißt *{Websitename}-{Webauftragsname}*. Beispiel: MeineWebsite-MeinWebauftrag. 
 
>[WACOM.NOTE]
> 
>* Informationen zur Befehlszeilenbereitstellung finden Sie unter [Enabling Command-line or Continuous Delivery of Azure WebJobs](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* Wenn Sie einen **periodischen Auftrag** konfigurieren und die Wiederholungsfrequenz auf einige wenige Minuten festlegen, ist der Azure Scheduler-Dienst nicht kostenlos. Andere Perioden (Stunden, Tage usw.) sind kostenlos.
>*Wenn Sie einen Webauftrag bereitstellen und den Ausführungsmodus später von fortlaufend in nicht fortlaufend ändern oder umgekehrt, erstellt Visual Studio bei der erneuten Bereitstellung einen Webauftrag in Azure. Wenn Sie andere Zeitplaneinstellungen ändern, ohne den Ausführungsmodus zu wechseln, oder zwischen "Geplant" und "Bedarfsgesteuert" wechseln, aktualisiert Visual Studio den vorhandenen Auftrag, ohne einen neuen zu erstellen.

## <a id="publishsettings"></a>webjob-publish-settings.json

Wenn Sie eine Konsolenanwendung für die Bereitstellung von Webaufträgen konfigurieren, installiert Visual Studio das NuGet-Paket [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) 
und speichert die Zeitplaninformationen in der Datei *webjob-publish-settings.json* im Ordner *Eigenschaften* des Webauftragsprojekts. Hier ist ein Beispiel dieser Datei:

		{
		  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
		  "webJobName": "WebJob1",
		  "startTime": "2014-06-23T00:00:00-08:00",
		  "endTime": "2014-06-27T00:00:00-08:00",
		  "jobRecurrenceFrequency": "Minute",
		  "interval": 5,
		  "runMode": "Scheduled"
		}

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) gespeichert und kann dort angezeigt werden.  

>[WACOM.NOTE]
>
>* Wenn Sie einen **periodischen Auftrag** konfigurieren und die Wiederholungsfrequenz auf einige wenige Minuten festlegen, ist der Azure Scheduler-Dienst nicht kostenlos. Andere Perioden (Stunden, Tage usw.) sind kostenlos.

## <a id="webjobslist"></a>webjobs-list.json

Wenn Sie ein webauftragsfähiges Projekt mit einem Webprojekt verknüpfen, speichert Visual Studio den Namen des Webauftragsprojekts in der Datei *webjobs-list.json* im Ordner *Eigenschaften* des Webprojekts. Die Liste kann mehrere Webauftragsprojekte umfassen (siehe das folgende Beispiel):

		{
		  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
		  "WebJobs": [
		    {
		      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
		    },
		    {
		      "filePath": "../WebJob1/WebJob1.csproj"
		    }
		  ]
		}

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) gespeichert und kann dort angezeigt werden.
  
## <a id="deploy"></a>Bereitstellen eines Webauftragsprojekts

Ein Webauftragsprojekt, das Sie mit einem Webprojekt verknüpft haben, wird automatisch mit dem Webprojekt bereitstellt. Weitere Informationen zur Bereitstellung von Webprojekten finden Sie unter [Bereitstellen einer Azure-Website](/en-us/documentation/articles/websites-dotnet-deploy/).

Klicken Sie zum Bereitstellen eines Webauftragsprojekts im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie dann auf **Als Azure-Webauftrag veröffentlichen**. 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Bei einem unabhängigen Webauftrag wird derselbe Assistent **Web veröffentlichen** wie bei Webprojekten angezeigt, wobei allerdings weniger Einstellungen geändert werden können.

## <a id="nextsteps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Azure-Webaufträgen mit Visual Studio und mithilfe eines fortlaufenden Bereitstellungsprozesses finden Sie unter [Azure WebJobs - Empfohlene Ressourcen - Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).

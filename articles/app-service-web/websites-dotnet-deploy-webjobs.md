<properties 
	pageTitle="Bereitstellen von Webaufträgen mit Visual Studio" 
	description="Erfahren Sie, wie Sie Azure-Webaufträge mithilfe von Visual Studio in Azure App Service-Web-Apps bereitstellen." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>


<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="06/29/2015" 
	ms.author="tdykstra"/>


# Bereitstellen von Webaufträgen mit Visual Studio

## Übersicht

In diesem Thema wird erklärt, wie mithilfe von Visual Studio ein Konsolenanwendungsprojekt in einer Web-App in [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) als [Azure-Webauftrag](http://go.microsoft.com/fwlink/?LinkId=390226) bereitgestellt wird. Weitere Informationen zum Bereitstellen von Webaufträgen mit dem [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) finden Sie unter [Ausführen von Hintergrundaufgaben mit Webaufträgen](web-sites-create-web-jobs.md).

Wenn mithilfe von Visual Studio ein webauftragsfähiges Konsolenanwendungsprojekt bereitgestellt wird, werden zwei Aufgaben ausgeführt:

* Laufzeitdateien werden in den entsprechenden Ordner der Web-App (*App\_Data/jobs/continuous* für fortlaufende Webaufträge, *App\_Data/jobs/triggered* für geplante und bedarfsabhängige Webaufträge) kopiert.
* [Azure Scheduler-Aufträge](#scheduler) für Webaufträge, die für die Ausführung zu bestimmten Zeiten geplant sind, werden eingerichtet. (Dies ist für fortlaufende Webaufträge nicht erforderlich.)

Einem webauftragsfähigen Projekt werden die folgenden Elemente hinzugefügt:

* Das NuGet-Paket [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)
* Die Datei [webjob-publish-settings.json](#publishsettings) mit Bereitstellungs- und Zeitplanungseinstellungen 

![Abbildung, die zeigt, was einer Konsolenanwendung zum Ermöglichen der Bereitstellung als Webauftrag hinzugefügt wird](./media/websites-dotnet-deploy-webjobs/convert.png)

Sie können diese Elemente einem vorhandenen Konsolenanwendungsprojekt hinzufügen oder eine Vorlage nutzen, um ein neues webauftragsfähiges Konsolenanwendungsprojekt zu erstellen.

Sie können ein Projekt als eigenständigen Webauftrag bereitstellen oder es mit einem Webprojekt verknüpfen, sodass es automatisch bereitgestellt wird, wenn Sie das Webprojekt bereitstellen. Zum Verknüpfen von Projekten fügt Visual Studio den Namen des webauftragsfähigen Projekts der Datei [webjobs-list.json](#webjobslist) im Webprojekt hinzu.

![Abbildung der Verknüpfung eines Webauftragsprojekts mit einem Webprojekt](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Voraussetzungen

WebJobs-Bereitstellungsfeatures stehen in Visual Studio 2013 zur Verfügung, wenn Sie die Azure SDK-Version 2.4 oder höher installieren:

* [Azure SDK für Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Die Funktionen zur Bereitstellung von Webaufträgen sind ebenfalls in [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) und höher enthalten.

## <a id="convert"></a>Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt

Sie haben zwei Möglichkeiten:

* [Aktivieren der automatischen Bereitstellung mit einem Webprojekt](#convertlink).

	Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt so, dass es automatisch als Webauftrag bereitgestellt wird, wenn Sie ein Webprojekt bereitstellen. Wählen Sie diese Option, wenn Sie Ihren Webauftrag in derselben Web-App ausführen möchten, in der die dazugehörige Webanwendung ausgeführt wird.

* [Ermöglichen der Bereitstellung ohne Webprojekt](#convertnolink).

	Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung in der Web-App ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

### <a id="convertlink"></a> Aktivieren der automatischen Bereitstellung von Webaufträgen mit einem Webprojekt
  
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt. Klicken Sie auf **Hinzufügen** und danach auf **Vorhandenes Projekt als Azure-Webauftrag**.

	![Vorhandenes Projekt als Azure-Webauftrag](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.

1. Wählen Sie in der Dropdownliste **Projektname** das Konsolenanwendungsprojekt aus, das als Webauftrag hinzugefügt werden soll.

	![Auswählen des Projekts im Dialogfeld "Azure-Webauftrag hinzufügen"](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure), und klicken Sie dann auf **OK**.

### <a id="convertnolink"></a> Aktivieren der Bereitstellung von Webaufträgen ohne Webprojekt
  
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Konsolenanwendungsprojekt und dann auf **Hinzufügen** und dann auf **Als Azure-Webauftrag veröffentlichen**. 

	![Als Azure-Webauftrag veröffentlichen](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird mit dem im Feld **Projektname** ausgewählten Projekt angezeigt.

2.  Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure), und klicken Sie dann auf **OK**.

	Der Assistent **Web veröffentlichen** wird geöffnet. Wenn Sie keine sofortige Veröffentlichung wünschen, schließen Sie den Assistenten. Die eingegebenen Einstellungen werden für den Zeitpunkt der gewünschten [Bereitstellung des Projekts](#deploy) gespeichert.

## <a id="create"></a>Erstellen eines neuen webauftragsfähigen Projekts

Zum Erstellen eines neuen webauftragsfähigen Projekts können Sie die Vorlage für Konsolenanwendungsprojekte verwenden und die Bereitstellung von Webaufträgen gemäß den Erläuterungen im [vorherigen Abschnitt](#convert) ermöglichen. Alternativ können Sie die Webauftragsvorlage "new-project" nutzen:

* [Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag](#createnolink)

	Erstellen Sie ein Projekt, und konfigurieren Sie es für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung in der Web-App ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

* [Verwenden der Webauftragsvorlage "new-project" für einen mit einem Webprojekt verknüpften Webauftrag](#createlink)

	Erstellen Sie ein Projekt, das für eine automatische Bereitstellung als Webauftrag konfiguriert ist, wenn in derselben Projektmappe ein Webprojekt bereitgestellt wird. Wählen Sie diese Option, wenn Sie Ihren Webauftrag in derselben Web-App ausführen möchten, in der die dazugehörige Webanwendung ausgeführt wird.

In der SDK-Version 2.4 bietet die Webauftragsvorlage "new-project" keine wesentliche Erleichterung im Vergleich zum Erstellen eines Konsolenanwendungsprojekts und Aktivieren der Bereitstellung von Webaufträgen. Künftig wird die Webauftragsvorlage "new-project" die [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)-Bereitstellung vereinfachen, da sie automatisch die entsprechenden WebJobs SDK NuGet-Pakete installiert. Bis dahin können Sie ein Projekt für die Verwendung des WebJobs SDK konfigurieren, indem Sie die Pakete manuell installieren (Anweisungen finden Sie im [Lernprogramm zum WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs).


### <a id="createnolink"></a> Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag
  
1. Klicken Sie auf **Datei** > **Neues Projekt** und anschließend im Dialogfeld **Neues Projekt** auf **Cloud** > **Microsoft Azure-Webauftrag**.

	![Dialogfeld "Neues Projekt" mit Webauftragsvorlage](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Befolgen Sie die zuvor gezeigten Anweisungen, um [das Konsolenanwendungsprojekt als unabhängiges Webauftragsprojekt zu erstellen](#convertnolink).

### <a id="createlink"></a> Verwenden der Webauftragsvorlage "new-project" für einen mit einem Webprojekt verknüpften Webauftrag

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt. Klicken Sie auf **Hinzufügen** und danach auf **Neues Azure-Webauftragsprojekt**.

	![Menüeintrag "Neues Azure-Webauftragsprojekt"](./media/websites-dotnet-deploy-webjobs/nawj.png)

	Das Dialogfeld [Azure-Webauftrag hinzufügen](#configure) wird angezeigt.

2. Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen](#configure), und klicken Sie dann auf **OK**.

## <a id="configure"></a>Das Dialogfeld "Azure-Webauftrag hinzufügen"

Im Dialogfeld **Azure-Webauftrag hinzufügen** können Sie den Namen des Webauftrags eingeben und Einstellungen für Ihren Webauftrag planen.

![Dialogfeld "Azure-Webauftrag hinzufügen"](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Die Felder in diesem Dialogfeld entsprechen den Feldern im Dialogfeld **Neuer Auftrag** im Azure-Verwaltungsportal. Weitere Informationen finden Sie unter [Ausführen von Hintergrundaufgaben mit Webaufträgen](web-sites-create-web-jobs.md).

Für geplante Webaufträge (nicht für fortlaufende Webaufträge) erstellt Visual Studio eine [Azure Scheduler](/services/scheduler/)-Auftragssammlung, sofern noch nicht vorhanden, und einen Auftrag in der Sammlung:

* Die Azure Scheduler-Auftragssammlung heißt *WebJobs-{Regionsname}*, wobei *{Regionsname}* auf die Region verweist, in der die Web-App gehostet wird. Beispiel: Webaufträge WestUS.
* Der Scheduler-Auftrag heißt *{Web-App-Name}-{Webauftragsname}*. Beispiel: MeineWebApp-MeinWebauftrag. 
 
>[AZURE.NOTE]
> 
>* Informationen zur Befehlszeilenbereitstellung finden Sie unter [Aktivieren der befehlszeilengesteuerten oder kontinuierlichen Bereitstellung von Azure-Webaufträgen](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* Wenn Sie einen **periodischen Auftrag** konfigurieren und die Wiederholungsfrequenz auf einige wenige Minuten festlegen, ist der Azure Scheduler-Dienst nicht kostenlos. Andere Perioden (Stunden, Tage usw.) sind kostenlos.
>* Wenn Sie einen Webauftrag bereitstellen und dann entscheiden, den Typ des Webauftrags zu ändern und diesen erneut bereitzustellen, müssen Sie die Datei "webjobs-publish-settings.json" löschen. Dies veranlasst Visual Studio, die Optionen für die Veröffentlichung erneut anzeigen, sodass Sie den Typ des Webauftrags ändern können.
>* Wenn Sie einen Webauftrag bereitstellen und den Ausführungsmodus später von fortlaufend in nicht fortlaufend ändern oder umgekehrt, erstellt Visual Studio bei der erneuten Bereitstellung einen Webauftrag in Azure. Wenn Sie andere Zeitplaneinstellungen ändern, ohne den Ausführungsmodus zu wechseln, oder zwischen "Geplant" und "Bedarfsgesteuert" wechseln, aktualisiert Visual Studio den vorhandenen Auftrag, ohne einen neuen zu erstellen.

## <a id="publishsettings"></a>webjob-publish-settings.json

Wenn Sie eine Konsolenanwendung für die Bereitstellung von Webaufträgen konfigurieren, installiert Visual Studio das NuGet-Paket [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) und speichert Zeitplanungsinformationen in der Datei *webjob-publish-settings.json* im Ordner *Eigenschaften* des Webauftragsprojekts. Hier ist ein Beispiel dieser Datei:

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

>[AZURE.NOTE]
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

Ein Webauftragsprojekt, das Sie mit einem Webprojekt verknüpft haben, wird automatisch mit dem Webprojekt bereitstellt. Informationen zur Bereitstellung von Webprojekten finden Sie unter [Bereitstellen in Web-Apps](web-sites-deploy.md).

Klicken Sie zum Bereitstellen eines Webauftragsprojekts im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt. Klicken Sie dann auf **Als Azure-Webauftrag veröffentlichen**.

![Als Azure-Webauftrag veröffentlichen](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Bei einem unabhängigen Webauftrag wird derselbe Assistent **Web veröffentlichen** wie bei Webprojekten angezeigt, wobei allerdings weniger Einstellungen geändert werden können.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## <a id="nextsteps"></a>Nächste Schritte

In diesem Artikel wird erklärt, wie WebJobs mit Visual Studio bereitgestellt wird. Informationen zum Bereitstellen von Azure WebJobs mit Visual Studio und mithilfe eines fortlaufenden Bereitstellungsprozesses finden Sie unter [Azure WebJobs – Empfohlene Ressourcen – Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Azure-Portals gegenüber dem Azure-Vorschauportal finden Sie unter [Referenz zur Navigation im Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=August15_HO6-->
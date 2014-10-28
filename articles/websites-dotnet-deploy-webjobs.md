<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Bereitstellen von Azure-Webaufträgen auf Azure-Websites

Unter diesem Thema wird erklärt, wie mithilfe von Visual Studio ein Konsolenanwendungsprojekt als [Azure-Webauftrag][Azure-Webauftrag] auf einer Azure-Website bereitgestellt wird. Eine Alternative zur Bereitstellung von Webaufträgen stellt das Azure-Verwaltungsportal dar. Weitere Informationen zu dieser Methode finden Sie unter [Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites][Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites].

## Inhaltsverzeichnis

-   [Übersicht][Übersicht]
-   [Voraussetzungen][Voraussetzungen]
-   [Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt][Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt]
-   [Erstellen eines neuen webauftragsfähigen Projekts][Erstellen eines neuen webauftragsfähigen Projekts]
-   [Das Dialogfeld "Azure-Webauftrag hinzufügen"][Das Dialogfeld "Azure-Webauftrag hinzufügen"]
-   [Die Datei "webjob-publish-settings.json"][Die Datei "webjob-publish-settings.json"]
-   [Die Datei "webjobs-list.json"][Die Datei "webjobs-list.json"]
-   [Bereitstellen eines Webauftragsprojekts][Bereitstellen eines Webauftragsprojekts]
-   [Nächste Schritte][Nächste Schritte]

## <span id="overview"></span></a>Übersicht

Wenn mithilfe von Visual Studio ein webauftragsfähiges Konsolenanwendungsprojekt bereitgestellt wird, werden zwei Aufgaben ausgeführt:

-   Laufzeitdateien werden in den entsprechenden Ordner der Azure-Website (*App\_Data/jobs/continuous* für fortlaufende Webaufträge, *App\_Data/jobs/triggered* für geplante und bedarfsabhängige Webaufträge) kopiert.
-   [Azure Scheduler-Aufträge][Azure Scheduler-Aufträge] für Webaufträge, die für die Ausführung zu bestimmten Zeiten geplant sind, werden eingerichtet. (Dies ist für fortlaufende Webaufträge nicht erforderlich.)

Einem webauftragsfähigen Projekt werden die folgenden Elemente hinzugefügt:

-   Das NuGet-Paket [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish]
-   Die Datei [webjob-publish-settings.json][Die Datei "webjob-publish-settings.json"] mit Bereitstellungs- und Zeitplanungseinstellungen

![Abbildung, die zeigt, was einer Konsolenanwendung zum Ermöglichen der Bereitstellung als Webauftrag hinzugefügt wird][Abbildung, die zeigt, was einer Konsolenanwendung zum Ermöglichen der Bereitstellung als Webauftrag hinzugefügt wird]

Sie können diese Elemente einem vorhandenen Konsolenanwendungsprojekt hinzufügen oder eine Vorlage nutzen, um ein neues webauftragsfähiges Konsolenanwendungsprojekt zu erstellen.

Sie können ein Projekt als eigenständigen Webauftrag bereitstellen oder es mit einem Webprojekt verknüpfen, sodass es automatisch bereitgestellt wird, wenn Sie das Webprojekt bereitstellen. Zum Verknüpfen von Projekten fügt Visual Studio den Namen des webauftragsfähigen Projekts der Datei [webjobs-list.json][Die Datei "webjobs-list.json"] im Webprojekt hinzu.

![Abbildung der Verknüpfung eines Webauftragsprojekts mit einem Webprojekt][Abbildung der Verknüpfung eines Webauftragsprojekts mit einem Webprojekt]

## Voraussetzungen

WebJobs-Bereitstellungsfeatures stehen in Visual Studio 2013 zur Verfügung, wenn Sie die Azure SDK-Version 2.4 oder höher installieren:

-   [Azure SDK für Visual Studio 2013][Azure SDK für Visual Studio 2013].

Die Features für die Bereitstellung von Webaufträgen sind auch in [Update 3][Update 3] enthalten.

## <span id="convert"></span></a>Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt

Sie haben zwei Möglichkeiten:

-   [Aktivieren der automatischen Bereitstellung mit einem Webprojekt][Aktivieren der automatischen Bereitstellung mit einem Webprojekt].

    Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt so, dass es automatisch als Webauftrag bereitgestellt wird, wenn Sie ein Webprojekt bereitstellen. Wählen Sie diese Option, wenn Sie Ihren Webauftrag auf derselben Website ausführen möchten, auf der die dazugehörige Webanwendung ausgeführt wird.

-   [Ermöglichen der Bereitstellung ohne Webprojekt][Ermöglichen der Bereitstellung ohne Webprojekt].

    Konfigurieren Sie ein vorhandenes Konsolenanwendungsprojekt für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung auf der Website ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

### <span id="convertlink"></span></a> Ermöglichen der automatischen Bereitstellung von Webaufträgen mit einem Webprojekt

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt. Klicken Sie auf **Hinzufügen** und danach auf **Vorhandenes Projekt als Azure-Webauftrag**.

    ![Vorhandenes Projekt als Azure-Webauftrag][Vorhandenes Projekt als Azure-Webauftrag]

    Das Dialogfeld [Azure-Webauftrag hinzufügen][Das Dialogfeld "Azure-Webauftrag hinzufügen"] wird angezeigt.

2.  Wählen Sie in der Dropdownliste **Projektname** das Konsolenanwendungsprojekt aus, das als Webauftrag hinzugefügt werden soll.

    ![Auswählen des Projekts im Dialogfeld "Azure-Webauftrag hinzufügen"][Auswählen des Projekts im Dialogfeld "Azure-Webauftrag hinzufügen"]

3.  Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen][Das Dialogfeld "Azure-Webauftrag hinzufügen"], und klicken Sie dann auf **OK**.

### <span id="convertnolink"></span></a> Ermöglichen der Bereitstellung von Webaufträgen ohne Webprojekt

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Konsolenanwendungsprojekt und dann auf **Hinzufügen** und dann auf **Als Azure-Webauftrag veröffentlichen**.

    ![Als Azure-Webauftrag veröffentlichen][Als Azure-Webauftrag veröffentlichen]

    Das Dialogfeld [Azure-Webauftrag hinzufügen][Das Dialogfeld "Azure-Webauftrag hinzufügen"] wird mit dem im Feld **Projektname** ausgewählten Projekt angezeigt.

2.  Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen][Das Dialogfeld "Azure-Webauftrag hinzufügen"], und klicken Sie dann auf **OK**.

    Der Assistent **Web veröffentlichen** wird geöffnet. Wenn Sie keine sofortige Veröffentlichung wünschen, schließen Sie den Assistenten. Die eingegebenen Einstellungen werden für den Zeitpunkt der gewünschten [Bereitstellung des Projekts][Bereitstellen eines Webauftragsprojekts] gespeichert.

## <span id="create"></span></a>Erstellen eines neuen webauftragsfähigen Projekts

Zum Erstellen eines neuen webauftragsfähigen Projekts können Sie die Vorlage für Konsolenanwendungsprojekte verwenden und die Bereitstellung von Webaufträgen gemäß den Erläuterungen im [vorherigen Abschnitt][Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt] ermöglichen. Alternativ können Sie die Webauftragsvorlage "new-project" nutzen:

-   [Verwenden der Webauftragsvorlage "new-project" für ein mit einem Webprojekt verknüpftes Webprojekt][Verwenden der Webauftragsvorlage "new-project" für ein mit einem Webprojekt verknüpftes Webprojekt]

    Erstellen Sie ein Projekt, das für eine automatische Bereitstellung als Webauftrag konfiguriert ist, wenn in derselben Projektmappe ein Webprojekt bereitgestellt wird. Wählen Sie diese Option, wenn Sie Ihren Webauftrag auf derselben Website ausführen möchten, auf der die dazugehörige Webanwendung ausgeführt wird.

-   [Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag][Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag]

    Erstellen Sie ein Projekt, und konfigurieren Sie es für die Bereitstellung als eigenständiger Webauftrag ohne Verknüpfung mit einem Webprojekt. Wählen Sie diese Option, wenn Sie einen Webauftrag eigenständig ausführen möchten, ohne dass eine Webanwendung auf der Website ausgeführt wird. Dies empfiehlt sich, wenn Sie Ihre Webauftragsressourcen unabhängig von Ihren Webanwendungsressourcen skalieren möchten.

In der SDK-Version 2.4 bietet die Webauftragsvorlage "new-project" keine wesentliche Erleichterung im Vergleich zum Erstellen eines Konsolenanwendungsprojekts und Aktivieren der Bereitstellung von Webaufträgen. Künftig wird die Webauftragsvorlage "new-project" die [WebJobs SDK][WebJobs SDK]-Bereitstellung vereinfachen, da sie automatisch die entsprechenden WebJobs SDK NuGet-Pakete installiert. Bis dahin können Sie ein Projekt für die Verwendung des WebJobs SDK konfigurieren, indem Sie die Pakete manuell installieren (Anweisungen finden Sie im [Lernprogramm zum WebJobs SDK][WebJobs SDK].

### <span id="createlink"></span></a> Verwenden der Webauftragsvorlage "new-project" für ein mit einem Webprojekt verknüpftes Webprojekt

-   Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt. Klicken Sie auf **Hinzufügen** und danach auf **Neues Azure-Webauftragsprojekt**.

    ![Menüeintrag "Neues Azure-Webauftragsprojekt"][Menüeintrag "Neues Azure-Webauftragsprojekt"]

    Das Dialogfeld [Azure-Webauftrag hinzufügen][Das Dialogfeld "Azure-Webauftrag hinzufügen"] wird angezeigt.

-   Vervollständigen Sie das Dialogfeld [Azure-Webauftrag hinzufügen][Das Dialogfeld "Azure-Webauftrag hinzufügen"], und klicken Sie dann auf **OK**.

### <span id="createnolink"></span></a> Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag

1.  Klicken Sie auf **Datei** \> **Neues Projekt** und anschließend im Dialogfeld **Neues Projekt** auf **Cloud** \> **Microsoft Azure-Webauftrag**.

    ![Dialogfeld "Neues Projekt" mit Webauftragsvorlage][Dialogfeld "Neues Projekt" mit Webauftragsvorlage]

2.  Befolgen Sie die zuvor gezeigten Anweisungen, um [das Konsolenanwendungsprojekt als unabhängiges Webauftragsprojekt zu erstellen][Ermöglichen der Bereitstellung ohne Webprojekt].

## <span id="configure"></span></a>Das Dialogfeld "Azure-Webauftrag hinzufügen"

Im Dialogfeld **Azure-Webauftrag hinzufügen** können Sie den Namen des Webauftrags eingeben und Einstellungen für Ihren Webauftrag planen.

![Dialogfeld "Azure-Webauftrag hinzufügen"][Dialogfeld "Azure-Webauftrag hinzufügen"]

Die Felder in diesem Dialogfeld entsprechen den Feldern im Dialogfeld **Neuer Auftrag** im Azure-Verwaltungsportal. Weitere Informationen finden Sie unter [Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites][Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites].

Für geplante Webaufträge (nicht für fortlaufende Webaufträge) erstellt Visual Studio eine [Azure Scheduler][Azure Scheduler]-Auftragssammlung, sofern noch nicht vorhanden, und einen Auftrag in der Sammlung:

-   Die Azure Scheduler-Auftragssammlung heißt *WebJobs-{Regionsname}*, wobei *{Regionsname}* auf die Region verweist, in der die Website gehostet wird. Beispiel: WebJobs-WestUS.
-   Der Scheduler-Auftrag heißt *{Websitename}-{Webauftragsname}*. Beispiel: MeineWebsite-MeinWebauftrag.

> [WACOM.NOTE]
>
> -   Bei der befehlszeilengestützten Bereitstellung mit MSBuild wird die Zeitplanung für geplante Webaufträge nicht eingerichtet. Diese Features funktionieren nur bei Bereitstellung mithilfe von Visual Studio.
> -   Wenn Sie einen **periodischen Auftrag** konfigurieren und die Wiederholungsfrequenz auf einige wenige Minuten festlegen, ist der Azure Scheduler-Dienst nicht kostenlos. Andere Perioden (Stunden, Tage usw.) sind kostenlos.
> -   Wenn Sie einen Webauftrag bereitstellen und den Ausführungsmodus später von fortlaufend in nicht fortlaufend ändern oder umgekehrt, erstellt Visual Studio bei der erneuten Bereitstellung einen Webauftrag in Azure. Wenn Sie andere Zeitplaneinstellungen ändern, ohne den Ausführungsmodus zu wechseln, oder zwischen "Geplant" und "Bedarfsgesteuert" wechseln, aktualisiert Visual Studio den vorhandenen Auftrag, ohne einen neuen zu erstellen.

## <span id="publishsettings"></span></a>webjob-publish-settings.json

Wenn Sie eine Konsolenanwendungsprojekt für die Bereitstellung von Webaufträgen konfigurieren, installiert Visual Studio das NuGet-Paket [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish]
 und speichert Zeitplanungsinformationen in der Datei *webjob-publish-settings.json* im Ordner *Eigenschaften* des Webauftragsprojekts. Hier ist ein Beispiel dieser Datei:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [][]<http://schemastore.org></a> gespeichert und kann dort angezeigt werden.

> [WACOM.NOTE]
>
> -   Wenn Sie einen **periodischen Auftrag** konfigurieren und die Wiederholungsfrequenz auf einige wenige Minuten festlegen, ist der Azure Scheduler-Dienst nicht kostenlos. Andere Perioden (Stunden, Tage usw.) sind kostenlos.

## <span id="webjobslist"></span></a>webjobs-list.json

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

Sie können diese Datei direkt bearbeiten, und Visual Studio stellt IntelliSense zur Verfügung. Das Dateischema wird unter [][1]<http://schemastore.org></a> gespeichert und kann dort angezeigt werden.

## <span id="deploy"></span></a>Bereitstellen eines Webauftragsprojekts

Ein Webauftragsprojekt, das Sie mit einem Webprojekt verknüpft haben, wird automatisch mit dem Webprojekt bereitstellt. Weitere Informationen zur Bereitstellung von Webprojekten finden Sie unter [Bereitstellen einer Azure-Website][Bereitstellen einer Azure-Website].

Klicken Sie zum Bereitstellen eines Webauftragsprojekts im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt. Klicken Sie dann auf **Als Azure-Webauftrag veröffentlichen**.

![Als Azure-Webauftrag veröffentlichen][Als Azure-Webauftrag veröffentlichen]

Bei einem unabhängigen Webauftrag wird derselbe Assistent **Web veröffentlichen** wie bei Webprojekten angezeigt, wobei allerdings weniger Einstellungen geändert werden können.

## <span id="nextsteps"></span></a>Nächste Schritte

Weitere Informationen zu Azure-Webaufträgen und zum WebJobs SDK finden Sie unter [Azure WebJobs - Empfohlene Ressourcen][Azure WebJobs - Empfohlene Ressourcen].

  [Azure-Webauftrag]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Verwenden von Webaufträgen zum Ausführen von Hintergrundaufgaben auf Microsoft Azure-Websites]: /de-de/documentation/articles/web-sites-create-web-jobs/
  [Übersicht]: #overview
  [Voraussetzungen]: #prerequisites
  [Aktivieren der Bereitstellung von Webaufträgen für ein vorhandenes Konsolenanwendungsprojekt]: #convert
  [Erstellen eines neuen webauftragsfähigen Projekts]: #create
  [Das Dialogfeld "Azure-Webauftrag hinzufügen"]: #configure
  [Die Datei "webjob-publish-settings.json"]: #publishsettings
  [Die Datei "webjobs-list.json"]: #webjobslist
  [Bereitstellen eines Webauftragsprojekts]: #deploy
  [Nächste Schritte]: #next-steps
  [Azure Scheduler-Aufträge]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [Abbildung, die zeigt, was einer Konsolenanwendung zum Ermöglichen der Bereitstellung als Webauftrag hinzugefügt wird]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [Abbildung der Verknüpfung eines Webauftragsprojekts mit einem Webprojekt]: ./media/websites-dotnet-deploy-webjobs/link.png
  [Azure SDK für Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Update 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Aktivieren der automatischen Bereitstellung mit einem Webprojekt]: #convertlink
  [Ermöglichen der Bereitstellung ohne Webprojekt]: #convertnolink
  [Vorhandenes Projekt als Azure-Webauftrag]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Auswählen des Projekts im Dialogfeld "Azure-Webauftrag hinzufügen"]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Als Azure-Webauftrag veröffentlichen]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [Verwenden der Webauftragsvorlage "new-project" für ein mit einem Webprojekt verknüpftes Webprojekt]: #createlink
  [Verwenden der Webauftragsvorlage "new-project" für einen unabhängigen Webauftrag]: #createnolink
  [WebJobs SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Menüeintrag "Neues Azure-Webauftragsprojekt"]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [Dialogfeld "Neues Projekt" mit Webauftragsvorlage]: ./media/websites-dotnet-deploy-webjobs/np.png
  [Dialogfeld "Azure-Webauftrag hinzufügen"]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [Azure Scheduler]: /de-de/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [1]: http://schemastore.org/schemas/json/webjobs-list.json
  [Bereitstellen einer Azure-Website]: /de-de/documentation/articles/websites-dotnet-deploy/
  [Azure WebJobs - Empfohlene Ressourcen]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources

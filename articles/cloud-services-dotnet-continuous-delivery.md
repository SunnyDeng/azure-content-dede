<properties linkid="dev-net-common-tasks-continuous-delivery" urlDisplayName="Continuous Delivery" pageTitle="Continuous delivery for cloud services with TFS in Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous deliver PowerShell" description="Learn how to set up continuous delivery for Azure cloud apps. Code samples for MSBuild command-line statements and PowerShell scripts." metaCanonical="" services="" documentationCenter="" title="Continuous Delivery for Cloud Services in Azure" authors="ghogen" solutions="" manager="" editor="" />





Kontinuierliche Zustellung für Cloud Services in Azure
======================================================

Der in diesem Artikel beschriebene Prozess zeigt, wie die kontinuierliche Zustellung für Azure-Cloud-Apps eingerichtet wird. Dieser Prozess bietet die Möglichkeit, Pakete automatisch zu erstellen und nach jedem Einchecken von Code in Azure bereitzustellen. Der in diesem Artikel beschriebene Prozess der Paketerstellung entspricht dem Befehl "Paket" in Visual Studio, die Veröffentlichungsschritte entsprechen dem Befehl "Veröffentlichen" in Visual Studio. Der Artikel befasst sich mit den Methoden, die üblicherweise dazu dienen, einen Buildserver mit MSBuild-Befehlszeilenanweisungen und Windows PowerShell-Skripts zu erstellen. Außerdem zeigt er, wie Visual Studio Team Foundation Server - Team Build-Definitionen optional für die Verwendung von MSBuild-Befehlen und PowerShell-Skripts konfiguriert werden. Der Prozess kann an Ihre Buildumgebung und Azure-Zielumgebungen angepasst werden.

Eine bequemere Möglichkeit hierzu bietet Visual Studio Online, eine in Azure gehostete Version von Team Foundation Server. Weitere Informationen finden Sie unter [Kontinuierliche Zustellung in Azure mithilfe von Visual Studio Online][].

Bevor Sie beginnen, müssen Sie Ihre Anwendung über Visual Studio veröffentlichen. Damit wird sichergestellt, dass alle Ressourcen verfügbar und initialisiert sind, wenn Sie versuchen, den Veröffentlichungsprozess zu automatisieren.

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Den Buildserver konfigurieren](#step1)
-   [Schritt 2: Ein Paket mithilfe von MSBuild-Befehlen erstellen](#step2)
-   [Schritt 3: Ein Paket mithilfe von TFS Team Build erstellen (optional)](#step3)
-   [Schritt 4: Ein Paket mit einem PowerShell-Skript veröffentlichen](#step4)
-   [Schritt 5: Ein Paket mithilfe von TFS Team Build veröffentlichen (optional)](#step5)

Konfigurieren des BuildserversSchritt 1: Den Buildserver konfigurieren
----------------------------------------------------------------------

Bevor Sie ein Azure-Paket mithilfe von MSBuild erstellen können, müssen Sie die erforderliche Software und Tools auf dem Buildserver installieren.

Visual Studio muss nicht auf dem Buildserver installiert werden. Wenn Sie Team Foundation-Builddienst zum Verwalten des Buildservers verwenden möchten, folgen Sie den Anweisungen in der Dokumentation [Einrichten des Team Foundation-Builddiensts](http://go.microsoft.com/fwlink/p/?LinkId=239963).

1.  Installieren Sie auf dem Buildserver [.NET Framework 4](http://go.microsoft.com/fwlink/?LinkId=239538) oder [.NET Framework 4.5](http://go.microsoft.com/fwlink/?LinkId=245484), das MSBuild einschließt.
2.  Installieren Sie die [Azure-Entwicklertools](http://go.microsoft.com/fwlink/?LinkId=239600) (suchen Sie je nach dem Prozessor Ihres Buildservers nach WindowsAzureAuthoringTools-x86.msi oder WindowsAzureAuthoringTools-x64.msi).
3.  Installieren Sie die [Windows Azure-Bibliotheken][] (suchen Sie nach WindowsAzureLibsForNet-x86.msi oder WindowsAzureLibsForNet-x64.msi).
4.  Kopieren Sie die Daten Microsoft.WebApplication.targets aus einer Visual Studio-Installation auf den Buildserver. Auf einem Computer, auf dem Visual Studio installiert ist, befindet sich diese Datei im Verzeichnis C:\\Programme (x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0 für Visual Studio 2013). Kopieren Sie die Datei in das gleiche Verzeichnis auf dem Buildserver.
5.  Installieren Sie die [Azure-Tools für Visual Studio][]. Suchen Sie nach WindowsAzureTools.VS120.exe um Visual Studio 2012-Projekte zu erstellen, oder nach WindowsAzureTools.VS120.exe, um Visual Studio 2013-Projekte zu erstellen.

Erstellen eines Pakets mit MSBuildSchritt 2: Ein Paket mithilfe von MSBuild-Befehlen erstellen
----------------------------------------------------------------------------------------------

In diesem Abschnitt wird erläutert, wie ein MSBuild-Befehl konstruiert wird, der ein Azure-Paket erstellt. Führen Sie diesen Schritt auf dem Buildserver aus, um sicherzustellen, dass alles richtig konfiguriert ist und der MSBuild-Befehl wie gewünscht funktioniert. Sie können diese Befehlszeile vorhandenen Buildskripts auf dem Buildserver hinzufügen, oder Sie können die Befehlszeile in einer TFS-Builddefinition verwenden, wie im nächsten Abschnitt erläutert wird. Weitere Informationen über Befehlszeilenparameter und MSBuild finden Sie unter [MSBuild-Befehlszeilenreferenz][].

1.  Wenn Visual Studio auf dem Buildserver installiert ist, klicken Sie auf **Start**, klicken Sie auf **Alle Programme**. Suchen Sie anschließend im Ordner **Visual Studio-Tools** nach **Visual Studio-Eingabeaufforderung**, und klicken Sie darauf.

    Wenn Visual Studio auf dem Buildserver nicht installiert ist, öffnen Sie eine Eingabeaufforderung, und stellen Sie sicher, dass MSBuild.exe im Pfad verfügbar ist. MSBuild wird mit .NET Framework im Pfad

     %WINDIR%\\Microsoft.NET\\Framework\\*&lt;Version\\\>* installiert. Um z. B. MSBuild.exe der Umgebungsvariable PATH hinzuzufügen, wenn .NET Framework 4 installiert ist, geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  Navigieren Sie an der Eingabeaufforderung zu dem Ordner, der die zu erstellende Windows Azure-Projektdatei enthält.

3.  Führen Sie msbuild wie im folgenden Beispiel mit der Option "/target:Publish" aus:

        MSBuild /target:Publish

    Sie können auch eine abgekürzte Version der Option verwenden: /t:Publish. Die Option "/t:Publish" in MSBuild darf nicht mit den in Visual Studio verfügbaren Veröffentlichungsbefehlen verwechselt werden, wenn Sie das Azure-SDK installiert haben. Die Option "/t:Publish" dient nur zum Erstellen der Azure-Pakete. Die Pakete werden nicht bereitgestellt, wie dies mit den Veröffentlichungsbefehlen in Visual Studio geschieht.

    Den Projektnamen können Sie optional als MSBuild-Parameter angeben. Wird er nicht angegeben, wird das aktuelle Verzeichnis verwendet. Weitere Informationen über MSBuild-Befehlszeilenoptionen finden Sie unter [MSBuild-Befehlszeilenreferenz][1].

4.  Suchen Sie die Ausgabe. Standardmäßig erstellt dieser Befehl ein Verzeichnis unter dem Stammordner des Projekts, z. B. *&lt;ProjektVz\>*\\bin\\*&lt;Konfiguration\>*\\app.publish\\. Beim Erstellen eines Azure-Projekts werden zwei Dateien generiert, die Paketdatei selbst und die zugehörige Konfigurationsdatei:

    -   Project.cspkg
    -   ServiceConfiguration.*&lt;Zielprofil\>*.cscfg

    Standardmäßig umfasst jedes Azure-Projekt eine Dienstkonfigurationsdatei (.cscfg file) für lokale (Debugging-)Builds und eine weitere Datei für Cloud-Builds (Staging oder Produktion). Sie können Dienstkonfigurationsdateien jedoch je nach Bedarf hinzufügen oder entfernen. Wenn Sie ein Paket in Visual Studio erstellen, werden Sie gefragt, welche Dienstkonfigurationsdatei für das Paket eingeschlossen werden soll.

5.  Geben Sie die Dienstkonfigurationsdatei an. Wenn Sie ein Paket mit MSBuild erstellen, wird die lokale Dienstkonfigurationsdatei standardmäßig eingeschlossen. Wenn Sie eine andere Dienstkonfigurationsdatei einschließen möchten, legen Sie die Eigenschaft "TargetProfile" des MSBuild-Befehls wie im folgenden Beispiel fest:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Geben Sie den Speicherort der Ausgabe an. Legen Sie den Pfad mit der Option "/p:PublishDir=*&lt;Verzeichnis\\\>*\\" fest, und geben Sie dabei wie im folgenden Beispiel auch den abschließenden umgekehrten Schrägstrich an:

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    Sobald Sie eine entsprechende MSBuild-Befehlszeile zum Erstellen Ihrer Projekte und zur Kombination dieser Projekte in einem Azure-Paket konstruiert und getestet haben, können Sie diese Befehlszeile Ihren Buildskripts hinzufügen. Wenn der Buildserver benutzerdefinierte Skripts verwendet, hängt dieser Prozess von den Besonderheiten Ihres benutzerdefinierten Buildprozesses ab. Wenn Sie TFS als Buildumgebung verwenden, können Sie den Anweisungen im nächsten Schritt folgen, um den Azure-Paketbuild zu Ihrem Buildprozess hinzuzufügen.

Erstellen eines Pakets mit TFS Schritt 3: Ein Paket mithilfe von TFS Team Build erstellen (optional)
----------------------------------------------------------------------------------------------------

Wenn Sie Team Foundation Server (TFS) als Buildcontroller und den Buildserver als TFS-Buildcomputer eingerichtet haben, können Sie für Ihr Azure-Paket einen automatischen Build einrichten. Informationen zum Einrichten und Verwenden von Team Foundation Server als Buildsystem finden Sie unter [Erläuterungen zum Team Foundation-Buildsystem][]. Beim folgenden Verfahren wird insbesondere vorausgesetzt, dass Sie Ihren Buildserver wie unter [Konfigurieren eines Buildcomputers][] erläutert konfiguriert haben.

Um TFS zum Erstellen von Azure-Paketen zu konfigurieren, führen Sie die folgenden Schritte aus:

1.  Wählen Sie auf dem Entwicklungscomputer in Visual Studio im Menü "Ansicht" den Befehl **Team Explorer**, oder wählen Sie Strg+\\, Strg+M. Erweitern Sie im Team Explorer-Fenster den Knoten **Builds**, klicken Sie mit der rechten Maustaste auf **Alle Builddefinitionen**, und klicken Sie dann auf **Neue Builddefinition**:

    ![][0]

2.  Klicken Sie auf die Registerkarte **Prozess**. Wählen Sie auf der Registerkarte "Prozess" die Standardvorlage unter "Zu erstellende Dokumente", wählen Sie das Projekt, und erweitern Sie den Abschnitt **Erweitert** im Raster.

3.  Wählen Sie **MSBuild-Argumente**, und legen Sie die entsprechenden MSBuild-Befehlszeilenargumente wie in Schritt 2 oben erläutert fest. Geben Sie z. B. **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** ein, um ein Paket zu erstellen und die Paketdateien in das Verzeichnis "\\\\myserver\\drops\\" zu kopieren:

    ![][2]

    **Hinweis:** Wenn Sie die Dateien in eine öffentliche Freigabe kopieren, wird es einfacher, die Pakete manuell über Ihren Entwicklungscomputer bereitzustellen.

4.  Klicken Sie auf die Registerkarte **Trigger**, und geben Sie die gewünschten Bedingungen für die Erstellung des Pakets an. Geben Sie z. B. **Fortlaufende Integration** an, um das Paket zu erstellen, sobald ein Einchecken von Quellcode erfolgt.

5.  Wählen Sie die Registerkarte **Build-Standardwerte**, und prüfen Sie unter "Buildcontroller" den Namen des Buildservers. Wählen Sie außerdem die Option **Kopieren der Buildausgabe in den folgenden Ablageordner**, und geben Sie den gewünschten Ablageort an.

6.  Prüfen Sie, ob der Buildschritt einwandfrei funktioniert, indem Sie eine Änderung am Projekt einchecken oder indem Sie einen neuen Build in die Warteschlange stellen. Klicken Sie zum Einstellen eines neuen Builds in die Warteschlange im Team Explorer mit der rechten Maustaste auf **Alle Builddefinitionen**, und wählen Sie dann **Neuen Build in Warteschlange**.

Veröffentlichen mithilfe von PowerShellSchritt 4: Ein Paket mit einem PowerShell-Skript veröffentlichen
-------------------------------------------------------------------------------------------------------

In diesem Abschnitt wird erläutert, wie ein Windows PowerShell-Skript erstellt wird, das die Ausgabe des Cloud-App-Pakets mithilfe optionaler Parameter in Azure veröffentlicht. Dieses Skript kann im benutzerdefinierten automatischen Build nach dem Buildschritt aufgerufen werden. Es kann auch über Prozessvorlagen-Workflowaktivitäten in Visual Studio TFS Team Build aufgerufen werden.

1.  Installieren Sie die [Azure PowerShell-Cmdlets][] (Version 0.6.1 oder höher). Wählen Sie während der Cmdlet-Einrichtung die Installation als Snap-In. Diese offiziell unterstützte Version ersetzt die ältere von CodePlex angebotene Version, auch wenn diese die Versionsnummer 2.x.x haben.

2.  Starten Sie Azure PowerShell über das Startmenü. Auf diese Weise werden die Azure PowerShell-Cmdlets geladen.

3.  Prüfen Sie an der PowerShell-Eingabeaufforderung, ob die PowerShell-Cmdlets geladen sind. Geben Sie dazu den Teilbefehl "Get-Azure" ein, und drücken Sie anschließend die Tabulatortaste, um die Anweisung zu vervollständigen.

    Die verschiedenen Azure PowerShell-Befehle werden angezeigt.

4.  Prüfen Sie, ob Sie eine Verbindung mit dem Azure-Abonnement herstellen können, indem Sie die Abonnementinformationen aus der .publishsettings-Datei importieren.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    Geben Sie danach folgenden Befehl ein:

    Get-AzureSubscription

    Damit werden Informationen über Ihr Abonnement angezeigt. Prüfen Sie, ob alles richtig ist.

5.  Speichern Sie die am [Ende dieses Artikels][] bereitgestellte Skriptvorlage im Skriptordner unter c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

6.  Prüfen Sie den Parameterabschnitt des Skripts. Fügen Sie Standardwerte hinzu, oder ändern Sie diese. Diese Werte können durch Übergabe expliziter Parameter stets überschrieben werden.

7.  Stellen Sie sicher, dass in Ihrem Abonnement gültige Clouddienst- und Speicherkonten erstellt sind, die vom Veröffentlichungsskript als Ziel angesprochen werden können. Das Speicherkonto (Blobspeicher) dient dazu, das Bereitstellungspaket und die Konfigurationsdatei hochzuladen und vorübergehend zu speichern, während die Bereitstellung erstellt wird.

    -   Wenn Sie einen neuen Clouddienst erstellen möchten, können Sie dieses Skript aufrufen oder das Azure-Verwaltungsportal verwenden. Der Name des Clouddiensts wird als Präfix in einem vollqualifizierten Domänennamen verwendet und muss daher eindeutig sein.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Wenn Sie ein neues Speicherkonto erstellen möchten, können Sie dieses Skript aufrufen oder das Azure-Verwaltungsportal verwenden. Der Name des Speicherkontos wird als Präfix in einem vollqualifizierten Domänennamen verwendet und muss daher eindeutig sein. Sie können versuchen, den gleichen Namen wie für den Clouddienst zu verwenden.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  Rufen Sie das Skript direkt in Azure PowerShell auf, oder reihen Sie es in der Host-Buildautomatisierung nach dem Paketbuild ein.

    **WARNUNG:** Standardmäßig werden vorhandene Bereitstellungen, falls sie erkannt werden, von dem Skript stets gelöscht oder ersetzt. Dies ist notwendig, um die kontinuierliche Zustellung bei der Automatisierung zu ermöglichen, bei der keine Eingabeaufforderungen möglich sind.

    **Beispielszenario 1:** kontinuierliche Bereitstellung in der Stagingumgebung eines Diensts:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    In der Regel folgen danach eine Testlaufverifizierung und ein VIP-Swap. Der VIP-Swap kann über das Azure-Verwaltungsportal oder mit dem Move-Deployment-Cmdlet ausgeführt werden.

    **Beispielszenario 2:** kontinuierliche Bereitstellung in der Produktionsumgebung eines dedizierten Testdiensts

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Remotedesktop:**

    Wenn Remotedesktop in Ihrem Azure-Projekt aktiviert ist, müssen Sie zusätzlich einmalige Schritte ausführen, um sicherzustellen, dass das richtige Clouddienst-Zertifikat zu allen Clouddiensten hochgeladen wird, auf die dieses Skript zielt.

    Suchen Sie die von Ihren Rollen erwarteten Zertifikatfingerabdruckwerte. Die Fingerabdruckwerte sind im Abschnitt "Zertifikate" der Cloud-Konfigurationsdatei (d. h. ServiceConfiguration.Cloud.cscfg) sichtbar. Sie werden außerdem im Dialogfeld "Remotedesktopkonfiguration" von Visual Studio angezeigt, wenn Sie "Optionen anzeigen" wählen und das ausgewählte Zertifikat anzeigen.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Laden Sie Remotedesktopzertifikate in einem einmaligen Schritt während der Einrichtung mit dem folgenden Cmdlet-Skript hoch:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY&lt;THUMBPRINT>)
        lt;THUMBPRINT>)

    Zum Beispiel:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Alternativ können Sie die PFX-Zertifikatdatei mit privatem Schlüssel exportieren und Zertifikate über das Azure-Verwaltungsportal zu jedem Ziel-Clouddienst hochladen. Weitere Informationen hierzu finden Sie im folgenden Artikel: [http://msdn.microsoft.com/library/azure/gg443832.aspx][].

    **Bereitstellung aktualisieren im Unterschied zu Bereitstellung löschen -\> Neue Bereitstellung**

    In der Standardeinstellung aktualisiert das Skript eine Bereitstellung (\$enableDeploymentUpgrade = 1), wenn kein Parameter übergeben wird oder der Wert 1 explizit übergeben wird. Bei Einzelinstanzen bietet dies den Vorteil, dass die Bereitstellung weniger Zeit in Anspruch nimmt als eine vollständige Bereitstellung. Bei Instanzen, die hoch verfügbar sein müssen, bietet dies außerdem den Vorteil, dass einige Instanzen weiter aktiv sein können, während andere aktualisiert werden (wandernde Aktualisierungsdomäne). Außerdem wird Ihr VIP nicht gelöscht.

    Die Aktualisierung der Bereitstellung kann im Skript deaktiviert werden (\$enableDeploymentUpgrade = 0) oder durch Übergabe von -enableDeploymentUpgrade 0 als Parameter. Dadurch wird das Skriptverhalten so geändert, dass zuerst vorhandene Bereitstellungen gelöscht werden und anschließend eine neue Bereitstellung erstellt wird.

    **Warnung:** Standardmäßig werden vorhandene Bereitstellungen, falls sie erkannt werden, von dem Skript stets gelöscht oder ersetzt. Dies ist notwendig, um die kontinuierliche Zustellung bei der Automatisierung zu ermöglichen, bei der keine Eingabeaufforderungen möglich sind.

Veröffentlichen mithilfe von TFS Schritt 5: Ein Paket mithilfe von TFS Team Build veröffentlichen (optional)
------------------------------------------------------------------------------------------------------------

Mit diesem Schritt wird TFS Team Build in das in Schritt 4 erstellte Skript eingereiht, das die Veröffentlichung des Paketbuilds in Azure übernimmt. Dies hat die Änderung der von der Builddefinition verwendeten Prozessvorlage zur Folge, sodass eine Veröffentlichungsaktivität am Ende des Workflows ausgeführt wird. Die Veröffentlichungsaktivität führt den PowerShell-Befehl aus und übergibt Parameter aus dem Build. Die Ausgabe der MSBuild-Ziele und des Veröffentlichungsskripts wird in die Standard-Buildausgabe geleitet.

1.  Bearbeiten Sie die für die kontinuierliche Bereitstellung verantwortliche Builddefinition.

2.  Wählen Sie die Registerkarte **Prozess**.

3.  Erstellen Sie eine neue Prozessvorlage, indem Sie im Bereich "Prozessvorlagenauswahl" der Registerkarte auf die Schaltfläche "Neu..." klicken. Das Dialogfeld "Neue Buildprozessvorlage" wird aufgerufen. Stellen Sie sicher, dass in diesem Dialogfeld "Vorhandene XAML-Datei kopieren" ausgewählt ist, und navigieren Sie zu der zu kopierenden Prozessvorlage, wenn Sie Standardwerte ändern möchten. Geben Sie der neuen Vorlage einen Namen wie z. B. DefaultTemplateAzure.

4.  Öffnen Sie die ausgewählte Prozessvorlage zur Bearbeitung. Sie können sie direkt im Workflow-Designer oder im XAML-Editor öffnen, um die XAML-Datei zu bearbeiten.

5.  Fügen Sie die folgende Liste neuer Argumente als separate Zeilenelemente auf der Registerkarte "Argumente" des Workflow-Designers ein. Für alle Argumente muss direction=In und type=String gelten. Mit diesen Einstellungen werden Parameter aus der Builddefinition in den Workflow übernommen und anschließend zum Aufrufen des Veröffentlichungsskripts verwendet.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![](./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png)

    Die entsprechende XAML-Datei sieht folgendermaßen aus:

        <Activity _ />
          <x data-morhtml="true":Members>
            <x data-morhtml="true":Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x data-morhtml="true":Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x data-morhtml="true":Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x data-morhtml="true":Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x data-morhtml="true":Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x data-morhtml="true":Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x data-morhtml="true":Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x data-morhtml="true":Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x data-morhtml="true":Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x data-morhtml="true":Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x data-morhtml="true":Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x data-morhtml="true":Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x data-morhtml="true":Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x data-morhtml="true":Property Name="GetVersion" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x data-morhtml="true":Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x data-morhtml="true":Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x data-morhtml="true":Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="Environment" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x data-morhtml="true":Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this data-morhtml="true":Process.MSBuildArguments>

6.  Fügen Sie am Ende von "Ausführung mit Agent" eine neue Sequenz hinzu:

    1.  Fügen Sie zunächst eine Aktivität vom Typ If-Anweisung hinzu, um zu prüfen, ob eine gültige Skriptdatei vorhanden ist. Legen Sie die Bedingung auf den folgenden Wert fest:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Fügen Sie anschließend im Then-Fall der If-Anweisung eine neue Sequenzaktivität hinzu. Legen Sie den Anzeigenamen auf "Start publish" fest.

    3.  Fügen Sie bei weiterhin ausgewählter "Start publish"-Sequenz die folgende Liste neuer Variablen auf der Registerkarte "Variablen" im Workflow-Designer als separate Zeilenelemente hinzu. Für alle Variablen muss Folgendes gelten: Variable type=String und Scope=Start publish. Mit diesen Einstellungen werden Parameter aus der Builddefinition in den Workflow übernommen und anschließend zum Aufrufen des Veröffentlichungsskripts verwendet.

        -   SubscriptionDataFilePath, Typ String (Zeichenfolge)

        -   PublishScriptFilePath, Typ String (Zeichenfolge)

            ![](./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png)

    4.  Fügen Sie am Beginn der neuen Sequenz eine ConvertWorkspaceItem-Aktivität hinzu. Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation', Result='PublishScriptFilePath', Workspace='Workspace'. Mit dieser Aktivität wird der Pfad zum Veröffentlichungsskript von TFS-Serverspeicherorten (falls zutreffend) in einen standardmäßigen Pfad auf dem lokalen Datenträger konvertiert.

    5.  Fügen Sie am Ende der neuen Sequenz eine weitere ConvertWorkspaceItem-Aktivität hinzu. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Fügen Sie am Ende der neuen Sequenz eine InvokeProcess-Aktivität hinzu. Mit dieser Aktivität wird PowerShell.exe mit den von der Builddefinition übergebenen Argumenten aufgerufen.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Execute publish script'

        3.  FileName = "PowerShell"

        4.  OutputEncoding= 'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  Legen Sie im Textfeld "Standardausgabe für Handle" von InvokeProcess den Textfeldwert auf "data" fest. Dies ist eine Variable zum Speichern der Standardausgabedaten.

    8.  Fügen Sie direkt unterhalb des Abschnitts für die Standardausgabe eine WriteBuildMessage-Aktivität hinzu. Legen Sie Folgendes fest: Importance='Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' und Message='data'. Damit wird sichergestellt, dass die Standardausgabe des Skripts in die Buildausgabe geschrieben wird.

    9.  Legen Sie im Textfeld des Abschnitts "Handle Standard Error" von InvokeProcess den Textfeldwert auf "data" fest. Dies ist eine Variable zum Speichern der Standardfehlerdaten.

    10. Fügen Sie direkt unterhalb des Abschnitts für die Standardausgabe eine WriteBuildError-Aktivität hinzu. Legen Sie Message='data' fest. Damit wird sichergestellt, dass die Standardfehler des Skripts in die Buildfehlerausgabe geschrieben werden.

    Das Endergebnis der Aktivitäten zum Veröffentlichen des Workflows sieht im Designer folgendermaßen aus:

    ![](./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png)

    Das Endergebnis der Aktivitäten zum Veröffentlichen des Workflows sieht in der XAML-Datei folgendermaßen aus:

           </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If data-morhtml="true".Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence data-morhtml="true".Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap data-morhtml="true":WorkflowViewStateService.ViewState>
                      <scg data-morhtml="true":Dictionary x:TypeArguments="x:String, x:Object">
                        <x data-morhtml="true":Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa data-morhtml="true":ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa data-morhtml="true":ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa data-morhtml="true":InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa data-morhtml="true":InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction data-morhtml="true".Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa data-morhtml="true":WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa data-morhtml="true":InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction data-morhtml="true".Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa data-morhtml="true":WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa data-morhtml="true":InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa data-morhtml="true":CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  Speichern Sie den DefaultTemplateAzure-Workflow und checken Sie diese Datei ein.

8.  Wählen Sie die Prozessvorlage DefaultTemplateAzure in der Builddefinition aus. Klicken Sie auf "Aktualisieren", oder wählen Sie die Schaltfläche "Neu", wenn diese Datei in der Liste der Prozessvorlagen noch nicht angezeigt wird.

9.  Legen Sie Parametereigenschaftenwerte im Abschnitt "Sonstiges" wie folgt fest:

    1.  CloudConfigLocation = 'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
         *Dieser Wert wird abgeleitet aus: (\$PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
         *Dieser Wert wird abgeleitet aus: (\$PublishDir)(\$ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
         *Verwenden Sie hier den Namen des entsprechenden Clouddiensts*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
         *Verwenden Sie hier den Namen des entsprechenden Speicherkontos*

    7.  SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![](./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png)

10. Speichern Sie die Änderungen in der Builddefinition.

11. Stellen Sie einen Build in die Warteschlange, um sowohl den Paketbuild als auch die Paketveröffentlichung auszuführen. Wenn Sie einen Auslöser auf "Kontinuierliche Bereitstellung" eingestellt haben, wird dieses Verhalten bei jedem Einchecken ausgeführt.

### Skriptvorlage PublishCloudService.ps1

``` {data-morhtml="true"}
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )
      

function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - Keine Bereitstellung wird erkannt. Neue Bereitstellung wird erstellt. "
    }
    #auf vorhandene Bereitstellung prüfen und dann entweder aktualisieren, löschen + bereitstellen oder abbrechen entsprechend den Booleschen Variablen $alwaysDeleteExistingDeployments und $enableDeploymentUpgrade
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1 
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Vorhandene Bereitstellung aktualisieren (i.d.R. schneller, billiger, VIP wird nicht zerstört)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung in $servicename vorhanden.  Bereitstellung wird aktualisiert."
                        UpgradeDeployment
                    }
                    0  #Bereitstellung löschen, dann neu erstellen
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung in $servicename vorhanden.  Bereitstellung wird gelöscht."
                        DeleteDeployment
                        CreateNewDeployment
                        
                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - FEHLER: Bereitstellung in $servicename vorhanden.  Skriptausführung abgebrochen."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Neue Bereitstellung wird erstellt" -Status "In Bearbeitung"
    Write-Output "$(Get-Date -f $timeStampFormat) - Neue Bereitstellung wird erstellt: In Bearbeitung"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
        
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Neue Bereitstellung wird erstellt" -completed -Status "Abgeschlossen"
    Write-Output "$(Get-Date -f $timeStampFormat) - Neue Bereitstellung wird erstellt: Abgeschlossen, Bereitstellungs-ID: $completeDeploymentID"
    
    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Bereitstellung wird aktualisiert" -Status "In Bearbeitung"
    Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung wird aktualisiert: In Bearbeitung"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid
    
    write-progress -id 3 -activity "Bereitstellung wird aktualisiert" -completed -Status "Abgeschlossen"
    Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung wird aktualisiert: Abgeschlossen, Bereitstellungs-ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Bereitstellung wird gelöscht" -Status "In Bearbeitung"
    Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung wird gelöscht: In Bearbeitung"

    #WARNUNG - erzwingt stets die Löschung
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Bereitstellung wird gelöscht: Abgeschlossen" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung wird gelöscht: Abgeschlossen"
    
}

function StartInstances()
{
    write-progress -id 4 -activity "Instanzen werden gestartet" -Status "In Bearbeitung"
    Write-Output "$(Get-Date -f $timeStampFormat) - Instanzen werden gestartet: In Bearbeitung"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running') 
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count
    
    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Instanz '$instanceName' wird gestartet: $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Instanz '$instanceName' wird gestartet" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Instanz '$instanceName' wird gestartet: $instanceStatus"
        }

        $i = $i + 1
    }
    
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status 
    
    write-progress -id 4 -activity "Instanzen werden gestartet" -completed -status $optstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Instanzen werden gestartet: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }
    
    return $true
}

#PowerShell mit Azure 1.7-Modulen konfigurieren
Import-Module Azure

#PowerShell mit publishsettings für Ihr Abonnement konfigurieren
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

#restliche Umgebungsvariablen für Azure-Cmdlets festlegen
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#Haupttreiber - Fortschritt veröffentlichen und in Aktivitätsprotokoll schreiben
Write-Output "$(Get-Date -f $timeStampFormat) - Azure-Clouddienst-Bereitstellungsskript gestartet."
Write-Output "$(Get-Date -f $timeStampFormat) - Bereitstellung von $deploymentLabel für $subscriptionname mit Abonnement-ID $subscriptionid wird vorbereitet."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Clouddienst mit URL $deploymentURL erstellt."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure-Clouddienst-Bereitstellungsskript beendet."
```

[Windows Azure-Bibliotheken][]: http://go.microsoft.com/fwlink/?LinkId=257862 
[Azure-Tools für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862 
[MSBuild-Befehlszeilenreferenz]: http://msdn.microsoft.com/en-us/library/ms164311(v=VS.90).aspx 
[1]: http://msdn.microsoft.com/library/ms164311.aspx 
[Skalieren des Buildsystems]: http://msdn.microsoft.com/library/dd793166.aspx 
[Konfigurieren eines Buildcomputers]: http://msdn.microsoft.com/library/ms181712.aspx 
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png 
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png 
[Azure PowerShell-Cmdlets]: http://go.microsoft.com/fwlink/?LinkId=256262 
[die .publishsettings-Datei]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0 
[Ende dieses Artikels]: \#script


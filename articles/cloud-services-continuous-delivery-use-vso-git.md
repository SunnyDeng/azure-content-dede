<properties 
	pageTitle="Kontinuierliche Bereitstellung in Azure mit Visual Studio Online" 
	description="Hier erfahren Sie, wie Sie Ihre Teamprojekte in Visual Studio Online so konfigurieren, dass sie automatisch auf Azure-Websites oder in Azure-Cloud-Diensten erstellt und bereitgestellt werden." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>




# Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online und Git

Sie können Visual Studio Online-Teamprojekte verwenden, um ein Git-Repository für Quellcode zu hosten und dies automatisch zu erstellen und zu Azure-Websites oder -Cloud-Diensten bereitzustellen, wenn Sie ein Commit zum Repository mithilfe von Push übertragen.

Sie müssen Visual Studio 2013 und das Azure-SDK installiert haben. Wenn Sie Visual Studio 2013 nicht bereits installiert haben, laden Sie es herunter, indem Sie auf der Seite [www.visualstudio.com](http://www.visualstudio.com) auf den Link **Starten Sie kostenlos** klicken. Installieren Sie das Azure-SDK von [hier](http://go.microsoft.com/fwlink/?LinkId=239540)


> [AZURE.NOTE] Sie benötigen ein Visual Studio Online-Konto, um dieses Lernprogramm durchführen zu können.
> Sie können [kostenlos ein Visual Studio Online-Konto anlegen](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Gehen Sie folgendermaßen vor, um einen Cloud-Dienst für eine automatische Erstellung und Bereitstellung mithilfe von Visual Studio Online zu konfigurieren:

-   [Schritt 1: Erstellen eines Git-Repositorys][]

-   [Schritt 2: Erstellen eines Projekts und Übertragen mithilfe von Push an das Git-Repository.][]

-   [Schritt 3: Verbinden des Projekts mit Azure][]

-   [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung][]

-   [Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)][]

-   [Schritt 6: Ändern der Produktionsbereitstellung][]

-	[Schritt 7: Bereitstellen von einer Arbeitsverzweigung][]

<h2> <a name="step1"></a>Schritt 1: Erstellen eines Git-Repositorys</h2>


1. Wenn Sie noch kein Visual Studio Online-Konto haben, folgen Sie den Anweisungen [hier](http://go.microsoft.com/fwlink/?LinkId=397665) Wenn Sie Ihr Teamprojekt erstellen, wählen Sie Git als Quellcodeverwaltungssystem aus. Befolgen Sie die Anweisungen, um Visual Studio mit dem Teamprojekt zu verbinden.

2. Klicken Sie in Team Explorer auf den Link **Dieses Repository klonen**. 
![][3]

3. Geben Sie den Speicherort der lokalen Kopie an, und klicken Sie auf die Schaltfläche **Klonen**.
 
<h2><a name="step2"> </a>Schritt 2: Erstellen eines Projekts und Übertragen an das Repository</h2>

1. Wählen Sie in Team Explorer im Abschnitt "Projektmappen" den Link "Neu", um ein neues Projekt im lokalen Repository zu erstellen.<br/>
![][4]

2. Sie können eine Website oder einen Cloud-Dienst (Azure-Anwendung) bereitstellen, indem Sie die Schritte in dieser Anleitung ausführen.
Erstellen Sie ein neues Microsoft Azure-Cloud-Dienstprojekt oder ein neues ASP.NET MVC-Projekt. Stellen Sie sicher, dass das Projekt auf .NET Framework 4 oder 4.5 abzielt. Wenn Sie ein Cloud-Dienstprojekt erstellen, fügen Sie eine ASP.NET MVC-Webrolle und eine Workerrolle hinzu.
Wenn Sie eine Website erstellen möchten, wählen Sie die Projektvorlage ASP.NET Web Application und anschließend MVC aus. Siehe [Erste Schritte mit Azure und ASP.NET](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/)

3. Öffnen Sie das Kontextmenü für die Projektmappe, und klicken Sie auf **Commit**.<br/>
![][7]

4. Wenn Sie zum ersten Mal Git in Visual Studio Online verwenden, müssen Sie einige Informationen angeben, um sich in Git zu identifizieren. Geben Sie im Bereich **Ausstehende Änderungen** von Team Explorer Ihren Benutzernamen und Ihre E-Mail-Adresse an. Geben Sie einen Kommentar für den Commit ein, und wählen Sie **Commit**.<br/>
![][8]

5. Bitte beachten Sie beim Einchecken die Option zum Ein- oder Ausschließen bestimmter Änderungen. Wenn die gewünschten Änderungen ausgeschlossen sind, klicken Sie auf **Alle einschließen**.<br/>

6. Sie haben jetzt die Änderungen in der lokalen Kopie des Repository übermittelt. Als Nächstes synchronisieren Sie diese Änderungen beim Server. Klicken Sie auf den Link **Sync**.

<h2> <a name="step3"> </a>Schritt 3: Verbinden des Projekts mit Azure</h2>

1. Nach der Erstellung eines Git-Repository in Visual Studio Online mit Quellcode sind Sie bereit, Ihr Git-Repository mit Azure zu verbinden.  Wählen Sie im [Azure-Portal](http://manage.windowsazure.com) Ihren Cloud-Dienst oder Ihre Website, oder erstellen Sie einen neuen Cloud-Dienst oder eine neue Website, indem Sie auf das +-Symbol links unten sowie auf **Cloud-Dienst** oder **Website** und anschließend auf **Schnellerfassung** klicken.
![][9]

3. Klicken Sie für Cloud-Dienste auf den Link **Veröffentlichung einrichten mit Visual Studio Online**. Für Websites wählen Sie den Link **Bereitstellung über Quellcodeverwaltung einrichten**.<br/>
![][10]

2. Geben Sie in das Textfeld des Assistenten den Namen Ihres Visual Studio Online-Kontos ein, und klicken Sie auf den Link **Jetzt autorisieren**. Eventuell werden Sie aufgefordert, sich anzumelden.<br/>
![][11]

3. Klicken Sie im OAuth-Popupdialogfeld auf **Übernehmen**, um Azure für eine Konfiguration Ihres Teamprojekts in Visual Studio Online zu autorisieren.<br/>
![][12]

4. Wenn die Autorisierung erfolgreich verläuft, wird eine Dropdownliste Ihrer Visual Studio Online-Teamprojekte angezeigt.  Wählen Sie den Namen des Teamprojekts aus, das Sie in den vorherigen Schritten erstellt haben, und klicken Sie auf das Häkchen im Assistenten.<br/>
![][13]

Wenn Sie das nächste Mal ein Commit zum Repository übertragen, wird Visual Studio Online Ihr Projekt erstellen und in Azure bereitstellen.<br/>


<h2><a name="step4"> </a>Schritt 4: Auslösen einer erneuten Erstellung und Bereitstellung Ihres Projekts</h2>

1. Öffnen Sie in Visual Studio eine Datei, und ändern Sie sie. Ändern Sie z. B. die Datei _Layout.cshtml im Ordner Views\Shared in eine MVC-Webrolle.<br/>
![][17]

2. Bearbeiten Sie den Fußzeilentext der Website, und speichern Sie die Datei.<br/>
![][18]

3. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für den Projektmappenknoten, für den Projektknoten oder für die geänderte Datei, und wählen Sie **Commit**.<br/>

4. Geben Sie einen Kommentar ein und wählen Sie **Commit**.<br/>
![][20]

5. Klicken Sie auf den Link **Sync**.<br/>
![][38]

6. Wählen Sie den Link **Push** aus, um Ihr Commit zum Repository in Visual Studio Online zu übertragen. (Sie können auch die Schaltfläche **Sync** verwenden, um Commits zum Repository zu übertragen. Der Unterschied liegt darin, dass **Sync** auch die letzten Änderungen aus dem Repository abruft.)<br/>
![][39]

7. Klicken Sie auf die Schaltfläche "Home", um zur Startseite von Team Explorer zurückzukehren.<br/>
![][21]

8. Klicken Sie auf den Link **Builds**, um die gerade stattfindenden Builds anzuzeigen.<br/>
![][22]
<br/>
In Team Explorer ist zu sehen, dass ein Build für Ihr Einchecken ausgelöst wurde.<br/>
![][23]

9. Doppelklicken Sie auf den Namen des aktiven Builds, um ein detailliertes Protokoll des Buildverlaufs anzuzeigen.<br/>

10. Betrachten Sie während des Buildverlaufs die Builddefinition, die erstellt wurde, als Sie mit dem Assistenten eine Verknüpfung mit Azure eingerichtet haben.  Öffnen Sie das Kontextmenü für die Builddefinition, und klicken Sie auf **Builddefinition bearbeiten**.<br/>
![][25]
<br/>
Auf der Registerkarte **Trigger** sehen Sie, dass die Builddefinition bei jedem Einchecken standardmäßig auf einen Build festgelegt ist. (Für einen Cloud-Dienst erstellt Visual Studio Online Builds und stellt die Hauptverzweigung automatisch zur Stagingumgebung bereit. Sie müssen für die Bereitstellung zur Live-Website weiterhin einen Schritt manuell durchführen. Für eine Website ohne Stagingumgebung wird die Hauptverzweigung direkt in der Live-Website bereitgestellt.<br/>
![][26]
<br/>
Auf der Registerkarte **Prozess** ist zu sehen, dass die Entwicklungsumgebung auf den Namen Ihres Cloud-Diensts oder Ihrer Website festgelegt ist.<br/>
![][27]
<br/>
Geben Sie Werte für die Eigenschaften ein, wenn Sie andere Werte als die Standardwerte verwenden möchten. Die Eigenschaften für die Azure-Veröffentlichung befinden sich im Bereitstellungsabschnitt, und Sie müssen möglicherweise MSBuild-Parameter festlegen. Um eine andere Dienstkonfiguration als "Cloud" anzugeben, legen Sie zum Beispiel in einem Cloud-Dienstprojekt die MSbuild-Parameter auf /p:TargetProfile=*YourProfile* fest, wobei  *YourProfile* einer Dienstkonfigurationsdatei mit einem Namen wie "ServiceConfiguration.*YourProfile*.cscfg" entspricht.
Die folgende Tabelle zeigt die im Bereitstellungsabschnitt verfügbaren Eigenschaften:
	<table>
<tr><td><b>Eigenschaft</b></td><td><b>Standardwert</b></td></tr>
><tr><td>Nicht vertrauenswürdige Zertifikate zulassen</td><td>Wenn dies "false" ist, müssen SSL-Zertifikate von einer Stammzertifizierungsstelle signiert sein.</td></tr>
<tr><td>Upgrade zulassen</td><td>Ermöglicht einer Bereitstellung, eine vorhandene Bereitstellung zu aktualisieren, anstatt eine neue zu erstellen. Die IP-Adresse wird beibehalten.</td></tr>
><tr><td>Nicht löschen</td><td>Wenn dies "true" ist, wird keine vorhandene, nicht verbundene Bereitstellung überschrieben (Upgrade ist zulässig).</td></tr>
<tr><td>Pfad zu Bereitstellungseinstellungen</td><td>Der Pfad zu Ihrer .pubxml-Datei für eine Website, entsprechend dem Stammordner des Repositorys. Wird für Cloud-Dienste ignoriert.</td></tr>
<tr><td>Sharepoint-Bereitstellungsumgebung</td><td>Entspricht dem Dienstnamen</td></tr>
<tr><td>Microsoft Azure-Bereitstellungsumgebung</td><td>Der Name der Website oder des Cloud-Diensts</td></tr>
</table>
<br/>

11. An diesem Punkt sollte Ihr Build erfolgreich abgeschlossen sein.<br/>
![][28]

12. Wenn Sie auf den Buildnamen doppelklicken, zeigt Visual Studio eine **Buildzusammenfassung** mit allen Testergebnissen aus entsprechenden Komponententestprojekten an.<br/>
![][29]

13. Im [Azure-Portal](http://manage.windowsazure.com) können Sie die entsprechende Bereitstellung auf der Registerkarte "Bereitstellungen" anzeigen, wenn Sie die Stagingumgebung auswählen.<br/>
![][30]

14.	Wechseln Sie zur URL Ihrer Site. Wählen Sie für eine Website die Schaltfläche **Durchsuchen** im Portal. Wenn es sich um einen Cloud-Dienst handelt, wählen Sie die URL im Abschnitt **Auf einen Blick** der Seite **Dashboard** aus, auf der die Stagingumgebung angezeigt wird. Bereitstellungen aus einer fortlaufenden Integration für Cloud-Dienste werden standardmäßig in der Stagingumgebung veröffentlicht. Sie können dies ändern, indem Sie die Eigenschaft "Alternate Cloud Service Environment" auf "Produktion" stellen. Hier wird gezeigt, wo auf der Dashboard-Seite des Cloud-Diensts sich die Website-URL befindet: <br/>
![][31]
<br/>
Eine neue Browser-Registerkarte wird geöffnet, die Ihre laufende Site anzeigt.<br/>
![][32]

15.	Wenn Sie weitere Änderungen an Ihrem Projekt vornehmen, lösen Sie weitere Builds aus und sammeln mehrere Bereitstellungen. Die letzte davon wird als "Aktiv" markiert.<br/>
![][33]

<h2> <a name="step5"> </a>Schritt 5: Erneute Bereitstellung eines früheren Builds</h2>

Dieser Schritt ist optional. Wählen Sie im Verwaltungsportal eine frühere Bereitstellung aus, und klicken Sie auf **Erneut bereitstellen**, um Ihre Website auf einen früheren Eincheckvorgang zurückzusetzen. Bitte beachten Sie, dass dadurch in TFS ein neuer Build ausgelöst und in Ihrem Bereitstellungsverlauf ein neuer Eintrag erstellt wird.<br/>
![][34]

<h2> <a name="step6"> </a>Schritt 6: Ändern der Produktionsbereitstellung</h2>

 Wenn Sie bereit sind, können Sie die Stagingumgebung in die Produktionsumgebung überführen, indem Sie im Verwaltungsportal auf die Schaltfläche **Swap** klicken. Die neu bereitgestellte Stagingumgebung wird in eine Produktionsumgebung geändert, und die vorherige Produktionsumgebung (falls vorhanden) wird zu einer Stagingumgebung. Die aktive Bereitstellung für die Produktions- und die Stagingumgebung kann unterschiedlich sein, aber der Bereitstellungsverlauf der bisherigen Builds ist unabhängig von der Umgebung gleich.<br/>
![][35]

<h2> <a name="step7"> </a>Schritt 6: Bereitstellen von einer Arbeitsverzweigung.</h2>

Wenn Sie Git verwenden, nehmen Sie die Änderungen normalerweise an einer Arbeitsverzweigung vor und integrieren diese in die Hauptverzweigung, wenn Ihre Entwicklung den abgeschlossenen Status erreicht. Während der Entwicklungsphase eines Projekts möchten Sie die Arbeitsverzweigung in Azure erstellen und bereitstellen.

1. Klicken Sie in Team Explorer auf die Schaltfläche **Home**, und klicken Sie dann auf die Schaltfläche **Verzweigungen**.<br/>
![][40]

2. Klicken Sie auf den Link **Neue Verzweigung**.<br/>
![][41]

3. Geben Sie den Namen der Verzweigung ein, zum Beispiel "working", und wählen Sie **Verzweigung erstellen**. Dadurch wird eine neue lokale Verzweigung erstellt.<br/>
![][42]

4. Veröffentlichen Sie die Verzweigung. Wählen Sie den Namen der Verzweigung in **Unveröffentlichte Verzweigungen** aus, und wählen Sie **Veröffentlichen**.<br/>
![][44]

6. Standardmäßig lösen nur Änderungen an der Hauptverzweigung eine kontinuierliche Erstellung aus. Um die kontinuierliche Erstellung für eine Arbeitsverzweigung einzurichten, wählen Sie die Seite "Builds" in Team Explorer aus, und wählen Sie **Builddefinition bearbeiten**.

7. Öffnen Sie die Registerkarte **Quelleinstellungen**. Wählen Sie unter **Überwachte Verzweigungen für fortlaufende Integration und parallele Builds** die Option **Klicken Sie hier, um eine neue Zeile hinzuzufügen**.<br/>
![][47]

8. Geben Sie die erstellte Verzweigung an, zum Beispiel refs/heads/working.
![][48]

9. Nehmen Sie eine Änderung am Code vor, öffnen Sie das Kontextmenü für die geänderte Datei, und wählen Sie **Commit**.<br/>
![][43]

10. Wählen Sie den Link **Unsynchronisierte Commits**, und wählen Sie die Schaltfläche **Sync** oder den Link **Push** aus, um die Änderungen der Arbeitsverzweigung in Visual Studio Online zu kopieren.
![][45]

11. Navigieren Sie zur Ansicht **Builds**, und suchen Sie den Build, der für die Arbeitsverzweigung ausgelöst wurde.

Weitere Informationen finden Sie unter [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Zusätzliche Tipps zur Verwendung von Git mit Visual Studio Online finden Sie unter [Freigeben von Code in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx). Informationen zur Verwendung eines Git-Repository, das nicht von Visual Studio Online verwaltet wird, für die Veröffentlichung zu Azure finden Sie unter [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](http://azure.microsoft.com/documentation/articles/web-sites-publish-source-control).

[Schritt 1: Erstellen eines Git-Repositorys]: #step1
[Schritt 2: Erstellen eines Projekts und Übertragen mithilfe von Push an das Git-Repository.]: #step2
[Schritt 3: Verbinden des Projekts mit Azure]: #step3
[Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung]: #step4
[Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)]: #step5
[Schritt 6: Ändern der Produktionsbereitstellung]: #step6
[Schritt 7: Bereitstellen von einer Arbeitsverzweigung]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG

<!--HONumber=45--> 

<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online and Git" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online und Git

Sie können Visual Studio Online-Teamprojekte verwenden, um ein Git-Repository für Quellcode zu hosten und dies automatisch zu erstellen und zu Azure-Websites oder -Cloud-Diensten bereitzustellen, wenn Sie ein Commit zum Repository mithilfe von Push übertragen.

Sie müssen Visual Studio 2013 und das Azure-SDK installiert haben. Wenn Sie Visual Studio 2013 nicht bereits installiert haben, laden Sie es herunter, indem Sie auf der Seite [www.visualstudio.com][www.visualstudio.com] auf den Link **Starten Sie kostenlos** klicken. Installieren Sie das Azure-SDK von [hier][hier] aus.

Gehen Sie folgendermaßen vor, um einen Cloud-Dienst für eine automatische Erstellung und Bereitstellung mithilfe von Visual Studio Online zu konfigurieren:

-   [Schritt 1: Anmelden bei Visual Studio Online und Erstellen eines Git-Repository.][Schritt 1: Anmelden bei Visual Studio Online und Erstellen eines Git-Repository.]

-   [Schritt 2: Erstellen eines Projekts und Übertragen mithilfe von Push an das Git-Repository.][Schritt 2: Erstellen eines Projekts und Übertragen mithilfe von Push an das Git-Repository.]

-   [Schritt 3: Verbinden des Projekts mit Azure][Schritt 3: Verbinden des Projekts mit Azure]

-   [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung][Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung]

-   [Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)][Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)]

-   [Schritt 6: Ändern der Produktionsbereitstellung][Schritt 6: Ändern der Produktionsbereitstellung]

-   [Schritt 7: Bereitstellen von einer Arbeitsverzweigung][Schritt 7: Bereitstellen von einer Arbeitsverzweigung]

## <a name="step1"></a><span class="short-header">Schritt 1: Anmelden bei Visual Studio Online und Erstellen eines Git-Repository.</span>Schritt 1: Anmelden bei Visual Studio Online und Erstellen eines Git-Repository

1.  Wenn Sie noch kein Visual Studio Online-Konto haben, folgen Sie den Anweisungen [hier][1]. Wenn Sie Ihr Teamprojekt erstellen, wählen Sie Git als Quellcodeverwaltungssystem aus. Befolgen Sie die Anweisungen, um Visual Studio mit dem Teamprojekt zu verbinden.

2.  Klicken Sie im Team Explorer auf den Link **Dieses Repository klonen**.
    ![][]

3.  Geben Sie den Speicherort der lokalen Kopie an, und klicken Sie auf **Klonen**.

## <a name="step2"> </a><span class="short-header">Erstellen eines Projekts und Übertragen an das Repository.</span>Schritt 2: Erstellen eines Projekts und Übertragen an das Repository

1.  Wählen Sie in Team Explorer im Abschnitt „Projektmappen“ den Link „Neu“, um ein neues Projekt im lokalen Repository zu erstellen.
    ![][2]

2.  Sie können eine Website oder einen Cloud-Dienst (Azure-Anwendung) bereitstellen, indem Sie die Schritte dieser Anleitung befolgen.
    Erstellen Sie ein neues Micrososft Azure-Cloud-Dienstprojekt
    oder ein neues ASP.NET-MVC-Projekt. Stellen Sie sicher, dass das Projekt auf .NET Framework 4 oder 4.5 abzielt, und fügen Sie, wenn Sie ein Cloud-Dienstprojekt erstellen, eine ASP.NET MVC-Webrolle und eine Workerrolle hinzu.
    Wenn Sie eine Website erstellen möchten, wählen Sie die ASP.NET-Webanwendungs-Projektvorlage aus, und wählen Sie dann MVC aus. Siehe [Erste Schritte mit Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET].

3.  Öffnen Sie das Kontextmenü für die Projektmappe, und klicken Sie auf **Commit**.
    ![][3]

4.  Wenn Sie zum ersten Mal Git in Visual Studio Online verwenden, müssen Sie einige Informationen angeben, um sich in Git zu identifizieren. Geben Sie im Bereich **Ausstehende Änderungen** des Team Explorer Ihren Benutzernamen und Ihre E-Mail-Adresse an. Geben Sie einen Kommentar für den Commit ein, und wählen Sie **Commit**.
    ![][4]

5.  Bitte beachten Sie beim Einchecken die Option zum Ein- oder Ausschließen bestimmter Änderungen. Wenn die gewünschten Änderungen ausgeschlossen sind, klicken Sie auf **Alle einschließen**.

6.  Sie haben jetzt die Änderungen in der lokalen Kopie des Repository übermittelt. Als Nächstes synchronisieren Sie diese Änderungen beim Server. Klicken Sie auf den Link **Sync**.

## <a name="step3"> </a><span class="short-header">Verbinden des Projekts mit Azure</span>Schritt 3: Verbinden des Projekts mit Azure

1.  Nach der Erstellung eines Git-Repository in Visual Studio Online mit Quellcode sind Sie bereit, Ihr Git-Repository mit Azure zu verbinden. Wählen Sie im [Azure-Portal][Azure-Portal] Ihren Cloud-dienst oder Ihre Website, oder erstellen Sie einen neuen Cloud-Dienst oder eine neue Website, indem Sie auf das +-Symbol links unten sowie auf **Cloud-Dienst** oder **Website** und anschließend auf **Schnellerfassung** klicken.<br.>
    ![][5]

2.  Klicken Sie für Cloud-Dienste auf den Link **Veröffentlichung einrichten mit Visual Studio Online**. Für Websites wählen Sie den Link **Bereitstellung über Quellcodeverwaltung einrichten**.
    ![][6]

3.  Geben Sie in das Textfeld des Assistenten den Namen Ihres Visual Studio Online-Kontos ein, und klicken Sie auf den Link **Jetzt autorisieren**. Eventuell werden Sie aufgefordert, sich anzumelden.
    ![][7]

4.  Klicken Sie im OAuth-Popupdialogfeld auf **Übernehmen**, um Azure für eine Konfiguration Ihres Teamprojekts in Visual Studio Online zu autorisieren.
    ![][8]

5.  Wenn die Autorisierung erfolgreich verläuft, wird eine Dropdown-Liste Ihrer Visual Studio Online-Teamprojekte angezeigt. Wählen Sie den Namen des Teamprojekts aus, das Sie in den vorherigen Schritten erstellt haben, und klicken Sie auf das Häkchen im Assistenten.
    ![][9]

Wenn Sie das nächste Mal ein Commit zum Repository übertragen, wird Visual Studio Online Ihr Projekt erstellen und in Azure bereitstellen.

## <a name="step4"> </a><span class="short-header">Auslösen einer erneuten Erstellung</span>Schritt 4: Auslösen einer erneuten Erstellung und Bereitstellung Ihres Projekts

1.  Öffnen Sie in Visual Studio eine Datei, und ändern Sie sie. Ändern Sie z. B. die Datei \_Layout.cshtml im Ordner Views\\Shared in eine MVC-Webrolle.
    ![][10]

2.  Bearbeiten Sie den Fußzeilentext der Website und speichern Sie die Datei.
    ![][11]

3.  Öffnen Sie im Projektmappen-Explorer das Kontextmenü für den Projektmappenknoten, für den Projektknoten oder für die geänderte Datei, und wählen Sie **Commit**.

4.  Geben Sie einen Kommentar ein und wählen Sie **Commit**.
    ![][4]

5.  Klicken Sie auf den Link **Sync**.
    ![][12]

6.  Wählen Sie den Link **Push** aus, um Ihr Commit zum Repository in Visual Studio Online zu übertragen. (Sie können auch die Schaltfläche **Sync** verwenden, um Commits zum Repository zu übertragen. Der Unterschied liegt darin, dass **Sync** auch die letzten Änderungen aus dem Repository abruft.)
    ![][13]

7.  Klicken Sie auf die Schaltfläche "Home", um zur Startseite des Teamexplorers zurückzukehren.
    ![][14]

8.  Wählen Sie **Builds**, um die aktiven Builds anzuzeigen.
    ![][15]
    Team Explorer zeigt an, dass ein Build für das Einchecken ausgelöst wurde.
    ![][16]

9.  Doppelklicken Sie auf den Namen des aktiven Builds, um ein detailliertes Protokoll des Buildverlaufs anzuzeigen.

10. Betrachten Sie während des Buildverlaufs die Builddefinition, die erstellt wurde, als Sie mit dem Assistenten eine Verknüpfung mit Azure eingerichtet haben. Öffnen Sie das Kontextmenü für die Builddefinition, und wählen Sie **Builddefinition bearbeiten**.
    ![][17]
    In der Registerkarte **Trigger** sehen Sie, dass die Builddefinition so eingestellt ist, dass standardmäßig bei jedem Einchecken ein Build erstellt wird. (Für einen Cloud-Dienst erstellt Visual Studio Online Builds und stellt die Hauptverzweigung automatisch zur Stagingumgebung bereit. Sie müssen für die Bereitstellung zur Live-Website weiterhin einen Schritt manuell durchführen. Für eine Website, die keine Stagingumgebung hat, erfolgt die Bereitstellung zur Hauptverzweigung direkt zur Live-Website.
    ![][18]
    Auf der Registerkarte **Prozess** können Sie sehen, dass die Bereitstellungsumgebung auf den Namen des Cloud-Diensts oder der Website festgelegt ist.
    ![][19]
    Geben Sie Werte für die Eigenschaften ein, wenn Sie andere Werte als die Standardwerte verwenden möchten. Die Eigenschaften für die Azure-Veröffentlichung befinden sich im Bereitstellungsabschnitt, und Sie müssen möglicherweise MSBuild-Parameter festlegen. Um eine andere Dienstkonfiguration als „Cloud“ anzugeben, legen Sie zum Beispiel in einem Cloud-Dienstprojekt die MSbuild-Parameter auf /p:TargetProfile=*YourProfile* fest, wobei *YourProfile* einer Dienstkonfigurationsdatei mit einem Namen wie ServiceConfiguration.*YourProfile*.cscfg entspricht.
    Die folgende Tabelle zeigt die verfügbaren Eigenschaften im Bereitstellungsabschnitt:

    <table>

    <tr>
    <td>
    **Eigenschaft**

    </td>
    <td>
    **Standardwert**

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
    > Nicht vertrauenswürdige Zertifikate zulassen
    >
    > </td>
    > <td>
    > Wenn dies „false“ ist, müssen SSL-Zertifikate von einer Stammzertifizierungsstelle signiert sein.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Upgrade zulassen
    >
    > </td>
    > <td>
    > Ermöglicht einer Bereitstellung, eine vorhandene Bereitstellung zu aktualisieren, anstatt eine neue zu erstellen. Behält die IP-Adresse bei.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Nicht löschen
    >
    > </td>
    > <td>
    > Wenn dies „true“ ist, wird keine vorhandene, nicht verbundene Bereitstellung überschrieben (Upgrade ist zulässig).
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Pfad zu Bereitstellungseinstellungen
    >
    > </td>
    > <td>
    > Der Pfad zu Ihrer .pubxml-Datei für eine Website, entsprechend dem Stammordner des Repositorys. Wird für Cloud-Dienste ignoriert.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Sharepoint-Bereitstellungsumgebung
    >
    > </td>
    > <td>
    > Entspricht dem Dienstnamen
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Microsoft Azure-Bereitstellungsumgebung
    >
    > </td>
    > <td>
    > Der Name der Website oder des Cloud-Diensts
    >
    > </td>
    > </tr>
    > </table>

11. An diesem Punkt sollte Ihr Build erfolgreich abgeschlossen sein.
    ![][20]

12. Wenn Sie auf den Buildnamen doppelklicken, zeigt Visual Studio eine **Buildzusammenfassung** mit allen Testergebnissen aus entsprechenden Komponententestprojekten an.
    ![][21]

13. Im [Azure-Portal][Azure-Portal] können Sie die entsprechende Bereitstellung auf der Registerkarte "Bereitstellungen" anzeigen, wenn Sie die Stagingumgebung auswählen.
    ![][22]

14. Wechseln Sie zur URL Ihrer Site. Wählen Sie für eine Website die Schaltfläche **Durchsuchen** im Portal. Wenn es sich um einen Cloud-Dienst handelt, wählen Sie die URL im Abschnitt **Auf einen Blick** der Seite **Dashboard**, auf der die Stagingumgebung angezeigt wird. Bereitstellungen aus einer fortlaufenden Integration für Cloud-Dienste werden standardmäßig in der Stagingumgebung veröffentlicht. Sie können dies ändern, indem Sie die Eigenschaft "Alternate Cloud Service Environment" auf "Produktion" stellen. Hier wird gezeigt, wo auf der Dashboard-Seite des Cloud-Diensts sich die Website-URL befindet:
    ![][23]
    Eine neue Browser-Registerkarte wird geöffnet, die Ihre laufende Site anzeigt.
    ![][24]

15. Wenn Sie weitere Änderungen an Ihrem Projekt vornehmen, lösen Sie weitere Builds aus und sammeln mehrere Bereitstellungen. Die letzte davon wird als „Aktiv“ markiert.
    ![][25]

## <a name="step5"> </a><span class="short-header">Erneute Bereitstellung eines früheren Builds</span>Schritt 5: Erneute Bereitstellung eines früheren Builds

Dieser Schritt ist optional. Wählen Sie im Verwaltungsportal eine frühere Bereitstellung aus, und klicken Sie auf die Schaltfläche **Erneut bereitstellen**, um Ihre Website auf einen früheren Eincheckvorgang zurückzusetzen. Bitte beachten Sie, dass dadurch in TFS ein neuer Build ausgelöst und in Ihrem Bereitstellungsverlauf ein neuer Eintrag erstellt wird.
![][26]

## <a name="step6"> </a><span class="short-header">Ändern der Produktionsbereitstellung</span>Schritt 6: Ändern der Produktionsbereitstellung

Wenn Sie bereit sind, können Sie die Stagingumgebung in die Produktionsumgebung überführen, indem Sie im Verwaltungsportal auf die Schaltfläche **Swap** klicken. Die neu bereitgestellte Stagingumgebung wird in eine Produktionsumgebung geändert, und die vorherige Produktionsumgebung (falls vorhanden) wird zu einer Stagingumgebung. Die aktive Bereitstellung für die Produktions- und die Stagingumgebung kann unterschiedlich sein, aber der Bereitstellungsverlauf der bisherigen Builds ist unabhängig von der Umgebung gleich,
![][27]

## <a name="step7"> </a><span class="short-header">Bereitstellen von einer Arbeitsverzweigung</span>Schritt 6: Bereitstellen von einer Arbeitsverzweigung.

Wenn Sie Git verwenden, nehmen Sie die Änderungen normalerweise an einer Arbeitsverzweigung vor und integrieren diese in die Hauptverzweigung, wenn Ihre Entwicklung den abgeschlossenen Status erreicht. Während der Entwicklungsphase eines Projekts möchten Sie die Arbeitsverzweigung in Azure erstellen und bereitstellen.

1.  Wählen Sie in Team Explorer die Schaltfläche **Home**, und wählen Sie dann die Schaltfläche **Verzweigungen** aus.
    ![][28]

2.  Klicken Sie auf den Link **Neue Verzweigung**.
    ![][29]

3.  Geben Sie den Namen der Verzweigung ein, zum Beispiel „working“, und wählen Sie **Verzweigung erstellen**. Dadurch wird eine neue lokale Verzweigung erstellt.
    ![][30]

4.  Veröffentlichen Sie die Verzweigung. Wählen Sie den Namen der Verzweigung in **Unveröffentlichte Verzweigungen** aus, und wählen Sie **Veröffentlichen**.
    ![][31]

5.  Standardmäßig lösen nur Änderungen an der Hauptverzweigung eine kontinuierliche Erstellung aus. Um die kontinuierliche Erstellung für eine Arbeitsverzweigung einzurichten, wählen Sie die Seite „Builds“ in Team Explorer aus, und wählen Sie **Builddefinition bearbeiten**.

6.  Öffnen Sie die Registerkarte **Quelleinstellungen**. Wählen Sie unter **Überwachte Verzweigungen für fortlaufende Integration und parallele Builds** die Option **Klicken Sie hier, um eine neue Zeile hinzuzufügen**.
    ![][32]

7.  Geben Sie die erstellte Verzweigung an, zum Beispiel refs/heads/working.
    ![][33]

8.  Nehmen Sie eine Änderung am Code vor, öffnen Sie das Kontextmenü für die geänderte Datei, und wählen Sie **Commit**.
    ![][4]

9.  Wählen Sie den Link **Unsynchronisierte Commits**, und wählen Sie die Schaltfläche **Sync** oder den Link **Push** aus, um die Änderungen der Arbeitsverzweigung in Visual Studio Online zu kopieren.
    ![][12]

10. Navigieren Sie zur Ansicht **Builds** und suchen Sie den Build, der für die Arbeitsverzweigung ausgelöst wurde.

Weitere Informationen finden Sie unter [Visual Studio Online][Visual Studio Online]. Zusätzliche Tipps zur Verwendung von Git mit Visual Studio Online finden Sie unter [Freigeben von Code in Git][Freigeben von Code in Git]. Informationen zur Verwendung eines Git-Repository, das nicht von Visual Studio Online verwaltet wird, für die Veröffentlichung zu Azure finden Sie unter [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites][Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites].

  [www.visualstudio.com]: http://www.visualstudio.com
  [hier]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Schritt 1: Anmelden bei Visual Studio Online und Erstellen eines Git-Repository.]: #step1
  [Schritt 2: Erstellen eines Projekts und Übertragen mithilfe von Push an das Git-Repository.]: #step2
  [Schritt 3: Verbinden des Projekts mit Azure]: #step3
  [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung]: #step4
  [Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)]: #step5
  [Schritt 6: Ändern der Produktionsbereitstellung]: #step6
  [Schritt 7: Bereitstellen von einer Arbeitsverzweigung]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=397665
  []: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
  [2]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
  [Erste Schritte mit Azure und ASP.NET]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-dotnet-get-started/
  [3]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
  [4]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
  [Azure-Portal]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
  [6]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
  [7]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
  [8]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
  [9]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
  [12]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
  [13]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
  [14]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
  [16]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
  [29]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
  [30]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
  [31]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
  [32]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Freigeben von Code in Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-publish-source-control

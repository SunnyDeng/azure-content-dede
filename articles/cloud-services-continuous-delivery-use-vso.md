<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Kontinuierliche Bereitstellung in Azure mit Visual Studio Online" metaKeywords="" description="Hier erfahren Sie, wie Sie Ihre Teamprojekte in Visual Studio Online so konfigurieren, dass sie automatisch auf Azure-Websites oder in Azure-Cloud-Diensten erstellt und bereitgestellt werden." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Fortlaufende Bereitstellung f&uuml;r Azure mithilfe von Visual Studio Online" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online

Sie können Ihre Teamprojekte in Visual Studio Online so konfigurieren, dass sie automatisch auf Azure-Websites oder in Azure-Cloud-Diensten erstellt und bereitgestellt werden. (Informationen zur Einrichtung eines Systems für kontinuierliche Erstellung und Bereitstellung mithilfe eines *lokalen* Team Foundation Servers finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure][Kontinuierliche Zustellung für Cloud Services in Azure].)

Bei diesem Lernprogramm wird davon ausgegangen, dass auf Ihrem Rechner Visual Studio 2013 und das Azure-SDK installiert sind. Wenn Sie Visual Studio 2013 nicht bereits installiert haben, laden Sie es herunter, indem Sie auf der Seite [www.visualstudio.com][www.visualstudio.com] auf den Link **Starten Sie kostenlos** klicken. Installieren Sie das Azure-SDK von [hier][hier] aus.

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Sie ben&ouml;tigen ein Visual Studio Online-Konto, um dieses Lernprogramm durchf&uuml;hren zu k&ouml;nnen.</h5>
<p>Sie k&ouml;nnen <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">kostenlos ein Visual Studio Online-Konto er&ouml;ffnen</a>.</p>
</div>

Gehen Sie folgendermaßen vor, um einen Cloud-Dienst für eine automatische Erstellung und Bereitstellung mithilfe von Visual Studio Online zu konfigurieren:

-   [Schritt 1: Erstellen eines Teamprojekts][Schritt 1: Erstellen eines Teamprojekts]

-   [Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung][Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung]

-   [Schritt 3: Verbinden des Projekts mit Azure][Schritt 3: Verbinden des Projekts mit Azure]

-   [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung][Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung]

-   [Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)][Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)]

-   [Schritt 6: Ändern der Produktionsbereitstellung (nur Clouddienste)][Schritt 6: Ändern der Produktionsbereitstellung (nur Clouddienste)]

-   [Schritt 7: Ausführen von Komponententests (optional)][Schritt 7: Ausführen von Komponententests (optional)]

## <a name="step1"></a><span class="short-header">Erstellen eines Teamprojekts</span>Schritt 1: Erstellen eines Teamprojekts

Befolgen Sie die Anweisungen [hier][1], um ein Teamprojekt zu erstellen und es mit Visual Studio zu verbinden. Bei dieser Anleitung wird angenommen, dass Sie Team Foundation Version Control (TFVC) als Lösung für die Quellcodeverwaltung verwenden. Wenn Sie Git für die Versionskontrolle verwenden möchten, finden Sie hier die [Git-Version dieser Anleitung][Git-Version dieser Anleitung].

## <a name="step2"> </a><span class="short-header">Einchecken eines Projekts in der Quellcodeverwaltung.</span>Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung

1.  Öffnen Sie in Visual Studio die Lösung, die Sie bereitstellen möchten, oder erstellen Sie eine neue Lösung.
    Sie können eine Website oder einen Cloud-Dienst (Azure-Anwendung) bereitstellen, indem Sie die Schritte dieser Anleitung befolgen.
    Wenn Sie eine neue Lösung erstellen möchten, erstellen Sie ein neues Azure-Cloud-Dienstprojekt
    oder ein neues ASP.NET-MVC-Projekt. Stellen Sie sicher, dass das Projekt auf .NET Framework 4 oder 4.5 abzielt. Wenn Sie ein Clouddienstprojekt erstellen, fügen Sie eine ASP.NET MVC-Webrolle hinzu, und wählen Sie für die Webrolle "Internetanwendung" aus. Wählen Sie **Internetanwendung** aus, wenn Sie dazu aufgefordert werden.
    Wenn Sie eine Website erstellen möchten, wählen Sie die Projektvorlage ASP.NET Web Application und anschließend MVC aus. Siehe [Erste Schritte mit Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET].

2.  Öffnen Sie das Kontextmenü für die Anwendung, und wählen Sie **Projektmappe zur Quellcodeverwaltung hinzufügen**.
    ![][0]

3.  Übernehmen oder ändern Sie die Standardeinstellungen, und klicken Sie auf die Schaltfläche **OK**. Wenn der Prozess abgeschlossen ist, werden im Projektmappen-Explorer die Symbole für die Quellcodeverwaltung angezeigt.
    ![][2]

4.  Öffnen Sie das Kontextmenü für die Projektmappe, und klicken Sie auf **Einchecken**.
    ![][3]

5.  Geben Sie im Bereich "Pending Changes" des Teamexplorers einen Kommentar zum Eincheckvorgang ein, und klicken Sie auf die Schaltfläche **Einchecken**.
    ![][4]

 Bitte beachten Sie beim Einchecken die Option zum Ein- oder Ausschließen bestimmter Änderungen. Wenn Sie die gewünschten Änderungen ausgeschlossen haben, klicken Sie auf den Link **Include All**.
![][5]

## <a name="step3"> </a><span class="short-header">Verbinden des Projekts mit Azure</span>Schritt 3: Verbinden des Projekts mit Azure

1.  Nun, da Sie ein VSO-Teamprojekt mit Quellcode erstellt haben, sind Sie bereit, Ihr Teamprojekt mit Azure zu verbinden. Wählen Sie im [Azure-Portal][Azure-Portal] Ihren Cloud-Dienst oder Ihre Website, oder erstellen Sie einen neuen Cloud-Dienst oder eine neue Website, indem Sie auf das +-Symbol links unten sowie auf **Cloud-Dienst** oder **Website** und anschließend auf **Schnellerfassung** klicken. Klicken Sie auf den Link **Set up publishing with Visual Studio Online**.
    ![][6]

2.  Geben Sie in das Textfeld des Assistenten den Namen Ihres Visual Studio Online-Kontos ein, und klicken Sie auf den Link **Authorize Now (Jetzt autorisieren)**. Eventuell werden Sie aufgefordert, sich anzumelden.
    ![][7]

3.  Klicken Sie im OAuth-Popupdialogfeld auf **Übernehmen**, um Azure für eine Konfiguration Ihres Teamprojekts in VSO zu autorisieren.
    ![][8]

4.  Wenn die Autorisierung erfolgreich verläuft, wird eine Dropdown-Liste Ihrer Visual Studio Online-Teamprojekte angezeigt. Wählen Sie den Namen des Teamprojekts aus, das Sie in den vorherigen Schritten erstellt haben, und klicken Sie auf das Häkchen im Assistenten.
    ![][9]

5.  Wenn Ihr Projekt verknüpft wird, erhalten Sie einige Anweisungen zum Einchecken von Änderungen in Ihrem Visual Studio Online-Teamprojekt. Bei Ihrer nächsten Anmeldung erstellt Visual Studio Online Ihr Projekt in Azure und stellt es dort bereit. Versuchen Sie dies nun, indem Sie auf den Link **Check In from Visual Studio (Einchecken in Visual Studio)** und dann auf den Link **Launch Visual Studio (Visual Studio starten)** (oder auf die entsprechende Schaltfläche **Visual Studio** unten auf dem Portal-Bildschirm) klicken.
    ![][10]

## <a name="step4"> </a><span class="short-header">Auslösen einer erneuten Erstellung</span>Schritt 4: Auslösen einer erneuten Erstellung und Bereitstellung Ihres Projekts

1.  Klicken Sie im Teamexplorer von Visual Studio auf den Link **Source Control Explorer (Quellcodeverwaltungs-Explorer)**.
    ![][11]

2.  Navigieren Sie zu Ihrer Projektmappendatei, und öffnen Sie sie.
    ![][12]

3.  Öffnen Sie im Projektmappen-Explorer eine Datei, und ändern Sie sie. Ändern Sie z. B. die Datei \_Layout.cshtml im Ordner Views\\Shared in eine MVC-Webrolle.
    ![][13]

4.  Bearbeiten sie das Logo für die Website, und drücken Sie Strg+S, um die Datei zu speichern.
    ![][14]

5.  Klicken Sie im Teamexplorer auf den Link **Pending Changes**.
    ![][15]

6.  Geben Sie einen Kommentar ein, und klicken Sie auf die Schaltfläche **Einchecken**.
    ![][16]

7.  Klicken Sie auf die Schaltfläche "Home", um zur Startseite des Teamexplorers zurückzukehren.
    ![][17]

8.  Wählen Sie **Builds**, um die aktiven Builds anzuzeigen.
    ![][18]
    Team Explorer zeigt an, dass ein Build für das Einchecken ausgelöst wurde.
    ![][19]

9.  Doppelklicken Sie auf den Namen des Builds, der gerade erstellt wird, um ein detailliertes Protokoll des Build-Verlaufs anzuzeigen.
    ![][20]

10. Betrachten Sie während des Buildverlaufs die Builddefinition, die erstellt wurde, als Sie TFS mithilfe des Assistenten mit Azure verknüpft haben. Öffnen Sie das Kontextmenü für die Builddefinition, und wählen Sie **Builddefinition bearbeiten**.
    ![][21]
    In der Registerkarte **Trigger** sehen Sie, dass die Builddefinition so eingestellt ist, dass standardmäßig bei jedem Einchecken ein Build erstellt wird.
    ![][22]
    Auf der Registerkarte **Prozess** können Sie sehen, dass die Bereitstellungsumgebung auf den Namen des Cloud-Diensts oder der Website festgelegt ist. Wenn Sie mit Websites arbeiten, unterscheiden sich die Einstellungen, die Sie sehen, von den hier gezeigten.
    ![][23]
    Geben Sie Werte für die Eigenschaften ein, wenn Sie andere Werte als die Standardwerte verwenden möchten. Die Eigenschaften für die Azure-Veröffentlichung befinden sich im Bereitstellungsabschnitt.
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

Wenn Sie mehrere Dienstkonfigurationen verwenden (.cscfg-Dateien), können Sie die gewünschte Dienstkonfiguration in der Einstellung **Build, Erweitert, MSBuild-Argumente** festlegen. Um zum Beispiel "ServiceConfiguration.Test.cscfg" zu verwenden, legen Sie die Zeilenoption der MSBuild-Argumente auf "/p:TargetProfile=Test" fest.
![][24]

1.  An diesem Punkt sollte Ihr Build erfolgreich abgeschlossen sein.
    ![][25]

2.  Wenn Sie auf den Buildnamen doppelklicken, zeigt Visual Studio eine **Buildzusammenfassung** mit allen Testergebnissen aus entsprechenden Komponententestprojekten an.
    ![][26]

3.  Im [Azure-Portal][Azure-Portal] können Sie die entsprechende Bereitstellung auf der Registerkarte "Bereitstellungen" anzeigen, wenn Sie die Stagingumgebung auswählen.
    ![][27]

4.  Wechseln Sie zur URL Ihrer Site. Wenn es sich um eine Website handelt, klicken Sie einfach in der Befehlsleiste auf die Schaltfläche "Durchsuchen". Wenn es sich um einen Clouddienst handelt, wählen Sie die URL im Abschnitt **Auf einen Blick** der Seite **Dashboard**, auf der die Stagingumgebung für einen Clouddienst angezeigt wird. Bereitstellungen aus einer fortlaufenden Integration für Cloud-Dienste werden standardmäßig in der Stagingumgebung veröffentlicht. Sie können dies ändern, indem Sie die Eigenschaft "Alternate Cloud Service Environment" auf "Produktion" stellen. Dieser Screenshot zeigt, wo auf der Dashboard-Seite des Clouddienstes sich die Website-URL befindet:
    ![][28]
    Eine neue Browser-Registerkarte wird geöffnet, die Ihre laufende Site anzeigt.
    ![][29]

5.  Wenn es sich dabei um einen Clouddienst handelt und Sie weitere Änderungen an Ihrem Projekt vornehmen, lösen Sie weitere Builds aus und sammeln mehrere Bereitstellungen. Die letzte davon wird als "Aktiv" markiert.
    ![][30]

## <a name="step5"> </a><span class="short-header">Erneute Bereitstellung eines früheren Builds</span>Schritt 5: Erneute Bereitstellung eines früheren Builds

Dieser Schritt gilt nur für Clouddienste und ist optional. Wählen Sie im Verwaltungsportal eine frühere Bereitstellung aus, und klicken Sie auf die Schaltfläche **Redeploy**, um Ihre Website auf einen früheren Eincheckvorgang zurückzusetzen. Bitte beachten Sie, dass dadurch in TFS ein neuer Build ausgelöst und in Ihrem Bereitstellungsverlauf ein neuer Eintrag erstellt wird.
![][31]

## <a name="step6"> </a><span class="short-header">Ändern der Produktionsbereitstellung</span>Schritt 6: Ändern der Produktionsbereitstellung

Dieser Schritt gilt nur für Cloud-Dienste, nicht für Websites. Wenn Sie bereit sind, können Sie die Stagingumgebung in die Produktionsumgebung überführen, indem Sie im Verwaltungsportal auf die Schaltfläche "Swap" klicken. Die neu bereitgestellte Stagingumgebung wird in eine Produktionsumgebung geändert, und die vorherige Produktionsumgebung (falls vorhanden) wird zu einer Stagingumgebung. Die aktive Bereitstellung für die Produktions- und die Stagingumgebung kann unterschiedlich sein, aber der Bereitstellungsverlauf der bisherigen Builds ist unabhängig von der Umgebung gleich,
![][32]

## <a name="step7"> </a><span class="short-header">Ausführen von Komponententests</span>Schritt 7: Ausführen von Komponententests

Für eine zusätzliche Qualitätsstufe Ihrer Live- oder Stagingbereitstellungen können Sie Komponententests ausführen und im Falle eines Fehlers die Bereitstellung anhalten.

1.  Fügen Sie in Visual Studio ein Testprojekt hinzu.
    ![][33]

2.  Fügen Sie Projektverweise zu den Projekten hinzu, die Sie testen möchten.
    ![][34]

3.  Fügen Sie einige Komponententests hinzu. Probieren Sie es zuerst mit einem Dummy-Test, der immer erfolgreich sein wird.

        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }

4.  Bearbeiten Sie die Builddefinition, wählen Sie die Prozessregisterkarte, und erweitern Sie den Testknoten.

5.  Legen Sie für **Buildfehler bei Testfehler** "True" fest. Dies bedeutet, dass die Bereitstellung erst erfolgt, wenn die Tests erfolgreich waren.
    ![][35]

6.  Setzen Sie einen neuen Build in die Warteschlange.
    ![][36]
    ![][37]

7.  Prüfen Sie den Fortschritt während der Buildverarbeitung.
    ![][38]
    ![][39]

8.  Prüfen Sie die Testergebnisse, wenn der Build abgeschlossen ist.
    ![][40]
    ![][41]

9.  Versuchen Sie einen Test zu erstellen, der fehlschlagen wird. Fügen Sie einen neuen Test hinzu, indem Sie den ersten Test kopieren, umbenennen und die Codezeile auskommentieren, die NotImplementedException auslöst.

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. Checken Sie die Änderung ein, um einen neuen Build in die Warteschlange zu stellen.
    ![][42]

11. Sehen Sie sich die Testergebnisse an, um Details zum Fehler zu erhalten.
    ![][43]
    ![][44]

Weitere Informationen zu Komponententests in Visual Studio Online finden Sie unter [Ausführen von Komponententests im Build][Ausführen von Komponententests im Build].

Weitere Informationen finden Sie unter [Visual Studio Online][Visual Studio Online]. Wenn Sie Git verwenden, finden Sie Informationen unter [Freigeben von Code in Git][Freigeben von Code in Git] und [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites][Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites].

  [Kontinuierliche Zustellung für Cloud Services in Azure]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [hier]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Schritt 1: Erstellen eines Teamprojekts]: #step1
  [Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung]: #step2
  [Schritt 3: Verbinden des Projekts mit Azure]: #step3
  [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung]: #step4
  [Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)]: #step5
  [Schritt 6: Ändern der Produktionsbereitstellung (nur Clouddienste)]: #step6
  [Schritt 7: Ausführen von Komponententests (optional)]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=512980
  [Git-Version dieser Anleitung]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Erste Schritte mit Azure und ASP.NET]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-dotnet-get-started/
  [0]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [Azure-Portal]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [44]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [Ausführen von Komponententests im Build]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Freigeben von Code in Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-publish-source-control

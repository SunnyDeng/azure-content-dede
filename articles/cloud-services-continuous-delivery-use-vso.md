<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure web sites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="" editor="" />

Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online
=======================================================================

Visual Studio Online (zuvor Team Foundation Service) ist eine cloudgehostete Dienstversion von Microsofts beliebter Software Team Foundation Server (TFS), die einen leicht anpassbaren Quellcode und Build-Verwaltung, agile Entwicklung, Teamprozess-Workflow, Verfolgung von Problemen und Arbeitselementen und mehr bietet. Sie können Ihre Teamprojekte in Visual Studio Online so konfigurieren, dass sie automatisch auf Azure-Websites oder in Azure-Clouddiensten erstellt und bereitgestellt werden. Informationen zur Einrichtung eines Systems für kontinuierliche Erstellung und Bereitstellung mithilfe eines lokalen Team Foundation Servers finden Sie unter [Fortlaufende Bereitstellung für Clouddienste in Azure](../cloud-services-dotnet-continuous-delivery).

Bei diesem Lernprogramm wird davon ausgegangen, dass auf Ihrem Rechner Visual Studio 2013 und das Azure-SDK installiert sind. Wenn Sie Visual Studio 2013 nicht bereits installiert haben, laden Sie es herunter, indem Sie auf der Seite [www.visualstudio.com](http://www.visualstudio.com) auf den Link **Starten Sie kostenlos** klicken. Installieren Sie das Azure-SDK von [hier](http://go.microsoft.com/fwlink/?LinkId=239540) aus.

Gehen Sie folgendermaßen vor, um einen Clouddienst für eine automatische Erstellung und Bereitstellung mithilfe von Visual Studio Online zu konfigurieren:

-   [Schritt 1: Anmelden bei Visual Studio Online.](#step1)

-   [Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung](#step2)

-   [Schritt 3: Verbinden des Projekts mit Azure](#step3)

-   [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung](#step4)

-   [Schritt 5: Erneute Bereitstellung eines früheren Builds (optional)](#step5)

-   [Schritt 6: Ändern der Produktionsbereitstellung (nur Clouddienste)](#step6)

<a name="step1"></a>Anmelden bei Visual Studio OnlineSchritt 1: Anmelden bei Visual Studio Online
-----------------------------------------------------------------------------

1.  Erstellen Sie ein Visual Studio Online-Konto, indem Sie zu <http://www.visualstudio.com> navigieren. Klicken Sie auf den Link **Anmelden**. Sie müssen diese Anmeldung mit einem Microsoft-Konto durchführen. Wenn dies Ihre erste Anmeldung ist, werden Sie aufgefordert, einige persönliche Informationen wie Ihren Namen und Ihre E-Mal-Adresse einzugeben.
![](./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG)

2.  Wenn dies nicht Ihre erste Anmeldung ist, wird Ihnen bei der Anmeldung dieser Bildschirm angezeigt. Klicken Sie auf den Link **Jetzt kostenloses Konto erstellen**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG)

3.  Erstellen Sie eine Konto-URL für Ihr neues Projekt. Ihr Konto hat die folgende Form: https://&lt;Kontoname\>.visualstudio.com.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG)

4.  Sie können nun Ihr erstes Projekt erstellen. Geben Sie den Projektnamen und die Beschreibung ein. Wählen Sie das Versionskontrollsystem aus, das Sie verwenden möchten. Team Foundation Version Control (TFVC) und Git werden unterstützt. Weitere Informationen über diese Optionen erhalten Sie unter [Verwenden der Versionskontrolle](http://go.microsoft.com/fwlink/?LinkId=324037). In dieser Anleitung wird angenommen, dass Sie TFVC verwenden. Wählen Sie anschließend die Prozessvorlage aus, die Ihre Organisation verwendet, und klicken Sie auf die Schaltfläche **Projekt erstellen**. Weitere Informationen über Prozessvorlagen erhalten Sie unter [Arbeiten mit Teamprojektartefakten, Auswählen eines Prozessleitfadens](http://go.microsoft.com/fwlink/?LinkId=324035).<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs1.png)

5.  Wenn die Projekterstellung abgeschlossen ist, klicken Sie auf die Schaltfläche **Zum Verbinden mit Visual Studio öffnen**, um Visual Studio automatisch in Verbindung mit Ihrem Teamprojekt zu öffnen. Wenn Sicherheits-Dialogfelder angezeigt werden, klicken Sie auf "Zulassen".<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs2.png)

<a name="step2"> </a>Einchecken eines Projekts in der Quellcodeverwaltung.Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung
--------------------------------------------------------------------------------------------------------------------

1.  Öffnen Sie in Visual Studio die Anwendung, die Sie bereitstellen möchten, oder erstellen Sie eine neue Anwendung. Sie können eine Website oder einen Clouddienst (Azure-Anwendung) bereitstellen, indem Sie die Schritte in dieser Anleitung ausführen. Wenn Sie eine neue Anwendung erstellen möchten, erstellen Sie ein neues Azure-Clouddienstprojekt oder ein neues ASP.NET MVC-Projekt. Stellen Sie sicher, dass das Projekt auf .NET Framework 4 oder 4.5 abzielt. Wenn Sie ein Clouddienstprojekt erstellen, fügen Sie eine ASP.NET MVC-Webrolle hinzu, und wählen Sie für die Webrolle "Internetanwendung" aus. Wenn Sie dazu aufgefordert werden, wählen Sie **Internetanwendung** aus. Wenn Sie eine Website erstellen möchten, wählen Sie die Projektvorlage ASP.NET Web Application und anschließend MVC aus. Siehe [Erste Schritte mit Azure und ASP.NET](http://www.windowsazure.com/de-de/documentation/articles/web-sites-dotnet-get-started/).

2.  Öffnen Sie das Kontextmenü für die Anwendung, und wählen Sie **Projektmappe zur Quellcodeverwaltung hinzufügen**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs5.png)

3.  Übernehmen oder ändern Sie die Standardeinstellungen, und klicken Sie auf die Schaltfläche **OK**. Wenn der Prozess abgeschlossen ist, werden im Projektmappen-Explorer die Symbole für die Quellcodeverwaltung angezeigt.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs6.png)

4.  Öffnen Sie das Kontextmenü für die Projektmappe, und klicken Sie auf **Einchecken**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs7.png)

5.  Geben Sie im Bereich "Pending Changes" des Teamexplorers einen Kommentar zum Eincheckvorgang ein, und klicken Sie auf die Schaltfläche **Einchecken**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs8.png)

<br/>
 Bitte beachten Sie beim Einchecken die Option zum Ein- oder Ausschließen bestimmter Änderungen. Wenn Sie die gewünschten Änderungen ausgeschlossen haben, klicken Sie auf den Link **Include All**.
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs9.png)

<a name="step3"> </a>Verbinden des Projekts mit AzureSchritt 3: Verbinden des Projekts mit Azure
---------------------------------------------------------------------------

1.  Nun, da Sie ein VSO-Teamprojekt mit Quellcode erstellt haben, sind Sie bereit, Ihr Teamprojekt mit Azure zu verbinden. Wählen Sie im [Azure-Portal](http://manage.windowsazure.com) Ihren Clouddienst oder Ihre Website, oder erstellen Sie einen neuen Clouddienst oder eine neue Website, indem Sie auf das +-Symbol links unten sowie auf **Clouddienst** oder **Website** und anschließend auf **Schnellerstellung** klicken. Klicken Sie auf den Link **Set up publishing with Visual Studio Online**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs10.png)

2.  Geben Sie in das Textfeld des Assistenten den Namen Ihres Visual Studio Online-Kontos ein, und klicken Sie auf den Link **Authorize Now (Jetzt autorisieren)**. Eventuell werden Sie aufgefordert, sich anzumelden.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs11.png)

3.  Klicken Sie im OAuth-Popupdialogfeld auf **Übernehmen**, um Azure für eine Konfiguration Ihres Teamprojekts in VSO zu autorisieren.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs12.png)

4.  Wenn die Autorisierung erfolgreich verläuft, wird eine Dropdown-Liste Ihrer Visual Studio Online-Teamprojekte angezeigt. Wählen Sie den Namen des Teamprojekts aus, das Sie in den vorherigen Schritten erstellt haben, und klicken Sie auf das Häkchen im Assistenten.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs13.png)

5.  Wenn Ihr Projekt verknüpft wird, erhalten Sie einige Anweisungen zum Einchecken von Änderungen in Ihrem Visual Studio Online-Teamprojekt. Bei Ihrer nächsten Anmeldung erstellt Visual Studio Online Ihr Projekt in Azure und stellt es dort bereit. Versuchen Sie dies nun, indem Sie auf den Link **Check In from Visual Studio (Einchecken in Visual Studio)** und dann auf den Link **Launch Visual Studio (Visual Studio starten)** (oder auf die entsprechende Schaltfläche **Visual Studio** unten auf dem Portal-Bildschirm) klicken.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs14.png)

<a name="step4"> </a>Auslösen einer erneuten ErstellungSchritt 4: Auslösen einer erneuten Erstellung und Bereitstellung Ihres Projekts
-----------------------------------------------------------------------------------------------------------------

1.  Klicken Sie im Teamexplorer von Visual Studio auf den Link **Source Control Explorer (Quellcodeverwaltungs-Explorer)**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs15.png)

2.  Navigieren Sie zu Ihrer Projektmappendatei, und öffnen Sie sie.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs16.png)

3.  Öffnen Sie im Projektmappen-Explorer eine Datei, und ändern Sie sie. Ändern Sie z. B. die Datei \_Layout.cshtml im Ordner Views\\Shared in eine MVC-Webrolle.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs17.png)

4.  Bearbeiten sie das Logo für die Website, und drücken Sie Strg+S, um die Datei zu speichern.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs18.png)

5.  Klicken Sie im Teamexplorer auf den Link **Pending Changes**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs19.png)

6.  Geben Sie einen Kommentar ein, und klicken Sie auf die Schaltfläche **Einchecken**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs20.png)

7.  Klicken Sie auf die Schaltfläche "Home", um zur Startseite des Teamexplorers zurückzukehren.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs21.png)

8.  Klicken Sie auf den Link **Builds**, um die gerade stattfindenden Builds anzuzeigen.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs22.png)
<br/>
Im Teamexplorer ist zu sehen, dass ein Build für Ihr Einchecken ausgelöst wurde.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs23.png)

9.  Doppelklicken Sie auf den Namen des Builds, der gerade erstellt wird, um ein detailliertes Protokoll des Build-Verlaufs anzuzeigen.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs24.png)

10. Betrachten Sie während des Buildverlaufs die Builddefinition, die erstellt wurde, als Sie TFS mithilfe des Assistenten mit Azure verknüpft haben. Öffnen sie das Kontextmenü für die Builddefinition, und klicken Sie auf **Edit Build Definition (Builddefinition bearbeiten)**.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs25.png)
<br/>
Auf der Registerkarte **Trigger** sehen Sie, dass die Builddefinition bei jedem Einchecken standardmäßig auf einen Build gestellt ist.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs26.png)
<br/>
Auf der Registerkarte **Process** ist zu sehen, dass die Entwicklungsumgebung auf den Namen Ihres Clouddienstes oder Ihrer Website eingestellt ist. Wenn Sie mit Websites arbeiten, unterscheiden sich die Einstellungen, die Sie sehen, von den hier gezeigten.<br/>
![](./media/cloud-services-continuous-delivery-use-vso/tfs27.png)
<br/>
Geben Sie Werte für die Eigenschaften ein, wenn Sie andere Werte als die Standardwerte verwenden möchten. Die folgende Tabelle enthält die Standardwerte für die Eigenschaften eines Clouddienstes:

	<table>
<tr><td><b>Eigenschaft</b></td><td><b>Standardwert</b></td></tr>
<tr><td>Upgrade zulassen</td><td>true</td></tr>
<tr><td>Clouddienstumgebung</td><td>Wird bereitgestellt</td></tr>
<tr><td>Cloudienstname</td><td>Der Name des Dienstes, mit dem Sie verbunden sind</td></tr>
<tr><td>Bereitstellungsbezeichnung</td><td>Entspricht dem Dienstnamen</td></tr>
<tr><td>Dienstkonfiguration</td><td>ServiceConfiguration.Cloud.cscfg</td></tr>
<tr><td>Speicherkontoname</td><td>Leer - vom Benutzer auszufüllen.</td></tr>
<tr><td>Veröffentlichungsprofil</td><td>Die Datei .azurePubxml. Wenn Sie ein Profil einchecken, können Sie es hier auswählen.</td></tr>
</table>
<br/>
 Wenn die Eigenschaft des Speicherkontos leer ist, sucht Azure nach einer Eigenschaft. Wenn ein Speicherkonto mit dem Namen des Clouddienstes existiert, wird dieses verwendet. Andernfalls wird ein anderes Speicherkonto verwendet oder, falls noch kein Speicherkonto existiert, erstellt. Das Speicherkonto bietet einen Platz in Azure zum Speichern von Dateien und anderen Daten. Weitere Informationen finden Sie unter [Was ist ein Speicherkonto?](http://www.windowsazure.com/de-de/documentation/articles/storage-whatis-account).

1.  An diesem Punkt sollte Ihr Build erfolgreich abgeschlossen sein.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs28.png)

2.  Wenn Sie auf den Buildnamen doppelklicken, zeigt Visual Studio eine **Buildzusammenfassung** mit allen Testergebnissen aus entsprechenden Komponententestprojekten an.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs29.png)

3.  Im [Azure-Portal](http://manage.windowsazure.com) können Sie die entsprechende Bereitstellung auf der Registerkarte "Bereitstellungen" anzeigen, wenn Sie die Stagingumgebung auswählen.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs30.png)

4.  Wechseln Sie zur URL Ihrer Site. Wenn es sich um Website handelt, klicken Sie einfach in der Befehlsleiste auf die Schaltfläche "Durchsuchen". Wenn es sich um einen Clouddienst handelt, wählen Sie die URL im Abschnitt **Auf einen Blick** der Seite **Dashboard**, auf der die Stagingumgebung für einen Clouddienst angezeigt wird. Bereitstellungen aus einer fortlaufenden Integration für Clouddienste werden standardmäßig in der Stagingumgebung veröffentlicht. Sie können dies ändern, indem Sie die Eigenschaft "Alternate Cloud Service Environment" auf "Produktion" stellen. Dieser Screenshot zeigt, wo auf der Dashboard-Seite des Clouddienstes sich die Website-URL befindet:<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs31.png)
<br/>
     Eine neue Browser-Registerkarte wird geöffnet, die Ihre laufende Site anzeigt.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs32.png)

5.  Wenn es sich dabei um einen Clouddienst handelt und Sie weitere Änderungen an Ihrem Projekt vornehmen, lösen Sie weitere Builds aus und sammeln mehrere Bereitstellungen. Die letzte davon wird als "Aktiv" markiert.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs33.png)

<a name="step5"> </a>Erneute Bereitstellung eines früheren BuildsSchritt 5: Erneute Bereitstellung eines früheren Builds
---------------------------------------------------------------------------------------------------

Dieser Schritt gilt nur für Clouddienste und ist optional. Wählen Sie im Verwaltungsportal eine frühere Bereitstellung aus, und klicken Sie auf die Schaltfläche **Redeploy**, um Ihre Website auf einen früheren Eincheckvorgang zurückzusetzen. Bitte beachten Sie, dass dadurch in TFS ein neuer Build ausgelöst und in Ihrem Bereitstellungsverlauf ein neuer Eintrag erstellt wird.
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs34.png)

<a name="step6"> </a>Ändern der ProduktionsbereitstellungSchritt 6: Ändern der Produktionsbereitstellung
-----------------------------------------------------------------------------------

Dieser Schritt gilt nur für Clouddienste, nicht für Websites. Wenn Sie bereit sind, können Sie die Stagingumgebung in die Produktionsumgebung überführen, indem Sie im Verwaltungsportal auf die Schaltfläche "Swap" klicken. Die neu bereitgestellte Stagingumgebung wird in eine Produktionsumgebung geändert, und die vorherige Produktionsumgebung (falls vorhanden) wird zu einer Stagingumgebung. Die aktive Bereitstellung für die Produktions- und die Stagingumgebung kann unterschiedlich sein, aber der Bereitstellungsverlauf der bisherigen Builds ist unabhängig von der Umgebung gleich,<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs35.png)

Weitere Informationen finden Sie unter [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Wenn Sie Git verwenden, siehe [Freigeben von Code in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) und[Publishing from Source Control to Azure Web Sites (Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites, in englischer Sprache)](http://www.windowsazure.com/de-de/documentation/articles/web-sites-publish-source-control).

[Step 1: Sign up for Visual Studio Online.]: #step1
[Step 2: Check in a project to source control.]: #step2
[Step 3: Connect the project to Azure.]: #step3
[Step 4: Make changes and trigger a rebuild and redeployment.]: #step4
[Step 5: Redeploy an earlier build (optional)]: #step5
[Step 6: Change the Production deployment (cloud services only)]: #step6
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png


[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG

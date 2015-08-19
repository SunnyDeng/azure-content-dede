<properties 
	pageTitle="Kontinuierliche Bereitstellung in Azure mit Visual Studio Online" 
	description="Hier erfahren Sie, wie Sie Ihre Teamprojekte in Visual Studio Online so konfigurieren, dass sie automatisch für die Web-App-Funktion in Azure App Service oder in Clouddiensten erstellt und bereitgestellt werden." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/08/2015" 
	ms.author="kempb"/>


# Fortlaufende Bereitstellung für Azure mithilfe von Visual Studio Online

  Sie können Ihre Teamprojekte in Visual Studio Online so konfigurieren, dass sie automatisch in Azure-Web-Apps oder -Clouddiensten erstellt und bereitgestellt werden. (Informationen zur Einrichtung eines Systems für kontinuierliche Erstellung und Bereitstellung mithilfe eines *lokalen* Team Foundation Servers finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure](cloud-services-dotnet-continuous-delivery.md).)

Bei diesem Lernprogramm wird davon ausgegangen, dass auf Ihrem Rechner Visual Studio 2013 und das Azure-SDK installiert sind. Wenn Sie Visual Studio 2013 nicht bereits installiert haben, laden Sie es herunter, indem Sie auf der Seite **www.visualstudio.com** auf den Link [Starten Sie kostenlos](http://www.visualstudio.com) klicken. Installieren Sie das Azure-SDK von [hier](http://go.microsoft.com/fwlink/?LinkId=239540) aus.

> [AZURE.NOTE]Sie benötigen ein Visual Studio Online-Konto, um dieses Lernprogramm durchführen zu können.
> Sie können [ein Visual Studio Online-Konto kostenlos eröffnen](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Gehen Sie folgendermaßen vor, um einen Clouddienst für eine automatische Erstellung und Bereitstellung mithilfe von Visual Studio Online zu konfigurieren:

-   [Schritt 1: Erstellen eines Teamprojekts][]

-   [Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung][]

-   [Schritt 3: Verbinden des Projekts mit Azure][]

-   [Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung][]

-   [Schritt 5: Erneutes Bereitstellen eines früheren Builds (optional)][]

-   [Schritt 6: Ändern der Produktionsbereitstellung (nur Clouddienste)][]

-	[Schritt 7: Ausführen von Komponententests (optional)][]

## <a name="step1"></a>Schritt 1: Erstellen eines Teamprojekts

Befolgen Sie die Anweisungen [hier](http://go.microsoft.com/fwlink/?LinkId=512980), um ein Teamprojekt zu erstellen und es mit Visual Studio zu verbinden. Bei dieser Anleitung wird angenommen, dass Sie Team Foundation Version Control (TFVC) als Lösung für die Quellcodeverwaltung verwenden. Wenn Sie Git für die Versionskontrolle verwenden möchten, finden Sie hier die [Git-Version dieser Anleitung](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="step2"> </a>Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung

1. Öffnen Sie in Visual Studio die Anwendung, die Sie bereitstellen möchten, oder erstellen Sie eine neue Anwendung.
Sie können eine Web-App oder einen Clouddienst (Azure-Anwendung) bereitstellen, indem Sie die Schritte in dieser Anleitung ausführen.
Wenn Sie eine neue Anwendung erstellen möchten, erstellen Sie ein neues Azure-Clouddienstprojekt oder ein neues ASP.NET MVC-Projekt. 
Stellen Sie sicher, dass das Projekt auf .NET Framework 4 oder 4.5 abzielt. Wenn Sie ein Clouddienstprojekt erstellen, fügen Sie eine ASP.NET MVC-Webrolle hinzu, und wählen Sie für die Webrolle "Internetanwendung" aus. Wenn Sie dazu aufgefordert werden, wählen Sie **Internetanwendung** aus. 
Wenn Sie eine Web-App erstellen möchten, wählen Sie die Projektvorlage "ASP.NET Web Application" und anschließend "MVC" aus. Siehe [Erstellen einer ASP.NET-Web-App in Azure App Service](../web-sites-dotnet-get-started.md).

> [AZURE.NOTE]Visual Studio Online unterstützt derzeit nur CI-Bereitstellungen von Visual Studio-Webanwendungen. Websiteprojekte werden nicht unterstützt.


2. Öffnen Sie das Kontextmenü der Projektmappe, und wählen Sie **Projektmappe zur Quellcodeverwaltung hinzufügen**.<br/>
 ![][5]

3. Übernehmen oder ändern Sie die Standardeinstellungen, und klicken Sie auf die Schaltfläche **OK**. Wenn der Prozess abgeschlossen ist, werden im Projektmappen-Explorer die Symbole für die Quellcodeverwaltung angezeigt.<br/>
 ![][6]

4. Öffnen Sie das Kontextmenü der Projektmappe, und klicken Sie auf **Einchecken**.<br/>
 ![][7]

5. Geben Sie in Team Explorer im Bereich "Ausstehende Änderungen" einen Kommentar zum Eincheckvorgang ein, und klicken Sie auf die Schaltfläche **Einchecken**.<br/>
 ![][8]

<br/>
 Beachten Sie die Optionen zum Ein- oder Ausschließen bestimmter Änderungen beim Einchecken. Wenn die gewünschten Änderungen ausgeschlossen wurden, wählen Sie den Link **Alles einschließen**.<br/>
 ![][9]

## <a name="step3"> </a>Schritt 3: Verbinden des Projekts mit Azure

1. Nun, da Sie ein VSO-Teamprojekt mit Quellcode erstellt haben, sind Sie bereit, Ihr Teamprojekt mit Azure zu verbinden. Wählen Sie im [Azure-Portal](http://manage.windowsazure.com) Ihren Clouddienst oder Ihre Web-App aus, oder erstellen Sie einen neuen Clouddienst oder eine neue Web-App, indem Sie auf das +-Symbol links unten sowie auf **Clouddienst** oder **Web-App** und anschließend auf **Schnellerstellung** klicken. Klicken Sie auf den Link **Veröffentlichung über Visual Studio Online einrichten**.<br/>
 ![][10]

2. Geben Sie in das Textfeld des Assistenten den Namen Ihres Visual Studio Online-Kontos ein, und klicken Sie auf den Link **Authorize Now (Jetzt autorisieren)**. Eventuell werden Sie aufgefordert, sich anzumelden.<br/>
 ![][11]

3. Klicken Sie im OAuth-Popupdialogfeld auf **Übernehmen**, um Azure für eine Konfiguration Ihres Teamprojekts in VSO zu autorisieren.<br/>
 ![][12]

4. Wenn die Autorisierung erfolgreich verläuft, wird eine Dropdown-Liste Ihrer Visual Studio Online-Teamprojekte angezeigt. Wählen Sie den Namen des Teamprojekts aus, das Sie in den vorherigen Schritten erstellt haben, und klicken Sie auf das Häkchen für den Assistenten.<br/>
![][13]

5. Wenn Ihr Projekt verknüpft wird, erhalten Sie einige Anweisungen zum Einchecken von Änderungen in Ihrem Visual Studio Online-Teamprojekt. Bei Ihrer nächsten Anmeldung erstellt Visual Studio Online Ihr Projekt in Azure und stellt es dort bereit. Jetzt sind Sie an der Reihe: Klicken Sie auf den Link **Aus Visual Studio einchecken** und dann auf den Link **Visual Studio starten** (oder auf die entsprechende **Visual Studio**-Schaltfläche am unteren Rand des Portalbildschirms.<br/>
 ![][14]

## <a name="step4"> </a>Schritt 4: Auslösen einer Neuerstellung und erneutes Bereitstellen Ihres Projekts

1. Klicken Sie in Visual Studio Team Explorer auf den Link **Quellcodeverwaltungs-Explorer**.<br/>
 ![][15]

2. Navigieren Sie zu Ihrer Projektmappendatei, und öffnen Sie sie.<br/>
 ![][16]

3. Öffnen Sie im Projektmappen-Explorer eine Datei, und ändern Sie sie. Ändern Sie z. B. die Datei \_Layout.cshtml im Ordner "Views\\Shared" in eine MVC-Webrolle.<br/>
![][17]

4. Bearbeiten Sie das Logo für die Website, und drücken Sie Strg+S, um die Datei zu speichern.<br/>
![][18]

5. Klicken Sie im Teamexplorer auf den Link **Ausstehende Änderungen**.<br/>
![][19]

6. Geben Sie einen Kommentar ein, und klicken Sie auf die Schaltfläche **Einchecken**.<br/>
![][20]

7. Klicken Sie auf die Schaltfläche "Startseite", um zur Startseite von Team Explorer zurückzukehren.<br/>
![][21]

8. Klicken Sie auf den Link **Builds**, um die gerade stattfindenden Builds anzuzeigen.<br/>
![][22]
<br/>
 In Team Explorer wird ersichtlich, dass für Ihren Eincheckvorgang ein Build ausgelöst wurde.<br/>
![][23]

9. Doppelklicken Sie auf den Namen des Builds, der gerade erstellt wird, um ein detailliertes Protokoll zum Buildverlauf anzuzeigen.<br/>
![][24]

10. Betrachten Sie während des Buildverlaufs die Builddefinition, die erstellt wurde, als Sie TFS mithilfe des Assistenten mit Azure verknüpft haben. Öffnen sie das Kontextmenü für die Builddefinition, und klicken Sie auf **Edit Build Definition (Builddefinition bearbeiten)**.<br/>
![][25]
<br/>
 Auf der Registerkarte **Trigger** sehen Sie, dass die Builddefinition standardmäßig so festgelegt ist, dass bei jedem Einchecken ein Buildvorgang ausgeführt wird.<br/>
![][26]
<br/>
Auf der Registerkarte **Prozess** ist zu sehen, dass die Bereitstellungsumgebung auf den Namen Ihres Clouddiensts oder Ihrer Web-App eingestellt ist. Wenn Sie mit Web-Apps arbeiten, unterscheiden sich die Einstellungen, die Sie sehen, von den hier gezeigten.<br/>
![][27]
<br/>
Geben Sie Werte für die Eigenschaften ein, wenn Sie andere Werte als die Standardwerte verwenden möchten. Die Eigenschaften für die Azure-Veröffentlichung befinden sich im Bereitstellungsabschnitt.
Die folgende Tabelle zeigt die im Bereitstellungsabschnitt verfügbaren Eigenschaften:
	<table>
<tr><td><b>Eigenschaft</b></td><td><b>Standardwert</b></td></tr>
><tr><td>Nicht vertrauenswürdige Zertifikate zulassen</td><td>Wenn dies "false" ist, müssen SSL-Zertifikate von einer Stammzertifizierungsstelle signiert sein.</td></tr>
<tr><td>Upgrade zulassen</td><td>Ermöglicht einer Bereitstellung, eine vorhandene Bereitstellung zu aktualisieren, anstatt eine neue zu erstellen. Behält die IP-Adresse bei.</td></tr>
<tr><td>Nicht löschen</td><td>Wenn dies "true" ist, wird keine vorhandene, nicht verbundene Bereitstellung überschrieben (Upgrade ist zulässig).</td></tr>
><tr><td>Pfad zu Bereitstellungseinstellungen</td><td>Der Pfad zu Ihrer PUBXML-Datei für eine Web-App, entsprechend dem Stammordner des Repositorys. Wird für Clouddienste ignoriert.</td></tr>
<tr><td>Sharepoint-Bereitstellungsumgebung</td><td>Entspricht dem Dienstnamen</td></tr>
<tr><td>Azure-Bereitstellungsumgebung</td><td>Der Name der Web-App oder des Clouddiensts</td></tr>
</table>
<br/>

Wenn Sie mehrere Dienstkonfigurationen verwenden (.cscfg-Dateien), können Sie die gewünschte Dienstkonfiguration in der Einstellung **Build, Erweitert, MSBuild-Argumente** festlegen. Um zum Beispiel "ServiceConfiguration.Test.cscfg" zu verwenden, legen Sie die Zeilenoption der MSBuild-Argumente auf "/p:TargetProfile=Test" fest.<br/>
 ![][38]

11. An diesem Punkt sollte Ihr Build erfolgreich abgeschlossen sein.<br/>
 ![][28]

12. Wenn Sie auf den Buildnamen doppelklicken, zeigt Visual Studio eine **Buildzusammenfassung** mit allen Testergebnissen aus den zugeordneten Komponententestprojekten an.<br/>
 ![][29]

13. Im [Azure-Portal](http://manage.windowsazure.com) können Sie die entsprechende Bereitstellung auf der Registerkarte "Bereitstellungen" anzeigen, wenn die Stagingumgebung ausgewählt ist.<br/>
 ![][30]

14.	Wechseln Sie zur URL Ihrer Site. Wenn es sich um eine Web-App handelt, klicken Sie einfach in der Befehlsleiste auf die Schaltfläche "Durchsuchen". Wenn es sich um einen Clouddienst handelt, wählen Sie die URL im Abschnitt **Auf einen Blick** der Seite **Dashboard**, auf der die Stagingumgebung für einen Clouddienst angezeigt wird. Bereitstellungen aus einer fortlaufenden Integration für Clouddienste werden standardmäßig in der Stagingumgebung veröffentlicht. Sie können dies ändern, indem Sie die Eigenschaft "Alternate Cloud Service Environment" auf "Produktion" stellen. Dieser Screenshot zeigt die Position der Website-URL auf der Dashboard-Seite des Clouddiensts: <br/>
 ![][31]
<br/>
 Die ausgeführte Website wird auf einer neuen Browserregisterkarte angezeigt.<br/>
 ![][32]

15.	Wenn es sich dabei um einen Clouddienst handelt und Sie weitere Änderungen an Ihrem Projekt vornehmen, lösen Sie weitere Builds aus und sammeln mehrere Bereitstellungen. Die letzte davon wird als "Aktiv" markiert.<br/>
 ![][33]

## <a name="step5"> </a>Schritt 5: Erneutes Bereitstellen eines früheren Builds

Dieser Schritt gilt nur für Clouddienste und ist optional. Wählen Sie im Verwaltungsportal eine frühere Bereitstellung aus, und klicken Sie auf die Schaltfläche **Erneut bereitstellen**, um Ihre Website auf einen früheren Eincheckvorgang zurückzusetzen. Beachten Sie, dass dadurch ein neuer Buildvorgang in TFS ausgelöst und in Ihrem Bereitstellungsverlauf ein neuer Eintrag erstellt wird.<br/>
 ![][34]

## <a name="step6"> </a>Schritt 6: Ändern der Produktionsbereitstellung

Dieser Schritt gilt nur für Clouddienste, nicht für Web-Apps. Wenn Sie bereit sind, können Sie die Stagingumgebung in die Produktionsumgebung überführen, indem Sie im Verwaltungsportal auf die Schaltfläche "Swap" klicken. Die neu bereitgestellte Stagingumgebung wird in eine Produktionsumgebung geändert, und die vorherige Produktionsumgebung (falls vorhanden) wird zu einer Stagingumgebung. Die aktive Bereitstellung für die Produktionsumgebung kann sich von der aktiven Bereitstellung der Stagingumgebung unterscheiden, aber der Bereitstellungsverlauf der letzten Builds ist unabhängig von der Umgebung identisch.<br/>
![][35]

## <a name="step7"> </a>Schritt 7: Ausführen von Komponententests

Dieser Schritt gilt nur für Web-Apps, nicht für Clouddienste. Für eine zusätzliche Qualitätsstufe Ihrer Bereitstellungen können Sie Komponententests ausführen und im Falle eines Fehlers die Bereitstellung anhalten.

1.  Fügen Sie in Visual Studio ein Komponententestprojekt hinzu.<br/>
![][39]

2.  Fügen Sie dem Projekt, das Sie testen möchten, Projektverweise hinzu.<br/>
![][40]

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


5.  Legen Sie für **Buildfehler bei Testfehler** "True" fest. Dies bedeutet, dass die Bereitstellung erst erfolgt, wenn die Tests erfolgreich waren.<br/>
![][41]

6.  Setzen Sie einen neuen Build in die Warteschlange.<br/>
![][42]
<br/>
![][43]

7. Prüfen Sie während der Buildverarbeitung den Fortschritt.<br/>
![][44]
<br/>
![][45]

7. Prüfen Sie die Testergebnisse, wenn der Build abgeschlossen ist.<br/>
![][46]
<br/>
![][47]

8.  Versuchen Sie einen Test zu erstellen, der fehlschlagen wird. Fügen Sie einen neuen Test hinzu, indem Sie den ersten Test kopieren, umbenennen und die Codezeile auskommentieren, die besagt, dass "NotImplementedException" eine erwartete Ausnahme ist.

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. Checken Sie die Änderung ein, um einen neuen Build in die Warteschlange zu stellen.<br/>
 ![][48]

10. Sehen Sie sich die Testergebnisse an, um Einzelheiten zum Fehler zu erhalten.<br/>
![][49]
<br/>
![][50]

Weitere Informationen zu Komponententests in Visual Studio Online finden Sie unter [Ausführen von Komponententests im Build](http://go.microsoft.com/fwlink/p/?LinkId=510474).

Weitere Informationen finden Sie unter [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Wenn Sie Git verwenden, finden Sie unter [Freigeben von Code in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) und [Kontinuierliche Bereitstellung mit GIT in Azure App Service](../web-sites-publish-source-control.md).

[Schritt 1: Erstellen eines Teamprojekts]: #step1
[Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung]: #step2
[Schritt 3: Verbinden des Projekts mit Azure]: #step3
[Schritt 4: Durchführen von Änderungen und Auslösen einer erneuten Erstellung und Bereitstellung]: #step4
[Schritt 5: Erneutes Bereitstellen eines früheren Builds (optional)]: #step5
[Schritt 6: Ändern der Produktionsbereitstellung (nur Clouddienste)]: #step6
[Schritt 7: Ausführen von Komponententests (optional)]: #step7
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
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
 

<!-----HONumber=August15_HO6-->
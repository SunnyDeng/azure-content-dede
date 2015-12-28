<properties
	pageTitle="Kontinuierliche Bereitstellung mit Visual Studio Team Services in Azure | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie Ihre Teamprojekte in Visual Studio Team Services so konfigurieren, dass sie automatisch erstellt und für das Web-App-Feature in Azure App Service oder in Clouddiensten bereitgestellt werden."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="tarcher"/>

# Continuous Delivery für Azure mithilfe von Visual Studio Team Services

Sie können Ihre Teamprojekte in Visual Studio Team Services so konfigurieren, dass sie automatisch erstellt und in Azure-Web-Apps oder -Clouddiensten bereitgestellt werden. (Informationen zur Einrichtung eines Systems für kontinuierliche Erstellung und Bereitstellung mithilfe eines *lokalen* Team Foundation Servers finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure](cloud-services-dotnet-continuous-delivery.md).)

Bei diesem Lernprogramm wird davon ausgegangen, dass auf Ihrem Rechner Visual Studio 2013 und das Azure-SDK installiert sind. Wenn Sie Visual Studio 2013 nicht bereits installiert haben, laden Sie es herunter, indem Sie auf der Seite **www.visualstudio.com** auf den Link [Starten Sie kostenlos](http://www.visualstudio.com) klicken. Installieren Sie das Azure-SDK von [hier](http://go.microsoft.com/fwlink/?LinkId=239540) aus.

> [AZURE.NOTE]Für dieses Tutorial benötigen Sie ein Visual Studio Team Services-Konto. Dieses können Sie [kostenlos erstellen](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Gehen Sie wie folgt vor, um mithilfe von Visual Studio Team Services einen Clouddienst für die automatische Erstellung und Bereitstellung für Azure zu konfigurieren.

## Schritt 1: Erstellen eines Teamprojekts

Befolgen Sie die Anweisungen [hier](http://go.microsoft.com/fwlink/?LinkId=512980), um ein Teamprojekt zu erstellen und es mit Visual Studio zu verbinden. Bei dieser Anleitung wird angenommen, dass Sie Team Foundation Version Control (TFVC) als Lösung für die Quellcodeverwaltung verwenden. Wenn Sie Git für die Versionskontrolle verwenden möchten, finden Sie hier die [Git-Version dieser Anleitung](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## Schritt 2: Einchecken eines Projekts in der Quellcodeverwaltung

1. Öffnen Sie in Visual Studio die Anwendung, die Sie bereitstellen möchten, oder erstellen Sie eine neue Anwendung. Sie können eine Web-App oder einen Clouddienst (Azure-Anwendung) bereitstellen, indem Sie die Schritte in dieser Anleitung ausführen. Wenn Sie eine neue Anwendung erstellen möchten, erstellen Sie ein neues Azure-Clouddienstprojekt oder ein neues ASP.NET MVC-Projekt. Stellen Sie sicher, dass das Projekt auf .NET Framework 4 oder 4.5 abzielt. Wenn Sie ein Clouddienstprojekt erstellen, fügen Sie eine ASP.NET MVC-Webrolle hinzu, und wählen Sie für die Webrolle "Internetanwendung" aus. Wenn Sie dazu aufgefordert werden, wählen Sie **Internetanwendung** aus. Wenn Sie eine Web-App erstellen möchten, wählen Sie die Projektvorlage "ASP.NET Web Application" und anschließend "MVC" aus. Siehe [Erstellen einer ASP.NET-Web-App in Azure App Service](../web-sites-dotnet-get-started.md).

	> [AZURE.NOTE]Visual Studio Team Services unterstützt derzeit nur CI-Bereitstellungen von Visual Studio-Webanwendungen. Websiteprojekte werden nicht unterstützt.

1. Öffnen Sie das Kontextmenü der Projektmappe, und wählen Sie **Projektmappe zur Quellcodeverwaltung hinzufügen**.

	![][5]

1. Übernehmen oder ändern Sie die Standardeinstellungen, und klicken Sie auf die Schaltfläche **OK**. Wenn der Prozess abgeschlossen ist, werden im **Projektmappen-Explorer** die Symbole für die Quellcodeverwaltung angezeigt.

	![][6]

1. Öffnen Sie das Kontextmenü der Projektmappe, und klicken Sie auf **Einchecken**.

	![][7]

1. Geben Sie in **Team Explorer** im Bereich **Ausstehende Änderungen** einen Kommentar zum Eincheckvorgang ein, und klicken Sie auf die Schaltfläche **Einchecken**.

	![][8]

	Beachten Sie die Optionen zum Ein- oder Ausschließen bestimmter Änderungen beim Einchecken. Wenn die gewünschten Änderungen ausgeschlossen wurden, wählen Sie den Link **Alles einschließen**.

	![][9]

## Schritt 3: Verbinden des Projekts mit Azure

1. Nachdem Sie nun über ein VSTS-Teamprojekt mit Quellcode verfügen, können Sie Ihr Teamprojekt mit Azure verbinden. Wählen Sie im [klassischen Azure-Portal](http://manage.windowsazure.com) Ihren Clouddienst oder Ihre Web-App aus, oder erstellen Sie einen neuen Clouddienst oder eine neue Web-App, indem Sie auf das **+**-Symbol links unten sowie auf **Cloud Service** oder **Web-App** und anschließend auf **Schnellerstellung** klicken. Wählen Sie den Link **Veröffentlichung einrichten mit Visual Studio Team Services** aus.

	![][10]

1. Geben Sie in das Textfeld des Assistenten den Namen Ihres Visual Studio Team Services-Kontos ein, und klicken Sie auf den Link **Jetzt autorisieren**. Eventuell werden Sie aufgefordert, sich anzumelden.

	![][11]

1. Klicken Sie im Popupdialogfeld **Verbindungsanforderung** auf **Annehmen**, um Azure für die Konfiguration Ihres Teamprojekts in VSTS zu autorisieren.

	![][12]

1. Nach erfolgreicher Autorisierung wird eine Dropdownliste mit Ihren Visual Studio Team Services-Teamprojekten angezeigt. Wählen Sie den Namen des Teamprojekts aus, das Sie in den vorherigen Schritten erstellt haben, und klicken Sie auf das Häkchen für den Assistenten.

	![][13]

1. Wenn Ihr Projekt verknüpft wurde, erhalten Sie einige Anweisungen zum Einchecken von Änderungen in Ihr Visual Studio Team Services-Teamprojekt. Beim nächsten Einchecken erstellt Visual Studio Team Services Ihr Projekt in Azure und stellt es dort bereit. Jetzt sind Sie an der Reihe: Klicken Sie auf den Link **Aus Visual Studio einchecken** und dann auf den Link **Visual Studio starten** (oder auf die entsprechende **Visual Studio**-Schaltfläche am unteren Rand des Portalbildschirms).

	![][14]

## Schritt 4: Auslösen einer erneuten Erstellung und Bereitstellung Ihres Projekts

1. Klicken Sie in **Team Explorer** von Visual Studio auf den Link **Quellcodeverwaltungs-Explorer**.

	![][15]

1. Navigieren Sie zu Ihrer Projektmappendatei, und öffnen Sie sie.

	![][16]

1. Öffnen Sie im **Projektmappen-Explorer** eine Datei, und ändern Sie sie. Ändern Sie z. B. die Datei `_Layout.cshtml` im Ordner „Views\\Shared“ in eine MVC-Webrolle.

	![][17]

1. Bearbeiten Sie das Logo für die Website, und drücken Sie **STRG+S**, um die Datei zu speichern.

	![][18]

1. Klicken Sie in **Team Explorer** auf den Link **Ausstehende Änderungen**.

	![][19]

1. Geben Sie einen Kommentar ein, und klicken Sie auf die Schaltfläche **Einchecken**.

	![][20]

1. Klicken Sie auf die Schaltfläche **Startseite**, um zur Startseite von **Team Explorer** zurückzukehren.

	![][21]

1. Klicken Sie auf den Link **Builds**, um die derzeit ausgeführten Builds anzuzeigen.

	![][22]

	In **Team Explorer** wird ersichtlich, dass für Ihren Eincheckvorgang ein Build ausgelöst wurde.

	![][23]

1. Doppelklicken Sie auf den Namen des Builds, der gerade erstellt wird, um ein detailliertes Protokoll des Build-Verlaufs anzuzeigen.

	![][24]

1. Betrachten Sie während des Buildverlaufs die Builddefinition, die erstellt wurde, als Sie TFS mithilfe des Assistenten mit Azure verknüpft haben. Öffnen sie das Kontextmenü für die Builddefinition, und klicken Sie auf **Builddefinition bearbeiten**.

	![][25]

	Auf der Registerkarte **Trigger** sehen Sie, dass die Builddefinition standardmäßig so festgelegt ist, dass bei jedem Einchecken ein Buildvorgang ausgeführt wird.

	![][26]

	Auf der Registerkarte **Prozess** ist zu sehen, dass die Bereitstellungsumgebung auf den Namen Ihres Clouddiensts oder Ihrer Web-App eingestellt ist. Wenn Sie mit Web-Apps arbeiten, unterscheiden sich die angezeigten Eigenschaften von den hier aufgeführten Eigenschaften.

	![][27]

1. Geben Sie Werte für die Eigenschaften ein, wenn Sie andere Werte als die Standardwerte verwenden möchten. Die Eigenschaften für die Azure-Veröffentlichung befinden sich im Abschnitt **Bereitstellung**.

	Die folgende Tabelle zeigt die im Abschnitt **Bereitstellung** verfügbaren Eigenschaften:

	|Eigenschaft|Standardwert|
	|---|---|
	|Nicht vertrauenswürdige Zertifikate zulassen|Wenn dies "false" ist, müssen SSL-Zertifikate von einer Stammzertifizierungsstelle signiert sein.|
	|Upgrade zulassen|Ermöglicht einer Bereitstellung, eine vorhandene Bereitstellung zu aktualisieren, anstatt eine neue zu erstellen. Behält die IP-Adresse bei.|
	|Nicht löschen|Wenn dies "true" ist, wird keine vorhandene, nicht verbundene Bereitstellung überschrieben (Upgrade ist zulässig).|
	|Pfad zu Bereitstellungseinstellungen|Der Pfad zu Ihrer PUBXML-Datei für eine Web-App, entsprechend dem Stammordner des Repositorys. Wird für Clouddienste ignoriert.|
	|Sharepoint-Bereitstellungsumgebung|Entspricht dem Dienstnamen.|
	|Azure-Bereitstellungsumgebung|Der Name der Web-App oder des Clouddiensts|

1. Wenn Sie mehrere Dienstkonfigurationen verwenden (.cscfg-Dateien), können Sie die gewünschte Dienstkonfiguration in der Einstellung **Build, Erweitert, MSBuild-Argumente** festlegen. Um beispielsweise „ServiceConfiguration.Test.cscfg“ zu verwenden, legen Sie die Zeilenoption `/p:TargetProfile=Test` der MSBuild-Argumente fest.

	![][38]

	An diesem Punkt sollte Ihr Build erfolgreich abgeschlossen sein.

	![][28]

1. Wenn Sie auf den Buildnamen doppelklicken, zeigt Visual Studio eine **Buildzusammenfassung** mit allen Testergebnissen aus den zugeordneten Komponententestprojekten an.

	![][29]

1. Im [klassischen Azure-Portal](http://manage.windowsazure.com) können Sie die entsprechende Bereitstellung auf der Registerkarte **Bereitstellungen** anzeigen, wenn die Stagingumgebung ausgewählt ist.

	![][30]

1.	Wechseln Sie zur URL Ihrer Site. Wenn es sich um eine Web-App handelt, klicken Sie einfach auf der Befehlsleiste auf die Schaltfläche **Durchsuchen**. Wenn es sich um einen Clouddienst handelt, wählen Sie die URL im Abschnitt **Auf einen Blick** der Seite **Dashboard**, auf der die Stagingumgebung für einen Clouddienst angezeigt wird. Bereitstellungen aus einer fortlaufenden Integration für Clouddienste werden standardmäßig in der Stagingumgebung veröffentlicht. Sie können dies ändern, indem Sie für die Eigenschaft **Alternative Umgebung für den Clouddienst** die Option **Produktion** festlegen. Dieser Screenshot zeigt, wo sich die Website-URL auf der Dashboard-Seite des Clouddiensts befindet:

	![][31]

	Eine neue Browser-Registerkarte wird geöffnet, die Ihre laufende Site anzeigt.

	![][32]

	Wenn es sich dabei um einen Clouddienst handelt und Sie weitere Änderungen an Ihrem Projekt vornehmen, lösen Sie weitere Builds aus und sammeln mehrere Bereitstellungen. Die letzte davon wird als "Aktiv" markiert.

	![][33]

## Schritt 5: Erneutes Bereitstellen eines früheren Builds

Dieser Schritt gilt nur für Clouddienste und ist optional. Wählen Sie im klassischen Azure-Portal eine frühere Bereitstellung aus, und klicken Sie dann auf die Schaltfläche **Erneut bereitstellen**, um Ihre Website auf einen früheren Eincheckvorgang zurückzusetzen. Beachten Sie, dass dadurch ein neuer Buildvorgang in TFS ausgelöst und in Ihrem Bereitstellungsverlauf ein neuer Eintrag erstellt wird.

![][34]

## Schritt 6: Ändern der Produktionsbereitstellung

Dieser Schritt gilt nur für Clouddienste, nicht für Web-Apps. Wenn Sie bereit sind, können Sie die Stagingumgebung in die Produktionsumgebung überführen, indem Sie im klassischen Azure-Portal auf die Schaltfläche **Tauschen** klicken. Die neu bereitgestellte Stagingumgebung wird in eine Produktionsumgebung geändert, und die vorherige Produktionsumgebung (falls vorhanden) wird zu einer Stagingumgebung. Die aktive Bereitstellung für die Produktions- und die Stagingumgebung kann unterschiedlich sein, aber der Bereitstellungsverlauf der bisherigen Builds ist unabhängig von der Umgebung gleich.

![][35]

## Schritt 7: Ausführen von Komponententests

Dieser Schritt gilt nur für Web-Apps, nicht für Clouddienste. Für eine zusätzliche Qualitätsstufe Ihrer Bereitstellungen können Sie Komponententests ausführen und im Falle eines Fehlers die Bereitstellung anhalten.

1.  Fügen Sie in Visual Studio ein Testprojekt hinzu.

	![][39]

1.  Fügen Sie Projektverweise zu dem Projekt hinzu, das Sie testen möchten.

	![][40]

1.  Fügen Sie einige Komponententests hinzu. Probieren Sie es zuerst mit einem Dummy-Test, der immer erfolgreich sein wird.

		```
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
		```

1.  Bearbeiten Sie die Builddefinition, wählen Sie die Registerkarte **Prozess**, und erweitern Sie den **Testknoten**.

1.  Legen Sie für **Buildfehler bei Testfehler** "True" fest. Dies bedeutet, dass die Bereitstellung erst erfolgt, wenn die Tests erfolgreich waren.

	![][41]

1.  Setzen Sie einen neuen Build in die Warteschlange.

	![][42]

	![][43]

1. Prüfen Sie den Fortschritt während der Buildverarbeitung.

	![][44]

	![][45]

1. Prüfen Sie die Testergebnisse, wenn der Build abgeschlossen ist.

	![][46]

	![][47]

1.  Versuchen Sie einen Test zu erstellen, der fehlschlagen wird. Fügen Sie einen neuen Test hinzu, indem Sie den ersten Test kopieren, umbenennen und die Codezeile auskommentieren, die besagt, dass "NotImplementedException" eine erwartete Ausnahme ist.

		```
		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}
		```

1. Checken Sie die Änderung ein, um einen neuen Build in die Warteschlange zu stellen.

	![][48]

1. Sehen Sie sich die Testergebnisse an, um Details zum Fehler zu erhalten.

	![][49]

	![][50]

## Nächste Schritte
Weitere Informationen zu Komponententests in Visual Studio Team Services finden Sie unter [Ausführen von Komponententests im Build](http://go.microsoft.com/fwlink/p/?LinkId=510474). Wenn Sie Git verwenden, finden Sie unter [Freigeben von Code in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) und [Kontinuierliche Bereitstellung mit GIT in Azure App Service](../web-sites-publish-source-control.md). Weitere Informationen zu Visual Studio Team Services finden Sie unter [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

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

<!---HONumber=AcomDC_1217_2015-->
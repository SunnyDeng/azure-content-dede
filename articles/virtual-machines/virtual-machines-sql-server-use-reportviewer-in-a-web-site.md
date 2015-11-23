<properties 
	pageTitle="Verwenden von ReportViewer auf einer Website | Microsoft Azure"
	description="In diesem Thema wird beschrieben, wie eine Microsoft Azure-Website mit dem Visual Studio-Steuerelement „ReportViewer“ erstellt wird, in dem ein Bericht angezeigt wird, der auf einem virtuellen Microsoft Azure-Computer gespeichert ist."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# Verwenden von ReportViewer auf einer in Azure gehosteten Website

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Sie können eine Microsoft Azure-Website mit dem Visual Studio-Steuerelement „ReportViewer“ erstellen, in dem ein Bericht angezeigt wird, der auf einem virtuellen Microsoft Azure-Computer gespeichert ist. Das ReportViewer-Steuerelement ist eine Webanwendung, die Sie mithilfe der Vorlage für ASP.NET-Webanwendungen erstellen.

>[AZURE.IMPORTANT]Die Vorlagen für ASP.NET MVC-Webanwendungen unterstützen das ReportViewer-Steuerelement nicht.

Um „ReportViewer“ in Ihre Microsoft Azure-Website zu integrieren, müssen Sie die folgenden Aufgaben ausführen.

- **Hinzufügen** von Assemblys zum Bereitstellungspaket

- **Konfigurieren** von Authentifizierung und Autorisierung

- **Veröffentlichen** der ASP.NET-Webanwendung in Azure

## Voraussetzungen

Lesen Sie den Abschnitt "Allgemeine Empfehlungen und bewährte Methoden" in [SQL Server-Business Intelligence auf virtuellen Azure-Computern](virtual-machines-sql-server-business-intelligence.md).

>[AZURE.NOTE]ReportViewer-Steuerelemente werden mit Visual Studio Standard Edition oder höher geliefert. Wenn Sie die Web Developer Express Edition verwenden, müssen Sie die [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) installieren, damit Sie die ReportViewer-Laufzeitfunktionen verwenden können.
>
>Ein im lokalen Verarbeitungsmodus konfiguriertes ReportViewer-Steuerelement wird in Microsoft Azure nicht unterstützt.

Lesen Sie das Whitepaper [ReportViewer-Steuerelement von Reporting Services und Berichtsserver, die auf virtuellen Microsoft Azure-Computern basieren](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## Hinzufügen von Assemblys zu dem Bereitstellungspaket

Wenn Sie Ihre ASP.NET-Anwendung lokal hosten, werden die ReportViewer-Assemblys bei der Installation von Visual Studio in der Regel direkt im globalen Assemblycache (GAC) des IIS-Servers installiert, sodass die Anwendung direkt auf sie zugreifen kann. Wenn Sie Ihre ASP.NET-Anwendung dagegen in der Cloud hosten, lässt Microsoft Azure es nicht zu, dass irgendeine Komponente im GAC installiert wird. Daher müssen Sie sicherstellen, dass die ReportViewer-Assemblys lokal für Ihre Anwendung verfügbar sind. Sie können dies erreichen, indem Sie in Ihrem Projekt Verweise auf die Assemblys hinzufügen und diese so konfigurieren, dass sie lokal kopiert werden.

Im Remoteverarbeitungsmodus verwendet das ReportViewer-Steuerelement die folgenden Assemblys:

- **Microsoft.ReportViewer.WebForms.dll**: Enthält den ReportViewer-Code, den Sie benötigen, um das ReportViewer-Steuerelement auf Ihrer Seite zu verwenden. Ein Verweis auf diese Assembly wird dem Projekt hinzugefügt, wenn Sie ein ReportViewer-Steuerelement auf einer ASP.NET-Seite in Ihrem Projekt ablegen.

- **Microsoft.ReportViewer.Common.dll**: Enthält Klassen, die vom ReportViewer-Steuerelement zur Laufzeit verwendet. Diese Assembly wird Ihrem Projekt nicht automatisch hinzugefügt.

### So fügen Sie einen Verweis auf „Microsoft.ReportViewer.Common“ hinzu

- Klicken Sie mit der rechten Maustaste auf den Knoten **Verweise** des Projekts, wählen Sie **Verweis hinzufügen** aus, wählen Sie die Assembly auf der .NET-Registerkarte aus, und klicken Sie auf **OK**.

### So erreichen Sie, dass Ihre ASP.NET-Anwendung lokal auf die Assemblys zugreifen kann

1. Klicken Sie im Ordner **Verweise** auf die Assembly "Microsoft.ReportViewer.Common", sodass seine Eigenschaften im Eigenschaftenbereich angezeigt werden.

1. Legen Sie im Eigenschaftenbereich die Eigenschaft **Lokale Kopie** auf "Wahr" fest.

1. Wiederholen Sie die Schritte 1 und 2 für „Microsoft.ReportViewer.WebForms“.

### So rufen Sie ReportViewer Language Pack ab

1. Installieren Sie das entsprechende verteilbare Microsoft Report Viewer 2012-Laufzeit-Paket aus dem [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Wählen Sie die Sprache in der Dropdownliste aus. Danach wird die Seite auf die entsprechende Download Center-Seite umgeleitet.

1. Klicken Sie auf **Herunterladen**, um mit dem Herunterladen von "ReportViewerLP.exe" zu beginnen.

1. Nachdem Sie "ReportViewerLP.exe" heruntergeladen haben, klicken Sie auf **Ausführen**, damit es sofort installiert wird, oder klicken Sie auf **Speichern**, damit die Datei auf dem Computer gespeichert wird. Wenn Sie auf **Speichern** klicken, sollten Sie sich den Namen des Ordners merken, in dem Sie die Datei speichern.

1. Navigieren Sie zu dem Ordner, in dem Sie die Datei gespeichert haben. Klicken Sie mit der rechten Maustaste auf "ReportViewerLP.exe", klicken Sie auf **Als Administrator ausführen**, und klicken Sie dann auf **Ja**.

1. Nach dem Ausführen von "ReportViewerLP.exe" können Sie sehen, dass "c:\\windows\\assembly" die Ressourcendateien **ReportViewerLP.exe** und **Microsoft.ReportViewer.Common.Resources** enthält.

### So konfigurieren Sie für ein lokalisiertes ReportViewer-Steuerelement

1. Gehen Sie gemäß den oben angegebenen Anweisungen vor, um das verteilbare Microsoft Report Viewer 2012-Laufzeit-Paket herunterzuladen und zu installieren.

1. Erstellen Sie im Projekt den Ordner <language>, und kopieren Sie die zugehörigen Ressourcenassemblydateien. Folgende Ressourcenassemblydateien müssen kopiert werden: **Microsoft.ReportViewer.Webforms.Resources.dll** und **Microsoft.ReportViewer.Common.Resources.dll**. Wählen Sie die Ressourcenassemblydateien aus, und legen Sie im Eigenschaftenbereich die Eigenschaft **In Ausgabeverzeichnis kopieren** auf "**Immer kopieren**" fest.

1. Legen Sie die Kultur und Benutzeroberflächenkultur für das Webprojekt fest. Weitere Informationen dazu, wie die Kultur und Benutzeroberflächenkultur für eine ASP.NET-Webseite festgelegt werden, finden Sie unter [Vorgehensweise: Festlegen der Kultur und Benutzeroberflächenkultur für die Globalisierung von ASP.NET-Webseiten](http://go.microsoft.com/fwlink/?LinkId=237461).

## Konfigurieren von Authentifizierung und Autorisierung

Das ReportViewer-Steuerelement muss geeignete Anmeldeinformationen verwenden, um sich beim Berichtsserver zu authentifizieren, und die Anmeldeinformationen müssen vom Berichtsserver autorisiert werden, damit Sie auf die gewünschten Berichte zuzugreifen können. Informationen über die Authentifizierung finden Sie im Whitepaper [ReportViewer-Steuerelement von Reporting Services und Berichtsserver, die auf virtuellen Microsoft Azure-Computern basieren](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## Veröffentlichen der ASP.NET-Webanwendung in Azure

Anleitungen zum Veröffentlichen einer ASP.NET Web-Anwendung in Azure finden Sie unter [Vorgehensweise: Migrieren und Veröffentlichen einer Web-Anwendung in Azure aus Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) und [Erste Schritte mit Web-Apps und ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT]Wenn der Befehl „Azure-Bereitstellungsprojekt hinzufügen“ oder „Azure-Clouddienst-Projekt hinzufügen“ nicht im Kontextmenü im Projektmappen-Explorer angezeigt wird, müssen Sie das Zielframework für das Projekt in .NET Framework 4 ändern.
>
>Die beiden Befehle bieten im Wesentlichen die gleiche Funktionalität. Je nachdem, welche Version von Microsoft Azure SDK Sie installiert haben, wird der entsprechende dieser beiden Befehle im Kontextmenü angezeigt.

## Ressourcen

[Microsoft-Berichte](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server-Business Intelligence auf virtuellen Azure-Computern](virtual-machines-sql-server-business-intelligence.md)

[Verwenden von PowerShell zum Erstellen einer Azure-VM mit einem Berichtsserver im einheitlichen Modus](virtual-machines-sql-server-create-native-mode-report-server-powershell.md)

[ReportViewer-Steuerelement von Reporting Services und Berichtsserver, die auf virtuellen Microsoft Azure-Computern basieren](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)

<!---HONumber=Nov15_HO3-->
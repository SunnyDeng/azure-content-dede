<properties 
   pageTitle="Build per Befehlszeile für Azure"
	description="Build per Befehlszeile für Azure"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Build per Befehlszeile für Azure

##Übersicht

Sie können ein Paket für die Azure-Bereitstellung durch Ausführen von "MSBuild" an der Eingabeaufforderung erstellen. Sie können neben der Automatisierung eines Teils des Buildprozesses Builds für das Debugging, Staging und die Produktion konfigurieren und definieren.


## Microsoft-Buildmodul (MSBuild)

Mithilfe des Microsoft-Buildmoduls (MSBuild) können Sie Produkte in Testumgebungen für Builds erstellen, in denen Visual Studio nicht installiert ist. Von MSBuild wird ein XML-Format für Projektdateien verwendet, das erweiterbar ist und von Microsoft vollständig unterstützt wird. In diesem Dateiformat können Sie beschreiben, welche Elemente für eine oder mehrere Plattformen und Konfigurationen erstellt werden müssen.

Sie können MSBuild auch an der Eingabeaufforderung ausführen, was in diesen Thema beschrieben wird. Durch Festlegen von Eigenschaften an der Eingabeaufforderung können Sie bestimmte Konfigurationen für ein Projekt erstellen. Entsprechend können Sie die Ziele definieren, die vom MSBuild-Befehl erstellt werden. Weitere Informationen über Befehlszeilenparameter und MSBuild finden Sie unter [MSBuild-Befehlszeilenreferenz](https://msdn.microsoft.com/library/ms164311.aspx).

## Installation

Wie im folgenden Verfahren beschrieben, müssen Software und Tools auf dem Buildserver installiert werden, bevor Sie ein Azure-Paket mithilfe von MSBuild erstellen können:

1. Installieren Sie .NET Framework 4 oder höher. MSBuild ist darin enthalten.

1. Installieren Sie die [Azure Authoring Tools](http://go.microsoft.com/fwlink/?LinkId=394615) (suchen Sie nach "MicrosoftAzureAuthoringTools-x64.msi" oder "MicrosoftAzureAuthoringTools-x86.msi").

1. Installieren Sie die [Azure-Bibliotheken für .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (suchen Sie nach "MicrosoftAzureLibsForNet-x64.msi" oder "MicrosoftAzureLibsForNet-x86.msi").

1. Kopieren Sie die Datei "Microsoft.WebApplication.targets" aus einer Visual Studio-Installation auf einem anderen Computer.

    Die Datei befindet sich im Verzeichnis "C:\\Programme (x86)\\MSBuild\\Microsoft\\Visual Studio\\v12.0\\WebApplications (v11.0 für Visual Studio 2012)". Kopieren Sie die Datei in dasselbe Verzeichnis auf dem Buildserver.

1. Installieren Sie die [Azure Tools für Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Suchen Sie nach "WindowsAzureTools.vs120.exe", um Visual Studio 2013-Projekte zu erstellen.

## MSBuild-Parameter

Die einfachste Möglichkeit zur Erstellung eines Pakets besteht im Ausführen von MSBuild mit der Option `/t:Publish`. Standardmäßig erstellt dieser Befehl ein Verzeichnis unter dem Stammordner des Projekts, z. B. "ProjectDir\\bin\\Configuration\\app.publish". Beim Erstellen eines Azure-Projekts werden zwei Dateien generiert, die Paketdatei selbst und die zugehörige Konfigurationsdatei:

- Project.cspkg

- ServiceConfiguration.Zielprofil.cscfg

In der Standardeinstellung enthält jedes Azure-Projekt eine Dienstkonfigurationsdatei für lokale (Debugging-) Builds und eine für Cloudbuilds (Staging oder Produktion), aber Sie können Dienstkonfigurationsdateien nach Bedarf hinzufügen oder entfernen. Beim Erstellen eines Pakets in Visual Studio werden Sie gefragt, welche Dienstkonfigurationsdatei zusammen mit dem Paket enthalten sein soll. Beim Erstellen eines Pakets mithilfe von MSBuild ist standardmäßig die lokale Dienstkonfigurationsdatei enthalten. Damit eine andere Dienstkonfigurationsdatei enthalten ist, legen Sie die `TargetProfile`-Eigenschaft des MSBuild-Befehls folgendermaßen fest: `MSBuild /t:Publish /p:TargetProfile=ProfileName`.

Wenn Sie ein alternatives Verzeichnis zur Speicherung des Pakets und der Konfigurationsdateien verwenden möchten, legen Sie den Pfad mithilfe der Option `/p:PublishDir=Directory` fest, einschließlich des nachgestellten umgekehrten Schrägstrichs als Trennzeichen.

## Bereitstellung

Nach dem Erstellen des Pakets können Sie es in Azure bereitstellen. Ein Tutorial, das diesen Prozess veranschaulicht, finden Sie auf der Azure-Website. Weitere Informationen zur Automatisierung dieses Prozesses finden Sie unter [Kontinuierliche Bereitstellung für Clouddienste in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery).

<!---HONumber=August15_HO9-->
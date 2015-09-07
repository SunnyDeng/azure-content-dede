<properties 
   pageTitle="Upgrade von Projekten auf die aktuelle Version von Azure Tools"
	description="Erfahren Sie, wie Sie ein Upgrade von Projekten auf die aktuelle Version von Azure Tools für Visual Studio durchführen."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tglee"/>
<tags 
   ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="multiple"
	ms.date="08/13/2015"
	ms.author="kempb"/>

# Upgrade von Projekten auf die aktuelle Version von Azure Tools für Visual Studio

##Übersicht

Nach der Installation der aktuellen Version von Azure Tools (bzw. einer früheren Version ab 1.6) werden alle Projekte, die mit einer Version von Azure Tools vor Version 1.6 (November 2011) erstellt wurden, beim Öffnen automatisch aktualisiert. Wenn Sie Projekte mit Version 1.6 (November 2011) der Tools erstellt haben und diese Version weiterhin installiert ist, können Sie die betreffenden Projekte in der älteren Version öffnen und später entscheiden, ob sie aktualisiert werden sollen.

## Beim Upgrade durchgeführte Projektänderungen

Wenn ein Projekt automatisch aktualisiert oder angegeben wird, dass es aktualisiert werden soll, wird das Projekt so geändert, dass es mit den aktuellen Versionen bestimmter Assemblys fehlerfrei funktioniert. Einige der in diesem Abschnitt beschriebenen Eigenschaften werden ebenfalls geändert. Wenn Ihr Projekt andere Änderungen erfordert, um Kompatibilität mit der neueren Version der Tools zu gewährleisten, müssen Sie diese Änderungen manuell vornehmen.

- Die Datei "web.config" für Webrollen und die Datei "app.config" für Workerrollen werden aktualisiert, um einen Verweis auf die neuere Version von "Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll" einzufügen.

- Die Assemblys "Microsoft.WindowsAzure.StorageClient.dll", "Microsoft.WindowsAzure.Diagnostics.dll" und "Microsoft.WindowsAzure.ServiceRuntime.dll" werden auf die neuen Versionen aktualisiert.

- Veröffentlichungsprofile, die in der Azure-Projektdatei (CCPROJ-Format) gespeichert wurden, werden in eine separate Datei mit der AZUREPUBXML-Erweiterung im Unterverzeichnis **Publish** verschoben.

- Einige Eigenschaften im Veröffentlichungsprofil werden aktualisiert, um neue und geänderte Funktionen unterstützen. **AllowUpgrade** wird durch **DeploymentReplacementMethod** ersetzt, da bereitgestellte Clouddienste simultan oder inkrementell aktualisiert werden können.

- Die Eigenschaft **UseIISExpressByDefault** wurde hinzugefügt und auf "False" festgelegt, sodass der Webserver für das Debuggen nicht automatisch von Internetinformationsdienste (IIS) zu IIS Express geändert wird. IIS Express ist der Standardwebserver für mit den neueren Versionen der Tools erstellte Projekte.

- Wenn der Azure Cache-Dienst in einer oder mehreren Rollen des Projekts gehostet wird, werden einige Eigenschaften in der Dienstkonfiguration (CSCFG-Datei) und in der Dienstdefinition (CSDEF-Datei) geändert, wenn ein Projekt aktualisiert wird. Wenn vom Projekt das NuGet-Paket für den Azure Cache-Dienst verwendet wird, wird das Projekt auf die aktuelle Version des Pakets aktualisiert. Öffnen Sie die Datei "web.config", und prüfen Sie, ob die Clientkonfiguration während des Upgradevorgangs ordnungsgemäß beibehalten wurde. Wenn Sie die Verweise auf Azure Cache-Clientassemblys ohne das NuGet-Paket hinzugefügt haben, werden diese Assemblys nicht aktualisiert. Diese Verweise müssen manuell auf die neuen Versionen aktualisiert werden.

>[AZURE.IMPORTANT]Bei F#-Projekten müssen Sie die Verweise auf Assemblys von Azure manuell aktualisieren, sodass diese auf die Assemblys der neueren Versionen verweisen.

### So aktualisieren Sie ein Azure-Projekt auf die aktuelle Version

1. Installieren Sie die aktuelle Version von Azure Tools in der Installation von Visual Studio, die Sie für das aktualisierte Projekt verwenden möchten. Öffnen Sie dann das Projekt, das Sie aktualisieren möchten. Wenn das Projekt mit einer Azure Tools-Version vor 1.6 (November 2011) erstellt wurde, wird es automatisch auf die aktuelle Version aktualisiert. Wenn das Projekt mit der November 2011-Version erstellt wurde und diese Version noch installiert ist, wird das Projekt in dieser Version geöffnet.

1. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für den Projektknoten, wählen Sie **Eigenschaften**, und wählen Sie dann im angezeigten Dialogfeld die Registerkarte **Anwendung** aus.

    Auf der Registerkarte **Anwendung** wird die Tools-Version angezeigt, die dem Projekt zugeordnet ist. Wenn die aktuelle Version von Azure Tools angezeigt wird, wurde das Projekt bereits aktualisiert. Wenn Sie eine neuere Version der Tools installiert haben, als auf der Registerkarte angezeigt wird, wird die Schaltfläche **Aktualisieren** angezeigt.

1. Klicken Sie auf die Schaltfläche **Aktualisieren**, um ein Projekt auf die aktuelle Version der Tools zu aktualisieren.

1. Erstellen Sie das Projekt, und beheben Sie Fehler, die sich aus Änderungen an der API ergeben können. Informationen dazu, wie Sie den Code zur Anpassung an die neue Version ändern, finden Sie in der Dokumentation zur jeweiligen API.

## Ressourcen

[Azure Tools – Versionsverlauf](http://go.microsoft.com/fwlink/p/?LinkId=623548)

<!---HONumber=August15_HO9-->
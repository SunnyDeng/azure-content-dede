
<properties 
   pageTitle="Versionshinweise zu Azure SDK für .NET 2.8" 
   description="Versionshinweise zu Azure SDK für .NET 2.8" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="01/31/2016"
   ms.author="juliako"/>

# Azure SDK für .NET 2.8, 2.8.1 und 2.8.2

##Übersicht
 
Dieser Artikel enthält die Versionshinweise (einschließlich bekannter Probleme und wichtiger Änderungen) für das Azure SDK für .NET 2.8, 2.8.1 und 2.8.2.

Eine vollständige Liste der neuen Funktionen und in dieser Version vorgenommenen Aktualisierungen finden Sie in der Ankündigung zum [Azure SDK 2.8 für Visual Studio 2013 und Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) (in englischer Sprache).

##  Azure SDK für .NET 2.8

### Herunterladen von Azure SDK für .NET 2.8

[Azure SDK für .NET 2.8 für Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285)

[Azure SDK für .NET 2.8 für Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### Unterstützung von .NET 4.5.2 

####Bekannte Probleme

Mit Azure .NET SDK 2.8 können Sie .NET 4.5.2-Clouddienstpakete erstellen. Das .NET 4.5.2-Framework wird jedoch bis zur Gastbetriebssystemversion im Januar 2016 nicht in den Gastbetriebssystem-Standardimages installiert. Bis zu dieser Version wird das .NET 4.5.2-Framework über eine separate Gastbetriebssystemversion (November 2015-02) zur Verfügung gestellt. Informationen zur Veröffentlichung des Images finden Sie auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](../cloud-services/cloud-services-guestos-update-matrix.md). Wenn das Image „November 2015-02“ veröffentlicht wurde, können Sie dieses verwenden, indem Sie die Clouddienst-Konfigurationsdatei (CSCFG-Datei) entsprechend aktualisieren. Legen Sie in der Dienstkonfigurationsdatei das osVersion-Attribut des ServiceConfiguration-Elements auf die Zeichenfolge „WA-GUEST-OS-4.26\_201511-02“ fest. Wenn Sie sich für die Verwendung dieses Images anmelden, erhalten Sie keine automatischen Updates für das Gastbetriebssystem mehr. Um automatische Updates zu erhalten, muss osVersion auf „*“ festgelegt sein. .NET 4.5.2 ist ab Januar 2016 nur noch über automatische Updates verfügbar.

###Azure Data Factory

####Bekannte Probleme 

Während der Projekterstellung einer **Data Factory-Vorlage** mit Beispieldaten kann das Azure PowerShell-Skript fehlschlagen, wenn auf dem Computer eine höhere Azure PowerShell-Version als Version 0.9.8 installiert ist.

Um diese Art Projekt erfolgreich zu erstellen, müssen Sie [Azure PowerShell 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) installieren.


### Azure-Ressourcen-Manager-Tools 

####Wichtige Änderungen

Das mit dem Projekt „Azure-Ressourcengruppe“ bereitgestellte PowerShell-Skript wurde in dieser Version so aktualisiert, dass es mit den neuen Azure PowerShell-Cmdlets, Version 1.0, verwendet werden kann. Dieses neue Skript kann nicht in Visual Studio ausgeführt werden, wenn eine frühere Version des SDK als Version 2.8 verwendet wird.

Skripts aus Projekten, die in früheren Versionen des SDK erstellt wurden, können bei Verwendung der Version 2.8 des SDK nicht ausgeführt werden. Alle Skripts funktionieren weiterhin außerhalb von Visual Studio mit der entsprechenden Version der Azure PowerShell-Cmdlets.

Für das SDK 2.8 ist Version 1.0 der Azure PowerShell-Cmdlets erforderlich. Alle anderen Versionen des SDK erfordern Version 0.9.8 der Azure PowerShell-Cmdlets. Weitere Informationen finden Sie in [diesem](http://go.microsoft.com/fwlink/?LinkID=623011) Blog.

###Erweiterungen für Webtools

####Bekannte Probleme

Die folgenden bekannten Probleme werden in der folgenden Version behoben.

- Bewegungen in Cloud Explorer und Server-Explorer in Bezug auf App Service für Nichtproduktionsumgebungen (z. B. Kunden von Azure China und Azure Stack) funktionieren nicht. Kunden in diesen betroffenen Bereichen können das Veröffentlichungsprofil über das Azure-Portal herunterladen, um die Veröffentlichungsfunktion zu ermöglichen. In einer zukünftigen Version werden Probleme bei Gesten wie „Attach Debugger“ und „View Streaming Logs“ für Kunden von Azure China und Azure Stack behoben. 
- Kunden erhalten möglicherweise Fehlermeldungen bei der App Service-Erstellung, wenn sich die App Insights-Instanz, für die die Bereitstellung erfolgt, in einer anderen Region als im Osten der USA befindet. In diesen Fällen können die Veröffentlichungsszenarios durch Erstellen eines App Service im Portal und Herunterladen des Veröffentlichungsprofils ermöglicht werden. 

###Azure HDInsight-Tools

####Neue Updates

- Sie können die Hive-Abfrage im Cluster über HiveServer2 fast ohne Mehraufwand ausführen und die Auftragsprotokolle in Echtzeit anzeigen.
- Mithilfe der neuen Aufgabenausführungsansicht für Hive können Sie Aufträge eingehender betrachten, mehr Details anzeigen und mögliche Probleme ermitteln.

Weitere Informationen finden Sie unter [Azure SDK 2.8 für Visual Studio 2013 und Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## Azure SDK für .NET 2.8.1

### Bekannte Probleme bei Visual Studio 2013 und Visual Studio 2015
 
1. Für ausgelöste Webaufträge, die in Slots veröffentlicht werden, wird eine Fehlermeldung angezeigt und es wird kein Zeitplan festgelegt. Der Webauftrag wird allerdings an Azure weitergegeben. Kunden, die einen geplanten Auftrag benötigen, können dann das Azure-Portal zum Einrichten des Webauftrags verwenden. 
2. Bei Python-Kunden treten möglicherweise Debugger-Probleme auf. Das Service-Team führt dafür ein Update ein. Wenn Kunden betroffen sind, informieren Sie Microsoft dennoch in den Foren, im Ankündigungsblog oder im Kommentarbereich der Versionshinweise. 
3. In bestimmten Regionen (z. B. Südindien) treten Fehler bei der App Service-Bereitstellung auf. Dies gilt auch für das Portal, und Kunden, bei denen dieses Problem auftritt, können das Azure-Portal nutzen, um den Zugriff zum Veröffentlichen in diesen Georegionen anzufordern. Nachdem die Kunden den Zugriff auf diese Regionen angefordert haben, sollte die Bereitstellung des Azure-Portals funktionieren. 

##Azure SDK für .NET 2.8.2

Nach der Installation von Version 2.8.2 dieses Tools kann es vorkommen, dass Sie folgende Probleme feststellen.

- Wenn Sie Windows 10 verwenden und Internet Explorer nicht installiert ist, kann es vorkommen, dass Ihnen die Fehlermeldung „Internet Explorer konnte nicht gefunden werden“ angezeigt wird. Installieren Sie Internet Explorer über das Dialogfeld „Windows-Komponenten hinzufügen/entfernen“.

Tritt dieses Problem bei Ihnen auf, teilen Sie uns dies bitte über die Funktion „Lächeln senden“ mit.

Weitere Informationen finden Sie in [diesem Beitrag](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/).
##Andere Updates

Andere Updates finden Sie im [Ankündigungsartikel zu Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##Siehe auch

[Ankündigungsbeitrag zu Azure SDK 2.8 (in englischer Sprache)](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Unterstützungs- und Deaktivierungsinformationen zum Azure SDK für .NET und APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx)

<!---HONumber=AcomDC_0204_2016-->
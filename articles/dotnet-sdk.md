<properties pageTitle="Was ist das Azure .NET SDK?" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure SDK for .NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Was ist das Azure-SDK für .NET?

Das Azure-SDK für .NET umfasst einen Satz Visual Studio-Tools, Befehlszeilen-Tools, Laufzeitbinärdateien und Clientbibliotheken, mit denen Sie Anwendungen, die in Azure ausgeführt werden, entwickeln, testen und bereitstellen können. In diesem Artikel erfahren Sie, was in der Installation des SDK enthalten ist. Sie können das SDK von der [Azure Downloads Seite](/de-de/downloads/) herunterladen. 

Das Azure-SDK für .NET enthält außerdem Clientbibliotheken für die Nutzung von Azure-Diensten. Diese Bibliotheken müssen Sie separat über [NuGet](http://go.microsoft.com/fwlink/?LinkId=510472) installieren.

## Inhaltsverzeichnis

- [Bei der Installation von Azure-SDK für .NET enthalten](#included)
- [Bei der Installation von Azure-SDK für .NET nicht enthalten](#notincluded)
- [Häufig gestellte Fragen](#faq)
- [Ressourcen](#resources)

##<a id="included"></a>Im Azure-SDK für .NET enthalten

Das Azure-SDK für .NET installiert folgende Produkte:

- [Visual Studio Express für das Web](#vwd)
- [Microsoft ASP.NET und Webtools für Visual Studio](#wte)
- [Microsoft Azure-Tools für Microsoft Visual Studio](#tools)
- [Microsoft Azure-Entwicklertools](#auth)
- [Microsoft Azure-Emulator](#emulator)
- [Microsoft Azure-Speicheremulator](#stgemulator)
- [Microsoft Azure-Speichertools](#stgtools)
- [Microsoft Azure-Bibliotheken für .NET](#libraries)
- [Azure Publishing-Add-On LightSwitch für Visual Studio](#ls)

###<a id="vwd"></a>Visual Studio Express für das Web

Wenn Visual Studio nicht auf Ihrem Computer installiert ist, installiert das SDK [Visual Studio Express for Web](http://www.visualstudio.com/de-de/products/visual-studio-express-vs.aspx). 
 
###<a id="wte"></a>Microsoft ASP.NET und Webtools für Visual Studio

Mit diesen Anleitungen können Sie mit Azure-Websites arbeiten:

* [Veröffentlichen von Webprojekten auf Azure-Websites](../web-sites-dotnet-get-started/).
* [Veröffentlichen von Konsolenanwendungsprojekten mit Azure-WebJobs](../websites-dotnet-deploy-webjobs/).
* [Erstellen einer Azure-Website und Erstellen von SQL-Datenbankressourcen bei der Erstellung eines neuen Webprojekts oder der Veröffentlichung eines Webprojekts](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
* [Erstellen von PowerShell-Bereitstellungsskripts während der Erstellung neuer Websites](http://msdn.microsoft.com/de-de/library/dn642480.aspx).
* [Azure Websites im Server Explorer verwalten und Fehler beheben](../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement).
* [Ausführen im Debugmodus aus der Ferne für Websites und WebJobs](../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug). 

>[WACOM.NOTE] Sie müssen das Azure-SDK für .NET nicht installieren, um diese Funktionen nutzen zu können. Sie sind auch in Updates von Visual Studio enthalten. 

###<a id="tools"></a>Microsoft Azure-Tools für Microsoft Visual Studio

Mit diesen Anleitungen können Sie mit Azure-Clouddiensten und virtuellen Azure-Computern arbeiten:

* [Erstellen, Öffnen und Veröffentlichen von Clouddienstprojekten](../cloud-services-dotnet-get-started/).
* [Erstellen von Bereitstellungspaketen für Clouddienstprojekte](http://msdn.microsoft.com/de-de/library/ff683672.aspx).
* [Erstellen von virtuellen Azure-Maschinen während der Erstellung neuer Webprojekte](../virtual-machines-dotnet-create-visual-studio-powershell/).
* [Erstellen von PowerShell-Skripts während der Erstellung neuer virtueller Maschinen](http://msdn.microsoft.com/de-de/library/dn642480.aspx).
* [Anzeigen und Verwalten von Einstellungen für Clouddienstprojekte in Projekteigenschaftsfenstern in Visual Studio](http://msdn.microsoft.com/de-de/library/ee405486.aspx).
* Anzeigen und Verwalten von [Cloud-Diensten](http://msdn.microsoft.com/de-de/library/ff683675.aspx), [virtuellen Computern](http://msdn.microsoft.com/de-de/library/jj131259.aspx) und [Service Bus](http://msdn.microsoft.com/de-de/library/jj149828.aspx) in Server Explorer. 
* [Ausführen im Debugmodus aus der Ferne für Clouddienste und virtuelle Maschinen](http://msdn.microsoft.com/de-de/library/ff683670.aspx).

###<a id="auth"></a>Microsoft Azure-Entwicklertools

Dies umfasst Folgendes:

* Das [CSPack-Befehlszeilentooltool](http://msdn.microsoft.com/de-de/library/gg432988.aspx) für die Erstellung von Bereitstellungspaketen.
* Das [CSEncrypt-Befehlszeiletool ](http://msdn.microsoft.com/de-de/library/hh404001.aspx) für die Verschlüsselung von Kennwörtern, die für den Zugriff auf Rolleninstanzen für Cloud-Dienste über eine Remotedesktopverbindung verwendet werden.
* Laufzeitbinärdateien, die Clouddienstprojekte benötigen, um mit deren Laufzeitumgebung zu kommunizieren und Diagnosen zu erstellen. Diese Binärdateien sind in NuGet-Paketen nicht verfügbar.

###<a id="emulator"></a>Microsoft Azure-Emulator

Der [Azure Emulator](http://msdn.microsoft.com/de-de/library/dn339018.aspx) simuliert die Clouddienstumgebung.So können Sie Clouddienstprojekte vor der Bereitstellung in Azure lokal auf Ihrem Computer testen.

###<a id="stgemulator"></a>Microsoft Azure-Speicheremulator

Der [Azure Speicheremulator](http://msdn.microsoft.com/de-de/library/hh403989.aspx) nutzt eine SQL-Serverinstanz und das lokale Dateisystem, um Azure-Speicher (Warteschlangen, Tabellen, Blobs) zu simulieren. 

###<a id="stgtools"></a>Microsoft Azure-Speichertools

Dies installiert [AzCopy](http://aka.ms/AzCopy). Dabei handelt es sich um ein Befehlszeilentool, mit dem Sie Daten in ein und aus einem Azure-Speicherkonto übertragen können.

###<a id="libraries"></a>Microsoft Azure-Bibliotheken für .NET

Dies umfasst Folgendes:

* NuGet-Pakete für den Azure-Speicher, Service Bus und Zwischenspeicherung, die auf Ihrem Computer gespeichert werden, sodass Visual Studio offline neue Clouddienstprojekte erstellen kann.
* Ein Plug-In für Visual Studio, mit dem [In-Role Cache](http://msdn.microsoft.com/de-de/library/dn386103.aspx)-Projekte lokal in Visual Studio ausgeführt werden können. 

###<a id="ls"></a>Azure Publishing-Add-On LightSwitch für Visual Studio

Mit diesem Add-On können Sie [LightSwitch-Projekte auf Azure Websites veröffentlichen](http://msdn.microsoft.com/de-de/library/jj131261.aspx). Das LightSwitch-Add-On ist in Updates von Visual Studio und dem Azure-SDK für .NET enthalten. Mit der Installation des SDK stellen Sie sicher, dass Sie über die aktuelle Version des Add-Ons verfügen. 

##<a id="notincluded"></a>Bei der Installation von Azure-SDK für .NET nicht enthalten

Sie benötigen möglicherweise einige Elemente für die Entwicklung in Azure, die bei der Installation des SDK nicht enthalten sind. Die wichtigsten Elemente sind:

* [Clientbibliotheken](http://go.microsoft.com/fwlink/?LinkId=510472). 

	Das Azure-SDK umfasst Clientbibliotheken für die Nutzung von Azure-Diensten. Es werden jedoch nicht alle installiert, wenn Sie das SDK installieren. Benötigt Ihre Anwendung eine Clientbibliothek, die nicht vom SDK installiert wird, können Sie diese auf [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) herunterladen. Verwendet Ihre Anwendung eine Clientbibliothek, die vom SDK installiert wird, ist es von Vorteil, ein Update auf die neueste Version von NuGet.org herunterzuladen.

  	**Lokale Kopien von Clientbibliotheken.** Das Azure-SDK für .NET kopiert die NuGet-Pakete für einige Azure-Clientbibliotheken, wie Speicher, Service Bus und Zwischenspeicherung, auf Ihren Computer. Diese Clientbibliotheken werden automatisch in neue Clouddienstprojekte integriert. Die lokalen NuGet-Pakete ermöglichen es Visual Studio, Projekte auch dann zu erstellen, wenn keine Verbindung zum Internet besteht. Clientbibliotheken werden im Allgemeinen häufiger aktualisiert als neue SDK-Versionen veröffentlicht werden. Daher sind die Clientbibliotheken auf NuGet.org meist aktueller als die im SDK enthaltenen. 

	**Projektvorlagen, die Clientbibliotheken enthalten.** Nur Projektvorlagen für den [Azure-Clouddienst](../cloud-services-dotnet-get-started/) und den [Azure Mobile Service](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/) enthalten automatisch einige Clientbibliotheken. Installieren Sie für andere Bibliotheken oder Vorlagen die erforderlichen [NuGet-Pakete mit Clientbibliotheken](http://go.microsoft.com/fwlink/?LinkId=510472).

* [Azure PowerShell](../install-configure-powershell/). 

	Mit Azure PowerShell können Sie die [Entwicklung und Bereitstellung von Azure-Umgebungen automatisieren.](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* [Projektvorlagen für Azure Mobile Service](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/).

	Mobile Service-Vorlagen sind nur in Visual Studio 2013 Update 2 und höher verfügbar. Sie sind nicht in Visual Studio 2012 oder früheren Versionen und auch nicht in Visual Studio 2013 Update 1 verfügbar. Auch dann nicht, wenn Sie das Azure-SDK für .NET installieren.

##<a id="faq"></a>Häufig gestellte Fragen

- [Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?](#azinvs)
- [Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?](#clientlib)
- [Wo finde ich ältere Versionen des Azure-SDK für .NET?](#olderversions)
- [Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?](#lifecycle)
- [Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?](#guestos)

###<a id="azinvs"></a>Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?

Es wird empfohlen, das SDK zu installieren, wenn Sie für die Azure-Entwicklung die neuesten Tools verwenden möchten. Wenn Sie das SDK nicht installieren möchten, stellen Sie sicher, dass folgende Bedingungen erfüllt sind:

* Sie haben das neueste Update von [Visual Studio Update](http://www.visualstudio.com/de-de/downloads/download-visual-studio-vs#DownloadFamilies_5) installiert.
* Sie entwickeln nur für Azure-Websites oder den mobilen Dienst, nicht für Clouddienste oder virtuelle Computer.
* Ihre Anwendung nutzt den Speicher nicht bzw. nutzt den Speicher, benötigt jedoch nicht den Speicheremulator oder das AzCopy-Tool.

###<a id="clientlib"></a>Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?

Das SDK installiert Clientbibliotheken, damit Sie Clouddienstprojekte auch dann erstellen können, wenn keine Verbindung zum Internet besteht. Aktuelle Clientbibliotheken sind in NuGet-Paketen unter [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) erhältlich. Weitere Informationen finden Sie unter [Bei der Installation von Azure-SDK für .NET nicht enthalten](#notincluded) weiter vorne in diesem Dokument.

###<a id="olderversions"></a>Wo finde ich ältere Versionen des Azure-SDK für .NET?

Ältere Versionen finden Sie auf der Downloadseite für [Azure SDK füor .NET](/de-de/downloads/archive-net-downloads/). 

###<a id="lifecycle"></a>Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?

Informationen finden Sie unter [Support Lifecycle-Richtlinien für Microsoft Azure-Clouddienste](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?

Informationen finden Sie unter [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](http://msdn.microsoft.com/de-de/library/ee924680.aspx).



##<a id="resources"></a>Ressourcen

Das Azure-SDK für .NET oder Clientbibliotheken können Sie auf der [Azure Download-Seite](/de-de/downloads/) herunterladen.

Den Quellcode des Azure-SDK für .NET, einschließlich Clientbibliotheken, finden Sie unter [GitHub.com/Azure](https://github.com/azure/).

Referenzdokumentation für Azure-Clientbibliotheken finden Sie unter [Azure .NET-Referenz](/de-de/develop/net/reference/). 

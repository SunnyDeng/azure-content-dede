<properties 
	pageTitle="Was ist das Azure .NET SDK?" 
	description="Erfahren Sie, was im Azure .NET SDK enthalten ist." 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="tdykstra"/>

# Was ist das Azure-SDK für .NET?

## Übersicht

Das Azure-SDK für .NET umfasst einen Satz Visual Studio-Tools, Befehlszeilen-Tools, Laufzeitbinärdateien und Clientbibliotheken, mit denen Sie Anwendungen, die in Azure ausgeführt werden, entwickeln, testen und bereitstellen können. In diesem Artikel erfahren Sie, was in der Installation des SDK enthalten ist. Sie können das SDK von der Seite [Azure Downloads](/downloads/) herunterladen.

Das Azure-SDK für .NET enthält außerdem [Clientbibliotheken für die Nutzung von Azure-Diensten](http://go.microsoft.com/fwlink/?LinkId=510472). Diese Bibliotheken müssen Sie separat über NuGet installieren.

##<a id="included"></a>Im Azure-SDK für .NET enthalten

Das Azure-SDK für .NET installiert folgende Produkte:

- [Visual Studio Express für das Web installiert](#vwd)
- [Microsoft ASP.NET und Webtools für Visual Studio](#wte)
- [Microsoft Azure-Tools für Microsoft Visual Studio](#tools)
- [Microsoft Azure-Entwicklertools](#auth)
- [Microsoft Azure-Emulator](#emulator)
- [Microsoft Azure-Speicheremulator](#stgemulator)
- [Microsoft Azure-Speichertools](#stgtools)
- [Microsoft Azure-Bibliotheken für .NET](#libraries)
- [HDInsight-Tools für Visual Studio](#hdinsight) und [Microsoft Hive ODBC-Treiber](#hdinsight)
- [Microsoft Azure Mobile App SDK V1.0](#mobile)
- [Microsoft Azure PowerShell](#ps)

###<a id="vwd"></a>Visual Studio Express für das Web installiert

Wenn Visual Studio nicht auf Ihrem Computer installiert ist, installiert das SDK [Visual Studio Express für das Web](http://www.visualstudio.com/products/visual-studio-express-vs.aspx).
 
###<a id="wte"></a>Microsoft ASP.NET und Webtools für Visual Studio

Mit diesen Anleitungen können Sie mit Azure-Websites arbeiten:

* [Veröffentlichen von Webprojekten auf Azure-Websites](web-sites-dotnet-get-started.md)
* [Veröffentlichen von Konsolenanwendungsprojekten mit Azure-WebJobs](websites-dotnet-deploy-webjobs.md)
* [Erstellen einer Azure-Website und Erstellen von SQL-Datenbankressourcen bei der Erstellung eines neuen Webprojekts oder der Veröffentlichung eines Webprojekts](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [Erstellen von PowerShell-Bereitstellungsskripts während der Erstellung neuer Websites](http://msdn.microsoft.com/library/dn642480.aspx)
* [Verwalten von Azure-Websites und Beheben von Problemen im Server-Explorer](web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement)
* [Ausführen im Debugmodus aus der Ferne für Websites und WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) 

>[AZURE.NOTE]Sie müssen das Azure-SDK für .NET nicht installieren, um diese Funktionen nutzen zu können. Sie sind auch in Updates von Visual Studio enthalten.

###<a id="tools"></a>Microsoft Azure-Tools für Microsoft Visual Studio

Mit diesen Anleitungen können Sie mit Azure-Ressourcen arbeiten, vor allem mit Clouddiensten und virtuellen Computern:

* [Erstellen, Öffnen und Veröffentlichen von Clouddienstprojekten](cloud-services-dotnet-get-started.md)
* [Erstellen von Bereitstellungspaketen für Clouddienstprojekte](http://msdn.microsoft.com/library/ff683672.aspx)
* [Erstellen von virtuellen Azure-Computern während der Erstellung neuer Webprojekte](virtual-machines-dotnet-create-visual-studio-powershell.md)
* [Erstellen von PowerShell-Skripts während der Erstellung neuer virtueller Computer](http://msdn.microsoft.com/library/dn642480.aspx)
* [Anzeigen und Verwalten von Einstellungen für Clouddienstprojekte in Projekteigenschaftsfenstern in Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx)
* Anzeigen und Verwalten von [Clouddiensten](http://msdn.microsoft.com/library/ff683675.aspx), [virtuellen Computern](http://msdn.microsoft.com/library/jj131259.aspx) und [Service Bus](http://msdn.microsoft.com/library/jj149828.aspx) im Server Explorer 
* [Ausführen im Debugmodus aus der Ferne für Clouddienste und virtuelle Computer](http://msdn.microsoft.com/library/ff683670.aspx)
* [Automatisieren der Ressourcenbereitstellung mithilfe von Azure-Ressourcengruppen-Bereitstellungsprojekten](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="auth"></a>Microsoft Azure-Entwicklertools

Dies umfasst Folgendes:

* Das [CSPack-Befehlszeilentool](http://msdn.microsoft.com/library/gg432988.aspx) für die Erstellung von Bereitstellungspaketen
* Das [CSEncrypt-Befehlszeilentool](http://msdn.microsoft.com/library/hh404001.aspx) für die Verschlüsselung von Kennwörtern, die für den Zugriff auf Rolleninstanzen für Clouddienste über eine Remotedesktopverbindung verwendet werden.
* Laufzeitbinärdateien, die Clouddienstprojekte benötigen, um mit deren Laufzeitumgebung zu kommunizieren und Diagnosen zu erstellen. Diese Binärdateien sind in NuGet-Paketen nicht verfügbar.

###<a id="emulator"></a>Microsoft Azure-Emulator

Der [Azure-Emulator](http://msdn.microsoft.com/library/dn339018.aspx) simuliert die Clouddienstumgebung. So können Sie Clouddienstprojekte vor der Bereitstellung in Azure lokal auf Ihrem Computer testen.

###<a id="stgemulator"></a>Microsoft Azure-Speicheremulator

Der [Azure-Speicheremulator](http://msdn.microsoft.com/library/hh403989.aspx) nutzt eine SQL-Serverinstanz und das lokale Dateisystem, um Azure-Speicher (Warteschlangen, Tabellen, Blobs) zu simulieren. Dadurch ist lokales Testen möglich.

###<a id="stgtools"></a>Microsoft Azure-Speichertools

Dies installiert [AzCopy](http://aka.ms/AzCopy). Dabei handelt es sich um ein Befehlszeilentool, mit dem Sie Daten in ein und aus einem Azure-Speicherkonto übertragen können.

###<a id="libraries"></a>Microsoft Azure-Bibliotheken für .NET

Dies umfasst Folgendes:

* NuGet-Pakete für den Azure-Speicher, Service Bus und Zwischenspeicherung, die auf Ihrem Computer gespeichert werden, sodass Visual Studio offline neue Clouddienstprojekte erstellen kann.
* Ein Plug-In für Visual Studio, mit dem [In-Role-Cache](http://msdn.microsoft.com/library/dn386103.aspx)-Projekte lokal in Visual Studio ausgeführt werden können. 

###<a id="hdinsight"></a>HDInsight-Tools für Visual Studio und Microsoft Hive ODBC-Treiber

Mit HDInsight-Tools im Server-Explorer können Sie in Hive-Datenbanken und verknüpften Speicherkonten für HDInsight-Cluster navigieren, Tabellen erstellen sowie Hive-Abfragen erstellen und übermitteln. Weitere Informationen finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="mobile">Microsoft Azure Mobile App SDK

Tools für die Arbeit mit [Azure App Service Mobile Apps](app-service-mobile-value-prop-preview.md).

###<a id="ps"></a>Microsoft Azure PowerShell

Mit Azure PowerShell können Sie [die Entwicklung und Bereitstellung von Azure-Umgebungen automatisieren](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

##<a id="notincluded"></a>Bei der Installation von Azure-SDK für .NET nicht enthalten

Sie benötigen möglicherweise einige Elemente für die Entwicklung in Azure, die bei der Installation des SDK nicht enthalten sind. Die wichtigsten Elemente sind:

* [Clientbibliotheke](http://go.microsoft.com/fwlink/?LinkId=510472). 

	Das Azure-SDK umfasst Clientbibliotheken für die Nutzung von Azure-Diensten. Es werden jedoch nicht alle installiert, wenn Sie das SDK installieren. Benötigt Ihre Anwendung eine Clientbibliothek, die nicht vom SDK installiert wird, können Sie diese auf [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) herunterladen. Verwendet Ihre Anwendung eine Clientbibliothek, die vom SDK installiert wird, ist es von Vorteil, ein Update auf die neueste Version von NuGet.org herunterzuladen.

  	**Lokale Kopien von Clientbibliotheken.** Das Azure-SDK für .NET kopiert die NuGet-Pakete für einige Azure-Clientbibliotheken, wie Speicher, Service Bus und Zwischenspeicherung, auf Ihren Computer. Diese Clientbibliotheken werden automatisch in neue Clouddienstprojekte integriert. Die lokalen NuGet-Pakete ermöglichen es Visual Studio, Projekte auch dann zu erstellen, wenn keine Verbindung zum Internet besteht. Clientbibliotheken werden im Allgemeinen häufiger aktualisiert als neue SDK-Versionen veröffentlicht werden. Daher sind die Clientbibliotheken auf NuGet.org meist aktueller als die im SDK enthaltenen.

	**Projektvorlagen, die Clientbibliotheken enthalten.** Nur Projektvorlagen für den [Azure-Clouddienst](cloud-services-dotnet-get-started.md) und [Azure Mobile Service](mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md) enthalten automatisch einige Clientbibliotheken. Installieren Sie für andere Bibliotheken oder Vorlagen die erforderlichen [NuGet-Pakete mit Clientbibliotheken](http://go.microsoft.com/fwlink/?LinkId=510472).

* [Projektvorlagen für Azure Mobile Service](mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md).

	Vorlagen für den mobilen Dienst sind nur in Visual Studio 2013 Update 2 und höher verfügbar. Sie sind nicht in Visual Studio 2012 oder früheren Versionen und auch nicht in Visual Studio 2013 Update 1 verfügbar. Auch dann nicht, wenn Sie das Azure-SDK für .NET installieren.

##<a id="faq"></a>Häufig gestellte Fragen

- [Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?](#azinvs)
- [Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?](#clientlib)
- [Wo finde ich ältere Versionen des Azure-SDK für .NET?](#olderversions)
- [Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?](#lifecycle)
- [Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?](#guestos)
- [Wie deinstalliere ich das Azure-SDK für .NET?](#uninstall)

###<a id="azinvs"></a>Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?

Es wird empfohlen, das SDK zu installieren, wenn Sie für die Azure-Entwicklung die neuesten Tools verwenden möchten. Wenn Sie das SDK nicht installieren möchten, stellen Sie sicher, dass folgende Bedingungen erfüllt sind:

* Sie haben das neueste [Update von Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5) installiert.
* Sie entwickeln nur für Azure-Websites oder den mobilen Dienst, nicht für Clouddienste oder virtuelle Computer.
* Ihre Anwendung nutzt den Speicher nicht bzw. nutzt den Speicher, benötigt jedoch nicht den Speicheremulator oder das AzCopy-Tool.

###<a id="clientlib"></a>Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?

Das SDK installiert Clientbibliotheken, damit Sie Clouddienstprojekte auch dann erstellen können, wenn keine Verbindung zum Internet besteht. Aktuelle Clientbibliotheken sind in NuGet-Paketen unter [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) erhältlich. Weitere Informationen finden Sie weiter oben in diesem Dokument unter [Bei der Installation von Azure-SDK für .NET nicht enthalten](#notincluded).

###<a id="olderversions"></a>Wo finde ich ältere Versionen des Azure-SDK für .NET?

Ältere Versionen finden Sie auf der Downloadseite für das [Azure-SDK für .NET](/downloads/archive-net-downloads/).

###<a id="lifecycle"></a>Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?

Informationen finden Sie unter [Support Lifecycle-Richtlinien für Microsoft Azure-Clouddienste](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?

Informationen finden Sie unter [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Wie deinstalliere ich das Azure-SDK für .NET?

Jedes in diesem Artikel unter [Im Azure-SDK für .NET enthalten](#included) gezeigtes Element ist ein separates Programm in der Liste der installierten Programme in der Windows-Systemsteuerung unter **Programme und Funktionen**. Es gibt keine Möglichkeit, diese als Gruppe zu deinstallieren. Sie müssen jedes Programm einzeln deinstallieren.

Wenn Sie das Azure SDK für .NET bereits installiert haben und eine neue Version installieren, ist es im Allgemeinen nicht erforderlich, die alte Version zu deinstallieren. In den meisten Fällen aktualisiert die SDK-Installation ein vorhandenes Programm, statt ein neues hinzuzufügen und das alte beizubehalten.

Wenn Sie nicht mehr benötigte Reste einer früheren Version entfernen möchten, deinstallieren Sie nur Programme, die die ältere Versionsnummer angeben, und deinstallieren Sie sie nur, wenn eine neuere Version desselben Programms vorhanden ist. Wenn Sie beispielsweise von 2.5 auf 2.6 aktualisieren, werden möglicherweise beide Versionen (2.5 und 2.6) von "Microsoft Azure Tools für Microsoft Visual Studio 2013" angezeigt, und Sie können die Version 2.5 deinstallieren. Aber möglicherweise wird nur die Version 2.5 von "Microsoft Azure Authoring Tools" angezeigt, und eine Deinstallation wäre nicht sicher.

Beachten Sie, dass die in den Programmtiteln unter **Programme und Funktionen** angezeigten Versionsnummern irreführend sein können. Beispielsweise enthält SDK Version 2.6 "Microsoft Azure Mobile App SDK V1. 0", wenn Sie das SDK für Visual Studio 2013 installieren, und "Microsoft Azure Mobile App SDK V2. 0", wenn Sie das SDK für Visual Studio 2015 installieren; die Version ist in diesem Fall nicht die SDK-Version, sondern ein Indikator dafür, für welche Visual Studio-Version das Programm gilt.

In diesem Artikel wird nicht jedes Programm aufgeführt, das in jeder früheren Version des Azure-SDK enthalten war. Es gibt andere Programme, die Sie aus früheren SDK-Versionen deinstallieren können, da frühere SDK-Versionen manchmal Programme enthalten haben, die in späteren Versionen nicht mehr vorhanden sind. Version 2.5 installiert z. B. "Azure-Ressourcen-Manager-Tools für Visual Studio", das aber nicht in der Liste dieses Artikels vorkommt, da es nicht mehr als separates Programm in **Programme und Funktionen** angezeigt wird. Dieser Artikel listet nur Programme auf, die im Azure-SDK für .NET Version 2.6 enthalten sind.

> **Hinweis:** Einige der Programme, die das SDK enthält, können auch separat in anderen Kontexten installiert werden und sind möglicherweise erforderlich, auch wenn Sie das vollständige SDK nicht benötigen. Dasselbe kann auch für Programme gelten, die von früheren SDK-Versionen installiert wurden, aber in späteren SDK-Versionen nicht mehr vorhanden sind. Wenn Sie Programme deinstallieren, dürfen Sie auf keinen Fall etwas entfernen, das noch auf Ihrem Computer benötigt wird.



##<a id="versions"></a>Versionen

Um festzustellen, welche Version aktuell ist, oder um ältere Versionen herunterzuladen, finden Sie entsprechende Informationen auf der Seite [Azure-SDK für .NET-Versionsverlauf](/downloads/archive-net-downloads/).

##<a id="resources"></a>Ressourcen

Das aktuelle Azure-SDK für .NET oder Clientbibliotheken können Sie auf der Azure Download-Seite herunterladen.

Quellcode des Azure-SDK für .NET, einschließlich Clientbibliotheken, finden Sie unter [GitHub.com/Azure](https://github.com/azure/).

Referenzdokumentation für Azure-Clientbibliotheken finden Sie unter [Azure .NET-Referenz](/documentation/api/).

<!---HONumber=July15_HO4-->
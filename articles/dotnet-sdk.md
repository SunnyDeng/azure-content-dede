<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Was ist das Azure-SDK für .NET?

Das Azure-SDK für .NET umfasst einen Satz Visual Studio-Tools, Befehlszeilen-Tools, Laufzeitbinärdateien und Clientbibliotheken, mit denen Sie Anwendungen, die in Azure ausgeführt werden, entwickeln, testen und bereitstellen können. In diesem Artikel erfahren Sie, was in der Installation des SDK enthalten ist. Sie können das SDK von der Seite [Azure Downloads][Azure Downloads] herunterladen.

Das Azure-SDK für .NET enthält außerdem Clientbibliotheken für die Nutzung von Azure-Diensten. Diese Bibliotheken müssen Sie separat über [NuGet][NuGet] installieren.

## Inhaltsverzeichnis

-   [Bei der Installation von Azure-SDK für .NET enthalten][Bei der Installation von Azure-SDK für .NET enthalten]
-   [Bei der Installation von Azure-SDK für .NET nicht enthalten][Bei der Installation von Azure-SDK für .NET nicht enthalten]
-   [Häufig gestellte Fragen][Häufig gestellte Fragen]
-   [Ressourcen][Ressourcen]

## <span id="included"></span></a>Im Azure-SDK für .NET enthalten

Das Azure-SDK für .NET installiert folgende Produkte:

-   [Visual Studio Express für das Web][Visual Studio Express für das Web]
-   [Microsoft ASP.NET und Webtools für Visual Studio][Microsoft ASP.NET und Webtools für Visual Studio]
-   [Microsoft Azure-Tools für Microsoft Visual Studio][Microsoft Azure-Tools für Microsoft Visual Studio]
-   [Microsoft Azure-Entwicklertools][Microsoft Azure-Entwicklertools]
-   [Microsoft Azure-Emulator][Microsoft Azure-Emulator]
-   [Microsoft Azure-Speicheremulator][Microsoft Azure-Speicheremulator]
-   [Microsoft Azure-Speichertools][Microsoft Azure-Speichertools]
-   [Microsoft Azure-Bibliotheken für .NET][Microsoft Azure-Bibliotheken für .NET]
-   [Azure Publishing-Add-On LightSwitch für Visual Studio][Azure Publishing-Add-On LightSwitch für Visual Studio]

### <span id="vwd"></span></a>Visual Studio Express für das Web

Wenn Visual Studio nicht auf Ihrem Computer installiert ist, installiert das SDK [Visual Studio Express für das Web][1].

### <span id="wte"></span></a>Microsoft ASP.NET und Webtools für Visual Studio

Mit diesen Anleitungen können Sie mit Azure-Websites arbeiten:

-   [Veröffentlichen von Webprojekten auf Azure-Websites][Veröffentlichen von Webprojekten auf Azure-Websites]
-   [Veröffentlichen von Konsolenanwendungsprojekten mit Azure-WebJobs][Veröffentlichen von Konsolenanwendungsprojekten mit Azure-WebJobs]
-   [Erstellen einer Azure-Website und Erstellen von SQL-Datenbankressourcen bei der Erstellung eines neuen Webprojekts oder der Veröffentlichung eines Webprojekts][Erstellen einer Azure-Website und Erstellen von SQL-Datenbankressourcen bei der Erstellung eines neuen Webprojekts oder der Veröffentlichung eines Webprojekts]
-   [Erstellen von PowerShell-Bereitstellungsskripts während der Erstellung neuer Websites][Erstellen von PowerShell-Bereitstellungsskripts während der Erstellung neuer Websites]
-   [Verwalten von Azure-Websites und Beheben von Problemen im Server-Explorer][Verwalten von Azure-Websites und Beheben von Problemen im Server-Explorer]
-   [Ausführen im Debugmodus aus der Ferne für Websites und WebJobs][Ausführen im Debugmodus aus der Ferne für Websites und WebJobs]

> [WACOM.NOTE] Sie müssen das Azure-SDK für .NET nicht installieren, um diese Funktionen nutzen zu können. Sie sind auch in Updates von Visual Studio enthalten.

### <span id="tools"></span></a>Microsoft Azure-Tools für Microsoft Visual Studio

Mit diesen Anleitungen können Sie mit Azure-Clouddiensten und virtuellen Azure-Computern arbeiten:

-   [Erstellen, Öffnen und Veröffentlichen von Clouddienstprojekten][Erstellen, Öffnen und Veröffentlichen von Clouddienstprojekten]
-   [Erstellen von Bereitstellungspaketen für Clouddienstprojekte][Erstellen von Bereitstellungspaketen für Clouddienstprojekte]
-   [Erstellen von virtuellen Azure-Computern während der Erstellung neuer Webprojekte][Erstellen von virtuellen Azure-Computern während der Erstellung neuer Webprojekte]
-   [Erstellen von PowerShell-Skripts während der Erstellung neuer virtueller Computer][Erstellen von PowerShell-Bereitstellungsskripts während der Erstellung neuer Websites]
-   [Anzeigen und Verwalten von Einstellungen für Clouddienstprojekte in Projekteigenschaftsfenstern in Visual Studio][Anzeigen und Verwalten von Einstellungen für Clouddienstprojekte in Projekteigenschaftsfenstern in Visual Studio]
-   Anzeigen und Verwalten von [Clouddiensten][Clouddiensten], [virtuellen Computern][virtuellen Computern] und [Service Bus][Service Bus] im Server Explorer
-   [Ausführen im Debugmodus aus der Ferne für Clouddienste und virtuelle Computer][Ausführen im Debugmodus aus der Ferne für Clouddienste und virtuelle Computer]

### <span id="auth"></span></a>Microsoft Azure-Entwicklertools

Dies umfasst Folgendes:

-   Das [CSPack-Befehlszeilentool][CSPack-Befehlszeilentool] für die Erstellung von Bereitstellungspaketen
-   Das [CSEncrypt-Befehlszeilentool][CSEncrypt-Befehlszeilentool] für die Verschlüsselung von Kennwörtern, die für den Zugriff auf Rolleninstanzen für Clouddienste über eine Remotedesktopverbindung verwendet werden.
-   Laufzeitbinärdateien, die Clouddienstprojekte benötigen, um mit deren Laufzeitumgebung zu kommunizieren und Diagnosen zu erstellen. Diese Binärdateien sind in NuGet-Paketen nicht verfügbar.

### <span id="emulator"></span></a>Microsoft Azure-Emulator

Der [Azure-Emulator][Azure-Emulator] simuliert die Clouddienstumgebung. So können Sie Clouddienstprojekte vor der Bereitstellung in Azure lokal auf Ihrem Computer testen.

### <span id="stgemulator"></span></a>Microsoft Azure-Speicheremulator

Der [Azure-Speicheremulator][Azure-Speicheremulator] nutzt eine SQL-Serverinstanz und das lokale Dateisystem, um Azure-Speicher (Warteschlangen, Tabellen, Blobs) zu simulieren. Dadurch ist lokales Testen möglich.

### <span id="stgtools"></span></a>Microsoft Azure-Speichertools

Dies installiert [AzCopy][AzCopy]. Dabei handelt es sich um ein Befehlszeilentool, mit dem Sie Daten in ein und aus einem Azure-Speicherkonto übertragen können.

### <span id="libraries"></span></a>Microsoft Azure-Bibliotheken für .NET

Dies umfasst Folgendes:

-   NuGet-Pakete für den Azure-Speicher, Service Bus und Zwischenspeicherung, die auf Ihrem Computer gespeichert werden, sodass Visual Studio offline neue Clouddienstprojekte erstellen kann.
-   Ein Plug-In für Visual Studio, mit dem [In-Role-Cache][In-Role-Cache]-Projekte lokal in Visual Studio ausgeführt werden können.

### <span id="ls"></span></a>Azure Publishing-Add-On LightSwitch für Visual Studio

Mit diesem Add-On können Sie [LightSwitch-Projekte auf Azure Websites veröffentlichen][LightSwitch-Projekte auf Azure Websites veröffentlichen]. Das LightSwitch-Add-On ist in Updates von Visual Studio und dem Azure-SDK für .NET enthalten. Mit der Installation des SDK stellen Sie sicher, dass Sie über die aktuelle Version des Add-Ons verfügen.

## <span id="notincluded"></span></a>Bei der Installation von Azure-SDK für .NET nicht enthalten

Sie benötigen möglicherweise einige Elemente für die Entwicklung in Azure, die bei der Installation des SDK nicht enthalten sind. Die wichtigsten Elemente sind:

-   [Clientbibliotheke][NuGet].

    Das Azure-SDK umfasst Clientbibliotheken für die Nutzung von Azure-Diensten. Es werden jedoch nicht alle installiert, wenn Sie das SDK installieren. Benötigt Ihre Anwendung eine Clientbibliothek, die nicht vom SDK installiert wird, können Sie diese auf [NuGet.org][NuGet] herunterladen. Verwendet Ihre Anwendung eine Clientbibliothek, die vom SDK installiert wird, ist es von Vorteil, ein Update auf die neueste Version von NuGet.org herunterzuladen.

    **Lokale Kopien von Clientbibliotheken.** Das Azure-SDK für .NET kopiert die NuGet-Pakete für einige Azure-Clientbibliotheken, wie Speicher, Service Bus und Zwischenspeicherung, auf Ihren Computer. Diese Clientbibliotheken werden automatisch in neue Clouddienstprojekte integriert. Die lokalen NuGet-Pakete ermöglichen es Visual Studio, Projekte auch dann zu erstellen, wenn keine Verbindung zum Internet besteht. Clientbibliotheken werden im Allgemeinen häufiger aktualisiert als neue SDK-Versionen veröffentlicht werden. Daher sind die Clientbibliotheken auf NuGet.org meist aktueller als die im SDK enthaltenen.

    **Projektvorlagen, die Clientbibliotheken enthalten.** Nur Projektvorlagen für den [Azure-Clouddienst][Erstellen, Öffnen und Veröffentlichen von Clouddienstprojekten] und den [Azure Mobile Service][Azure Mobile Service] enthalten automatisch einige Clientbibliotheken. Installieren Sie für andere Bibliotheken oder Vorlagen die erforderlichen [NuGet-Pakete mit Clientbibliotheken][NuGet].

-   [Azure PowerShell][Azure PowerShell].

    Mit Azure PowerShell können Sie [die Entwicklung und Bereitstellung von Azure-Umgebungen automatisieren][die Entwicklung und Bereitstellung von Azure-Umgebungen automatisieren].

-   [Projektvorlagen für Azure Mobile Service][Azure Mobile Service].

    Vorlagen für den mobilen Dienst sind nur in Visual Studio 2013 Update 2 und höher verfügbar. Sie sind nicht in Visual Studio 2012 oder früheren Versionen und auch nicht in Visual Studio 2013 Update 1 verfügbar. Auch dann nicht, wenn Sie das Azure-SDK für .NET installieren.

## <span id="faq"></span></a>Häufig gestellte Fragen

-   [Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?][Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?]
-   [Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?][Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?]
-   [Wo finde ich ältere Versionen des Azure-SDK für .NET?][Wo finde ich ältere Versionen des Azure-SDK für .NET?]
-   [Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?][Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?]
-   [Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?][Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?]

### <span id="azinvs"></span></a>Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?

Es wird empfohlen, das SDK zu installieren, wenn Sie für die Azure-Entwicklung die neuesten Tools verwenden möchten. Wenn Sie das SDK nicht installieren möchten, stellen Sie sicher, dass folgende Bedingungen erfüllt sind:

-   Sie haben das neueste [Update von Visual Studio][Update von Visual Studio] installiert.
-   Sie entwickeln nur für Azure-Websites oder den mobilen Dienst, nicht für Clouddienste oder virtuelle Computer.
-   Ihre Anwendung nutzt den Speicher nicht bzw. nutzt den Speicher, benötigt jedoch nicht den Speicheremulator oder das AzCopy-Tool.

### <span id="clientlib"></span></a>Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?

Das SDK installiert Clientbibliotheken, damit Sie Clouddienstprojekte auch dann erstellen können, wenn keine Verbindung zum Internet besteht. Aktuelle Clientbibliotheken sind in NuGet-Paketen unter [NuGet.org][NuGet] erhältlich. Weitere Informationen finden Sie weiter oben in diesem Dokument unter [Bei der Installation von Azure-SDK für .NET nicht enthalten][Bei der Installation von Azure-SDK für .NET nicht enthalten].

### <span id="olderversions"></span></a>Wo finde ich ältere Versionen des Azure-SDK für .NET?

Ältere Versionen finden Sie auf der Downloadseite für das [Azure-SDK für .NET][Azure-SDK für .NET].

### <span id="lifecycle"></span></a>Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?

Informationen finden Sie unter [Support Lifecycle-Richtlinien für Microsoft Azure-Clouddienste][Support Lifecycle-Richtlinien für Microsoft Azure-Clouddienste].

### <span id="guestos"></span></a>Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?

Informationen finden Sie unter [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix][Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix].

## <span id="resources"></span></a>Ressourcen

Das Azure-SDK für .NET oder Clientbibliotheken können Sie auf der [Azure-Downloadseite][Azure Downloads] herunterladen.

Quellcode des Azure-SDK für .NET, einschließlich Clientbibliotheken, finden Sie unter [GitHub.com/Azure][GitHub.com/Azure].

Referenzdokumentation für Azure-Clientbibliotheken finden Sie unter [Azure .NET-Referenz][Azure .NET-Referenz].

  [Azure Downloads]: /de-de/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [Bei der Installation von Azure-SDK für .NET enthalten]: #included
  [Bei der Installation von Azure-SDK für .NET nicht enthalten]: #notincluded
  [Häufig gestellte Fragen]: #faq
  [Ressourcen]: #resources
  [Visual Studio Express für das Web]: #vwd
  [Microsoft ASP.NET und Webtools für Visual Studio]: #wte
  [Microsoft Azure-Tools für Microsoft Visual Studio]: #tools
  [Microsoft Azure-Entwicklertools]: #auth
  [Microsoft Azure-Emulator]: #emulator
  [Microsoft Azure-Speicheremulator]: #stgemulator
  [Microsoft Azure-Speichertools]: #stgtools
  [Microsoft Azure-Bibliotheken für .NET]: #libraries
  [Azure Publishing-Add-On LightSwitch für Visual Studio]: #ls
  [1]: http://www.visualstudio.com/de-de/products/visual-studio-express-vs.aspx
  [Veröffentlichen von Webprojekten auf Azure-Websites]: ../web-sites-dotnet-get-started/
  [Veröffentlichen von Konsolenanwendungsprojekten mit Azure-WebJobs]: ../websites-dotnet-deploy-webjobs/
  [Erstellen einer Azure-Website und Erstellen von SQL-Datenbankressourcen bei der Erstellung eines neuen Webprojekts oder der Veröffentlichung eines Webprojekts]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Erstellen von PowerShell-Bereitstellungsskripts während der Erstellung neuer Websites]: http://msdn.microsoft.com/de-de/library/dn642480.aspx
  [Verwalten von Azure-Websites und Beheben von Problemen im Server-Explorer]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [Ausführen im Debugmodus aus der Ferne für Websites und WebJobs]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Erstellen, Öffnen und Veröffentlichen von Clouddienstprojekten]: ../cloud-services-dotnet-get-started/
  [Erstellen von Bereitstellungspaketen für Clouddienstprojekte]: http://msdn.microsoft.com/de-de/library/ff683672.aspx
  [Erstellen von virtuellen Azure-Computern während der Erstellung neuer Webprojekte]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [Anzeigen und Verwalten von Einstellungen für Clouddienstprojekte in Projekteigenschaftsfenstern in Visual Studio]: http://msdn.microsoft.com/de-de/library/ee405486.aspx
  [Clouddiensten]: http://msdn.microsoft.com/de-de/library/ff683675.aspx
  [virtuellen Computern]: http://msdn.microsoft.com/de-de/library/jj131259.aspx
  [Service Bus]: http://msdn.microsoft.com/de-de/library/jj149828.aspx
  [Ausführen im Debugmodus aus der Ferne für Clouddienste und virtuelle Computer]: http://msdn.microsoft.com/de-de/library/ff683670.aspx
  [CSPack-Befehlszeilentool]: http://msdn.microsoft.com/de-de/library/gg432988.aspx
  [CSEncrypt-Befehlszeilentool]: http://msdn.microsoft.com/de-de/library/hh404001.aspx
  [Azure-Emulator]: http://msdn.microsoft.com/de-de/library/dn339018.aspx
  [Azure-Speicheremulator]: http://msdn.microsoft.com/de-de/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [In-Role-Cache]: http://msdn.microsoft.com/de-de/library/dn386103.aspx
  [LightSwitch-Projekte auf Azure Websites veröffentlichen]: http://msdn.microsoft.com/de-de/library/jj131261.aspx
  [Azure Mobile Service]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [Azure PowerShell]: ../install-configure-powershell/
  [die Entwicklung und Bereitstellung von Azure-Umgebungen automatisieren]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Viele Azure-Funktionen sind bereits in Visual Studio enthalten. Muss ich das Azure-SDK für .NET zwingend installieren?]: #azinvs
  [Ich benötige eine Clientbibliothek. Muss ich das Azure-SDK für .NET zwingend installieren, um sie zu erhalten?]: #clientlib
  [Wo finde ich ältere Versionen des Azure-SDK für .NET?]: #olderversions
  [Wie gestaltet sich der Lebenszyklus für Versionen des Azure-SDK für .NET?]: #lifecycle
  [Mit welchen Gastbetriebssystemversionen ist das Azure-SDK für .NET kompatibel?]: #guestos
  [Update von Visual Studio]: http://www.visualstudio.com/de-de/downloads/download-visual-studio-vs#DownloadFamilies_5
  [Azure-SDK für .NET]: /de-de/downloads/archive-net-downloads/
  [Support Lifecycle-Richtlinien für Microsoft Azure-Clouddienste]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix]: http://msdn.microsoft.com/de-de/library/ee924680.aspx
  [GitHub.com/Azure]: https://github.com/azure/
  [Azure .NET-Referenz]: /de-de/develop/net/reference/

<properties pageTitle="Verwenden von Media Services" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#Verwenden von Media Services

Diese Anleitung zeigt, wie Sie die Programmierung mit Azure Media Services beginnen. Die Anleitung umfasst eine Reihe von Artikeln sowie eine technische Übersicht über Media Services, Schritte zum Konfigurieren Ihres Azure-Kontos für Media Services, eine Einrichtungsanleitung für die Entwicklung sowie Themen, in denen typische Programmieraufgaben dargestellt werden. Die gezeigten Szenarios umfassen Folgendes: Hochladen von Ressourcen, Verschlüsseln von Ressourcen, Codieren von Ressourcen und Übermitteln von Ressourcen. Die Beispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET. Weitere Informationen zu Azure Media Services finden Sie unter [Nächste Schritte][].

Sie können Media Services auch mit den OData-basierten REST-APIs programmieren. Sie können mit .NET-Sprachen oder anderen Programmiersprachen eine Anwendung erstellen, die REST-API-Aufrufe von Media Services ausführt. Eine umfassende Dokumentation der Programmierung mit der Media Services REST-API finden Sie unter [Erstellen von Anwendungen mit der Azure Media Services REST-API](http://go.microsoft.com/fwlink/?linkid=252967). 

Um die Programmierung mit der Media Services REST-API oder dem Media Services SDK zu beginnen, aktivieren Sie zuerst Ihr Azure-Konto für Media Services, wie unter [Einrichten eines Azure-Kontos für Media Services][] beschrieben.

Die aktuelle Media Services SDK-Dokumentation finden Sie [hier](http://msdn.microsoft.com/de-de/library/hh973613.aspx). 

##<a name="what-are"></a><span class="short header">Was sind Media Services?</span>
Azure Media Services bildet eine erweiterbare Medienplattform, welche die besten Komponenten der Microsoft-Medienplattform und der Medienkomponenten von Drittanbietern in Azure integriert. Media Services stellt in der Cloud eine Medienpipeline bereit, die es Industriepartnern ermöglicht, Komponententechnologien zu erweitern oder zu ersetzen. ISVs und Medienanbieter können mit Media Services durchgängige Medienlösungen erstellen. In dieser Übersicht werden die allgemeine Architektur und häufige Entwicklungsszenarien für Media Services beschrieben.

Das folgende Diagramm zeigt die grundlegende Media Services-Architektur.

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

###Unterstützung von Media Services-Funktionen
Die aktuelle Version von Media Services stellt für die Entwicklung von Medienanwendungen in der Cloud die folgenden Funktionen bereit.

- **Ingest**. Durch Ingest-Operationen werden Ressourcen in das System eingebracht, zum Beispiel, indem sie hochgeladen und verschlüsselt werden, bevor sie im Azure-Speicher platziert werden. Media Services ermöglicht die Integration in Partnerkomponenten, die schnelle UDP (User Datagram Protocol)-Uploadlösungen bereitstellen.
- **Codieren**. Codieroperationen umfassen das Codieren, Transformieren und Konvertieren von Medienressourcen. Codieraufgaben können in der Cloud mit dem Azure Media Encoder ausgeführt werden. Die folgenden Codieroptionen stehen zur Verfügung:
   - Verwenden des Azure Media Encoder und Arbeiten mit einer Reihe von Standardcodecs und -formaten wie dem branchenführenden IIS Smooth Streaming, MP4 und Konvertierung in Apple HTTP Live Streaming
   - Konvertieren ganzer Bibliotheken oder einzelner Dateien mit umfassender Kontrolle über Ein- und Ausgabe
   - Große Anzahl unterstützter Dateitypen, -formate und Codecs (siehe [Unterstützte Codecs und Dateitypen für Windows Azure Media Encoder][]).
   - Unterstützte Formatkonvertierungen. Mit Media Services können Sie ISO MP4-Dateien (.mp4) in das Smooth Streaming-Dateiformat (PIFF 1.3) (.ismv; isma) konvertieren. Das Smooth Streaming-Dateiformat (PIFF) kann auch in Apple HTTP Live Streaming (.msu8, .ts) konvertiert werden.
- **Schützen**. Schützen von Inhalten bedeutet, Live Streaming- oder bedarfsgesteuerte Inhalte zu verschlüsseln, damit sie sicher transportiert, gespeichert und zugestellt werden können. Media Services stellt eine von der DRM-Technologie unabhängige Lösung zum Schützen von Inhalten bereit.  Die derzeit unterstützten DRM-Technologien sind Microsoft PlayReady und die gemeinsame MPEG-Verschlüsselung. Die Unterstützung weiterer DRM-Technologien ist vorgesehen. 
- **Streaming**. Beim Streamen werden Inhalte live oder bedarfsgesteuert an Clients gesendet, oder es können bestimmte Mediendateien aus der Cloud heruntergeladen werden. Media Services stellt eine formatunabhängige Lösung zum Streamen von Inhalten bereit.  Media Services unterstützt den Streamingursprung für die Formate Smooth Streaming, Apple HTTP Live Streaming und MP4. Die Unterstützung weiterer Formate ist vorgesehen. Sie können Streaminginhalte auch nahtlos über ein Drittanbieter-CDN übermitteln, das die Skalierung für Millionen von Benutzern ermöglicht.   

###Media Services-Entwicklungsszenarien
Media Services unterstützt zahlreiche gängige Szenarien für die Medienentwicklung, wie in der folgenden Tabelle beschrieben. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Szenario</th>
       <th>Beschreibung</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Erstellen von durchgängigen Workflows</td>
        <td>Erstellen Sie umfassende Medienworkflows ausschließlich in der Cloud. Vom Hochladen von Medien bis zum Verteilen von Inhalten stellt Media Services eine Reihe von Komponenten zur Verfügung, die für bestimmte Anwendungsworkflows kombiniert werden können. Zu den aktuellen Fähigkeiten zählen Hochladen, Speichern, Codieren, Formatkonvertierung, Schutz von Inhalten und bedarfsgesteuertes Streaming.</td>
    </tr>
    <tr>
        <td>Erstellen von hybriden Workflows</td>
        <td>Sie können Media Services in vorhandene Tools und Prozesse integrieren. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über ein Azure-CDN oder ein Drittanbieter-CDN zu übermitteln. Media Services kann individuell über standardmäßige REST-APIs aufgerufen werden und in externe Anwendungen und Dienste integriert werden.</td>
    </tr>
    <tr>
        <td>Cloud-Unterstützung für Media Player</td>
        <td>Sie können Medien auf mehreren Geräten (einschließlich iOS-, Android- und Windows-Geräten) und Plattformen erstellen, verwalten und übermitteln.</td>
    </tr>
  </tbody>
</table>

<h3><a name="media-client"></a>Media Services-Cliententwicklung</h3>
Erweitern Sie die Reichweite Ihrer Media Services-Lösung mithilfe von SDKs und Player-Frameworks, um Medienclientanwendungen zu erstellen. Mit diesen Clients können Entwickler Media Services-Anwendungen erstellen, die auf einer großen Bandbreite von Geräten und Plattformen ein überzeugendes Benutzererlebnis bieten. Je nach den Zielgeräten stehen von Microsoft und Drittanbietern Optionen für SDKs und Player-Frameworks zur Verfügung.  

Im Folgenden sind einige der verfügbaren Client-SDKs und Player-Frameworks aufgeführt.  Weitere Informationen über diese und weitere geplante SDKs und Player-Frameworks und über die von ihnen unterstützten Funktionen finden Sie unter [Media Services-Cliententwicklung][] in den Media Services-Foren. 

####Unterstützung von Mac- und PC-Clients  
Für PCs und Macs können Sie eine Streaming-Anwendung mithilfe von Microsoft Silverlight oder Adobe Open Source Media Framework erstellen.

-	[Smooth Streaming-Client für Silverlight](http://www.microsoft.com/de-de/download/details.aspx?id=29940)
-	[Microsoft-Medienplattform: Player-Framework für Silverlight](http://smf.codeplex.com/documentation)
-	[Smooth Streaming-Plug-In für OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). Informationen zur Verwendung dieses Plug-In finden Sie unter [Verwenden des Microsoft Smooth Streaming-Plug-In für das Adobe Open Source Media Framework](../media-services-use-osmf-smooth-streaming-client-plugin/).

####Windows 8-Anwendungen
Für Windows 8 können Sie Windows Store-Anwendungen mit einer der unterstützten Entwicklungssprachen und Konstrukten wie HTML, JavaScript, XAML, C# und C+ erstellen.

-	[Smooth Streaming-Client SDK für Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). Weitere Informationen zum Erstellen einer Windows Store-Anwendung mit diesem SDK finden Sie unter [Erstellen einer Smooth Streaming Windows Store-Anwendung](http://go.microsoft.com/fwlink/?LinkId=271647). Informationen zum Erstellen eines Smooth Streaming-Players in HTML5 finden Sie unter [Exemplarische Vorgehensweise: Erstellen Ihres ersten HTML5 Smooth Streaming-Players](http://msdn.microsoft.com/de-de/library/jj573656(v=vs.90).aspx).

-	[Microsoft-Medienplattform: Player-Framework für Windows 8 Windows Store-Anwendungen](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
Xbox unterstützt Xbox LIVE-Anwendungen, die Smooth Streaming-Inhalte nutzen können. Das Xbox LIVE Application Development Kit (ADK) umfasst Folgendes:

-	Smooth Streaming-Client für Xbox LIVE ADK
-	Microsoft-Medienplattform: Player-Framework für Xbox LIVE ADK

####Eingebettete oder dedizierte Geräte
Geräte wie verbundene Fernsehgeräte, Set-Top-Boxen, Blu-Ray-Player, OTT-TV-Boxen und mobile Geräte, die ein benutzerdefiniertes Anwendungsentwicklungs-Framework und eine benutzerdefinierte Medienpipeline haben. Microsoft stellt die folgenden Portierungs-Kits zur Verfügung, die lizenziert werden können, und gibt Partnern die Möglichkeit, die Smooth Streaming-Wiedergabe für die Plattform zu portieren.

-	[Portierungs-Kit für Smooth Streaming-Client](http://www.microsoft.com/de-de/mediaplatform/sspk.aspx)
-	[Portierungs-Kit für Microsoft-PlayReady-Geräte](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
Microsoft stellt ein SDK bereit, das zum Erstellen von erstklassigen Videoanwendungen für Windows Phone verwendet werden kann. 

-	[Smooth Streaming-Client für Silverlight](http://www.microsoft.com/de-de/download/details.aspx?id=29940)
-	[Microsoft-Medienplattform: Player-Framework für Silverlight](http://smf.codeplex.com/documentation)

####iOS-Geräte
Für iOS-Geräte wie z. B. iPhone, iPod und iPad stellt Microsoft ein SDK zur Verfügung, mit dem Sie Anwendungen für diese Plattformen erstellen können, um erstklassige Videoinhalte zu übermitteln: Smooth Streaming SDK für iOS Devices mit PlayReady.  Das SDK ist nur für Lizenzinhaber verfügbar. Wenn Sie weitere Informationen wünschen, [senden Sie eine E-Mail an Microsoft](mailto:askdrm@microsoft.com). Informationen zur iOS-Entwicklung finden Sie im [iOS Developer Center](https://developer.apple.com/devcenter/ios/index.action).

####Android-Geräte
Mehrere Microsoft-Partner stellen SDKs für die Android-Plattform zur Verfügung, welche die Smooth Streaming-Wiedergabe auf Android-Geräten ermöglichen. Wenden Sie sich per [E-Mail an Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices), wenn Sie mehr über die Partner erfahren möchten.

##Nächste Schritte
Nachdem Sie nun einen Überblick über Media Services haben, rufen Sie das Thema [Einrichten Ihres Computers für die Media Services-Entwicklung](../media-services-set-up-computer/) auf.


  [Was sind Media Services?]: #what-are
  [Media Services-Cliententwicklung]: #media-client
  [Einrichten eines Azure-Kontos für Media Services]: #setup-account
  [Einrichtung für die Media Services-Entwicklung]: #setup-dev
  [Gewusst wie: Programmgesteuertes Verbinden mit Media Services]: #connect
  [Gewusst wie: Erstellen eines verschlüsselten Medienobjekts und Hochladen in den Speicher]: #create-asset
  [Gewusst wie: Abrufen einer Media Processor-Instanz]: #get-mediaproc
  [Gewusst wie: Nachverfolgen des Auftragsstatus]: #check-job-progress
  [Gewusst wie: Codieren von Medienobjekten]: #encode-asset
  [Gewusst wie: Schützen eines Medienobjekts mit dem PlayReady-Schutz]: #playready
  [Gewusst wie: Verwalten von Medienobjekten im Speicher]: #manage-asset
  [Gewusst wie: Bereitstellen eines Medienobjekts durch Herunterladen]: #download-asset
  [Gewusst wie: Bereitstellen von Streaming-Inhalten]: #stream-asset
  [Gewusst wie: Bereitstellen von Apple HLS-Streaming-Inhalten]: #stream-HLS
  [Gewusst wie: Aktivieren von Azure CDN]: #enable-cdn
  [Nächste Schritte]: #next-steps

  [Erstellen von Anwendungen mit der Azure Media Services REST-API]: http://go.microsoft.com/fwlink/?linkid=252967
  [OData (Open Data Protocol)]: http://odata.org/
  [WCF Data Services 5.0 für OData v3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Azure Marketplace]: https://datamarket.azure.com/
  [Media Services-Cliententwicklung]: http://social.msdn.microsoft.com/Forums/de-de/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Media Services-Vorschau:  Unterstützte Funktionen]: http://social.msdn.microsoft.com/Forums/de-de/MediaServices/thread/eb946433-16f2-4eac-834d-4057335233e0
  [Anstehende Versionen von Media Services:  Geplante Funktionsunterstützung]: http://social.msdn.microsoft.com/Forums/de-de/MediaServices/thread/431ef036-0939-4784-a939-0ecb31151ded
  [Kontoeinrichtung für die Media Services-Vorschau]: http://go.microsoft.com/fwlink/?linkid=247287
  [Azure Media Services SDK für .NET]: http://go.microsoft.com/fwlink/?LinkID=256500
  [Webplattform-Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Installieren des Azure SDKs unter Windows 8]: http://www.windowsazure.com/de-de/develop/net/other-resources/windows-azure-on-windows-8/
  [Azure Media Services-Dokumentation]: http://go.microsoft.com/fwlink/?linkid=245437
  [Erste Schritte mit Azure CDN]: http://msdn.microsoft.com/de-de/library/windowsazure/ff919705.aspx
  [Media Services-Forum]: http://social.msdn.microsoft.com/Forums/de-de/MediaServices/threads
  [Schnelleinstieg: Verwenden des Media Services .NET SDKs]: http://go.microsoft.com/fwlink/?linkid=252966
  [Erstellen von Anwendungen mit dem Media Services SDK für .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Erstellen eines Media Services-Kontos]: ../media-services-create-account/
  [Unterstützte Dateitypen für Media Services]: http://msdn.microsoft.com/de-de/library/dn535852.aspx



<!--HONumber=35.1-->

<!--HONumber=35.1-->

<properties 
	pageTitle="Dokumentationsressourcen zu Azure WebJobs" 
	description="Empfohlene Ressourcen zum Erlernen der Verwendung von Azure WebJobs und des Azure WebJobs-SDK" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="tdykstra"/>

# Dokumentationsressourcen zu Azure WebJobs

## Übersicht

Dieses Thema enthält Links zu Dokumentationsressourcen zur Verwendung von Azure WebJobs und dem Azure WebJobs-SDK. Azure WebJobs bietet eine einfache Möglichkeit zum Ausführen von Skripts oder Programmen als Hintergrundprozesse in [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Sie können eine ausführbare Datei, z. B. eine CMD-, BAT-, EXE (.NET)-, PS1-, SH-, PHP, PY-, JS- und JAR-Datei, hochladen und ausführen. Diese Programme werden als Webaufträge nach einem Zeitplan (Cron) oder laufend ausgeführt.

Das WebJobs-SDK vereinfacht die Verwendung des Azure-Speichers. Das WebJobs-SDK verfügt über ein Bindungs- und Auslösersystem, das Microsoft Azure-Speicherblobs, Warteschlangen und Tabellen sowie Service Bus-Warteschlangen verwendet.

Das Erstellen, Bereitstellen und Verwalten von WebJobs erfolgt dank der integrierten Tools in Visual Studio nahtlos. Sie können Webaufträge aus Vorlagen erstellen, veröffentlichen und verwalten (Ausführen/Anhalten/Überwachen/Debuggen).

Das WebJobs-Dashboard im Azure-Portal bietet leistungsstarke Verwaltungsfunktionen, mit denen Sie vollständige Kontrolle über die Ausführung von Webaufträgen haben, einschließlich der Möglichkeit, einzelne Funktionen innerhalb von WebJobs aufzurufen. Das Dashboard zeigt auch Funktionslaufzeiten und Protokollausgaben an.

##<a name="getstarted"></a>Erste Schritte mit WebJobs und dem WebJobs-SDK

* [Einführung in Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs are awesome and you should start using them right now!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Setzen Sie ab sofort das großartige Azure WebJobs ein; in englischer Sprache) (Blogbeitrag von Troy Hunt)
* [Funktionen von Azure WebJobs](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Was ist das WebJobs-SDK?](websites-dotnet-webjobs-sdk.md)
* [Anleitungen für Hintergrundaufträge von Microsoft Patterns and Practices](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Ankündigung der Version 1.0.0 RTM des Microsoft Azure-WebJobs-SDK](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Erste Schritte mit dem Azure WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started.md)
* [Verwenden von Azure-Warteschlangenspeicher mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Verwenden von Azure-Blobspeicher mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Verwenden von Azure-Tabellenspeicher mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Verwenden von Azure Service Bus mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-service-bus.md)
* [Kurzübersicht zum Azure WebJobs-SDK (PDF zum Herunterladen)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Videos
	* [WebJobs und das WebJobs-SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Videoreihe zu Azure WebJobs auf Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Einführung in WebJobs-Tools für Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs-Tools und Remotedebuggen](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Weitere Informationen finden Sie auch in den Abschnitten [Bereitstellen von WebJobs](#deploy) und [Testen und Debuggen von WebJobs](#debug).

##<a name="deploy"></a>Bereitstellen von WebJobs

* [Bereitstellen von Azure WebJobs mit Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Bereitstellen von WebJobs mithilfe des Azure-Verwaltungsportals](web-sites-create-web-jobs.md)
* [Aktivieren der befehlszeilengesteuerten oder der kontinuierlichen Bereitstellung von Azure WebJobs](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git-Bereitstellung einer .NET-Konsolen-App in Azure mithilfe von WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Deploying an F# WebJob to Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx) (Bereitstellen eines F#-Webauftrags in Azure; in englischer Sprache)
* Videos
	* [Einführung in WebJobs-Tools für Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [WebJobs-Tools und Remotedebuggen](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Planen von WebJobs

* [Das Dialogfeld "Azure-Webauftrag hinzufügen"](websites-dotnet-deploy-webjobs.md#configure)
* [Erstellen geplanter WebJobs im Azure-Verwaltungsportal](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>Testen und Debuggen von WebJobs

* [Neue Entwickler- und Debuggingfunktionen für Azure WebJobs in Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Anzeigen des Webauftrags-Dashboards](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Schreiben von Protokollen mit dem WebJobs-SDK und Anzeigen im Dashboard](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Remotedebuggen von WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Wer hat dieses Blob geschrieben?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hosten von interaktivem Code in der Cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Abrufen eines Dashboards für die lokale Entwicklung mit dem WebJobs-SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Hinzufügen der Ablaufverfolgung zu Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](../storage-monitoring-diagnosing-troubleshooting/)
* Videos
	* [WebJobs-Tools und Remotedebuggen](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Skalieren von WebJobs

* [Skalieren Ihrer Webanwendung mit Azure-Websites](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service: Entwickeln sehr vieler einsatzbereiter Web-Apps](https://channel9.msdn.com/Events/Build/2014/3-626). Behandelt die Skalierung von Web-Apps mit WebJobs, einschließlich des WebJobs-SDK.
* Videos
	* [Horizontales Skalieren von WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Zusätzliche WebJobs-Ressourcen

* [Blogbeitrag zur allgemeinen Verfügbarkeit von Azure WebJobs von Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Dokumentation zu Webauftragseinstellungen in GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Ausführen von PowerShell-Webaufträgen in Azure App Service](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Erhalten von Benachrichtigungen, wenn die von Azure ausgelösten Webaufträge abgeschlossen sind](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Aufbewahrungsrichtlinie für einfache Web-App-Sicherung mit WebJobs](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure-Web-Apps und Cloud-Dienste langsam bei erster Anforderung](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Zeigt die Verwendung von WebJobs zur Simulation der Funktion "AlwaysOn", die nur für die Standardpreisstufe verfügbar ist.
* [Ordnungsgemäßes Herunterfahren von WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Informationen zum ordnungsgemäßen Herunterfahren des WebJobs-SDK finden Sie unter [Ordnungsgemäßes Herunterfahren](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatisierung von Sicherungen mit Azure WebJobs und AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Videos
	* [Videos zu Azure WebJobs von Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Videoreihe zu Azure WebJobs auf Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Zusätzliche Ressourcen für das WebJobs-SDK

* [Quellcode des WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk)
* [How does [BlobTrigger] work?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) (Wie funktioniert [BlobTrigger]?; in englischer Sprache) 
* [Erweiterte Bindungen mit dem Azure-WebJobs-SDK](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Webauftrag zum Hochladen von FREB-Dateien in Azure Storage mithilfe des WebJobs-SDK](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosten von Azure WebJobs außerhalb von Azure mit den Protokollierungsvorteilen eines auf Azure geosteten Webauftrags](http://bypassion.dk/?p=510)
* [Erstellen eines Tools zum Importieren von Daten mit Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Videos
	* [Videoreihe zu Azure WebJobs auf Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Beispielanwendungen für WebJobs

* [Vom WebJobs-Team von GitHub bereitgestellte Beispielanwendungen](https://github.com/azure/azure-webjobs-sdk-samples)
* [Einfache Azure-Web-App mit WebJobs-Back-End mit dem WebJobs-SDK](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Veranschaulicht die Verwendung von geplanten und ereignisgesteuerten Webaufträgen. Weitere Informationen finden Sie im Blogbeitrag [Neuerstellung von SiteMonitR mithilfe des Azure WebJobs-SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Azure-Blog](/blog)
* [Apple-Blog von Amit](http://blog.amitapple.com/). Behandelt WebJobs (aber nicht das SDK).
* [Blog von Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Hilfe zu WebJobs

* [Stapelüberlauf bei WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [Stapelüberlauf beim WebJobs-SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Forum für Azure und ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum für Azure App Service-Web-Apps](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [User Voice-Website für Azure-Web-Apps](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Verwenden Sie das Hashtag #AzureWebJobs
* [Report a WebJobs bug or issue](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues) (Melden eines WebJobs-Fehlers oder -Problems; in englischer Sprache)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=August15_HO6-->
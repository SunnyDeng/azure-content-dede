<properties 
	pageTitle="Empfohlene Ressourcen für Azure-Webaufträge" 
	description="Empfohlene Ressourcen zum Erlernen der Verwendung von Azure-Webaufträgen und des Azure-Webaufträge-SDK" 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tdykstra"/>

#Empfohlene Ressourcen für Azure-Webaufträge

Dieses Thema enthält Links zu Dokumentationsressourcen zur Verwendung von Azure-Webaufträgen und dem Azure-Webaufträge-SDK. Azure-Webaufträge bieten eine einfache Möglichkeit zum Ausführen von Skripts oder Programmen als Hintergrundprozesse auf Azure-Websites. Sie können eine ausführbare Datei, z. B. eine CMD-, BAT-, EXE (.NET)-, PS1-, SH-, PHP, PY-, JS- und JAR-Datei, hochladen und ausführen. Diese Programme werden als Webaufträge nach einem Zeitplan (Cron) oder laufend ausgeführt.

Das Webaufträge-SDK vereinfacht die Verwendung des Azure-Speichers. Das Webaufträge-SDK verfügt über ein Bindungs- und Auslösersystem, das Microsoft Azure-Speicher-BLOBs, Warteschlangen und Tabellen sowie Service Bus-Warteschlangen verwendet.

Das Erstellen, Bereitstellen und Verwalten von Webaufträgen erfolgt dank der integrierten Tools in Visual Studio nahtlos. Sie können Webaufträge aus Vorlagen erstellen, veröffentlichen und verwalten (Ausführen/Anhalten/Überwachen/Debuggen). 

Das Webauftrags-Dashboard im Azure-Verwaltungsportal bietet leistungsstarke Verwaltungsfunktionen, mit denen Sie vollständige Kontrolle über die Ausführung von Webaufträgen haben, einschließlich der Möglichkeit, einzelne Funktionen innerhalb der Webaufträge aufzurufen. Das Dashboard zeigt auch Funktionslaufzeiten und Protokollausgaben an. 

## Inhaltsverzeichnis

* [Erste Schritte mit Webaufträgen](#getstarted)
* [Bereitstellen von Webaufträgen](#deploy)
* [Testen und Debuggen von Webaufträgen](#debug)
* [Zusätzliche Ressourcen für Webaufträge](#additional)
* [Zusätzliche Ressourcen für das Webaufträge-SDK](#additionalsdk)
* [Beispielanwendungen für Webaufträge](#samples)
* [Blogs](#blogs) 
* [Hilfe zu Webaufträgen](#gethelp)

##<a name="getstarted"></a>Erste Schritte mit Webaufträgen

* [Einführung in Azure-Webaufträge](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Funktionen von Azure-Webaufträgen](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Was ist das Webaufträge-SDK?](../websites-dotnet-webjobs-sdk/)
* [Ankündigung der Version 1.0.0 RTM des Microsoft Azure-Webaufträge-SDK](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Erste Schritte mit Azure-Webaufträge-SDK](../websites-dotnet-webjobs-sdk-get-started/)
* [Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* [Verwenden von Azure-BLOB-Speicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-blobs-how-to)
* [Verwenden von Azure-Tabellenspeicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-tables-how-to)
* [Verwenden von Azure Service Bus mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-service-bus)
* [Kurzübersicht zum Azure-Webaufträge-SDK (PDF herunterladen)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Videos
	* [Webaufträge und das Webaufträge-SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Videoreihe zu Azure-Webaufträgen auf Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Einführung in Webauftrags-Tools für Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Webauftrags-Tools und Remotedebuggen](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Siehe auch die folgenden Abschnitten zum [Bereitstellen von Webaufträgen](#deploy) und [Testen und Debuggen von Webaufträgen](#debug).

##<a name="deploy"></a>Bereitstellen von Webaufträgen

* [Bereitstellen von Azure-Webaufträgen auf Azure-Websites](../websites-dotnet-deploy-webjobs/)
* [Bereitstellen von Webaufträgen mithilfe des Azure-Verwaltungsportals](../web-sites-create-web-jobs/)
* [Aktivieren der befehlszeilengesteuerten oder kontinuierlichen Bereitstellung von Azure-Webaufträgen](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git-Bereitstellung einer .NET-Konsolen-App für Azure mithilfe von Webaufträgen](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Videos
	* [Einführung in Webauftrags-Tools für Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Webauftrags-Tools und Remotedebuggen](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="debug"></a>Testen und Debuggen von Webaufträgen

* [Neue Entwickler- und Debuggingfunktionen für Azure-Webaufträge in Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Problembehandlung von Azure-Websites in Visual Studio](../web-sites-dotnet-troubleshoot-visual-studio/)
* [Wer hat dieses Blob geschrieben?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hosten von interaktivem Code in der Cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Abrufen eines Dashboards für die lokale Entwicklung mit dem Webaufträge-SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Hinzufügen der Ablaufverfolgung zu Azure-Websites und Webaufträgen](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](../storage-monitoring-diagnosing-troubleshooting/)
* Videos
	* [Webauftrags-Tools und Remotedebuggen](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="additional"></a>Zusätzliche Ressourcen für Webaufträge

* [Blogbeitrag von Magnus Mårtensson zur allgemeinen Verfügbarkeit von Azure-Webaufträgen](http://magnusmartensson.com/azure-webjobs-ga)
* [Dokumentation zu Webauftragseinstellungen in GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Ausführen von PowerShell-Webaufträgen auf Azure-Websites](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Erhalten von Benachrichtigungen, wenn die von Azure ausgelösten Webaufträge abgeschlossen sind](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Azure Web Sites: Entwickeln sehr vieler einsatzbereiter Webanwendungen](https://channel9.msdn.com/Events/Build/2014/3-626). Behandelt die Skalierung von Azure-Websites mit Webaufträgen einschließlich Webaufträge-SDK
* [Aufbewahrungsrichtlinie für einfache Websitesicherung mit Webaufträgen](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Microsoft Azure-Websites und Cloud-Dienste langsam bei erster Anforderung](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Zeigt, wie Webaufträge zur Simulation der Funktion "AlwaysOn" verwendet wird, die nur für Websites der Ebene "Standard" verfügbar ist.
* [Ordnungsgemäßes Herunterfahren von Webaufträgen](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Informationen zum ordnungsgemäßen Herunterfahren finden Sie unter [Ordnungsgemäßes Herunterfahren](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful).)
* [Skalieren Ihrer Webanwendung mit Azure-Websites](http://msdn.microsoft.com/magazine/dn786914.aspx)
* Videos
	* [Horizontales Skalieren von Webaufträgen](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Video zu Azure-Webaufträgen von Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Videoreihe zu Azure-Webaufträgen auf Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Zusätzliche Ressourcen für das Webaufträge-SDK

* [Auslöser, Bindungen und Routenparameter in Azure-Webaufträgen](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Azure-Speicherbindungen
	* [Blobs](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Warteschlangen](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Tabellen](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Wie funktioniert [BlobTrigger]?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Erweiterte Bindungen mit dem Azure-Webaufträge-SDK](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Webauftrag zum Hochladen von FREB-Dateien in Azure-Speicher mithilfe des Webauftrags-SDK](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosten von Azure-Webaufträgen außerhalb von Azure mit den Protokollierungsvorteilen eines auf Azure gehosteten Webauftrags](http://bypassion.dk/?p=510)
* [Erstellen eines Tools zum Importieren von Daten mit Azure-Webaufträgen](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Videos
	* [Videoreihe zu Azure-Webaufträgen auf Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Beispielanwendungen für Webaufträge

* [Vom Webaufträge-Team von GitHub bereitgestellte Beispielanwendungen](https://github.com/azure/azure-webjobs-sdk-samples)
* [Einfache Azure-Website mit Webauftrags-Back-End mit dem Webauftrags-SDK](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Veranschaulicht die Verwendung von geplanten und ereignisgesteuerten Webaufträgen. Weitere Informationen finden Sie im Blogbeitrag [Neuerstellung von SiteMonitR mithilfe des Azure-Webaufträge-SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Azure-Blog](/blog)
* [Apple-Blog von Amit](http://blog.amitapple.com/). Behandelt Webaufträge (aber nicht das SDK).
* [Blog von Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Hilfe zu Webaufträgen

* [Stapelüberlauf bei Webaufträgen](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [Stapelüberlauf beim Webaufträge-SDK](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Forum für Azure und ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum für Azure-Websites](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [User Voice-Website für Azure-Websites](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Verwenden Sie das Hashtag #AzureWebJobs




<!--HONumber=42-->

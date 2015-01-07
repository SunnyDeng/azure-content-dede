<properties urlDisplayName="Resources" pageTitle="Erstellen einer digitalen Marketingkampagne mithilfe von Azure-Websites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />

# Erstellen einer digitalen Marketingkampagne mithilfe von Azure-Websites
Dieser Leitfaden bietet einen technischen Überblick über das Erstellen von digitalen Marketingkampagnen mithilfe von Azure-Websites. Bei einer digitalen Marketingkampagne handelt es sich normalerweise um eine kurzlebige Entität, die ein kurzfristiges Marketingziel vorantreiben soll. Zwei wichtige Szenarien müssen berücksichtigt werden. Beim ersten Szenario erstellt ein fremdes Marketingunternehmen die Kampagne für ihre Kunden und verwaltet diese während der Aktionsdauer. Beim zweiten Szenario erstellt das Marketingunternehmen die Kampagne und überträgt anschließend den Besitz der Ressourcen für die digitale Marketingkampagne auf ihren Kunden. Danach führt der Kunde die digitale Marketingkampagne aus und verwaltet diese selbst. 

[Azure-Websites][websitesoverview] ist für beide Szenarien bestens geeignet. Mit deren Hilfe ist eine schnelle Erstellung möglich. Darüber hinaus werden mehrere Frameworks, Sprachen und Skalierungen unterstützt, um Benutzeranforderungen nachzukommen. Zahlreiche Bereitstellungs- und Quellcodeverwaltungssysteme stehen außerdem zur Verfügung. Wenn Sie Azure verwenden, haben Sie auch Zugriff auf die anderen Azure-Dienste, z. B. Media Services, wodurch eine Marketingkampagne optimiert werden kann.

> [WACOM.NOTE]
> Einen Einstieg in Azure Websites vor Anmeldung für ein Konto finden Sie auf <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, wo Sie sofort eine kurzlebige, kostenlose ASP.NET-Startwebsite in Azure Websites erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Obwohl [Azure Cloud Services][csoverview] oder [Azure Virtual Machines][vmoverview] zum Hosten von Websites verwendet werden können, sind diese Komponenten nicht die ideale Lösung für dieses Szenario, sofern nicht eine bestimmte Funktion erforderlich ist, die Azure-Websites nicht zur Verfügung stellt. Weitere Informationen zu den Optionen finden Sie untere [Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich][chooseservice].

In diesem Leitfaden werden die folgenden Themen behandelt:

- [Bereitstellen von bereits vorhandenen Websites](#deployexisting)
- [Integrieren mit sozialen Medien](#socialmedia)
- [Skalieren mithilfe der Benutzeranforderung](#scale)
- [Integrieren mit anderen Diensten](#integrate)
- [Überwachen der Kampagne](#monitor)

> [WACOM.NOTE]
> In diesem Leitfaden werden einige der häufigsten Themen und Aufgaben vorgestellt, die mit der öffentlichen .COM-Websiteentwicklung abgestimmt werden. Allerdings gibt es weitere Funktionen von Azure-Websites, die Sie in Ihrer speziellen Implementierung verwenden können. Weitere Informationen zu diesen Funktionen finden Sie auch unter <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/global-web-presence-solution-overview/">Globale Webpräsenz</a> - und <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/business-application-solution-overview">Geschäftsanwendungen</a>.

##<a name="deployexisting"></a>Bereitstellen vorhandener Websites
Im Szenario zur globalen Webpräsenz haben wir verschiedene Optionen zum Erstellen und Bereitstellen einer neuen Website erläutert. Falls Sie neu bei Azure-Websites sind, empfehlen wir Ihnen, sich [diese Informationen anzusehen][scenarioglobalweb]. Wenn Sie des Öfteren digitale Marketingkampagnen erstellen, ist es möglich, dass Sie über bereits vorhandene Webressourcen verfügen, die Sie für verschiedene Aktionen anpassen. In diesem Abschnitt werden die Optionen zum Bereitstellen verschiedener Websitetypen über die Quellcodeverwaltung genauer erläutert.

Wenn Sie Webressourcen für verschiedene Zwecke erneut verwenden, sollten Sie ein Quellcodeverwaltungssystem unbedingt in Betracht ziehen, falls Sie noch kein System dieser Art nutzen. Mithilfe dieses Systems können Sie Vorlagen von allgemeinen Weblösungen speichern, die für bestimmte Kunden verzweigt und angepasst werden können. Bei Websites ist die Synchronisierung mit vielen verschiedenen Quellcoderepositorys möglich. Wählen Sie auf der Registerkarte **Dashboard** den Link **Bereitstellung über Quellcodeverwaltung einrichten** aus.

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

Ein Dialogfeld mit mehreren Optionen für die Quellcodeverwaltung wird angezeigt. Dazu gehören Quellcodeverwaltungssysteme mit allen Funktionen, wie beispielsweise TFS, sowie einfache Bereitstellungslösungen, wie beispielsweise Dropbox.

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

Sie können verschiedene Techniken für die Quellcodeverwaltung verwenden, um das neue Projekt zu verwalten, das auf einer vorhandenen Baseline beruht. Sie können beispielsweise ein zuvor gespeichertes Baseline-Repository kopieren, um mit der Arbeit am neuen Projekt zu beginnen. Alternativ können Sie auch eine neue Verzweigung erstellen, sodass die Anpassungen für das aktuelle Projekt nachverfolgt werden können. Ein gutes Beispiel für die Verwendung von Verzweigungen beim Verwalten verschiedener Bereitstellungen aus demselben Quellcodeverwaltungs-Repository finden Sie unter [Multiple Environments with Azure Web Sites][gitstaging]. In diesem Beitrag wird gezeigt, wie die Git-Verzweigung zum Verwalten von Staging- und Produktionsumgebungen verwendet wird.

Wenn Sie eine Verbindung zwischen Ihrer Website und der Quellcodeverwaltung hergestellt haben, können Sie die Bereitstellungen über das Portal konfigurieren und nachverfolgen. Weitere Informationen zum Verwenden der Quellcodeverwaltung mit Websites finden Sie unter [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites][publishingwithgit].

Wenn Sie bereits vorhandene Webressourcen verwenden, ist eine gewisse Flexibilität erforderlich, um viele verschiedene Websitetypen hosten zu können. Auf der Registerkarte **Konfigurieren** können Sie sowohl die .NET- als auch die PHP-Unterstützung für Ihre Website auswählen. 

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

Zusätzlich zu diesen Konfigurationsoptionen bietet Websites automatisch Unterstützung für Python 2.7 und Node.js. Die standardmäßige Node.js-Version ist 0.10.5.

Ein weiterer Vorteil von Azure-Websites ist die Geschwindigkeit beim Bereitstellen von gestaffelten Websites im Web. Während der Planung, beim Erstellen eines Prototyps und in der frühen Entwicklungsphase einer Website können die Agentur und deren Kunden tatsächliche Versionen der Kampagnen-Website anschauen, bevor diese veröffentlicht wird. Wenn die Website für die Produktion bereit ist, kann die Agentur entweder die Produktionsbereitstellung für den Kunden verwalten oder die Webressourcen dem Kunden zur Verfügung stellen, damit dieser die Ressourcen bereitstellen und verwalten kann.

##<a name="socialmedia"></a>Integrieren mit sozialen Medien
Bei den meisten digitalen Marketingkampagnen kommen soziale Medienwebsites, wie beispielsweise Facebook oder Twitter, zum Einsatz. Ein Integrationspunkt besteht darin, die Identitäten der sozialen Medien für die Authentifizierung zu nutzen. Ein Beispiel für diese Vorgehensweise in Zusammenhang mit einer ASP.NET-Anwendung finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website][deploysecurewebsite].

Zahlreiche digitale Marketingkampagnen gehen über die Authentifizierung hinaus und nutzen die Integration der sozialen Medien als Schlüsselfunktion der entsprechenden Strategie. Soziale Medienwebsites verfügen normalerweise über einen Entwicklerbereich, in dem verschiedene Möglichkeiten aufgezeigt werden, wie Anwendungen mit den dazugehörigen Diensten integriert werden können. Dienste, bei denen eine REST-API zur Verfügung steht, können mit nahezu allen Web-Frameworks verwendet werden. Oftmals sind Informationen verfügbar, die speziell für die von Ihnen ausgewählte Sprache gelten. Beispielsweise verfügt Twitter über [eine Liste mit den verfügbaren Bibliotheken, die die Twitter-API unterstützen][twitter], einschließlich derer für .NET, Node.js und PHP. Dabei handelt es sich lediglich um ein Beispiel. Suchen Sie auf allen sozialen Medienwebsites, die Sie als Ziel auswählen, direkt nach einem ähnlichen Entwicklerleitfaden.

Bei einem ASP.NET-Entwickler, der Facebook als Ziel hat, verfügt Visual Studio über eine Vorlage für eine MVC 4 Facebook-Anwendung.  

![DigitalMarketingFacebook][DigitalMarketingFacebook]

Eine schnelle Anleitung zur Verwendung dieser Vorlage mit Websites finden Sie unter [Creating a Facebook app using ASP.NET MVC Facebook Templates and hosting them for free on Azure Websites][fbtutorial] (Erstellen einer Facebook-Anwendung mithilfe von ASP.NET MVC Facebook-Vorlagen und kostenloses Hosten der Vorlagen auf Azure-Websites, in englischer Sprache). Ein ausführliches Lernprogramm sowie eine Beispielanwendung finden Sie unter [ASP.NET MVC Facebook Birthday App][fbbirthdayapp] und [Die neue Facebook-Anwendungsvorlage und -Bibliothek für ASP.NET MVC][fbvstemplate].

Wenn Sie ein ASP.NET-Entwickler sind, ist es wichtig zu wissen, dass die Integration mit sozialen Medien nicht auf Vorlagen beschränkt ist, die von Visual Studio zur Verfügung gestellt werden. Mithilfe einer Vorlage wird der Prozess lediglich vereinfacht. Wie bereits erwähnt, werden auf allen sozialen Medienwebsites normalerweise Informationen bereitgestellt, wie eine Verbindung über .NET und viele andere Sprachen und Frameworks hergestellt wird.

##<a name="scale"></a>Skalieren mithilfe der Benutzeranforderung
Bei unvorhersehbaren Arbeitslasten ist Cloud-Computing hilfreich. Digitale Marketingkampagnen gehören zu dieser Kategorie. Es ist schwierig, die Beliebtheit einer relativ kurzlebigen Marketingwebsite vorherzusagen, da vieles davon abhängt, das Interesse der Benutzer zu wecken. Auch die zugehörigen Interaktionen bezüglich der sozialen Medien, sodass zusätzlicher Datenverkehr für die Website generiert wird, spielen eine große Rolle. Azure bietet mehrere Optionen für das Skalieren von Websites und Cloud-Diensten.

- Manuelles Skalieren mithilfe des [Azure-Verwaltungsportals][managementportal].
- Programmgesteuertes Skalieren mithilfe der [Dienstverwaltungs-API][servicemanagementapi] oder [PowerShell-Skripterstellung][powershell].
- Automatisches Skalieren mithilfe der Funktion "Autoscale" (Vorschau)

Navigieren Sie im Verwaltungsportal zur Registerkarte **Skalieren** Ihrer Website. Für die Skalierung sind mehrere Optionen verfügbar. Mit der ersten Option wird der Websitemodus (**Kostenlos**, **Freigegeben** oder **Standard**) festgelegt. 

![DigitalMarketingScale][DigitalMarketingScale]

Bei jeder Ebene kommen jeweils weitere Skalierbarkeitsfunktionen und -optionen hinzu. Beispielsweise können Websites im Modus **Kostenlos** nicht auf mehrere Instanzen verkleinert werden. Websites im Modus **Freigegeben** können hingegen auf 6 Instanzen verkleinert werden. Das Hochskalieren ist auch möglich, indem Sie den Modus **Standard** auswählen. In diesem Modus wird Ihre Website auf dedizierten virtuellen Computern ausgeführt. Darüber hinaus sind die Optionen "Klein", "Mittel" und "Groß" hinsichtlich der Größe des Computers verfügbar. Nachdem Sie die Größe des virtuellen Computers festgelegt haben, können Sie auch auf mehrere Instanzen verkleinern. Im Modus **Standard** ist es möglich, auf 10 Instanzen zu verkleinern. Eine vollständige Liste mit den Unterschieden zwischen den jeweiligen Modi finden Sie unter [Websites - Preisdetails][pricing].

Wenn Sie den Modus **Standard** auswählen, können Sie zusätzlich die Funktion "Autoscale" (Vorschau) aktivieren. Daraufhin erfolgt die Skalierung auf Basis der CPU. Die in Prozent angegebene Option **Ziel-CPU** steht für die Prozessorauslastung, die Azure für Ihre Websiteinstanzen vorgibt. Falls die durchschnittliche CPU-Auslastung unter dieser Vorgabe liegt, verringert Azure die Anzahl der Instanzen, da dieselbe Last auf weniger Instanzen zu einer erhöhten Auslastung auf den übrigen Instanzen führt. Die Anzahl der Instanzen kann nicht auf einen Wert festgelegt werden, der unterhalb des Minimalwerts für **Instanzanzahl** liegt. Falls die durchschnittliche CPU-Auslastung höher ist als die **Ziel-CPU**, erhöht Azure die Anzahl der Instanzen. Wenn dieselbe Last auf zusätzliche Computer verteilt wird, wird die CPU-Auslastung auf jedem einzelnen Computer verringert. Die Anzahl der Instanzen, die hinzugefügt werden können, ist auf den Maximalwert für **Instanzanzahl** beschränkt.

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

Mithilfe der automatischen Skalierung können Ressourcen effektiver genutzt werden. Beispielsweise ist es möglich, dass die digitale Marketingkampagne abends und am Wochenende besonders aktiv ist. Dies gilt auch für ein Szenario, bei dem die Beliebtheit einer Kampagne überraschend steigt. Mit der automatischen Skalierung kann die erhöhte Last effektiv bewältigt werden. Die Anzahl der Instanzen und folglich auch die Kosten werden anschließend verringert, sobald die Last geringer wird. 

Weitere Informationen zum Skalieren von Websites finden Sie unter [Skalieren von Websites][scalewebsite]. Dieses Thema steht in engem Zusammenhang mit der Überwachung. Weitere Informationen finden Sie im entsprechenden Abschnitt dieses Leitfadens unter [Überwachen der Kampagne](#monitor).

> [WACOM.NOTE]
> Für Webanwendungen, bei denen Clouddienste und Webrollen zum Einsatz kommen, ist eine zusätzliche Option für die Skalierung vorhanden, die auf der Länge der in einer Warteschlange befindlichen Elemente basiert. Bei einem Clouddienst sind Rollen, die Backend-Warteschlangen verarbeiten, ein weitverbreitetes Architekturmuster. Weitere Informationen über die Clouddienst-Skalierung finden Sie unter <a href="http://www.windowsazure.com/de-de/manage/services/cloud-services/how-to-scale-a-cloud-service/">Skalieren eines Clouddienstes</a>.

##<a name="integrate"></a>Integrieren mit anderen Diensten
Eine digitale Marketing-Website enthält oftmals Rich-Media-Inhalte, wie zum Beispiel Videostreaming. Werden diese Websites in Azure gehostet, ist eine enge Integration mit den dazugehörigen Azure-Diensten möglich. Sie können beispielsweise Azure Media Services zum Codieren und Streamen von Videos für Ihre Website verwenden. Weitere Informationen zu Media Services finden Sie unter [Azure Media Services - Konzepte][mediaservices].

Weitere Azure-Dienste können zum Erstellen einer stabileren Anwendung genutzt werden. Für Websites kann beispielsweise das verteilte Zwischenspeichern verwendet werden, das über den neuen [Azure Cache Service (Vorschau)][caching] zur Verfügung gestellt wird. Alternativ können Sie auch die Speicherdienste von Azure zum Speichern der Anwendungsdaten und Ressourcen nutzen. Beispielsweise können Grafiken, Videos und andere große Dateien dauerhaft in Blobs gespeichert werden. Datenbankdienste, wie z. B. Azure SQL-Datenbank und MySQL, sind ebenfalls für die Anforderungen bezüglich relationaler Daten verfügbar.

##<a name="monitor"></a>Überwachen der Kampagne
Die Registerkarte **Überwachen** enthält Kennzahlen, mit deren Hilfe Sie den Erfolg und die Leistung Ihrer digitalen Marketing-Website bewerten können.

![DigitalMarketingMonitor][DigitalMarketingMonitor]

Beispielsweise können Sie Auslastungsmuster und -grade überprüfen, indem Sie sich Kennzahlen wie **CPU-Zeit**, **Anforderungen** und **Ausgehende Daten** anzeigen lassen. Alle diese Kennzahlen werden größer, weil die Marketingkampagne beliebter wird. Informationen zur Auslastung können verwendet werden, wenn die Entscheidung erfolgt, wann hoch- oder herunterskaliert wird. Weitere Informationen finden Sie unter [HÜberwachen von Websites][monitoring].

Bei den Modi **Kostenlos** und **Freigegeben** sollten Sie auch Ressourcenkontingente berücksichtigen. Auf der Registerkarte **Dashboard** zeigt das Portal die aktuelle Nutzungsstatistik mehrerer Kontingente an und wann diese Kontingente zurückgesetzt werden. Diese Nutzungsstatistik variiert je nach ausgewähltem Modus. Im folgenden Screenshot ist die angezeigte Statistik für den Modus **Kostenlos** dargestellt. Im Modus **Freigegeben** ist kein Kontingent für **Ausgehende Daten** vorhanden. Im Modus **Standard** werden lediglich **Dateisystemspeicher** und **Größe** angezeigt.

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

Falls Sie der Meinung sind, dass diese Kontingente nahezu erschöpft sind, erwägen Sie einen Wechsel von **Kostenlos** zu **Freigegeben** oder von **Freigegeben** zu **Standard**. Im Modus **Standard** verfügen Sie über dedizierte Ressourcen auf einem oder mehreren kleinen, mittelgroßen oder großen virtuellen Computern. 

Für diese Informationen kann das Verwaltungsportal verwendet werden. Darüber hinaus gibt es zahlreiche Drittanbietertools, die erweiterte Überwachungsdaten für Ihre Websites zur Verfügung stellen. Sie können beispielsweise ein New Relic-Add-on aus dem Azure Store installieren. Eine Anleitung zum Verwenden von New Relic für die Überwachung finden Sie unter [Verwaltung der Anwendungsleistung mit New Relic auf Azure][newrelic]. 

Schließlich können für Websites im Modus "Standard" die Endpunktüberwachung und Warnungen aktiviert werden. Die Endpunktüberwachung versucht, in regelmäßigen Abständen Ihre Website zu erreichen und meldet anschließend die Antwortzeit. Warnungen erfolgen in Form von E-Mail-Benachrichtigungen, falls die Antwortzeit einen bestimmten Schwellenwert überschreitet. Weitere Informationen finden Sie im Abschnitt zur Überwachung des Szenarios [Globale Webpräsenz][scenarioglobalweb] und im Thema [Vorgehensweise: Empfangen von Warnungs-Benachrichtigungen und Verwaltung von Alarmregeln in Azure][receivealerts].

##<a name="summary"></a>Zusammenfassung
Azure-Websites eignen sich gut für wiederverwendbare Webinhalte, die für einzelne Marketingkampagnen angepasst werden. Sie unterstützen zahlreiche gängige Sprachen, Frameworks und Quellcodeverwaltungssysteme, sodass das Migrieren zu diesen Ressourcen und Workflows in der Cloud einfacher ist. Mithilfe der ASP.NET Facebook-Anwendungsvorlage können Facebook-Anwendungen einfacher erstellt werden. Sie können allerdings auch eine beliebige Integration der sozialen Medien von Drittanbietern verwenden, wenn diese Webschnittstellen unterstützt. Über Azure Media Services und andere dazugehörige Dienste in Azure sind zusätzliche Tools verfügbar, sodass eine sorgfältig konzipierte Kampagnen-Website erstellt werden kann. Die zahlreichen Optionen für die manuelle und automatische Skalierung sind bei der Bewältigung der schwer einzuschätzenden Benutzeranforderungen hilfreich. Weitere Informationen finden Sie in den folgenden Fachartikeln:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Bereich</th>
   <th align="left" valign="top">Ressourcen</th>
</tr>
<tr>
   <td valign="middle"><strong>Planen</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/choose-web-app-service">Azure-Websites, Cloud-Dienste und virtuelle Computer: Wann soll welche Technologie eingesetzt werden?</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Erstellen</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-create-websites/">Erstellen und Bereitstellen einer Website</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Bereitstellen</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-deploy/">Bereitstellen einer Azure-Website</a><br/>- <a href="http://www.windowsazure.com/de-de/develop/net/common-tasks/publishing-with-git/">Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Soziale Medien</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/de-de/develop/net/tutorials/web-site-with-sql-database/">Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">Erstellen einer Facebook-App mithilfe von ASP.NET MVC Facebook-Vorlagen</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">Facebook-App-Vorlage und Bibliothek für ASP.NET MVC</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Skalieren</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-scale-websites/">Skalieren von Websites</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Rich-Media-Inhalte</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223282.aspx">Azure Media Services - Konzepte</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Überwachen</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-monitor-websites/">Überwachen von Websites</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure.</a></td>
</tr>
</table>

  [websitesoverview]:/de-de/documentation/services/web-sites/
  [csoverview]:/de-de/documentation/services/cloud-services/
  [vmoverview]:/de-de/documentation/services/virtual-machines/
  [chooseservice]:/de-de/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/de-de/manage/services/web-sites/global-web-presence-solution-overview/
  
  
  [publishingwithgit]:/de-de/develop/net/common-tasks/publishing-with-git/
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [deploysecurewebsite]:/de-de/develop/net/tutorials/web-site-with-sql-database/
  [twitter]:https://dev.twitter.com/docs/twitter-libraries#dotnet
  [fbtutorial]:http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [fbbirthdayapp]:http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [fbvstemplate]:http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [managementportal]:http://manage.windowsazure.com/
  [servicemanagementapi]:http://msdn.microsoft.com/de-de/library/windowsazure/ee460799.aspx
  [powershell]:http://msdn.microsoft.com/de-de/library/windowsazure/jj152841.aspx
  [pricing]:https://www.windowsazure.com/de-de/pricing/details/web-sites/
  [scalewebsite]:/de-de/manage/services/web-sites/how-to-scale-websites/
  
  [mediaservices]:http://msdn.microsoft.com/de-de/library/windowsazure/dn223282.aspx
  [caching]:http://msdn.microsoft.com/de-de/library/windowsazure/dn386094.aspx
  [monitoring]:/de-de/manage/services/web-sites/how-to-monitor-websites/
  [newrelic]:/de-de/develop/net/how-to-guides/new-relic/
  [receivealerts]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
  
  
  
   [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  
  
  
  
  

<!--HONumber=35.1-->

<!--HONumber=35.1-->

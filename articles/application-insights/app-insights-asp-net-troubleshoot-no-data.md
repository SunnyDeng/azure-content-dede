<properties 
	pageTitle="Problembehandlung ohne Daten – Application Insights für .NET" 
	description="Sie sehen in Visual Studio Application Insights keine Daten? Versuchen Sie es hier." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016" 
	ms.author="awills"/>
 
# Problembehandlung ohne Daten – Application Insights für .NET


## Statusmonitor-Probleme

*Ich habe [Statusmonitor auf meinem Webserver installiert](app-insights-monitor-performance-live-website-now.md), um vorhandene Apps zu überwachen. Es werden keine Ergebnisse angezeigt.*

Siehe [Problembehandlung für den Statusmonitor](app-insights-monitor-performance-live-website-now.md#troubleshooting). Das häufigste Problem sind Firewallports.


## <a name="q01"></a>Keine Option „Application Insights hinzufügen“ in Visual Studio

*Wenn ich in Visual Studio ein neues Projekt erstelle oder im Projektmappen-Explorer mit der rechten Maustaste auf ein vorhandenes Projekt klicke, werden keine Application Insights-Optionen angezeigt.*

+ Nicht alle Typen von .NET-Projekten werden von den Tools unterstützt. Web- und WCF-Projekte werden unterstützt. Für andere Projekttypen, z. B. Desktop- oder Dienstanwendungen, können Sie [Ihrem Projekt trotzdem manuell ein Application Insights-SDK hinzufügen](app-insights-windows-desktop.md).
+ Stellen Sie sicher, dass Sie über [Visual Studio 2013 Update 3 oder höher](http://go.microsoft.com/fwlink/?LinkId=397827) verfügen. Dies ist bei den Application Insights-Tools vorinstalliert.
+ Wählen Sie **Extras** > **Erweiterungen und Updates**, und stellen Sie sicher, dass die **Application Insights-Tools** installiert und aktiviert sind. Wenn dies der Fall ist, klicken Sie auf **Updates**, um zu prüfen, ob ein Update verfügbar ist.
+ Öffnen Sie das Dialogfeld „Neues Projekt“, und wählen Sie die ASP.NET-Webanwendung aus. Wenn die Application Insights-Option hier angezeigt wird, sind die Tools installiert. Wenn nicht, können Sie versuchen, die Application Insights-Tools zu deinstallieren und dann neu zu installieren.


## <a name="q02"></a>Fehler beim Hinzufügen von Application Insights

*Wenn ich ein neues Webprojekt erstelle oder versuche, Application Insights einem vorhandenen Projekt hinzuzufügen, wird eine Fehlermeldung angezeigt.*

Wahrscheinliche Ursachen:

* Es liegt ein Fehler bei der Kommunikation mit dem Application Insights-Portal vor.
* Es gibt ein Problem mit Ihrem Azure-Konto.
* Sie verfügen nur über [Lesezugriff auf das Abonnement oder die Gruppe, in dem oder der Sie die neue Ressource erstellen möchten](app-insights-resources-roles-access-control.md).

Behebung:

+ Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben. 
+ Überprüfen Sie in Ihrem Browser, ob Sie auf das [Azure-Portal](https://portal.azure.com) zugreifen können. Öffnen Sie "Einstellungen", und stellen Sie fest, ob eine Einschränkung besteht.
+ [Fügen Sie Application Insights einem vorhandenen Projekt hinzu](app-insights-asp-net.md): Klicken Sie mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie "Application Insights hinzufügen" aus.
+ Wenn es immer noch nicht funktioniert, führen Sie das [manuelle Verfahren](app-insights-start-monitoring-app-health-usage.md)aus, um eine Ressource im Portal hinzuzufügen, und fügen Sie anschließend das SDK zum Projekt hinzu. 

## <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Es scheint ein Fehler aufgetreten zu sein, während Sie Application Insights oder vielleicht einen Protokollierungsadapter installiert haben.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`, und wählen Sie die Option **Application Insights konfigurieren**. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt.


##<a name="NuGetBuild"></a> „NuGet-Pakete fehlen“ auf meinem Buildserver

*Alles wird problemlos erstellt, wenn ich auf meinem Entwicklungscomputer das Debuggen durchführe, aber ich erhalte einen NuGet-Fehler auf dem Buildserver.*

Informationen hierzu finden Sie unter [NuGet-Paketwiederherstellung](http://docs.nuget.org/Consume/Package-Restore) und [Automatische Paketwiederherstellung](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## Fehlender Menübefehl zum Öffnen von Application Insights aus Visual Studio

*Wenn ich im Projektmappen-Explorer mit der rechten Maustaste auf mein Projekt klicke, werden keine Application Insights-Befehle angezeigt, oder der Befehl „Application Insights öffnen“ wird nicht angezeigt.*

Wahrscheinliche Ursachen:

* Sie haben die Application Insights-Ressource manuell erstellt, oder das Projekt hat einen Typ, der von den Application Insights-Tools nicht unterstützt wird.
* Die Application Insights-Tools sind in Ihrer Visual Studio-Anwendung deaktiviert.
* Ihre Visual Studio-Version ist älter als 2013 Update 3.

Behebung:

* Stellen Sie sicher, dass Sie die Visual Studio-Version 2013 Update 3 oder höher verwenden.
* Wählen Sie **Extras** > **Erweiterungen und Updates**, und stellen Sie sicher, dass die **Application Insights-Tools** installiert und aktiviert sind. Wenn dies der Fall ist, klicken Sie auf **Updates**, um zu prüfen, ob ein Update verfügbar ist.
* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt. Wenn Sie den Befehl **Application Insights konfigurieren** sehen, können Sie ihn verwenden, um Ihr Projekt mit der Ressource im Application Insights-Dienst zu verbinden.


Andernfalls wird Ihr Projekttyp von den Application Insights-Tools nicht direkt unterstützt. Melden Sie sich zum Anzeigen Ihrer Telemetriedaten am [Azure-Portal](https://portal.azure.com) an, wählen Sie in der linken Navigationsleiste „Application Insights“, und wählen Sie dann Ihre Anwendung aus.

## „Zugriff verweigert“ beim Öffnen von Application Insights aus Visual Studio

*Mit dem Menübefehl „Application Insights öffnen“ gelange ich zum Azure-Portal, aber ich erhalte den Fehler „Zugriff verweigert“.*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Für die Microsoft-Anmeldung, die Sie zuletzt in Ihrem Standardbrowser verwendet haben, besteht kein Zugriff auf [die Ressource, die beim Hinzufügen von Application Insights zu dieser App erstellt wurde](app-insights-asp-net.md). Es gibt zwei wahrscheinliche Ursachen:

* Sie besitzen mehr als ein Microsoft-Konto, z. B. ein Geschäftskonto und ein persönliches Microsoft-Konto. Die Anmeldung, die Sie zuletzt in Ihrem Standardbrowser verwendet haben, gilt für ein anderes Konto als das Konto, für das Zugriff zum [Hinzufügen von Application Insights zum Projekt](app-insights-asp-net.md) besteht. 

 * Behebung: Klicken Sie oben rechts im Browserfenster auf Ihren Namen, und melden Sie sich ab. Melden Sie sich mit dem Konto an, für das Zugriff besteht. Klicken Sie dann in der linken Navigationsleiste auf „Application Insights“, und wählen Sie Ihre App aus.

* Eine andere Person hat Application Insights dem Projekt hinzugefügt und vergessen, Ihnen [Zugriff auf die Ressourcengruppe](app-insights-resources-roles-access-control.md) zu gewähren, in der die Erstellung durchgeführt wurde.

 * Behebung: Falls die Person ein Unternehmenskonto verwendet hat, kann sie Ihren Namen dem Team hinzufügen, oder sie kann Ihnen individuellen Zugriff auf die Ressourcengruppe gewähren.



## „Ressource nicht gefunden“ beim Öffnen von Application Insights aus Visual Studio

*Mit dem Menübefehl „Application Insights öffnen“ gelange ich zum Azure-Portal, aber ich erhalte den Fehler „Ressource nicht gefunden“.*

Wahrscheinliche Ursachen:

* Die Application Insights-Ressource für Ihre Anwendung wurde gelöscht. -oder-
* Der Instrumentierungsschlüssel wurde in „ApplicationInsights.config“ festgelegt oder geändert, indem er direkt bearbeitet wurde, ohne die Projektdatei zu aktualisieren. 

Mit dem Instrumentierungsschlüssel in „ApplicationInsights.config“ wird gesteuert, wohin die Telemetriedaten gesendet werden. Über eine Zeile in der Projektdatei wird gesteuert, welche Ressource geöffnet wird, wenn Sie den Befehl in Visual Studio verwenden.

Behebung:

* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights“ und dann „Application Insights konfigurieren“. Im Dialogfeld können Sie wählen, ob Sie die Telemetriedaten an eine vorhandene Ressource senden oder eine neue erstellen. Oder:
* Öffnen Sie die Ressource direkt. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, klicken Sie in der linken Navigationsleiste auf „Application Insights“, und wählen Sie Ihre App aus.



## Wo finde ich meine Telemetriedaten?

*Ich habe mich am [Microsoft Azure-Portal](https://portal.azure.com) angemeldet, und das Dashboard auf der Azure-Startseite wird angezeigt. Wo finde ich nun meine Application Insights-Daten?*

* Klicken Sie in der linken Navigationsleiste auf „Application Insights“ und dann auf den Namen Ihrer App. Wenn noch keine Projekte vorhanden sind, müssen Sie [Application Insights Ihrem Webprojekt hinzufügen oder konfigurieren](app-insights-asp-net.md).

    Es werden einige Diagramme mit Zusammenfassungen angezeigt. Beim Durchklicken können Sie weitere Details einblenden.

* Klicken Sie in Visual Studio beim Debuggen Ihrer App auf die Schaltfläche „Application Insights“.


## <a name="q03"></a> Keine Serverdaten (oder überhaupt keine Daten)

*Ich habe meine App ausgeführt und dann den Application Insights-Dienst in Microsoft Azure geöffnet, aber für alle Diagramme wird „Erfahren Sie, wie Sie...“ oder „Nicht konfiguriert“ angezeigt.* Oder es sind *nur Seitenansichts- und Benutzerdaten zu sehen, keine Serverdaten.*

+ Führen Sie Ihre Anwendung in Visual Studio im Debugmodus aus (F5). Verwenden Sie die Anwendung, um einige Telemetriedaten zu generieren. Überprüfen Sie, ob im Visual Studio-Ausgabefenster protokollierte Ereignisse angezeigt werden. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ Öffnen Sie im Application Insights-Portal die [Diagnosesuche](app-insights-diagnostic-search.md). Hier werden Daten normalerweise zuerst angezeigt.
+ Klicken Sie auf die Schaltfläche "Aktualisieren". Das Blatt aktualisiert sich in regelmäßigen Abständen selbst, doch Sie können es auch manuell aktualisieren. Das Aktualisierungsintervall für größere Zeiträume ist länger.
+ Überprüfen Sie, ob die Instrumentierungsschlüssel übereinstimmen. Sehen Sie sich im Application Insights-Portal auf dem Hauptblatt Ihrer App in der Dropdownliste **Essentials** den **Instrumentierungsschlüssel** an. Öffnen Sie dann in Ihrem Projekt in Visual Studio die Datei „ApplicationInsights.config“, und suchen Sie nach `<instrumentationkey>`. Überprüfen Sie, ob die beiden Schlüssel identisch sind. Gehen Sie wie folgt vor, falls dies nicht so ist:
 + Klicken Sie im Portal auf „Application Insights“, und suchen Sie nach der App-Ressource mit dem richtigen Schlüssel. -oder-
 + Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights“ und dann „Konfigurieren“. Setzen Sie die App zurück, um Telemetriedaten an die richtige Ressource zu senden.
 + Gehen Sie wie folgt vor, wenn Sie die übereinstimmenden Schlüssel nicht finden können: Stellen Sie sicher, dass Sie in Visual Studio und im Portal die gleichen Anmeldeinformationen verwenden.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ Sehen Sie sich auf dem [Dashboard der Microsoft Azure-Startseite](https://portal.azure.com) die Karte zur Dienstintegrität an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
+ Schauen Sie auch in [unserem Statusblog](http://blogs.msdn.com/b/applicationinsights-status/) nach.
+ Haben Sie Code für das [serverseitige SDK](app-insights-api-custom-events-metrics.md) geschrieben, mit dem der Instrumentierungsschlüssel in `TelemetryClient`-Instanzen oder in `TelemetryContext` geändert wird? Oder haben Sie eine [Konfiguration für die Filterung oder Stichprobenerstellung](app-insights-api-filtering-sampling.md) geschrieben, bei der ggf. zu viel herausgefiltert wird?
+ Wenn Sie „ApplicationInsights.config“ bearbeitet haben, sollten Sie die Konfiguration von [TelemetryInitializers und TelemetryProcessors](app-insights-api-filtering-sampling.md) sorgfältig überprüfen. Ein falsch benannter Typ oder Parameter kann dazu führen, dass das SDK keine Daten gesendet.

## <a name="q04"></a>Keine Daten zu Seitenansichten, Browsern, Verwendung

*Ich sehe Daten in Diagrammen zur Serverantwortzeit und zu Serveranforderungen, aber keine Daten zur Dauer der Seitenansicht oder auf den Blättern „Browser“ oder „Verwendung“.*

Die Daten kommen aus Skripts auf den Webseiten.

+ Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen](app-insights-javascript.md).
+ Stellen Sie sicher, dass Internet Explorer Ihre Website nicht im Kompatibilitätsmodus anzeigt.
+ Verwenden Sie die Debugfunktion Ihres Browsers (bei einigen Browsern drücken Sie F12 und wählen anschließend „Netzwerk“), um sicherzustellen, dass Daten an `dc.services.visualstudio.com` gesendet werden.

## Keine Abhängigkeits- oder Ausnahmedaten

Weitere Informationen finden Sie unter [Telemetriedaten zu Abhängigkeiten](app-insights-asp-net-dependencies.md) und [Telemetriedaten für Ausnahmen](app-insights-asp-net-exceptions.md).

## Keine Daten (Serverdaten), seitdem ich die App auf meinem Server veröffentlicht habe

+ Überprüfen Sie, ob Sie tatsächlich alle Daten von Microsoft kopiert haben. ApplicationInsights-DLLs auf dem Server, zusammen mit „Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll“
+ In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.
+ Wenn Sie einen Proxy verwenden müssen, um Daten aus Ihrem Unternehmensnetzwerk zu senden, legen Sie [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in der Datei "Web.config" fest.
+ Windows Server 2008: Stellen Sie sicher, dass folgende Updates installiert sind: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).


## Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.

* Überprüfen Sie den [Statusblog](http://blogs.msdn.com/b/applicationinsights-status/).
* Ist Ihr monatliches Kontingent an Datenpunkten erreicht? Öffnen Sie "Einstellungen – Kontingente und Preisübersicht", um es herauszufinden. Wenn dies der Fall ist, können Sie Ihren Plan aktualisieren oder zusätzliche Kapazität erwerben. Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/application-insights/).


## Nicht alle Daten werden erwartungsgemäß angezeigt.

Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, können Sie die [adaptive Stichprobenerstellung](app-insights-sampling.md) nutzen, bei der nur ein bestimmter Prozentsatz der Telemetriedaten übermittelt wird.

Sie können diese Funktion deaktivieren, aber dies ist nicht zu empfehlen. Die Stichprobenerstellung ist so konzipiert, dass die zugehörigen Telemetriedaten zu Diagnosezwecken richtig übertragen werden.

## Falsche geografische Daten in Benutzertelemetriedaten

Die Dimensionen für Ort, Region und Land werden von IP-Adressen abgeleitet und sind nicht immer exakt.

## Ausnahme „Methode nicht gefunden“ bei der Ausführung in Azure Cloud Services

Haben Sie für .NET 4.6 erstellt? 4.6 wird nicht automatisch in Azure Cloud Services-Rollen unterstützt. [Installieren Sie 4.6 für jede Rolle](../cloud-services/cloud-services-dotnet-install-dotnet.md), bevor Sie Ihre App ausführen.

## Noch nicht funktionsfähig ...

* [Application Insights-Forum](https://social.msdn.microsoft.com/Forums/vstudio/de-DE/home?forum=ApplicationInsights)

<!---HONumber=AcomDC_0224_2016-->
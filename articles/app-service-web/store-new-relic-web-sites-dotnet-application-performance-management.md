<properties 
	pageTitle=".NET Web-App in Azure App Service mit Verwaltung der Anwendungsleistung mit New Relic" 
	description="Erfahren Sie, wie Sie die Leistungsüberwachung von New Relic für ASP.NET-Anwendungen in Azure App Service verwenden." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="stepsic"/>



# .NET Web-App in Azure App Service mit Verwaltung der Anwendungsleistung mit New Relic

In diesem Leitfaden wird beschrieben, wie Sie Ihrer Web-App in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) die erstklassige Leistungsüberwachung von New Relic hinzufügen. Wir erläutern das schnelle und einfache Verfahren zum Hinzufügen von New Relic zu Anwendungen und stellen einige der Funktionen von New Relic vor. Weitere Informationen zum Arbeiten mit New Relic finden Sie unter [Verwenden von New Relic](#using-new-relic).

## Was ist New Relic?

New Relic ist ein für Entwickler konzipiertes Tool, mit dem Produktionsanwendungen überwacht und detaillierte Informationen zu deren Leistung und Zuverlässigkeit abgerufen werden können. Es beschleunigt die Identifizierung und Diagnostizierung von Leistungsproblemen und gibt Ihnen die benötigten Informationen an die Hand, um diese Probleme zu lösen.

New Relic verfolgt die Ladezeit und den Durchsatz von Webtransaktionen, sowohl vom Server als auch von den Browsern der Benutzer. Das Tool zeigt, wie viel Zeit Sie in der Datenbank verbracht haben, analysiert langsame Abfragen und Webanforderungen, bietet Verfügbarkeitsüberwachung und Warnfunktionen, verfolgt Anwendungsausnahmen und vieles mehr.

## Sonderpreis für New Relic im Azure Store

New Relic Standard steht Azure-Benutzern kostenlos zur Verfügung. New Relic Pro wird in mehreren Paketen angeboten – basierend auf dem verwendeten Websitemodus und der Instanzgröße, falls Sie den reservierten Modus verwenden.

Preisinformationen finden Sie auf der [New Relic-Seite im Azure Marketplace](/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE] Es werden nur Preise für bis zu zehn Compute-Instanzen aufgeführt. Volumenpreise für mehr als zehn Instanzen erfragen Sie bitte bei New Relic (sales@newrelic.com).

Azure-Kunden erhalten bei Bereitstellung des New Relic-Agenten ein zweiwöchiges Testabonnement von New Relic Pro.

Anmelden bei New Relic über den Azure Marketplace --

New Relic kann nahtlos in Azure-Web- und -Workerrollen sowie Azure App Service integriert werden.

Um sich direkt im Azure Marketplace bei New Relic anzumelden, führen Sie diese vier einfachen Schritte aus.

## Schritt 1: Erstellen eines New Relic-Kontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und klicken Sie in der Ecke auf **Neu**.
3. Klicken Sie auf **Entwicklerdienste** > **New Relic-APM**.
4. Konfigurieren Sie Ihr New Relic-Konto mit den folgenden Angaben, und klicken Sie dann auf **Erstellen**.
	- **Name**
	- **Tarif**
	- **Ressourcengruppe**
	- **Abonnement**
	- **Standort**
	- **Rechtliche Bedingungen**

11. Nachdem Sie auf **Erstellen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status überwachen, indem Sie auf die Schaltfläche **Benachrichtigungen** klicken. Nach dem Erstellen wird das Blatt des New Relic-Kontos angezeigt.

12. Um Ihren New Relic-Lizenzschlüssel abzurufen, zeigen Sie oben im Blatt den Bereich **Essentials** an. Die Web-App-Instanz registriert diesen Lizenzschlüssel automatisch in den App-Einstellungen, wenn Sie die Web-App in Ihr New Relic-Konto integrieren.

## Schritt 2: Konfigurieren der New Relic-Integration für die Web-App

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) das Blatt Ihrer Web-App.
2. Klicken Sie im oberen Bereich des Blatts auf das Menü "...", und wählen Sie **Kacheln hinzufügen**.
3. Wählen Sie auf der Registerkarte **Überwachung** **Anwendungsübersicht**, und ziehen Sie die Kachel auf die gewünschte Stelle im Blatt für die Web-App.
4. Klicken Sie auf „Fertig“, um das Hinzufügen der Kacheln zu beenden.
5. Klicken Sie auf die Kachel **Anwendungsüberwachung**, und wählen Sie **New Relic**.
6. Wählen Sie das im vorherigen Schritt erstellte Konto aus, und klicken Sie auf **OK**. 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	Klicken Sie nach Abschluss des Vorgangs auf dem Blatt der Web-App auf **Alle Einstellungen** und anschließend auf **Anwendungseinstellungen**. Die Einstellung **NEWRELIC\_LICENSEKEY** sollte dem Abschnitt **App-Einstellungen** des Blatts zur Unterstützung von New Relic hinzugefügt worden sein:

	>[AZURE.NOTE] Es kann bis zu 30 Sekunden dauern, bis die neuen App-Einstellungen wirksam sind. Damit die Einstellungen sofort wirksam werden, starten Sie die Web-App neu.

## Schritt 3: Veröffentlichen der ASP.NET-Web-App

Veröffentlichen Sie Ihre Web-App mit Visual Studio. Wenn Sie Ihre Web-App bereits veröffentlicht haben, veröffentlichen Sie diese erneut, damit die Web-App-Instanz das erforderliche New Relic NuGet-Paket zur Aktivierung der New Relic-Überwachung hinzufügt.

## Schritt 4: Überprüfen der Anwendungsleistung in New Relic.

So zeigen Sie das New Relic-Dashboard an:

2. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) das Blatt Ihrer Web-App.
3. Klicken Sie auf **Anwendungsüberwachung** > **Anwendungsname** > **View at New Relic**.

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. Wenn Sie Ihr Konto erstmalig verwenden, konfigurieren Sie Ihre Kontoinformationen.
3. Wählen Sie in der New Relic-Menüleiste **Applications > (Anwendungsname)** aus.

	Das Dashboard **Monitoring > Overview** wird automatisch angezeigt.

	![New Relic-Überwachungsdashboard](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

	Nachdem Sie in der Liste des Menüs **Applications** eine App ausgewählt haben, werden im Dashboard **Overview** aktuelle App-Server- und Browserinformationen angezeigt.

### <a id="using-new-relic"></a>Verwenden von New Relic

Nachdem Sie Ihre App in der Liste des Menüs "Applications" ausgewählt haben, werden im Dashboard "Overview" aktuelle App-Server- und Browserinformationen angezeigt. Zum Umschalten zwischen den zwei Ansichten klicken Sie auf die Schaltfläche **App server** oder **Browser**.

Neben der <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">standardmäßigen New Relic-Benutzeroberfläche</a> und den <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">Dashboard-Drilldown</a>-Funktionen bietet das Dashboard "Overview" weitere Funktionen.

<table border="1">
  <thead>
    <tr>
      <th><b>Gewünschte Aktion</b></th>
      <th><b>Maßnahme...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Anzeigen von Dashboardinformationen für den ausgewählten App-Server oder Browser</td>
       <td>Klicken Sie auf die Schaltfläche <b>App server</b> oder <b>Browser</b>.</td>
    </tr>
     <tr>
       <td>Anzeigen von Schwellenwerten für das <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a>-Ergebnis Ihrer App</td>
       <td>Zeigen Sie auf das <b>?<b>-Symbol</b></b> des Apdex-Ergebnisses.</td>
    </tr>
    <tr>
       <td>Anzeigen weltweiter Apdex-Details</td>
       <td>Zeigen Sie in der <b>Browser</b>-Ansicht der Übersicht auf eine beliebige Stelle der globalen Apdex-Karte.<br /><b>Tipp:</b> Wenn Sie direkt zum Dashboard <a href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> der ausgewählten App wechseln möchten, klicken Sie auf den Titel <b>Global Apdex</b>, oder klicken Sie auf eine beliebige Stelle der globalen Apdex-Karte.</td>
    </tr>
    <tr>
       <td>Anzeigen des Dashboards <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web Transactions</a></td>
       <td>Klicken Sie im Dashboard "Overview" auf die Tabelle "Web Transactions". Wenn Sie Details zu einer bestimmten Webtransaktion (einschließlich <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a> anzeigen möchten), klicken Sie auf den entsprechenden Namen.</td>
    </tr>
    <tr>
       <td>Anzeigen des Dashboards <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td>Klicken Sie im Dashboard "Overview" der Anwendung auf den Titel des Fehlerratendiagramms.<br /><b>Tipp:</b> Sie können das Dashboard "Errors" auch über <b>Applications > (Ihre App) > Events > Errors</b> anzeigen.</td>
    </tr>
    <tr>
       <td>Anzeigen von Details zum App-Server</td>
       <td><p>Führen Sie einen der folgenden Schritte aus:<p>
        <ul>
          <li>Schalten Sie zwischen einer Tabellenansicht der Hosts und ausführlichen Metrikdetails zu jedem Host um.</li>
          <li>Klicken Sie auf den Namen eines einzelnen Servers.</li>
          <li>Zeigen Sie auf das Apdex-Ergebnis eines einzelnen Servers.</li>
          <li>Klicken Sie auf die CPU-Auslastung oder den Arbeitsspeicher eines einzelnen Servers.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Nachfolgend sehen Sie ein Beispiel des Dashboards "Overview" bei Auswahl der Browseransicht.

![Paket-Manager-Konsole](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## Nächste Schritte

In den folgenden Ressourcen finden Sie weitere Informationen:

 * [Installieren des .NET-Agents für Azure Web-Apps](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-preview-portal#install-new-relic-azure-webapps): Vorgehensweise für das Installieren des New Relic-.NET-Agents 
 * [New Relic-Benutzeroberfläche](https://newrelic.com/docs/site/the-new-relic-ui): Überblick über die New Relic-Benutzeroberfläche, Festlegen von Benutzerrechten und -profilen, Verwenden von Standardfunktionen und Details zu Drilldowns in Dashboards
 * [Anwendungsübersicht](https://newrelic.com/docs/site/applications-overview): Merkmale und Funktionen des New Relic-Dashboards "Overview"
 * [Apdex](https://newrelic.com/docs/site/apdex): Erläuterung, wie Apdex die Endbenutzerzufriedenheit mit Ihrer Anwendung misst
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Überblick darüber, wie mit RUM (Real User Monitoring) die Zeit gemessen wird, die das Laden Ihrer Webseiten in den Browsern von Benutzern dauert, woher diese Benutzer stammen und welche Browser sie verwenden
 * [Finding Help](https://newrelic.com/docs/site/finding-help): Über das Online-Hilfecenter von New Relic aufrufbare Ressourcen

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).


[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!---HONumber=AcomDC_0128_2016-->
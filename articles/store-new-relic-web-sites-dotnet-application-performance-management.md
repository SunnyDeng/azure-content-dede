<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Web Sites" authors="larryfr" solutions="" manager="" editor="" />

Verwaltung der Anwendungsleistung mit New Relic auf Azure-Websites
==================================================================

In diesem Leitfaden wird beschrieben, wie Sie Ihrer Azure-Website die erstklassige Leistungsüberwachung von New Relic hinzufügen. Wir erläutern das schnelle und einfache Verfahren zum Hinzufügen von New Relic zu Anwendungen und stellen einige der Funktionen von New Relic vor. Weitere Informationen zum Arbeiten mit New Relic finden Sie unter [Verwenden von New Relic](#using-new-relic).

Was ist New Relic?
------------------

New Relic ist ein für Entwickler konzipiertes Tool, mit dem Produktionsanwendungen überwacht und detaillierte Informationen zu deren Leistung und Zuverlässigkeit abgerufen werden können. Es beschleunigt die Identifizierung und Diagnostizierung von Leistungsproblemen und gibt Ihnen die benötigten Informationen an die Hand, um diese Probleme zu lösen.

New Relic verfolgt die Ladezeit und den Durchsatz von Webtransaktionen, sowohl vom Server als auch von den Browsern der Benutzer. Das Tool zeigt, wie viel Zeit Sie in der Datenbank verbracht haben, analysiert langsame Abfragen und Webanforderungen, bietet Verfügbarkeitsüberwachung und Warnfunktionen, verfolgt Anwendungsausnahmen und vieles mehr.

Sonderpreis für New Relic im Azure Store
----------------------------------------

New Relic Standard steht Azure-Benutzern kostenlos zur Verfügung. New Relic Pro wird in mehreren Paketen angeboten – basierend auf dem verwendeten Websitemodus und der Instanzgröße, falls Sie den reservierten Modus verwenden.

Preisinformationen finden Sie auf der [New Relic-Seite im Azure Store](http://www.windowsazure.com/de-de/gallery/store/new-relic/new-relic/).

**Hinweis:**

Es werden nur Preise für bis zu zehn Serverinstanzen aufgeführt. Volumenpreise für mehr als zehn Instanzen erfragen Sie bitte bei New Relic (sales@newrelic.com).

Azure-Kunden erhalten bei Bereitstellung des New Relic-Agenten ein zweiwöchiges Testabonnement von New Relic Pro.

Anmelden bei New Relic über den Azure Store
-------------------------------------------

New Relic kann nahtlos in Azure-Web- und -Workerrollen sowie -Websites integriert werden.

Um sich direkt im Azure Store bei New Relic anmelden, führen Sie diese vier einfachen Schritte aus.

### Schritt 1. Anmelden über den Azure Store

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2.  Klicken Sie im unteren Bereich des Verwaltungsportals auf **Neu**.
3.  Klicken Sie auf **Speichern**.
4.  Wählen Sie im Dialogfeld **Add-On auswählen** die Option **New Relic** aus, und klicken Sie auf **Weiter**.
5.  Wählen Sie im Dialogfeld **Add-On personalisieren** den gewünschten New Relic-Plan aus.
6.  Geben Sie den Namen ein, mit dem der New Relic-Dienst in Ihren Azure-Einstellungen angezeigt werden soll, oder wählen Sie den Standardwert **NewRelic** aus. Dieser Name muss in der Liste der abonnierten Azure Store-Elemente eindeutig sein.
7.  Wählen Sie einen Wert für die Region aus, z. B. **West US**.
8.  Klicken Sie auf **Weiter**.
9.  Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen im Dialogfeld **Kauf überprüfen**. Wenn Sie den Bedingungen zustimmen, klicken Sie auf **Kaufen**.
10. Nachdem Sie auf **Kaufen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status im Azure-Verwaltungsportal überwachen.
11. Um Ihren New Relic-Lizenzschlüssel abzurufen, klicken Sie auf das gerade erstellte Add-on und dann auf **Verbindungsinformationen**.
12. Kopieren Sie den angezeigten Lizenzschlüssel. Diesen müssen Sie bei der Installation des New Relic Nuget-Pakets eingeben.

### Schritt 2. Installieren des New Relic-Pakets

Der New Relic Web Sites-Agent wird als NuGet-Paket verteilt, das Sie Ihrer Website mit Visual Studio oder WebMatrix hinzufügen können. Falls Sie keine Erfahrung mit dem Verwenden von Visual Studio oder WebMatrix mit einer Azure-Website haben, erhalten Sie Informationen unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website](http://www.windowsazure.com/de-de/develop/net/tutorials/get-started/) und [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix](http://www.windowsazure.com/de-de/develop/net/tutorials/website-with-webmatrix/).

Führen Sie die folgenden Schritte für die von Ihnen verwendete Entwicklungsumgebung aus:

**Visual Studio**

1.  Öffnen Sie Ihre Visual Studio-Websitelösung.

2.  Wählen Sie **Tools \> Library Package Manager \> Package Manager Console**, um die Paket-Manager-Konsole zu öffnen. Wählen Sie Ihr Projekt oben im Fenster der Paket-Manager-Konsole als Standardprojekt aus.

    ![Paket-Manager-Konsole](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3.  Verwenden Sie an der Paket-Manager-Eingabeaufforderung den folgenden Befehl zum Installieren des Pakets:

         Install-Package NewRelic.Azure.WebSites

4.  Geben Sie an der Eingabeaufforderung für den Lizenzschlüssel den vom Azure Store erhaltenen Lizenzschlüssel ein.

    ![Lizenzschlüssel eingeben](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png)

**WebMatrix**

1.  Öffnen Sie Ihre Website mit WebMatrix.

2.  Wählen Sie auf der Registerkarte **Start** des Menübands **NuGet** aus.

    ![Nuget-Schaltfläche auf der Start-Registerkarte](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png)

3.  Legen Sie in der NuGet-Galerie die Quelle auf **NuGet Official Package Source** fest, und suchen Sie dann nach "NewRelic.Azure.WebSites".

    ![Nuget-Galeriesuche nach NewRelic.Azure.WebSites](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png)

4.  Wählen Sie den Eintrag **New Relic for Azure Web Sites** aus, und klicken Sie dann auf **Installieren**.

5.  Nach der Installation des Pakets enthält Ihre Website einen Ordner namens **newrelic**. Erweitern Sie diesen Ordner, und öffnen Sie die Datei **newrelic.config**. Ersetzen Sie in dieser Datei den Wert **REPLACE\_WITH\_LICENSE\_KEY** durch den Lizenzschlüssel, den Sie im Azure Store erhalten haben.

    ![erweiterter newrelic-Ordner mit newrelic.conf ausgewählt](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png)

    Nach dem Hinzufügen der Lizenzschlüsselinformationen speichern Sie die Änderungen in der Datei **newrelic.config**.

### Schritt 3. Konfigurieren der Website und Veröffentlichen der Anwendung.

Das New Relic-Paket, das im vorherigen Schritt Ihrer Anwendung hinzugefügt wurde, wird durch **App-Einstellungen** konfiguriert und Ihrer Azure-Website hinzugefügt. Führen Sie zum Hinzufügen dieser Einstellungen die folgenden Schritte aus.

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com) an, und wechseln Sie zu Ihrer Website.

2.  Wählen Sie auf der Website **Konfigurieren** aus. Wählen Sie im Abschnitt **Developer Analytics** die Option **Add-on** oder **Benutzerdefiniert** aus. Beide Methoden erzeugen die gleiche Ausgabe, erfordern aber eine leicht unterschiedliche Eingabe. **Add-on** listet Ihre aktuellen New Relic-Lizenzen auf und ermöglicht die Auswahl einer Lizenz, während **Benutzerdefiniert** erfordert, dass Sie den Lizenzschlüssel manuell angeben.

    Wenn Sie **Add-on** ausgewählt haben, verwenden Sie das Feld **Add-On auswählen**, um Ihre New Relic-Lizenz auszuwählen.

    ![Bild der Add-on-Felder](./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png)

    Wenn Sie **Benutzerdefiniert** ausgewählt haben, wählen Sie "New Relic" als **Provider** aus. Geben Sie dann Ihre Lizenz in das Feld **Provider Key** ein.

    ![Bild der benutzerdefinierten Felder](./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png)

3.  Klicken Sie nach dem Angeben der Lizenz in **Developer Analytics** auf **Speichern**. Durch das Speichern werden die folgenden Werte zum Abschnitt **App-Einstellungen** auf der Seite zur Unterstützung von New Relic hinzugefügt:

	<table data-morhtml="true" border="1">
	<thead data-morhtml="true">
	<tr data-morhtml="true">
	<td data-morhtml="true">Schl&uuml;ssel</td>
	<td data-morhtml="true">Wert</td>
	</tr>
	</thead>
	<tbody data-morhtml="true">
	<tr data-morhtml="true">
	<td data-morhtml="true">COR\_ENABLE\_PROFILING</td><td data-morhtml="true">1</td>
	</tr>
	<tr data-morhtml="true">
	<td data-morhtml="true">COR\_PROFILER</td><td data-morhtml="true">{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr data-morhtml="true">
	<td data-morhtml="true">COR\_PROFILER\_PATH</td><td data-morhtml="true">C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr data-morhtml="true">
	<td data-morhtml="true">NEWRELIC\_HOME</td><td data-morhtml="true">C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr data-morhtml="true">
	<td data-morhtml="true">NEWRELIC\_LICENSEKEY</td><td data-morhtml="true">Ihr Lizenzschl&uuml;ssel</td>
	</tr>
	</tbody>
	</table>


	**Hinweis**

	Es kann bis zu 30 Sekunden dauern, bis die neuen **App-Einstellungen** wirksam sind. Damit die Einstellungen sofort wirksam werden, starten Sie die Website neu.

4.  Veröffentlichen Sie Ihre Anwendung mit Visual Studio oder WebMatrix.

### Schritt 4. Überprüfen der Anwendungsleistung in New Relic.

So zeigen Sie das New Relic-Dashboard an:

1.  Klicken Sie im Azure-Portal auf die Schaltfläche **Verwalten**.
2.  Melden Sie sich mit der E-Mail-Adresse und dem Kennwort Ihres New Relic-Kontos an.
3.  Wählen Sie in der New Relic-Menüleiste **Applications \> (Anwendungsname)** aus.

    Das Dashboard **Monitoring \> Overview** wird automatisch angezeigt.

    ![New Relic-Überwachungsdashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

    Nachdem Sie in der Liste des Menüs **Applications** eine App ausgewählt haben, werden im Dashboard **Overview** aktuelle App-Server- und Browserinformationen angezeigt.

### Verwenden von New Relic

Nachdem Sie Ihre App in der Liste des Menüs "Applications" ausgewählt haben, werden im Dashboard "Overview" aktuelle App-Server- und Browserinformationen angezeigt. Zum Umschalten zwischen den zwei Ansichten klicken Sie auf die Schaltfläche **App server** oder **Browser**.

Neben der [standardmäßigen New Relic-Benutzeroberfläche](https://newrelic.com/docs/site/the-new-relic-ui#functions) und der [Dashboard-Drilldown](https://newrelic.com/docs/site/the-new-relic-ui#drilldown)-Funktionen bietet das Dashboard "Overview" weitere Funktionen.

<table data-morhtml="true" border="1">
  <thead data-morhtml="true">
    <tr data-morhtml="true">
      <th data-morhtml="true"><b data-morhtml="true">Gew&uuml;nschte Aktion</b></th>
      <th data-morhtml="true"><b data-morhtml="true">Ma&szlig;nahme</b></th>
    </tr>
  </thead>
  <tbody data-morhtml="true">
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen von Dashboardinformationen f&uuml;r den ausgew&auml;hlten App-Server oder Browser</td>
       <td data-morhtml="true">Klicken Sie auf die Schaltfl&auml;che <b data-morhtml="true">App server</b> oder <b data-morhtml="true">Browser</b>.</td>
    </tr>
     <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen von Schwellenwerten f&uuml;r das <a data-morhtml="true" href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a>-Ergebnis Ihrer App</td>
       <td data-morhtml="true">Zeigen Sie auf das <b data-morhtml="true">?<b data-morhtml="true">-Symbol</b></b> des Apdex-Ergebnisses.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen weltweiter Apdex-Details</td>
       <td data-morhtml="true">Zeigen Sie in der <b data-morhtml="true">Browser</b>-Ansicht der &Uuml;bersicht auf eine beliebige Stelle der globalen Apdex-Karte.<br data-morhtml="true" /><b data-morhtml="true">Tipp:</b> Wenn Sie direkt zum Dashboard <a data-morhtml="true" href="https://newrelic.com/docs/site/geography" target="_blank">Geography</a> der ausgew&auml;hlte App wechseln m&ouml;chten, klicken Sie auf den Titel <b data-morhtml="true">Global Apdex</b>, oder klicken Sie auf eine beliebige Stelle der globalen Apdex-Karte.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen des Dashboards <a data-morhtml="true" href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions</a></td>
       <td data-morhtml="true">Klicken Sie im Dashboard &quot;Overview&quot; auf die Tabelle &quot;Web Transactions&quot;. Wenn Sie Details zu einer bestimmten Webtransaktion (einschlie&szlig;lich <a data-morhtml="true" href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a> anzeigen m&ouml;chten), klicken Sie auf den entsprechenden Namen.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen des Dashboards <a data-morhtml="true" href="https://newrelic.com/docs/site/errors" target="_blank">Errors</a></td>
       <td data-morhtml="true">Klicken Sie im Dashboard &quot;Overview&quot; auf den Titel des Fehlerratendiagramms.<br data-morhtml="true" /><b data-morhtml="true">Tipp:</b> Sie k&ouml;nne das Dashboard &quot;Errors&quot; auch &uuml;ber <b data-morhtml="true">Applications</b> &gt; (Ihre App) &gt; Events &gt; Errors anzeigen.</td>
    </tr>
    <tr data-morhtml="true">
       <td data-morhtml="true">Anzeigen von Details zum App-Server</td>
       <td data-morhtml="true"><p data-morhtml="true">F&uuml;hren Sie einen der folgenden Schritte aus:<p data-morhtml="true">
        <ul data-morhtml="true">
          <li data-morhtml="true">Schalten Sie zwischen einer Tabellenansicht der Hosts und ausf&uuml;hrlichen Metrikdetails zu jedem Host um.</li>
          <li data-morhtml="true">Klicken Sie auf den Namen eines einzelnen Servers.</li>
          <li data-morhtml="true">Zeigen Sie auf das Apdex-Ergebnis eines einzelnen Servers.</li>
          <li data-morhtml="true">Klicken Sie auf die CPU-Auslastung oder den Arbeitsspeicher eines einzelnen Servers.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>


Nachfolgend sehen Sie ein Beispiel des Dashboards "Overview" bei Auswahl der Browseransicht.

![Paket-Manager-Konsole](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

Nächste Schritte
----------------

In den folgenden Ressourcen finden Sie weitere Informationen:

-   [Installing the .NET Agent for Azure Web Sites](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): Installationsverfahren für den New Relic .NET Agent (in englischer Sprache)
-   [The New Relic User Interface](https://newrelic.com/docs/site/the-new-relic-ui): Überblick über die New Relic-Benutzeroberfläche, Definieren von Benutzerrechten und -profilen, Verwenden von Standardfunktionen und Details zum Drilldown in Dashboards (in englischer Sprache)
-   [Applications Overview](https://newrelic.com/docs/site/applications-overview): Merkmale und Funktionen des New Relic-Dashboards "Overview" (in englischer Sprache)
-   [Apdex](https://newrelic.com/docs/site/apdex): Erläuterung, wie Apdex die Endbenutzerzufriedenheit mit Ihrer Anwendung misst (in englischer Sprache)
-   [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Überblick darüber, wie mit RUM (Real User Monitoring) die Zeit gemessen wird, die das Laden Ihrer Webseiten in den Browsern von Benutzern dauert, woher diese Benutzer stammen und welche Browser sie verwenden (in englischer Sprache)
-   [Finding Help](https://newrelic.com/docs/site/finding-help): Über das Online-Hilfecenter von New Relic aufrufbare Ressourcen (in englischer Sprache)


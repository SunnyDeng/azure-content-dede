<properties urlDisplayName="New Relic" pageTitle="Verwenden von New Relic mit Azure - Azure-Featureleitfäden" metaKeywords="" description="Erfahren Sie, wie Sie den New Relic-Dienst zum Verwalten und Überwachen Ihrer Azure-Anwendung verwenden." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="carolz" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/25/2014" ms.author="stepsic" />



#Verwaltung der Anwendungsleistung mit New Relic unter Azure

In diesem Leitfaden wird beschrieben, wie Sie Ihren in Azure gehosteten Anwendungen die erstklassige Leistungsüberwachung von New Relic hinzufügen. Wir erläutern das schnelle und einfache Verfahren zum Hinzufügen von New Relic zu Anwendungen und stellen einige der Funktionen von New Relic vor. Weitere Informationen zum Arbeiten mit New Relic finden Sie unter [Verwenden von New Relic](#using-new-relic).

Was ist New Relic?
--

New Relic ist ein für Entwickler konzipiertes Tool, mit dem Produktionsanwendungen überwacht und detaillierte Informationen zu deren Leistung und Zuverlässigkeit abgerufen werden können. Es beschleunigt die Identifizierung und Diagnostizierung von Leistungsproblemen und gibt Ihnen die benötigten Informationen an die Hand, um diese Probleme zu lösen.

New Relic verfolgt die Ladezeit und den Durchsatz von Webtransaktionen, sowohl vom Server als auch von den Browsern der Benutzer. Das Tool zeigt, wie viel Zeit Sie in der Datenbank verbracht haben, analysiert langsame Abfragen und Webanforderungen, bietet Verfügbarkeitsüberwachung und Warnfunktionen, verfolgt Anwendungsausnahmen und vieles mehr.

Sonderpreis für New Relic im Azure Store
--

New Relic Standard steht Azure-Benutzern kostenlos zur Verfügung.
New Relic Pro wird basierend auf der Instanzgröße für Azure Cloud Services angeboten.

Preisinformationen finden Sie auf der [New Relic-Seite im Azure Store](http://www.windowsazure.com/de-de/gallery/store/new-relic/new-relic/).

<div class="dev-callout"> 
<strong>Hinweis:</strong>
<p>Es werden nur Preise für bis zu zehn Serverinstanzen aufgeführt. Volumenpreise für mehr als zehn Instanzen erfragen Sie bitte bei New Relic (sales@newrelic.com).</p>
</div>

Azure-Kunden erhalten bei Bereitstellung des New Relic-Agenten ein zweiwöchiges Testabonnement von New Relic Pro.

Anmelden bei New Relic über den Azure Store
--

New Relic kann nahtlos in Azure-Web- und -Workerrollen integriert werden.

Wenn Sie sich direkt aus dem Azure Store bei New Relic registrieren möchten, führen Sie die folgenden drei einfachen Schritte aus.

### Schritt 1: Registrieren über den Azure Store

1. Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie im unteren Bereich des Verwaltungsportal auf **Neu**.
3. Klicken Sie auf **Store**.
4. Wählen Sie im Dialogfeld **Add-On auswählen** die Option **New Relic** aus, und klicken Sie auf **Weiter**.
5. Wählen Sie im Dialogfeld **Add-On personalisieren** den gewünschten New Relic-Plan aus.
6. Geben Sie ggf. einen Promotioncode ein.
7. Geben Sie den Namen ein, mit dem der New Relic-Dienst in Ihren Azure-Einstellungen angezeigt werden soll, oder wählen Sie den Standardwert **NewRelic** aus. Dieser Name muss in der Liste der abonnierten Azure Store-Elemente eindeutig sein.
8. Wählen Sie einen Wert für die Region aus, z. B. **West US**.
9. Klicken Sie auf **Weiter**.
10. Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen im Dialogfeld **Kauf überprüfen**. Wenn Sie den Bedingungen zustimmen, klicken Sie auf **Kaufen**.
11. Nachdem Sie auf **Kaufen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status im Azure-Verwaltungsportal überwachen.
12. Zum Abrufen des New Relic-Lizenzschlüssels klicken Sie auf **Output Values**. 
13. Kopieren Sie den angezeigten Lizenzschlüssel. Diesen müssen Sie bei der Installation des New Relic Nuget-Pakets eingeben.

### Schritt 2: Installieren des NuGet-Pakets

1. Öffnen Sie Ihre Visual Studio-Projektmappe, oder erstellen Sie eine neue Projektmappe, indem Sie
   **Datei > Neu > Projekt** auswählen.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Wenn Ihre Projektmappe noch kein Azure-Clouddienstprojekt enthält, fügen Sie eins hinzu, indem Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre App klicken und **Add Azure Cloud Service Project** wählen.

	![Create cloud service](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Wählen Sie **Tools > Library Package Manager > Package Manager Console**, um die Paket-Manager-Konsole zu öffnen. Wählen Sie Ihr Projekt oben im Fenster der Paket-Manager-Konsole als Standardprojekt aus.

	![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. Geben Sie an der Paket-Manager-Eingabeaufforderung den Befehl "Install-Package
   NewRelicWindowsAzure" ein, und drücken Sie dann die EINGABETASTE.

	![install in package manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. Geben Sie an der Eingabeaufforderung für den Lizenzschlüssel den vom Azure Store erhaltenen Lizenzschlüssel ein.

	![enter license key](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Optional: Geben Sie an der Eingabeaufforderung für den Anwendungsnamen den Namen Ihrer App so ein, wie er im New Relic-Dashboard angezeigt wird. Alternativ können Sie den Projektmappennamen als Standard verwenden.

	![enter application name](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Azure-Clouddienstprojekt, und wählen Sie **Veröffentlichen**.

	![public the cloud project](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Hinweis:** Wenn Sie diese App zum ersten Mal in Azure bereitstellen, werden Sie zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Weitere Informationen finden Sie unter <a href="/de-de/develop/net/tutorials/get-started/">Bereitstellen einer ASP.NET-Webanwendung auf einer Azure-Website</a>.

![publish settings](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Schritt 3: Überprüfen der Anwendungsleistung in New Relic.

So zeigen Sie das New Relic-Dashboard an:

1. Klicken Sie im Azure-Portal auf die Schaltfläche **Verwalten**.
2. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort Ihres New Relic-Kontos an.
3. Wählen Sie in der New Relic-Menüleiste **Applications > (Anwendungsname)** aus.

	Das Dashboard **Monitoring > Overview** wird automatisch angezeigt.

	![New Relic monitoring dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	Nachdem Sie in der Liste des Menüs **Applications** eine App ausgewählt haben, werden im Dashboard **Overview** aktuelle App-Server- und Browserinformationen angezeigt.

### <a id="using-new-relic"></a>Verwenden von New Relic

Nachdem Sie Ihre App in der Liste des Menüs "Applications" ausgewählt haben, werden im Dashboard "Overview" aktuelle App-Server- und Browserinformationen angezeigt. Zum Umschalten zwischen den zwei Ansichten klicken Sie auf die Schaltfläche **App server** oder **Browser**.

Zusätzlich zur <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">Standardbenutzeroberfläche von New Relic</a> und <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">den Drilldownfunktionen des Dashboards</a> weist das Dashboard "Applications Overview" zusätzliche Funktionen auf.

<table border="1">
  <thead>
    <tr>
      <th><b>Gewünschte Aktion</b></th>
      <th><b>Maßnahme</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Anzeigen von Dashboardinformationen für den ausgewählten Server oder Browser der App</td>
       <td>Klicken Sie auf die Schaltfläche <b>"App-Server"</b> oder <b>"Browser"</b> .</td>
    </tr>
     <tr>
       <td>Anzeigen von Schwellenwertstufen für das <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex-</a> Ergebnis Ihrer App</td>
       <td>Zeigen Sie auf das Symbol <b>?<b> des Apdex-Ergebnisses.</b></b></td>
    </tr>
    <tr>
       <td>Anzeigen weltweiter Apdex-Details</td>
       <td>Zeigen Sie in der Ansicht <b>Browser</b> auf eine beliebige Stelle der globalen Apdex-Karte.<br /><b>Tipp:</b> Navigieren Sie direkt zum Dashboard <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">Geography</a>der ausgewählten App, klicken Sie auf den Titel <b>Global Apdex</b> oder auf eine beliebige Stelle der globalen Apdex-Karte.</td>
    </tr>
    <tr>
       <td>Anzeigen des <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web Transactions-</a> Dashboards</td>
       <td>Klicken Sie im Dashboard "Overview" auf die Tabelle "Web Transactions". Zum Anzeigen von Details zu einer bestimmten Webtransaktion (einschließlich <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Key Transactions</a>), klicken Sie auf deren Namen.</td>
    </tr>
    <tr>
       <td>Anzeigen des <a href="https://newrelic.com/docs/site/errors" target="_blank">Errors-</a> Dashboards</td>
       <td>Klicken Sie im Dashboard "Applications Overview" auf den Titel des Fehlerratendiagramms.<br /><b>Tipp:</b> Sie können das Dashboard "Errors" auch über <b>"Applications</b> &gt; (Ihre App) &gt; Events &gt; Errors" anzeigen.</td>
    </tr>
    <tr>
       <td>Anzeigen der Serverdetails der App</td>
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

![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Nächste Schritte

In den folgenden Ressourcen finden Sie weitere Informationen:

 * [Installieren des .NET-Agents unter Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): Installationsverfahren für den .NET-Agent von New Relic. 
 * [Die Benutzeroberfläche von New Relic](https://newrelic.com/docs/site/the-new-relic-ui): 
Überblick über die Benutzeroberfläche von New Relic, Definieren von Benutzerrechten und -profilen, Verwenden von Standardfunktionen und Details zum Drilldown in Dashboards.
 * [Anwendungsübersicht](https://newrelic.com/docs/site/applications-overview): Merkmale und Funktionen des New Relic-Dashboards "Overview".
 * [Apdex](https://newrelic.com/docs/site/apdex): Erläuterung, wie Apdex die Endbenutzerzufriedenheit mit Ihrer Anwendung misst.
 * [RUM (Real User Monitoring)](https://newrelic.com/docs/features/real-user-monitoring): Überblick darüber, wie mit RUM (Real User Monitoring) die Zeit gemessen wird, die das Laden Ihrer Webseiten in den Browsern von Benutzern dauert, woher diese Benutzer stammen und welche Browser sie verwenden.
 * [Finding Help](https://newrelic.com/docs/site/finding-help): Ressourcen, die über das Onlinehilfecenter von New Relic verfügbar sind.

<!--HONumber=35.2-->

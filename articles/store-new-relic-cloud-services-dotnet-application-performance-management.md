<properties linkid="develop-net-how-to-guides-new-relic" urlDisplayName="New Relic" pageTitle="Using New Relic with Azure - Azure feature guides" metaKeywords="" description="Learn how to use the New Relic service to manage and monitor your Azure application." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new relic"></tags>

# Verwaltung der Anwendungsleistung mit New Relic auf Azure

In diesem Leitfaden wird beschrieben, wie Sie Ihren in Azure gehosteten
Anwendungen die erstklassige Leistungsüberwachung von New Relic hinzufügen. Wir erläutern das schnelle und einfache
Verfahren zum Hinzufügen von New Relic zu Anwendungen und stellen
einige der Funktionen von New Relic vor. Weitere Informationen zum Arbeiten mit New Relic finden Sie unter [Verwenden von New Relic][Verwenden von New Relic].

## Was ist New Relic?

New Relic ist ein für Entwickler konzipiertes Tool, mit dem Produktionsanwendungen
überwacht und detaillierte Informationen zu deren Leistung und Zuverlässigkeit abgerufen werden können. Es
beschleunigt die Identifizierung und Diagnostizierung von Leistungsproblemen und gibt
Ihnen die benötigten Informationen an die Hand, um diese Probleme zu lösen.

New Relic verfolgt die Ladezeit und den Durchsatz von Webtransaktionen sowohl vom
Server als auch von den Browsern der Benutzer. Das Tool zeigt, wie viel Zeit Sie in der
Datenbank verbracht haben, analysiert langsame Abfragen und Webanforderungen,
bietet Verfügbarkeitsüberwachung und Warnfunktionen, verfolgt Anwendungsausnahmen und vieles mehr.

## Sonderpreis für New Relic im Azure Store

New Relic Standard ist für Azure-Benutzer kostenlos.
New Relic Pro wird basierend auf der Instanzgröße für Azure Cloud Services angeboten

Preisinformationen finden Sie auf der [New Relic-Seite im Azure Store][New Relic-Seite im Azure Store].

<div class="dev-callout"> 
<strong>Hinweis:</strong>
<p>Es werden nur Preise f&uuml;r bis zu zehn Serverinstanzen aufgef&uuml;hrt. Volumenpreise f&uuml;r mehr als zehn Instanzen erfragen Sie bitte bei New Relic (sales@newrelic.com).</p>
</div>

Azure-Kunden erhalten bei Bereitstellung des New Relic-Agenten ein zweiwöchiges Testabonnement von New Relic Pro.

## Anmelden bei New Relic über den Azure Store

New Relic kann nahtlos in Azure-Web- und -Workerrollen integriert werden.

Um sich direkt im Azure Store bei New Relic anmelden, führen Sie diese drei einfachen Schritte aus.

### Schritt 1. Anmelden über den Azure Store

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie im unteren Bereich des Verwaltungsportal auf **Neu**.
3.  Klicken Sie auf **Speichern**.
4.  Wählen Sie im Dialogfeld **Add-On auswählen** die Option **New Relic** aus, und klicken Sie auf **Weiter**.
5.  Wählen Sie im Dialogfeld **Add-On personalisieren** den gewünschten New Relic-Plan aus.
6.  Geben Sie ggf. einen Promotioncode ein.
7.  Geben Sie den Namen ein, mit dem der New Relic-Dienst in Ihren
    Azure-Einstellungen angezeigt werden soll, oder verwenden Sie den Standardwert **NewRelic**. Dieser Name muss in der
    Liste der abonnierten Azure Store-Elemente eindeutig sein.
8.  Wählen Sie einen Wert für die Region aus, z. B. **West US**.
9.  Klicken Sie auf **Weiter**.
10. Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen im
    Dialogfeld **Kauf überprüfen**. Wenn Sie den Bedingungen zustimmen, klicken Sie auf **Kaufen**.
11. Nachdem Sie auf **Kaufen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status im Azure-Verwaltungsportal überwachen.
12. Zum Abrufen des New Relic-Lizenzschlüssels klicken Sie auf **Output Values**.
13. Kopieren Sie den angezeigten Lizenzschlüssel. Diesen müssen Sie bei der Installation des New Relic Nuget-Pakets eingeben.

### Schritt 2. Installieren des Nuget-Pakets

1.  Öffnen Sie Ihre Visual Studio-Projektmappe, oder wählen Sie
    **Datei \> Neu \> Projekt**, um eine neue zu erstellen.

    ![Visual Studio][Visual Studio]

2.  Wenn Ihre Projektmappe noch kein Azure-Cloud-Dienstprojekt enthält,
    fügen Sie eines hinzu, indem Sie im Projektmappen-Explorer
    mit der rechten Maustaste auf Ihre App klicken und **Azure-Cloud-Dienstprojekt hinzufügen** auswählen.

    ![Clouddienst erstellen][Clouddienst erstellen]

3.  Wählen Sie **Extras \> Bibliothekspaket-Manager \> Paket-Manager-Konsole**, um
    die Paket-Manager-Konsole zu öffnen. Legen Sie Ihr Projekt oben im Fenster der
    Paket-Manager-Konsole als Standardprojekt fest.

    ![Paket-Manager-Konsole][Paket-Manager-Konsole]

4.  Geben Sie an der Eingabeaufforderung des Paket-Managers `Install-Package    NewRelicWindowsAzure` ein, und drücken Sie die **EINGABETASTE**.

    ![Installieren im Paket-Manager][Installieren im Paket-Manager]

5.  Geben Sie an der Eingabeaufforderung für den Lizenzschlüssel den vom Azure Store erhaltenen Lizenzschlüssel ein.

    ![Lizenzschlüssel eingeben][Lizenzschlüssel eingeben]

6.  Optional: Geben Sie an der Eingabeaufforderung für den Anwendungsnamen
    den Namen Ihrer App so ein, wie er im New Relic-Dashboard angezeigt wird. Alternativ können Sie den Projektmappennamen als Standard verwenden.

    ![Anwendungsnamen eingeben][Anwendungsnamen eingeben]

7.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Azure-Clouddienstprojekt, und wählen Sie **Veröffentlichen**.

    ![Cloudprojekt veröffentlichen][Cloudprojekt veröffentlichen]

**Hinweis:** Wenn Sie diese App zum ersten Mal in Azure bereitstellen, werden Sie zur Eingabe
Ihrer Azure-Anmeldeinformationen aufgefordert. Weitere Informationen finden Sie unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website][Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website].

![Veröffentlichungseinstellungen][Veröffentlichungseinstellungen]

### Schritt 3. Überprüfen der Anwendungsleistung in New Relic.

So zeigen Sie das New Relic-Dashboard an:

1.  Klicken Sie im Azure-Portal auf die Schaltfläche **Verwalten**.
2.  Melden Sie sich mit der E-Mail-Adresse und dem Kennwort Ihres New Relic-Kontos an.
3.  Wählen Sie in der New Relic-Menüleiste **Applications \> (Anwendungsname)** aus.

    Das Dashboard **Monitoring \> Overview** wird automatisch angezeigt.

    ![New Relic-Überwachungsdashboard][New Relic-Überwachungsdashboard]

    Nachdem Sie in der Liste des Menüs **Applications** eine App ausgewählt haben, werden im Dashboard **Overview** aktuelle App-Server- und Browserinformationen angezeigt.

### <span id="using-new-relic"></span></a>Verwenden von New Relic

Nachdem Sie Ihre App in der Liste des Menüs "Applications" ausgewählt haben, werden im Dashboard "Overview" aktuelle App-Server- und Browserinformationen angezeigt. Zum Umschalten zwischen den zwei Ansichten klicken Sie auf die Schaltfläche **App server** oder **Browser**.

Neben der [standardmäßigen New Relic-Benutzeroberfläche][standardmäßigen New Relic-Benutzeroberfläche] und der [Dashboard-Drilldown][Dashboard-Drilldown]-Funktionen bietet das Dashboard "Overview" weitere Funktionen.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Gewünschte Aktion</strong></th>
<th align="left"><strong>Maßnahme</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Anzeigen von Dashboardinformationen für den ausgewählten App-Server oder Browser</td>
<td align="left">Klicken Sie auf die Schaltfläche <strong>App server</strong> oder <strong>Browser</strong>.</td>
</tr>
<tr class="even">
<td align="left">Anzeigen von Schwellenwerten für das <a href="https://newrelic.com/docs/site/apdex">Apdex</a>-Ergebnis Ihrer App</td>
<td align="left">Zeigen Sie auf das <strong>?<strong>-Symbol</strong></strong> des Apdex-Ergebnisses.</td>
</tr>
<tr class="odd">
<td align="left">Anzeigen weltweiter Apdex-Details</td>
<td align="left">Zeigen Sie in der <strong>Browser</strong>-Ansicht der Übersicht auf eine beliebige Stelle der globalen Apdex-Karte.<br /><strong>Tipp:</strong> Wenn Sie direkt zum Dashboard <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard">Geography</a> der ausgewählte App wechseln möchten, klicken Sie auf den Titel <strong>Global Apdex</strong>, oder klicken Sie auf eine beliebige Stelle der globalen Apdex-Karte.</td>
</tr>
<tr class="even">
<td align="left">Anzeigen des Dashboards <a href="https://newrelic.com/docs/applications-dashboards/web-transactions">Web Transactions</a></td>
<td align="left">Klicken Sie im Dashboard &quot;Overview&quot; auf die Tabelle &quot;Web Transactions&quot;. Wenn Sie Details zu einer bestimmten Webtransaktion (einschließlich <a href="https://newrelic.com/docs/site/key-transactions">Key Transactions</a> anzeigen möchten), klicken Sie auf den entsprechenden Namen.</td>
</tr>
<tr class="odd">
<td align="left">Anzeigen des Dashboards <a href="https://newrelic.com/docs/site/errors">Errors</a></td>
<td align="left">Klicken Sie im Dashboard &quot;Overview&quot; auf den Titel des Fehlerratendiagramms.<br /><strong>Tipp:</strong> Sie könne das Dashboard &quot;Errors&quot; auch über <strong>Applications</strong> &gt; (Ihre App) &gt; Events &gt; Errors anzeigen.</td>
</tr>
<tr class="even">
<td align="left">Anzeigen von Details zum App-Server</td>
<td align="left"><p>Führen Sie einen der folgenden Schritte aus:</p>
<p></p>
<ul>
<li>Schalten Sie zwischen einer Tabellenansicht der Hosts und ausführlichen Metrikdetails zu jedem Host um.</li>
<li>Klicken Sie auf den Namen eines einzelnen Servers.</li>
<li>Zeigen Sie auf das Apdex-Ergebnis eines einzelnen Servers.</li>
<li>Klicken Sie auf die CPU-Auslastung oder den Arbeitsspeicher eines einzelnen Servers.</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

Nachfolgend sehen Sie ein Beispiel des Dashboards "Overview" bei Auswahl der Browseransicht.

![Paket-Manager-Konsole][1]

## Nächste Schritte

In den folgenden Ressourcen finden Sie weitere Informationen:

-   [Installing the .NET Agent on Azure][Installing the .NET Agent on Azure]: Installationsverfahren für den New Relic .NET Agent (in englischer Sprache)
-   [New Relic-Benutzeroberfläche][New Relic-Benutzeroberfläche]:
    Überblick über die New Relic-Benutzeroberfläche, Festlegen von Benutzerrechten und -profilen, Verwenden von Standardfunktionen und Details zu Drilldowns in Dashboards
-   [Applications Overview][Applications Overview]: Merkmale und Funktionen des New Relic-Dashboards "Overview" (in englischer Sprache)
-   [Apdex][Apdex]: Erläuterung, wie Apdex die Endbenutzerzufriedenheit mit Ihrer Anwendung misst (in englischer Sprache)
-   [Real User Monitoring][Real User Monitoring]: Überblick darüber, wie mit RUM (Real User Monitoring) die Zeit
    gemessen wird, die das Laden Ihrer Webseiten in den Browsern von Benutzern dauert, woher diese Benutzer stammen und welche Browser sie verwenden
-   [Finding Help][Finding Help]: Über das Online-Hilfecenter von New Relic aufrufbare Ressourcen (in englischer Sprache)

  [Verwenden von New Relic]: #using-new-relic
  [New Relic-Seite im Azure Store]: http://www.windowsazure.com/de-de/gallery/store/new-relic/new-relic/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Visual Studio]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png
  [Clouddienst erstellen]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png
  [Paket-Manager-Konsole]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [Installieren im Paket-Manager]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png
  [Lizenzschlüssel eingeben]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png
  [Anwendungsnamen eingeben]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png
  [Cloudprojekt veröffentlichen]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png
  [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website]: /de-de/develop/net/tutorials/get-started/
  [Veröffentlichungseinstellungen]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png
  [New Relic-Überwachungsdashboard]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png
  [standardmäßigen New Relic-Benutzeroberfläche]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [Dashboard-Drilldown]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [Geography]: https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard
  [Web Transactions]: https://newrelic.com/docs/applications-dashboards/web-transactions
  [Key Transactions]: https://newrelic.com/docs/site/key-transactions
  [Errors]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png
  [Installing the .NET Agent on Azure]: https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure
  [New Relic-Benutzeroberfläche]: https://newrelic.com/docs/site/the-new-relic-ui
  [Applications Overview]: https://newrelic.com/docs/site/applications-overview
  [Real User Monitoring]: https://newrelic.com/docs/features/real-user-monitoring
  [Finding Help]: https://newrelic.com/docs/site/finding-help

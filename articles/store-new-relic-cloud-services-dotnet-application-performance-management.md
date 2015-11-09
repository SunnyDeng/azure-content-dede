<properties 
	pageTitle="Verwenden von New Relic mit Azure | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den New Relic-Dienst zum Verwalten und Überwachen Ihrer Azure-Anwendung verwenden." 
	services="" 
	documentationCenter=".net" 
	authors="nickfloyd" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="nickfloyd@newrelic.com"/>



# Verwaltung der Anwendungsleistung mit New Relic auf Azure

In diesem Leitfaden wird beschrieben, wie Sie Ihren auf Azure gehosteten Anwendungen die erstklassige Leistungsüberwachung von New Relic hinzufügen. Wir erläutern das schnelle und einfache Verfahren zum Hinzufügen von New Relic zu Anwendungen und stellen einige der Funktionen von New Relic vor. Weitere Informationen zum Arbeiten mit New Relic finden Sie unter [Verwenden von New Relic](#using-new-relic).

## Was ist New Relic?

New Relic ist ein für Entwickler konzipiertes Tool, mit dem Produktionsanwendungen überwacht und detaillierte Informationen zu deren Leistung und Zuverlässigkeit abgerufen werden können. Es beschleunigt die Identifizierung und Diagnostizierung von Leistungsproblemen und gibt Ihnen die benötigten Informationen an die Hand, um diese Probleme zu lösen.

New Relic verfolgt die Ladezeit und den Durchsatz von Webtransaktionen, sowohl vom Server als auch von den Browsern der Benutzer. Das Tool zeigt, wie viel Zeit Sie in der Datenbank verbracht haben, analysiert langsame Abfragen und Webanforderungen, bietet Verfügbarkeitsüberwachung und Warnfunktionen, verfolgt Anwendungsausnahmen und vieles mehr.

## Sonderpreis für New Relic im Azure Store


New Relic Standard ist für Azure-Benutzer kostenlos; New Relic Pro wird basierend auf der Instanzgröße für Azure-Clouddienste angeboten

Preisinformationen finden Sie auf der [New Relic-Seite im Azure Store](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE]Es werden nur Preise für bis zu zehn Serverinstanzen aufgeführt. Volumenpreise für mehr als zehn Instanzen erfragen Sie bitte bei New Relic (sales@newrelic.com).

Azure-Kunden erhalten bei Bereitstellung des New Relic-Agenten ein zweiwöchiges Testabonnement von New Relic Pro.

## Anmelden bei New Relic über den Azure Store

New Relic kann nahtlos in Azure-Web- und -Workerrollen integriert werden.

Um sich direkt im Azure Store bei New Relic anmelden, führen Sie diese drei einfachen Schritte aus.

### Schritt 1: Registrieren über den Azure Store

1. Melden Sie sich im [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie im unteren Bereich des Verwaltungsportals auf **Neu**.
3. Klicken Sie auf **Speichern**.
4. Wählen Sie im Dialogfeld **Add-On auswählen** die Option **New Relic** aus, und klicken Sie auf **Weiter**.
5. Wählen Sie im Dialogfeld **Add-On personalisieren** den gewünschten New Relic-Plan aus.
6. Geben Sie ggf. einen Promotioncode ein.
7. Geben Sie den Namen ein, mit dem der New Relic-Dienst in Ihren Azure-Einstellungen angezeigt werden soll, oder wählen Sie den Standardwert **NewRelic** aus. Dieser Name muss in der Liste der abonnierten Azure Store-Elemente eindeutig sein.
8. Wählen Sie einen Wert für die Region aus, z. B. **West US**.
9. Klicken Sie auf **Weiter**.
10. Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen im Dialogfeld **Kauf überprüfen**. Wenn Sie den Bedingungen zustimmen, klicken Sie auf **Kaufen**.
11. Nachdem Sie auf **Kaufen** geklickt haben, wird mit der Erstellung Ihres neuen New Relic-Kontos begonnen. Sie können den Status im Azure-Verwaltungsportal überwachen.
12. Zum Abrufen des New Relic-Lizenzschlüssels klicken Sie auf **Output Values**. 
13. Kopieren Sie den angezeigten Lizenzschlüssel. Diesen müssen Sie bei der Installation des New Relic Nuget-Pakets eingeben.

### Schritt 2. Installieren des NuGet-Pakets

1. Öffnen Sie Ihre Visual Studio-Projektmappe, oder wählen Sie **Datei > Neu > Projekt**, um eine neue zu erstellen.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Wenn Ihre Projektmappe noch kein Azure-Clouddienstprojekt enthält, fügen Sie eins hinzu, indem Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre App klicken und **Add Azure Cloud Service Project** wählen.

	![Clouddienst erstellen](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Wählen Sie **Tools > Library Package Manager > Package Manager Console**, um die Paket-Manager-Konsole zu öffnen. Wählen Sie Ihr Projekt oben im Fenster der Paket-Manager-Konsole als Standardprojekt aus.

	![Paket-Manager-Konsole](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. Geben Sie an der Eingabeaufforderung des Paket-Managers `Install-Package
   NewRelicWindowsAzure` ein, und drücken Sie die **EINGABETASTE**.

	![Installieren im Paket-Manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. Geben Sie an der Eingabeaufforderung für den Lizenzschlüssel den vom Azure Store erhaltenen Lizenzschlüssel ein.

	![Lizenzschlüssel eingeben](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Optional: Geben Sie an der Eingabeaufforderung für den Anwendungsnamen den Namen Ihrer App so ein, wie er im New Relic-Dashboard angezeigt wird. Alternativ können Sie den Projektmappennamen als Standard verwenden.

	![Anwendungsnamen eingeben](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Azure-Clouddienstprojekt, und wählen Sie **Veröffentlichen**.

	![Cloudprojekt veröffentlichen](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Hinweis:** Wenn Sie diese App zum ersten Mal in Azure bereitstellen, werden Sie zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Weitere Informationen finden Sie unter [Bereitstellen einer ASP.NET-Webanwendung für eine Azure-Website](app-service-web\web-sites-dotnet-get-started.md).

![Veröffentlichungseinstellungen](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Schritt 3. Überprüfen der Anwendungsleistung in New Relic.

So zeigen Sie das New Relic-Dashboard an:

1. Klicken Sie im Azure-Portal auf die Schaltfläche **Verwalten**.
2. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort Ihres New Relic-Kontos an.
3. Wählen Sie in der New Relic-Menüleiste **Applications > (Anwendungsname)** aus.

	Das Dashboard **Monitoring > Overview** wird automatisch angezeigt.

	![New Relic-Überwachungsdashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	Nachdem Sie in der Liste des Menüs **Applications** eine App ausgewählt haben, werden im Dashboard **Overview** aktuelle App-Server- und Browserinformationen angezeigt.

### <a id="using-new-relic"></a>Verwenden von New Relic

Nachdem Sie Ihre App in der Liste des Menüs "Applications" ausgewählt haben, werden im Dashboard "Overview" aktuelle App-Server- und Browserinformationen angezeigt. Zum Umschalten zwischen den zwei Ansichten klicken Sie auf die Schaltfläche **App server** oder **Browser**.

Neben der [standardmäßigen New Relic-Benutzeroberfläche](https://newrelic.com/docs/site/the-new-relic-ui#functions") und der [Dashboard-Drilldown](https://newrelic.com/docs/site/the-new-relic-ui#drilldown)-Funktionen bietet das Dashboard "Overview" weitere Funktionen.

| Gewünschte Aktion | Maßnahme |
| ----------------- | ---------- |
| Anzeigen von Dashboardinformationen für den ausgewählten App-Server oder Browser. | Klicken Sie auf die Schaltfläche **App server** oder **Browser**. |
| Anzeigen von Schwellenwerten für das [Apdex](https://newrelic.com/docs/site/apdex)-Ergebnis Ihrer App. | Zeigen Sie auf das **?**-Symbol des Apdex-Ergebnisses. |
| Anzeigen weltweiter Apdex-Details. | Zeigen Sie in der **Browser**-Ansicht der Übersicht auf eine beliebige Stelle der globalen Apdex-Karte. **Tipp:** Wenn Sie direkt zum Dashboard [Geography](https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard") der ausgewählten App wechseln möchten, klicken Sie auf den Titel **Global Apdex**, oder klicken Sie auf eine beliebige Stelle der globalen Apdex-Karte. |
| Anzeigen des Dashboards [Web Transactions](https://newrelic.com/docs/applications-dashboards/web-transactions) | Klicken Sie im Dashboard "Overview" auf die Tabelle "Web Transactions". Wenn Sie Details zu einer bestimmten Webtransaktion (einschließlich [Key Transactions](https://newrelic.com/docs/site/key-transactions") anzeigen möchten), klicken Sie auf den entsprechenden Namen. |
| Anzeigen des Dashboards [Errors](https://newrelic.com/docs/site/errors). | Klicken Sie im Dashboard "Overview" der Anwendung auf den Titel des Fehlerratendiagramms. **Tipp:** Sie können das Dashboard "Errors" auch über **Applications > (Ihre App) > Events > Errors** anzeigen. |


Zum Anzeigen der Serverdetails der App können Sie zusätzlich eine der folgenden Aktionen ausführen:

- Schalten Sie zwischen einer Tabellenansicht der Hosts und ausführlichen Metrikdetails zu jedem Host um.
- Klicken Sie auf den Namen eines einzelnen Servers.
- Zeigen Sie auf das Apdex-Ergebnis eines einzelnen Servers.
- Klicken Sie auf die CPU-Auslastung oder den Arbeitsspeicher eines einzelnen Servers.

Nachfolgend sehen Sie ein Beispiel des Dashboards "Overview" bei Auswahl der Browseransicht.

![Paket-Manager-Konsole](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Nächste Schritte

In den folgenden Ressourcen finden Sie weitere Informationen:

 * [Installieren des .NET-Agents für Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): Vorgehensweise für das Installieren des New Relic-.NET-Agents 
 * [New Relic-Benutzeroberfläche](https://newrelic.com/docs/site/the-new-relic-ui): Überblick über die New Relic-Benutzeroberfläche, Festlegen von Benutzerrechten und -profilen, Verwenden von Standardfunktionen und Details zu Drilldowns in Dashboards
 * [Anwendungsübersicht](https://newrelic.com/docs/site/applications-overview): Merkmale und Funktionen des New Relic-Dashboards "Overview"
 * [Apdex](https://newrelic.com/docs/site/apdex): Erläuterung, wie Apdex die Endbenutzerzufriedenheit mit Ihrer Anwendung misst
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Überblick darüber, wie mit RUM (Real User Monitoring) die Zeit gemessen wird, die das Laden Ihrer Webseiten in den Browsern von Benutzern dauert, woher diese Benutzer stammen und welche Browser sie verwenden
 * [Finding Help](https://newrelic.com/docs/site/finding-help): Über das Online-Hilfecenter von New Relic aufrufbare Ressourcen

<!---HONumber=Nov15_HO1-->
<properties
    pageTitle="Bereitstellungsanmerkungen für Application Insights | Microsoft Azure"
    description="Fügen Sie den Diagrammen im Metrik-Explorer in Application Insights Bereitstellungs- oder Buildmarker hinzu."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="01/05/2016"
    ms.author="awills"/>

# Versionsanmerkungen in Application Insights

Die Versionsanmerkungen in den Diagrammen des [Metrik-Explorers](app-insights-metrics-explorer.md) geben an, wo Sie einen neuen Build bereitgestellt haben. Dank dieser Anmerkungen sehen Sie auf einen Blick, ob Ihre Änderungen Auswirkungen auf die Leistung Ihrer Anwendung hatten. Sie können automatisch vom [Buildsystem von Visual Studio Team Services](https://www.visualstudio.com/de-DE/get-started/build/build-your-app-vs) erstellt werden.

![Beispiel für Anmerkungen mit sichtbarer Korrelation mit Serverantwortzeit](./media/app-insights-annotations/00.png)

Versionsanmerkungen sind ein Feature des cloudbasierten Build- und Versionsdiensts von Visual Studio Team Services.

## Installieren der Erweiterung für Anmerkungen (einmalig)

Um Versionsanmerkungen erstellen zu können, müssen Sie eine der zahlreichen Team Service-Erweiterungen installieren, die im Visual Studio Marketplace zur Verfügung stehen.

1. Melden Sie sich bei Ihrem [Visual Studio Team Services](https://www.visualstudio.com/de-DE/get-started/setup/sign-up-for-visual-studio-online)-Projekt an.
2. Öffnen Sie Visual Studio-Marketplace, suchen Sie nach der Erweiterung für Application Insights-Anmerkungen, und fügen Sie sie Ihrem Team Services-Konto hinzu.

![Öffnen Sie oben rechts auf der Team Services-Website den Marketplace. Suchen Sie die Application Insights-Anmerkungen, und installieren Sie sie unter Ihrem Konto.](./media/app-insights-annotations/10.png)

Diesen Vorgang müssen Sie nur einmal für Ihr Visual Studio Team Services-Konto ausführen. Versionsanmerkungen können nun für jedes Projekt in Ihrem Konto konfiguriert werden.



## Hinzufügen einer Anmerkungsaufgabe zur Versionsvorlage

Diesen Schritt müssen Sie für jede Versionsvorlage ausführen, für die Sie Versionsanmerkungen erstellen möchten.

Öffnen (oder erstellen) Sie über Visual Studio Team Services die Versionsvorlage, mit der Ihre Bereitstellungen verwaltet werden.

Fügen Sie eine Aufgabe hinzu, und wählen Sie im Menü die Aufgabe für Application Insights-Versionsanmerkungen aus.

![Öffnen Sie oben rechts auf der Team Services-Website den Marketplace. Suchen Sie die Application Insights-Anmerkungen, und installieren Sie sie unter Ihrem Konto.](./media/app-insights-annotations/40.png)

Um diesen Schritt abzuschließen, benötigen Sie einige Details der Application Insights-Ressource, mit der Sie Ihre Anwendung überwachen.

Lassen Sie das Team Services-Fenster geöffnet, während Sie die Details aus Application Insights abrufen.

## Kopieren eines API-Schlüssels aus Application Insights

In einem separaten Browserfenster:

1. Melden Sie sich im [Microsoft Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Application Insights-Ressource, mit der Sie Ihre Anwendung überwachen. (Oder [erstellen Sie nun eine](app-insights-overview.md), sofern noch nicht geschehen.)
2. Öffnen Sie das Dropdownmenü **Essentials**, und kopieren Sie die Abonnement-ID, die Ressourcengruppe und den Namen der Ressource in die Aufgabe für Versionsanmerkungen. ![](./media/app-insights-annotations/50.png)
2. Öffnen Sie **Einstellungen** > **API-Schlüssel**, und erstellen Sie einen neuen Schlüssel. Kopieren Sie diesen Schlüssel. ![](./media/app-insights-annotations/30.png)

**Speichern** Sie abschließend die Versionsdefinition.

## Bereitstellungsmarker

Wenn Sie nun diese Versionsvorlage zum Bereitstellen einer neuen Version verwenden, wird jedes Mal eine Anmerkung an Application Insights gesendet. Die Anmerkungen werden in Diagrammen im Metrik-Explorer angezeigt.

<!---HONumber=AcomDC_0107_2016-->
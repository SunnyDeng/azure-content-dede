<properties 
	pageTitle="Exemplarische Vorgehensweise: Überwachen von Microsoft Dynamics CRM mit Application Insights" 
	description="Abrufen von Telemetriedaten aus Microsoft Dynamics CRM Online mithilfe von Application Insights Exemplarische Vorgehensweise für das Einrichten, Abrufen von Daten, Visualisieren und Exportieren." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="awills"/>
 
# Exemplarische Vorgehensweise: Aktivieren von Telemetriedaten für Microsoft Dynamics CRM Online mithilfe von Application Insights

Dieser Artikel beschreibt, wie Sie Telemetriedaten aus [Microsoft Dynamics CRM Online](https://www.dynamics.com/) mit [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/) abrufen können. Der vollständige Vorgang des Hinzufügens eines Application Insights-Skripts zu Ihrer Anwendung, das Erfassen von Daten und die Visualisierung von Daten werden erläutert.

>[AZURE.NOTE] [Browse the sample solution](https://dynamicsandappinsights.codeplex.com/).

## Hinzufügen von Application Insights zu einer neuen oder vorhandenen CRM Online-Instanz 

Zum Überwachen Ihrer Anwendung fügen Sie Ihrer Anwendung ein Application Insights-SDK hinzu. Das SDK sendet Telemetriedaten an das [Application Insights-Portal](https://portal.azure.com), in dem Sie unsere leistungsstarken Analyse- und Diagnosetools verwenden oder die Daten in den Speicher exportieren können.

### Erstellen einer Application Insights-Ressource in Azure

1. Fordern Sie [ein Konto in Microsoft Azure](http://azure.com/pricing) an. 
2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und fügen Sie eine neue Application Insights-Ressource hinzu. Hier werden die Daten verarbeitet und angezeigt.

    ![Klicken Sie auf "+", "Entwicklerdienste", "Application Insights".](./media/app-insights-sample-mscrm/01.png)

    Wählen Sie als Anwendungstyp "ASP.NET" aus.

3. Öffnen Sie die Registerkarte "Schnellstart", und öffnen Sie das Codeskript.

    ![](./media/app-insights-sample-mscrm/03.png)

**Lassen Sie die Codeseite geöffnet**, während Sie den nächsten Schritt in einem anderen Browserfenster ausführen. Sie benötigen den Code bald.

### Erstellen einer JavaScript-Webressource in Microsoft Dynamics CRM

1. Öffnen Sie Ihre CRM Online-Instanz, und melden Sie sich mit Administratorrechten an.
2. Öffnen Sie „Microsoft Dynamics CRM Settings“, „Anpassungen“, „System anpassen“.

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Erstellen Sie eine JavaScript-Ressource.

    ![](./media/app-insights-sample-mscrm/07.png)

    Geben Sie ihr einen Namen, wählen Sie **Skript (JScript)**, und öffnen Sie den Text-Editor.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Kopieren Sie den Code aus Application Insights. Achten Sie beim Kopieren darauf, Skript-Tags zu ignorieren. Siehe Screenshot unten:

    ![](./media/app-insights-sample-mscrm/09.png)

    Der Code enthält den Instrumentationsschlüssel, der Ihre Application Insights-Ressource identifiziert.

5. Speichern und veröffentlichen Sie die Ressource.

    ![](./media/app-insights-sample-mscrm/10.png)

### Instrumentieren von Formularen

1. Öffnen Sie in Microsoft CRM Online das Formular "Firma".

    ![](./media/app-insights-sample-mscrm/11.png)

2. Öffnen Sie "Formulareigenschaften".

    ![](./media/app-insights-sample-mscrm/12.png)

3. Fügen Sie die JavaScript-Webressource hinzu, die Sie erstellt haben.

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Speichern und veröffentlichen Sie die Formularanpassungen.


## Erfasste Metriken

Sie haben jetzt Telemetriedatenerfassung für das Formular eingerichtet. Immer wenn es verwendet wird, werden Daten an die Application Insights-Ressource gesendet.

Im Folgenden finden Sie Beispiele für die Daten, die Sie sehen werden.

#### Anwendungsintegrität

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Browserausnahmen:

![](./media/app-insights-sample-mscrm/17.png)

Klicken Sie auf das Diagramm, um weitere Details zu erhalten:

![](./media/app-insights-sample-mscrm/18.png)

#### Verwendung

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### Browser

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### Geolocation

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### Details zu Anforderungen von Seitenansichten

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## Beispielcode

[Durchsuchen Sie den Beispielcode](https://dynamicsandappinsights.codeplex.com/).

## Power BI

Sie können sogar eine tiefer gehende Analyse durchführen, wenn Sie [die Daten nach Microsoft Power BI exportieren](app-insights-export-power-bi.md).

## Microsoft Dynamics CRM-Beispiellösung

[Hier ist die in Microsoft Dynamics CRM implementierte Beispiellösung](https://dynamicsandappinsights.codeplex.com/).

## Weitere Informationen

* [Was ist Application Insights?](app-insights-overview.md)
* [Application Insights für Webseiten](app-insights-javascript.md)
* [Weitere Beispiele und exemplarische Vorgehensweisen](app-insights-code-samples.md)

 

<!---HONumber=AcomDC_1125_2015-->
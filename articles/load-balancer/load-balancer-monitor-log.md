<properties 
   pageTitle="Überwachen von Vorgängen, Ereignissen und Leistungsindikatoren für den Lastenausgleich | Microsoft Azure"
   description="Erfahren Sie, wie Sie die Protokollierung für Warnereignisse und Integritätsteststatus für den Azure-Lastenausgleich aktivieren."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/26/2016"
   ms.author="joaoma" />

# Protokollanalysen für den Azure-Lastenausgleich 
Sie können in Azure verschiedene Protokolltypen verwenden, um den Lastenausgleich zu verwalten und eventuelle Fehler zu beheben. Auf einige dieser Protokolle können Sie über das Portal zugreifen, und alle Protokolle können von einem Azure-Blobspeicher extrahiert und in anderen Tools wie Excel und PowerBI angezeigt werden. In der unten stehenden Liste finden Sie weitere Informationen über die verschiedenen Typen von Protokollen.


- **Überwachungsprotokolle:** Sie können [Azure-Überwachungsprotokolle](insights-debugging-with-events.md) (ehemals Betriebsprotokolle) verwenden, um alle Vorgänge und deren Status anzuzeigen, die an Ihre Azure-Abonnements übermittelt werden. Überwachungsprotokolle sind standardmäßig aktiviert und können im Azure-Portal angezeigt werden.
- **Warnereignisprotokolle**: Verwenden Sie dieses Protokoll, um die Warnungen anzuzeigen, die für den Lastenausgleich ausgelöst wurden. Der Status des Lastenausgleichs wird alle fünf Minuten erfasst. Dieses Protokoll wird nur geschrieben, wenn ein Warnereignis für den Lastenausgleich ausgelöst wird.  
- **Integritätstestprotokolle**: Verwenden Sie dieses Protokoll, um den Status von Integritätstests abzurufen und zu überprüfen, wie viele Instanzen im Back-End des Lastenausgleichs online sind und wie viel Prozent der virtuellen Computer Netzwerkdatenverkehr aus dem Lastenausgleich empfangen. Dieses Protokoll wird bei Ereignisänderungen des Teststatus geschrieben.

>[AZURE.WARNING] Protokolle sind nur für Ressourcen verfügbar, die im Ressourcen-Manager-Bereitstellungsmodell bereitgestellt werden. Sie können Protokolle nicht für Ressourcen im klassischen Bereitstellungsmodell verwenden. Für ein besseres Verständnis der beiden Modelle lesen Sie den Artikel [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](resource-manager-deployment-model.md). <BR> Die Protokollanalyse funktioniert derzeit nur für Lastenausgleichsmodule mit Internetzugriff. Diese Einschränkung ist nicht dauerhaft und kann sich jederzeit ändern. Besuchen Sie diese Seite regelmäßig, um über zukünftige Änderungen informiert zu sein.

##Aktivieren der Protokollierung
Die Überwachungsprotokollierung ist automatisch jederzeit für alle Ressourcen-Manager-Ressourcen aktiviert. Sie müssen die Ereignis- und Integritätstestprotokollierung aktivieren, um mit der Erfassung von Daten aus diesen Protokollen zu beginnen. Führen Sie zum Aktivieren der Protokollierung die folgenden Schritte aus.

Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an. Wenn Sie noch nicht über ein Lastenausgleichsmodul verfügen, [erstellen Sie einen Load Balancer](load-balancer-internet-arm-ps.md), bevor Sie fortfahren.

Klicken Sie im Portal auf **Durchsuchen** >> **Load Balancer**.

![Portal – Lastenausgleich](./media/load-balancer-monitor-log/load-balancer-browse.png)

Wählen Sie ein vorhandene Lastenausgleichsmodul aus >> **Alle Einstellungen**.

![Portal – Einstellungen für den Lastenausgleich](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

Klicken Sie auf dem Blatt **Einstellungen** auf **Diagnose**, und klicken Sie im Bereich **Diagnose** neben **Status** auf **Ein**. Klicken Sie auf dem Blatt **Einstellungen** auf **Speicherkonto**, und wählen Sie entweder ein vorhandenes Speicherkonto aus oder erstellen ein neues.

Wählen Sie in der Dropdownliste direkt unter **Speicherkonto** aus, ob Sie Warnereignisse, Integritätsteststatus oder beides protokollieren möchten, und klicken Sie dann auf **Speichern**.

![Vorschauportal – Diagnoseprotokolle](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] Für Überwachungsprotokolle ist kein separates Speicherkonto erforderlich. Bei der Nutzung von Speicher zur Ereignis- und Integritätstestprotokollierung fallen Gebühren an.


## Überwachungsprotokoll
Dieses Protokoll (früher als "Betriebsprotokoll" bekannt) wird standardmäßig von Azure generiert. Die Protokolle werden 90 Tage lang im Azure-Ereignisprotokollspeicher aufbewahrt. Weitere Informationen zu diesen Protokollen finden Sie im Artikel [Anzeigen von Ereignis- und Überwachungsprotokollen](insights-debugging-with-events.md).

## Warnereignisprotokoll
Dieses Protokoll wird nur generiert, wenn Sie es wie oben beschrieben für das jeweilige Lastenausgleichsmodul aktiviert haben. Die Daten werden im Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die Informationen werden im JSON-Format protokolliert, wie unten dargestellt.

	
	{
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventTimeStampUtc": "1/23/2016 2:27:07 AM",
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
	

Die JSON-Ausgabe zeigt die *eventname*-Eigenschaft, die den Grund beschreibt, aus dem der Lastenausgleich eine Warnung generiert hat. In diesem Fall wurde die Warnung generiert, weil Quell-IP-NAT-Grenzwerte (SNAT) eine TCP-Portauslastung verursacht haben.

## Integritätstestprotokoll
Dieses Protokoll wird nur generiert, wenn Sie es wie oben beschrieben für das jeweilige Lastenausgleichsmodul aktiviert haben. Die Daten werden im Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die folgenden Daten werden protokolliert:

		{
	    "records":

	    {
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "eventTimeStampUtc": "1/23/2016 2:18:58 AM",
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 1,
	            "healthPercentage": 50.000000
	        }
	    },
	    {
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "eventTimeStampUtc": "1/23/2016 2:20:31 AM",
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 0,
	            "healthPercentage": 100.000000
	        }
	    }

	]
	}

Die JSON-Ausgabe zeigt im Eigenschaftenfeld die grundlegenden Informationen zum Status des Integritätstests. Die *dipDownCount*-Eigenschaft zeigt die Gesamtzahl von Instanzen im Back-End, die aufgrund fehlerhafter Testantworten keinen Netzwerkdatenverkehr empfangen.

##Anzeigen und Analysieren des Überwachungsprotokolls
Mit einer der folgenden Methoden können Sie die Überwachungsprotokolldaten anzeigen und analysieren:

- **Azure-Tools:** Rufen Sie Informationen aus den Überwachungsprotokollen über Azure PowerShell, die Azure-Befehlszeilenschnittstelle, die Azure REST-API oder über das Azure-Vorschauportal ab. Schrittweise Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit dem Ressourcen-Manager](resource-group-audit.md).
- **Power BI:** Wenn Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe des [Azure Audit Logs Content Pack for Power BI](https://support.powerbi.com/knowledgebase/articles/742695) können Sie Ihre Daten mit vorkonfigurierten Dashboards analysieren, die Sie im Istzustand oder angepasst verwenden können.

##Anzeigen und Analysieren des Integritätstest- und Ereignisprotokolls 
Sie müssen eine Verbindung mit Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge für das Integritätstest- und das Ereignisprotokoll abrufen. Sobald Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, PowerBI oder einem anderen Datenvisualisierungstool anzeigen.

>[AZURE.TIP] Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von Github verwenden

##Zusätzliche Ressourcen

- Blogbeitrag [Visualize your Azure Audit Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Blogbeitrag [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0128_2016-->
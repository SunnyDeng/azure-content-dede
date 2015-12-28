<properties 
   pageTitle="Überwachen von Vorgängen, Ereignissen und Leistungsindikatoren für NSGs | Microsoft Azure"
   description="Erfahren Sie, wie Sie Leistungsindikatoren-, Ereignis- und Überwachungsprotokolle für NSGs aktivieren."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

#Protokollanalysen für Netzwerksicherheitsgruppen (NSGs)

Sie können in Azure verschiedene Protokolltypen verwenden, um NSGs zu verwalten und eventuelle Fehler zu beheben. Auf einige dieser Protokolle können Sie über das Portal zugreifen, und alle Protokolle können von einem Azure-Blobspeicher extrahiert und in anderen Tools wie Excel und PowerBI angezeigt werden. In der unten stehenden Liste finden Sie weitere Informationen über die verschiedenen Typen von Protokollen.

- **Überwachungsprotokolle:** Sie können [Azure-Überwachungsprotokolle](insights-debugging-with-events.md) (ehemals Betriebsprotokolle) verwenden, um alle Vorgänge und deren Status anzuzeigen, die an Ihre Azure-Abonnements übermittelt werden. Überwachungsprotokolle sind standardmäßig aktiviert und können im Azure-Vorschauportal angezeigt werden.
- **Ereignisprotokolle:** Mithilfe dieses Protokolls können Sie anzeigen, welche NSG-Regeln auf virtuelle Computer und Instanzrollen auf Grundlage der MAC-Adresse angewendet werden. Der Status für diese Regeln wird alle 60 Sekunden erfasst. 
- **Leistungsindikatorenprotokolle:** Verwenden Sie dieses Protokoll, um anzuzeigen, wie oft jede NSG-Regel angewendet wurde, um Datenverkehr zuzulassen oder zu verweigern.

>[AZURE.WARNING]Protokolle sind nur für Ressourcen verfügbar, die im Ressourcen-Manager-Bereitstellungsmodell bereitgestellt werden. Sie können Protokolle nicht für Ressourcen im klassischen Bereitstellungsmodell verwenden. Für ein besseres Verständnis der beiden Modelle lesen Sie den Artikel [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](resource-manager-deployment-model.md).

##Aktivieren der Protokollierung
Die Überwachungsprotokollierung ist automatisch jederzeit für alle Ressourcen-Manager-Ressourcen aktiviert. Sie müssen die Ereignis- und Leistungsindikatorenprotokollierung aktivieren, um mit der Erfassung von Daten aus diesen Protokollen zu beginnen. Führen Sie zum Aktivieren der Protokollierung die folgenden Schritte aus.

1.  Melden Sie sich beim [Azure-Vorschauportal](http://portal.azure.com) an. Wenn Sie noch keine Netzwerksicherheitsgruppe haben, [erstellen Sie eine NSG](virtual-networks-create-nsg-arm-ps.md), bevor Sie fortfahren. 

2.  Klicken Sie im Vorschauportal auf **Durchsuchen** > **Netzwerksicherheitsgruppen**.

	![Vorschauportal – Netzwerksicherheitsgruppen](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Wählen Sie eine vorhandene Netzwerksicherheitsgruppe aus.

	![Vorschauportal – Einstellungen für Netzwerksicherheitsgruppen](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Klicken Sie im Blatt **Einstellungen** auf **Diagnose** und dann im Blatt **Diagnose** neben **Status** auf **Ein**.
5. Klicken Sie im Blatt **Einstellungen** auf **Speicherkonto**, und wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues.  

>[AZURE.INFORMATION]Für Überwachungsprotokolle ist kein separates Speicherkonto erforderlich. Bei der Nutzung von Speicher zur Ereignis- und Regelprotokollierung fallen Gebühren an.

6. Wählen Sie in der Dropdownliste direkt unter **Speicherkonto** aus, ob Sie Ereignisse, Leistungsindikatoren oder beides protokollieren möchten, und klicken Sie dann auf **Speichern**.

	![Vorschauportal – Diagnoseprotokolle](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## Überwachungsprotokoll
Dieses Protokoll (früher als "Betriebsprotokoll" bekannt) wird standardmäßig von Azure generiert. Die Protokolle werden 90 Tage lang im Azure-Ereignisprotokollspeicher aufbewahrt. Weitere Informationen zu diesen Protokollen finden Sie im Artikel [Anzeigen von Ereignis- und Überwachungsprotokollen](insights-debugging-with-events.md).

## Leistungsindikatorenprotokoll
Dieses Protokoll wird nur generiert, wenn Sie es wie oben beschrieben für die jeweiligen NSGs aktiviert haben. Die Daten werden im Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Wie unten dargestellt, wird jede Regel, die auf Ressourcen angewendet wurden, im JSON-Format protokolliert.

	{
		"time": "2015-09-11T23:14:22.6940000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupRuleCounter",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupCounters",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"DenyAllOutBound",
			"direction":"Out",
			"type":"block",
			"matchedConnections":0
			}
	}

## Ereignisprotokoll
Dieses Protokoll wird nur generiert, wenn Sie es wie oben beschrieben für die jeweiligen NSGs aktiviert haben. Die Daten werden im Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die folgenden Daten werden protokolliert:

	{
		"time": "2015-09-11T23:05:22.6860000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupEvent",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupEvents",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"AllowVnetOutBound",
			"direction":"Out",
			"priority":65000,
			"type":"allow",
			"conditions":{
				"destinationPortRange":"0-65535",
				"sourcePortRange":"0-65535",
				"destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
				"sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
			}
		}
	}

##Anzeigen und Analysieren des Überwachungsprotokolls
Mit einer der folgenden Methoden können Sie die Überwachungsprotokolldaten anzeigen und analysieren:

- **Azure-Tools:** Rufen Sie Informationen aus den Überwachungsprotokollen über Azure PowerShell, die Azure-Befehlszeilenschnittstelle, die Azure REST-API oder über das Azure-Vorschauportal ab. Schrittweise Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit dem Ressourcen-Manager](resource-group-audit.md).
- **Power BI:** Wenn Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe des [Azure Audit Logs Content Pack for Power BI](https://support.powerbi.com/knowledgebase/articles/742695) können Sie Ihre Daten mit vorkonfigurierten Dashboards analysieren, die Sie im Istzustand oder angepasst verwenden können.

##Anzeigen und Analysieren der Leistungsindikatoren- und Ereignisprotokolle 
Für Leistungsindikatoren- und Ereignisprotokolle müssen Sie eine Verbindung zu Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge abrufen. Sobald Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, PowerBI oder einem anderen Datenvisualisierungstool anzeigen.

>[AZURE.TIP]Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von Github verwenden

##Zusätzliche Ressourcen

- Blogbeitrag [Visualize your Azure Audit Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Blogbeitrag [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_1217_2015-->
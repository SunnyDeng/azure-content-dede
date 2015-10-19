<properties
	pageTitle="Integration in einen lokalen SAP-Server in Microsoft Azure App Service"
	description="Informationen zur Integration in einen lokalen SAP-Server"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="sameerch"/>


# Integration in einen lokalen SAP-Server
Mithilfe des SAP-Connectors können Sie Web-, mobile und Logik-Apps von Azure App Services in Ihren vorhandenen SAP-Server integrieren. Dadurch können Sie RFCs, BAPIs und tRFCs aufrufen sowie IDocs an den SAP-Server senden, auch wenn dieser sich hinter Ihrer lokalen Firewall befindet.

Wenn Sie über einen lokalen SAP-Server verfügen, verwenden Sie einen Hybridlistener, um wie dargestellt eine Verbindung zum SAP-Connector herzustellen:

![Datenfluss der Hybridkonnektivität][1]

Während ein SAP-Connector in der Cloud keine direkte Verbindung zu einem SAP-Server hinter einer lokalen Firewall herstellen kann, können Sie die Lücke mithilfe des Hybridlisteners schließen. Dies richten Sie durch Hosten eines Relay-Endpunkts ein, der dem Connector erlaubt, eine sichere Verbindung mit dem SAP-Server herzustellen.


## Verschiedene Möglichkeiten zur Integration mit SAP
Die folgenden Aktionen werden unterstützt:

- RFC aufrufen
- tRFC aufrufen
- BAPI aufrufen
- IDoc senden

## Voraussetzungen
Die SAP-spezifischen Clientbibliotheken sind auf dem Clientcomputer erforderlich, auf dem der Hybridlistener installiert und ausgeführt wird. Die genauen Details finden Sie [hier][9] im Abschnitt **Für den SAP-Adapter**.


## Erstellen eines neuen SAP-Connectors
1. Melden Sie sich beim Microsoft Azure-Verwaltungsportal an.
2. Wählen Sie **Neu** aus.
3. Wählen Sie auf dem Blatt "Erstellen" **Compute** > **Azure Marketplace** aus.
4. Wählen Sie auf dem Blatt "Marketplace" **API-Apps**, und suchen Sie auf der Suchleiste nach SAP:

	![SAP-Connector-API-App][2]
5. Wählen Sie den von Microsoft veröffentlichten **SAP-Connector** aus.
6. Wählen Sie auf dem Blatt "SAP-Connector" **Erstellen** aus.
7. Geben Sie auf dem neuen Blatt, das geöffnet wird, Folgendes ein:
	1. **Standort** – Wählen Sie den geografischen Standort, an dem Sie den Connector bereitstellen möchten.
	2. **Abonnement** – Wählen Sie ein Abonnement, in dem dieser Connector erstellt werden soll.
	3. **Ressourcengruppe** – Wählen oder erstellen Sie eine Ressourcengruppe, in der sich der Connector befinden soll.
	4. **Webhostingplan** - Wählen oder erstellen Sie einen Webhostingplan.
	5. **Tarif** – Wählen Sie einen Tarif für den Connector.
	6. **Name** – Geben Sie einen Namen für den SAP-Connector ein.
	7. **Paketeinstellungen**
		- **Servername** – Geben Sie den Namen des SAP-Servers ein. Beispiel: "SAPserver" oder "SAPserver.mydomain.com".
		- **Benutzername** – Geben Sie einen gültigen Benutzernamen für die Verbindung mit dem SAP-Server ein.
		- **Kennwort** – Geben Sie ein gültiges Kennwort für die Verbindung mit dem SAP-Server ein.
		- **Systemnummer** – Geben Sie die Systemnummer des SAP-Anwendungsservers ein.
		- **Sprache** – Geben Sie die Anmeldesprache ein, z. B. "DE". Wird kein Wert eingegeben, wird "EN" ausgewählt.
		- **Lokal** – Geben Sie an, ob sich Ihr SAP-Server lokal hinter einer Firewall befindet oder nicht. Falls ja, müssen Sie einen Listener-Agent auf einem Server installieren, der auf Ihren SAP-Server zugreifen kann. Sie können zur API-App-Seite "Zusammenfassung" wechseln und zum Installieren des Agents "Hybridverbindung" auswählen.
		- **Service Bus-Verbindungszeichenfolge** – Geben Sie diesen Parameter ein, wenn Ihr SAP-Server lokal ist. Dies muss eine gültige Verbindungszeichenfolge für den Service Bus-Namespace sein.
		- **RFCs** – Geben Sie die RFCs in SAP ein, die vom Connector aufgerufen werden dürfen.
		- **TRFCs** – Geben Sie die TRFCs in SAP ein, die vom Connector aufgerufen werden dürfen.
		- **BAPIs** – Geben Sie die BAPIs in SAP ein, die vom Connector aufgerufen werden dürfen.
		- **IDOCs** – Geben Sie die IDOCs in SAP ein, die vom Connector gesendet werden können.
	8. Klicken Sie auf "Auswählen". Innerhalb weniger Minuten wird Ihr SAP-Connector erstellt.


## Installieren des Hybridlisteners
Navigieren Sie über **Durchsuchen** > **API-Apps** > *Name des Connectors* zum SAP-Connector, den Sie erstellt haben.

Auf dem Blatt des Connectors sehen Sie, dass der Hybridverbindungsstatus "Ausstehend" ist. Wählen Sie "Hybridverbindung" aus. Das Blatt "Hybridverbindung" wird geöffnet:

![Blatt "Hybridverbindung"][3]

Kopieren Sie die primäre Gateway-Konfigurationszeichenfolge. Sie verwenden sie später im Rahmen der Konfiguration des Hybridlisteners.

Klicken Sie auf den Link **Download and configure**. Klicken Sie nach dem Öffnen des Installers auf folgendes Element:

![ClickOnce-Installer für Hybridverbindung][4]

Klicken Sie auf **Installieren**, und geben Sie dann die Konfigurationszeichenfolge für das primäre Gateway ein, die Sie zuvor kopiert haben:

![Relay-Verbindungszeichenfolge für Lauschen][5]

Klicken Sie auf **Installieren**, um die Hybrid Connection Manager-Einrichtung abzuschließen:

![Hybrid Connection Manager-Installation wird durchgeführt][6]

![Hybrid Connection Manager-Installation ist abgeschlossen][7]

## Überprüfen der Hybridverbindung
Navigieren Sie über **Durchsuchen** > **API-Apps** > *Name des Connectors* zum SAP-Connector, den Sie erstellt haben.

Auf dem Blatt des Connectors sehen Sie, dass der Hybridverbindungsstatus *Verbunden* lautet.

![Hybridverbindungsstatus – verbunden][8]


## Verwenden des SAP-Connectors in Logik-Apps
Sobald der SAP-Connector erstellt wurde, kann es innerhalb des Workflows Ihrer Logik-Apps verwendet werden. Erstellen Sie dazu eine neue Logik-App über **Neu** > **Logik-Apps** > **Erstellen**. Geben Sie die Metadaten für die Logik-App samt Ressourcengruppe ein.

Wählen Sie **Triggers and actions** aus. Der Workflow-Designer für Logik-Apps wird geöffnet.

Wählen Sie im rechten Bereich den SAP-Connector aus, und wählen Sie auf der Registerkarte "Aktionen" eine Aktion aus.

> [AZURE.NOTE]Die Liste der Aktionen basiert auf der Konfiguration, die Sie eingegeben haben, als Sie den SAP-Connector erstellt haben.

Für die ausgewählte Aktion sehen Sie die Eingabe- und Ausgabeparameter. Sie können die Eingaben für die Aktion eingeben und die Ausgabe der aktuellen Aktion in anderen API-Apps ggf. für die weitere Entscheidungsfindung im Workflow nutzen.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://download.microsoft.com/download/2/D/7/2D7CE8DF-A6C5-45F0-8319-14C3F1F9A0C7/InstallationGuide.htm

<!---HONumber=Oct15_HO2-->
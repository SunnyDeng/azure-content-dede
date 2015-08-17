<properties 
	pageTitle="Verschieben von Daten zwischen lokalen Quellen und der Cloud mit Azure Data Factory" 
	description="Erfahren Sie mehr über das Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway und Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway
Eine Herausforderung für die moderne Datenintegration ist das nahtlose Verschieben von Daten zwischen lokalen Speicherorten und der Cloud. Mit Data Factory ist diese Integration durch das Datenverwaltungsgateway nahtlos möglich. Das Data Factory-Verwaltungsgateway ist ein Agent, den Sie lokal installieren können, um hybride Pipelines zu ermöglichen.

Dieser Artikel bietet einen Überblick über die Integration von lokalen Datenspeichern in Clouddatenspeicher und die Cloudverarbeitung mit Data Factory. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) und anderen Artikeln über zentrale Konzepte von Data Factory auf. Im folgenden Überblick wird davon ausgegangen, dass Sie mit Data Factory-Konzepten wie Pipelines, Aktivitäten, Datasets und der Kopieraktivität vertraut sind.

Das Datengateway bietet die folgenden Funktionen:

1.	Modellieren von lokalen Datenquellen und Clouddatenquellen innerhalb einer Data Factory und Verschieben von Daten.
2.	Nutzen einer zentralen Konsole für die Überwachung und Verwaltung mit Informationen über den Gatewaystatus über cloudbasierte Data Factory-Dashboards.
3.	Sicheres Verwalten des Zugriffs auf lokale Datenquellen.
	1. An der Unternehmensfirewall ist keine Änderung erforderlich. Das Gateway stellt nur ausgehende HTTP-basierte Verbindungen mit dem Internet her.
	2. Verschlüsseln Sie die Anmeldeinformationen für Ihre lokalen Datenspeicher mit dem Zertifikat.
4.	Effizientes Verschieben von Daten – Daten werden parallel verschoben, und zwar mit Ausfallsicherheit bei vorübergehenden Netzwerkproblemen dank automatischer Wiederholungslogik.

## Überlegungen zur Verwendung des Datenverwaltungsgateways
1.	Eine einzelne Instanz des Datenverwaltungsgateways kann für mehrere lokale Datenquellen verwendet werden. Denken Sie aber daran, dass eine **Gatewayinstanz an nur eine Azure Data Factory gebunden ist** und nicht mit einer anderen Data Factory gemeinsam genutzt werden kann.
2.	Es darf **nur eine Instanz des Datenverwaltungsgateways** auf einem Computer installiert sein. Angenommen, Sie haben zwei Data Factorys, die Zugriff auf lokale Datenquellen benötigen, dann müssen Sie die Gateways auf zwei lokalen Computern installieren, wobei jedes Gateway an eine separate Daten Factory gebunden ist.
3.	Das **Gateway muss sich nicht auf dem gleichen Computer wie die Datenquelle befinden**, aber die Nähe zur Datenquelle verkürzt die Verbindungszeit des Gateways mit der Datenquelle. Es wird empfohlen, das Gateway auf einem anderen Computer als dem Computer zu installieren, auf dem die lokale Datenquelle gehostet wird. So konkurriert das Gateway nicht mit der Datenquelle um Ressourcen.
4.	Sie können über **mehrere Gateways auf verschiedenen Computern verfügen, die eine Verbindung mit der gleichen lokalen Datenquelle herstellen**. Sie können z. B. über zwei Gateways verfügen, die zwei Data Factorys mit Daten versorgen, wobei jedoch die lokale Datenquelle für die beiden Data Factorys registriert ist.
5.	Wenn Sie bereits ein Gateway auf dem Computer installiert haben, das ein **Power BI**-Szenario unterstützt, installieren Sie auf einem anderen Computer ein **separates Gateway für die Azure Data Factory**.
6.	Sie müssen **das Gateway selbst bei der Verwendung von ExpressRoute verwenden**. 
7.	Auch bei Verwendung von **ExpressRoute** und **des Gateways**, um eine Verbindung zwischen dem Dienst und der Datenquelle herzustellen, sollten Sie Ihre Datenquelle wie eine lokale Datenquelle behandeln (die sich hinter einer Firewall befindet). 

## Gatewayinstallation – Voraussetzungen
1.	Die unterstützten **Betriebssystemversionen** sind Windows 7, Windows 8/8.1, Windows Server 2008 R2 und Windows Server 2012.
2.	Die empfohlene **Konfiguration** für den Gatewaycomputer lautet: mindestens 2 GHz, 4 Kerne, 8 GB RAM und 80 GB Festplattenspeicher.
3.	Wenn der Hostcomputer in den Ruhezustand wechselt, kann das Gateway nicht auf Datenanforderungen reagieren. Aus diesem Grund sollten Sie vor der Installation des Gateways einen entsprechenden **Energiesparplan** auf dem Computer konfigurieren. Bei der Gatewayinstallation wird eine Meldung angezeigt, wenn der Computer für den Ruhezustand konfiguriert ist.

Aufgrund der Tatsache, dass die Kopieraktivität mit einer bestimmten Häufigkeit ausgeführt wird, folgt auch die Ressourcenverwendung (CPU, Arbeitsspeicher) auf dem Computer dem gleichen Muster mit Spitzen- und Leerlaufzeiten. Die Ressourcenverwendung hängt auch stark von der Datenmenge ab, die verschoben wird. Wenn mehrere Kopieraufträge in Bearbeitung sind, werden Sie feststellen, dass die Ressourcenverwendung während der Spitzenzeiten ansteigt. Zwar ist oben die minimale Konfiguration angegeben, es ist aber immer besser, eine Konfiguration mit mehr Ressourcen als die oben beschriebene minimale Konfiguration zu haben. Dabei ist Ihre spezifische Auslastung für Datenverschiebungen relevant.

## Installation
Das Datenverwaltungsgateway kann installiert werden, indem Sie ein MSI-Setuppaket aus dem Microsoft Download Center herunterladen. Die MSI-Datei kann auch verwendet werden, um ein vorhandenes Datenverwaltungsgateway auf die neueste Version zu aktualisieren, wobei alle Einstellungen beibehalten werden. Anhand der schrittweisen exemplarischen Vorgehensweise unten können Sie den Link für das MSI-Paket im Azure-Portal finden.

### Bewährte Methoden für die Installation
1.	Konfigurieren Sie den Energiesparplan auf dem Hostcomputer für das Gateway, sodass der Computer nicht in den Ruhezustand wechselt. Wenn der Hostcomputer in den Ruhezustand wechselt, kann das Gateway nicht auf Datenanforderungen reagieren.
2.	Sichern Sie das Zertifikat, das dem Gateway zugeordnet ist.

### Problembehandlung bei der Installation:
Wenn Ihr Unternehmen eine Firewall oder einen Proxyserver verwendet, sind möglicherweise zusätzliche Schritte erforderlich, falls das Datenverwaltungsgateway keine Verbindung mit Microsoft-Clouddiensten herstellen kann.

#### Anzeigen von Gatewayprotokollen mit der Ereignisanzeige:

Der Gatewaykonfigurations-Manager zeigt den Status für das Gateway an, beispielsweise "Getrennt" oder "Verbindung wird hergestellt".

Weitere Informationen finden Sie in den Gatewayprotokollen der Windows-Ereignisprotokolle. Sie befinden sich in der **Ereignisanzeige** von Windows unter **Anwendungs- und Dienstprotokolle** > **Datenverwaltungsgateway**. Suchen Sie bei der Behandlung von Problemen mit Gateways in der Ereignisanzeige nach Fehlerereignissen.


#### Mögliche Symptome für Probleme mit der Firewall:

1. Wenn Sie versuchen, das Gateway zu registrieren, wird die folgende Fehlermeldung angezeigt: "Fehler beim Registrieren des Gatewayschlüssels. Prüfen Sie, ob sich das Datenverwaltungsgateway im Status "Verbunden" befindet und der Datenverwaltungsgateway-Hostdienst gestartet wurde, bevor Sie versuchen, den Gatewayschlüssel erneut zu registrieren."
2. Wenn Sie den Konfigurations-Manager öffnen, wird der Status als "Getrennt" oder "Verbindung wird hergestellt" angezeigt. Beim Anzeigen der Windows-Ereignisprotokolle unter "Ereignisanzeige" > "Anwendungs- und Dienstprotokolle" > "Datenverwaltungsgateway" werden Fehlermeldungen wie "Fehler beim Verbinden mit dem Remoteserver" oder "Eine Komponente des Datenverwaltungsgateways reagiert nicht mehr und wird automatisch neu gestartet. Komponentenname: Gateway." angezeigt.

Diese werden durch eine nicht ordnungsgemäße Konfiguration der Firewall oder des Proxyservers verursacht, durch die das Datenverwaltungsgateway keine Verbindung mit den Clouddiensten herstellen kann, um sich zu authentifizieren.

Die möglicherweise relevanten Firewalls sind die Unternehmensfirewall, die auf dem zentralen Router des Unternehmens ausgeführt wird, und die Windows-Firewall, die als Daemon auf dem lokalen Computer konfiguriert ist, auf dem das Gateway installiert ist. Im Folgenden sind einige Überlegungen aufgeführt:

- Es besteht keine Notwendigkeit, die eingehende Richtlinie für die für Unternehmensfirewall zu ändern.
- Die Unternehmensfirewall und die Windows-Firewall sollten die ausgehende Regel für TCP-Ports 80, 440 und 9305 bis 9354 aktivieren. Diese werden von Microsoft Azure Service Bus verwendet, um die Verbindung zwischen den Clouddiensten und dem Datenverwaltungsgateway herzustellen.

Das MSI-Setup konfiguriert die Windows-Firewallregeln für eingehende Ports für den Gatewaycomputer automatisch (weitere Informationen finden Sie im Abschnitt zu Ports und Sicherheitsaspekten).

Aber beim Setup wird davon ausgegangen, dass die oben erwähnten ausgehenden Ports auf dem lokalen Computer und für die Unternehmensfirewall standardmäßig zulässig sind. Sie müssen diese ausgehenden Ports aktivieren, wenn dies nicht der Fall ist. Wenn Sie die Windows-Firewall durch die Firewall eines Drittanbieters ersetzt haben, müssen diese Ports möglicherweise manuell geöffnet werden.

Wenn Ihr Unternehmen einen Proxyserver verwendet, müssen Sie Microsoft Azure der Positivliste hinzufügen. Sie können eine Liste der gültigen Microsoft Azure-IP-Adressen aus dem [Microsoft Download Center](http://msdn.microsoft.com/library/windowsazure/dn175718.aspx) herunterladen.

## Verwenden des Datengateways – schrittweise exemplarische Vorgehensweise
In dieser exemplarischen Vorgehensweise erstellen Sie eine Data Factory mit einer Pipeline, die Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob kopiert.

### Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt verwenden Sie das Azure-Verwaltungsportal zum Erstellen einer Azure Data Factory-Instanz mit dem Namen **ADFTutorialOnPremDF**. Sie können auch eine Data Factory mithilfe von Azure Data Factory-Cmdlets erstellen.

1.	Nach der Anmeldung beim [Azure-Vorschauportal](https://portal.azure.com) klicken Sie links unten auf **NEU**, dann auf **Datenanalyse** auf dem Blatt **Erstellen** und anschließend auf **Data Factory** auf dem Blatt **Datenanalyse**.

	![Neu->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. Gehen Sie auf dem Blatt **Neue Data Factory** wie folgt vor:
	1. Geben Sie **ADFTutorialOnPremDF** als **Namen** ein.
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und wählen Sie **ADFTutorialResourceGroup** aus. Sie können eine vorhandene Ressourcengruppe auswählen oder eine neue erstellen. So erstellen Sie eine neue Ressourcengruppe:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie auf dem Blatt **Ressourcengruppe erstellen** unter **Name** einen Namen für die Ressourcengruppe ein, und klicken Sie auf **OK**.

7. Auf dem Blatt **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.

	![Zum Startmenü hinzufügen](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data factory name "ADFTutorialOnPremDF" is not available** ändern Sie den Namen der Data Factory (z. B.in "IhrNameADFTutorialOnPremDF") und wiederholen den Vorgang. Verwenden Sie diesen Namen beim Ausführen der restlichen Schritte in diesem Lernprogramm anstelle von "ADFTutorialOnPremDF".

9. Suchen Sie im Hub **BENACHRICHTIGUNGEN** auf der linken Seite nach Benachrichtigungen zum Erstellungsvorgang. Klicken Sie auf **X**, um das Blatt **BENACHRICHTIGUNGEN** zu schließen, falls es geöffnet ist.

	![Hub BENACHRICHTIGUNGEN](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Nach Abschluss der Erstellung wird das Blatt **DATA FACTORY** wie unten dargestellt angezeigt:

	![Data Factory-Startseite](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### Schritt 2: Erstellen eines Datenverwaltungsgateways
5.	Klicken Sie auf dem Blatt **Data Factory** für **ADFTutorialOnPremDF** auf **Verknüpfte Dienste**. 

	![Data Factory-Startseite](./media/data-factory-move-data-between-onprem-and-cloud/DataFactoryHomePage.png)

2.	Klicken Sie auf dem Blatt **Verknüpfte Dienste** auf **Neues Datengateway**.

	![Verknüpfte Dienste – Schaltfläche "Gateway hinzufügen"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremLinkedServicesAddGaewayButton.png)

2. Geben Sie auf dem Blatt **Erstellen** den Namen **adftutorialgateway** unter **Name** ein, und klicken Sie auf **OK**.

	![Blatt "Gateway erstellen"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Klicken Sie auf dem Blatt **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Installationspaket für das Gateway heruntergeladen, installiert, konfiguriert und das Gateway auf dem Computer registriert.

	> [AZURE.NOTE]Hierfür ist Internet Explorer oder ein mit Microsoft ClickOnce kompatibler Webbrowser erforderlich.

	![Blatt "Gateway konfigurieren"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Dies ist die einfachste Möglichkeit (One-Click) zum Herunterladen, Installieren, Konfigurieren und Registrieren des Gateways in einem einzigen Schritt. Sie können sehen, dass die Anwendung **Microsoft Datenverwaltungsgateway – Konfigurations-Manager** auf Ihrem Computer installiert ist. Die ausführbare Datei **ConfigManager.exe** befindet sich im Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Sie können das Gateway auch manuell über die Links in diesem Fenster herunterladen und installieren und mit dem Schlüssel im Textfeld **MIT SCHLÜSSEL REGISTRIEREN** registrieren.
	
	Im Abschnitt [Datenverwaltungsgateway](#DMG) finden Sie ausführliche Informationen zum Gateway mit bewährten Methoden und wichtigen Hinweisen.

	>[AZURE.NOTE]Sie müssen ein Administrator auf dem lokalen Computer sein, um das Datenverwaltungsgateway erfolgreich installieren und konfigurieren zu können. Sie können der lokalen Windows-Gruppe "Datenverwaltungsgateway-Benutzer" zusätzliche Benutzer hinzufügen. Die Mitglieder dieser Gruppe können den Datenverwaltungsgateway-Konfigurations-Manager verwenden, um das Gateway zu konfigurieren.

4. Klicken Sie auf der linken Seite auf den Hub **BENACHRICHTIGUNGEN**. Warten Sie, bis die Meldung **Express-Setup für "adftutorialgateway" erfolgreich** auf dem Blatt **Benachrichtigungen** angezeigt wird.

	![Express-Installation erfolgreich](./media/data-factory-move-data-between-onprem-and-cloud/express-setup-succeeded.png)
6. Klicken Sie auf dem Blatt **Erstellen** auf **OK** und dann auf das Blatt **Neues Datenverwaltungsgateway**.
6. Schließen Sie das Blatt **Verknüpfte Dienste** (durch Klicken auf **X** rechts oben), und öffnen Sie erneut das Blatt **Verknüpfte Dienste**, um den aktuellen Status des Gateways anzuzeigen. 
7. Überprüfen Sie, ob der **Status** des Gateways **Online** lautet. 

	![Gatewaystatus](./media/data-factory-move-data-between-onprem-and-cloud/gateway-status.png)

5. Starten Sie den **Microsoft Datenverwaltungsgateway-Konfigurations-Manager** auf Ihrem Computer.

	![Gatewaykonfigurations-Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Warten Sie, bis die Werte wie folgt festgelegt sind:
	1. Wenn der **Status** des Diensts nicht auf **Gestartet** festgelegt ist, klicken Sie auf **Dienst starten**, um den Dienst zu starten. Warten Sie eine Minute, bis die anderen Felder aktualisiert wurden.
	2. **Gatewayname** wird auf **adftutorialgateway** festgelegt.
	3. **Instanzname** wird auf **adftutorialgateway** festgelegt.
	4. **Gateway-Schlüsselstatus** wird auf **Registriert** festgelegt.
	5. Die Statusleiste im unteren Bereich zeigt **Mit Datenverwaltungsgateway-Clouddienst verbunden** sowie ein **grünes Häkchen** an.
	
7. Wechseln Sie zu **Zertifikate**. Das auf dieser Registerkarte angegebene Zertifikat wird zum Verschlüsseln/Entschlüsseln von Anmeldeinformationen für den lokalen Datenspeicher verwendet, den Sie im Portal angeben. Klicken Sie auf **Ändern**, um Ihr eigenes Zertifikat zu verwenden. Standardmäßig verwendet das Gateway das Zertifikat, das vom Data Factory-Dienst automatisch generiert wird.

	![Konfiguration des Gatewayzertifikats](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

8. Vergewissern Sie sich im Portal auf dem Blatt **Verknüpfte Dienste**, dass der **Status** des Gateways **Gut** lautet.


### Schritt 2: Erstellen von verknüpften Diensten 
In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **SqlServerLinkedService**. Der **SqlServerLinkedService** verknüpft eine lokale SQL Server-Datenbank, und der verknüpfte Dienst **StorageLinkedService** verknüpft einen Azure-Blobspeicher mit der **ADFTutorialDataFactory**. Sie erstellen eine Pipeline weiter unten in dieser exemplarischen Vorgehensweise, die Daten aus der lokalen SQL Server-Datenbank in den Azure-Blobspeicher kopiert.

#### Hinzufügen eines verknüpften Diensts zu einer lokalen SQL Server-Datenbank
1.	Klicken Sie auf dem Blatt **Verknüpfte Dienste** auf der Befehlsleiste auf **Neuer Datenspeicher**.
2.	Geben Sie **SqlServerLinkedService** für **Name** ein. 
2.	Klicken Sie auf den Pfeil neben **Typ**, und wählen Sie **SQL Server** aus.

	![Neuen Datenspeicher erstellen](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	Es gibt weitere Einstellungen unter der Einstellung **Typ**.
4.	Wählen Sie als **Datengateway** das Gateway aus, das Sie gerade erstellt haben. 

	![SQL Server-Einstellungen](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	Geben Sie den Namen des Datenbankservers für die Einstellung **Server** ein.
5.	Geben Sie den Namen der Datenbank für die Einstellung **Datenbank** ein.
6.	Klicken Sie auf den Pfeil neben **Anmeldeinformationen**.

	![Blatt "Anmeldeinformationen"](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	Klicken Sie auf dem Blatt **Anmeldeinformationen** auf **Klicken Sie hier, um Anmeldeinformationen festzulegen**.
8.	Gehen Sie im Dialogfeld **Anmeldeinformationen festlegen** folgendermaßen vor:

	![Dialogfeld "Anmeldeinformationen festlegen"](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png) 1. Wählen Sie die **Authentifizierung** aus, die der Data Factory-Dienst für die Verbindung mit der Datenbank verwenden soll. 2. Geben Sie für die Einstellung **BENUTZERNAME** den Namen des Benutzers ein, der auf die Datenbank zugreifen kann. 3. Geben Sie für die Einstellung **KENNWORT** das Kennwort für den Benutzer ein. 4. Klicken Sie auf **OK**, um das Dialogfeld zu schließen. 
4. Klicken Sie auf **OK**, um das Blatt **Anmeldeinformationen** zu schließen. 
5. Klicken Sie auf dem Blatt **Neuer Datenspeicher** auf **OK**. 	
6. Überprüfen Sie, ob der Status für **SqlServerLinkedService** auf dem Blatt "Verknüpfte Dienste" auf "Online" festgelegt ist. ![Status des verknüpften SQL Server-Diensts](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

Wenn Sie auf das Portal auf einem Computer zugreifen, der sich vom Computer mit dem Gateway unterscheidet, müssen Sie sicherstellen, dass die Anwendung "Anmeldeinformations-Manager" eine Verbindung mit dem Gatewaycomputer herstellen kann. Wenn die Anwendung den Computer mit dem Gateway nicht erreichen kann, können Sie keine Anmeldeinformationen für die Datenquelle festlegen und die Verbindung mit der Datenquelle nicht testen.

##### Festlegen von Anmeldeinformationen und Sicherheit
Wenn Sie die oben beschriebene Methode verwendet und die Anwendung "Anmeldeinformationen festlegen" über das Azure-Portal gestartet haben, um Anmeldeinformationen für eine lokale Datenquelle festzulegen, werden die Anmeldeinformationen im Portal mit dem Zertifikat verschlüsselt, das Sie auf der Registerkarte "Zertifikat" des Datenverwaltungsgateway-Konfigurations-Managers auf dem Gatewaycomputer angegeben haben.

Wenn Sie einen API-basierten Ansatz zum Verschlüsseln der Anmeldeinformationen suchen, können Sie das PowerShell-Cmdlet [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/azure/dn834940.aspx) verwenden, um Anmeldeinformationen zu verschlüsseln. Das Cmdlet verwendet das Zertifikat, für dessen Verwendung dieses Gateway konfiguriert ist, um die Anmeldeinformationen zu verschlüsseln. Sie können die verschlüsselten Anmeldeinformationen, die von diesem Cmdlet zurückgegeben werden, zum EncryptedCredential-Element von connectionString in der JSON-Datei, die Sie mit dem Cmdlet [New-AzureDataFactoryLinkedService](https://msdn.microsoft.com/library/azure/dn820246.aspx) verwenden, oder im JSON-Ausschnitt im Data Factory-Editor im Portal hinzufügen.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**Hinweis:** Bei Verwendung der Anwendung "Anmeldeinformationen festlegen" werden die verschlüsselten Anmeldeinformationen im verknüpften Dienst automatisch festgelegt, wie oben dargestellt.

Es gibt eine weitere Möglichkeit zum Festlegen der Anmeldeinformationen mit dem Data Factory-Editor. Wenn Sie einen verknüpften SQL Server-Dienst mit dem Editor erstellen und Anmeldeinformationen im Nur-Text-Format eingeben, werden die Anmeldeinformationen mit einem Zertifikat verschlüsselt, das der Data Factory-Dienst besitzt, NICHT mit dem Zertifikat, für dessen Verwendung dieses Gateway konfiguriert ist. Dieser Ansatz ist möglicherweise etwas schneller, in einigen Fällen aber weniger sicher. Daher empfehlen wir, dass Sie diesen Ansatz nur für die Entwicklung/Tests verwenden.

#### Hinzufügen eines verknüpften Diensts für Azure-Speicherkonten
 
1. Klicken Sie auf dem Blatt **Verknüpfte Dienste** auf der Symbolleiste auf **Neuer Datenspeicher**. 
2. Geben Sie **StorageLinkedService** im Feld **Name** ein. 
3. Klicken Sie auf den Pfeil neben **Typ**, und wählen Sie **Azure Storage** aus.
4. Sie sollten jetzt die neuen Felder **Kontoname** und **Kontoschlüssel** unter der Einstellung **Typ** sehen. 
3. Geben Sie den Namen des Azure-Speicherkontos für **Kontoname** ein.
4. Geben Sie den Schlüssel des Azure-Speicherkontos für **Kontoschlüssel** ein. 
5. Klicken Sie auf **OK**, um das Dialogfeld zu schließen. 

 
### Schritt 3: Erstellen von Eingabe- und Ausgabedatasets
In diesem Schritt erstellen Sie die Eingabe- und Ausgabedatasets, die ein- und ausgehende Daten für den Kopiervorgang darstellen (lokale SQL Server-Datenbank = > Azure-Blobspeicher). Vor dem Erstellen von Datasets oder Tabellen (rechteckige Datasets) müssen Sie Folgendes ausführen (detaillierte Schritte in der Liste):

- Erstellen Sie eine Tabelle mit dem Namen **emp** in der SQL Server-Datenbank, die Sie der Data Factory als verknüpften Dienst hinzugefügt haben, und fügen Sie verschiedene Beispieleinträge in die Tabelle ein.
- Erstellen Sie einen Blobcontainer mit dem Namen **adftutorial** im Azure-Blobspeicherkonto, das Sie der Data Factory als verknüpfter Dienst hinzugefügt haben.

### Vorbereitung des lokalen SQL Servers für das Lernprogramm

1. Verwenden Sie in der Datenbank, die Sie für den lokalen verknüpften SQL Server-Dienst (**SqlServerLinkedService**) angegeben haben, das folgende SQL-Skript zum Erstellen der Tabelle **emp** in der Datenbank.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Fügen Sie einige Beispiele in die Tabelle ein:


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Erstellen der Eingabetabelle

1.	Klicken Sie auf dem Blatt **DATA FACTORY** auf die Kachel **Erstellen und bereitstellen**, um den Editor für die Data Factory zu starten.

	![Kachel "Erstellen und bereitstellen"](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
1. Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf **Neues Dataset**, und wählen Sie **Lokaler SQL Server**. 
2.	Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Beachten Sie Folgendes:
	
	- **type** ist auf **SqlServerTable** festgelegt.
	- **tableName** ist auf **emp** festgelegt.
	- **linkedServiceName** ist auf **SqlServerLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	- Für eine Eingabetabelle, die nicht durch eine andere Pipeline in Azure Data Factory generiert wird, müssen Sie **external** auf **true** festlegen. Dies bedeutet, dass die eingegebenen Daten außerhalb des Azure Data Factory-Diensts erstellt werden. Optional können Sie externe Datenrichtlinien mit dem **externalData**-Element im **policy**-Abschnitt angeben.    

	Details zu den JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen (eine Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass auf der Titelleiste **TABELLE ERFOLGREICH BEREITGESTELLT** angezeigt wird.


### Erstellen der Ausgabetabelle

1.	Klicken Sie im **Data Factory-Editor** auf der Symbolleiste auf **Neues Dataset** und dann auf **Azure-Blobspeicher**.
2.	Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text: 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Beachten Sie Folgendes:
	
	- **type** ist auf **AzureBlob** festgelegt.
	- **linkedServiceName** ist auf **StorageLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	- **folderPath** ist auf **adftutorial/outfromonpremdf** festgelegt, wobei "outfromonpremdf" der Ordner im "adftutorial"-Container ist. Sie müssen lediglich den **adftutorial**-Container erstellen.
	- **availability** ist auf **hourly** festgelegt (**frequency** auf **hour** und **interval** auf **1**). Der Data Factory-Dienst generiert in der Tabelle **emp** in der Azure SQL-Datenbank stündlich einen Ausgabedatenslice. 

	Wenn Sie keinen **fileName** für eine **Eingabetabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner (**folderPath**) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beachten Sie auch die Beispieldateien im [Tutorial][adf-tutorial].
 
	Wenn Sie keinen **fileName** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die "partitionedBy"-Eigenschaft. Im folgenden Beispiel verwendet folderPath die Angaben für Jahr, Monat und Tag aus "SliceStart" (Startzeit des zu verarbeitenden Slices) und "fileName" die Angabe für Stunde aus "SliceStart". Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Details zu den JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

2.	Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen (eine Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass auf der Titelleiste **TABELLE ERFOLGREICH BEREITGESTELLT** angezeigt wird.
  

### Schritt 4: Erstellen und Ausführen einer Pipeline
In diesem Schritt erstellen Sie eine **Pipeline** mit einer **Kopieraktivität**, die **EmpOnPremSQLTable** als Eingabe und **OutputBlobTable** als Ausgabe verwendet.

1.	Klicken Sie auf dem Blatt **DATA FACTORY** auf die Kachel **Erstellen und bereitstellen**, um den Editor für die Data Factory zu starten.

	![Kachel "Erstellen und bereitstellen"](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	Klicken Sie auf der Befehlsleiste auf **Neue Pipeline**. Wenn die Schaltfläche nicht angezeigt wird, klicken Sie auf **... (Auslassungspunkte)**, um die Befehlsleiste zu erweitern.
2.	Ersetzen Sie das JSON-Skript im rechten Bereich durch folgenden Text:   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Beachten Sie Folgendes:
 
	- Der Abschnitt "activities" enthält nur eine Aktivität, deren **type** auf **Copy** festgelegt ist.
	- **Input** für die Aktivität ist auf **EmpOnPremSQLTable** und **output** auf **OutputBlobTable** festgelegt.
	- Im Abschnitt **transformation** ist **SqlSource** als **Quelltyp** und **BlobSink** als **Senkentyp** angegeben.
- Die SQL-Abfrage **select * from emp** ist für die **sqlReaderQuery**-Eigenschaft von **SqlSource** angegeben.

	Ersetzen Sie den Wert der **start**-Eigenschaft durch den aktuellen Tag und den Wert der **end**-Eigenschaft durch den nächsten Tag. Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Angabe für **end** ist optional, wird aber in diesem Lernprogramm verwendet.
	
	Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als **start + 48 Stunden** berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9/9/9999** an.
	
	Durch Angabe des aktiven Zeitraums für eine Pipeline definieren Sie die Dauer, für die die Datenslices verarbeitet werden, basierend auf den **Verfügbarkeitseigenschaften**, die für jede Azure Data Factory-Tabelle definiert wurden.
	
	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen (eine Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass auf der Titelleiste **PIPELINE ERFOLGREICH BEREITGESTELLT** angezeigt wird.
5. Schließen Sie jetzt das Blatt **Editor**, indem Sie auf **X** klicken. Klicken Sie erneut auf **X**, um das Blatt "ADFTutorialDataFactory" mit der Symbolleiste und Strukturansicht zu schließen. Wenn die Meldung **Ihre nicht gespeicherten Änderungen werden verworfen** angezeigt wird, klicken Sie auf **OK**.
6. Sie sollten sich nun wieder auf dem Blatt **DATA FACTORY** für **ADFTutorialOnPremDF** befinden.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

#### Anzeigen einer Diagrammansicht der Data Factory 
1. Klicken Sie im **Azure-Vorschauportal** auf der Startseite für die Data Factory **ADFTutorialOnPremDF** auf die Kachel **Diagramm**.

	![Link "Diagramm"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagrammansicht](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	Sie können die Ansicht vergrößern, verkleinern, auf 100 % anpassen, an die Fenstergröße anpassen, Pipelines und Tabellen automatisch positionieren und Informationen zur Datenherkunft anzeigen (d. h. vor- und nachgelagerte Elemente ausgewählter Elemente hervorheben). Sie können auf ein Objekt (in der Ein-/Ausgabetabelle oder Pipeline) doppelklicken, um seine Eigenschaften anzuzeigen.

### Schritt 5: Überwachen der Datasets und Pipelines
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Einzelheiten zur Überwachung finden Sie unter [Überwachen und Verwalten von Pipelines](monitor-manage-pipelines.md).

1. Navigieren Sie zum **Azure-Vorschauportal** (falls Sie es geschlossen haben).
2. Wenn das Blatt für **ADFTutorialOnPremDF** nicht geöffnet ist, klicken Sie im **Startmenü** zum Öffnen auf **ADFTutorialOnPremDF**.
3. Auf diesem Blatt sollten die **Anzahl** und **Namen** der erstellten Tabellen und der Pipeline angezeigt werden.

	![Data Factory-Startseite](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Klicken Sie nun auf die Kachel **Datasets**.
5. Klicken Sie auf dem Blatt **Datasets** auf **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable-Slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Das liegt daran, dass Sie die Daten in die SQL Server-Datenbank eingefügt haben und sie immer vorhanden sind. Überprüfen Sie, ob keine Slices im Abschnitt **Problemslices** am unteren Rand angezeigt werden.


	Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** werden anhand der **UHRZEIT DER LETZTEN AKTUALISIERUNG** sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.
    

	-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set AzureDataFactorySliceStatus** oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
	-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).
 
	Klicken Sie auf den Titel der Listen oder auf **...** (Auslassungspunkte), um eine umfangreichere Liste mit Slices anzuzeigen. Klicken Sie auf der Symbolleiste auf **Filter**, um die Slices zu filtern.
	
	Zum Anzeigen der nach Start-/Endzeit sortierten Datenslices klicken Sie auf die Kachel **Datenslices (nach Slicezeit)**.

7. Klicken Sie jetzt auf dem Blatt **Datasets** auf **OutputBlobTable**.

	![OputputBlobTable-Slices][image-data-factory-output-blobtable-slices]
8. Überprüfen Sie, ob die Slices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Warten Sie, bis der Status der Slices bis zum aktuellen Zeitpunkt auf **Bereit** festgelegt ist.
9. Klicken Sie auf einen beliebigen Datenslice in der Liste. Das Blatt **DATENSLICE** sollte angezeigt werden.

	![Blatt "Datenslice"](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.

10. Klicken Sie in der Liste unten auf die **Aktivitätsausführung**, um **Aktivitätsausführung – Details** anzuzeigen.

	![Blatt "Aktivitätsausführung – Details"][image-data-factory-activity-run-details]

11. Klicken Sie auf **X**, um alle Blätter zu schließen, bis wieder
12. das Startblatt für **ADFTutorialOnPremDF** angezeigt wird.
14. (Optional) Klicken Sie auf **Pipelines**, dann auf **ADFTutorialOnPremDF**, und zeigen Sie Details zu den Eingabetabellen (**Genutzt**) oder Ausgabetabellen (**Erstellt**) an.
15. Verwenden Sie Tools wie z. B. **Azure Storage-Explorer**, um die Ausgabe zu überprüfen.

	![Azure-Speicher-Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)


## Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets 
Dieser Abschnitt beschreibt das Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets.

1. Starten Sie **Azure PowerShell** im Administratormodus. 
2. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager**-Modus verfügbar. Führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln.     

        switch-azuremode AzureResourceManager


2. Verwenden Sie das **New-AzureDataFactoryGateway**-Cmdlet, um wie folgt ein logisches Gateway zu erstellen:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Beispiel für eine Befehlszeile und Ausgabe**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Verwenden Sie das **New-AzureDataFactoryGatewayKey**-Cmdlet, um einen Registrierungsschlüssel für das neu erstellte Gateway zu generieren, und speichern Sie den Schlüssel in der lokalen Variablen **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Beispiel für eine Befehlszeilenausgabe:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Wechseln Sie in Azure PowerShell zum Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\PowerShellScript\\**, und führen Sie das Skript **RegisterGateway.ps1** aus, das mit der lokalen Variablen **$Key** verknüpft ist, wie im folgenden Befehl dargestellt. Sie registrieren damit den Client-Agent auf dem Computer mit dem logischen Gateway, das Sie zuvor erstellt haben.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Mit dem **Get-AzureDataFactoryGateway**-Cmdlet können Sie die Liste mit Gateways in Ihrer Data Factory abrufen. Wenn **online** als **Status** angezeigt wird, bedeutet dies, dass Ihr Gateway einsatzbereit ist.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Sie können ein Gateway mit dem **Remove-AzureDataFactoryGateway**-Cmdlet entfernen und die Beschreibung für das Gateway mithilfe der **Set-AzureDataFactoryGateway**-Cmdlets aktualisieren. Syntax und andere Details zu diesen Cmdlets finden Sie in der Data Factory-Cmdlet-Referenz.


## Datenfluss beim Kopieren mit dem Datenverwaltungsgateway
Wenn Sie eine Kopieraktivität in einer Datenpipeline verwenden, um lokale Daten in der Cloud zur weiteren Verarbeitung zu erfassen oder um Ergebnisdaten in der Cloud wieder in einen lokalen Datenspeicher zu exportieren, verwendet die Kopieraktivität intern ein Gateway zum Übertragen von Daten aus der lokalen Datenquelle in die Cloud und umgekehrt.

Im Folgenden sind der allgemeine Datenfluss und eine Zusammenfassung der Schritte für das Kopieren mit dem Datengateway angegeben:![Datenfluss über ein Gateway](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	Der Datenentwickler erstellt entweder mit dem [Azure-Portal](http://portal.azure.com) oder einem [PowerShell-Cmdlet](https://msdn.microsoft.com/library/dn820234.aspx) ein neues Gateway für Azure Data Factory. 
2.	Der Datenentwickler verwendet den Bereich "Verknüpfte Dienste", um einen neuen verknüpften Dienst für einen lokalen Datenspeicher mit dem Gateway zu definieren. Als Teil der Einrichtung des verknüpften Diensts verwendet der Datenentwickler die Anwendung "Anmeldeinformationen festlegen", wie in der schrittweisen exemplarischen Vorgehensweise veranschaulicht, um Authentifizierungstypen und Anmeldeinformationen anzugeben. Die Anwendung "Anmeldeinformationen festlegen" kommuniziert mit dem Datenspeicher, um die Verbindung zu testen, und mit dem Gateway, um die Anmeldeinformationen zu speichern.
3.	Das Gateway verschlüsselt die Anmeldeinformationen mit dem Zertifikat, das dem Gateway zugeordnet wurde (bereitgestellt vom Datenentwickler), bevor die Anmeldeinformationen in der Cloud gespeichert werden.
4.	Der Data Factory-Verschiebungsdienst kommuniziert für die Planung und Verwaltung von Aufträgen mit dem Gateway. Dazu wird ein Steuerungskanal genutzt, der eine freigegebene Azure Service Bus-Warteschlange verwendet. Wenn ein Auftrag der Kopieraktivität gestartet werden muss, reiht Data Factory die Anforderung zusammen mit Anmeldeinformationen in die Warteschlange ein. Das Gateway startet den Auftrag, nachdem die Warteschlange abgerufen wurde.
5.	Das Gateway entschlüsselt die Anmeldeinformationen mit dem gleichen Zertifikat und stellt dann mit dem richtigen Authentifizierungstyp eine Verbindung mit dem lokalen Datenspeicher her.
6.	Das Gateway kopiert Daten aus dem lokalen Speicher in einen Cloudspeicher oder aus einem Cloudspeicher in einen lokalen Datenspeicher. Dies ist abhängig von der Konfiguration der Kopieraktivität in der Datenpipeline. Hinweis: Für diesen Schritt kommuniziert das Gateway über einen sicheren Kanal (HTTPS) direkt mit dem cloudbasierten Speicherdienst (z. B. Azure Blob, Azure SQL usw.).

### Ports und Sicherheitsaspekte

1. Wie oben in der schrittweisen exemplarischen Vorgehensweise erwähnt, gibt es mehrere Methoden zum Festlegen der Anmeldeinformationen für lokale Datenspeicher mit Data Factory. Die Überlegungen zu Ports variieren für diese Optionen.	

	- Verwendung der App **Anmeldeinformationen festlegen**: Das Installationsprogramm für das Datenverwaltungsgateway öffnet für den Gatewaycomputer standardmäßig die Ports **8050** und **8051** in der lokalen Windows-Firewall. Diese Ports werden von der Anwendung "Anmeldeinformationen festlegen" verwendet, um die Anmeldeinformationen an das Gateway weiterzuleiten. Diese Ports werden für den Computer nur in der lokalen Windows-Firewall geöffnet. Diese Ports können nicht über das Internet erreicht werden, und Sie müssen sie nicht in der Unternehmensfirewall öffnen.
	2.	Verwendung des PowerShell-Cmdlets [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx): a. Wenn Sie den PowerShell-Befehl verwenden, um die Anmeldeinformationen zu verschlüsseln, und nicht möchten, dass bei der Gatewayinstallation die eingehenden Ports auf dem Gatewaycomputer in der Windows-Firewall geöffnet werden, können Sie während der Installation den folgenden Befehl verwenden:
	
			msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
3.	Wenn Sie die Anwendung **Anmeldeinformationen festlegen** verwenden, müssen Sie sie auf einem Computer starten, der sich mit dem Datenverwaltungsgateway verbinden kann, um Anmeldeinformationen für die Datenquelle festzulegen und die Verbindung mit der Datenquelle zu testen.
4.	Stellen Sie beim Kopieren von Daten zwischen einer lokalen SQL Server-Datenbank und einer Azure SQL-Datenbank Folgendes sicher:	
	- 	Die Firewall auf dem Gatewaycomputer lässt die ausgehende TCP-Kommunikation über **TCP**-Port **1433** zu.
	- 	Die [Azure SQL-Firewalleinstellungen](https://msdn.microsoft.com/library/azure/jj553530.aspx) müssen so konfiguriert sein, dass die **IP-Adresse des Gatewaycomputers** den **zulässigen IP-Adressen** hinzugefügt wird.
5.	Wenn Sie Daten zwischen einer lokalen SQL Server-Datenbank und beliebigen Zielen kopieren und der Gateway- und die SQL Server-Computer sich unterscheiden, gehen Sie folgendermaßen vor: [Konfigurieren Sie die Windows-Firewall](https://msdn.microsoft.com/library/ms175043.aspx) auf dem SQL Server-Computer so, dass das Gateway auf die Datenbank über Ports zugreifen kann, die von der SQL Server-Instanz überwacht werden. Für die Standardinstanz ist dies Port 1433.

<!---HONumber=August15_HO6-->
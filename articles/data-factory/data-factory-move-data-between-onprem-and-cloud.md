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
	ms.date="03/09/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway
Eine Herausforderung für die moderne Datenintegration ist das nahtlose Verschieben von Daten zwischen lokalen Speicherorten und der Cloud. Mit Data Factory ist diese Integration durch das Datenverwaltungsgateway nahtlos möglich. Das Data Factory-Verwaltungsgateway ist ein Agent, den Sie lokal installieren können, um hybride Pipelines zu ermöglichen.

Dieser Artikel bietet eine Übersicht über die Integration von lokalen Datenspeichern in Clouddatenspeicher und die Cloudverarbeitung mit Data Factory. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) und anderen Artikeln über zentrale Konzepte von Data Factory auf. In der folgenden Übersicht wird davon ausgegangen, dass Sie mit Data Factory-Konzepten wie Pipelines, Aktivitäten, Datasets und der Kopieraktivität vertraut sind.

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
4.	Sie können über **mehrere Gateways auf verschiedenen Computern verfügen, die eine Verbindung mit der gleichen lokalen Datenquelle herstellen**. Sie können z. B. über zwei Gateways verfügen, die zwei Data Factorys mit Daten versorgen, wobei jedoch die lokale Datenquelle für die beiden Data Factorys registriert ist.
5.	Wenn Sie bereits ein Gateway auf dem Computer installiert haben, das ein **Power BI**-Szenario unterstützt, installieren Sie auf einem anderen Computer ein **separates Gateway für die Azure Data Factory**.
6.	Sie müssen **das Gateway selbst bei der Verwendung von ExpressRoute verwenden**. 
7.	Auch bei Verwendung von **ExpressRoute** und **des Gateways**, um eine Verbindung zwischen dem Dienst und der Datenquelle herzustellen, sollten Sie Ihre Datenquelle wie eine lokale Datenquelle behandeln (die sich hinter einer Firewall befindet). 

## Installieren eines Datenverwaltungsgateways

### Gatewayinstallation – Voraussetzungen
1.	Die unterstützten **Betriebssystemversionen** sind Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2.
2.	Die empfohlene **Konfiguration** für den Gatewaycomputer lautet: mindestens 2 GHz, 4 Kerne, 8 GB RAM und 80 GB Festplattenspeicher.
3.	Wenn der Hostcomputer in den Ruhezustand wechselt, kann das Gateway nicht auf Datenanforderungen reagieren. Aus diesem Grund sollten Sie vor der Installation des Gateways einen entsprechenden **Energiesparplan** auf dem Computer konfigurieren. Bei der Gatewayinstallation wird eine Meldung angezeigt, wenn der Computer für den Ruhezustand konfiguriert ist.

Aufgrund der Tatsache, dass die Kopieraktivität mit einer bestimmten Häufigkeit ausgeführt wird, folgt auch die Ressourcenverwendung (CPU, Arbeitsspeicher) auf dem Computer dem gleichen Muster mit Spitzen- und Leerlaufzeiten. Die Ressourcenverwendung hängt auch stark von der Datenmenge ab, die verschoben wird. Wenn mehrere Kopieraufträge in Bearbeitung sind, werden Sie feststellen, dass die Ressourcenverwendung während der Spitzenzeiten ansteigt. Zwar ist oben die minimale Konfiguration angegeben, es ist aber immer besser, eine Konfiguration mit mehr Ressourcen als die oben beschriebene minimale Konfiguration zu haben. Dabei ist Ihre spezifische Auslastung für Datenverschiebungen relevant.

### Installation
Das Datenverwaltungsgateway kann installiert werden, indem Sie ein MSI-Setuppaket aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) herunterladen. Die MSI-Datei kann auch verwendet werden, um ein vorhandenes Datenverwaltungsgateway auf die neueste Version zu aktualisieren, wobei alle Einstellungen beibehalten werden. Anhand der exemplarischen Schritt-für-Schritt-Vorgehensweise unten können Sie den Link für das MSI-Paket im Azure-Portal ermitteln.


### Bewährte Methoden für die Installation:
1.	Konfigurieren Sie den Energiesparplan auf dem Hostcomputer für das Gateway, sodass der Computer nicht in den Ruhezustand wechselt. Wenn der Hostcomputer in den Ruhezustand wechselt, kann das Gateway nicht auf Datenanforderungen reagieren.
2.	Sichern Sie das Zertifikat, das dem Gateway zugeordnet ist.

## Aktualisieren eines Datenverwaltungsgateways
Standardmäßig wird das Datenverwaltungsgateway automatisch aktualisiert, wenn eine neuere Version des Gateways verfügbar ist. Das Gateway wird erst aktualisiert, nachdem alle geplanten Aufgaben erledigt wurden. Vom Gateway werden keine weiteren Aufgaben verarbeitet, bevor der Aktualisierungsvorgang abgeschlossen ist. Wenn die Aktualisierung fehlschlägt, wird für das Gateway ein Rollback auf die alte Version durchgeführt.

Die geplante Aktualisierungszeit wird im Portal auf dem Blatt mit den Gatewayeigenschaften, auf der Startseite des Datenverwaltungsgateway-Konfigurations-Managers und in der Benachrichtigung in der Taskleiste angezeigt. Sie können das Update sofort installieren oder warten, bis das Gateway zum geplanten Zeitpunkt automatisch aktualisiert wird. Der folgende Screenshot zeigt beispielsweise die Benachrichtigung, die im Datenverwaltungsgateway-Konfigurations-Manager zusammen mit der Schaltfläche „Aktualisieren“ angezeigt wird, auf die Sie zum sofortigen Installieren klicken.

![Update im Datenverwaltungsgateway-Konfigurations-Manager](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-config-manager.png)

Die Benachrichtigung in der Taskleiste sieht wie folgt aus:

![Meldung in der Taskleiste](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-tray-message.png)

Der Status des Aktualisierungsvorgangs (manuell oder automatisch) wird in der Taskleiste angezeigt. Wenn Sie den Datenverwaltungsgateway-Konfigurations-Manager das nächste Mal öffnen, wird in der Benachrichtigungsleiste eine Meldung mit dem Hinweis eingeblendet, dass das Gateway aktualisiert wurde. Außerdem ist ein Link zu einem [Thema mit Neuigkeiten](data-factory-gateway-release-notes.md) angegeben.

Auf der Registerkarte „Aktualisieren“ des Datenverwaltungsgateway-Konfigurations-Managers werden der Aktualisierungszeitplan und der Zeitpunkt der letzten Installation bzw. Aktualisierung des Gateways angezeigt. Wenn die automatische Aktualisierung deaktiviert ist, wird eine entsprechende Meldung eingeblendet. Sie können das Feature auf der Registerkarte aber nicht aktivieren. Sie müssen das Cmdlet verwenden, um das Feature zu aktivieren.
  

## Symbole und Benachrichtigungen in der Taskleiste
Die folgende Abbildung enthält einige Symbole, die in der Taskleiste angezeigt werden.

![Taskleistensymbole](./media/data-factory-move-data-between-onprem-and-cloud/gateway-tray-icons.png)

Wenn Sie den Cursor auf das Symbol oder die Benachrichtigung in der Taskleiste bewegen, werden Details zum Status des Gateways bzw. des Aktualisierungsvorgangs in einem Popupfenster angezeigt.

## So deaktivieren/aktivieren Sie das Feature für die automatische Aktualisierung
Sie können das Feature für die automatische Aktualisierung wie folgt deaktivieren und aktivieren:

1. Starten Sie Windows PowerShell auf dem Gatewaycomputer. 
2. Wechseln Sie in den Ordner „C:\\Programme\\Microsoft Data Management Gateway\\1.0\\PowerShellScript“.
3. Führen Sie den folgenden Befehl aus, um das Feature für die automatische Aktualisierung zu deaktivieren.   

		.\GatewayAutoUpdateToggle.ps1  -off

4. So aktivieren Sie das Feature wieder
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Port- und Sicherheitsaspekte
Es gibt zwei Firewalls, die berücksichtigt werden müssen: Die **Unternehmensfirewall**, die auf dem zentralen Router des Unternehmens ausgeführt wird, und die **Windows-Firewall**, die als Daemon auf dem lokalen Computer mit dem installierten Gateway konfiguriert ist.

![Firewalls](./media/data-factory-move-data-between-onprem-and-cloud/firewalls.png)

### Verbinden des Gateways mit Clouddiensten
Um die Verbindung des Gateways mit Azure Data Factory und anderen Clouddiensten aufrechtzuerhalten, müssen Sie sicherstellen, dass die ausgehende Regel für die **TCP**-Ports **80** und **443** konfiguriert ist. Optional können Sie auch die Ports **9350** bis **9354** aktivieren. Sie werden von Microsoft Azure Service Bus zum Herstellen einer Verbindung zwischen Azure Data Factory und dem Datenverwaltungsgateway verwendet und können eine Verbesserung der Kommunikationsleistung bewirken.

Auf Ebene der Unternehmensfirewall müssen Sie die folgenden Domänen und ausgehenden Ports konfigurieren:

| Domänennamen | Ports | Beschreibung |
| ------ | --------- | ------------ |
| **.servicebus.windows.net | 443, 80 | Listener auf Service Bus Relay per TCP (443 für Access Control-Tokenbeschaffung erforderlich) |
| *.servicebus.windows.net | 9350-9354 | Optionales Service Bus Relay per TCP |
| *.core.windows.net | 443 | HTTPS |
| *.clouddatahub.net | 443 | HTTPS |
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

Auf Ebene der Windows-Firewall sind diese ausgehenden Ports normalerweise aktiviert. Falls nicht, können Sie die Domänen und Ports auf dem Gatewaycomputer entsprechend konfigurieren.

### Festlegen von Anmeldeinformationen
Der eingehende Port **8050** wird von der Anwendung zum **Festlegen der Anmeldeinformationen** genutzt, um die Anmeldeinformationen an das Gateway weiterzugeben, wenn Sie im Azure-Portal einen lokalen verknüpften Dienst einrichten (Details hierzu weiter unten im Artikel). Während der Einrichtung des Gateways wird es von der Datenverwaltungsgateway-Installation standardmäßig auf dem Gatewaycomputer geöffnet.
 
Wenn Sie die Firewall eines Drittanbieters verwenden, können Sie Port 8050 manuell öffnen. Falls beim Einrichten des Gateways ein Firewallproblem auftritt, können Sie versuchen, den folgenden Befehl zum Installieren des Gateways ohne Konfiguration der Firewall zu verwenden.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Falls Sie die Entscheidung treffen, Port 8050 auf dem Gatewaycomputer nicht zu öffnen, müssen Sie zum Einrichten eines lokalen verknüpften Diensts andere Verfahren als die Anwendung zum **Festlegen der Anmeldeinformationen** nutzen, um die Datenspeicher-Anmeldeinformationen zu konfigurieren. Beispielsweise können Sie das PowerShell-Cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) verwenden. Informationen zum Festlegen von Datenspeicher-Anmeldeinformationen finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](#setting-credentials-and-security).

**Gehen Sie wie folgt vor, um Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher zu kopieren:**

Sie müssen sicherstellen, dass die Firewallregeln für die Unternehmensfirewall, die Windows-Firewall auf dem Gatewaycomputer und den Datenspeicher selbst richtig aktiviert sind. So kann das Gateway sowohl mit der Quelle als auch mit der Senke erfolgreich eine Verbindung herstellen. Sie müssen Regeln für jeden Datenspeicher aktivieren, der am Kopiervorgang beteiligt ist.

Zum Kopieren aus **einem lokalen Datenspeicher in eine Azure SQL-Datenbank-Senke oder eine Azure SQL Data Warehouse-Senke** müssen Sie beispielsweise die ausgehende **TCP**-Kommunikation über Port **1433** für die Windows-Firewall und die Unternehmensfirewall zulassen. Außerdem müssen Sie die Firewalleinstellungen des Azure SQL-Servers konfigurieren, um die IP-Adresse des Gatewaycomputers der Liste mit den zulässigen IP-Adressen hinzuzufügen.

### Proxyserver-Aspekte
Standardmäßig nutzt das Datenverwaltungsgateway die Proxyeinstellungen aus Internet Explorer und verwendet für den Zugriff darauf Standardanmeldeinformationen. Falls dies für Sie nicht geeignet ist, können Sie die **Proxyserver-Einstellungen** wie unten gezeigt weiter konfigurieren, um sicherzustellen, dass das Gateway eine Verbindung mit Azure Data Factory herstellen kann:

1.	Erstellen Sie nach dem Installieren des Datenverwaltungsgateways im Datei-Explorer eine sichere Kopie von „C:\\Programme\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config“, um die Originaldatei zu sichern.
2.	Starten Sie „Notepad.exe“ als Administrator, und öffnen Sie die Textdatei „C:\\Programme\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config“. Sie finden das Standardtag für „system.net“ wie folgt:

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Sie können dann die Proxyserverdetails, z. B. die Proxyadresse, in diesem übergeordneten Tag hinzufügen. Beispiel:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Zusätzliche Eigenschaften sind im Proxytag zulässig, um die erforderlichen Einstellungen anzugeben, z. B. scriptLocation. Informationen zur Syntax finden Sie unter [<proxy>-Element (Netzwerkeinstellungen)](https://msdn.microsoft.com/library/sa91de1e.aspx).

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Speichern Sie die Konfigurationsdatei am ursprünglichen Speicherort, und starten Sie den Datenverwaltungsgateway-Dienst, um die Änderungen zu übernehmen. Dies ist über **Start** > **Services.msc** möglich, oder klicken Sie im **Datenverwaltungsgateway-Konfigurations-Manager** auf die Schaltfläche **Dienst beenden** und dann auf **Dienst starten**. Wenn der Dienst nicht gestartet wird, wurde der bearbeiteten Anwendungskonfigurationsdatei wahrscheinlich eine falsche XML-Tagsyntax hinzugefügt.

Zusätzlich zu den obigen Punkten müssen Sie auch sicherstellen, dass Microsoft Azure in der Positivliste Ihres Unternehmens aufgeführt ist. Sie können die Liste mit den gültigen Microsoft Azure-IP-Adressen im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653) herunterladen.

### Mögliche Symptome für Probleme im Zusammenhang mit der Firewall und dem Proxyserver
Wenn Sie auf die unten aufgeführten Fehler treffen, liegt dies meist an der unsauberen Konfiguration der Firewall oder des Proxyservers, da für das Datenverwaltungsgateway die Verbindungsherstellung mit Azure Data Factory für die Authentifizierung blockiert wird. Verwenden Sie den obigen Abschnitt, um sicherzustellen, dass die Firewall und der Proxyserver richtig konfiguriert sind.

1.	Wenn Sie versuchen, das Gateway zu registrieren, wird die folgende Fehlermeldung angezeigt: "Fehler beim Registrieren des Gatewayschlüssels. Prüfen Sie, ob sich das Datenverwaltungsgateway im Status "Verbunden" befindet und der Datenverwaltungsgateway-Hostdienst gestartet wurde, bevor Sie versuchen, den Gatewayschlüssel erneut zu registrieren."
2.	Wenn Sie den Konfigurations-Manager öffnen, wird der Status als "Getrennt" oder "Verbindung wird hergestellt" angezeigt. Beim Anzeigen der Windows-Ereignisprotokolle unter "Ereignisanzeige" > "Anwendungs- und Dienstprotokolle" > "Datenverwaltungsgateway" werden Fehlermeldungen wie "Fehler beim Verbinden mit dem Remoteserver" oder "Eine Komponente des Datenverwaltungsgateways reagiert nicht mehr und wird automatisch neu gestartet. Komponentenname: Gateway." angezeigt.

## Problembehandlung bei Gateways


- Ausführliche Informationen hierzu finden Sie in den Gatewayprotokollen und Windows-Ereignisprotokollen. Sie befinden sich in der **Ereignisanzeige** von Windows unter **Anwendungs- und Dienstprotokolle** > **Datenverwaltungsgateway**. Suchen Sie bei der Behandlung von Problemen mit Gateways in der Ereignisanzeige nach Fehlerereignissen.
- Falls das Gateway nach dem **Ändern des Zertifikats** nicht mehr funktioniert, starten Sie den **Datenverwaltungsgateway-Dienst** neu (beenden und starten), indem Sie das Tool „Microsoft-Datenverwaltungsgateway-Konfigurations-Manager“ oder das Applet „Dienste“ in der Systemsteuerung verwenden. Falls weiterhin ein Fehler angezeigt wird, müssen Sie für Benutzer des Datenverwaltungsgateway-Diensts unter Umständen explizite Berechtigungen erteilen, damit sie auf das Zertifikat im Zertifikat-Manager (certmgr.msc) zugreifen können. Das standardmäßige Benutzerkonto für den Dienst lautet: **NT Service\\DIAHostService**. 
- Wenn Fehler in Bezug auf die Datenspeicherverbindung oder Treiber auftreten, starten Sie auf dem Computer mit dem Gateway den **Konfigurations-Manager für Datenverwaltungsgateways**. Wechseln Sie zur Registerkarte **Diagnose**, auf der Sie entsprechende Werte für die Felder in der Gruppe **Testen Sie die Verbindung mit einer lokalen Datenquelle mithilfe dieses Gateways** auswählen bzw. eingeben. Klicken Sie dann auf **Verbindung testen**, um zu prüfen, ob Sie sich auf dem Computer mit dem Gateway mithilfe der Verbindungs- und Anmeldeinformationen mit einer lokalen Datenquelle verbinden können. Wenn das Testen der Verbindung weiter misslingt, nachdem Sie einen Treiber installiert haben, starten Sie das Gateway neu, damit es die letzte Änderung übernimmt.  

	![Verbindung testen](./media/data-factory-move-data-between-onprem-and-cloud/TestConnection.png)
		
## Exemplarische Vorgehensweise: Verwenden des Datenverwaltungsgateways 
In dieser exemplarischen Vorgehensweise erstellen Sie eine Data Factory mit einer Pipeline, die Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob kopiert.

### Schritt 1: Erstellen einer Azure Data Factory
In diesem Schritt verwenden Sie das Azure-Portal zum Erstellen einer Azure Data Factory-Instanz mit dem Namen **ADFTutorialOnPremDF**. Sie können auch eine Data Factory mithilfe von Azure Data Factory-Cmdlets erstellen.

1.	Nach der Anmeldung beim [Azure-Portal](https://portal.azure.com) klicken Sie links unten auf **NEU**, wählen **Datenanalyse** auf dem Blatt **Erstellen** aus und klicken auf dem Blatt **Datenanalyse** auf **Data Factory**.

	![Neu -> Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. Gehen Sie auf dem Blatt **Neue Data Factory** wie folgt vor:
	1. Geben Sie **ADFTutorialOnPremDF** als **Namen** ein.
	2. Klicken Sie auf **RESSOURCENGRUPPENNAME**, und wählen Sie **ADFTutorialResourceGroup** aus. Sie können eine vorhandene Ressourcengruppe auswählen oder eine neue erstellen. So erstellen Sie eine neue Ressourcengruppe:
		1. Klicken Sie auf **Neue Ressourcengruppe erstellen**.
		2. Geben Sie auf dem Blatt **Ressourcengruppe erstellen** unter **Name** einen Namen für die Ressourcengruppe ein, und klicken Sie auf **OK**.

7. Auf dem Blatt **Neue Data Factory** ist **Zum Startmenü hinzufügen** aktiviert.

	![Zum Startmenü hinzufügen](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data factory name "ADFTutorialOnPremDF" is not available** ändern Sie den Namen der Data Factory (z. B.in "IhrNameADFTutorialOnPremDF") und wiederholen den Vorgang. Verwenden Sie diesen Namen beim Ausführen der restlichen Schritte in diesem Lernprogramm anstelle von "ADFTutorialOnPremDF".

9. Suchen Sie Benachrichtigungen des Erstellungsvorgangs, indem Sie auf der Titelleiste auf die Schaltfläche **Benachrichtigungen** klicken, wie im folgenden Bild gezeigt. Klicken Sie erneut auf die Schaltfläche, um das Benachrichtigungsfenster zu schließen.

	![Hub BENACHRICHTIGUNGEN](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Nach Abschluss der Erstellung wird das Blatt **DATA FACTORY** wie unten dargestellt angezeigt:

	![Data Factory-Startseite](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### Schritt 2: Erstellen eines Datenverwaltungsgateways
5. Klicken Sie auf dem Blatt **DATA FACTORY** auf die Kachel **Erstellen und bereitstellen**, um den Editor für die Data Factory zu starten.

	![Kachel "Erstellen und bereitstellen"](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	Klicken Sie in Data Factory Editor auf der Symbolleiste auf **... (Auslassungspunkte)** und dann auf **Neues Daten-Gateway**. 

	!["Neues Daten-Gateway" auf der Symbolleiste](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Geben Sie auf dem Blatt **Erstellen** den Namen **adftutorialgateway** unter **Name** ein, und klicken Sie auf **OK**. 	

	![Blatt "Gateway erstellen"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Klicken Sie auf dem Blatt **Konfigurieren** auf **Direkt auf diesem Computer installieren**. Daraufhin wird das Installationspaket für das Gateway heruntergeladen, installiert, konfiguriert und das Gateway auf dem Computer registriert.

	> [AZURE.NOTE] 
	Hierfür ist Internet Explorer oder ein mit Microsoft ClickOnce kompatibler Webbrowser erforderlich.
	> 
	> Navigieren Sie bei Verwendung von Chrome zum [Chrome Web Store](https://chrome.google.com/webstore/), führen Sie eine Suche mit dem Begriff „ClickOnce“ durch, wählen Sie eine der ClickOnce-Erweiterungen aus, und installieren Sie sie.
	>  
	> Gehen Sie bei Firefox genauso vor (Installation des Add-Ins). Klicken Sie auf der Symbolleiste auf die Schaltfläche **Menü öffnen** (**drei waagerechte Striche** in der oberen rechten Ecke), klicken Sie auf **Add-Ons**, suchen Sie nach dem Stichwort „ClickOnce“, wählen Sie eine der ClickOnce-Erweiterungen aus, und installieren Sie sie.

	![Blatt "Gateway konfigurieren"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Dies ist die einfachste Möglichkeit (One-Click) zum Herunterladen, Installieren, Konfigurieren und Registrieren des Gateways in einem einzigen Schritt. Sie können sehen, dass die Anwendung **Microsoft Datenverwaltungsgateway – Konfigurations-Manager** auf Ihrem Computer installiert ist. Die ausführbare Datei **ConfigManager.exe** befindet sich im Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Sie können das Gateway auch manuell über die Links in diesem Blatt herunterladen und installieren und mit dem Schlüssel im Textfeld **MIT SCHLÜSSEL REGISTRIEREN** registrieren.
	
	In den Abschnitten am Anfang dieses Artikels finden Sie ausführliche Informationen zum Gateway sowie bewährte Methoden und wichtige Hinweise.

	>[AZURE.NOTE] Sie müssen ein Administrator auf dem lokalen Computer sein, um das Datenverwaltungsgateway erfolgreich installieren und konfigurieren zu können. Sie können der lokalen Windows-Gruppe "Datenverwaltungsgateway-Benutzer" zusätzliche Benutzer hinzufügen. Die Mitglieder dieser Gruppe können den Datenverwaltungsgateway-Konfigurations-Manager verwenden, um das Gateway zu konfigurieren.

5. Warten Sie einige Minuten, und starten Sie dann den **Datenverwaltungsgateway-Konfigurations-Manager** auf Ihrem Computer. Geben Sie im Fenster **Suchen** den Begriff **Datenverwaltungsgateway** ein, um auf dieses Hilfsprogramm zuzugreifen. Die ausführbare Datei **ConfigManager.exe** befindet sich im Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\Shared**.

	![Gatewaykonfigurations-Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Warten Sie, bis die Werte wie folgt festgelegt sind:
	1. **Status** wird auf **Gestartet** festgelegt.
	2. **Gatewayname** wird auf **adftutorialgateway** festgelegt.
	3. **Instanzname** wird auf **adftutorialgateway** festgelegt.
	4. **Registrierung** wird auf **Registriert** festgelegt.
	5. Die Statusleiste im unteren Bereich zeigt **Mit Datenverwaltungsgateway-Clouddienst verbunden** sowie ein **grünes Häkchen** an.

8. Wechseln Sie zur Registerkarte **Zertifikate**. Das auf dieser Registerkarte angegebene Zertifikat wird zum Verschlüsseln/Entschlüsseln von Anmeldeinformationen für den lokalen Datenspeicher verwendet, den Sie im Portal angeben. Klicken Sie auf **Ändern**, um Ihr eigenes Zertifikat zu verwenden. Standardmäßig verwendet das Gateway das Zertifikat, das vom Data Factory-Dienst automatisch generiert wird.

	![Konfiguration des Gatewayzertifikats](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (Optional) Wechseln Sie zur Registerkarte **Diagnose**, und aktivieren Sie die Option **Ausführliche Protokollierung für die Problembehandlung aktivieren**, wenn Sie die ausführliche Protokollierung aktivieren möchten, um Probleme mit dem Gateway behandeln zu können. Die Protokollinformationen finden Sie in der **Ereignisanzeige** unter **Anwendungs- und Dienstprotokolle** -> Knoten **Datenverwaltungsgateway**. 

	![Registerkarte „Diagnose“](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	Sie können auf dieser Seite auch **Verbindungstests** für eine lokale Datenquelle über das Gateway durchführen.
10. Klicken Sie im Azure-Portal auf **OK** auf dem Blatt **Konfigurieren** und dann auf dem Blatt **Neues Daten-Gateway**.
6. In der Strukturansicht links sollte **adftutorialgateway** unter **Daten-Gateways** angezeigt werden. Wenn Sie darauf klicken, sollte die zugehörige JSON angezeigt werden. 
	

### Schritt 3: Erstellen von verknüpften Diensten 
In diesem Schritt erstellen Sie zwei verknüpfte Dienste: **StorageLinkedService** und **SqlServerLinkedService**. Der **SqlServerLinkedService** verknüpft eine lokale SQL Server-Datenbank, und der verknüpfte Dienst **StorageLinkedService** verknüpft einen Azure-Blobspeicher mit der Data Factory. Sie erstellen eine Pipeline weiter unten in dieser exemplarischen Vorgehensweise, die Daten aus der lokalen SQL Server-Datenbank in den Azure-Blobspeicher kopiert.

#### Hinzufügen eines verknüpften Diensts zu einer lokalen SQL Server-Datenbank
1.	Klicken Sie in **Data Factory Editor** auf der Symbolleiste auf **Neuer Datenspeicher**, und wählen Sie **SQL Server**. 

	![Neuer mit SQL Server verknüpfter Dienst](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	Gehen Sie im **JSON-Editor** folgendermaßen vor: 
	1. Geben Sie unter **gatewayName** den Namen **adftutorialgateway** an.	
	2. Bei Verwendung der Windows-Authentifizierung:
		1. In **connectionString**: 
			1. Legen Sie **Integrierte Sicherheit** auf **wahr** fest.
			2. Geben Sie für die Datenbank **Servername** und **Datenbankname** an. 
			2. Entfernen Sie **Benutzer-ID** und **Kennwort**. 
		3. Geben Sie den Benutzernamen und das Kennwort für die Eigenschaften **userName** und **password** an.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. Bei Verwendung der SQL-Authentifizierung:
		1. Geben Sie für die Datenbank **Servername**, **Datenbankname**, **Benutzer-ID** und **Kennwort** in **connectionString** an.       
		2. Entfernen Sie die letzten beiden JSON-Eigenschaften – **userName** und **password** – aus der JSON.
		3. Entfernen Sie das nachgestellte **, (Komma)** am Ende der Zeile, die den Wert für die **gatewayName**-Eigenschaft angibt. 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	   
2.	Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften SQL Server-Dienst bereitzustellen.

#### Hinzufügen eines verknüpften Diensts für Azure-Speicherkonten
 
1. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neuer Datenspeicher** und dann auf **Azure-Speicher**.
2. Geben Sie den Namen des Azure-Speicherkontos für **Kontoname** ein.
3. Geben Sie den Schlüssel des Azure-Speicherkontos für **Kontoschlüssel** ein.
4. Klicken Sie auf **Bereitstellen**, um **StorageLinkedService** bereitzustellen.
   
 
### Schritt 4: Erstellen von Eingabe- und Ausgabedatasets
In diesem Schritt erstellen Sie die Eingabe- und Ausgabedatasets, die ein- und ausgehende Daten für den Kopiervorgang darstellen (lokale SQL Server-Datenbank = > Azure-Blobspeicher). Vor dem Erstellen von Datasets oder Tabellen (rechteckige Datasets) müssen Sie Folgendes ausführen (detaillierte Schritte in der Liste):

- Erstellen Sie eine Tabelle mit dem Namen **emp** in der SQL Server-Datenbank, die Sie der Data Factory als verknüpften Dienst hinzugefügt haben, und fügen Sie verschiedene Beispieleinträge in die Tabelle ein.
- Erstellen Sie einen Blob-Container mit dem Namen **adftutorial** im Azure-Blob-Speicherkonto, das Sie der Data Factory als verknüpften Dienst hinzugefügt haben.

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

1. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neues Dataset** und dann auf **SQL Server-Tabelle**. 
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
	- **linkedServiceName** ist auf **SqlServerLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
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
	- **linkedServiceName** ist auf **StorageLinkedService** festgelegt (diesen verknüpften Dienst haben Sie in Schritt 2 erstellt).
	- **folderPath** ist auf **adftutorial/outfromonpremdf** festgelegt, wobei "outfromonpremdf" der Ordner im "adftutorial"-Container ist. Sie müssen lediglich den **adftutorial**-Container erstellen.
	- Die Verfügbarkeit (**availability**) ist auf **hourly**, (**frequency** auf **hour** und **interval** auf **1**) festgelegt. Der Data Factory-Dienst generiert in der Tabelle **emp** in der Azure SQL-Datenbank stündlich einen Ausgabedatenslice. 

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
  

### Schritt 5: Erstellen und Ausführen einer Pipeline
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
	
	Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9/9/9999** an.
	
	Durch Angabe des aktiven Zeitraums für eine Pipeline definieren Sie die Dauer, für die die Datenslices verarbeitet werden, basierend auf den **Verfügbarkeitseigenschaften**, die für jede Azure Data Factory-Tabelle definiert wurden.
	
	Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.
	
2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen (eine Tabelle ist ein rechteckiges Dataset). Vergewissern Sie sich, dass auf der Titelleiste **PIPELINE ERFOLGREICH BEREITGESTELLT** angezeigt wird.
5. Schließen Sie jetzt das Blatt **Editor**, indem Sie auf **X** klicken. Klicken Sie erneut auf **X**, um das Blatt "ADFTutorialDataFactory" mit der Symbolleiste und Strukturansicht zu schließen. Wenn die Meldung **Ihre nicht gespeicherten Änderungen werden verworfen** angezeigt wird, klicken Sie auf **OK**.
6. Sie sollten sich nun wieder auf dem Blatt **DATA FACTORY** für **ADFTutorialOnPremDF** befinden.

**Glückwunsch!** Sie haben erfolgreich eine Azure Data Factory, verknüpfte Dienste, Tabellen und eine Pipeline erstellt und die Pipeline geplant.

#### Anzeigen einer Diagrammansicht der Data Factory 
1. Klicken Sie im **Azure-Portal** auf der Startseite für die Data Factory **ADFTutorialOnPremDF** auf die Kachel **Diagramm**.

	![Link "Diagramm"](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Ein Diagramm ähnlich dem folgenden sollte angezeigt werden:

	![Diagrammansicht](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	Sie können die Ansicht vergrößern, verkleinern, auf 100 % anpassen, an die Fenstergröße anpassen, Pipelines und Tabellen automatisch positionieren und Informationen zur Datenherkunft anzeigen (d. h. vor- und nachgelagerte Elemente ausgewählter Elemente hervorheben). Sie können auf ein Objekt (in der Ein-/Ausgabetabelle oder Pipeline) doppelklicken, um seine Eigenschaften anzuzeigen.

### Schritt 6: Überwachen der Datasets und Pipelines
In diesem Schritt verwenden Sie das Azure-Portal zur Überwachung der Aktivitäten in einer Azure Data Factory. Sie können auch PowerShell-Cmdlets zum Überwachen von Datasets und Pipelines verwenden. Einzelheiten zur Überwachung finden Sie unter [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md).

1. Navigieren Sie zum **Azure-Portal** (sofern Sie es geschlossen haben).
2. Wenn das Blatt für **ADFTutorialOnPremDF** nicht geöffnet ist, klicken Sie im **Startmenü** zum Öffnen auf **ADFTutorialOnPremDF**.
3. Auf diesem Blatt sollten die **Anzahl** und **Namen** der erstellten Tabellen und der Pipeline angezeigt werden.

	![Data Factory-Startseite](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Klicken Sie nun auf die Kachel **Datasets**.
5. Klicken Sie auf dem Blatt **Datasets** auf **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable-Slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Beachten Sie, dass die Datenslices bis zum aktuellen Zeitpunkt bereits erstellt wurden und den Status **Bereit** aufweisen. Das liegt daran, dass Sie die Daten in die SQL Server-Datenbank eingefügt haben und sie immer vorhanden sind. Überprüfen Sie, ob keine Slices im Abschnitt **Problemslices** am unteren Rand angezeigt werden.


	Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** werden anhand der **UHRZEIT DER LETZTEN AKTUALISIERUNG** sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.
    

	-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set-AzureRmDataFactorySliceStatus**, oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
	-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).
 
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
15. Verwenden Sie Tools wie z. B. **Azure Storage-Explorer**, um die Ausgabe zu überprüfen.

	![Azure-Speicher-Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## Verschieben eines Gateways von einem Computer auf einen anderen
Dieser Abschnitt enthält Anweisungen zum Verschieben des Gatewayclients von einem Computer auf einen anderen.

2. Wechseln Sie im Portal zu **Data Factory home page**, und klicken Sie auf die Kachel **Verknüpfte Dienste**. 

	![Link „Datengateways“](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. Wählen Sie das Gateway im Abschnitt **DATENGATEWAYS** des Blatts **Verknüpfte Dienste** aus.
	
	![Blatt „Verknüpfte Dienste“ mit ausgewähltem Gateway](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. Klicken Sie auf dem Blatt **Datengateway** auf **Datengateway herunterladen und installieren**.
	
	![Link „Gateway herunterladen“](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. Klicken Sie auf dem Blatt **Konfigurieren** auf **Datengateway herunterladen und installieren**, und befolgen Sie die Anweisungen zum Installieren des Datengateways auf dem Computer. 

	![Blatt „Konfigurieren“](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. Lassen Sie den **Microsoft-Datenverwaltungsgateway-Konfigurations-Manager** geöffnet. 
 
	![Konfigurations-Manager](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. Klicken Sie im Portal auf dem Blatt **Konfigurieren** auf der Befehlsleiste auf **Schlüssel neu erstellen** und dann für die Warnmeldung auf **Ja**. Klicken Sie auf die Schaltfläche **Kopieren** neben dem Schlüsseltext, um den Schlüssel in die Zwischenablage zu kopieren. Beachten Sie, dass das Gateway auf dem alten Computer den Betrieb einstellt, sobald Sie den Schlüssel neu erstellen.  
	
	![Schlüssel neu erstellen](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. Fügen Sie den **Schlüssel** in das Textfeld auf der Seite **Gateway registrieren** des Datenverwaltungsgateway-Konfigurations-Managers auf Ihrem Computer ein. (Optional:) Aktivieren Sie das Kontrollkästchen **Gatewayschlüssel anzeigen**, um den Text des Schlüssels einzublenden.
 
	![Schlüssel kopieren und registrieren](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. Klicken Sie auf **Registrieren**, um das Gateway beim Clouddienst zu registrieren.
10. Klicken Sie auf der Seite **Zertifikat angeben** auf **Durchsuchen**, um das Zertifikat auszuwählen, das mit dem alten Gateway verwendet wurde, geben Sie das **Kennwort** ein, und klicken Sie auf **Fertig stellen**. 
 
	![Zertifikat angeben](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	Sie können folgendermaßen ein Zertifikat vom alten Gateway exportieren: Starten Sie den Konfigurations-Manager für Datenverwaltungsgateways auf dem alten Computer, wechseln Sie zur Registerkarte **Zertifikat**, klicken Sie auf **Exportieren**, und folgen Sie den Anweisungen. 
10. Nach erfolgreicher Registrierung des Gateways sollten auf der Startseite des Gateway-Konfigurations-Managers **Registrierung** auf **Registriert** und die Option **Status** auf **Gestartet** festgelegt sein. 

## Festlegen von Anmeldeinformationen und Sicherheit

Sie können einen verknüpften SQL Server-Dienst auch mithilfe des Blatts "Verknüpfte Dienste" anstelle des Data Factory-Editors erstellen.
 
3.	Klicken Sie auf der Data Factory-Startseite auf die Kachel **Verknüpfte Dienste**. 
4.	Klicken Sie auf dem Blatt **Verknüpfte Dienste** auf der Befehlsleiste auf **Neuer Datenspeicher**. 
4.	Geben Sie **SqlServerLinkedService** für **Name** ein. 
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

	![Dialogfeld "Anmeldeinformationen festlegen"](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png)
	1.	Wählen Sie die **Authentifizierung** aus, die der Data Factory-Dienst für die Verbindung mit der Datenbank verwenden soll. 
	2.	Geben Sie für die Einstellung **BENUTZERNAME** den Namen des Benutzers ein, der auf die Datenbank zugreifen kann. 
	3.	Geben Sie für die Einstellung **KENNWORT** das Kennwort für den Benutzer ein.  
	4.	Klicken Sie auf **OK**, um das Dialogfeld zu schließen. 
4. Klicken Sie auf **OK**, um das Blatt **Anmeldeinformationen** zu schließen. 
5. Klicken Sie auf dem Blatt **Neuer Datenspeicher** auf **OK**. 	
6. Überprüfen Sie, ob der Status für **SqlServerLinkedService** auf dem Blatt "Verknüpfte Dienste" auf "Online" festgelegt ist.
	![Status des verknüpften SQL Server-Diensts](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

Wenn Sie auf das Portal auf einem Computer zugreifen, der sich vom Computer mit dem Gateway unterscheidet, müssen Sie sicherstellen, dass die Anwendung "Anmeldeinformations-Manager" eine Verbindung mit dem Gatewaycomputer herstellen kann. Wenn die Anwendung den Computer mit dem Gateway nicht erreichen kann, können Sie keine Anmeldeinformationen für die Datenquelle festlegen und die Verbindung mit der Datenquelle nicht testen.

Wenn Sie die Anwendung "Anmeldeinformationen festlegen" über das Azure-Portal gestartet haben, um Anmeldeinformationen für eine lokale Datenquelle festzulegen, werden die Anmeldeinformationen im Portal mit dem Zertifikat verschlüsselt, das Sie auf der Registerkarte "Zertifikat" des Datenverwaltungsgateway-Konfigurations-Managers auf dem Gatewaycomputer angegeben haben.

Wenn Sie einen API-basierten Ansatz zum Verschlüsseln der Anmeldeinformationen nutzen möchten, können Sie das PowerShell-Cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) verwenden, um Anmeldeinformationen zu verschlüsseln. Das Cmdlet verwendet das Zertifikat, für dessen Verwendung dieses Gateway konfiguriert ist, um die Anmeldeinformationen zu verschlüsseln. Sie können die verschlüsselten Anmeldeinformationen, die von diesem Cmdlet zurückgegeben werden, dem EncryptedCredential-Element von „connectionString“ in der JSON-Datei, die Sie mit dem Cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) verwenden, oder im Portal im Data Factory-Editor dem JSON-Ausschnitt hinzufügen.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**Hinweis:** Bei Verwendung der Anwendung "Anmeldeinformationen festlegen" werden die verschlüsselten Anmeldeinformationen im verknüpften Dienst automatisch festgelegt, wie oben dargestellt.

Es gibt eine weitere Möglichkeit zum Festlegen der Anmeldeinformationen mit dem Data Factory-Editor. Wenn Sie einen verknüpften SQL Server-Dienst mit dem Editor erstellen und Anmeldeinformationen im Nur-Text-Format eingeben, werden die Anmeldeinformationen mit einem Zertifikat verschlüsselt, das der Data Factory-Dienst besitzt, NICHT mit dem Zertifikat, für dessen Verwendung dieses Gateway konfiguriert ist. Dieser Ansatz ist möglicherweise etwas schneller, in einigen Fällen aber weniger sicher. Daher empfehlen wir, dass Sie diesen Ansatz nur für die Entwicklung/Tests verwenden.


## Erstellen und Registrieren eines Datenverwaltungsgateways mit Azure PowerShell 
Dieser Abschnitt beschreibt das Erstellen und Registrieren eines Gateways mit Azure PowerShell-Cmdlets.

1. Starten Sie **Azure PowerShell** im Administratormodus. 
2. Melden Sie sich bei Ihrem Azure-Konto an, indem Sie den folgenden Befehl ausführen und Ihre Azure-Anmeldeinformationen eingeben. 

	Login-AzureRmAccount
2. Verwenden Sie das **New-AzureRmDataFactoryGateway**-Cmdlet, um wie folgt ein logisches Gateway zu erstellen:

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Beispiel für eine Befehlszeile und Ausgabe**:


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. Wechseln Sie in Azure PowerShell zum Ordner **C:\\Programme\\Microsoft Data Management Gateway\\1.0\\PowerShellScript\\**, und führen Sie das Skript **RegisterGateway.ps1** aus, das mit der lokalen Variablen **$Key** verknüpft ist, wie im folgenden Befehl dargestellt. Sie registrieren damit den Client-Agent auf dem Computer mit dem logischen Gateway, das Sie zuvor erstellt haben.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	Sie können das Gateway auf einem Remotecomputer registrieren, indem Sie den Parameter „IsRegisterOnRemoteMachine“ verwenden. Beispiel:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. Mit dem **Get-AzureRmDataFactoryGateway**-Cmdlet können Sie die Liste mit Gateways in Ihrer Data Factory abrufen. Wenn **online** als **Status** angezeigt wird, bedeutet dies, dass Ihr Gateway einsatzbereit ist.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Sie können ein Gateway mit dem **Remove-AzureRmDataFactoryGateway**-Cmdlet entfernen und die Beschreibung für das Gateway mithilfe der **Set-AzureRmDataFactoryGateway**-Cmdlets aktualisieren. Syntax und andere Details zu diesen Cmdlets finden Sie in der Data Factory-Cmdlet-Referenz.

## Auflisten von Gateways mit PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

## Entfernen von Gateways mit PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## Datenfluss beim Kopieren mit dem Datenverwaltungsgateway
Wenn Sie eine Kopieraktivität in einer Datenpipeline verwenden, um lokale Daten in der Cloud zur weiteren Verarbeitung zu erfassen oder um Ergebnisdaten in der Cloud wieder in einen lokalen Datenspeicher zu exportieren, verwendet die Kopieraktivität intern ein Gateway zum Übertragen von Daten aus der lokalen Datenquelle in die Cloud und umgekehrt.

Im Folgenden sind der allgemeine Datenfluss und eine Zusammenfassung der Schritte für das Kopieren mit dem Datengateway angegeben:
![Datenfluss über ein Gateway](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	Der Datenentwickler erstellt entweder über das [Azure-Portal](https://portal.azure.com) oder mit einem [PowerShell-Cmdlet](https://msdn.microsoft.com/library/dn820234.aspx) ein neues Gateway für Azure Data Factory. 
2.	Der Datenentwickler verwendet den Bereich "Verknüpfte Dienste", um einen neuen verknüpften Dienst für einen lokalen Datenspeicher mit dem Gateway zu definieren. Als Teil der Einrichtung des verknüpften Diensts verwendet der Datenentwickler die Anwendung "Anmeldeinformationen festlegen", wie in der schrittweisen exemplarischen Vorgehensweise veranschaulicht, um Authentifizierungstypen und Anmeldeinformationen anzugeben. Die Anwendung "Anmeldeinformationen festlegen" kommuniziert mit dem Datenspeicher, um die Verbindung zu testen, und mit dem Gateway, um die Anmeldeinformationen zu speichern.
3.	Das Gateway verschlüsselt die Anmeldeinformationen mit dem Zertifikat, das dem Gateway zugeordnet wurde (bereitgestellt vom Datenentwickler), bevor die Anmeldeinformationen in der Cloud gespeichert werden.
4.	Der Data Factory-Verschiebungsdienst kommuniziert für die Planung und Verwaltung von Aufträgen mit dem Gateway. Dazu wird ein Steuerungskanal genutzt, der eine freigegebene Azure Service Bus-Warteschlange verwendet. Wenn ein Auftrag der Kopieraktivität gestartet werden muss, reiht Data Factory die Anforderung zusammen mit Anmeldeinformationen in die Warteschlange ein. Das Gateway startet den Auftrag, nachdem die Warteschlange abgerufen wurde.
5.	Das Gateway entschlüsselt die Anmeldeinformationen mit dem gleichen Zertifikat und stellt dann mit dem richtigen Authentifizierungstyp eine Verbindung mit dem lokalen Datenspeicher her.
6.	Das Gateway kopiert Daten aus dem lokalen Speicher in einen Cloudspeicher oder aus einem Cloudspeicher in einen lokalen Datenspeicher. Dies ist abhängig von der Konfiguration der Kopieraktivität in der Datenpipeline. Hinweis: Für diesen Schritt kommuniziert das Gateway über einen sicheren Kanal (HTTPS) direkt mit dem cloudbasierten Speicherdienst (z. B. Azure Blob, Azure SQL usw.).

<!---HONumber=AcomDC_0309_2016-->

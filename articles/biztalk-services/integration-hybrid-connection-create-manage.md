<properties 
	pageTitle="Erstellen und Verwalten von Hybridverbindungen | Azure" 
	description="Erfahren Sie, wie Sie eine Hybridverbindung herstellen, die Verbindung verwalten und den Hybridverbindungs-Manager installieren. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="mandia"/>


# Erstellen und Verwalten von Hybridverbindungen


## Übersicht über die Schritte
1. Erstellen Sie eine Hybridverbindung, indem Sie den Hostnamen oder die IP-Adresse der lokalen Ressource im privaten Netzwerk eingeben.
2. Verknüpfen Sie die Azure-Web-Apps bzw. die mobilen Azure-Apps mit der Hybridverbindung.
3. Installieren Sie den Hybrid Connection Manager auf der lokalen Ressource, und stellen Sie eine Verbindung zur betreffenden Hybridverbindung her. Das Azure-Portal stellt ein Ein-Klick-Verfahren für die Installation und Verbindung bereit.
4. Verwalten Sie Hybridverbindungen und deren Verbindungsschlüssel.

In diesem Thema werden diese Schritte aufgeführt.


## <a name="CreateHybridConnection"></a>Erstellen einer Hybridverbindung

Eine Hybridverbindung kann im Azure-Verwaltungsportal anhand von Web-Apps **oder** BizTalk Services erstellt werden.

**Informationen zum Erstellen von Hybridverbindungen mit Web-Apps** finden Sie unter [Verbinden von Azure-Web-Apps mit einer lokalen Ressource](../web-sites-hybrid-connection-get-started.md).

**So erstellen Sie Hybridverbindungen in BizTalk Services**:

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus. <br/>Wenn Sie keinen vorhandenen BizTalk Service haben, können Sie [einen BizTalk Service erstellen](biztalk-provision-services.md).
3. Wählen Sie die Registerkarte "Hybridverbindungen" aus: <br/> ![Registerkarte "Hybridverbindungen"][HybridConnectionTab]

4. Wählen Sie **Hybridverbindung erstellen** aus, oder klicken Sie auf die Schaltfläche **HINZUFÜGEN** in der Taskleiste. Geben Sie Folgendes ein:

	Eigenschaft | Beschreibung
--- | ---
Name | Der Name der Hybridverbindung muss eindeutig sein, und es darf sich nicht um den Namen des BizTalk Service handeln. Sie können jeden beliebigen Namen eingeben; er sollte aber auf den Zweck hinweisen. Beispiele:<br/><br/>Lohnbuchhaltung*SQLServer*<br/>Lieferantenliste*SharepointServer*<br/>Kunden*OracleServer*
Hostname | Geben Sie den vollständig qualifizierten Hostnamen, nur den Hostnamen oder die IPv4-Adresse der lokalen Ressource ein. Beispiele:<br/><br/>mySQLServer<br/>*mySQLServer*.*Domäne*.corp.*IhreFirma*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*IhreFirma*.com<br/>10.100.10.10
Port | Geben Sie die Portnummer der lokalen Ressource ein. Wenn Sie Web-Apps verwenden, geben Sie beispielsweise Port 80 oder Port 443 ein. Wenn Sie SQL Server verwenden, geben Sie Port 1433 ein.

5. Wählen Sie das Häkchen aus, um das Setup abzuschließen.

#### Zusätzlich

- Es können mehrere Hybridverbindungen erstellt werden. Die Anzahl der zulässigen Verbindungen finden Sie unter [BizTalk Services: Editionsübersicht](biztalk-editions-feature-chart.md) für . 
- Jede Hybridverbindung wird mit einem Paar von Verbindungszeichenfolgen erstellt: Anwendungsschlüsseln, die SENDEN, und lokalen Schlüsseln, die LAUSCHEN. Jedes Paar hat einen primären und einen sekundären Schlüssel. 


## <a name="LinkWebSite"></a>Verknüpfen von Azure-Web-Apps oder mobilen Azure-Apps

Um die Azure-Web-Apps mit einer vorhandenen Hybridverbindung zu verknüpfen, wählen Sie auf dem Blatt "Hybridverbindungen" die Option **Vorhandene Hybridverbindung verwenden** aus. Weitere Informationen finden Sie unter [Verbinden von Azure-Web-Apps mit einer lokalen Ressource](../web-sites-hybrid-connection-get-started.md).

Um die mobilen Azure-Apps mit einer vorhandenen Hybridverbindung zu verknüpfen, wählen Sie die Option **Hybridverbindung hinzufügen** aus, wenn Sie einen mobilen Dienst ändern oder erstellen. Weitere Informationen finden Sie unter [Azure Mobile Services und Hybridverbindungen](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).


## <a name="InstallHCM"></a>Lokale Installation des Hybrid Connection Manager

Nachdem eine Hybridverbindung erstellt wurde, installieren Sie den Hybrid Connection Manager auf der lokalen Ressource. Er kann aus den Azure-Web-Apps oder von BizTalk Service heruntergeladen werden. Schritte für BizTalk Services:

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus. 
3. Wählen Sie die Registerkarte **Hybridverbindungen** aus: <br/> ![Registerkarte "Hybridverbindungen"][HybridConnectionTab]
4. Wählen Sie auf der Taskleiste **Lokale Einrichtung** aus: <br/> ![Lokale Einrichtung][HCOnPremSetup]
5. Wählen Sie **Installieren und konfigurieren** aus, um den Hybrid Connection Manager auf dem lokalen System herunterzuladen oder auszuführen. 
6. Wählen Sie das Häkchen, um die Installation zu starten. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
-->

#### Zusätzlich
- Hybridverbindungen unterstützen lokale Ressourcen, die unter den folgenden Betriebssystemen installiert sind:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Nachdem Sie den Hybrid Connection Manager installiert haben, geschieht Folgendes:

	- Die auf Azure gehostete Hybridverbindung wird automatisch so konfiguriert, dass sie die primäre Anwendungsverbindungszeichenfolge verwendet. 
	- Die lokale Ressource wird automatisch so konfiguriert, dass sie die primäre lokale Verbindungszeichenfolge verwendet.

- Der Hybrid Connection Manager muss eine gültige lokale Verbindungszeichenfolge für die Autorisierung verwenden. Die Azure-Web-Apps bzw. die mobilen Apps müssen eine gültige Anwendungsverbindungszeichenfolge für die Autorisierung verwenden.
- Sie können Hybridverbindungen skalieren, indem Sie eine andere Instanz von Hybrid Connection Manager auf einem anderen Server installieren. Konfigurieren Sie den lokalen Listener so, dass er die gleiche Adresse wie der erste lokale Listener verwenden. In diesem Fall wird der Datenverkehr zufällig (Roundrobin) zwischen den aktiven lokalen Listenern verteilt. 


## <a name="ManageHybridConnection"></a>Verwalten von Hybridverbindungen
Zum Verwalten der Hybridverbindungen haben Sie folgende Möglichkeiten:

- Verwenden Sie das Azure-Portal und gehen Sie zu Ihrem BizTalk Service. 
- Verwenden Sie [REST-APIs](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### Kopieren Sie die Hybridverbindungszeichenfolgen oder generieren Sie diese erneut.

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus. 
3. Wählen Sie die Registerkarte **Hybridverbindungen** aus: <br/> ![Registerkarte "Hybridverbindungen"][HybridConnectionTab]
4. Wählen Sie die Hybridverbindung aus. Wählen Sie in der Taskleiste **Verbindung verwalten** aus: <br/> ![Verwalten von Optionen][HCManageConnection] <br/> **Verbindung verwalten** listet die Anwendungs- und lokalen Verbindungszeichenfolgen auf. Sie können die Verbindungszeichenfolgen kopieren oder den Zugriffsschlüssel in der Verbindungszeichenfolge regenerieren. <br/> <br/> **Wenn Sie "Regenerieren" auswählen**, verändert sich der freigegebene Zugriffsschlüssel innerhalb der Verbindungszeichenfolge. Gehen Sie wie folgt vor:
- Wählen Sie im Azure-Verwaltungsportal die Option **Synchronisierungsschlüssel** in der Azure-Anwendung aus.
- Führen Sie das **lokale Setup** erneut aus. Wenn Sie das lokale Setup erneut ausführen, wird die lokale Ressource automatisch für die Verwendung der aktualisierten primären Verbindungszeichenfolge konfiguriert.


#### Verwenden Sie Gruppenrichtlinien, um die von einer Hybridverbindung verwendeten lokalen Ressourcen zu steuern.

1. Laden Sie die [administrativen Vorlagen des Hybrid Connection Manager](http://www.microsoft.com/download/details.aspx?id=42963) herunter.
2. Extrahieren Sie die Dateien.
3. Gehen Sie auf dem Computer, der die Gruppenrichtlinie ändert, wie folgt vor: 

	- Kopieren Sie die ADMX-Dateien in den Ordner *%WINROOT%\PolicyDefinitions*.
	- Kopieren Sie die ADML-Dateien in den Ordner *%WINROOT%\PolicyDefinitions\de-de*.

Nach dem Kopieren können Sie den Gruppenrichtlinien-Editor verwenden, um die Richtlinie zu ändern.




## Weiter

[Verbinden von Azure-Web-Apps mit einer lokalen Ressource](../web-sites-hybrid-connection-get-started.md)<br/> [Verbinden mit einem lokalen SQL-Server über Azure-Web-Apps](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Azure Mobile Services und Hybridverbindungen](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)<br/> [Übersicht über Hybridverbindungen](integration-hybrid-connection-overview.md)


## Weitere Informationen

[REST-API zum Verwalten von BizTalk Services auf Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)<br/> [BizTalk Services: Editionsübersicht](biztalk-editions-feature-chart.md)<br/> [Erstellen eines BizTalk Service mit dem Azure-Verwaltungsportal](biztalk-provision-services.md)<br/> [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](biztalk-dashboard-monitor-scale-tabs.md)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=July15_HO4-->
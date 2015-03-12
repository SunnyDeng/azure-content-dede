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
	ms.date="02/17/2015" 
	ms.author="mandia"/>


#Erstellen und Verwalten von Hybridverbindungen

In diesem Thema werden die Schritte zum Erstellen und Verwalten von Azure-Hybridverbindungen aufgeführt. 

Folgende Schritte sind für Verbindungen zu einer lokalen Ressource erforderlich:

1. [Erstellen Sie eine Hybridverbindung](#CreateHybridConnection), indem Sie den Hostnamen oder die IP-Adresse der lokalen Ressource im privaten Netzwerk angeben.

2.	[Verknüpfen Sie die Azure-Website bzw. den Azure Mobile Service](#LinkWebSite) mit der Hybridverbindung.

3. [Installieren Sie den Hybrid Connection Manager](#InstallHCM) auf der lokalen Ressource, und stellen Sie eine Verbindung zur betreffenden Hybridverbindung her. Das Azure-Portal stellt ein Ein-Klick-Verfahren für die Installation und Verbindung bereit.

4. [Verwalten Sie Hybridverbindungen](#ManageHybridConnection) und deren Verbindungsschlüssel.


##<a name="CreateHybridConnection"></a>Erstellen einer Hybridverbindung

Eine Hybridverbindung kann im Azure-Verwaltungsportal anhand von Websites **oder** BizTalk Services erstellt werden. 

**Informationen zum Erstellen von Hybridverbindungen mit Websites** finden Sie unter [Verbinden einer Azure-Website mit einer lokalen Ressource](http://go.microsoft.com/fwlink/p/?LinkId=397538).

**So erstellen Sie Hybridverbindungen in BizTalk Services**:

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus. 
<br/>Wenn Sie keinen vorhandenen BizTalk Service haben, können Sie einen [BizTalk Service erstellen](http://go.microsoft.com/fwlink/p/?LinkID=329870).
3. Wählen Sie die Registerkarte Hybrid-Verbindungen:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]

4. Wählen Sie **Hybridverbindung erstellen** aus, oder klicken Sie in der Taskleiste auf die Schaltfläche **HINZUFÜGEN**. Geben Sie Folgendes ein:

	<table border="1">
    <tr>
       <td><strong>Name</strong></td>
        <td>Der Name der Hybridverbindung muss eindeutig sein, und es darf sich nicht um den Namen des BizTalk Service handeln. Sie können jeden beliebigen Namen eingeben; er sollte aber auf den Zweck hinweisen. Beispiele:<br/><br/>
		Gehaltsliste<em>SQLServer</em><br/>
		Lieferliste<em>SharepointServer</em><br/>
		Kunden<em>OracleServer</em>
        </td>
    </tr>
    <tr>
        <td><strong>Hostname</strong></td>
        <td>Geben Sie den vollständig qualifizierten Hostnamen, nur den Hostnamen oder die IPv4-Adresse der lokalen Ressource ein. Beispiele:
        <br/><br/>
<em>mySQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domain</em>.corp.<em>yourCompany</em>.com
<br/>
<em>myHTTPSharePointServer</em>
<br/>
<em>myHTTPSharePointServer</em>.<em>yourCompany</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Port</strong></td>
        <td>Geben Sie die Portnummer der lokalen Ressource ein. Zum Beisiel Port 80 oder Port 443, wenn Sie eine Website verwenden. Wenn Sie SQL Server verwenden, geben Sie Port 1433 ein.</td>
	</tr>
	</table>


5. Aktivieren Sie das Kontrollkästchen. 

####Zusätzlich

- Es können mehrere Hybridverbindungen erstellt werden. Siehe [BizTalk Services: Editionendiagramm](http://go.microsoft.com/fwlink/p/?LinkID=302279) für die Anzahl der zulässigen Verbindungen. 
- Jede Hybridverbindung wird mit einem Paar Verbindungszeichenfolgen erstellt: Anwendungsschlüssel, die SENDEN und lokale Schlüssel, die ABHÖREN. Jedes Paar hat einen primären und einen sekundären Schlüssel. 


##<a name="LinkWebSite"></a>Verknüpfen der Azure-Website oder des Azure Mobile Service

Um die Azure-Website mit einer vorhandenen Hybridverbindung zu verknüpfen, wählen Sie im Fenster "Hybridverbindungen" die Option vorhandene **Hybridverbindung verwenden** aus. Weitere Informationen finden Sie unter [Verbinden einer Azure-Website mit einer lokalen Ressource](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Um den Azure Mobile Service mit einer vorhandenen Hybridverbindung zu verknüpfen, wählen Sie die Option **Hybridverbindung hinzufügen**, wenn Sie einen Mobildienst ändern oder erstellen. Weitere Informationen finden Sie unter [Azure Mobile Services und Hybridverbindungen](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).


##<a name="InstallHCM"></a>Lokale Installation des Hybrid Connection Manager

Nachdem eine Hybridverbindung erstellt wurde, installieren Sie den Hybrid Connection Manager auf der lokalen Ressource. Er kann von der Azure-Website oder von BizTalk Service heruntergeladen werden. Schritte für BizTalk Services: 

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus. 
3. Wählen Sie die Registerkarte **Hybrid-Verbindungen**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Wählen Sie auf der Taskleiste **Lokaler Setup** aus:
<br/>
![On-Premises Setup][HCOnPremSetup]
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

- Der Hybrid Connection Manager muss eine gültige lokale Verbindungszeichenfolge für die Autorisierung verwenden. Die Azure-Website bzw. der Mobildienst muss eine gültige Anwendungsverbindungszeichenfolge für die Autorisierung verwenden.


##<a name="ManageHybridConnection"></a>Verwalten von Hybridverbindungen
Zum Verwalten der Hybridverbindungen haben Sie folgende Möglichkeiten:

- Verwenden Sie das Azure-Portal und gehen Sie zu Ihrem BizTalk Service. 
- Verwenden Sie [REST APIs](http://msdn.microsoft.com/library/azure/dn232347.aspx).

####Kopieren Sie die Hybridverbindungszeichenfolgen oder generieren Sie diese erneut.

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2. Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus. 
3. Wählen Sie die Registerkarte **Hybrid-Verbindungen**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Wählen Sie die Hybridverbindung aus. Wählen Sie auf der Taskleiste **Verbindung verwalten** aus.
<br/>
![Manage Options][HCManageConnection]
<br/>
**Verbindungen verwalten** führt die Anwendung und die Vor-Ort-Verbindungszeichenfolgen auf. Sie können die Verbindungszeichenfolgen kopieren oder den Zugriffsschlüssel in der Verbindungszeichenfolge regenerieren. 
<br/>
<br/>
**Wenn Sie Regenerieren** auswählen, verändert sich der gemeinsame Zugriffsschlüssel innerhalb der Verbindungszeichenfolge. Gehen Sie wie folgt vor:
- Wählen Sie im Azure-Verwaltungsportal die Option **Synchronisierungsschlüssel** in der Azure-Anwendung.
- Führen Sie das **lokale Setup** erneut aus. Wenn Sie das lokale Setup erneut ausführen, wird die lokale Ressource automatisch für die Verwendung der aktualisierten primären Verbindungszeichenfolge konfiguriert.


####Verwenden Sie Gruppenrichtlinien, um die von einer Hybridverbindung verwendeten lokalen Ressourcen zu steuern.

1. Laden Sie die administrativen Vorlagen des Hybrid Connection Manager herunter.
2. Extrahieren Sie die Dateien.
3. Gehen Sie auf dem Computer, der die Gruppenrichtlinie ändert, wie folgt vor: 
- Kopieren Sie die ADMX-Dateien in den Ordner *%WINROOT%\PolicyDefinitions*.
- Kopieren Sie die ADML-Dateien in den Ordner *%WINROOT%\PolicyDefinitions\de-de*.

Nach dem Kopieren können Sie den Gruppenrichtlinien-Editor verwenden, um die Richtlinie zu ändern.




## Weiter

- [Verbinden einer Azure-Website mit einer lokalen Ressource](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Azure Mobile Services und Hybridverbindungen](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)
- [Überblick über Hybridverbindungen](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-overview)


## Weitere Informationen

- [REST API für die Verwaltung von BizTalk Services unter Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Erstellen eines BizTalk Service mit Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!--HONumber=46--> 

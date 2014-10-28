<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Create and Manage Hybrid Connections - BizTalk Services" pageTitle="Create and Manage Hybrid Connections | Azure" metaKeywords="BizTalk Services, BizTalk, web sites, website, hybrid connections, Azure" description="Learn how to create a hybrid connection, manage the connection, and install the Hybrid Connection Manager." metaCanonical="" services="integration-services" documentationCenter="" title="Create and Manage Hybrid Connections" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia"></tags>

# Erstellen und Verwalten von Hybridverbindungen

In diesem Thema werden die Schritte zum Erstellen und Verwalten von Azure-Hybridverbindungen aufgeführt.

Folgende Schritte sind für Verbindungen zu einer lokalen Ressource erforderlich:

1.  [Erstellen Sie eine Hybridverbindung][Erstellen Sie eine Hybridverbindung], indem Sie den Hostnamen oder die IP-Adresse der lokalen Ressource im privaten Netzwerk angeben.

2.  [Verknüpfen Sie die Azure-Website bzw. den Azure Mobile Service][Verknüpfen Sie die Azure-Website bzw. den Azure Mobile Service] mit der Hybridverbindung.

3.  [Installieren Sie den Hybrid Connection Manager][Installieren Sie den Hybrid Connection Manager] auf der lokalen Ressource, und stellen Sie eine Verbindung zur betreffenden Hybridverbindung her. Das Azure-Portal stellt ein Ein-Klick-Verfahren für die Installation und Verbindung bereit.

4.  [Verwalten Sie Hybridverbindungen][Verwalten Sie Hybridverbindungen] und deren Verbindungsschlüssel.

## <a name="CreateHybridConnection"></a>Erstellen einer Hybridverbindung

Eine Hybridverbindung kann im Azure-Verwaltungsportal anhand von Websites **oder** BizTalk Services erstellt werden.

**Informationen zum Erstellen von Hybridverbindungen mit Websites** finden Sie unter [Verbinden einer Azure-Website mit einer lokalen Ressource][Verbinden einer Azure-Website mit einer lokalen Ressource].

**So erstellen Sie Hybridverbindungen in BizTalk Services**:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Wählen Sie im linken Navigationsbereich **BizTalk Services** aus, und wählen Sie Ihren BizTalk Service aus.
    Wenn Sie nicht über einen vorhandenen BizTalk Service verfügen, können Sie einen [BizTalk Service erstellen][BizTalk Service erstellen].
3.  Klicken Sie auf die Registerkarte „Hybridverbindungen“:
    ![Registerkarte „Hybridverbindungen“][Registerkarte „Hybridverbindungen“]

4.  Wählen Sie **Hybridverbindung erstellen** aus, oder klicken Sie auf die Schaltfläche **HINZUFÜGEN** in der Taskleiste. Geben Sie Folgendes ein:

    |--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Name**     | Der Name der Hybridverbindung muss eindeutig sein, und es darf sich nicht um den Namen des BizTalk Service handeln. Sie können jeden beliebigen Namen eingeben; er sollte aber auf den Zweck hinweisen. Beispiele: 
                     Gehaltsliste*SQLServer*                                                                                                                                                                                            
                     Lieferliste*SharepointServer*                                                                                                                                                                                      
                     Kunden*OracleServer*                                                                                                                                                                                               |
    | **Hostname** | Geben Sie den vollständig qualifizierten Hostnamen, nur den Hostnamen oder die IPv4-Adresse der lokalen Ressource ein. Beispiele:                                                                                  
                     *mySQLServer*                                                                                                                                                                                                      
                     *mySQLServer*.*Domäne*.corp.*IhrUnternehmen*.com                                                                                                                                                                   
                     *myHTTPSharePointServer*                                                                                                                                                                                           
                     *myHTTPSharePointServer*.*IhrUnternehmen*.com                                                                                                                                                                      
                     10.100.10.10                                                                                                                                                                                                       |
    | **Port**     | Geben Sie die Portnummer der lokalen Ressource ein. Beispiel: Wenn Sie eine Website verwenden, geben Sie Port 80 oder Port 443 ein. Wenn Sie SQL Server verwenden, geben Sie Port 1433 ein.                        |

5.  Aktivieren Sie das Kontrollkästchen.

#### Zusätzlich

-   Es können mehrere Hybridverbindungen erstellt werden. Unter [BizTalk Services: Editionsübersicht][BizTalk Services: Editionsübersicht] finden Sie die Anzahl der zulässigen Verbindungen.
-   Jede Hybridverbindung wird mit einem Paar Verbindungszeichenfolgen erstellt: Anwendungsschlüssel, die SENDEN und lokale Schlüssel, die ABHÖREN. Jedes Paar hat einen primären und einen sekundären Schlüssel.

## <a name="LinkWebSite"></a>Verknüpfen der Azure-Website oder des Azure Mobile Service

Um die Azure-Website mit einer vorhandene Hybridverbindung zu verknüpfen, wählen Sie im Fenster „Hybridverbindungen“ die Option **vorhandene Hybridverbindung verwenden** aus. Weitere Informationen finden Sie unter [Verbinden einer Azure-Website mit einer lokalen Ressource][Verbinden einer Azure-Website mit einer lokalen Ressource].

Um den Azure Mobile Service mit einer vorhandenen Hybridverbindung zu verknüpfen, wählen Sie die Option **Hybridverbindung hinzufügen**, wenn Sie einen Mobildienst ändern oder erstellen. Weitere Informationen finden Sie unter [Azure Mobile Services und Hybridverbindungen][Azure Mobile Services und Hybridverbindungen].

## <a name="InstallHCM"></a>Lokale Installation des Hybrid Connection Manager

Nachdem eine Hybridverbindung erstellt wurde, installieren Sie den Hybrid Connection Manager auf der lokalen Ressource. Er kann von der Azure-Website oder von BizTalk Service heruntergeladen werden. Schritte für BizTalk Services:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus.
3.  Klicken Sie auf die Registerkarte **Hybridverbindungen**:
    ![Registerkarte „Hybridverbindungen“][Registerkarte „Hybridverbindungen“]
4.  Wählen Sie in der Taskleiste die Option **Lokales Setup** aus:
    ![Lokales Setup][Lokales Setup]
5.  Wählen Sie **Installieren und konfigurieren** aus, um den Hybrid Connection Manager auf dem lokalen System herunterzuladen oder auszuführen.
6.  Wählen Sie das Häkchen, um die Installation zu starten.

<!-- You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:  1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps. 2. Download the Hybrid Connection Manager MSI file.  3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file.  4. Using Windows PowerShell, type:  > Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*”  -->

#### Zusätzlich

-   Hybridverbindungen unterstützen lokale Ressourcen, die unter den folgenden Betriebssystemen installiert sind:

    -   Windows Server 2008 R2
    -   Windows Server 2012
    -   Windows Server 2012 R2
-   Nachdem Sie den Hybrid Connection Manager installiert haben, geschieht Folgendes:

    -   Die auf Azure gehostete Hybridverbindung wird automatisch so konfiguriert, dass sie die primäre Anwendungsverbindungszeichenfolge verwendet.
    -   Die lokale Ressource wird automatisch so konfiguriert, dass sie die primäre lokale Verbindungszeichenfolge verwendet.
-   Der Hybrid Connection Manager muss eine gültige lokale Verbindungszeichenfolge für die Autorisierung verwenden. Die Azure-Website bzw. der Mobildienst muss eine gültige Anwendungsverbindungszeichenfolge für die Autorisierung verwenden.

## <a name="ManageHybridConnection"></a>Verwalten von Hybridverbindungen

Zum Verwalten der Hybridverbindungen haben Sie folgende Möglichkeiten:

-   Verwenden Sie das Azure-Portal und gehen Sie zu Ihrem BizTalk Service.
-   Verwenden Sie [REST APIs][REST APIs].
    <!-- - Use Windows PowerShell cmdlets  **INSERT LINK**. -->

#### Kopieren Sie die Hybridverbindungszeichenfolgen oder generieren Sie diese erneut.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Wählen Sie im linken Navigationsbereich die Option **BizTalk Services** aus, und wählen Sie dann Ihren BizTalk Service aus.
3.  Klicken Sie auf die Registerkarte **Hybridverbindungen**:
    ![Registerkarte „Hybridverbindungen“][Registerkarte „Hybridverbindungen“]
4.  Wählen Sie die Hybridverbindung aus. Wählen Sie in der Taskleiste **Verbindung verwalten** aus:
    ![Optionen verwalten][Optionen verwalten]
    **Verbindung verwalten** listet die Anwendungs- und lokalen Verbindungszeichenfolgen auf. Sie können die Verbindungszeichenfolgen kopieren oder den in der Verbindungszeichenfolge verwendeten Zugriffsschlüssel erneut generieren.
    **Wenn Sie erneutes Generieren wählen**, wird der freigegebene Zugriffsschlüssel, der in der Verbindungszeichenfolge verwendet wird, geändert. Gehen Sie wie folgt vor:

-   Wählen Sie im Azure-Verwaltungsportal die Option **Synchronisierungsschlüssel** in der Azure-Anwendung.
-   Führen Sie das **lokale Setup** erneut aus. Wenn Sie das lokale Setup erneut ausführen, wird die lokale Ressource automatisch für die Verwendung der aktualisierten primären Verbindungszeichenfolge konfiguriert.

#### Verwenden Sie Gruppenrichtlinien, um die von einer Hybridverbindung verwendeten lokalen Ressourcen zu steuern.

1.  Laden Sie die administrativen Vorlagen des Hybrid Connection Manager herunter.
2.  Extrahieren Sie die Dateien.
3.  Gehen Sie auf dem Computer, der die Gruppenrichtlinie ändert, wie folgt vor:

-   Kopieren Sie die ADMX-Dateien in den Ordner *%WINROOT%\\PolicyDefinitions*.
-   Kopieren Sie die ADML-Dateien in den Ordner *%WINROOT%\\PolicyDefinitions\\de-de*.

Nach dem Kopieren können Sie den Gruppenrichtlinien-Editor verwenden, um die Richtlinie zu ändern.

## Weiter

-   [Verbinden einer Azure-Website mit einer lokalen Ressource][Verbinden einer Azure-Website mit einer lokalen Ressource]
-   [Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus][Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus]
-   [Azure Mobile Services und Hybridverbindungen][Azure Mobile Services und Hybridverbindungen]
-   [Überblick über Hybridverbindungen][Überblick über Hybridverbindungen]

## Weitere Informationen

-   [REST API für die Verwaltung von BizTalk Services unter Microsoft Azure][REST APIs]
-   [BizTalk Services: Editionsübersicht][BizTalk Services: Editionsübersicht]
-   [Erstellen eines BizTalk Service mit Azure-Verwaltungsportal][Erstellen eines BizTalk Service mit Azure-Verwaltungsportal]
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"][BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]

  [Erstellen Sie eine Hybridverbindung]: #CreateHybridConnection
  [Verknüpfen Sie die Azure-Website bzw. den Azure Mobile Service]: #LinkWebSite
  [Installieren Sie den Hybrid Connection Manager]: #InstallHCM
  [Verwalten Sie Hybridverbindungen]: #ManageHybridConnection
  [Verbinden einer Azure-Website mit einer lokalen Ressource]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [BizTalk Service erstellen]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Registerkarte „Hybridverbindungen“]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
  [BizTalk Services: Editionsübersicht]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Azure Mobile Services und Hybridverbindungen]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [Lokales Setup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
  [REST APIs]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [Optionen verwalten]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
  [Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Überblick über Hybridverbindungen]: http://azure.microsoft.com/de-de/documentation/articles/integration-hybrid-connection-overview
  [Erstellen eines BizTalk Service mit Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"]: http://go.microsoft.com/fwlink/p/?LinkID=302281

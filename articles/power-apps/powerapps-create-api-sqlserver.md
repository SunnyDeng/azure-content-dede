<properties
	pageTitle="Hinzufügen der SQL Server-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen SQL Server-API in der App Service-Umgebung Ihrer Organisation und Hinzufügen einer Verbindung mit lokalen Daten"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>


# Erstellen einer neuen SQL Server-API in PowerApps Enterprise

Fügen Sie die SQL Server-API in der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet. 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][14]  
3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]  
4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus.
5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzuzufügen.
6. Geben Sie einen beschreibenden **Namen** für Ihre API ein. Wenn Sie beispielsweise die SQL Server-API zu Demonstrationszwecken hinzufügen, können Sie den Namen *SQLServerDemo* verwenden.  	
7. Wählen Sie in **Source** die Option **Available APIs**, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **SQL Server** aus. 
8. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue SQL Server-API hinzugefügt.

## Konfigurieren der lokalen Verbindung mit SQL Server

Sie können lokal eine Verbindung mit SQL Server herstellen. Zum Herstellen dieser Hybridverbindung können Sie vorhandene Hybridnetzwerklösungen in Azure verwenden, z. B.:

- [ExpressRoute](../expressroute/expressroute-introduction.md)
- [Standort-zu-Standort-VPN-Verbindung](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Punkt-zu-Standort-Verbindung](../vpn-gateway/vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]  Jeder App Service-Umgebung ist ein virtuelles Netzwerk zugeordnet. Sie können die Netzwerkverbindung mit diesem virtuellen Netzwerk herstellen.  
- [Hybridverbindungen](../app-service-web/web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]  Jede registrierte API in Ihrer App Service-Umgebung verfügt über eine entsprechende Web-App. Über diese Web-App können Sie genau wie über jede andere Web-App Hybridverbindungen herstellen.
	
Im folgenden Beispiel wird veranschaulicht, wie eine Hybridverbindung hergestellt wird:

1. Wählen Sie die soeben erstellte SQL Server-API und die Ressourcengruppe aus. Wählen Sie in diesem Beispiel die API *sqlconnectordemo* und die Ressourcengruppe *DedicatedAses* aus: ![Ressourcengruppe](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  Wählen Sie die Kachel **Ressourcen** und dann die Web-App aus, deren Namen dem Ihrer SQL Server-API entspricht. Wählen Sie in diesem Beispiel die Web-App *sqlconnectordemo* aus: ![SQL-Web-App](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  Wählen Sie in **Einstellungen** die Option **Netzwerk** aus. Wählen Sie **Configure your hybrid connection endpoints** aus, und befolgen Sie dann [diese Anweisungen](../app-service-web/web-sites-hybrid-connection-get-started.md) zum Erstellen der Hybridverbindung: ![Netzwerk](./media/powerapps-create-api-sqlserver/network.png)

Nachdem die Hybridverbindung erstellt und hergestellt wurde, haben Sie die Verbindung mit dem lokalen Server aktiviert. Erstellen Sie als Nächstes die Verbindung mit Ihren Daten, und richten Sie den Zugriff für Benutzer ein: ![Hybridverbindung](./media/powerapps-create-api-sqlserver/hybridconn.png)

## Erstellen der Verbindung für die SQL Server-API

1. Öffnen Sie PowerApps im Azure-Portal, und wählen Sie **Manage APIs** aus. Eine Liste der konfigurierten APIs wird angezeigt: ![](./media/powerapps-create-api-sqlserver/apilist.png)

2. Wählen Sie die gewünschte API aus. Wählen Sie in diesem Beispiel **SQLServerDemo** und dann **Verbindungen** aus.

3. Wählen Sie in „Verbindungen“ die Option **Verbindung hinzufügen** aus: ![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. Geben Sie einen Namen für die Verbindung und die Verbindungszeichenfolge ein. Für die Eingabe der Verbindungszeichenfolge müssen Sie einige spezifische Eigenschaften für den Dienst kennen, mit dem Sie eine Verbindung herstellen. Wenn Sie z. B. lokal eine Verbindung mit SQL Server herstellen, müssen Sie den Benutzernamen, das Kennwort und andere Eigenschaften kennen, die zum Herstellen der Verbindung erforderlich sind.

5. Klicken Sie zum Speichern der Änderungen auf **Hinzufügen**.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die SQL Server-API hinzugefügt, um lokal eine Verbindung mit SQL Server herzustellen. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0309_2016-->
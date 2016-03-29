<properties
	pageTitle="Hinzufügen der Microsoft Translator-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Microsoft Translator-API in der App Service-Umgebung Ihrer Organisation"
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

# Erstellen einer neuen Microsoft Translator-API in PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Fügen Sie die Microsoft Translator-API in der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**:  
![][7]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben:  
![][8]  

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus:  
![Zu registrierten APIs navigieren][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen:  
![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs**, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Microsoft Translator** aus:  
![Microsoft Translator-API auswählen][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus:  
![Microsoft Translator-API-Einstellungen konfigurieren][4]

9. Geben Sie die *Client-Id* und den *geheimen Clientschlüssel* Ihrer Microsoft Translator-Anwendung ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer Microsoft Translator-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für die ID und den geheimen Schlüssel.

9. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Microsoft Translator-API hinzugefügt.


## Optional: Registrieren einer Microsoft Translator-App zur Verwendung mit PowerApps

Wenn Sie über keine vorhandene Microsoft Translator-App mit den Werten für die ID und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.


1. Wechseln Sie zur [Azure Data Market-Entwicklerseite][5] und melden Sie sich mit Ihrem Microsoft-Konto an. 

2. Wählen Sie **Registrieren**.

3. Unter **Anwendung registrieren**:  

	1. Geben Sie einen Wert für **Client-ID** ein.  
	2. Geben Sie den **Namen** Ihrer Anwendung ein.  
	3. Geben Sie einen Dummywert für **Umleitungs-URL** ein. Geben Sie z. B. Folgendes ein: *https://contosoredirecturl*.  
	4. Geben Sie eine **Beschreibung** ein.  
	5. Klicken Sie auf **Erstellen**.  

	![Anwendung registrieren][6]

Eine neue Microsoft Translator-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Microsoft Translator-API im Azure-Portal verwenden.

## Informationen zu REST-APIs

Referenz zur [Microsoft Translator-REST-API](../connectors/create-api-microsofttranslator.md).

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die Microsoft Translator-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, sodass sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!----HONumber=AcomDC_0309_2016-->



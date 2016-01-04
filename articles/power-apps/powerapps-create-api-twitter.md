<properties
	pageTitle="Hinzufügen der Twitter-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Twitter-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Erstellen einer neuen Twitter-API in der App Service-Umgebung Ihrer Organisation

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet. 

2. Wählen Sie auf der Taskleiste die Option **Durchsuchen** aus: ![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Navigieren zu registrierten APIs][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzuzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs** aus , um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Twitter** aus: ![Auswählen der Twitter-API][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Konfigurieren der Einstellungen für die Twitter-API][4]

9. Geben Sie den *Verbraucherschlüssel* und den *geheimen Verbraucherschlüssel* Ihrer Twitter-Anwendung ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer Twitter-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für den Schlüssel und den geheimen Schlüssel.

	> [AZURE.IMPORTANT]Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Twitter-API hinzugefügt.


## Optional: Registrieren einer Twitter-App zur Verwendung mit PowerApps

Wenn Sie über keine vorhandene Twitter-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Rufen Sie [https://apps.twitter.com/](https://apps.twitter.com) auf, und melden Sie sich mit Ihrem Twitter-Konto an.

2. Klicken Sie auf **Create New App**: ![Seite für Twitter-Apps][6]

3. In **Create an application**:
   
	a) Geben Sie unter **Name** einen Namen ein. b) Geben Sie unter **Description** eine Beschreibung ein. c) Geben Sie unter **Website** eine Website ein. d) Legen Sie die **Callback URL** auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen Twitter-API im Azure-Portal erhalten haben (weiter oben in diesem Thema). e) Akzeptieren Sie die Entwicklervereinbarung, und wählen Sie **Create your Twitter application** aus.

	![Erstellen der Twitter-App][7]

4. Nach der erfolgreichen Erstellung der App werden Sie zur App-Seite weitergeleitet.

Eine neue Twitter-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Twitter-API im Azure-Portal verwenden.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die Twitter-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->
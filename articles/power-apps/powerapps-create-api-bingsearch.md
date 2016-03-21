<properties
	pageTitle="Die Bing-Suche-API zu PowerApps Enterprise hinzufügen | Microsoft Azure"
	description="Erstellen einer neue Bing-Suche-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Erstellen einer neuen Bing-Such-API in PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)

Fügen Sie die Bing-Such-API in der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][4]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][5]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Navigieren zu registrierten APIs][2]

2. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen: ![API hinzufügen][3]

3. Geben Sie einen beschreibenden **Namen** für Ihre API ein.

4. Wählen Sie in **Quelle** die Option **Verfügbare API**, um die vorgefertigten APIs auszuwählen und wählen Sie **Bing-Suche**:

	1. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus.
	2. Geben Sie den *Kontoschlüssel* ein. Wenn Sie keinen Bing-Suche-Schlüssel haben, erstellen Sie ein kostenloses [Bing-Suche-Angebot][1], um einen Schlüssel zu erhalten.
	3. Klicken Sie auf **OK**. 

5. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Abschließend wird in Ihrer App Service-Umgebung eine neue Bing-Such-API hinzugefügt.

## Informationen zu REST-APIs

Referenz zur [REST-API für die Bing-Suche](../connectors/create-api-bingsearch.md).


## Zusammenfassung und nächste Schritte
Mithilfe dieses Themas haben Sie die Bing-Suche-API zu PowerApps Enterprise hinzugefügt. Als nächstes können Sie Benutzern Zugriff auf die API verschaffen, sodass sie zu den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0309_2016-->
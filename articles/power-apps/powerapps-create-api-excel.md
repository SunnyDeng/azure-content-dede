<properties
	pageTitle="Hinzufügen der Excel-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Excel-API in der App Service-Umgebung Ihrer Organisation"
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

# Erstellen einer neuen Excel-API in der App Service-Umgebung Ihrer Organisation

## Erstellen der API im Azure-Portal

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][4]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][5]

4. Wählen Sie in **PowerApps** die Option **APIs verwalten**: ![Navigieren zu registrierten APIs][1]

5. Wählen Sie in **APIs verwalten** die Option **Hinzufügen**, um die neue API hinzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Quelle** die Option **Verfügbare APIs** wählen Sie die vorgefertigten APIs, und wählen Sie **Excel**: ![Excel-API auswählen][3]

8. Wählen Sie **OK**, um die Schritte ausführen.

Abschließend wird Ihrer App Service-Umgebung eine neue Excel-API hinzugefügt.

## Zusammenfassung und nächste Schritte
Mithilfe dieses Thema haben Sie die Excel-API zu PowersApps Enterprise hinzugefügt. Als nächstes können Sie Benutzern Zugriff auf die API verschaffen, sodass sie zu den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)



<!--References-->
[1]: ./media/powerapps-create-api-excel/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-excel/add-api.PNG
[3]: ./media/powerapps-create-api-excel/select-excel-api.PNG
[4]: ./media/powerapps-create-api-excel/browseall.png
[5]: ./media/powerapps-create-api-excel/allresources.png

<!---HONumber=AcomDC_1203_2015-->
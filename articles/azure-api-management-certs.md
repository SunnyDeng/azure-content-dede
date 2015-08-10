<properties 
	pageTitle="Hochladen eines API-Zertifikats für die Microsoft Azure-Verwaltung in das Portal" 
	description="Erfahren Sie, wie ein API-Verwaltungszertifikat in Microsoft Azure hochgeladen wird" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="na" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2015"
	ms.author="adegeo"/>


# Hochladen eines Verwaltungszertifikats für die Azure-Verwaltung-API

Mit Verwaltungszertifikaten können Sie die Authentifizierung mit der in Azure bereitgestellten Dienstverwaltungs-API durchführen. Diese Zertifikate werden in vielen Programmen und Tools \(z. B. Visual Studio oder Azure SDK\) zum Automatisieren der Konfiguration und Bereitstellung verschiedener Azure-Dienste verwendet. Sie stehen nicht wirklich in Zusammenhang mit Clouddiensten.

>[AZURE.WARNING]Vorsicht ist geboten! Alle Personen, die die Authentifizierung mit diesen Zertifikaten durchführen, können das zugeordnete Abonnement verwalten.

Weitere Informationen zu Azure-Zertifikaten \(einschließlich der Erstellung eines selbstsignierten Zertifikats\) sind bei Bedarf [verfügbar](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Sie können auch [Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/) für die Authentifizierung von Clientcode zur Automatisierung verwenden.

## Hochladen eines Verwaltungszertifikats

Sobald Sie ein Verwaltungszertifikat erstellt haben \(CER-Datei nur mit dem öffentlichen Schlüssel\), können Sie es in das Portal hochladen. Wenn das Zertifikat im Portal verfügbar ist, kann jeder Benutzer mit einem entsprechenden Zertifikat \(privater Schlüssel\) über die Verwaltungs-API eine Verbindung herstellen und auf die Ressourcen für das zugehörige Abonnement zugreifen.

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.
2. Klicken Sie auf der linken Seite des Portals auf **Einstellungen** \(möglicherweise müssen Sie einen Bildlauf durchführen\). 
    
    ![Einstellungen](./media/azure-api-management-certs/settings.png)

3. Klicken Sie auf die Registerkarte **Verwaltungszertifikate**.

    ![Einstellungen](./media/azure-api-management-certs/certificates-tab.png)
    
4. Klicken Sie auf die Schaltfläche **Upload**.

    ![Einstellungen](./media/azure-api-management-certs/upload.png)
    
5. Füllen Sie das Dialogfeld mit den Informationen aus, und klicken Sie zum Abschluss auf das **Häkchen**.

    ![Einstellungen](./media/azure-api-management-certs/upload-dialog.png)

## Nächste Schritte

Da Sie jetzt einem Abonnement ein Verwaltungszertifikat zugeordnet haben, können Sie \(nachdem Sie das entsprechende Zertifikat lokal installiert haben\) programmgesteuert eine Verbindung mit der [Dienstverwaltungs-REST-API](https://msdn.microsoft.com/library/azure/ee460799.aspx) herstellen und die verschiedenen Azure-Ressourcen automatisieren, die auch diesem Abonnement zugeordnet sind.

<!---HONumber=July15_HO5-->
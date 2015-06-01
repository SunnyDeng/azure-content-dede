<properties 
	pageTitle="Erstellen eines API-App-Pakets" 
	description="Erfahren Sie, wie Sie ein API-App-Paket im Azure Marketplace veröffentlichen." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Veröffentlichen eines API-App-Pakets im Azure Marketplace

## Übersicht

Dieser Artikel zeigt, wie Sie ein API-App-Paket im [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) veröffentlichen.

- Informationen zum Erstellen einer API-App finden Sie unter [Erstellen einer API-App mit Visual Studio](app-service-dotnet-create-api-app.md)
- Informationen zum Erstellen eines API-App-Pakets finden Sie unter [Erstellen eines API-App-Pakets](app-service-api-create-package).

## Allgemeiner Ablauf bei der Veröffentlichung

Nachfolgend werden die allgemeinen Schritte im Veröffentlichungsablauf aufgeführt.

1. Erstellen Sie für Ihre API-App ein Nuget-Paket, indem Sie die Anweisungen im Lernprogramm [Erstellen eines API-App-Pakets](app-service-api-create-package) befolgen.
2. Veröffentlichen Sie das Paket in einem Nuget-gestützten Katalog unter https://apiapps.nuget.org.
3. Das Paket wird anschließend automatisch mit dem [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) synchronisiert.
4. Wechseln Sie zum [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) und zum [Azure-Vorschauportal](https://portal.azure.com), um das gesamte Verhalten zu überprüfen.

## Veröffentlichen im Nuget-gestützten Katalog

1. Wechseln Sie zur Adresse https://apiapps.nuget.org.

    ![Nuget-gestützter Katalog – Startseite](./media/app-service-api-publish-package/nuget-homepage.png)

2. Klicken Sie auf **Anmelden**, und melden Sie sich mit Ihrem Azure-Konto an.
3. Klicken Sie auf die Registerkarte **API-Apps**, klicken Sie auf **API-App hochladen**, und laden Sie dann Ihr API-App-Paket hoch.

    ![Nuget-gestützter Katalog – Seite für den Paketupload](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. Für **Namespace** zeigt die Dropdownliste alle verifizierten Domänen der Azure Active Directory-Mandanten für Ihr Azure-Konto an. Stellen Sie sicher, dass Sie eine Domäne auswählen, die der namespace-Eigenschaft in der Datei "apiapp.json" Ihres API-App-Pakets entspricht. So ist gewährleistet, dass Herausgeber gültige Namespaces für ihre API-App-Pakete anfordern.
5. Stellen Sie sicher, dass **Eintragung dieser API-App aufheben** deaktiviert ist.
6. Klicken Sie auf **Veröffentlichen**.
7. Wenn ein Validierungsfehler vorliegt, beheben Sie diesen, und laden Sie das API-App-Paket erneut hoch.

## Anzeigen Ihres API-App-Pakets im Azure Marketplace

Nach einigen Minuten wird Ihr API-App-Paket mit dem Azure Marketplace synchronisiert. Sie können [hier](http://azure.microsoft.com/marketplace/api-apps/) prüfen, ob Titel, Beschreibung, Symbol usw. richtig angezeigt werden. Falls Änderungen erforderlich sind, nehmen Sie einfach Änderungen im API-App-Paket vor, und veröffentlichen Sie das Paket erneut.

![Azure Marketplace – API-App-Seite](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Bereitstellen Ihres API-App-Pakets im Azure-Vorschauportal

Sie können sich auch mit Ihrem Azure-Konto beim [Azure-Vorschauportal](https://portal.azure.com) anmelden (es kann sich um ein anderes Konto handeln als das zum Veröffentlichen des API-App-Pakets verwendete Konto). Hier können Sie den Erstellvorgang für Ihr API-App-Paket im Azure-Vorschauportal prüfen. Falls Änderungen erforderlich sind, nehmen Sie einfach Änderungen im API-App-Paket vor, und veröffentlichen Sie das Paket erneut.

Ausführliche Informationen zum Bereitstellen eines API-App-Pakets im Azure-Portal finden Sie [hier](app-service-api-connnect-your-app-to-saas-connector.md) im Bereitstellungsbeispiel für DropboxConnector.

<!--HONumber=52-->

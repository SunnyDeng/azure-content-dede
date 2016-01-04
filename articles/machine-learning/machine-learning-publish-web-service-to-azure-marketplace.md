<properties 
	pageTitle="Veröffentlichen von Machine Learning-Webdiensten im Azure Marketplace | Microsoft Azure" 
	description="Veröffentlichen Ihres Azure Machine Learning-Webdiensts im Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="bharaths"/>

# Veröffentlichen Ihres Azure Machine Learning-Webdiensts im Azure Marketplace 

Im Azure Marketplace können Sie Azure Machine Learning-Webdienste als kostenlose oder zahlungspflichtige Dienste für externe Consumer veröffentlichen. Dieser Artikel bietet eine Übersicht über diesen Vorgang sowie Hinweise zu den ersten Schritten. Mit diesem Prozess können Sie Ihre Webdienste bereitstellen, sodass andere Entwickler diese in ihren Anwendungen verwenden können.


[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Übersicht über den Veröffentlichungsprozess 

Die Veröffentlichung eines Azure Machine Learning-Webdiensts im Azure Marketplace umfasst die folgenden Schritte:

1. Erstellen und Veröffentlichen eines Machine Learning RRS (Request-Response)-Diensts
2. Stellen Sie den Dienst in der Produktionsumgebung bereit, und rufen Sie den API-Schlüssel und die OData-Endpunktdaten ab.
3. Verwenden Sie die URL des veröffentlichten Webdiensts für die Veröffentlichung im [Azure Marketplace (Datenmarkt)](https://publish.windowsazure.com/workspace/). 
4. Nach der Übermittlung wird Ihr Angebot geprüft und muss genehmigt werden, bevor Ihre Kunden das Angebot abonnieren können. Der Veröffentlichungsprozess kann einige Geschäftstage in Anspruch nehmen. 

## Exemplarische Vorgehensweise
###Schritt 1: Erstellen und Veröffentlichen eines Machine Learning RRS (Request-Response)-Diensts###
 Sofern noch nicht erfolgt, beachten Sie diese [exemplarische Vorgehensweise](machine-learning-walkthrough-5-publish-web-service.md).

###Schritt 2: Bereitstellen des Diensts in der Produktionsumgebung und Abrufen des API-Schlüssels und der OData-Endpunktdaten###
1. Wählen Sie im klassischen [Azure-Portal](http://manage.windowsazure.com) in der linken Navigationsleiste die Option **MACHINE LEARNING** und anschließend den Arbeitsbereich aus. 

2. Klicken Sie auf die Registerkarte **WEBDIENSTE**, und wählen Sie den Webdienst aus, den Sie im Marketplace veröffentlichen möchten.

	![Azure Marketplace][workspace]

3. Wählen Sie den Endpunkt aus, der im Marketplace verwendet werden soll. Wenn noch keine zusätzlichen Endpunkte erstellt wurden, können Sie den Endpunkt **Standard** auswählen.

4. Wenn Sie auf den Endpunkt klicken, wird der **API-SCHLÜSSEL** angezeigt. Sie benötigen diese Daten später in Schritt 3. Erstellen Sie also eine Kopie.

	![Azure Marketplace][apikey]

5. Klicken Sie auf die Methode **REQUEST/RESPONSE**. Derzeit wird das Veröffentlichen von Stapelausführungsdiensten im Marketplace nicht unterstützt. Sie gelangen zur API-Hilfeseite zur Methode "Request/Response".

6. Kopieren Sie die **Adresse des OData-Endpunkts**, die Sie später in Schritt 3 benötigen.

	![Azure Marketplace][odata]




Stellen Sie den Dienst in der Produktionsumgebung bereit.



###Schritt 3: Verwenden der URL des veröffentlichten Webdiensts für die Veröffentlichung im Azure Marketplace (Datenmarkt)###

1.  Navigieren Sie zum [Azure Marketplace (Datenmarkt)](http://datamarket.azure.com/home). 
2.  Klicken Sie oben auf der Seite auf den Link **Veröffentlichen**. Hierdurch gelangen Sie zum [Microsoft Azure-Veröffentlichungsportal](https://publish.windowsazure.com).
3.  Klicken Sie auf den Bereich **Herausgeber**, um sich als Herausgeber zu registrieren.
4.	Wählen Sie zur Erstellung eines neuen Angebots die Option **Datendienst** aus und klicken Sie auf **Neuen Datendienst erstellen**. 
 
	![Azure Marketplace][image1]

	<br />


5.	Geben Sie unter **Pläne** Daten zu Ihrem Angebot an, inklusive einer Preisgestaltung. Legen Sie fest, ob Sie Ihren Dienst kostenlos oder zahlungspflichtig anbieten möchten. Um Zahlungen zu erhalten, müssen Sie Zahlungsinformationen wie z. B. Ihre Bank- und Steuerdaten angeben.

6.	Geben Sie unter **Marketing** Daten zu Ihrem Angebot an, z. B. den Titel und die Beschreibung Ihres Angebots.

7.	Geben Sie unter **Preisdetails** den Preis für Ihre Pläne für bestimmte Länder ein, oder lassen Sie einen automatischen Preis für Ihr Angebot erstellen.

8. Klicken Sie auf der Registerkarte **Datendienst** die Option **Webdienst** als **Datenquelle** aus.

	![Azure Marketplace][image2]

9.	Rufen Sie die Webdienst-URL und den API-Schlüssel vom klassischen Azure-Portal aus ab (siehe Schritt 2).

10.	Fügen Sie im Setupdialogfeld des Marketplace-Datendiensts die OData-Endpunktadresse in die **Dienst-URL** ein.

11. Wählen Sie unter **Authentifizierung** die Option **Header** als **Authentifizierungsschema** aus.

	- Geben Sie als **Headername** "Authorization" ein.
	- Geben Sie als **Headerwert** "Bearer" (ohne Anführungszeichen) ein, und drücken Sie die **LEERTASTE**, um anschließend den API-Schlüssel einzufügen.
	- Aktivieren Sie das Kontrollkästchen **This Service is OData**.
	- Klicken Sie auf **Verbindung testen**, um die Verbindung zu testen.

12.	Stellen Sie unter **Kategorien** sicher, dass **Machine Learning** ausgewählt wurde.

13. Wenn alle Metadaten für Ihr Angebot eingegeben wurden, klicken Sie auf **Veröffentlichen** und dann auf **Push to Staging**. An diesem Punkt werden Sie auf alle verbleibenden Probleme hingewiesen, die Sie beheben müssen.

14. Nachdem Sie alle ausstehenden Probleme behoben haben, klicken Sie auf **Request approval to push to Production**. Der Veröffentlichungsprozess kann einige Geschäftstage in Anspruch nehmen.


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=AcomDC_1203_2015-->
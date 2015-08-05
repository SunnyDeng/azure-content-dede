<properties
	pageTitle="Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen"
	description="Erfahren Sie, wie Sie eine Azure API Management-Dienstinstanz für mehrere Azure-Regionen bereitstellen." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="sdanie"/>

# Bereitstellen einer Azure API Management-Dienstinstanz für mehrere Azure-Regionen

API Management unterstützt eine Bereitstellung für mehrere Regionen, wodurch API-Herausgeber einen einzelnen API-Verwaltungsdienst in einer beliebigen Anzahl von gewünschten Azure-Regionen zur Verfügung stellen können. Dies trägt dazu bei, die Anforderungslatenz bei geografisch verteilten API-Nutzern zu verringern, und verbessert gleichzeitig die Dienstverfügbarkeit, wenn eine Region offline geht.

Bei der anfänglichen Erstellung eines API Management-Diensts enthält dieser nur eine [Einheit][] und befindet sich in einer einzelnen Azure-Region. Diese wird als primäre Region bezeichnet. Mit dem Azure-Portal können auf einfache Weise zusätzliche Regionen hinzugefügt werden. Der API Management-Proxyserver wird in jeder Region bereitgestellt, und Datenverkehr durch Aufrufe wird an den nächstgelegenen Proxy geroutet. Wenn eine Region offline geht, wird der Datenverkehr automatisch an den nächstgelegenen Proxy umgeleitet.

> [AZURE.IMPORTANT]Die Bereitstellung in mehreren Regionen steht nur im Tarif **[Premium][]** zur Verfügung.

## <a name="add-region"> </a>Bereitstellen einer API Management-Dienstinstanz für eine neue Region

Klicken Sie zunächst im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**. Daraufhin gelangen Sie zum API Management-Herausgeberportal.

![Herausgeberportal][api-management-management-console]

>Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][].

Navigieren Sie im Azure-Portal zur Registerkarte **Skalieren** für Ihre API Management-Dienstinstanz.

![Registerkarte "Skalieren"][api-management-scale-service]

Um eine Bereitstellung in einer neuen Region durchzuführen, klicken Sie auf die Dropdownliste unterhalb der primären Region und wählen eine Region aus der Liste aus.

![Region hinzufügen][api-management-add-region]

Wählen Sie nach Auswahl der Region in der Dropdownliste für die Einheitenanzahl die Anzahl von Einheiten für die neue Region aus.

![Einheiten angeben][api-management-select-units]

Wenn Sie die gewünschten Regionen und Einheiten konfiguriert haben, klicken Sie auf **Speichern**.

## <a name="remove-region"> </a>Löschen einer API Management-Dienstinstanz aus einer Region

Um eine API Management-Dienstinstanz aus einer Region zu löschen, navigieren Sie im Azure-Portal für Ihre API Management-Dienstinstanz zur Registerkarte **Skalieren**.

![Registerkarte "Skalieren"][api-management-scale-service]

Klicken Sie auf das **X** rechts neben der Region, die Sie entfernen möchten.

![Region entfernen][api-management-remove-region]

Wenn Sie die gewünschten Regionen entfernt haben, klicken Sie auf **Speichern**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Erstellen einer API-Verwaltungsinstanz]: api-management-get-started.md#create-service-instance
[Erste Schritte mit der Azure API-Verwaltung]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[Einheit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=July15_HO4-->
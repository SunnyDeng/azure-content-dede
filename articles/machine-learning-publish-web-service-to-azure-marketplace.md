<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Veröffentlichen von Azure ML-Webdiensten im Azure Marketplace

In diesem Dokument werden folgende Themen behandelt:

- [Einführung][Einführung]
- [Der Veröffentlichungsprozess im Überblick][Der Veröffentlichungsprozess im Überblick]
- [Leitfaden für die Veröffentlichung im Azure Marketplace][Leitfaden für die Veröffentlichung im Azure Marketplace]
- [Spezielle Machine Learning-Optionen][Spezielle Machine Learning-Optionen]

<!--Anchors-->

## Einführung

Im Azure Marketplace können Sie Azure Machine Learning-Webdienste als kostenlose oder zahlungspflichtige Dienste für externe Consumer veröffentlichen. Dieses Dokument beschreibt den Veröffentlichungsprozess und bietet Hinweise zu den ersten Schritten. Mit diesem Prozess können Sie Ihre Webdienste bereitstellen, sodass andere Entwickler diese in ihren Anwendungen verwenden können.

## Der Veröffentlichungsprozess im Überblick

Die Veröffentlichung eines Azure ML-Webdiensts im Azure Marketplace umfasst die folgenden Schritte:

1.  Erstellen und Veröffentlichen eines Azure ML-Webdiensts im RRS- (Request-Response Service, Antwort-/Anfrage-Dienst) oder BES-Format (Batch-Execution Service, Stapelausführungsdienst).
2.  Stellen Sie den Dienst über das Azure-Verwaltungsportal in Ihrer Produktionsumgebung bereit.
3.  Verwenden Sie die URL des veröffentlichten Webdiensts für die Veröffentlichung im Azure Marketplace.
4.  Überblick über den Veröffentlichungsprozess: <http://msdn.microsoft.com/de-de/library/azure/hh580725.aspx>
5.  Nach der Übermittlung wird Ihr Angebot geprüft und muss genehmigt werden, bevor Ihre Kunden das Angebot abonnieren können. Der Veröffentlichungsprozess kann einige Geschäftstage in Anspruch nehmen. Wir versuchen, diesen Prozess möglichst kurz zu halten und werden in den kommenden Ankündigungen ein Update hierzu veröffentlichen.

## Leitfaden für die Veröffentlichung im Azure Marketplace

1.  Sie müssen sich als Herausgeber registrieren. Weitere Informationen finden Sie unter: <http://msdn.microsoft.com/de-de/library/azure/hh563872.aspx>
2.  Sie müssen Daten zu Ihrem Angebot liefern, inklusive einer Preisgestaltung. Legen Sie fest, ob Sie Ihren Dienst kostenlos oder zahlungspflichtig anbieten möchten. Weitere Informationen finden Sie unter: <http://msdn.microsoft.com/de-de/library/azure/hh563873.aspx>
3.  Für zahlungspflichtige Dienste müssen Sie Bezahlungsinformationen wie z. B. Ihre Bank- und Steuerdaten angeben. Weitere Informationen finden Sie unter: <http://msdn.microsoft.com/de-de/library/azure/hh563873.aspx>

## Spezielle Machine Learning-Optionen


1.  Wählen Sie zur Erstellung eines neuen Angebots die Option **Datendienst** aus und klicken Sie auf **Neuen Datendienst erstellen**.

    ![Azure Marketplace][Azure Marketplace]

2.  Wählen Sie in der Registerkarte **Datendienst** die Option **Webdienst** als Datenquelle aus.

    ![Azure Marketplace][1]

3.  Geben Sie unter **Dienst-URL** die URL Ihres Webdiensts ein:

	- Klicken Sie im linken Menü in Azure ML Studio auf **WEBDIENSTE**.
	- Klicken Sie auf den Webdienst, den Sie im Marketplace veröffentlichen möchten.
	- Klicken Sie auf der **Dashboard**-Seite auf **API-Hilfeseite** für den RRS-Dienst.
	- Kopieren Sie die OData-Endpunktadresse.

	<br />

4.  Wählen Sie unter Authentifizierung die Option **Header** als **Authentifizierungsschema** aus.

	- Geben Sie "Authorization" unter **Headername** ein.
	- Unter **Headerwert**:
		- Kopieren Sie den **API-Schlüssel** auf der **Dashboard**-Seite für Ihren Webdienst in ML Studio.
		- Geben Sie im Feld **Headerwert** den Wert "Bearer" (ohne Anführungszeichen) ein, dann ein Leerzeichen, und anschließend den API-Schlüssel.
	- Markieren Sie das Kontrollkästchen **Dies ist ein OData-Dienst**.

	<br />

5.  Kategorien:
	- Die Option **Machine Learning** muss markiert sein.

  [Einführung]: #introduction
  [Der Veröffentlichungsprozess im Überblick]: #overview-of-the-publishing-process
  [Leitfaden für die Veröffentlichung im Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
  [Spezielle Machine Learning-Optionen]: #machine-learning-specific-options
  [Azure Marketplace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png

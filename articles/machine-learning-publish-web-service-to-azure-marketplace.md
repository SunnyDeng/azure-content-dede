<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Veröffentlichen von Azure ML-Webdiensten im Azure Marketplace | Azure" description="Veröffentlichen von Azure ML-Webdiensten im Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/03/2014" ms.author="garye" />

# Veröffentlichen von Azure ML-Webdiensten im Azure Marketplace 

In diesem Dokument werden folgende Themen behandelt:

- [Einführung]
- [Der Veröffentlichungsprozess im Überblick]
- [Leitfaden für die Veröffentlichung im Azure Marketplace]
- [Spezielle Machine Learning-Optionen] 

<!--Anchors-->
[Einführung]: #introduction
[Der Veröffentlichungsprozess im Überblick]: #overview-of-the-publishing-process
[Leitfaden für die Veröffentlichung im Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
[Spezielle Machine Learning-Optionen]: #machine-learning-specific-options 

## Einführung

Im Azure Marketplace können Sie Azure Machine Learning-Webdienste als kostenlose oder zahlungspflichtige Dienste für externe Consumer veröffentlichen. Dieses Dokument beschreibt den Veröffentlichungsprozess und bietet Hinweise zu den ersten Schritten. Mit diesem Prozess können Sie Ihre Webdienste bereitstellen, sodass andere Entwickler diese in ihren Anwendungen verwenden können.

## Der Veröffentlichungsprozess im Überblick 

Die Veröffentlichung eines Azure ML-Webdiensts im Azure Marketplace umfasst die folgenden Schritte:

1.	Erstellen und Veröffentlichen eines Webdienstes Azure ML RRS-Webdiensts (Request-Response Service, Antwort-/Anfrage-Dienst).
2.	Stellen Sie den Dienst über das Azure-Verwaltungsportal in Ihrer Produktionsumgebung bereit.
3.	Verwenden Sie die URL des veröffentlichten Webdiensts für die Veröffentlichung im Azure Marketplace.
4.	Überblick über den Veröffentlichungsprozess: http://msdn.microsoft.com/de-de/library/azure/hh580725.aspx 
5.	Nach der Übermittlung wird Ihr Angebot geprüft und muss genehmigt werden, bevor Ihre Kunden das Angebot abonnieren können. Der Veröffentlichungsprozess kann einige Geschäftstage in Anspruch nehmen. Wir versuchen, diesen Prozess möglichst kurz zu halten und werden in den kommenden Ankündigungen ein Update hierzu veröffentlichen.

## Leitfaden für die Veröffentlichung im Azure Marketplace

1.	Sie müssen sich als Herausgeber registrieren. Weitere Informationen finden Sie unter: http://msdn.microsoft.com/de-de/library/azure/hh563872.aspx
2.	Sie müssen Daten zu Ihrem Angebot liefern, inklusive einer Preisgestaltung. Legen Sie fest, ob Sie Ihren Dienst kostenlos oder zahlungspflichtig anbieten möchten. Weitere Informationen finden Sie unter: http://msdn.microsoft.com/de-de/library/azure/hh563873.aspx 
3.	Für zahlungspflichtige Dienste müssen Sie Bezahlungsinformationen wie z. B. Ihre Bank- und Steuerdaten angeben. Weitere Informationen finden Sie unter: http://msdn.microsoft.com/de-de/library/azure/hh563873.aspx

## Spezielle Machine Learning-Optionen


1.	Wählen Sie zur Erstellung eines neuen Angebots die Option **Datendienst** aus und klicken Sie auf **Neuen Datendienst erstellen**. 
 
	![Azure Marketplace][image1]

	<br />

2. Wählen Sie in der Registerkarte **Datendienst** die Option **Webdienst** als Datenquelle aus.

	![Azure Marketplace][image2]

3.	Rufen Sie die Webdienst-URL und den API-Schlüssel vom Azure-Verwaltungsportal ab:
	1.	Melden Sie sich in einem separaten Browserfenster oder einer separaten Registerkarte beim Azure-Verwaltungsportal ([https://manage.windowsazure.com](https://manage.windowsazure.com)) an. 
	2.	Wählen Sie im linken Menü **Machine Learning**.
	3.	Klicken Sie auf **Webdienste**, und klicken Sie dann auf den Webdienst, den Sie veröffentlichen.
	4.	Kopieren Sie den **API-Schlüssel** in ein temporäres Verzeichnis (z. B. Editor).
	5.	Klicken Sie auf die **API-Hilfeseite** für den Antwort-/Anfrage-Diensttyp.
	6.	Kopieren Sie die **OData-Endpunktadresse** in das temporäre Verzeichnis.

	<br />

3.	Fügen Sie im Setupdialogfeld Marketplace-Datendienst die OData-Endpunktadresse in die **Dienst-URL** ein.

	<br />

4. Wählen Sie unter Authentifizierung die Option **Header**als **Authentifizierungsschema** aus.

	- Geben Sie "Authorization" unter **Headername ein.**
	- Geben Sie im Feld **Headerwert** den Wert "Bearer" (ohne Anführungszeichen) ein, dann ein Leerzeichen, und anschließend den API-Schlüssel.
	- Aktivieren Sie das Kontrollkästchen **Dies ist ein OData-Dienst**.
	- Klicken Sie auf **Verbindung testen**, um die Verbindung zu testen.

	<br />

5.	Unter "Kategorien":
	- Die Option **Machine Learning** muss markiert sein.



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png

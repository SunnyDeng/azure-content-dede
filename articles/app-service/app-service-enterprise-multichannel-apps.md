<properties 
	pageTitle="Multichannel-Apps für Unternehmen" 
	description="Übersicht darüber, wie eine Multichannel-App sich über lokale Ressourcen und cloudbasierte Softwaredienste erstreckt." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# Erstellen von Multichannel-Anwendungen für das Unternehmen

## Übersicht

Multichannel-Apps für Unternehmen verwenden verschiedene Technologien, um Kunden Daten zu präsentieren. Unternehmensbenutzer, die per Web, mobil und über APIs auf Ressourcen zugreifen, empfangen und verarbeiten Informationen aus einer Vielzahl von Quellen. Diese Quellen umfassen interne Systeme, die lokal ausgeführt werden, sowie cloudbasierte Dienste.

Für Unternehmensanwendungen muss außerdem ein sicherer Zugriff gewährleistet werden, bei dem Mitarbeiter und Geschäftspartner über sichere Identitäten und unter direkter Kontrolle durch das Unternehmen eine Verbindung mit Datenquellen herstellen.

In Azure App Service ausgeführte Unternehmensanwendungen bieten all diese Funktionen.

Das nachstehende Beispiel einer Anwendung für Geschäftsreisen von Mitarbeitern zeigt eine Multichannel-App für Unternehmen, die über Azure Active Directory (AAD) geschützt wird und sowohl in lokale Ressourcen als auch in SaaS-Dienste (Software-as-a-Service) wie beispielsweise Office 365 und Salesforce.

## <a name="acceptablefiles"></a>Steuern des Zugriffs mit Azure Active Directory

Benutzer der Anwendung für Geschäftsreisen müssen sich zunächst bei den Active Directory-Diensten des Unternehmens authentifizieren. Es wurden einige wenige Schritte ausgeführt, um die Anwendung für die Verwendung von Azure Active Directory (AAD) zu konfigurieren:

* Es wurde ein Azure Active Directory für das Unternehmen erstellt.
* Das lokale Active Directory des Unternehmens wurde in einem Verbund mit Azure Active Directory konfiguriert.
* Schließlich wurde die Anwendung mithilfe der einfachen AAD-Integrationsfunktion von Azure App Service für AAD registriert. 

Das Ergebnis ist, dass die Anwendung Benutzer zur Anmeldung bei AAD (und in der Folge beim Unternehmens-AD) auffordert.
	
![AAD-Anmeldung][AADLogin]

Weitere Informationen zum Einrichten der Azure App Service-Integration in AAD finden Sie unter [Azure App Service-Integration in AAD][AASIntegrationwithAAD].

## <a name="acceptablefiles"></a>Zugreifen auf lokale Ressourcen

Nachdem ein Benutzer über AAD angemeldet wurde, wird eine Liste der geplanten Geschäftsreisen angezeigt. Da die Webanwendung in Microsoft Azure ausgeführt wird, erfordert sie eine Funktion für den Zugriff auf die lokalen SQL-Server, die diese Informationen enthalten.

![Daten aus einem lokalen SQL Server][DatafromOnpremisesSqlServer]

Die Anwendung wird mit der Punkt-zu-Standort-Integrationsfunktion von VNET konfiguriert. Dies ermöglicht der Anwendung die Verwendung einer standardmäßigen Datenzugriffslogik (in diesem Fall Entity Framework) für den transparenten Zugriff auf einen Remote-SQL-Server, der innerhalb des Unternehmensnetzwerks ausgeführt wird.

Weitere Informationen zur Punkt-zu-Standort-VNET-Integration finden Sie unter [VNET-Integration][VNETIntegration].

## <a name="acceptablefiles"></a>Integration in Office 365

Jedes Mal, wenn ein Mitarbeiter einen Reisedatensatz erstellt, erstellt die Webanwendung einen Reiseantrag in einer SharePoint Online-Liste. In der Anwendung ist ein Link vorhanden, über den die Mitarbeiter bequem auf die SharePoint-Liste zugreifen können:

![Link zur SharePoint-Liste][SharepointListLink]

Durch einen Klick auf den Link werden die Benutzer automatisch zur SharePoint-Liste weitergeleitet. Da die Mitarbeiter beim Unternehmens-AAD angemeldet sind, werden sie mithilfe des von AAD ausgegebenen Sicherheitstokens transparent bei Office 365 authentifiziert.

![SharePoint-Liste][SharepointList]

Die Webanwendung erstellt außerdem ein Reisedokument in einer Sharepoint Online-Dokumentbibliothek.

![SharePoint-Dokumentbibliothek][SharepointDocumentLibrary]

Die in SharePoint Online erstellten Objekte ermöglichen es der Webanwendung, Funktionen in Office 365 zu nutzen. Beispielsweise könnte immer dann ein Workflow zur Entscheidung/Genehmigung gestartet werden, wenn ein Element in der SharePoint-Liste erstellt wird.

Weitere Informationen zur Office 365-Integration finden Sie unter [Office 365-Integration][Office365Integration].

## <a name="acceptablefiles"></a>Integration in SaaS-Dienste

Unternehmen von heute nutzen eine Vielzahl von SaaS-Diensten und müssen mit SaaS-Daten aus anderen Anwendungen interagieren. Die Anwendung für Geschäftsreisen ist ein Beispiel für ein solches Szenario. Sie aktualisiert in Salesforce immer dann Informationen zum Kundenkonto, wenn ein Mitarbeiter eine Geschäftsreise zu einem Kunden plant.

![Salesforce-Integration][SalesforceIntegration]

Das Salesforce-Konto des Unternehmens ist so mit AAD eingerichtet, dass mithilfe der AAD-Anmeldeinformationen eine transparente Authentifizierung bei Salesforce durchgeführt wird. Aus diesem Grund kann die Anwendung, sobald die Mitarbeiter mithilfe von AAD an der Reiseanwendung angemeldet wurden, Lese- und Schreibvorgänge für in Salesforce gespeicherte Daten ausführen.

Weitere Informationen zur SaaS-Integration finden Sie unter [SaaS-Integration][SaaSIntegration].

## <a name="NextSteps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure Application Services][AzureApplicationServices].
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-office365/
[SaaSIntegration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-salesforce/
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!---HONumber=July15_HO3-->
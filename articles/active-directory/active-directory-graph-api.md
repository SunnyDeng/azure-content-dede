<properties
   pageTitle="Azure Active Directory Graph-API"
   description="Eine Übersicht und eine Schnellstartanleitung für die Graph-API, die den programmgesteuerten Zugriff auf Azure AD über REST-API-Endpunkte ermöglicht."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="mbaldwin" />

# Azure Active Directory Graph-API

> *Legen Sie direkt los mit der [Graph-API-Schnellstartanleitung](active-directory-graph-api-quickstart.md) und der [interaktiven Graph-API-Referenzdokumentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).*

Die Azure Active Directory Graph-API ermöglicht programmgesteuerten Zugriff auf Azure AD über REST-API-Endpunkte. Anwendungen können die Graph-API verwenden, um CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen) für Verzeichnisdaten und Objekte auszuführen. Die Graph-API unterstützt beispielsweise die folgenden allgemeinen Vorgänge für ein Benutzerobjekt:

- Erstellen eines neuen Benutzers in einem Verzeichnis

- Abrufen der detaillierten Eigenschaften eines Benutzers, z. B. seiner Gruppen

- Aktualisieren der Eigenschaften eines Benutzers, z. B. seines Standorts und seiner Telefonnummer oder das Ändern seines Kennworts

- Überprüfen der Gruppenmitgliedschaft eines Benutzers für den rollenbasierten Zugriff

- Deaktivieren oder vollständiges Löschen des Kontos eines Benutzers

Ähnliche Vorgänge wie für Benutzerobjekte können Sie auch für andere Objekte wie Gruppen oder Anwendungen ausführen. Damit die Graph-API in einem Verzeichnis aufgerufen werden kann, muss die Anwendung bei Azure AD registriert und so konfiguriert sein, dass ein Zugriff auf das Verzeichnis zulässig ist. Dies wird in der Regel durch einen Benutzer- oder Administrator-Zustimmungsdatenfluss erreicht.

Informationen zu den ersten Schritten mit der Azure Active Directory Graph-API finden Sie in der [Graph-API-Schnellstartanleitung](active-directory-graph-api-quickstart.md) und in der [interaktiven Graph-API-Referenz](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## Merkmale

Die Graph-API bietet die folgenden Features:

- **REST-API-Endpunkte**: Die Graph-API ist ein RESTful-Dienst, der aus Endpunkten besteht, auf die mithilfe von HTTP-Standardanforderungen zugegriffen wird. Die Graph-API unterstützt XML- oder JSON (Javascript Object Notation)-Inhaltstypen für Anforderungen und Antworten. Weitere Informationen finden Sie unter [REST-API-Referenz zu Azure AD Graph](https://msdn.microsoft.com/library/azure/hh974478.aspx).

- **Authentifizierung mit Azure AD**: Jede Anforderung an die Graph-API muss durch Anfügen eines JSON-Webtokens (JWT) im Autorisierungsheader der Anforderung authentifiziert werden. Dieses Token wird durch eine Anforderung an den Token-Endpunkt von Azure AD und die Bereitstellung gültiger Anmeldeinformationen abgerufen. Sie können den OAuth 2.0-Datenfluss für Clientanmeldeinformationen oder den Datenfluss für die Autorisierungscodegewährung verwenden, um ein Token für den Graph-Aufruf abzurufen. Weitere Informationen finden Sie unter [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Rollenbasierte Autorisierung (RBAC)**: Um RBAC in der Graph-API ausführen zu können, werden Sicherheitsgruppen verwendet. Wenn Sie beispielsweise ermitteln möchten, ob ein Benutzer über Zugriff auf eine bestimmte Ressource verfügt, kann die Anwendung den unter [Überprüfen der Gruppenmitgliedschaft (transitiv)](https://msdn.microsoft.com/library/azure/dn151601.aspx) beschriebenen Vorgang aufrufen, der "true" oder "false" zurückgibt.

- **Differenzielle Abfrage**: Wenn Sie ohne mehrfaches Abfragen der Graph-API überprüfen möchten, ob zwischen zwei Zeiträumen Änderungen an einem Verzeichnis vorgenommen wurden, können Sie eine differenzielle Abfrageanforderung verwenden. Dieser Anforderungstyp gibt nur die Änderungen zurück, die zwischen der vorherigen differenziellen Abfrageanforderung und der aktuellen Anforderung erfolgt sind. Weitere Informationen finden Sie unter [Differenzielle Abfragen der Azure AD Graph-API](https://msdn.microsoft.com/library/azure/jj836245.aspx).

- **Verzeichniserweiterungen**: Wenn Sie eine Anwendung entwickeln, die eindeutige Eigenschaften für Verzeichnisobjekte lesen oder schreiben muss, können Sie mithilfe der Graph-API Erweiterungswerte registrieren und verwenden. Wenn Ihre Anwendung beispielsweise eine Skype-ID-Eigenschaft für jeden Benutzer erfordert, können Sie die neue Eigenschaft im Verzeichnis registrieren. Sie steht dann für jedes Benutzerobjekt zur Verfügung. Weitere Informationen finden Sie unter [Verzeichnisschemaerweiterungen der Azure AD Graph-API](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## Szenarios

Die Graph-API ermöglicht eine Vielzahl von Anwendungsszenarios. Die gängigsten Szenarios sind die folgenden:

- **Branchenanwendung (Einzelinstanz)**: In diesem Szenario arbeitet ein Unternehmensentwickler für eine Organisation, die über ein Office 365-Abonnement verfügt. Der Entwickler erstellt eine Webanwendung, die mit Azure AD interagiert, um Aufgaben wie das Zuweisen einer Lizenz zu einem Benutzer auszuführen. Für diese Aufgabe ist der Zugriff auf die Graph-API erforderlich. Der Entwickler registriert daher die Einzelinstanzanwendung in Azure AD und konfiguriert Lese- und Schreibberechtigungen für die Graph-API. Anschließend wird die Anwendung für die Verwendung eigener Anmeldeinformationen oder der Anmeldeinformationen des aktuell angemeldeten Benutzers konfiguriert, um ein Token zum Aufrufen der Graph-API abzurufen.

- **SaaS-Anwendung (Software as a Service, mehrinstanzenfähig)**: In diesem Szenario entwickelt ein unabhängiger Softwarehersteller (Independent Software Vendor, ISV) eine gehostete mehrinstanzenfähige Webanwendung, die Benutzerverwaltungsfeatures für andere Organisationen bereitstellt, die Azure AD verwenden. Da diese Features Zugriff auf Verzeichnisobjekte erfordern, muss die Anwendung die Graph-API aufrufen. Der Entwickler registriert die Anwendung in Azure AD und konfiguriert sie so, dass Lese- und Schreibberechtigungen für die Graph-API erforderlich sind. Anschließend aktiviert er den externen Zugriff, damit andere Organisationen der Verwendung der Anwendung in ihrem Verzeichnis zustimmen können. Wenn sich ein Benutzer in einer anderen Organisation erstmals bei der Anwendung authentifiziert, wird ein Zustimmungsdialogfeld mit den Berechtigungen angezeigt, die die Anwendung anfordert. Durch die Zustimmung erhält die Anwendung dann die angeforderten Berechtigungen für die Graph-API im Verzeichnis des Benutzers. Weitere Informationen zum Consent Framework finden Sie unter [Das Consent Framework im Überblick](https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Consent).

## Weitere Informationen

[Schnellstartanleitung für die Azure AD Graph-API](active-directory-graph-api-quickstart.md)

[AD Graph-REST-Dokumentation](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Entwicklerhandbuch zu Azure Active Directory](active-directory-developers-guide.md)

<!---HONumber=Sept15_HO3-->
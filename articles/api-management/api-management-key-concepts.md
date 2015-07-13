<properties 
	pageTitle="API Management-Schlüsselkonzepte" 
	description="Erfahren Sie mehr über APIs, Produkte, Rollen, Gruppen und andere API Management-Schlüsselkonzepte." 
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
	ms.date="06/19/2015" 
	ms.author="sdanie"/>

#Was ist API Management?

API Management unterstützt Organisationen beim Veröffentlichen von APIs für externe, Partner- und interne Entwickler, um das volle Potenzial von Daten und Diensten nutzen zu können. Unternehmen sind weltweit dabei, ihren Betrieb als digitale Plattform zu erweitern, neue Kanäle zu erschließen, neue Kunden zu finden und die Bindung zu existierenden Kunden zu vertiefen. API Management bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, Geschäftserkenntnissen, Analyse, Sicherheit und Schutz.

> [AZURE.VIDEO microsoft-ignite-2015-azure-api-management-and-the-api-economy]

Administratoren erstellen APIs, um API Management zu nutzen. Jede API besteht aus einer oder mehreren Operationen, und jede API kann zu einem oder mehreren Produkten hinzugefügt werden. Um eine API zu nutzen, abonnieren Entwickler ein Produkt, das diese API enthält. Anschließend können sie die Operationen der API aufrufen und unterliegen dabei allen geltenden Nutzungsrichtlinien.

Dieses Thema behandelt einige Schlüsselkonzepte von API Management.

>[AZURE.NOTE]Weitere Informationen finden Sie im Whitepaper [Cloud-based API Management: Harnessing the Power of APIs](http://j.mp/ms-apim-whitepaper) (PDF, in englischer Sprache). In diesem Whitepaper zu API Management von CITO Research werden folgende Themen behandelt:
>
> - Allgemeine API-Anforderungen und -Herausforderungen
>     - Entkoppeln von APIs und Präsentieren von Fassaden
>     - Schnelle Einrichtung und Inbetriebnahme durch Entwickler
>     - Sichern des Zugriffs
>     - Analysen und Metriken
> - Kontrolle und Einblick mit einer API Management-Plattform
> - Verwenden von Cloudlösungen im Vergleich zu lokalen Lösungen
> - Azure API Management

## <a name="apis"> </a>APIs und Operationen

APIs sind die Grundlage einer API Management-Dienstinstanz. Jede API stellt Entwicklern einen Satz von Operationen zur Verfügung. Jede API enthält einen Verweis auf den Back-End-Dienst, der die API implementiert, und die Operationen der API sind den Operationen des Back-End-Diensts zugeordnet. Für Operationen in API Management ist eine umfangreiche Konfiguration möglich. Sie können die URL-Zuordnung, Abfrage- und Pfadparameter, Anforderungs- und Antwortinhalte sowie das Zwischenspeichern von Operationsantworten steuern. Durchsatzgrenzen, Kontingente und IP-Einschränkungen können ebenfalls auf API-Ebene oder für einzelne Operationen konfiguriert werden.

Weitere Informationen finden Sie unter [Erstellen von APIs][] und [Hinzufügen von Operationen zu einer API][].


## <a name="products"> </a>Produkte

APIs werden in Form von Produkten an Entwickler bereitgestellt. Produkte in API Management enthalten eine oder mehrere APIs und werden mit einem Titel, einer Beschreibung und Nutzungsbedingungen konfiguriert. Produkte können **Offen** oder **Geschützt** sein. Geschützte Produkte müssen abonniert werden, bevor Sie verwendet werden können, während offene Produkte ohne Abonnement genutzt werden können. Wenn ein Produkt bereit für die Nutzung durch Entwickler ist, kann es veröffentlicht werden. Nach der Veröffentlichung kann das Produkt (bei geschützten Produkten nach dem Abonnieren) durch die Entwickler angezeigt werden. Die Genehmigung von Abonnements wird auf der Produktebene konfiguriert. Abonnements können entweder eine Genehmigung eines Administrators erfordern oder automatisch genehmigt werden.

Gruppen dienen zur Verwaltung der Sichtbarkeit von Produkten für Entwickler. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind.

Weitere Informationen finden Sie unter [Erstellen und Veröffentlichen eines Produkts][] und im folgenden Video.

> [AZURE.VIDEO using-products]

## <a name="groups"> </a>Gruppen

Gruppen dienen zur Verwaltung der Sichtbarkeit von Produkten für Entwickler. API Management umfasst die folgenden unveränderlichen Systemgruppen.

-	**Administratoren** – Azure-Abonnementadministratoren sind Mitglieder dieser Gruppe. Administratoren verwalten API Management-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
-	**Entwickler** – Zu dieser Gruppe gehören authentifizierte Benutzer des Entwicklerportals Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum Entwicklerportal und erstellen Anwendungen, die die Operationen einer API aufrufen.
-	**Gäste** – Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API Management-Instanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z. B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Zusätzlich zu diesen Systemgruppen können Administratoren benutzerdefinierte Gruppen erstellen oder [externe Gruppen in zugeordneten Azure Active Directory-Mandanten verwenden](api-management-howto-aad.md/#how-to-add-an-external-azure-active-directory-group). Benutzerdefinierte und externe Gruppen können gemeinsam mit Systemgruppen verwendet werden, um API-Produkte für Entwickler sichtbar zu machen und ihnen den Zugriff auf die API-Produkte zu ermöglichen. Beispielsweise können Sie eine benutzerdefinierte Gruppe für Entwickler eines spezifischen Partnerunternehmens erstellen und diesen Entwicklern Zugriff auf die APIs über ein Produkt erteilen, das nur die relevanten APIs enthält. Ein Benutzer kann Mitglied von mehreren Gruppen sein.

Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen][].

## <a name="developers"> </a> Entwickler

Entwickler stellen die Benutzerkonten in einer API Management-Dienstinstanz dar. Entwickler können von Administratoren erstellt oder eingeladen werden, oder sie können sich im [Entwicklerportal][] anmelden. Jeder Entwickler ist Mitglied in einer oder mehreren Gruppen und kann die Produkte abonnieren, die für die entsprechenden Gruppen sichtbar sind.

Wenn Entwickler ein Produkt abonnieren, erhalten Sie den primären und den sekundären Schlüssel für das Produkt. Diese Schlüssel kommen bei den Aufrufen an die APIs des Produkts zum Einsatz.

Weitere Informationen finden Sie unter [Erstellen und Einladen von Entwicklern][] und [Zuordnen von Entwicklern zu Gruppen][].

## <a name="policies"> </a> Richtlinien

Richtlinien sind ein umfassendes Werkzeug in API Management, mit dem Anbieter das Verhalten der API in Form von Konfigurationen verändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Durchsatzgrenzen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind verfügbar.

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung][] und [Variable festlegen][], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][], [Richtlinienausdrücke][] und im folgenden Video.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

Eine vollständige Liste der Richtlinien für API Management finden Sie unter [Gruppenrichtlinienreferenz][]. Weitere Informationen zur Verwendung und Konfiguration von Richtlinien finden Sie unter [Richtlinien für API Management][]. Ein Lernprogramm zum Erstellen eines Produkts mit Richtlinien für Durchsatzgrenzen und Kontingente finden Sie unter [Erstellen und Konfigurieren erweiterter Produkteinstellungen][]. Das folgende Video enthält eine Veranschaulichung.

> [AZURE.VIDEO rate-limits-and-quotas]

## <a name="developer-portal"> </a> Entwicklerportal

Im Entwicklerportal können Entwickler auf Ihre APIs zugreifen, Operationen anzeigen und aufrufen und Produkte abonnieren. Potenzielle Kunden können das Entwicklerportal besuchen, APIs und Operationen anzeigen und sich anmelden. Sie finden die URL Ihres Entwicklerportals im Dashboard des Azure-Portals für Ihre API Management-Dienstinstanz.

Sie können das Erscheinungsbild Ihres Entwicklerportals konfigurieren, indem Sie eigene Inhalte hinzufügen, Stilregeln anpassen und Ihr Markenbild einfügen.

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Entwicklerportal]: #developer-portal

[Erstellen von APIs]: api-management-howto-create-apis.md
[Hinzufügen von Operationen zu einer API]: api-management-howto-add-operations.md
[Erstellen und Veröffentlichen eines Produkts]: api-management-howto-add-products.md
[Erstellen und Verwenden von Gruppen]: api-management-howto-create-groups.md
[Zuordnen von Entwicklern zu Gruppen]: api-management-howto-create-groups.md#associate-group-developer
[Erstellen und Konfigurieren erweiterter Produkteinstellungen]: api-management-howto-product-with-rules.md
[Erstellen und Einladen von Entwicklern]: api-management-howto-create-or-invite-developers.md
[Gruppenrichtlinienreferenz]: api-management-policy-reference.md
[Richtlinien für API Management]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance



 

<!---HONumber=62-->
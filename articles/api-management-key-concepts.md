<properties 
	pageTitle="Schlüsselkonzepte für die API-Verwaltung" 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

#Schlüsselkonzepte für die API-Verwaltung

Die API-Verwaltung unterstützt Organisationen beim Veröffentlichen von APIs für externe, Partner- und interne Entwickler, um das volle Potenzial von Daten und Diensten nutzen zu können. Unternehmen sind weltweit dabei, ihren Betrieb als digitale Plattform zu erweitern, neue Kanäle zu erschließen, neue Kunden zu finden und die Bindung zu existierenden Kunden zu vertiefen. Die API-Verwaltung bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, Geschäftserkenntnissen, Analyse, Sicherheit und Schutz.

Administratoren erstellen APIs, um die API-Verwaltung zu nutzen. Jede API besteht aus einer oder mehreren Operationen, und jede API kann zu einem oder mehreren Produkten hinzugefügt werden. Um eine API zu nutzen, abonnieren Entwickler ein Produkt, das diese API enthält. Anschließend können sie die Operationen der API aufrufen und unterliegen dabei allen geltenden Nutzungsrichtlinien.

Dieses Thema behandelt einige Schlüsselkonzepte der API-Verwaltung.

## In diesem Thema

-   [APIs und Operationen][]
-   [Produkte][]
-   [Gruppen][]
-   [Entwickler][]
-   [Richtlinien][]
-	[Entwicklerportal][]


## <a name="apis"> </a>APIs und Operationen

APIs sind das Fundament einer API-Verwaltungs-Dienstinstanz. Jede API stellt Entwicklern einen Satz von Operationen zur Verfügung. Jede API enthält einen Verweis auf den Back-End-Dienst, der die API implementiert, und die Operationen der API sind den Operationen des Back-End-Diensts zugeordnet. Operationen in der API-Verwaltung sind hochkonfigurierbar. Sie haben Kontrolle über die URL-Zuordnung, Abfrage- und Pfadparameter, Inhalt von Anfragen und Antworten sowie Caching von Operationsantworten. Richtlinien für Aufrufbeschränkungen, Kontingente und IP-Einschränkungen können ebenfalls auf API-Ebene oder für einzelne Operationen konfiguriert werden.

Weitere Informationen finden Sie unter [Erstellen von APIs][] und [Hinzufügen von Operationen zu einer API][].


## <a name="products"> </a> Produkte

APIs werden in Form von Produkten an Entwickler bereitgestellt. Produkte in der API-Verwaltung enthalten eine oder mehrere APIs und werden mit einem Titel, einer Beschreibung und Nutzungsbedingungen konfiguriert. Wenn ein Produkt bereit für die Nutzung durch Entwickler ist, kann es veröffentlicht werden. Nach der Veröffentlichung können Entwickler das Produkt anzeigen und abonnieren. Die Genehmigung von Abonnements wird auf der Produktebene konfiguriert. Abonnements können entweder eine Genehmigung eines Administrators erfordern oder automatisch genehmigt werden.

Gruppen dienen zur Verwaltung der Sichtbarkeit von Produkten für Entwickler. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind. 

Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen und Veröffentlichen eines Produkts][].

## <a name="groups"> </a> Gruppen

Gruppen dienen zur Verwaltung der Sichtbarkeit von Produkten für Entwickler. Die API-Verwaltung enthält die folgenden integrierten Gruppen.

-	**Administratoren** - Administratoren verwalten API-Verwaltungs-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
-	**Entwickler** - Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum [Entwicklerportal][] und erstellen Anwendungen, die die Operationen einer API aufrufen.
-	**Gäste** - Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API-Verwaltungsinstanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z. B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Neben diesen integrierten Gruppen können Administratoren auch eigene Gruppen erstellen. Benutzerdefinierte Gruppen haben dieselben Berechtigungen wie die integrierte Gruppe der Entwickler und dienen zur Verwaltung unterschiedlicher Gruppen von Entwicklern. Sie können z. B. eine Gruppe für Entwickler erstellen, die die APIs eines bestimmten Produkts verwenden, und eine andere Gruppe für Entwickler, die die APIs eines anderen Produkts verwenden.

Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen][].

## <a name="developers"> </a> Entwickler

Entwickler stellen die Benutzerkonten in einer API-Verwaltungs-Dienstinstanz dar. Entwickler können von Administratoren erstellt oder eingeladen werden, oder sie können sich im [Entwicklerportal][] anmelden. Jeder Entwickler ist Mitglied in einer oder mehreren Gruppen und kann die Produkte abonnieren, die für die entsprechenden Gruppen sichtbar sind.

Wenn Entwickler ein Produkt abonnieren, erhalten Sie den primären und den sekundären Schlüssel für das Produkt. Diese Schlüssel kommen bei den Aufrufen an die APIs des Produkts zum Einsatz.

Weitere Informationen finden Sie unter [Erstellen und Einladen von Entwicklern][] und [Zuordnen von Entwicklern zu Gruppen][].

## <a name="policies"> </a> Richtlinien

Richtlinien sind ein umfassendes Werkzeug in der API-Verwaltung, mit dem Anbieter das Verhalten der API in Form von Konfigurationen verändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Aufrufbeschränkungen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind verfügbar.

Eine vollständige Liste der Richtlinien für die API-Verwaltung finden Sie unter [Richtlinienreferenz][]. Weitere Informationen zur Verwendung und Konfiguration von Richtlinien finden Sie unter [Richtlinien für die API-Verwaltung][]. Ein Lernprogramm zum Erstellen eines Produkts mit Richtlinien für Aufrufbeschränkungen und Kontingente finden Sie unter [Erstellen und Konfigurieren erweiterter Produkteinstellungen][].


## <a name="developer-portal"> </a> Entwicklerportal

Im Entwicklerportal können Entwickler auf Ihre APIs zugreifen, Operationen anzeigen und aufrufen und Produkte abonnieren. Potenzielle Kunden können das Entwicklerportal besuchen, APIs und Operationen anzeigen und sich anmelden. Sie finden die URL Ihres Entwicklerportals im Dashboard des Azure-Portals für Ihre API-Verwaltungs-Dienstinstanz.

Sie können das Erscheinungsbild Ihres Entwicklerportals konfigurieren, indem Sie eigene Inhalte hinzufügen, Stilregeln anpassen und Ihr Markenbild einfügen.

[APIs und Operationen]: #apis
[Produkte]: #products
[Gruppen]: #groups
[Entwickler]: #developers
[Richtlinien]: #policies
[Entwicklerportal]: #developer-portal

[Erstellen von APIs]: ../api-management-howto-create-apis
[Hinzufügen von Operationen zu einer API]: ../api-management-howto-add-operations
[Erstellen und Veröffentlichen eines Produkts]: ../api-management-howto-add-products
[Erstellen und Verwenden von Gruppen]: ../api-management-howto-create-groups
[Zuordnen von Entwicklern zu Gruppen]: ../api-management-howto-create-groups/#associate-group-developer
[Erstellen und Konfigurieren erweiterter Produkteinstellungen]: ../api-management-howto-product-with-rules
[Erstellen und Einladen von Entwicklern]: ../api-management-howto-create-or-invite-developers
[Richtlinienreferenz]: ../api-management-policy-reference
[Richtlinien für die API-Verwaltung]: ../api-management-howto-policies
[Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance




<!--HONumber=35.2-->

<!--HONumber=46--> 

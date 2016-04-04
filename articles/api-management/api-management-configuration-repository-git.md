<properties 
	pageTitle="Speichern und Konfigurieren der API Management-Dienstkonfiguration mit Git" 
	description="Erfahren Sie, wie Sie die API Management-Dienstkonfiguration mit Git speichern und konfigurieren." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="sdanie"/>


# Speichern und Konfigurieren der API Management-Dienstkonfiguration mit Git

>[AZURE.IMPORTANT] Die Git-Konfiguration für API Management ist derzeit in der Vorschau. Sie ist funktionell abgeschlossen, aber in der Vorschau ist, da wir aktiv Feedback zu dieser Funktion suchen. Es ist möglich, dass wir als Reaktion auf Kundenfeedback eine wichtige Änderung vornehmen, und raten daher von der Verwendung der Funktion in Produktionsumgebungen ab. Wenn Sie Feedback oder Fragen haben, informieren Sie uns bitte unter `apimgmt@microsoft.com`.

Jede API Management-Dienstinstanz verwaltet eine Konfigurationsdatenbank, die Informationen über die Konfiguration und die Metadaten für die Dienstinstanz enthält. Änderungen können an der Dienstinstanz durch Ändern einer Einstellung im Herausgeberportal, mithilfe eines PowerShell-Cmdlets oder durch einen REST-API-Aufruf vorgenommen werden. Neben diesen Methoden können Sie die Dienstinstanzkonfiguration auch mit Git verwalten, um Dienstverwaltungsszenarien wie die folgenden zu ermöglichen:

-	Verwaltung von Konfigurationsversionen: Laden Sie verschiedene Versionen der Dienstkonfiguration herunter, und speichern Sie sie.
-	Massenänderungen an der Konfiguration: Ändern Sie in einem einzelnen Vorgang mehrere Teile der Dienstkonfiguration in Ihrem lokalen Repository, und integrieren Sie die Änderungen wieder auf dem Server.
-	Vertraute Git-Tools und -Workflows: Verwenden Sie die Git-Tools und -Workflows, die Sie bereits kennen.

Das folgende Diagramm zeigt eine Übersicht über die verschiedenen Methoden zum Konfigurieren Ihrer API Management-Dienstinstanz.

![Konfigurieren mit Git][api-management-git-configure]

Wenn Sie den Dienst mit dem Herausgeberportal, PowerShell-Cmdlets oder der REST-API ändern, verwalten Sie die Konfigurationsdatenbank des Diensts mithilfe des Endpunkts `https://{name}.management.azure-api.net`, der auf der rechten Seite des Diagramms angezeigt wird. Die linke Seite des Diagramms veranschaulicht, wie Sie die Dienstkonfiguration mithilfe von Git und dem Git-Repository für Ihren Dienst unter `https://{name}.scm.azure-api.net` verwalten können.

Die folgenden Schritte bieten eine Übersicht über die Verwaltung Ihrer API Management-Dienstinstanz mit Git.

1.	Aktivieren von Git-Zugriff in Ihrem Dienst
2.	Speichern der Konfigurationsdatenbank des Diensts in Ihrem Git-Repository
3.	Klonen des Git-Repositorys auf Ihrem lokalen Computer
4.	Herunterladen des aktuellen Repositorys auf Ihren lokalen Computer und Ausführen eines Commits und Übertragen der Änderungen zurück in das Repository
5.	Bereitstellen der Änderungen aus dem Repository in der Konfigurationsdatenbank des Diensts

In diesem Artikel wird das Aktivieren und Verwenden von Git für die Verwaltung der Dienstkonfiguration beschrieben. Zudem enthält er eine Referenz für die Dateien und Ordner im Git-Repository.

## So aktivieren Sie Git-Zugriff

Sie können den Status der Git-Konfiguration schnell anzeigen, indem Sie das Git-Symbol im Herausgeberportal in der oberen rechten Ecke anzeigen. In diesem Beispiel ist der Git-Zugriff noch nicht aktiviert.

![Git-Status][api-management-git-icon-enable]

Um Ihre Git-Konfiguration anzuzeigen und zu konfigurieren, können Sie entweder auf das Git-Symbol klicken, oder Sie klicken auf das Menü **Sicherheit** und navigieren zu der Registerkarte **Konfigurations-Repository**.

![Git aktivieren][api-management-enable-git]

Um den Git-Zugriff zu aktivieren, aktivieren Sie das Kontrollkästchen **Git-Zugriff aktivieren**.

Kurze Zeit später wird die Änderung gespeichert, und eine Meldung wird angezeigt. Beachten Sie, dass das Git-Symbol farbig geworden ist, um anzuzeigen, dass der Git-Zugriff aktiviert ist, und die Statusmeldung jetzt angibt, dass Änderungen vorliegen, die noch nicht im Repository gespeichert sind. Dies liegt daran, dass die API Management-Dienstkonfigurationsdatenbank noch nicht im Repository gespeichert wurde.

![Git aktiviert][api-management-git-enabled]

>[AZURE.IMPORTANT] Geheime Schlüssel, die nicht als Eigenschaften definiert sind, werden im Repository gespeichert und verbleiben in dessen Verlauf, bis Sie den Git-Zugriff deaktivieren und erneut aktivieren. Eigenschaften stellen einen sicheren Ort zum Verwalten von konstanten Zeichenfolgenwerten, einschließlich geheimer Schlüssel, für alle API-Konfigurationen und -Richtlinien dar. Sie müssen sie also nicht direkt in Ihren Richtlinienanweisungen speichern. Weitere Informationen finden Sie unter [How to use properties in Azure API Management policies](api-management-howto-properties.md) (Verwenden von Eigenschaften in Azure API Management-Richtlinien).

Informationen zum Aktivieren oder Deaktivieren des Git-Zugriffs mithilfe der REST-API finden Sie unter [Azure API Management-REST-API-Mandanten-Entität](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## So speichern Sie die Dienstkonfiguration im Git-Repository

Der erste Schritt vor dem Klonen des Repositorys ist, den aktuellen Zustand der Dienstkonfiguration im Repository zu speichern. Klicken Sie auf **Konfiguration im Repository speichern**.

![Konfiguration speichern][api-management-save-configuration]

Nehmen Sie auf dem Bestätigungsbildschirm alle gewünschten Änderungen vor, und klicken Sie zum Speichern auf **OK**.

![Konfiguration speichern][api-management-save-configuration-confirm]

Nach einigen Augenblicken wird die Konfiguration gespeichert, und der Konfigurationsstatus des Repositorys wird angezeigt, einschließlich des Datums und der Uhrzeit der letzten Konfigurationsänderung und der letzten Synchronisierung zwischen der Dienstkonfiguration und dem Repository.

![Konfigurationsstatus][api-management-configuration-status]

Sobald die Konfiguration im Repository gespeichert wurde, kann sie geklont werden.

Informationen zum Ausführen dieses Vorgangs mit der REST-API finden Sie unter [Commit der Konfigurationsmomentaufnahme mithilfe der REST-API](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## So klonen Sie das Repository auf Ihrem lokalen Computer

Um ein Repository zu klonen, benötigen Sie die URL des Repositorys, einen Benutzernamen und ein Kennwort. Der Benutzername und die URL werden oben auf der Registerkarte **Konfigurationsrepository** angezeigt.

![Klonen mit Git][api-management-configuration-git-clone]

Das Kennwort wird unten auf der Registerkarte **Konfigurationsrepository** generiert.

![Kennwort generieren][api-management-generate-password]

Um ein Kennwort zu generieren, stellen Sie zuerst sicher, dass **Ablauf** auf das gewünschte Ablaufdatum und die gewünschte Uhrzeit festgelegt ist, und klicken Sie dann auf **Token generieren**.

![Kennwort][api-management-password]

>[AZURE.IMPORTANT] Notieren Sie sich dieses Kennwort. Das Kennwort wird nicht wieder angezeigt, nachdem Sie diese Seite verlassen haben.

In den folgenden Beispielen wird das Tool Git Bash aus [Git für Windows](http://www.git-scm.com/downloads) verwendet, Sie können aber auch ein beliebiges Git-Tool verwenden, mit dem Sie vertraut sind.

Öffnen Sie Ihr Git-Tool im gewünschten Ordner, und führen Sie den folgenden Befehl zum Klonen des Git-Repositorys auf Ihrem lokalen Computer aus. Verwenden Sie dazu den Befehl aus dem Herausgeberportal.

	git clone https://bugbashdev4.scm.azure-api.net/ 

Geben Sie nach Aufforderung den Benutzernamen und das Kennwort ein.

Wenn Sie Fehlermeldungen erhalten, ändern Sie den Befehl `git clone`, sodass er den Benutzernamen und das Kennwort enthält, wie im folgenden Beispiel gezeigt.

	git clone https://username:password@bugbashdev4.scm.azure-api.net/

Wenn dies zu einem Fehler führt, codieren Sie den Kennwortteil des Befehls als URL. Eine schnelle Möglichkeit, dies zu erreichen, ist, Visual Studio zu öffnen und den folgenden Befehl im **Direktfenster** auszugeben. Um das **Direktfenster** zu öffnen, öffnen Sie eine Projektmappe oder ein Projekt in Visual Studio (oder erstellen Sie eine neue leere Konsolenanwendung), und wählen Sie im Menü **Debuggen** erst **Fenster** und dann **Direkt** aus.

	?System.NetWebUtility.UrlEncode("password from publisher portal")

Verwenden Sie das verschlüsselte Kennwort zusammen mit Ihrem Benutzernamen und dem Repositoryspeicherort, um den Git-Befehl zu erstellen.

	git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Sobald das Repository geklont wurde, können Sie es anzeigen und in Ihrem lokalen Dateisystem verwenden. Weitere Informationen finden Sie unter [Referenz der Datei- und Ordnerstruktur des lokalen Git-Repositorys](#file-and-folder-structure-reference-of-local-git-repository).

## So aktualisieren Sie Ihr lokales Repository mit der aktuellen Dienstinstanzkonfiguration

Wenn Sie Ihre API Management-Dienstinstanz im Herausgeberportal oder mit der REST-API ändern, müssen Sie diese Änderungen im Repository speichern, bevor Sie Ihr lokales Repository mit den neuesten Änderungen aktualisieren können. Klicken Sie hierzu im Herausgeberportal auf der Registerkarte **Konfigurationsrepository** auf **Konfiguration im Repository speichern**, und geben Sie dann den folgenden Befehl im lokalen Repository aus.

	git pull

Stellen Sie vor dem Ausführen von `git pull` sicher, dass Sie sich im Ordner für das lokale Repository befinden. Wenn Sie den Befehl `git clone` gerade abgeschlossen haben, müssen Sie das Verzeichnis in Ihr Repository ändern, indem Sie einen Befehl wie den folgenden ausführen.

	cd bugbashdev4.scm.azure-api.net/

## So übertragen Sie Änderungen aus Ihrem lokalen Repository in das Serverrepository

Um Änderungen aus Ihrem lokalen Repository in das Serverrepository zu übertragen, müssen Sie einen Commit für die Änderungen ausführen und sie dann in das Serverrepository übertragen. Um einen Commit für die Änderungen auszuführen, öffnen Sie Ihr Git-Befehlstool, wechseln Sie in das Verzeichnis des lokalen Repositorys, und geben Sie die folgenden Befehle aus.

	git add --all
	git commit -m "Description of your changes"

Führen Sie den folgenden Befehl aus, um alle Commits auf den Server zu übertragen.

	git push

## So stellen Sie Änderungen an der Dienstkonfiguration der API Management-Dienstinstanz bereit

Sobald ein Commit für Ihre lokalen Änderungen ausgeführt wurde und sie in das Serverrepository übertragen wurden, können Sie sie Ihrer API Management-Dienstinstanz bereitstellen.

![Bereitstellen][api-management-configuration-deploy]

Informationen zum Ausführen dieses Vorgangs mit der REST-API finden Sie unter [Azure API Management-REST-API-Mandanten-Entität](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## Referenz der Datei- und Ordnerstruktur des lokalen Git-Repositorys

Die Dateien und Ordner im lokalen Git-Repository enthalten die Konfigurationsinformationen der Dienstinstanz.

| Item | Beschreibung |
|-------------------------   |--------------------------------------------------------------------------------------------|
| Stammordner „api-management“ | Enthält die Konfiguration der obersten Ebene für die Dienstinstanz |
| Ordner „apis“ | Enthält die Konfiguration für die APIs in der Dienstinstanz |
| Ordner „groups“ | Enthält die Konfiguration für die Gruppen in der Dienstinstanz |
| Ordner „policies“ | Enthält die Richtlinien in der Dienstinstanz |
| Ordner „portalStyles“ | Enthält die Konfiguration für Anpassungen des Entwicklerportals in der Dienstinstanz |
| Ordner „products“ | Enthält die Konfiguration für die Produkte in der Dienstinstanz |
| Ordner „templates“ | Enthält die Konfiguration für die E-Mail-Vorlagen in der Dienstinstanz |

Jeder Ordner kann eine oder mehrere Dateien enthalten, und in einigen Fällen einen oder mehrere Ordner, z. B. ein Ordner für jede API, jedes Produkt oder jede Gruppe. Die Dateien in jedem Ordner gelten für den Entitätstyp, der durch den Namen des Ordners beschrieben wird.

| Dateityp | Zweck |
|-----------|------------------------------------------------------------------------|
| json | Konfigurationsinformationen zur jeweiligen Entität |
| html | Beschreibungen der Entität, werden häufig im Entwicklerportal angezeigt |
| xml | Richtlinienanweisungen |
| css | Stylesheets für die Anpassung des Entwicklerportals |

Diese Dateien können im lokalen System erstellt, gelöscht, bearbeitet und verwaltet werden, und die Änderungen werden wieder Ihrer API Management-Dienstinstanz bereitgestellt.

>[AZURE.NOTE] Die folgenden Entitäten sind im Git-Repository nicht enthalten und können nicht mithilfe von Git konfiguriert werden.
>
>-    Benutzer
>-    Abonnements
>-    Eigenschaften
>-    Andere Entitäten des Entwicklerportals als Stile

### Stammordner „api-management“

Der Stammordner `api-management` enthält eine Datei `configuration.json`, die Informationen der obersten Ebene über die Dienstinstanz im folgenden Format aufweist.

	{
	  "settings": {
	    "RegistrationEnabled": "True",
	    "UserRegistrationTerms": null,
	    "UserRegistrationTermsEnabled": "False",
	    "UserRegistrationTermsConsentRequired": "False",
	    "DelegationEnabled": "False",
	    "DelegationUrl": "",
	    "DelegatedSubscriptionEnabled": "False",
	    "DelegationValidationKey": ""
	  },
	  "$ref-policy": "api-management/policies/global.xml"
	}

Die ersten vier Einstellungen (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled` und `UserRegistrationTermsConsentRequired`) entsprechen den folgenden Einstellungen auf der Registerkarte **Identitäten** im Abschnitt **Sicherheit**.

| Identitätseinstellung | Entsprechung |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled | Kontrollkästchen **Anonyme Benutzer zur Anmeldeseite umleiten** |
| UserRegistrationTerms | Textfeld **Nutzungsbedingungen für die Benutzerregistrierung** |
| UserRegistrationTermsEnabled | Kontrollkästchen **Nutzungsbedingungen auf der Registrierungsseite anzeigen** |
| UserRegistrationTermsConsentRequired | Kontrollkästchen **Zustimmung anfordern** |

![Identitätseinstellungen][api-management-identity-settings]

Die nächsten vier Einstellungen (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled` und `DelegationValidationKey`) entsprechen den folgenden Einstellungen auf der Registerkarte **Delegierung** im Abschnitt **Sicherheit**.

| Delegierungseinstellung | Entsprechung |
|------------------------------|--------------------------------------------|
| DelegationEnabled | Kontrollkästchen **Anmeldung und Registrierung delegieren** |
| DelegationUrl | Textfeld **Delegierungsendpunkt-URL** |
| DelegatedSubscriptionEnabled | Kontrollkästchen **Produktabonnierung delegieren** |
| DelegationValidationKey | Textfeld **Überprüfungsschlüssel delegieren** |

![Delegierungseinstellungen][api-management-delegation-settings]

Die letzte Einstellung, `$ref-policy`, entspricht der globalen Datei mit Richtlinienanweisungen für die Dienstinstanz.

### Ordner „apis“

Der Ordner `apis` enthält einen Ordner für jede API in der Dienstinstanz, der die folgenden Elemente enthält.

-	`apis<api name>\configuration.json`: Dies ist die Konfiguration der API, und die Datei enthält Informationen zur Back-End-Dienst-URL und zu den Vorgängen. Dies sind die gleichen Informationen, die zurückgegeben werden, wenn Sie [Abrufen einer bestimmten API](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) mit `export=true` im Format `application/json` aufrufen.
-	`apis<api name>\api.description.html`: Dies ist die Beschreibung der API, und sie entspricht der Eigenschaft `description` der [Entität „API“](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-	`apis<api name>\operations`: Dieser Ordner enthält `<operation name>.description.html`-Dateien, die den Vorgängen in der API entsprechen. Jede Datei enthält die Beschreibung eines einzelnen Vorgangs in der API, die der Eigenschaft `description` der [Entität „Operation“](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) in der REST-API entspricht.

### Ordner „groups“

Der Ordner `groups` enthält einen Ordner für jede in der Dienstinstanz definierte Gruppe.

-	`groups<group name>\configuration.json`: Dies ist die Konfiguration für die Gruppe. Dies sind die gleichen Informationen, die zurückgegeben werden, wenn Sie den Vorgang [Abrufen einer bestimmten Gruppe](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) aufrufen.
-	`groups<group name>\description.html`: Dies ist die Beschreibung der Gruppe, und sie entspricht der Eigenschaft `description` der [Entität „Group“](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### Ordner „policies“

Der Ordner `policies` enthält die Richtlinienanweisungen für Ihre Dienstinstanz.

-	`policies\global.xml`: Enthält Richtlinien, die im globalen Bereich für Ihre Dienstinstanz definiert sind.
-	`policies\apis<api name>`: Wenn Sie über Richtlinien verfügen, die im API-Bereich definiert sind, sind sie in diesem Ordner enthalten.
-	Ordner `policies\apis<api name><operation name>`: Wenn Sie über Richtlinien verfügen, die im Vorgangsbereich definiert sind, sind sie in diesem Ordner in `<operation name>.xml`-Dateien enthalten, die den Richtlinienanweisungen für jeden Vorgang entsprechen.
-	`policies\products`: Wenn Sie über Richtlinien verfügen, die im Produktbereich definiert sind, sind sie in diesem Ordner enthalten, der `<product name>.xml`-Dateien enthält, die den Richtlinienanweisungen für jedes Produkt entsprechen.

### Ordner „portalStyles“

Der Ordner `portalStyles` enthält Konfigurationen und Stylesheets für Anpassungen des Entwicklerportals für die Dienstinstanz.

-	`portalStyles\configuration.json`: Enthält die Namen der Stylesheets, die vom Entwicklerportal verwendet werden.
-	`portalStyles<style name>.css`: Jede `<style name>.css`-Datei enthält Stile für das Entwicklerportal (standardmäßig `Preview.css` und `Production.css`).

### Ordner „products“

Der Ordner `products` enthält einen Ordner für jedes in der Dienstinstanz definierte Produkt.

-	`products<product name>\configuration.json`: Dies ist die Konfiguration für das Produkt. Dies sind die gleichen Informationen, die zurückgegeben werden, wenn Sie den Vorgang [Abrufen eines bestimmten Produkts](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) aufrufen.
-	`products<product name>\product.description.html`: Dies ist die Beschreibung des Produkts, und sie entspricht der Eigenschaft `description` der [Entität „Product“](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) in der REST-API.

### Vorlagen

Der Ordner `templates` enthält die Konfiguration für die [E-Mail-Vorlagen](api-management-howto-configure-notifications.md) der Dienstinstanz.

-	`<template name>\configuration.json`: Dies ist die Konfiguration für die E-Mail-Vorlage.
-	`<template name>\body.html`: Dies ist der Text der E-Mail-Vorlage.

## Nächste Schritte

Informationen zu anderen Möglichkeiten für die Verwaltung Ihrer Dienstinstanz finden Sie unter:

-	Verwalten der Dienstinstanz mit den folgenden PowerShell-Cmdlets
	-	[Referenz zu PowerShell-Cmdlets für die Dienstbereitstellung](https://msdn.microsoft.com/library/azure/mt619282.aspx)
	-	[Referenz zu PowerShell-Cmdlets für die Dienstverwaltung](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-	Verwalten der Dienstinstanz im Herausgeberportal
	-	[Verwalten Ihrer ersten API](api-management-get-started.md)
-	Verwalten der Dienstinstanz mit der REST-API
	-	[REST-API-Referenz zu API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## Überblicksvideo ansehen

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png

<!---HONumber=AcomDC_0323_2016-->
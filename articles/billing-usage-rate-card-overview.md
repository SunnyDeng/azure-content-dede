<properties
   pageTitle="Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure"
   description="Enthält eine konzeptuelle Übersicht über die Azure-Abrechnungs- und Gebührenkarten-APIs, mit denen Einblicke in den Azure-Ressourcenverbrauch und damit verbundene Trends erlangt werden können."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/14/2015"
   ms.author="mobandyo;bryanla"/>

# Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure 

Kunden und Partner müssen ihre Azure-Kosten genau vorhersagen und verwalten können. Wenn Kunden und Partner die Umstellung von einem CAPEX- zu einem OPEX-Modell vollziehen, müssen sie auch eine „Showback vs. Chargeback“-Analyse durchführen und sich auf eine sichere Schätzung und Abrechnung verlassen können. Dies gilt besonders bei großen Cloudbereitstellungen.

Mit den Azure-APIs zur Ressourcennutzung und Gebührenkarte (Resource Usage und RateCard), die in diesem Artikel beschrieben werden, werden diese Anforderungen erfüllt. Sie ermöglichen neue Einblicke in die Nutzung von Azure-Ressourcen.

## Einführung in die Azure-APIs zur Ressourcennutzung und Gebührenkarte 

Azure-APIs zur Ressourcennutzung und Gebührenkarte werden als Ressourcenanbieter implementiert. Sie sind Teil der API-Familie, die vom Azure-Ressourcen-Manager verfügbar gemacht wird.

### Azure-API zur Ressourcennutzung (Preview)
Kunden und Partner können mit der Azure-API zur Ressourcennutzung ihre geschätzten Azure-Verbrauchsdaten abrufen. Sie enthält folgende Features:
	
- **Rollenbasierte Zugriffssteuerung für Azure** – Kunden und Partner können ihre Zugriffsrichtlinien im [Azure-Vorschauportal](https://portal.azure.com) oder mit [Azure PowerShell-Cmdlets](https://azure.microsoft.com/de-de/documentation/articles/powershell-install-configure/) konfigurieren, um anzugeben, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Außerdem muss dem Aufrufer die Rolle „Leser“, „Besitzer“ oder „Mitwirkender“ zugeordnet sein, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.

- **Stündliche oder tägliche Aggregationen** – Aufrufer können angeben, ob sie ihre Azure-Nutzungsdaten in stündlichen Buckets oder täglichen Buckets erhalten möchten. Die Standardeinstellung ist „Täglich“.

- **Bereitstellung von Instanzmetadaten (inkl. Ressourcentags)** – Details der Instanzebene, z. B. der vollqualifizierte Ressourcen-URI (/subscriptions/{subscription-id}/..) sowie Ressourcengruppeninformationen und Ressourcentags werden in der Antwort angegeben. Damit können Kunden die Nutzung auf deterministische und programmgesteuerte Weise anhand der Tags zuordnen, z. B. bei der internen Verrechnung.

- **Bereitstellung von Ressourcenmetadaten** – Ressourcendetails (z. B. Meter Name, Meter Category, Meter Sub-Category, Unit und Region) werden in der Antwort ebenfalls übergeben, damit Aufrufer besser verstehen, was genutzt wurde. Außerdem arbeiten wir an einer Vereinheitlichung der Terminologie für Ressourcenmetadaten im Azure-Portal, in der CSV-Datei zur Azure-Nutzung, in der CSV-Datei zur EA-Abrechnung und in anderen öffentlich zugänglichen Benutzeroberflächen, um Kunden die Korrelation von Daten über verschiedene Umgebungen hinweg zu ermöglichen.

- **Nutzung für alle Angebotstypen** – Nutzungsdaten werden für alle Angebotstypen verfügbar sein, z. B. Nutzungsbasierte Bezahlung, MSDN, Verbindliche Zusage, Guthaben und EA.

### Azure-API für Ressourcengebührenkarte (Preview)
Kunden und Partner können mit der Azure-Ressourcengebührenkarten-API (Resource RateCard) die Liste der verfügbaren Azure-Ressourcen sowie jeweils die geschätzten Preise abrufen. Sie enthält folgende Features:

- **Rollenbasierte Zugriffssteuerung für Azure** – Kunden und Partner können ihre Zugriffsrichtlinien im [Azure-Vorschauportal](https://portal.azure.com) oder mit [Azure PowerShell-Cmdlets](https://azure.microsoft.com/de-de/documentation/articles/powershell-install-configure/) konfigurieren, um anzugeben, welche Benutzer oder Anwendungen Zugriff auf die Daten der Gebührenkarte erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Außerdem muss dem Aufrufer die Rolle „Leser“, „Besitzer“ oder „Mitwirkender“ zugeordnet sein, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
	
- **Unterstützung für die Angebote Nutzungsbasierte Bezahlung, MSDN, Verbindliche Zusage und Guthaben (EA wird nicht unterstützt)** – Mit dieser API werden Preisinformationen der Azure-Angebotsebene bereitgestellt (im Gegensatz zur Abonnementebene). Aufrufer dieser API müssen die Angebotsinformationen übergeben, um Ressourcendetails und -preise abzurufen. Da EA-Angebote über angepasste Preise pro Registrierung verfügen, können wir derzeit keine EA-Preise angeben.

## Szenarios

Hier sind einige Szenarios angegeben, die aufgrund der Kombination aus Nutzungs- und Gebührenkarten-APIs möglich sind:

- **Azure-Ausgaben während des Monats** – Kunden können die Nutzungs- und Gebührenkarten-APIs zusammen einsetzen, um einen besseren Einblick in ihre Cloudausgaben während eines Monats zu erhalten. Hierzu analysieren sie die stündlichen und täglichen Buckets für die Nutzung und die Gebührenschätzungen. 

- **Einrichten von Warnungen** – Kunden und Partner können ressourcenbasierte oder kostenbasierte Warnungen bezüglich ihrer Cloudnutzung einrichten, indem sie mit den Nutzungs- und Gebührenkarten-APIs den geschätzten Verbrauch und die Gebührenschätzung abrufen.

- **Vorhersagen des Rechnungsbetrags** – Kunden und Partner können ihren geschätzten Verbrauch und die Coudausgaben abrufen und Machine Learning-Algorithmen anwenden, um vorherzusagen, welcher Betrag sich am Ende des Abrechnungszeitraums ergibt.

- **Kostenanalyse vor der Nutzung** – Kunden können mit der Gebührenkarten-API auch vorhersagen, wie hoch ihre Rechnung wäre, wenn sie ihre Workloads auf Azure verlagern. Hierzu können sie die Werte zur gewünschten Nutzung angeben. Wenn Kunden über vorhandene Workloads in anderen Clouds bzw. Private Clouds verfügen, können sie die Nutzung ebenfalls den Azure-Preisen zuordnen, um eine bessere Schätzung der Azure-Ausgaben zu erhalten. Dies verdeutlicht, was mit dem [Azure-Preisrechner](http://azure.microsoft.com/pricing/calculator/) möglich ist. Beispiel: Unsere Abrechnungspartner bieten die Pivot-Funktion für ein Angebot und das Vergleichen und Gegenüberstellen von Angebotstypen über die nutzungsbasierte Bezahlung hinaus an, z. B. verbindliche Zusage und Guthaben. Außerdem sind mit den APIs Kostenschätzungsänderungen nach Region möglich. Dies ermöglicht die Art von Was-wäre-wenn-Analysen, die zum Treffen von Bereitstellungsentscheidungen erforderlich sind, da sich die Bereitstellung von Ressourcen in verschiedenen DCs weltweit direkt auf die Gesamtkosten auswirken kann.

- **Was-wäre-wenn-Analyse** –

	- Kunden und Partner können ermitteln, ob es kostengünstiger wäre, ihre Workloads in einer anderen Region oder unter einer anderen Konfiguration der Azure-Ressource auszuführen. Azure-Ressourcenkosten können je nach der Azure-Region variieren, in der sie ausgeführt werden, sodass Kunden und Partner Kostenoptimierungen vornehmen können.

	- Außerdem können Kunden und Partner ermitteln, ob ein anderer Azure-Angebotstyp zu einem besseren Preis für eine Azure-Ressource führt.

## Partnerlösungen

Unter [Nutzungs- und Gebührenkarten-APIs von Microsoft Azure ermöglichen Cloudyn die ITFM-Bereitstellung für Kunden](billing-usage-rate-card-partner-solution-cloudyn.md) wird die Integrationsumgebung beschrieben, die vom Azure-Abrechnungs-API-Partner [Cloudyn](https://www.cloudyn.com/microsoft-azure/) angeboten wird. Dieser Artikel enthält ausführliche Informationen zu den Umgebungen, darunter ein kurzes Video mit einer Darstellung, wie ein Azure-Kunde Cloudyn und die Azure-Abrechnungs-APIs verwenden kann, um Einblicke in die Azure-Nutzungsdaten zu erhalten.

Unter [Integration von Cloud Cruiser und Microsoft Azure-Abrechnungs-API](billing-usage-rate-card-partner-solution-cloudcruiser.md) wird beschrieben, wie das [Express for Azure Pack von Cloud Cruiser](http://www.cloudcruiser.com/partners/microsoft/) direkt über das WAP-Portal eingesetzt werden kann. So können Kunden sowohl die betrieblichen als auch die finanziellen Aspekte ihrer Microsoft Azure Private Cloud oder gehosteten Public Cloud über eine zentrale Benutzeroberfläche nahtlos verwalten.

## Nächste Schritte
+ Ausführlichere Informationen zu beiden APIs finden Sie unter [Azure-Abrechnungs-REST-API – Referenz](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c). Sie sind Teil der APIs, die vom Azure-Ressourcen-Manager bereitgestellt werden.
+ Wenn Sie direkt in den Beispielcode eintauchen möchten, können Sie dies unter [Codebeispiele für Microsoft Azure-Abrechnungs-API bei GitHub](https://github.com/Azure/BillingCodeSamples) tun.

## Weitere Informationen
+ Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).
+ Weitere Informationen zu den Tools, die zum Entwickeln eines Verständnisses der Cloudausgaben erforderlich sind, finden Sie im Gartner-Artikel [Market Guide for IT Financial Management (ITFM) Tools](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb) (Marktleitfaden für IT Financial Management (ITFM)-Tools).

<!---HONumber=July15_HO1-->
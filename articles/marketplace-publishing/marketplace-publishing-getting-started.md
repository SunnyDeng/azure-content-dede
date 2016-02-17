<properties
   pageTitle="Übersicht über das Erstellen und Bereitstellen eines Angebots im Marketplace | Microsoft Azure"
   description="Beschreibung der Schritte, die ausgeführt werden müssen, um sich als zugelassener Microsoft-Entwickler zu qualifizieren und Angebote für VM-Images, Vorlagen, Datendienste oder Entwicklerdienste im Azure Marketplace zu erstellen und bereitzustellen."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="hascipio" />

# Veröffentlichen eines Angebots im Azure Marketplace
Dieser Artikel soll Verkäufer dabei unterstützen, ihre Lösungen im Azure Marketplace zu erstellen und bereitzustellen, damit sie von anderen Azure-Kunden und -Partnern erworben und genutzt werden können.

Als Herausgeber einer Lösung müssen Sie zunächst definieren, welche Art von Lösung Ihr Unternehmen anbietet. Der Azure Marketplace unterstützt verschiedene Lösungen, und für jeden Lösungstyp müssen für eine erfolgreiche Veröffentlichung im Marketplace geringfügig andere Schritte ausgeführt werden.

Arten von Lösungen:

- VM-Image
- Entwicklerdienst
- Datendienst
- Lösungsvorlage

Einige Schritte müssen für alle Lösungstypen ausgeführt werden. In diesem Artikel erhalten Sie eine kurze Übersicht darüber, welche Schritte für die einzelnen Lösungstypen erforderlich sind.

> [AZURE.NOTE] Bevor Sie im Azure Marketplace arbeiten können, müssen Sie über eine Vorabgenehmigung verfügen. Dies gilt nicht für Herausgeber von Datendiensten.

||VM-Image |Entwicklerdienst | Datendienst | Lösungsvorlage |
|----|----|----|----|----|
| **Erhalten einer Vorabgenehmigung** | [Microsoft Azure Certified][link-certification] | [Microsoft Azure Certified][link-certification] | – | [Microsoft Azure Certified][link-certification] |
| **Schritt 1: Registrieren des Entwicklerkontos** | [Microsoft Developer-Konto: Erstellung und Registrierung][link-accts] | [Microsoft Developer-Konto: Erstellung und Registrierung][link-accts] | [Microsoft Developer-Konto: Erstellung und Registrierung][link-accts] | [Microsoft Developer-Konto: Erstellung und Registrierung][link-accts] |
|**Schritt 2: Erstellen Ihres Angebots**| [Allgemeine nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| [Allgemeine nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| [Allgemeine nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| [Allgemeine nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)|
|| [Technische Voraussetzungen für VMs][link-single-vm-prereq] | Technische Voraussetzungen für Entwicklerdienste | [Technische Voraussetzungen für Datendienste](marketplace-publishing-data-service-creation-prerequisites.md) | [Technische Voraussetzungen für Lösungsvorlagen](marketplace-publishing-solution-template-creation-prerequisites.md) |
||[Anleitung zum Veröffentlichen von VM-Images][link-single-vm] | Anleitung zum Veröffentlichen von Entwicklerdiensten | [Anleitung zum Veröffentlichen von Datendiensten](marketplace-publishing-data-service-creation.md) | [Anleitung zum Veröffentlichen von Lösungsvorlagen](marketplace-publishing-solution-template-creation.md) |
|| [Anleitung für Marketinginhalte in Azure Marketplace][link-pushstaging] | [Anleitung für Marketinginhalte in Azure Marketplace][link-pushstaging] | [Anleitung für Marketinginhalte in Azure Marketplace][link-pushstaging] | [Anleitung für Marketinginhalte in Azure Marketplace][link-pushstaging] |
| **Schritt 3: Freigeben des Angebots in der Stagingphase** | [Testen des VM-Angebots in der Stagingphase](marketplace-publishing-vm-image-test-in-staging.md) | Testen des Entwicklerdienstangebots in der Stagingphase | [Testen des Datendienstangebots in der Stagingphase](marketplace-publishing-data-service-test-in-staging.md) | [Testen des Angebots einer Lösungsvorlage in der Stagingphase](marketplace-publishing-solution-template-test-in-staging.md) |
| **Schritt 4: Bereitstellen des Angebots in Marketplace** | [Bereitstellen des Angebots in Marketplace][link-pushprod] | [Bereitstellen des Angebots in Marketplace][link-pushprod] | [Bereitstellen des Angebots in Marketplace][link-pushprod] | [Bereitstellen des Angebots in Marketplace][link-pushprod] |

## Support
- [Zugriff auf die Publisher-Unterstützung][suppt-general]
- [Grundlegendes zu den Verkäufer-Insights-Berichten][suppt-rpt-insights]
- [Grundlegendes zu den Auszahlungsberichten][suppt-rpt-payouts]
- [Beheben häufiger Probleme bei der Veröffentlichung im Marketplace][suppt-common]

## Zusätzliche Ressourcen
- Weitere Informationen zu den verwendeten Portalen finden Sie unter [Portale, die Sie benötigen](marketplace-publishing-portals.md).

**Virtuelle Computer**

- [Einrichten von Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Erstellen eines lokalen VM-Images](marketplace-publishing-vm-image-creation-on-premise.md)
- [Erstellen eines virtuellen Computers unter Windows im Azure-Vorschauportal](../virtual-machines-windows-tutorial/)

**Datendienste**

- [OData-Zuordnung für Datendienste](marketplace-publishing-data-service-creation-odata-mapping.md)
- [OData-Zuordnungsknoten für Datendienste](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [Beispiele für OData-Zuordnungen für Datendienste](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0204_2016-->
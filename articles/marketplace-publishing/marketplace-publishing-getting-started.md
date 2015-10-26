<properties
   pageTitle="Übersicht über das Erstellen und Bereitstellen eines Angebots im Marketplace | Microsoft Azure"
   description="Beschreibung der Schritte, die ausgeführt werden müssen, um sich als zugelassener Microsoft-Verkäufer zu qualifizieren und Angebote für VM-Images, Vorlagen, Datendienste oder Entwicklerdienste im Azure Marketplace zu erstellen und bereitzustellen."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Veröffentlichen eines Angebots im Microsoft Azure Marketplace
Dieser Artikel soll Verkäufer dabei unterstützen, ihre Lösungen – z. B. einzelne VM-Images, Lösungsvorlagen, Entwicklerdienste oder Datendienste – als Angebot im Azure Marketplace zu erstellen und bereitzustellen, damit diese von anderen Azure-Kunden und -Partnern erworben und verwendet werden können.

Als Herausgeber einer Lösung müssen Sie zunächst definieren, welche Art von Lösung Ihr Unternehmen anbietet. Azure Marketplace unterstützt verschiedene Arten von Lösungen, und für jeden Lösungstyp müssen für eine erfolgreiche Veröffentlichung im Marketplace geringfügig andere Schritte ausgeführt werden.

**Arten von Lösungen:**

- Datendienste
- Entwicklerdienste
- Virtuelle Computer (VMs)
- Lösungsvorlagen

Einige Schritte müssen für alle Lösungstypen ausgeführt werden. Nachfolgend erhalten Sie einen kurzen Überblick darüber, welche Schritte für alle Lösungstypen erforderlich sind.

**Bevor Sie im Azure Marketplace arbeiten können, müssen Sie über eine Vorabgenehmigung verfügen. Dies gilt nicht für Herausgeber von Datendiensten.**

||VM-Image |Entwicklerdienst | Datendienst | Lösungsvorlage | |----|----|----|----|----| | **Vorabgenehmigung anfordern** | [Microsoft Azure Certified][link-certification] | [Microsoft Azure Certified][link-certification] | – | [Microsoft Azure Certified][link-certification] | | **Schritt 1. Registrieren eines Verkäuferkontos** | [Microsoft-Verkäuferkonto: Erstellung und Registrierung][link-accts] | [Microsoft-Verkäuferkonto: Erstellung und Registrierung][link-accts] | [Microsoft-Verkäuferkonto: Erstellung und Registrierung][link-accts] | [Microsoft-Verkäuferkonto: Erstellung und Registrierung][link-accts] | |**Schritt 2. Erstellen Ihres Angebots**| [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| [Allgemeine, nicht technische Voraussetzungen](marketplace-publishing-pre-requisites.md)| || [Technische Voraussetzungen für VMs][link-single-vm-prereq] | Technische Voraussetzungen für Entwicklerdienste | Technische Voraussetzungen für Datendienste | [Technische Voraussetzungen für Lösungsvorlagen](marketplace-publishing-solution-template-creation-prerequisites.md) | ||[Veröffentlichungsleitfaden für VM-Image][link-single-vm] | Veröffentlichungsleitfaden für Entwicklerdienste | Veröffentlichungsleitfaden für Datendienste | [Veröffentlichungsleitfaden für Lösungsvorlagen](marketplace-publishing-solution-template-creation.md) | || [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | [Leitfaden zu Azure Marketplace-Marketinginhalten][link-pushstaging] | | **Schritt 3. Überführen des Angebots in die Stagingumgebung** | [Testen Ihres VM-Angebots in der Stagingumgebung](marketplace-publishing-vm-image-test-in-staging.md) | Testen Ihres Entwicklerdiensts in der Stagingumgebung | Testen Ihres Datendiensts in der Stagingumgebung | [Testen Ihrer Lösungsvorlage in der Stagingumgebung](marketplace-publishing-solution-template-test-in-staging.md) | | **Schritt 4. Bereitstellen Ihres Angebots im Marketplace** | [Bereitstellen Ihres Angebots im Marketplace][link-pushprod] | [Bereitstellen Ihres Angebots im Marketplace][link-pushprod] | [Bereitstellen Ihres Angebots im Marketplace][link-pushprod] | [Bereitstellen Ihres Angebots im Marketplace][link-pushprod] |

## Support
- [Support für Herausgeber anfordern][suppt-general]
- [Grundlegendes zu den Verkäufer-Insights-Berichten][suppt-rpt-insights]
- [Grundlegendes zu den Auszahlungsberichten][suppt-rpt-payouts]
- [Beheben häufiger Probleme bei der Veröffentlichung im Marketplace][suppt-common]

## Zusätzliche Ressourcen
- Weitere Informationen zu den verwendeten Portalen finden Sie unter [Benötigte Portale](marketplace-publishing-portals.md).

**Virtuelle Computer**

- [Einrichten von Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Erstellen eines lokalen VM-Images](marketplace-publishing-vm-image-creation-on-premise.md)
- [Erstellen eines virtuellen Computers unter Windows im Azure-Vorschauportal](../virtual-machines-windows-tutorial/)

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

<!---HONumber=Oct15_HO3-->
<properties
   pageTitle="Testen Ihres VM-Angebots für Marketplace | Microsoft Azure"
   description="Grundlegendes zum Testen Ihres VM-Images für Azure Marketplace."
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
   ms.date="02/01/2016"
   ms.author="hascipio" />

# Testen Ihres VM-Angebots für den Azure Marketplace in der Stagingumgebung

In der Stagingumgebung wird Ihr Angebot in einem privaten "Sandkasten" bereitgestellt, in dem Sie seine Funktionalität vor der Bereitstellung in Marketplace testen und überprüfen können. Die SKU wird in der Stagingumgebung genau wie bei einem Kunden angezeigt, der sie bereitgestellt hat. Ihr VM-Image muss für die Freigabe in der Stagingumgebung zertifiziert sein.

## Schritt 1: Freigeben Ihres Angebot in der Stagingumgebung

1. Klicken Sie auf der Registerkarte **Veröffentlichen** auf **Für Stagingumgebung freigeben**.

  ![Abbildung](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Sollten Sie vom Veröffentlichungsportal auf Fehler hingewiesen werden, beheben Sie sie.
3.	Geben Sie im Dialogfeld für den Zugriff auf Ihr in der Stagingumgebung bereitgestelltes Angebot die Azure-Abonnements an, die Sie für die Vorschau Ihres Angebots im [Azure-Vorschauportal](https://portal.azure.com) verwenden möchten.
4. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) mit einem der Azure-Abonnements aus dem vorherigen Schritt an.
5. Suchen Sie Ihr Angebot, und überprüfen Sie für Ihr VM-Image die folgenden Punkte:
  - Vergewissern Sie sich, dass Marketinginhalte im Marketplace korrekt angezeigt werden.
  - End-to-End-Bereitstellung des VM-Images
  
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)




> [AZURE.IMPORTANT] Ihr Angebot bleibt so lange in der Stagingphase, bis Sie Microsoft über das Veröffentlichungsportal [**Veröffentlichen** > **Genehmigung für Freigabe in Produktionsumgebungen anfordern**] informieren, dass Ihr Angebot für die Veröffentlichung bereit ist. Vor der Veröffentlichung Ihres Angebots sollte das gesamte Team noch einmal alles überprüfen.

> Die Replikation in allen Datencentern dauert bis zu 48 Stunden. Nach Abschluss der Replikation wird Ihr Angebot im [Azure Marketplace](https://azure.microsoft.com/marketplace/) gelistet.

## Nächste Schritte
Nachdem Ihr Angebot in die Stagingphase versetzt wurde und Sie die Funktionalität und die Marketinginhalte getestet haben, können Sie zur letzten Veröffentlichungsphase übergehen: **Schritt 4**: [Bereitstellen Ihres Angebots im Marketplace](marketplace-publishing-push-to-production.md).

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0204_2016-->
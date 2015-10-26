<properties
   pageTitle="Testen Ihres VM-Angebots für Marketplace | Microsoft Azure"
   description="Grundlegendes zum Testen Ihres VM-Images für Azure Marketplace."
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

# Testen Ihres VM-Angebots für Marketplace in der Stagingumgebung

In der Stagingumgebung wird Ihr Angebot in einem privaten "Sandkasten" bereitgestellt, in dem Sie seine Funktionalität vor der Bereitstellung in Marketplace testen und überprüfen können. Die SKU wird in der Stagingumgebung genau wie bei einem Kunden angezeigt, der es bereitgestellt hat. Ihr VM-Image muss für die Freigabe in der Stagingumgebung zertifiziert sein.

## Schritt 1: Freigeben Ihres Angebot in der Stagingumgebung

1. Klicken Sie auf der Registerkarte **Veröffentlichen** auf **Für Stagingumgebung freigeben**.

  ![Abbildung](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Korrigieren Sie etwaige Fehler oder Abweichungen, über die Sie das Veröffentlichungsportal an dieser Stelle informiert.
3.	Geben Sie, wie im obigen Bildschirmfoto gezeigt, im Popupfeld die Liste der Azure-Abonnements an, mit denen Sie Ihr Angebot im [Azure-Vorschauportal](https://portal.azure.com) testen möchten.
4. Melden Sie sich am [Azure-Vorschauportal](http://portal.azure.com) mit einem der Azure-Abonnements an, die im vorherigen Schritt aufgelistet wurden.
5. Suchen Sie Ihr Angebot, und überprüfen Sie für Ihr VM-Image die folgenden Punkte:
  1. Marketinginhalte werden im Katalog ordnungsgemäß angezeigt.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. Bereitstellung des VM-Images von A bis Z.

> [AZURE.IMPORTANT]Ihr Angebot verbleibt so lange in der Stagingphase, bis Sie Microsoft über das Veröffentlichungsportal [**Veröffentlichen** > **Genehmigung für Freigabe in Produktionsumgebungen anfordern**] informieren, dass Ihr Angebot für die Veröffentlichung bereit ist. Nun sollten alle Mitarbeiter des Teams alle Eingaben noch einmal überprüfen, bevor Ihr Angebot veröffentlicht wird.

> Die Replikation in allen Datencentern dauert 24-48 Stunden. Nachdem die Replikation abgeschlossen ist, wird Ihr Angebot in [Azure Marketplace](http://azure.microsoft.com/marketplace) gelistet.

## Nächste Schritte
Nun da sich Ihr Angebot in der Stagingphase befindet und Sie seine Funktionalität und seinen Marketinginhalt getestet haben, können Sie mit der letzten Phase zum Veröffentlichen Ihres Angebots fortfahren: **Schritt 4**, [Bereitstellen Ihres Angebots in Marketplace](marketplace-publishing-push-to-production.md).

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->
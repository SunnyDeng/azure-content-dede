<properties
	pageTitle="Grundlegendes zu Azure Marketplace-Gebühren | Microsoft Azure"
	description="Es wird beschrieben, wie Sie Ihre Marketplace-Bestellungen besser verstehen."
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="cjiang"/>

# Grundlegendes zu Azure Marketplace-Gebühren
Dieser Artikel enthält grundlegende Informationen zu den Gebühren, die für Ihre Marketplace-Bestellungen gelten. Einige Punkte, die beim Prüfen Ihrer Rechnung zu beachten sind, sind die unterschiedlichen Preistypen („Nur Basis“, „Nur Nutzung“ und „Basis und Nutzung“). Beachten Sie, dass jede von Ihnen aufgegebene Marketplace-Bestellung über einen eigenen monatlichen Abrechnungszyklus verfügt. Diese Themen werden unten ausführlich behandelt.

## Prüfen Ihrer Marketplace-Rechnung
Nachdem Sie sich am [Azure-Kontoportal](https://account.windowsazure.com/subscriptions/) angemeldet haben, können Sie aktuelle und ältere Rechnungen prüfen und herunterladen.

So zeigen Sie eine Rechnung an oder laden sie herunter

1. Melden Sie sich mit Ihrer Organisations-ID am Azure-Kontoportal an.
2. Klicken Sie auf **Marketplace**. Es wird eine vollständige Liste mit allen Marketplace-Bestellungen angezeigt, die über Ihr Azure-Konto aufgegeben wurden. Sie sehen, dass die Bestellungen nach Name und ID des übergeordneten Azure-Abonnements gruppiert sind. ![Marketplace-Bestellungen](./media/billing-understand-your-azure-marketplace-charges/marketplace-orders.png)
3. Wenn Sie auf eine der angezeigten Bestellungen klicken, gelangen Sie zur Zusammenfassung dieser Bestellung für den aktuellen Abrechnungszyklus. Eine Beschreibung der unterschiedlichen Preismodelle finden Sie unter [Häufig gestellte Fragen zum Marketplace](https://azure.microsoft.com/marketplace/faq/). ![Bestellübersicht](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

Die Zusammenfassung enthält zu jedem Auftrag folgende Informationen:
- Preis
- Billing Cycle
- Kaufdatum
- Zugehöriges übergeordnetes Abonnement
- Rechnungswährung

Beachten Sie hierbei, dass wir einige Änderungen an der Anzeige dieser Informationen vorgenommen haben. Bisher wurden die Kosten der Bestellung inklusive Steuern angezeigt. Dies wurde aktualisiert. Es wird nun der Preis angezeigt, der je nach Land entweder inklusive oder exklusive Steuern gilt. Außerdem wurde die Position des Preises von der rechten Seite in die Mitte verschoben. Damit Sie besser verfolgen können, zu welchem Azure-Abonnement diese Gebühren gehören, haben wir den Navigationselementen auf der rechten Seite den Namen und die ID des Azure-Abonnements sowie die Option **Zahlungsmethode ändern** hinzugefügt.

## Aktualisieren der Zahlungsmethode und Bestellungsverwaltung
Die Seite mit der Zusammenfassung verfügt über Benutzeraktionen, sodass Sie die Funktionen für das Zahlungsmodell und die Bestellungsverwaltung aktualisieren können:

> [AZURE.NOTE] Wenn Sie Ihre Organisations-ID zum Ändern von persönlichen Informationen verwenden, müssen Sie beim Support ein Ticket erstellen.

Klicken Sie zum Aktualisieren der Zahlungsmethode rechts auf der Seite auf den Link **Zahlungsmethode ändern**. ![Bestellübersicht](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

Mit diesem Link gelangen Sie zu einem anderen Portal, in dem Sie Änderungen an der bevorzugten Zahlungsmethode vornehmen können.

Führen Sie die folgenden Schritte aus, um Ihre Zahlungsmethode zu ändern:

1. Klicken Sie auf **Zahlungsweise ändern**. ![Abonnements](./media/billing-understand-your-azure-marketplace-charges/subscriptions.jpg)
2. Wählen Sie die Zahlungsmethode aus, zu der Sie wechseln möchten. Bei der Option **Zahlung per** können Sie Ihre Kreditkarte auswählen. Bei der Option **Neue Zahlungsmethode hinzufügen** können Sie eine neue Kreditkarte hinzufügen. ![Zahlungsmethode ändern](./media/billing-understand-your-azure-marketplace-charges/change-payment-method.jpg)

## Bestellungsverwaltung
Auf der Seite mit der Zusammenfassung wird der Link **Bestellung verwalten** angezeigt. Mit diesem Link gelangen Sie zu Ihrem Portal, in dem Sie alle Marketplace-Bestellungen anzeigen, die Dienstintegrität verfolgen und Support anfordern können. ![Portal](./media/billing-understand-your-azure-marketplace-charges/portal.jpg)

Klicken Sie zum Anfordern von Support auf die Kachel **Hilfe und Support**. Daraufhin wird die folgende Seite **Hilfe und Support** angezeigt, auf der Sie Folgendes tun können:
- Öffnen Sie eine Supportanfrage.
- Verwalten Sie vorhandene Supportanfragen.
- Verwalten Sie die Ressourcenintegrität.

![Anfordern von Support](./media/billing-understand-your-azure-marketplace-charges/request-support.jpg)

## Abrechnungsverlauf
Ein neues Feature auf der Zusammenfassungsseite ist das Anzeigen von nutzungsbasierten Angeboten, z. B. virtuellen Maschinen. Sie können jetzt sowohl für aktuelle als auch für vergangene Perioden die Nutzungsdaten nach Abrechnungszyklus herunterladen. Klicken Sie zum Anzeigen der Nutzungsdaten auf den Link **Nutzung herunterladen** für den gewünschten Zeitraum.

Falls Sie die tatsächlichen Gebühren aufrufen möchten, können Sie auf **Gebühren anzeigen** klicken. Sie werden an ein anderes Portal weitergeleitet, in dem Sie alle Gebühren einschließlich Steuern anzeigen können. Wenn Sie eine Organisations-ID verwenden, ist die Schaltfläche **Gebühren anzeigen** nicht aktiviert. Sie müssen ein Supportticket erstellen, um eine Zusammenfassung Ihrer Gebühren anzufordern. ![Abrechnungsverlauf](./media/billing-understand-your-azure-marketplace-charges/billing-history.png)

Führen Sie die folgenden Schritte aus, um die tatsächlichen Gebühren anzuzeigen:

1. Klicken Sie auf der Seite **ABRECHNUNGSVERLAUF** auf **Gebühren anzeigen**. Eine neue Registerkarte mit dem [Bestellverlauf](https://account.microsoft.com/billing/orders#/) wird geöffnet. Beachten Sie Folgendes: Wenn Sie versuchen, im Portal für die Abrechnungs- und Kontoverwaltung auf die Rohdaten der Nutzung zuzugreifen, müssen Sie sich an den Support wenden. Klicken Sie hierfür unten auf der Seite auf den Link "Support". ![Bestellverlauf](./media/billing-understand-your-azure-marketplace-charges/order-history.jpg)
2. Wählen Sie die Bestellung aus, die Sie prüfen möchten, und klicken Sie dann auf **Details**, um eine Aufstellung Ihrer Gebühren anzuzeigen. Die Aufstellung enthält Punkte wie Zwischensumme, Steuern und Gesamtgebühren. ![Bestelldetails](./media/billing-understand-your-azure-marketplace-charges/order-details.jpg)

<!---HONumber=AcomDC_0309_2016-->
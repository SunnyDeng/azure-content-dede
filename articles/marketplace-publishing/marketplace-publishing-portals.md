<properties
   pageTitle="Übersicht über die verschiedenen Portale, die zum Erstellen eines Angebots für den Marketplace benötigt werden | Microsoft Azure"
   description="Übersicht über die verschiedenen Portale, die zum Erstellen eines Angebots für den Marketplace benötigt werden"
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
   ms.date="10/05/2015"
   ms.author="hascipio" />


# Portale, die Sie benötigen
Bevor Sie mit der Veröffentlichung eines Angebots beginnen, sollten Sie sich mit den verschiedenen Portalen vertraut machen, die Sie dazu benötigen. Im Anschluss folgt eine kurze Zusammenfassung der Portale (Verkäuferdashboard, Veröffentlichungsportal und Azure-Vorschauportal) in der Reihenfolge, in der Sie mit ihnen interagieren.
## Verkäuferdashboard
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### Beschreibung
Das Erstellen Ihres Verkäuferdashboard-Kontos ist eine einmalige Angelegenheit. Vergewissern Sie sich vor der Kontoerstellung, dass Ihr Unternehmen nicht bereits über ein Verkäuferdashboard-Konto verfügt. Während des Prozesses erfassen wir Bankdaten, Steuerinformationen und Adressdaten Ihres Unternehmens.

> [AZURE.NOTE]Wenn Sie nur kostenlose Angebote (oder Angebote mit Verwendung Ihrer eigenen Lizenz) veröffentlichen, benötigen wir keine steuerlichen Informationen oder Bankdaten.

### Verwendete Identität/Verwendetes Konto
Im Idealfall eine Verteilerliste oder Sicherheitsgruppe (z. B. azurepublishing@*partnercompany*.com)). Diese Verteilerliste oder Sicherheitsgruppe **muss** als Microsoft-Konto registriert sein.

> [AZURE.TIP]Wir empfehlen die Verwendung einer Verteilerliste oder Sicherheitsgruppe, um die Abhängigkeit von einer Einzelperson zu vermeiden. Es kann jedoch auch ein einzelnes Konto verwendet werden.

## Veröffentlichungsportal
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Beschreibung
Dieses Portal dient zum Bearbeiten und Veröffentlichen des Angebots. Hierzu zählen unter anderem Marketing, Preisgestaltung, Veröffentlichung und ggf. Zertifizierung.

### Verwendete Identität/Verwendetes Konto
Die weiter oben erwähnte Verteilerliste oder Sicherheitsgruppe muss bei der erstmaligen Anmeldung beim Veröffentlichungsportal verwendet werden. Später können dann weitere Benutzer als Co-Admins hinzugefügt werden. So geschieht die Zuordnung zu den Registrierungsdaten im Verkäuferdashboard.

## Azure-Vorschauportal
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### Beschreibung
Dies ist das Portal, in dem Sie Ihre bereitgestellten und veröffentlichten Angebote im Azure Marketplace anzeigen können (gilt für virtuelle Computer, Lösungsvorlagen und auf dem Azure-Ressourcen-Manager basierende Entwicklerdienste).
### Verwendete Identität/Verwendetes Konto
Beim Bereitstellen des Angebots über das Veröffentlichungsportal muss eine Abonnement-ID auf die Whitelist gesetzt werden. Das gleiche Abonnement (mit zugeordnetem Benutzernamen und Kennwort) muss bei der Portalanmeldung verwendet werden, um das bereitgestellte Angebot zu testen.

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO4-->
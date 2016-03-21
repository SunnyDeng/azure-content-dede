<properties
	pageTitle="Azure AD Connect-Synchronisierung: Synchronization Service Manager-Benutzeroberfläche | Microsoft Azure"
	description="Grundlegende Informationen zur Registerkarte „Connectors“ in Synchronization Service Manager für Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="andkjell"/>


# Azure AD Connect-Synchronisierung: Synchronization Service Manager

| [Vorgänge](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connectors](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse-Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Metaverse-Suche](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Die Registerkarte „Connectors“ wird verwendet, um alle Systeme zu verwalten, mit denen das Synchronisierungsmodul verbunden ist.

## Connectoraktionen

| Aktion | Kommentar |
| --- | --- |
| Erstellen | Nicht verwenden. Verwenden Sie den Installations-Assistenten zum Verbinden mit weiteren Active Directory-Gesamtstrukturen. |
| Eigenschaften | Wird zum Filtern von Domänen und Organisationseinheiten verwendet. |
| Löschen | Wird zum Löschen der Daten im Connectorbereich oder zum Löschen der Verbindung mit einer Gesamtstruktur verwendet. |
| Ausführungsprofile konfigurieren | Hier muss nur das Filtern von Domänen konfiguriert werden. |
| Ausführen | Wird zum Starten einer einmaligen Ausführung eines Profils verwendet. |
| Beenden | Beendet einen Connector, der gerade ein Profil ausführt. |
| Connector exportieren | Nicht verwenden. |
| Connector importieren | Nicht verwenden. |
| Connector aktualisieren | Nicht verwenden. |
| Schema aktualisieren | Aktualisiert das zwischengespeicherte Schema. Sie sollten die Option im Installations-Assistenten verwenden, da damit auch die Synchronisierungsregeln aktualisiert werden.
| Connectorbereich durchsuchen | Wird verwendet, um Objekte zu finden und um [ein Objekt und dessen Daten durch das System zu verfolgen](#follow-an-object-and-its-data-through-the-system). |

### Löschen
Die Löschaktion wird für zwei verschiedene Dinge verwendet. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Mit der Option **Nur Connectorbereich löschen** werden alle Daten entfernt, aber die gesamte Konfiguration bleibt erhalten.

Mit der Option **Connector und Connectorbereich löschen** werden alle Daten und die gesamte Konfiguration entfernt. Diese Option wird verwendet, wenn Sie mit einer Gesamtstruktur keine Verbindung mehr herstellen möchten.

Mit beiden Optionen werden alle Objekte synchronisiert und die Metaverse-Objekte aktualisiert. Dieser Vorgang dauert lange.

### Connectorbereich durchsuchen
Die Aktion zum Durchsuchen des Connectorbereichs ist nützlich, um nach Objekten zu suchen und Probleme zu behandeln.

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Wählen Sie zuerst einen **Bereich** aus. Sie können basierend auf Daten (RDN, DN, Anker, Teilstruktur) oder des Zustands des Objekts (alle anderen Optionen) suchen. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png) Wenn Sie beispielsweise eine Unterstruktur durchsuchen, erhalten Sie alle Objekte in einer Organisationseinheit. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) Hier können Sie ein Objekt auswählen, **Eigenschaften** auswählen und das Objekt aus dem Quellconnectorbereich durch das Metaverse und zum Zielconnectorbereich [verfolgen](#follow-an-object-and-its-data-through-the-system).

## Verfolgen eines Objekts und dessen Daten durch das System
Wenn Sie ein Problem mit Daten behandeln möchten, ist das Verfolgen eines Objekts vom Quellconnectorbereich zum Metaverse und zum Zielconnectorbereich ein wichtiges Verfahren, um zu verstehen, warum die Daten nicht die erwarteten Werte aufweisen.

### Eigenschaften der Objekte des Connectorbereichs
**Import** Beim Öffnen eines CS-Objekts befinden sich oben mehrere Registerkarten. Auf der Registerkarte **Import** werden die Daten angezeigt, die nach einem Import bereitgestellt werden. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) Unter **Alter Wert** wird dargestellt, was derzeit im System gespeichert ist, und unter **Neuer Wert**, was aus dem Quellsystem empfangen wurde, aber noch nicht angewendet wurde. Da in diesem Fall ein Synchronisierungsfehler vorliegt, kann die Änderung angewendet werden.

**Fehler** Die Fehlerseite wird nur angezeigt, wenn ein Problem mit dem Objekt besteht. Unter den Details auf der Seite mit Vorgängen finden Sie weitere Informationen zur [Problembehandlung bei Synchronisierungsfehlern](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Herkunft** Auf der Registerkarte für die Herkunft wird gezeigt, wie das Objekt des Connectorbereichs mit dem Metaverse-Objekt verknüpft ist. Wir sehen, wann wir zuletzt eine Änderung aus dem verbundenen System importiert haben und welche Regeln zum Auffüllen der Daten im Metaverse angewendet werden. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) In der Spalte **Aktion** können wir sehen, dass es eine Synchronisierungsregel **Eingehend** mit der Aktion **Bereitstellen** gibt. Damit wird angegeben, dass das Metaverse-Objekt erhalten bleibt, solange dieses Objekt des Connectorbereichs vorhanden ist. Wenn die Liste der Synchronisierungsregeln stattdessen eine Synchronisierungsregel mit Richtung **Ausgehend** und **Bereitstellen** aufweist, wird angegeben, dass dieses Objekt gelöscht wird, wenn das Metaverse-Objekt gelöscht wird. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) Darüber hinaus sehen wird in der Spalte für die **Kennwortsynchronisierung**, dass der eingehende Connectorbereich Änderungen am Kennwort beitragen kann, da eine Synchronisierungsregel den Wert **True** aufweist. Dieses Kennwort wird anschließend über die ausgehende Regel an Azure AD gesendet.

Auf der Registerkarte für die Herkunft gelangen Sie zum Metaverse, indem Sie auf [Metaverse-Objekteigenschaften](#metaverse-object-properties) klicken.

Unterhalb der Registerkarten befinden sich zwei Schaltflächen: **Vorschau** und **Protokoll**.

**Vorschau** Die Vorschauseite wird verwendet, um ein einzelnes Objekt zu synchronisieren. Sie ist nützlich, wenn Sie Probleme mit den Synchronisierungsregeln einiger Kunden behandeln und die Auswirkungen einer Änderung auf ein einzelnes Objekt sehen möchten. Sie können zwischen **Vollständige Synchronisierung** und **Deltasynchronisierung** auswählen. Sie können auch zwischen **Vorschau generieren**, um die Änderung nur im Arbeitsspeicher beizubehalten, und **Commit der Vorschau ausführen**, um alle Änderungen im Zielconnectorbereich bereitzustellen, auswählen. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) Sie können das Objekt und die für einen bestimmten Attributfluss angewendete Regel prüfen. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Protokoll** Die Protokollseite wird verwendet, um Status und Verlauf der Kennwortsynchronisierung anzuzeigen. Weitere Informationen finden Sie unter [Problembehandlung bei der Kennwortsynchronisierung](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).

### Metaverse-Objekteigenschaften
**Attribute** Auf der Registerkarte der Attribute sehen Sie die Werte und den Connector, von dem sie stammen. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png) **Connectors** Auf der Registerkarte der Connectors werden alle Connectorbereiche angezeigt, die über eine Darstellung des Objekts verfügen. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) Auf dieser Registerkarte können Sie auch zum [Objekt des Connectorbereichs](#connector-space-object-properties) navigieren.

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0309_2016-->
<properties
	pageTitle="Azure AD Connect-Synchronisierung: Synchronization Service Manager-Benutzeroberfläche | Microsoft Azure"
	description="Verstehen Sie die Registerkarte „Vorgänge“ in Synchronization Service Manager für Azure AD Connect."
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

| [Vorgänge](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connectors](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Metaverse Search](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Die Registerkarte „Vorgänge“ zeigt die Ergebnisse der letzten Vorgänge. Diese Registerkarte ist sehr wichtig, um Probleme zu verstehen und zu beheben.

## Verstehen der Informationen, die in der Registerkarte „Vorgänge“ angezeigt werden
Die obere Hälfte zeigt alle Ausführungen in einer chronologischen Reihenfolge. Standardmäßig behält das Vorgangsprotokoll Informationen der letzten 7 Tagen bei; dies können Sie mit dem [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) ändern. Sie wollen nach allen Ausführungen suchen, die nicht den Status „success“ anzeigen. Sie können die Sortierung durch Klicken auf die Kopfzeilen ändern.

Die **Status** Spalte gibt Ihnen die wichtigste Information und zeigt das schwerwiegendste Problem einer Ausführung. Hier folgt eine kurze Zusammenfassung der häufigsten Status, in der Reihenfolge ihrer Untersuchungspriorität (bei „*“ gibt es mehrere mögliche Fehlerzeichenfolgen).

| Status | Kommentar |
| --- | --- |
| stopped-* | Die Ausführung konnte nicht abgeschlossen werden. Beispielsweise wenn das Remotesystem ausgefallen ist und nicht kontaktiert werden kann. |
| stopped-error-limit | Es gibt mehr als 5.000 Fehler. Die Ausführung wurde aufgrund der großen Anzahl von Fehlern automatisch beendet. |
| completed-*-errors | Die Ausführung ist abgeschlossen, jedoch sind Fehler aufgetreten (weniger als 5.000) die untersucht werden sollten. | | completed-*-warnings | Die Ausführung wurde abgeschlossen, einige Daten sind jedoch nicht im erwarteten Zustand. Wenn Fehler auftreten, ist dies normalerweise nur ein Symptom. Bis Sie die Fehler behoben haben, sollten Sie keine Warnungen untersuchen. |
| Erfolg | Keine Probleme |

Wenn Sie eine Zeile auswählen, wird der unteren Bereich aktualisiert, und die Details dieser Ausführung angezeigt. Ganz links neben dem unteren Teil erscheint möglicherweise eine Liste mit **Schritt #**. Diese wird nur angezeigt, wenn Sie mehrere Domänen in Ihrer Gesamtstruktur haben, und jede Domäne als ein Schritt dargestellt wird. Den Domänennamen finden Sie unter der Überschrift **Partition**. Unter **Synchronisierungsstatistiken** finden Sie weitere Informationen über die Anzahl der Änderungen, die verarbeitet wurden. Sie können auf die Links klicken, um eine Liste der geänderten Objekte zu erhalten. Wenn Sie Objekte mit einem Fehler haben, werden diese unter **Synchronisierungsfehler** angezeigt.

## Fehlerbehandlung in der Registerkarte „Vorgänge“
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png) Wenn Fehler auftreten, werden sowohl das Objekt im Fehler als auch der Fehler selbst zu Links, die weitere Informationen bieten.

Starten Sie durch Klicken auf die Fehlerzeichenfolge (in der Abbildung oben **sync-rule-error-function-triggered**). Es wird Ihnen zunächst eine Übersicht über das Objekt angezeigt. Um den tatsächlichen Fehler anzuzeigen, klicken Sie auf die Schaltfläche **Stapelüberwachung**. Dadurch werden Informationen der Debugebene für den Fehler angezeigt.

**Tipp:** Sie können in den **Aufruflisteninformationen** mit der rechten Maustaste auf **Alle auswählen** klicken, und **Kopieren**. Sie können dann den Stapel kopieren und den Fehler in Ihrem bevorzugten Editor, z. B. „Notepad“ betrachten.

- Wenn der Fehler aus **SyncRulesEngine** stammt, werden die Aufruflisteninformationen zuerst eine Liste aller Attribute für das Objekt beinhalten. Scrollen Sie nach unten, bis Sie die Überschrift **InnerException = >** sehen. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png) In der nächsten Zeile wird der Fehler angezeigt. Der Fehler in der obigen Abbildung stammt aus einer benutzerdefinierten Synchronisationsregel, die von Fabrikam erstellt wurde.

Wenn der Fehler selbst nicht genügend Informationen liefert, ist es an der Zeit, sich die Daten selbst anzusehen. Klicken Sie auf den Link mit dem Objektbezeichner und [Verfolgen Sie ein Objekt und seine Daten durch das System](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0309_2016-->
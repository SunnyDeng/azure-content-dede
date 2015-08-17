<properties
   pageTitle="Verwalten von Servern und Nutzungsdaten"
   description="Erfahren Sie, wie viele Daten von Ihren Servern an den Operational Insights-Dienst gesendet werden"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders" />


# Verwalten von Servern und Nutzungsdaten

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights sammelt Daten und sendet diese regelmäßig an den Operational Insights-Dienst. Im Dashboard **Nutzung** können Sie anzeigen, wie viele Daten an den Operational Insights-Dienst gesendet werden. Das Dashboard **Nutzung** zeigt Ihnen auch, wie viele Daten täglich von Lösungen gesendet werden und wie häufig die Verwaltungsgruppen Daten senden.

>[AZURE.NOTE]Wenn Sie ein kostenloses Konto verwenden, ist die Datenmenge, die täglich an den Operational Insights-Dienst gesendet wird, auf 500 MB begrenzt. Ist die Tagesgrenze erreicht, wird die Datenanalyse beendet und zu Beginn des nächsten Tages fortgesetzt.

Sie können Ihre Nutzung über die Kachel **Server und Verwendung** im Dashboard **Übersicht** in Operational Insights anzeigen.

![Abbildung der Kachel "Server und Verwendung"](./media/operational-insights-usage/overview-servers-usage.png)

Wenn Sie die tägliche Nutzungsgrenze überschritten haben oder sich dem Grenzwert nähern, können Sie optional eine Lösung entfernen, um die an den Operational Insights-Dienst gesendete Datenmenge zu reduzieren. Weitere Informationen zum Entfernen von Lösungen finden Sie unter [Verwenden von Solutions Gallery zum Hinzufügen oder Entfernen von Lösungen](operational-insights-setup-workspace.md).

Wenn eine Operations Manager-Verwaltungsgruppe beim Senden von Daten an den Operational Insights-Dienst Probleme feststellt, können Sie das Problem beheben oder die Gruppe nach Bedarf aus Operational Insights entfernen.

![Abbildung des Nutzungs-Dashboards](./media/operational-insights-usage/usage-dash.png)

Im Dashboard **Nutzung** werden die folgenden Informationen angezeigt:

- Durchschnittliche Nutzung pro Tag

- Datennutzung für jede Lösung für den heutigen Tag

- Wie häufig die Server in den einzelnen Verwaltungsgruppen Daten an den Operational Insights-Dienst senden

## So arbeiten Sie mit Nutzungsdaten

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Server und Verwendung**.

2. Zeigen Sie im Dashboard **Nutzung** die Nutzungskategorien für Bereiche an, die Sie interessieren.

3. Wenn Daten zu einer Lösung vorliegen, die unnötigerweise einen Großteil des zugewiesenen Kontingents beansprucht, sollten Sie diese Lösung entfernen.

## So beheben Sie Probleme oder entfernen Verwaltungsgruppen

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Server und Verwendung**.

2. Zeigen Sie im Dashboard **Nutzung** Informationen zu den Verwaltungsgruppen an, die keine Daten senden.

3. Wenn eine Verwaltungsgruppe keine Daten sendet, können Sie auf **Problembehandlung** klicken, um ausführliche Informationen zur Problembehandlung zu erhalten. Wenn Sie eine Verwaltungsgruppe und alle Agents, die diesen Bericht erstatten, nicht behalten möchten, klicken Sie auf **Entfernen**.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->
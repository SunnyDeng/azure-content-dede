<properties 
	pageTitle="Verwenden des Azure-Vorschauportals zum Verwalten von Azure-Ressourcen | Microsoft Azure" 
	description="Gruppieren Sie mehrere Ressourcen als logische Gruppe, die zur Lebenszyklusbegrenzung für enthaltene Ressourcen wird." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="tomfitz"/>


# Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen

## Einführung

Um eine Ressource (z. B. ein Datenbankserver, eine Datenbank oder eine Web-App) in Microsoft Azure zu verwalten, konnten Sie Vorgänge bisher nur an jeweils einer Ressource ausführen. Wenn Sie eine komplexe Anwendung verwendet haben, die aus mehreren Ressourcen besteht, mussten Sie Änderungen an der Infrastruktur der Anwendung manuell koordinieren. Im Azure-Vorschauportal können Sie den Azure-Ressourcen-Manager nutzen, um Ressourcengruppen zu erstellen, mit denen alle Ressourcen einer Anwendung zusammen bereitgestellt und verwaltet werden können.

Normalerweise enthält eine Ressourcengruppe Ressourcen, die sich auf eine bestimmte Anwendung beziehen. Eine Gruppe kann z. B. eine Web-App enthalten, die Ihre öffentliche Website hostet, eine SQL-Datenbank, in der von der Website verwendete relationale Daten gespeichert sind, und ein Speicherkonto, in dem nicht-relationale Ressourcen gespeichert sind. Jede Ressource in einer Ressourcengruppe sollte den gleichen Lebenszyklus aufweisen. Weitere Informationen zum Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).

Dieses Thema enthält eine Übersicht über die Nutzung von Ressourcengruppen im Azure-Vorschauportal.

## Erstellen von Ressourcengruppen und Ressourcen

Wenn Sie eine leere Ressourcengruppe erstellen müssen, können Sie **Neu**, **Verwaltung** und dann **Ressourcengruppe** wählen.

![Leere Ressourcengruppe erstellen](./media/resource-group-portal/create-empty-group.png)

Sie vergeben einen Namen und Speicherort und wählen, falls erforderlich, ein Abonnement aus.

![Gruppenwerte festlegen](./media/resource-group-portal/set-group-properties.png)

Es ist aber nicht erforderlich, explizit eine leere Ressourcengruppe zu erstellen. Beim Erstellen einer neuen Ressource können Sie entweder eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe verwenden. Die folgende Abbildung veranschaulicht, wie Sie eine neue Web-App erstellen und dabei eine vorhandene Ressourcengruppe auswählen oder eine neue erstellen.

![Ressourcengruppe erstellen](./media/resource-group-portal/select-existing-group.png)

## Durchsuchen von Ressourcengruppen

Sie können alle Ressourcengruppen durchsuchen, indem Sie auf **Alle durchsuchen** und **Ressourcengruppen** klicken.

![Ressourcengruppen durchsuchen](./media/resource-group-portal/browse-groups.png)

Wenn Sie eine bestimmte Ressourcengruppe auswählen, wird ein Ressourcengruppen-Blatt mit Informationen zur jeweiligen Ressourcengruppe angezeigt. Darin ist auch eine Liste mit allen Ressourcen der Gruppe enthalten.

![Zusammenfassung der Ressourcengruppe](./media/resource-group-portal/group-summary.png)

Das Ressourcengruppen-Blatt bietet auch eine zusammenfassende Ansicht Ihrer Abrechnungs- und Überwachungsinformationen für alle Ressourcen in der Ressourcengruppe.

![Überwachung und Abrechnung](./media/resource-group-portal/monitoring-billing.png)

## Anpassen der Benutzeroberfläche

Um schnell auf die Zusammenfassung der Ressourcengruppe zugreifen zu können, können Sie das Blatt an Ihr Startmenü anheften.

![Ressourcengruppe anheften](./media/resource-group-portal/pin-group.png)

Sie können auch einen Abschnitt des Blatts an das Startmenü anheften, indem Sie auf die Auslassungspunkte (...) oberhalb des Abschnitts klicken. Außerdem können Sie die Größe des Abschnitts im Blatt anpassen oder ihn vollständig entfernen. Die folgende Abbildung veranschaulicht, wie Sie den Abschnitt „Ereignisse“ anheften, anpassen oder entfernen.

![Abschnitt anheften](./media/resource-group-portal/pin-section.png)

Nachdem Sie den Abschnitt „Ereignisse“ an das Startmenü angeheftet haben, wird im Startmenü eine Zusammenfassung der Ereignisse angezeigt.

![Startmenü „Ereignisse“](./media/resource-group-portal/events-startboard.png)

Wenn Sie diese auswählen, werden sofort weitere Details zu den Ereignissen eingeblendet.

## Anzeigen von erfolgten Bereitstellungen

Im Ressourcengruppen-Blatt können Sie das Datum und den Status der letzten Bereitstellung für diese Ressourcengruppe sehen. Wenn Sie den Link auswählen, wird ein Verlauf der Bereitstellungen für die Gruppe angezeigt.

![Letzte Bereitstellung](./media/resource-group-portal/last-deployment.png)

Wenn Sie eine Bereitstellung aus dem Verlauf auswählen, werden Details zu dieser Bereitstellung angezeigt.

![Zusammenfassung der Bereitstellungen](./media/resource-group-portal/deployment-summary.png)

Sie sehen die einzelnen Vorgänge, die im Verlauf der Bereitstellung ausgeführt wurden. In der folgenden Abbildung sind ein erfolgreicher und ein nicht erfolgreicher Vorgang zu sehen.

![Vorgangsdetails](./media/resource-group-portal/operation-details.png)

Wenn Sie einen Vorgang auswählen, werden mehr Details zum Vorgang angezeigt. Dies kann besonders hilfreich sein, wenn ein Vorgang fehlgeschlagen ist, wie unten dargestellt. Dies dient als Hilfe bei der Ermittlung des Grunds, warum eine Bereitstellung nicht erfolgreich war. In der folgenden Abbildung sehen Sie, dass die Website nicht bereitgestellt wurde, weil der Name nicht eindeutig war.

![Vorgangsmeldung](./media/resource-group-portal/operation-message.png)

## Anzeigen von Überwachungsprotokollen

Das Überwachungsprotokoll enthält nicht nur Bereitstellungsvorgänge, sondern alle Verwaltungsvorgänge für Ressourcen in Ihrem Abonnement. Sie können in den Überwachungsprotokollen beispielsweise sehen, wenn eine Person in Ihrer Organisation eine App beendet hat. Wählen Sie zum Anzeigen der Überwachungsprotokolle **Alle durchsuchen** und **Überwachungsprotokolle**.

![Überwachungsprotokolle durchsuchen](./media/resource-group-portal/browse-audit-logs.png)

Im Abschnitt „Vorgänge“ werden die einzelnen Vorgänge angezeigt, die für Ihr Abonnement durchgeführt wurden.

![Überwachungsprotokoll anzeigen](./media/resource-group-portal/view-audit-log.png)

Wenn Sie einen Vorgang auswählen, werden mehr Details angezeigt, z. B. welcher Benutzer den Vorgang ausgeführt hat.

Sie können filtern, was im Überwachungsprotokoll angezeigt wird, indem Sie die Option **Filter** wählen.

![Protokoll filtern](./media/resource-group-portal/filter-logs.png)

Sie können auswählen, welche Art von Vorgängen angezeigt werden soll, z. B. Vorgänge einer Ressourcengruppe oder Ressource, innerhalb eines bestimmten Zeitraums, von einem bestimmten Aufrufer oder die Vorgangsebenen.

![Filteroptionen](./media/resource-group-portal/filter-options.png)

## Hinzufügen von Ressourcen zu Ressourcengruppen

Mit dem Befehl **Hinzufügen** auf dem Blatt "Ressourcengruppen" können Sie einer Ressourcengruppe Ressourcen hinzufügen.

![Ressource hinzufügen](./media/resource-group-portal/add-resource.png)

Sie können die gewünschte Ressource aus der verfügbaren Liste auswählen.

## Löschen von Ressourcengruppen

Da Sie mit Ressourcengruppen den Lebenszyklus aller darin enthaltenen Ressourcen verwalten können, werden beim Löschen einer Ressourcengruppe alle darin enthaltenen Ressourcen gelöscht. Sie können auch einzelne Ressourcen in einer Ressourcengruppe löschen. Seien Sie vorsichtig beim Löschen einer Ressourcengruppe, da mit dieser eventuell andere Ressourcen verknüpft sind. In der Ressourcenzuordnung können Sie die verknüpften Ressourcen sehen und die notwendigen Schritte ergreifen, um nicht beabsichtigte Folgen zu vermeiden, wenn Sie Ressourcengruppen löschen. Die verknüpften Ressourcen werden nicht gelöscht, aber sie funktionieren unter Umständen nicht wie erwartet.

![Gruppe löschen](./media/resource-group-portal/delete-group.png)

## Kennzeichnen von Ressourcen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Informationen zur Verwendung von Tags über das Vorschauportal finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../resource-group-using-tags.md).

## Bereitstellen einer benutzerdefinierten Vorlage

Wenn Sie eine Bereitstellung ausführen möchten, ohne eine der Vorlagen des Marketplace zu nutzen, können Sie eine angepasste Vorlage erstellen, mit der die Infrastruktur für Ihre Lösung definiert wird. Weitere Informationen zu Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Wählen Sie zum Bereitstellen einer angepassten Vorlage über das Vorschauportal die Optionen **Neu**, **Marketplace** und **Alles**.

![Vorlagenbereitstellung finden](./media/resource-group-portal/launch-template.png)

Suchen Sie nach **Vorlagenbereitstellung**, und wählen Sie die Option in der zurückgegebenen Liste aus.

![Vorlagenbereitstellung suchen](./media/resource-group-portal/search-template.png)

Nach dem Starten der Vorlagenbereitstellung können Sie die benutzerdefinierte Vorlage erstellen und Werte für die Bereitstellung festlegen.

![Vorlage erstellen](./media/resource-group-portal/show-custom-template.png)

## Nächste Schritte
Erste Schritte

- Eine Einführung in die Konzepte des Ressourcen-Managers finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).  
- Eine Einführung zur Verwendung von Azure PowerShell für das Bereitstellen von Ressourcen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).
- Eine Einführung zur Verwendung der Azure-Befehlszeilenschnittstelle für das Bereitstellen von Ressourcen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md). 
  


 

<!---HONumber=Oct15_HO3-->
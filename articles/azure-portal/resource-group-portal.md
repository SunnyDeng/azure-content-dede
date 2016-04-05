<properties 
	pageTitle="Verwenden des Azure-Portals zum Verwalten von Azure-Ressourcen | Microsoft Azure" 
	description="Verwenden Sie das Azure-Portal und Azure Resource Manager zum Bereitstellen und Verwalten Ihrer Ressourcen. Es wird gezeigt, wie Sie Ressourcen markieren und Überwachungsprotokolle anzeigen." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/29/2016" 
	ms.author="tomfitz"/>


# Verwenden des Azure-Portals zum Bereitstellen und Verwalten Ihrer Azure-Ressourcen

## Einführung

In diesem Thema wird veranschaulicht, wie Sie das [Azure-Portal](https://portal.azure.com) mit [Azure Resource Manager](../resource-group-overview.md) verwenden, um Ihre Azure-Ressourcen bereitzustellen und zu verwalten.

Das Portal und der Ressourcen-Manager werden derzeit nicht von allen Diensten unterstützt. Verwenden Sie für diese Dienste das [klassische Portal](https://manage.windowsazure.com). Den Status der einzelnen Dienste finden Sie im [Verfügbarkeitsdiagramm für das Azure-Portal](https://azure.microsoft.com/features/azure-portal/availability/).

Sie können Ressourcen auch über Azure PowerShell und die Azure-Befehlszeilenschnittstelle verwalten. Weitere Informationen zur Verwendung dieser Schnittstellen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md) und [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager](../xplat-cli-azure-resource-manager.md). Weitere Informationen zum Bereitstellen von Lösungen mit Visual Studio finden Sie unter [Erstellen und Bereitstellen von Azure-Ressourcengruppen über Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Erstellen und Verwalten von Ressourcengruppen

Wählen Sie zum Erstellen einer leeren Ressourcengruppe **Neu**, **Verwaltung** und **Ressourcengruppe**.

![Leere Ressourcengruppe erstellen](./media/resource-group-portal/create-empty-group.png)

Sie vergeben einen Namen und Speicherort und wählen, falls erforderlich, ein Abonnement aus.

![Gruppenwerte festlegen](./media/resource-group-portal/set-group-properties.png)

Nachdem die Ressourcengruppe erstellt wurde, können Sie darin Ressourcen bereitstellen. Wählen Sie zum Starten der Bereitstellung einfach **Neu**, und geben Sie den Typ von Ressource an, den Sie bereitstellen möchten.

![Ressource bereitstellen](./media/resource-group-portal/deploy-resource.png)

Falls der gewünschte Ressourcentyp für die Bereitstellung nicht angezeigt wird, können Sie auf dem Marketplace danach suchen.

![Marketplace durchsuchen](./media/resource-group-portal/search-resource.png)

Je nach ausgewähltem Ressourcentyp müssen Sie einige relevante Eigenschaften festlegen, bevor die Bereitstellung beginnen kann. Diese Optionen werden hier nicht angezeigt, da sie je nach Ressourcentyp variieren. Für alle Typen müssen Sie eine Zielressourcengruppe auswählen. Die folgende Abbildung zeigt, wie Sie eine neue Web-App erstellen und in der gerade erstellten Ressourcengruppe bereitstellen.

![Ressourcengruppe erstellen](./media/resource-group-portal/select-existing-group.png)

Sie können aber auch beim Bereitstellen Ihrer Ressourcen eine neue Ressourcengruppe erstellen. Wählen Sie anstelle einer vorhandenen Ressourcengruppe in Ihrem Abonnement die Option **Neu**, und geben Sie der Ressourcengruppe einen Namen.

![neue Ressourcengruppe erstellen](./media/resource-group-portal/select-new-group.png)

Die Bereitstellung wird gestartet. Dies kann einige Minuten in Anspruch nehmen. Nachdem die Bereitstellung abgeschlossen ist, wird eine Benachrichtigung angezeigt.

![Benachrichtigung anzeigen](./media/resource-group-portal/view-notification.png)

### Hinzufügen von Ressourcen zu einer vorhandenen Ressourcengruppe

Mit dem Befehl **Hinzufügen** auf dem Blatt "Ressourcengruppen" können Sie einer Ressourcengruppe Ressourcen hinzufügen.

![Ressource hinzufügen](./media/resource-group-portal/add-resource.png)

Sie können die gewünschte Ressource aus der verfügbaren Liste auswählen.

### Durchsuchen von Ressourcengruppen

Sie können alle Ressourcengruppen durchsuchen, indem Sie auf **Ressourcengruppen** klicken.

![Ressourcengruppen durchsuchen](./media/resource-group-portal/browse-groups.png)

Wenn Sie eine bestimmte Ressourcengruppe auswählen, wird ein Ressourcengruppen-Blatt mit Informationen zur jeweiligen Ressourcengruppe angezeigt. Darin ist auch eine Liste mit allen Ressourcen der Gruppe enthalten.

![Zusammenfassung der Ressourcengruppe](./media/resource-group-portal/group-summary.png)

Sie können dem Blatt mit der Ressourcengruppe weitere Graphen und Tabellen hinzufügen, indem Sie unterhalb der Zusammenfassung die Option **Abschnitt hinzufügen** wählen.

![Abschnitt hinzufügen](./media/resource-group-portal/add-section.png)

In einem Kachelkatalog können Sie die Informationen auswählen, die in das Blatt eingebunden werden sollen. Die Typen der angezeigten Kacheln werden je nach Ressourcentyp gefiltert. Wenn Sie eine andere Ressource auswählen, ändern sich die verfügbaren Kacheln.

![Abschnitt hinzufügen](./media/resource-group-portal/tile-gallery.png)

Ziehen Sie die gewünschte Kachel jeweils an eine der verfügbaren Positionen.

![Kachel ziehen](./media/resource-group-portal/drag-tile.png)

Nachdem Sie oben im Portal **Fertig** gewählt haben, ist die neue Sicht Teil des Blatts.

![Kachel anzeigen](./media/resource-group-portal/show-lens.png)

Um den schnellen Zugriff auf eine Ressourcengruppe zu ermöglichen, können Sie das Blatt in Ihrem Dashboard anheften.

![Ressourcengruppe anheften](./media/resource-group-portal/pin-group.png)

Sie können auch einen Abschnitt des Blatts an das Dashboard anheften, indem Sie auf die Auslassungspunkte (...) oberhalb des Abschnitts klicken. Außerdem können Sie die Größe des Abschnitts im Blatt anpassen oder ihn vollständig entfernen. Die folgende Abbildung veranschaulicht, wie Sie den Abschnitt „CPU und Arbeitsspeicher“ anheften, anpassen oder entfernen.

![Abschnitt anheften](./media/resource-group-portal/pin-cpu-section.png)

Nach dem Anheften des Abschnitts im Dashboard wird die Zusammenfassung im Dashboard angezeigt.

![Dashboard anzeigen](./media/resource-group-portal/view-startboard.png)

Wenn Sie diese auswählen, werden sofort weitere Details zu den Daten eingeblendet.

### Ressourcengruppe löschen

Da Sie mit Ressourcengruppen den Lebenszyklus aller darin enthaltenen Ressourcen verwalten können, werden beim Löschen einer Ressourcengruppe alle darin enthaltenen Ressourcen gelöscht. Sie können auch einzelne Ressourcen in einer Ressourcengruppe löschen. Seien Sie vorsichtig beim Löschen einer Ressourcengruppe, da mit dieser eventuell andere Ressourcen verknüpft sind. In der Ressourcenzuordnung können Sie die verknüpften Ressourcen sehen und die notwendigen Schritte ergreifen, um nicht beabsichtigte Folgen zu vermeiden, wenn Sie Ressourcengruppen löschen. Die verknüpften Ressourcen werden nicht gelöscht, aber sie funktionieren unter Umständen nicht wie erwartet.

![Gruppe löschen](./media/resource-group-portal/delete-group.png)


## Anzeigen vorheriger Bereitstellungen

Im Ressourcengruppen-Blatt können Sie das Datum und den Status der letzten Bereitstellung für diese Ressourcengruppe sehen. Wenn Sie den Link auswählen, wird ein Verlauf der Bereitstellungen für die Gruppe angezeigt.

![Letzte Bereitstellung](./media/resource-group-portal/last-deployment.png)

Wenn Sie eine Bereitstellung aus dem Verlauf auswählen, werden Details zu dieser Bereitstellung angezeigt.

![Zusammenfassung der Bereitstellungen](./media/resource-group-portal/deployment-summary.png)

Sie sehen die einzelnen Vorgänge, die im Verlauf der Bereitstellung ausgeführt wurden. In der folgenden Abbildung sind ein erfolgreicher und ein nicht erfolgreicher Vorgang zu sehen.

![Vorgangsdetails](./media/resource-group-portal/operation-details.png)

Weitere Informationen zur Problembehandlung für eine Bereitstellung finden Sie unter [Problembehandlung beim Bereitstellen von Ressourcengruppen mit dem Azure-Portal](../resource-manager-troubleshoot-deployments-portal.md).

Sie können die Vorlage abrufen, die für die Bereitstellung verwendet wurde, indem Sie die Option **Vorlage exportieren** wählen.

![Vorlage exportieren](./media/resource-group-portal/export-template.png)

Es wird genau die Vorlage angezeigt, die für die Bereitstellung verwendet wurde.

![Vorlage anzeigen](./media/resource-group-portal/show-template.png)

Es ist keine vollständige Darstellung der Ressourcengruppe. Wenn Sie Ressourcen außerhalb dieser Bereitstellung hinzugefügt oder gelöscht haben, werden diese Aktionen nicht in der Vorlage widergespiegelt. Das Blatt enthält die Vorlage , eine Parameterdatei für die Verwendung mit der Vorlage und ein PowerShell-Skript zum Bereitstellen der Vorlage. Sie können diese drei Dateien herunterladen, indem Sie die Option **In Datei speichern** wählen.

## Anzeigen von Überwachungsprotokollen

Das Überwachungsprotokoll enthält nicht nur Bereitstellungsvorgänge, sondern alle Verwaltungsvorgänge für Ressourcen in Ihrem Abonnement. Sie können in den Überwachungsprotokollen beispielsweise sehen, wenn eine Person in Ihrer Organisation eine App beendet hat. Wählen Sie zum Anzeigen der Überwachungsprotokolle **Alle durchsuchen** und **Überwachungsprotokolle**.

![Überwachungsprotokolle durchsuchen](./media/resource-group-portal/browse-audit-logs.png)

Im Abschnitt „Vorgänge“ werden die einzelnen Vorgänge angezeigt, die für Ihr Abonnement durchgeführt wurden.

![Überwachungsprotokoll anzeigen](./media/resource-group-portal/view-audit-log.png)

Wenn Sie einen Vorgang auswählen, werden mehr Details angezeigt, z. B. welcher Benutzer den Vorgang ausgeführt hat.

Weitere Informationen zum Anzeigen der Überwachungsprotokolle finden Sie unter [Überwachen von Vorgängen mit dem Ressourcen-Manager](../resource-group-audit.md).

## Markieren von Ressourcen

Sie können Ressourcengruppen und Ressourcen Tags zuordnen, um sie logisch zu organisieren. Informationen zur Verwendung von Tags über das Portal finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../resource-group-using-tags.md).

## Bereitstellen einer benutzerdefinierten Vorlage

Wenn Sie eine Bereitstellung ausführen möchten, ohne eine der Vorlagen des Marketplace zu nutzen, können Sie eine angepasste Vorlage erstellen, mit der die Infrastruktur für Ihre Lösung definiert wird. Weitere Informationen zu Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

Wählen Sie zum Bereitstellen einer benutzerdefinierten Vorlage über das Portal die Option **Neu**, und starten Sie eine Suche nach **Bereitstellungen von Vorlagen**, bis Sie sie aus den Optionen auswählen können.

![Vorlagenbereitstellung suchen](./media/resource-group-portal/search-template.png)

Wählen Sie **Vorlagenbereitstellung** aus den verfügbaren Ressourcen aus.

![Vorlagenbereitstellung auswählen](./media/resource-group-portal/select-template.png)

Nach dem Starten der Vorlagenbereitstellung können Sie die benutzerdefinierte Vorlage erstellen und Werte für die Bereitstellung festlegen.

![Vorlage erstellen](./media/resource-group-portal/show-custom-template.png)

## Anzeigen Ihres Abonnements und der Kosten

Sie können Informationen zu Ihrem Abonnement und die zusammengefassten Kosten für alle Ihre Ressourcen anzeigen. Wählen Sie **Abonnements** und das Abonnement aus, das Sie anzeigen möchten. Möglicherweise steht nur ein Abonnement zur Auswahl.

![Abonnement](./media/resource-group-portal/select-subscription.png)

Im Blatt „Abonnement“ wird eine Verbrauchsrate angezeigt.

![Verbrauchsrate](./media/resource-group-portal/burn-rate.png)

Eine Aufschlüsselung der Kosten nach Ressourcentyp wird ebenfalls angezeigt.

![Ressourcenkosten](./media/resource-group-portal/cost-by-resource.png)

## Zugriffskontrolle für Azure-Dashboards

Der Zugriff auf die Informationen, die von den meisten Kacheln im Portal angezeigt werden, wird in Azure über die [rollenbasierte Zugriffssteuerung](../active-directory/role-based-access-control-configure.md) geregelt. Um Dashboards nahtlos in das Ökosystem zu integrieren, werden alle veröffentlichten Dashboards als Azure-Ressourcen implementiert. Aus Sicht der Zugriffssteuerung unterscheiden sich Dashboards nicht von einem virtuellen Computer oder Speicherkonto.

Beispiel: Angenommen, Sie verfügen über ein Azure-Abonnement, und verschiedenen Mitgliedern Ihres Teams wurden die Rollen **Besitzer**, **Mitwirkender** oder**Leser** für das Abonnement zugewiesen. Benutzer, die Besitzer oder Mitwirkende sind, können Dashboards im Abonnement auflisten, anzeigen, erstellen, ändern oder löschen. Benutzer, die als Leser festgelegt wurden, können Dashboards auflisten und anzeigen, aber sie können diese nicht ändern oder löschen. Benutzer mit Leserzugriff können lokale Änderungen an einem veröffentlichten Dashboard vornehmen (z.B. beim Behandeln eines Problems), aber sie haben nicht die Möglichkeit, diese Änderungen zurück auf den Server zu veröffentlichen. Diese Benutzer können eine private Kopie des Dashboards zur eigenen Nutzung erstellen.

Beachten Sie, dass die einzelnen Kacheln im Dashboard jeweils eigene Anforderungen an die Zugriffssteuerung durchsetzen. Diese basieren auf den Ressourcen, für die Daten angezeigt werden. Dies bedeutet, dass Sie ein Dashboard entwerfen können, das umfassender genutzt werden kann, während die Daten auf den einzelnen Kacheln trotzdem geschützt sind.

## Nächste Schritte

- Eine Einführung in die Konzepte des Ressourcen-Managers finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).
- Eine Einführung zur Verwendung von Azure PowerShell für das Bereitstellen von Ressourcen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).
- Eine Einführung zur Verwendung der Azure-Befehlszeilenschnittstelle für das Bereitstellen von Ressourcen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0330_2016-->
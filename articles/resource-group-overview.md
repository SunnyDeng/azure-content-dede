<properties
   pageTitle="Übersicht über Azure Resource Manager"
   description="Es wird beschrieben, wie Sie den Azure-Ressourcen-Manager für die Bereitstellung, Verwaltung und Zugriffssteuerung von Ressourcen unter Azure verwenden."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Übersicht über Azure Resource Manager

Anwendungen bestehen normalerweise aus vielen Komponenten, also z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Sie sehen diese Komponenten nicht als separate Entitäten, sondern als verwandte und voneinander abhängige Teile einer einzelnen Entität. Diese möchten Sie als Gruppe bereitstellen, verwalten und überwachen. Mit dem Azure-Ressourcen-Manager können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen.

Mit dem Azure-Ressourcen-Manager wird die Zugriffssteuerung auf systemeigene Weise in die Verwaltungsplattform integriert. So können Sie angeben, welche Aktionen ein Benutzer Ihrer Organisation für eine Ressourcengruppe durchführen kann.

> [AZURE.NOTE]In diesem Thema werden Ressourcen, Gruppen und Vorlagen in Verbindung mit dem Vorschauportal beschrieben, um die Konzepte zu demonstrieren. Sie können Azure-Ressourcen auch mit der [Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows](virtual-machines/xplat-cli-azure-resource-manager.md) und mit [PowerShell](powershell-azure-resource-manager.md) erstellen, verwalten und löschen.

## Ressourcengruppen

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Die Ressourcengruppe kann alle Ressourcen für eine Anwendung enthalten, oder nur die Ressourcen, die logisch gruppiert sind. Je nachdem, was für Ihre Organisation am sinnvollsten ist, können Sie entscheiden, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten.

Beim Definieren der Ressourcengruppe sind einige wichtige Faktoren zu beachten:

1. Alle Ressourcen einer Gruppe müssen über den gleichen Lebenszyklus verfügen. Sie werden zusammen bereitgestellt, aktualisiert und gelöscht. Falls eine Ressource, z. B. ein Datenbankserver, in einem anderen Entwicklungszyklus vorhanden sein muss, sollte er in einer anderen Ressourcengruppe enthalten sein.
2. Jede Ressource kann nur in einer Ressourcengruppe vorhanden sein.
3. Sie können eine Ressource einer Ressourcengruppe jederzeit hinzufügen bzw. die Ressource daraus entfernen.
4. Eine Ressourcengruppe kann Ressourcen enthalten, die sich in unterschiedlichen Regionen befinden.
5. Eine Ressourcengruppe kann zum Festlegen der Zugriffssteuerung für administrative Aktionen verwendet werden.

Im Azure-Vorschauportal werden alle neue Ressourcen in einer Ressourcengruppe erstellt. Auch wenn Sie nur eine einzelne Ressource erstellen, z. B. eine Website, müssen Sie entscheiden, ob Sie diese Ressource einer vorhandenen Gruppe hinzufügen oder eine neue Gruppe für die Ressource erstellen.

Die folgende Abbildung zeigt eine Ressourcengruppe mit Application Insights, einem Datenbankserver, einer Datenbank, einem App Services-Plan und einer Website.

![Zusammenfassung der Ressourcengruppe](./media/resource-group-overview/resourcegroupsummary2.png)

Eine Ressourcengruppe kann auch mit einer Ressource in einer anderen Ressourcengruppe verknüpft werden. Eine Ressource wird als verknüpft angesehen, wenn zwischen Ressourcen in unterschiedlichen Ressourcengruppen eine Bereitstellungsabhängigkeit besteht. Wenn eine Web-App in einer Ressourcengruppe eine Verbindung mit einer Datenbank in einer anderen Ressourcengruppe herstellt, sind diese Ressourcen verknüpft. Sie können Verknüpfungen zwischen Ressourcen in einer anderen Ressourcengruppe auch explizit definieren.

Weitere Informationen zum Verknüpfen von Ressourcen finden Sie unter [Verknüpfen von Ressourcen im Azure-Ressourcen-Manager](resource-group-link-resources.md).

Wenn Sie eine Ressource in eine neue Ressourcengruppe verschieben müssen, lesen Sie die Informationen unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).

Im Vorschauportal können Sie bequem Kosten anzeigen, Ereignisse überwachen und Warnungen verwalten. Die folgende Abbildung zeigt die zusammengefasste Abrechnung für eine Gruppe.

![Abrechnung](./media/resource-group-overview/billing.png)

## Bereitstellungen von Vorlagen

Mit dem Azure-Ressourcen-Manager können Sie eine einfache Vorlage \(im JSON-Format\) erstellen, mit der die Bereitstellung und Konfiguration Ihrer Anwendung definiert wird. Diese Vorlage wird als Azure-Vorlage bezeichnet und ist eine deklarative Möglichkeit zum Definieren der Bereitstellung. Mit einer Vorlage können Sie die Anwendung während des gesamten App-Lebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.

In der Vorlage definieren Sie die Infrastruktur für Ihre App, die Konfiguration der Infrastruktur und die Veröffentlichung Ihres App-Codes in der Infrastruktur. Sie müssen sich nicht mit der Reihenfolge für die Bereitstellung befassen, weil der Azure-Ressourcen-Manager die Abhängigkeiten analysiert und so sicherstellt, dass Ressourcen in der richtigen Reihenfolge erstellt werden.

Sie können die Vorlage auch für Aktualisierungen der Infrastruktur verwenden. Beispielsweise können Sie Ihrer App eine neue Ressource sowie Konfigurationsregeln für die Ressourcen hinzufügen, die bereits bereitgestellt wurden. Wenn in der Vorlage die Erstellung einer neuen Ressource angegeben ist, diese aber bereits vorhanden ist, führt der Azure-Ressourcen-Manager anstelle der Erstellung einer neuen Ressource eine Aktualisierung durch. Der Azure-Ressourcen-Manager aktualisiert die vorhandene Ressource auf den Zustand, der für eine neue Ressource gelten würde.

Sie können Parameter in Ihrer Vorlage angeben, um in Bezug auf die Bereitstellung für Anpassung und Flexibilität zu sorgen. Beispielsweise können Sie Parameterwerte übergeben, mit denen die Bereitstellung für Ihre Testumgebung angepasst wird. Durch das Angeben der Parameter können Sie dieselbe Vorlage für die Bereitstellung für alle Umgebungen Ihrer App verwenden.

Sie können alle erforderlichen Schritte für die Bereitstellung und Konfiguration über die Vorlage ausführen. Es sollten keine weiteren manuellen Schritte erforderlich sein. Der Azure-Ressourcen-Manager verfügt über Erweiterungen für Szenarien, in denen Sie zusätzliche Vorgänge benötigen, z. B. das Installieren bestimmter Software, die nicht Teil des Setups ist. Wenn Sie bereits einen Konfigurationsverwaltungsdienst verwenden, z. B. DSC, Chef oder Puppet, können Sie ihn weiter nutzen, indem Sie Erweiterungen einsetzen.

Wenn Sie eine Lösung über den Marketplace erstellen, enthält die Lösung automatisch eine Bereitstellungsvorlage. Sie müssen die Vorlage nicht völlig neu erstellen, weil Sie mit der Vorlage für Ihre Lösung beginnen und sie dann an die speziellen Anforderungen anpassen können.

Die Vorlage wird schließlich zu einem Teil des Quellcodes für Ihre App. Sie können sie in das Quellcoderepository einchecken und im Verlauf der App-Entwicklung aktualisieren. Sie können die Vorlage mit Visual Studio bearbeiten.

Weitere Informationen zum Definieren der Vorlage finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](./resource-group-authoring-templates.md).

Vorlagenschemas finden Sie unter [Schemas des Azure-Ressourcen-Manager](https://github.com/Azure/azure-resource-manager-schemas).

Informationen zum Verwenden einer Vorlage für die Bereitstellung finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](azure-portal/resource-group-template-deploy.md) und [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).

Anleitungen zum Strukturieren von Vorlagen finden Sie unter [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

## Tags

Der Azure-Ressourcen-Manager verfügt über eine Markierungsfunktion, mit der Sie Ressourcen gemäß Ihren Anforderungen für die Verwaltung oder Abrechnung kategorisieren können. Die Verwendung von Markierungen \(Tags\) kann ratsam sein, wenn Sie über eine komplexe Sammlung von Ressourcengruppen und Ressourcen verfügen und diese Ressourcen auf möglichst sinnvolle Weise visualisieren müssen. Beispielsweise können Sie Ressourcen markieren, die in Ihrer Organisation eine ähnliche Funktion haben oder zu derselben Abteilung gehören.

Im Vorschauportal können Sie mit der Verwendung von Tags beginnen, indem Sie auf das Tagsymbol klicken.

![tags](./media/resource-group-overview/tags.png)

Ressourcen müssen sich nicht in derselben Ressourcengruppe befinden, um ein gemeinsames Tag aufzuweisen. Sie können Ihre eigene Tag-Taxonomie erstellen, um dafür zu sorgen, dass alle Benutzer in Ihrer Organisation die gleichen Tags verwenden. So wird verhindert, dass Benutzer versehentlich leicht unterschiedliche Tags nutzen \(z. B. „dept“ anstelle von „department“\).

Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md).

## Zugriffssteuerung

Mit dem Azure-Ressourcen-Manager können Sie steuern, wer Zugriff auf spezielle Aktionen für Ihre Organisation hat. OAuth und die rollenbasierte Zugriffssteuerung \(Role-Based Access Control, RBAC\) sind standardmäßig in die Verwaltungsplattform integriert, und diese Zugriffssteuerung wird auf alle Dienste in der Ressourcengruppe angewendet. Sie können Benutzer vordefinierten Plattformrollen und ressourcenspezifischen Rollen hinzufügen und diese Rollen auf ein Abonnement, eine Ressourcengruppe oder eine Ressource anwenden, um den Zugriff zu beschränken. Beispielsweise können Sie die vordefinierte Rolle „SQL DB Contributor“ verwenden, mit der Benutzer Datenbanken verwalten können, aber keine Datenbankserver oder Sicherheitsrichtlinien. Sie fügen Benutzer Ihrer Organisation, die diese Art von Zugriff benötigen, der Rolle „SQL DB Contributor“ hinzu und wenden die Rolle auf das Abonnement, die Ressourcengruppe oder die Ressource an.

Im Vorschauportal können Sie die Zugriffssteuerung definieren, indem Sie auf die Schaltfläche für den Zugriff klicken.

![Benutzerzugriffssteuerung](./media/resource-group-overview/access.png)

Der Azure-Ressourcen-Manager protokolliert Benutzeraktionen automatisch zu Prüfzwecken.

Sie können kritische Ressourcen auch explizit sperren, um zu verhindern, dass sie von Benutzern gelöscht oder geändert werden. Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](resource-group-lock-resources.md).

Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung über das Azure-Vorschauportal](./role-based-access-control-configure.md).

Beispiele zum Festlegen von Zugriffsrichtlinien finden Sie unter [Verwalten und Überwachen des Zugriffs auf Ressourcen](azure-portal/resource-group-rbac.md).

Bewährte Methoden finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).

## Einheitliche Verwaltungsebene

Der Azure-Ressourcen-Manager verfügt für Azure PowerShell, die Azure-Befehlszeilenschnittstelle für Mac, Linux, und Windows, das Azure-Portal und die REST-API über vollständig kompatible Vorgänge. Sie können die Schnittstelle verwenden, die für Sie am besten geeignet ist, und schnell zwischen Schnittstellen wechseln, ohne dass Verwirrung entsteht. Im Portal werden sogar Benachrichtigungen für Aktionen angezeigt, die außerhalb des Portals durchgeführt werden.

Weitere Informationen zu PowerShell finden Sie unter [Verwenden von Azure PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md) und [Azure-Ressourcen-Manager-Cmdlets](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Informationen zur Azure-Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./virtual-machines/xplat-cli-azure-resource-manager.md).

Informationen zur REST-API finden Sie unter [Referenz zur REST-API des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Informationen zum Verwenden des Vorschauportals finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](azure-portal/resource-group-portal.md).

## Nächste Schritte

- Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Vorlagen](./resource-group-authoring-templates.md).
- Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Vorlagen](azure-portal/resource-group-template-deploy.md).
- Grundlegende Informationen zu den Funktionen, die in einer Vorlage verwendet werden können, finden Sie unter [Vorlagenfunktionen](./resource-group-template-functions.md).
- Anleitungen zum Entwerfen von Vorlagen finden Sie unter [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

<!---HONumber=July15_HO5-->
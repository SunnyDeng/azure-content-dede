<properties
   pageTitle="Übersicht über den Azure-Ressourcen-Manager | Microsoft Azure"
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
   ms.date="11/09/2015"
   ms.author="tomfitz"/>

# Übersicht über den Azure-Ressourcen-Manager

Die Infrastruktur für Ihre Anwendung besteht normalerweise aus vielen Komponenten: womöglich ein virtueller Computer, ein Speicherkonto und ein virtuelles Netzwerk oder eine Web-App, eine Datenbank, ein Datenbankserver und Drittanbieterdienste. Sie sehen diese Komponenten nicht als separate Entitäten, sondern als verwandte und voneinander abhängige Teile einer einzelnen Entität. Diese möchten Sie als Gruppe bereitstellen, verwalten und überwachen. Mit dem Azure-Ressourcen-Manager können Sie als Gruppe mit den Ressourcen in Ihrer Lösung arbeiten. Sie können alle Ressourcen für Ihre Lösung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Der Ressourcen-Manager bietet Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Sie Ihre Ressourcen nach der Bereitstellung verwalten können.

## Vorteile der Verwendung des Ressourcen-Managers

Der Ressourcen-Manager bietet mehrere Vorteile:

- Sie können alle Ressourcen für Ihre Lösung als Gruppe bereitstellen, verwalten und überwachen, anstatt diese Ressourcen einzeln zu verarbeiten.
- Sie können die Lösung während des gesamten Entwicklungslebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.
- Sie können deklarative Vorlagen verwenden, um Ihre Bereitstellung zu definieren.
- Sie können die Abhängigkeiten zwischen Ressourcen definieren, sodass diese in der richtigen Reihenfolge bereitgestellt werden.
- Sie können die Zugriffssteuerung auf alle Dienste in der Ressourcengruppe anwenden, da die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) standardmäßig in die Verwaltungsplattform integriert ist.
- Sie können Tags auf Ressourcen anwenden, um alle Ressourcen in Ihrem Abonnement logisch zu organisieren.
- Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe oder für eine Gruppe an Ressourcen mit dem gleichen Tag anzeigen.  

Der Ressourcen-Manager stellt eine neue Möglichkeit zur Bereitstellung und Verwaltung Ihrer Lösungen bereit. Weitere Informationen zu den Änderungen an dem von Ihnen verwendeten früheren Bereitstellungsmodell finden Sie unter [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](resource-manager-deployment-model.md).

## Anleitungen

Die folgenden Vorschläge helfen Ihnen, bei der Arbeit mit Lösungen die Vorteile des Ressourcen-Managers zu nutzen.

1. Definieren Sie die Infrastruktur mithilfe der deklarativen Syntax in Ressourcen-Manager-Vorlagen anstatt mit imperativen Befehlen, und stellen Sie sie so bereit.
2. Definieren Sie alle Bereitstellungs- und Konfigurationsschritte in der Vorlage. Sie sollten keine manuellen Schritte zum Einrichten der Lösung durchführen müssen.
3. Führen Sie imperative Befehle aus, um Ihre Ressourcen zu verwalten, z. B. Starten oder Beenden einer App oder des Computers.
4. Gruppieren Sie Ressourcen mit dem gleichen Lebenszyklus in einer Ressourcengruppe. Verwenden Sie Tags für die weitere Organisation von Ressourcen.

## Ressourcengruppen

Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Die Ressourcengruppe kann alle Ressourcen für eine Anwendung enthalten, oder nur die Ressourcen, die logisch gruppiert sind. Je nachdem, was für Ihre Organisation am sinnvollsten ist, können Sie entscheiden, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten.

Beim Definieren der Ressourcengruppe sind einige wichtige Faktoren zu beachten:

1. Alle Ressourcen einer Gruppe müssen über den gleichen Lebenszyklus verfügen. Sie werden zusammen bereitgestellt, aktualisiert und gelöscht. Falls eine Ressource, z. B. ein Datenbankserver, in einem anderen Entwicklungszyklus vorhanden sein muss, sollte er in einer anderen Ressourcengruppe enthalten sein.
2. Jede Ressource kann nur in einer Ressourcengruppe vorhanden sein.
3. Sie können eine Ressource einer Ressourcengruppe jederzeit hinzufügen bzw. die Ressource daraus entfernen.
4. Sie können eine Ressource aus einer Ressourcengruppe in eine andere Gruppe verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).
4. Eine Ressourcengruppe kann Ressourcen enthalten, die sich in unterschiedlichen Regionen befinden.
5. Eine Ressourcengruppe kann zum Festlegen der Zugriffssteuerung für administrative Aktionen verwendet werden.
6. Eine Ressource kann mit einer Ressource in einer anderen Ressourcengruppe verknüpft werden, wenn die beiden Ressourcen miteinander interagieren müssen, aber nicht den gleichen Lebenszyklus aufweisen (z. B. mehrere Apps, die mit einer Datenbank verbunden werden). Weitere Informationen finden Sie unter [Verknüpfen von Ressourcen im Azure-Ressourcen-Manager](resource-group-link-resources.md).

## Bereitstellung von Vorlagen

Mit dem Ressourcen-Manager können Sie eine einfache Vorlage (im JSON-Format) erstellen, mit der die Bereitstellung und Konfiguration Ihrer Anwendung definiert wird. Diese Vorlage wird als Ressourcen-Manager-Vorlage bezeichnet und ist eine deklarative Möglichkeit zum Definieren der Bereitstellung. Mit einer Vorlage können Sie die Anwendung während des gesamten App-Lebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.

In der Vorlage definieren Sie die Infrastruktur für Ihre App, die Konfiguration der Infrastruktur und die Veröffentlichung Ihres App-Codes in der Infrastruktur. Sie müssen sich nicht mit der Reihenfolge für die Bereitstellung befassen, weil der Azure-Ressourcen-Manager die Abhängigkeiten analysiert und so sicherstellt, dass Ressourcen in der richtigen Reihenfolge erstellt werden. Weitere Informationen finden Sie unter [Definieren von Abhängigkeiten in Azure-Ressourcen-Manager-Vorlagen](resource-group-define-dependencies.md).

Sie müssen nicht die gesamte Infrastruktur in einer einzelnen Vorlage definieren. Oftmals ist es sinnvoll, die Bereitstellungsanforderungen in eine Gruppe von spezifischen, zweckgebundenen Vorlagen zu unterteilen. Sie können diese Vorlagen mühelos für verschiedene Lösungen erneut verwenden. Um eine bestimmte Lösung bereitzustellen, erstellen Sie eine Mastervorlage, die alle erforderlichen Vorlagen verknüpft. Weitere Informationen finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure-Ressourcen-Manager](resource-group-linked-templates.md).

Sie können die Vorlage auch für Aktualisierungen der Infrastruktur verwenden. Beispielsweise können Sie Ihrer App eine neue Ressource sowie Konfigurationsregeln für die Ressourcen hinzufügen, die bereits bereitgestellt wurden. Wenn in der Vorlage die Erstellung einer neuen Ressource angegeben ist, diese aber bereits vorhanden ist, führt der Azure-Ressourcen-Manager anstelle der Erstellung einer neuen Ressource eine Aktualisierung durch. Der Azure-Ressourcen-Manager aktualisiert die vorhandene Ressource auf den Zustand, der für eine neue Ressource gelten würde.

Sie können Parameter in Ihrer Vorlage angeben, um in Bezug auf die Bereitstellung für Anpassung und Flexibilität zu sorgen. Beispielsweise können Sie Parameterwerte übergeben, mit denen die Bereitstellung für Ihre Testumgebung angepasst wird. Durch das Angeben der Parameter können Sie dieselbe Vorlage für die Bereitstellung für alle Umgebungen Ihrer App verwenden.

Der Ressourcen-Manager verfügt über Erweiterungen für Szenarien, in denen zusätzliche Vorgänge, wie z. B. das Installieren bestimmter Software, die nicht Teil des Setups ist, erforderlich sind. Wenn Sie bereits einen Konfigurationsverwaltungsdienst verwenden, z. B. DSC, Chef oder Puppet, können Sie ihn weiter nutzen, indem Sie Erweiterungen einsetzen.

Wenn Sie eine Lösung über den Marketplace erstellen, enthält die Lösung automatisch eine Bereitstellungsvorlage. Sie müssen die Vorlage nicht völlig neu erstellen, weil Sie mit der Vorlage für Ihre Lösung beginnen und sie dann an die speziellen Anforderungen anpassen können.

Die Vorlage wird schließlich zu einem Teil des Quellcodes für Ihre App. Sie können sie in das Quellcoderepository einchecken und im Verlauf der App-Entwicklung aktualisieren. Sie können die Vorlage mit Visual Studio bearbeiten.

Weitere Informationen zum Definieren der Vorlage finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](./resource-group-authoring-templates.md).

Vorlagenschemas finden Sie unter [Schemas des Azure-Ressourcen-Managers](https://github.com/Azure/azure-resource-manager-schemas).

Informationen zum Verwenden einer Vorlage für die Bereitstellung finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

Anleitungen zum Strukturieren von Vorlagen finden Sie unter [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

Informationen zum Bereitstellen der Lösung in andere Umgebungen finden Sie unter [Entwicklungs- und Testumgebungen in Microsoft Azure](solution-dev-test-environments-preview-portal.md).

## Tags

Der Ressourcen-Manager verfügt über eine Markierungsfunktion, mit der Sie Ressourcen gemäß Ihren Anforderungen für die Verwaltung oder Abrechnung kategorisieren können. Die Verwendung von Markierungen (Tags) kann ratsam sein, wenn Sie über eine komplexe Sammlung von Ressourcengruppen und Ressourcen verfügen und diese Ressourcen auf möglichst sinnvolle Weise visualisieren müssen. Beispielsweise können Sie Ressourcen markieren, die in Ihrer Organisation eine ähnliche Funktion haben oder zu derselben Abteilung gehören.

Ressourcen müssen sich nicht in derselben Ressourcengruppe befinden, um ein gemeinsames Tag aufzuweisen. Sie können Ihre eigene Tag-Taxonomie erstellen, um dafür zu sorgen, dass alle Benutzer in Ihrer Organisation die gleichen Tags verwenden. So wird verhindert, dass Benutzer versehentlich leicht unterschiedliche Tags nutzen (z. B. „dept“ anstelle von „department“).

Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md).

## Zugriffssteuerung

Mit dem Ressourcen-Manager können Sie steuern, wer Zugriff auf spezielle Aktionen für Ihre Organisation hat. OAuth und die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) sind standardmäßig in die Verwaltungsplattform integriert, und diese Zugriffssteuerung wird auf alle Dienste in der Ressourcengruppe angewendet. Sie können Benutzer vordefinierten Plattformrollen und ressourcenspezifischen Rollen hinzufügen und diese Rollen auf ein Abonnement, eine Ressourcengruppe oder eine Ressource anwenden, um den Zugriff zu beschränken. Beispielsweise können Sie die vordefinierte Rolle „SQL DB Contributor“ verwenden, mit der Benutzer Datenbanken verwalten können, aber keine Datenbankserver oder Sicherheitsrichtlinien. Sie fügen Benutzer Ihrer Organisation, die diese Art von Zugriff benötigen, der Rolle „SQL DB Contributor“ hinzu und wenden die Rolle auf das Abonnement, die Ressourcengruppe oder die Ressource an.

Der Ressourcen-Manager protokolliert automatisch Benutzeraktionen zu Überwachungszwecken. Informationen zum Arbeiten mit den Überwachungsprotokollen finden Sie unter [Überwachen von Vorgängen mit dem Ressourcen-Manager](resource-group-audit.md).

Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Zugriffssteuerung über das Azure-Vorschauportal](role-based-access-control-configure.md). Dieses Thema enthält eine Liste der integrierten Rollen und zulässigen Aktionen. Integrierte Rollen sind allgemeine Rollen, wie z. B. Besitzer, Leser und Mitwirkender, sowie servicespezifische Rollen, wie z. B. Virtual Machine Contributor, Virtual Network Contributor und SQL Security Manager (um nur einige der verfügbaren Rollen zu nennen).

Beispiele zum Zuweisen von Rollen finden Sie unter [Verwalten des Zugriffs auf Ressourcen](resource-group-rbac.md).

Sie können kritische Ressourcen auch explizit sperren, um zu verhindern, dass sie von Benutzern gelöscht oder geändert werden. Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](resource-group-lock-resources.md).

Bewährte Methoden finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).

## Verwalten von Ressourcen mit benutzerdefinierten Richtlinien

Mit dem Ressourcen-Manager können Sie benutzerdefinierte Richtlinien zum Verwalten Ihrer Ressourcen erstellen. Die von Ihnen erstellten Richtlinien können verschiedenste Szenarien abdecken. Sie können z. B. festlegen, dass bestimmte Namenskonventionen zwingend einzuhalten sind, oder einschränken, welche Regionen einen bestimmten Typ von Ressourcen hosten können. Oder Sie können einen Tagwert für Ressourcen als erforderlich deklarieren, um eine Abrechnung nach Abteilungen zu ermöglichen. Weitere Informationen finden Sie unter [Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung](resource-manager-policy.md).

## Einheitliche Verwaltungsebene

Der Ressourcen-Manager bietet über Azure PowerShell, die Azure-Befehlszeilenschnittstelle für Mac, Linux, und Windows, das Azure-Vorschauportal und die REST-API vollständig kompatible Vorgänge. Sie können die Schnittstelle verwenden, die für Sie am besten geeignet ist, und schnell zwischen Schnittstellen wechseln, ohne dass Verwirrung entsteht. Im Portal werden sogar Benachrichtigungen für Aktionen angezeigt, die außerhalb des Portals durchgeführt werden.

Weitere Informationen zu PowerShell finden Sie unter [Verwenden von Azure PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md) und [Azure-Ressourcen-Manager-Cmdlets](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Informationen zur Azure-Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./virtual-machines/xplat-cli-azure-resource-manager.md).

Informationen zur REST-API finden Sie unter [Referenz zur REST-API des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Informationen zum Verwenden des Vorschauportals finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](azure-portal/resource-group-portal.md).

Der Azure-Ressourcen-Manager unterstützt Cross-Origin Resource Sharing (CORS). Mit CORS können Sie die Ressourcen-Manager-REST-API oder die REST-API eines Azure-Diensts über eine Webanwendung aufrufen, die sich in einer anderen Domäne befindet. Ohne CORS-Unterstützung verhindert der Webbrowser, dass eine App in einer Domäne auf Ressourcen in einer anderen Domäne zugreifen kann. Der Ressourcen-Manager aktiviert CORS für alle Anforderungen mit gültigen Anmeldeinformationen für die Authentifizierung.

## Nächste Schritte

- Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Vorlagen](./resource-group-authoring-templates.md).
- Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Vorlagen](resource-group-template-deploy.md).
- Grundlegende Informationen zu den Funktionen, die in einer Vorlage verwendet werden können, finden Sie unter [Vorlagenfunktionen](./resource-group-template-functions.md).
- Anleitungen zum Entwerfen von Vorlagen finden Sie unter [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

Hier sehen Sie eine Videodemonstration dieser Übersicht:

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=Nov15_HO3-->
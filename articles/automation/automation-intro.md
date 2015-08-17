<properties
	pageTitle="Was ist Azure Automation?"
	description="Sie erfahren, welchen Nutzen Azure Automation hat, und erhalten Antworten auf häufig gestellte Fragen, damit Sie mit dem Erstellen und Verwenden von Runbooks beginnen können."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/06/2015"
	ms.author="bwren"/>

# Was ist Azure Automation?

Microsoft Azure Automation bietet Benutzern die Möglichkeit, die manuellen, langfristigen, Fehler verursachenden und häufig wiederholten Aufgaben zu automatisieren, die für gewöhnlich in einer Cloud- und Unternehmensumgebung ausgeführt werden. Dieser Artikel enthält kurze Antworten auf häufig gestellte Fragen zu Azure Automation. In anderen Artikeln dieser Bibliothek finden Sie ausführlichere Informationen zu den verschiedenen Themen.

## Welchen Nutzen bietet Azure Automation?

Mit Azure Automation sparen Sie Zeit und steigern die Zuverlässigkeit der normalen Verwaltungsaufgaben, die Sie in Cloud- und Unternehmensumgebungen ausführen. Sie können diese Prozesse als Runbooks implementieren, mit denen mehrere Aufgaben ohne Eingriff des Menschen ausgeführt werden können. Sie können sogar so geplant werden, dass sie automatisch in regelmäßigen Abständen ausgeführt werden.

## Was ist ein Runbook?

Ein Runbook umfasst eine Reihe von Aufgaben, mit denen in Azure Automation ein automatisierter Prozess durchgeführt wird. Dies kann ein einfacher Prozess sein, z. B. das Starten eines virtuellen Computers oder das Erstellen eines Protokolleintrags. Sie können auch ein komplexes Runbook nutzen, in dem andere kleinere Runbooks kombiniert sind, um einen komplexen Prozess für mehrere Ressourcen oder sogar mehrere Clouds durchzuführen.

Beispielsweise verfügen Sie unter Umständen über einen vorhandenen manuellen Prozess zur Bereitstellung eines neuen virtuellen Computers, der mehrere Schritte umfasst, z. B. das Erstellen des virtuellen Computers, das Verbinden mit einem Netzwerk, das Zuweisen einer IP-Adresse und schließlich das Benachrichtigen des Benutzers über die Verfügbarkeit des Computers. Anstatt diese Schritte manuell auszuführen, können Sie ein Runbook erstellen, mit dem diese Aufgaben als einzelner Prozess ausgeführt werden. Sie starten dazu das Runbook, geben die erforderlichen Informationen an, z. B. den Namen des virtuellen Computers, die IP-Adresse und die E-Mail-Adresse des Empfängers, und können sich dann zurücklehnen, während der Prozess ohne Ihr Zutun durchgeführt wird.


## Was kann mit Runbooks automatisiert werden?

Runbooks in Azure Automation basieren auf dem PowerShell-Workflow, sodass alle Optionen von PowerShell verfügbar sind. Wenn eine Anwendung oder ein Dienst über eine API verfügt, kann ein Runbook damit arbeiten. Falls Sie ein PowerShell-Modul dafür haben, können Sie das Modul in Azure Automation laden und diese Cmdlets in Ihr Runbook einbinden. Azure Automation-Runbooks werden in der Azure-Cloud ausgeführt, sodass Sie auf alle Ressourcen in der Cloud oder externen Ressourcen zugreifen können, auf die über die Cloud zugegriffen werden kann. Mit dem [Hybrid-Runbook Worker](automation-hybrid-runbook-worker.md) können Runbooks in Ihrem lokalen Rechenzentrum ausgeführt werden, um lokale Ressourcen zu verwalten.


## Wo erhalte ich Runbooks?

Der [Runbookkatalog](http://msdn.microsoft.com/library/azure/dn781422.aspx) enthält Runbooks von Microsoft und der Community, die Sie entweder unverändert in Ihrer Umgebung verwenden oder für Ihre eigenen Zwecke anpassen können. Außerdem sind sie als Referenz hilfreich, um zu lernen, wie Sie eigene Runbooks erstellen. Sie können auch Ihre eigenen Runbooks im Katalog bereitstellen, die für andere Benutzer nützlich sein können.


## Wie erstelle ich eigene Runbooks?

Sie können völlig neue [eigene Runbooks erstellen](http://msdn.microsoft.com/library/azure/dn643637.aspx) oder die Runbooks aus dem [Runbookkatalog](http://msdn.microsoft.com/library/azure/dn781422.aspx) für Ihre eigenen Anforderungen anpassen. Falls Sie das direkte Arbeiten mit PowerShell-Code vorziehen, können Sie [das Runbook mit dem Text-Editor bearbeiten](http://msdn.microsoft.com/library/azure/dn879137.aspx) (im Azure-Portal oder offline). Wenn Sie ein Runbook lieber bearbeiten möchten, ohne dass der zugrunde liegende Code angezeigt wird, können sie im Azure-Vorschauportal den [Grafik-Editor](automation-graphical-authoring-intro.md) verwenden.


## Wie steht Azure Automation im Vergleich mit anderen Automatisierungstools da?

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) wird zum Automatisieren von Verwaltungsaufgaben in der Private Cloud verwendet. Es wird lokal in Ihrem Rechenzentrum als Komponente des [Windows Azure-Pakets](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx) installiert. Für SMA und Azure Automation wird dasselbe Runbookformat genutzt, das auf dem Windows PowerShell-Workflow basiert, aber bei SMA werden keine [grafischen Runbooks](automation-graphical-authoring-intro.md) unterstützt.

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) dient zur Automatisierung von lokalen Ressourcen. Hierbei wird ein anderes Runbookformat als für Azure Automation und Service Management Automation verwendet, und die Anwendung verfügt über eine grafische Oberfläche zum Erstellen von Runbooks ohne Skripts. Die Runbooks setzen sich aus den Aktivitäten der Integrationspakete zusammen, die speziell für Orchestrator geschrieben wurden.

## Wo erhalte ich weitere Informationen?

Um mehr über Azure Automation zu erfahren und Ihre eigenen Runbooks zu erstellen, steht Ihnen eine Vielzahl von Ressourcen zur Verfügung.

- Sie befinden sich gerade in der **Azure Automation-Bibliothek**. Die Artikel in dieser Bibliothek enthalten die vollständige Dokumentation für die Konfiguration und Verwaltung von Azure Automation und für die Erstellung Ihrer eigenen Runbooks.
- Unter [Azure PowerShell-Cmdlets](http://msdn.microsoft.com/library/jj156055.aspx) finden Sie Informationen zur Automatisierung von Azure-Vorgängen mit Windows PowerShell. Runbooks verwenden diese Cmdlets zum Arbeiten mit Azure-Ressourcen.
- Der [Azure Automation-Blog](http://azure.microsoft.com/blog/tag/azure-automation) liefert Ihnen die neuesten Informationen von Microsoft zu Azure Automation. Es ist ratsam, diesen Blog zu abonnieren, um stets die neuesten Informationen vom Azure Automation-Team zu erhalten.
- Im [Automation-Forum](http://go.microsoft.com/fwlink/p/?LinkId=390561) können Sie Fragen über Azure Automation stellen, um sie von Microsoft und der Automation-Community beantworten zu lassen.

## Kann ich Feedback geben?

**Bitte geben Sie uns Feedback!** Wenn Sie nach einer Azure Automation-Runbook-Lösung oder einem Integrationsmodul suchen, senden Sie im Script Center eine Skriptanforderung. Wenn Sie Feedback oder Vorschläge zu Features für Azure Automation haben, veröffentlichen Sie sie auf [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Vielen Dank!

<!---HONumber=August15_HO6-->
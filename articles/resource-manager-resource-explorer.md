<properties
   pageTitle="Azure-Ressourcen-Explorer | Microsoft Azure"
   description="Beschreibt den Azure-Ressourcen-Explorer, und wie er zum Anzeigen und Aktualisieren von Bereitstellungen über den Azure Resource Manager verwendet werden kann"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="stuartle;tomfitz"/>

# Verwenden des Azure-Ressourcen-Explorer zum Anzeigen und Ändern von Ressourcen
Der [Azure-Ressourcen-Explorer](https://resources.azure.com) eignet sich hervorragend zum Untersuchen von Ressourcen, die Sie bereits in Ihrem Abonnement erstellt haben. Mit diesem Tool können Sie ermitteln, wie die Ressourcen strukturiert sind, und welche Eigenschaften den Ressourcen zugewiesen sind. Sie erfahren, welche REST-API-Vorgänge und PowerShell-Cmdlets für einen Ressourcentyp verfügbar sind, und Sie können Befehle über die Benutzeroberfläche ausführen lassen. Der Ressourcen-Explorer kann für Sie beim Erstellen von Ressourcen-Manager-Vorlagen von besonderem Nutzen sein, da Sie mit seiner Hilfe die Eigenschaften vorhandener Ressourcen anzeigen können.

Die Quelle für das Ressourcen-Explorer-Tool ist auf [Github](https://github.com/projectkudu/ARMExplorer) verfügbar, wo Sie eine wertvolle Referenz finden, wenn Sie ein ähnliches Verhalten in Ihren eigenen Anwendungen implementieren müssen.

## Anzeigen von Ressourcen
Navigieren Sie zu [https://resources.azure.com](https://resources.azure.com), und melden Sie sich mit den gleichen Anmeldeinformationen an, die Sie für das [Azure-Portal](https://portal.azure.com) verwenden.

Nach dem Laden können Sie in der Strukturansicht auf der linken Seite in Ihren Abonnements und Ressourcengruppen einen Drilldown ausführen:

![Strukturansicht](./media/resource-manager-resource-explorer/are-01-treeview.png)

Wenn Sie in einer Ressourcengruppe einen Drilldown ausführen, sehen Sie die Anbieter, für die Ressourcen in dieser Gruppe vorhanden sind:

![providers](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Dort können Sie beginnen, den Drilldown in die Ressourceninstanzen auszuführen. Im folgenden Screenshot sehen Sie die SQL Server-Instanz `sltest` in der Strukturansicht. Auf der rechten Seite sehen Sie Informationen über die REST-API-Anforderungen, die Sie für diese Ressource verwenden können. Wenn Sie zum Knoten einer Ressource navigieren, erstellt der Ressourcen-Explorer automatisch die GET-Anforderung, um Informationen über die Ressource abzurufen. In dem großen Textbereich unterhalb der URL sehen Sie die Antwort der API.

Wenn Sie mit den ARM-Vorlagen vertraut werden, wird Ihnen auch der Textinhalt vertraut! Der Abschnitt **properties** der Antwort entspricht den Werten, die Sie im Abschnitt **properties** Ihrer Vorlage angeben können.

![SQL Server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Mit dem Ressourcen-Explorer können Sie den Drilldown zum Untersuchen untergeordneter Ressourcen fortsetzen. Im Falle des SQL-Datenbankservers gibt es untergeordnete Ressourcen für Elemente wie Datenbanken und Firewallregeln.

Indem Sie eine Datenbank untersuchen, lernen Sie ihre Eigenschaften kennen. Im folgenden Screenshot sehen Sie: Die Datenbank `edition` ist `Standard`, und `serviceLevelObjective` (bzw. die Datenbankebene) ist `S1`.

![SQL-Datenbank](./media/resource-manager-resource-explorer/are-04-database-get.png)

## Ändern von Ressourcen

Sobald Sie zu einer Ressource navigiert sind, können Sie die Schaltfläche „Bearbeiten“ auswählen, damit der JSON-Inhalt bearbeitbar wird. Anschließend können Sie mit dem Ressourcen-Explorer den JSON-Inhalt bearbeiten und eine PUT-Anforderung zum Ändern der Ressource senden. Die folgende Abbildung zeigt z. B. die in `S0` geänderte Datenbankebene:

![Datenbank – PUT-Anforderung](./media/resource-manager-resource-explorer/are-05-database-put.png)

Durch Auswahl von **PUT** senden Sie die Anforderung.

Sobald die Anforderung gesendet wurde, initiiert der Ressourcen-Explorer die GET-Anforderung erneut, um den Status zu aktualisieren. In diesem Fall sehen wir, dass die `requestedServiceObjectiveId` aktualisiert wurde und sich von der `currentServiceObjectiveId` unterscheidet, was bedeutet, dass ein Skalierungsvorgang ausgeführt wird. Sie können auf die GET-Schaltfläche klicken, um den Status manuell zu aktualisieren.

![Datenbank – GET-Anforderung](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## Ausführen von Aktionen für Ressourcen

Auf der Registerkarte **Aktionen** können Sie zusätzliche REST-Vorgänge anzeigen und ausführen. Wenn Sie z. B. eine Websiteressource ausgewählt haben, wird auf der Registerkarte „Aktionen“ eine lange Liste verfügbarer Vorgänge angezeigt, von denen einige unten dargestellt werden.

![Web – POST-Anforderung](./media/resource-manager-resource-explorer/are-web-post.png)

## Aufrufen der API über PowerShell
Auf der PowerShell-Registerkarte im Ressourcen-Explorer werden die Cmdlets für die Interaktion mit der Ressource angezeigt, die Sie derzeit untersuchen. Je nach Typ der Ressource, die Sie ausgewählt haben, reicht das angezeigte PowerShell-Skript von einfachen Cmdlets (z. B. `Get-AzureRmResource` und `Set-AzureRmResource`) bis zu komplizierteren Cmdlets (z. B. das Austauschen von Slots auf einer Website).

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Weitere Informationen zu Azure PowerShell-Cmdlets finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](./powershell-azure-resource-manager.md).

## Zusammenfassung
Bei der Arbeit mit ARM kann der Ressourcen-Explorer ein äußerst nützliches Tool sein. Er ist eine hervorragende Möglichkeit, Verfahren zur Verwendung von PowerShell zum Abfragen und Ausführen von Änderungen zu entdecken. Wenn Sie mit der REST-API arbeiten, ist er eine hervorragende Möglichkeit für Sie, die ersten Schritte zu gehen und API-Aufrufe schnell zu testen, bevor Sie mit dem Schreiben von Code beginnen. Und wenn Sie ARM-Vorlagen schreiben, kann er eine hervorragende Möglichkeit sein, die Ressourcenhierarchie kennenzulernen und herauszufinden, wo Konfiguration eingebracht werden kann – Sie können eine Änderung im Portal vornehmen und dann die entsprechenden Einträge im Ressourcen-Explorer finden!

Weitere Informationen bietet Ihnen das [Channel 9-Video mit Scott Hanselman und David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo).

<!---HONumber=AcomDC_0309_2016-->
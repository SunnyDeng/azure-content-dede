<properties 
	pageTitle="Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen" 
	description="Gruppieren Sie mehrere Ressourcen als logische Gruppe, die zur Lebenszyklusbegrenzung für enthaltene Ressourcen wird." 
	services="azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>


# Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen

## Einführung

Um eine Ressource (eine vom Benutzer verwaltete Entität wie ein Datenbankserver, eine Datenbank oder eine Website) in Microsoft Azure zu verwalten, konnten Sie Vorgänge bisher nur für jeweils eine Ressource ausführen. Wenn es sich um eine komplexe Anwendung aus mehreren Ressourcen handelte, war die Verwaltung dieser Anwendung sehr komplex. Im Azure-Vorschauportal können Sie Ressourcengruppen erstellen, um alle Ressourcen in einer Anwendung gemeinsam zu verwalten. Ressourcengruppen sind ein neues Konzept in Azure, das als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen fungiert.

Mit Ressourcengruppen können Sie alle Ihre Ressourcen in einer Anwendung gemeinsam verwalten. Ressourcengruppen werden mit der neuen Verwaltungsfunktion Azure-Ressourcen-Manager eingerichtet. Mit dem Azure-Ressourcen-Manager können Sie mehrere Ressourcen in einer logischen Gruppe zusammenfassen, die als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen dient. Eine Gruppe enthält in der Regel Ressourcen, die zu einer bestimmten Anwendung gehören. Eine Gruppe kann z. B. eine Websiteressource enthalten, die Ihre öffentliche Website hostet, eine SQL-Datenbank, in der von der Website verwendete relationale Daten gespeichert sind, und ein Speicherkonto, in dem nicht relationale Ressourcen gespeichert sind.

Es folgt ein kurzer Überblick über die Verwendung von Ressourcengruppen im Azure-Portal.

## Erstellen von Ressourcengruppen

Wenn im Portal eine Ressource erstellt wird, wird sie stets innerhalb einer Ressourcengruppe erstellt. Während der Erstellung können Sie wählen, ob eine neue Ressourcengruppe erstellt oder eine vorhandene Ressourcengruppe verwendet werden soll.<br><br />

![Ressourcengruppe erstellen](./media/resource-group-portal/1_createWebsite.png)

Wenn Sie eine Anwendung erstellen, die mehrere zusammenwirkende Ressourcen umfasst (z. B. Website und Datenbank), wird sie stets in ihrer eigenen Ressourcengruppe erstellt, sodass Sie den Lebenszyklus aller zugehörigen Ressourcen mit der Ressourcengruppe verwalten können. Während der Entwicklung Ihrer Anwendung können Sie der Ressourcengruppe weitere Ressourcen hinzufügen oder Ressourcen daraus entfernen.

![Ressourcengruppe erstellen](./media/resource-group-portal/2_createWSandDB.png)

## Durchsuchen von Ressourcengruppen

Wenn Sie auf die Indexleiste auf der linken Bildschirmseite klicken, können Sie alle Ressourcengruppen durchsuchen. Eine Ressourcengruppe verfügt über einen Flügel, der alle Informationen über eine bestimmte Ressourcengruppe enthält. Der Flügel der Ressourcengruppe bietet auch eine zusammenfassende Ansicht Ihrer Abrechnungs- und Überwachungsinformationen für alle Ressourcen in der Ressourcengruppe.

Im Zusammenfassungsabschnitt wird nicht nur die Zuordnung aller Ressourcen in der Ressourcengruppe grafisch dargestellt, sondern es werden auch Ressourcen in anderen Ressourcengruppen dargestellt, die mit der Ressourcengruppe verknüpft sind. Die Ressourcenzuordnung zeigt auch den Status der einzelnen Ressourcen. ![Zusammenfassung der Ressourcengruppe](./media/resource-group-portal/3_1BrowseRGs.png)

Der Ressourcenzuordnungsteil kann angepasst und vergrößert werden, sodass alle in der Ressourcengruppe enthaltenen Ressourcen sowie verknüpfte Ressourcen in anderen Ressourcengruppen angezeigt werden. Wird dieser Teil an das Startmenü geheftet, wird er in das Startmenü kopiert.

![PIN](./media/resource-group-portal/3_2BrowseRGs.png)

Beim Klicken auf die Ressourcenzuordnung wird die Listenansicht aller Ressourcen in der Ressourcenzuordnung aufgerufen. In dieser Ansicht werden alle in einer Ressourcengruppe enthaltenen oder damit verknüpften Ressourcen aufgelistet. Wenn Sie auf diese Ressourcen klicken, werden deren Fenster aufgerufen.

![Ressourcen anzeigen](./media/resource-group-portal/3_3BrowseRGs.png)

## Hinzufügen von Ressourcen zu Ressourcengruppen

Mit dem Befehl **Hinzufügen** auf dem Blatt "Ressourcengruppen" können Sie einer Ressourcengruppe Ressourcen hinzufügen. Wenn Sie die Schritte im Ablauf durchführen, können Sie der Ressourcengruppe weitere Ressourcen hinzufügen.

![Ressource hinzufügen](./media/resource-group-portal/4_AddResource.png)

Hinweis: Es wird davon abgeraten, Teamprojekte zusammen mit anderen Azure-Ressourcen in die gleiche Ressourcengruppe einzufügen. Wenn Sie ein Teamprojekt in einem neuen Konto und in einer neuen Gruppe erstellen und dann eine Website erstellen, wird für die Website standardmäßig die zuletzt verwendete Gruppe (VSO-Gruppe) verwendet. Dies führt dazu, dass sich Laufzeit- und Entwicklerressourcen in derselben Gruppe befinden.

## Löschen von Ressourcengruppen

Da Sie mit Ressourcengruppen den Lebenszyklus aller darin enthaltenen Ressourcen verwalten können, werden beim Löschen einer Ressourcengruppe alle darin enthaltenen Ressourcen gelöscht. Sie können auch einzelne Ressourcen in einer Ressourcengruppe löschen. Seien Sie vorsichtig beim Löschen einer Ressourcengruppe, da mit dieser eventuell andere Ressourcen verknüpft sind. In der Ressourcenzuordnung können Sie die verknüpften Ressourcen sehen und die notwendigen Schritte ergreifen, um nicht beabsichtigte Folgen zu vermeiden, wenn Sie Ressourcengruppen löschen.

## Nächste Schritte
Erste Schritte

- Eine Einführung in die Konzepte des Ressourcen-Managers finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).  
- Eine Einführung zur Verwendung von Azure PowerShell für das Bereitstellen von Ressourcen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).
- Eine Einführung zur Verwendung der Azure-Befehlszeilenschnittstelle für das Bereitstellen von Ressourcen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md). 
- Informationen zum logischen Organisieren von Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../resource-group-using-tags.md).
  


 

<!---HONumber=August15_HO7-->
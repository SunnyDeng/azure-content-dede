<properties urlDisplayName="" pageTitle="Using Resource groups to manage your Azure resources" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Using Resource groups to manage your Azure resources" authors="Nafisa Bhojawala"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Nafisa Bhojawala"></tags>


# Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen

### Einführung

Um eine Ressource (eine vom Benutzer verwaltete Entität wie ein Datenbankserver, eine Datenbank oder eine Website) in Microsoft Azure zu verwalten, konnten Sie Vorgänge bisher nur für jeweils eine Ressource ausführen. Wenn es sich um eine komplexe Anwendung aus mehreren Ressourcen handelte, war die Verwaltung dieser Anwendung sehr komplex. Im Microsoft Azure-Vorschauportal können Sie Ressourcengruppen erstellen, um alle Ressourcen in einer Anwendung gemeinsam zu verwalten. Ressourcengruppen sind ein neues Konzept in Azure, das als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen fungiert.
<br></br>

Mit Ressourcengruppen können Sie alle Ihre Ressourcen in einer Anwendung gemeinsam verwalten. Ressourcengruppen werden mit der neuen Verwaltungsfunktion Azure-Ressourcen-Manager eingerichtet. Mit dem Azure-Ressourcen-Manager können Sie mehrere Ressourcen in einer logischen Gruppe zusammenfassen, die als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen dient. Eine Gruppe enthält in der Regel Ressourcen, die zu einer bestimmten Anwendung gehören. Eine Gruppe kann z. B. eine Websiteressource enthalten, die Ihre öffentliche Website hostet, eine SQL-Datenbank, in der von der Website verwendete relationale Daten gespeichert sind, und ein Speicherkonto, in dem nicht relationale Ressourcen gespeichert sind.

Es folgt ein kurzer Überblick über die Verwendung von Ressourcengruppen im Microsoft Azure-Vorschauportal.
<br></br>

### Erstellen von Ressourcengruppen

Wenn im Vorschauportal eine Ressource erstellt wird, wird sie stets innerhalb einer Ressourcengruppe erstellt. Während der Erstellung können Sie wählen, ob eine neue Ressourcengruppe erstellt oder eine vorhandene Ressourcengruppe verwendet werden soll.<br></br>

![][]
<br></br>
Wenn Sie eine Anwendung erstellen, die mehrere zusammenwirkende Ressourcen umfasst (z. B. Website und Datenbank), wird sie stets in ihrer eigenen Ressourcengruppe erstellt, sodass Sie den Lebenszyklus aller zugehörigen Ressourcen mit der Ressourcengruppe verwalten können. Während der Entwicklung Ihrer Anwendung können Sie der Ressourcengruppe weitere Ressourcen hinzufügen oder Ressourcen daraus entfernen.

![][1]
<br></br>
### Durchsuchen von Ressourcengruppen

Wenn Sie auf die Indexleiste auf der linken Bildschirmseite klicken, können Sie alle Ressourcengruppen durchsuchen. Eine Ressourcengruppe verfügt über einen Flügel, der alle Informationen über eine bestimmte Ressourcengruppe enthält. Der Flügel der Ressourcengruppe bietet auch eine zusammenfassende Ansicht Ihrer Abrechnungs- und Überwachungsinformationen für alle Ressourcen in der Ressourcengruppe.

Im Zusammenfassungsabschnitt wird nicht nur die Zuordnung aller Ressourcen in der Ressourcengruppe grafisch dargestellt, sondern es werden auch Ressourcen in anderen Ressourcengruppen dargestellt, die mit der Ressourcengruppe verknüpft sind. Die Ressourcenzuordnung zeigt auch den Status der einzelnen Ressourcen.

![][2]
<br></br>
Der Ressourcenzuordnungsteil kann angepasst und vergrößert werden, sodass alle in der Ressourcengruppe enthaltenen Ressourcen sowie verknüpfte Ressourcen in anderen Ressourcengruppen angezeigt werden. Wird dieser Teil an das Startmenü geheftet, wird er in das Startmenü kopiert.

![][3]
<br></br>
Beim Klicken auf die Ressourcenzuordnung wird die Listenansicht aller Ressourcen in der Ressourcenzuordnung aufgerufen. In dieser Ansicht werden alle in einer Ressourcengruppe enthaltenen oder damit verknüpften Ressourcen aufgelistet. Wenn Sie auf diese Ressourcen klicken, werden deren Flügel aufgerufen.

![][4]
<br></br>
### Hinzufügen von Ressourcen zu Ressourcengruppen

Mit dem Befehl "Hinzufügen" im Ressourcengruppenflügel können Sie einer Ressourcengruppe Ressourcen hinzufügen. Wenn Sie die Schritte im Ablauf durchführen, können Sie der Ressourcengruppe weitere Ressourcen hinzufügen.

![][5]

Hinweis: Es wird davon abgeraten, Teamprojekte zusammen mit anderen Azure-Ressourcen in die gleiche Ressourcengruppe einzufügen. Wenn Sie ein Teamprojekt in einem neuen Konto und in einer neuen Gruppe erstellen und dann eine Website erstellen, wird für die Website standardmäßig die zuletzt verwendete Gruppe (VSO-Gruppe) verwendet. Dies führt dazu, dass sich Laufzeit- und Entwicklerressourcen in derselben Gruppe befinden.
<br></br>
### Löschen von Ressourcengruppen

Da Sie mit Ressourcengruppen den Lebenszyklus aller darin enthaltenen Ressourcen verwalten können, werden beim Löschen einer Ressourcengruppe alle darin enthaltenen Ressourcen gelöscht. Sie können auch einzelne Ressourcen in einer Ressourcengruppe löschen. Seien Sie vorsichtig beim Löschen einer Ressourcengruppe, da mit dieser eventuell andere Ressourcen verknüpft sind. In der Ressourcenzuordnung können Sie die verknüpften Ressourcen sehen und die notwendigen Schritte ergreifen, um nicht beabsichtigte Folgen zu vermeiden, wenn Sie Ressourcengruppen löschen.

![][6]

  []: http://i.imgur.com/USKkQdW.png
  [1]: http://i.imgur.com/Me0jbio.png
  [2]: http://i.imgur.com/PhJeLZQ.png
  [3]: http://i.imgur.com/5Wqv2XR.png
  [4]: http://i.imgur.com/COPjNng.png
  [5]: http://i.imgur.com/G79kayH.png
  [6]: http://i.imgur.com/ZTXoISb.png

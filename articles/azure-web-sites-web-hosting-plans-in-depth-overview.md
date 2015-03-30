<properties 
	pageTitle="Webhosting-Pläne für Azure-Websites: ausführliche Übersicht - Microsoft Azure-Featurehandbuch" 
	description="Erfahren Sie, wie Webhostingpläne für Azure-Websites funktionieren und wie Ihre Managementerfahrung davon profitiert." 
	services="web-sites" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="byvinyal"/>
</br>
# Webhosting-Pläne für Azure-Websites: ausführliche Übersicht#
</br>
Webhostingpläne (WHPs) stellen einen Satz an Funktionen und Kapazitäten dar, die Sie über Ihre Websites freigeben können.  Webhostingpläne unterstützen die 4 Preisebenen für Azure-Websites (Kostenlos, Freigegeben, Basic und Standard), wobei jede Ebene eigene Funktionen bietet.  Websites im gleichen Abonnement, in der gleichen Ressourcengruppe und am gleichen geografischen Standort können sich einen Webhostingplan teilen. Alle Websites mit gemeinsamem Webhostingplan können den Funktionsumfang nutzen, der durch die Ebene des Plans definiert ist. Alle einem bestimmten Webhostingplan zugeordneten Websites werden auf den Ressourcen ausgeführt, die im Webhostingplan definiert sind. Wenn Ihr Webhostingplan z. B. für die Verwendung zweier virtueller Computer der Größe "klein" konfiguriert ist, werden alle diesem Plan zugeordneten Websites auf beiden virtuellen Computern ausgeführt. Wie immer bei Azure Websites sind die virtuellen Computer, auf dem die Websites ausgeführt werden, vollständig verwaltet und hoch verfügbar sind.
</br>
In diesem Artikel erfahren Sie die Hauptmerkmale, z. B. Ebene und Skalierung eines Webhostingplans und wie sie zum Verwalten von Websites beitragen. 
</br>
## Websites, Webhostingpläne und Ressourcengruppen##
</br>
Jede Website kann nur einem Webhostingplan gleichzeitig zugeordnet werden. Ein Webhostingplan ist einer Ressourcengruppe zugeordnet. Ressourcengruppen sind ein neues Konzept in Azure, das als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen fungiert. Mit Ressourcengruppen können Sie alle Ihre Ressourcen in einer Anwendung gemeinsam verwalten. 
</br>
Sie können mehrere Webhostingpläne in einer Ressourcengruppe haben, und jeder Webhostingplan hat einen eigenen Satz von Features und Funktionen, die von den zugeordneten Websites verwendet werden.  Die folgende Abbildung veranschaulicht diese Beziehung:
</br>
</br>
![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)
</br>
</br>
Die Möglichkeit, mehrere Webhostingpläne in einer einzelnen Ressourcengruppen zu haben, ermöglicht das Zuordnen verschiedener Websites zu verschiedenen Ressourcen, die in erster Linie virtuelle Computer sind, auf denen Ihre Websites ausgeführt werden. Diese Fähigkeit ermöglicht z. B. eine Trennung von Ressourcen zwischen Entwicklungs- und Test- und Produktionswebsites, an denen Sie einen Webhostingplan mit einem eigenen Satz von Ressourcen für Ihre Produktionswebsites zuordnen möchten, und einen zweiten Webhostingplan für Ihre Entwicklungs- und Testwebsites planen. 
</br>
Mehrere Webhostingpläne in einer einzelnen Ressourcengruppen ermöglichen Ihnen die Definition einer Anwendung, die sich über Regionen erstreckt. Eine hochverfügbare Website in zwei Regionen umfasst z. B. zwei Webhostingpläne, je eine pro Region, und je eine Website pro Webhostingplan. Ein einer solchen Situation werden alle Websites einer einzigen Ressourcengruppe zugeordnet, die eine Anwendung definiert.  Dank einer einzigen Ansicht einer Ressourcengruppe mit mehreren Webhostingplänen und mehreren Websites können Sie diese Websites schnell und einfach verwalten, steuern und überwachen. Neben der Verwaltung von Website-Ressourcen und Websites für eine Anwendung können Sie auch sonstige verwandte Azure-Ressourcen zuordnen, wie z. B. Azure SQL-Datenbanken und Teamprojekte. 
</br>
## Wann sollte ich eine neue Ressourcengruppe erstellen, und wann einen neuen Webhostingplan?##
</br>
Bei der Erstellung einer neuen Website sollten Sie die Erstellung einer neuen Ressourcengruppe erwägen, wenn die neue Website eine neue Webanwendung darstellt. In diesem Fall sollten Sie eine neue Ressourcengruppe, einen zugeordneten Webhostingplan und eine Website erstellen.  Beim Erstellen einer solchen neuen Website im neuen Azure-Vorschauportal mithilfe des Katalogs oder der neuen Option "Website + SQL" erstellt das Portal standardmäßig eine neue Ressourcengruppe und einen Webhostingplan für Ihre Website. Wenn Sie möchten, können Sie diese Standardeinstellungen jedoch überschreiben.
</br>
</br>
![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
</br>
</br>
Sie können einer vorhandenen Ressourcengruppe immer eine neue Website oder andere Ressourcen hinzufügen. Beim Erstellen einer neuen Website aus dem Kontext einer vorhandenen Ressourcengruppe verwendet der Assistent für neue Websites automatisch die vorhandene Ressource und den vorhandenen Webhostingplan. Diese Standardwerte können Sie bei Bedarf ebenfalls überschreiben. Wenn einer vorhandenen Ressourcengruppe eine neue Website hinzugefügt wird, können Sie die Website einem vorhandenen Webhostingplan hinzufügen (dies ist die Standardoption in der neuen Azure-Portalvorschau), oder Sie können einen neuen Webhostingplan zum Hinzufügen der Website erstellen.
</br>
Das Erstellen eines neuen Webhostingplans ermöglicht Ihnen das Zuordnen eines neuen Satzes von Ressourcen für Ihre Websites und bietet Ihnen größere Kontrolle über die Ressourcenzuordnung, da jeder Webhostingplan einen eigenen Satz von virtuellen Computern erhält. Da Sie Websites zwischen Webhostingplänen verschieben können, sofern sich diese in derselben Region befinden, ist die Entscheidung über die Erstellung eines neuen Webhostingplans weniger wichtig. Wenn eine Website zu viele Ressourcen beansprucht oder Sie nur wenige Websites trennen müssen, können Sie einen neuen Webhostingplan erstellen und Ihre Websites dahin verschieben.
</br>
Wenn Sie eine neue Website in einer anderen Region erstellen möchten und diese Region nicht über einen vorhandenen Webhostingplan verfügt, müssen Sie einen neuen Webhostingplan in dieser Region erstellen, um eine Website zuordnen zu können. 
</br>
Unbedingt zu beachten ist, dass Webhostingpläne oder Websites nicht zwischen Ressourcengruppen verschoben werden können. Sie können auch keine Website zwischen Webhostingplänen verschieben, die sich in unterschiedlichen Regionen befinden. 
</br>
## Vorhandene Ressourcengruppe im Azure-Vorschauportal##
</br>
Wenn Sie bereits vorhandene Azure-Websites haben, werden Sie feststellen, dass all Ihre Websites im Azure-Vorschauportal angezeigt werden. Sie können Ihre gesamte Website als flache Liste anzeigen, indem Sie auf die Schaltfläche **Durchsuchen** im linken Navigationsbereich klicken und **Websites** auswählen:
</br>
</br>
![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)
</br>
</br>
Sie können auch alle Ressourcengruppen anzeigen, die für Sie erstellt wurden, indem Sie auf die Schaltfläche **Durchsuchen** im linken Navigationsbereich klicken und **Ressourcengruppen** auswählen:
</br>
</br>
![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)
</br>
</br>
Beachten Sie auch, dass Sie eine automatisch generierte Standardressourcengruppe in jeder Region mit vorhandenen Websites haben. Der automatisch generierte Ressourcengruppenname für Websites ist *Default-Web-<LOCATION NAME>*, wobei der Standortname der Name einer Azure-Region ist (z. B. *Default-Web-WestUS*). In jeder Ressourcengruppe finden Sie all Ihre vorhandenen Websites für die Region der Gruppe. Jede in der Vergangenheit erstellte und in Zukunft erstellte Website im vollständigen Azure-Portal oder im Azure-Vorschauportal ist in beiden Portalen verfügbar. 
</br>
Da jede Website einem Webhostingplan zugeordnet werden muss, haben wir die standardmäßigen Webhostingpläne für die vorhandenen Websites gemäß der folgenden Konvention in jeder Region erstellt:
</br>
* Alle Ihre **kostenlosen** Websites werden einem **Standard**-Webhostingplan zugeordnet, und die Preisebene wird auf **Kostenlos** gesetzt. 
</br>
* Alle Ihre **gemeinsam genutzten** Websites werden einem **Standard**-Webhostingplan zugeordnet, und die Preisebene wird auf **Gemeinsam genutzt** gesetzt.
</br>
* Alle Ihre **Standard**-Websites werden einem Standard-Webhostingplan zugeordnet, und die Preisebene wird auf **Standard** festgelegt. 
</br>
Der Name dieses Webhostingplans ist **DefaultServerFarm**. Dieser Name wurde gewählt, um eine Legacy-API zu unterstützen. Der Name **ServerFarm** kann etwas irreführend sein, da er sich auf einen **Webhostingplan** bezieht. Es ist jedoch wichtig, zu verstehen, dass dies der Name eines Webhostingplans und nicht einer eigenen Entität ist. 
</br>
## FAQ für Webhostingpläne##
</br>
**Frage**: Wie kann ich einen Webhostingplan erstellen?
</br>
**Antwort**: Ein Webhostingplan ist ein Container, und daher können Sie keinen leeren Webhostingplan erstellen. Ein neuer Webhostingplan wird jedoch bei der Websiteerstellung explizit erstellt.
</br>
Um dies mit der UI in der neuen **Azure-Portalvorschau** durchzuführen, klicken Sie auf **NEU**, und wählen Sie **Website** aus, um das Website-Erstellungsfenster zu öffnen. In der ersten Abbildung unten sehen Sie unten links das Symbol **NEU** und in der zweiten Abbildung sehen Sie das **Website**-Erstellungsfenster sowie die Fenster **Webhostingplan** und **Preisebene**:
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)
</br>
</br>
![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)
</br>
</br>
In diesem Beispiel werden wir eine neue Website namens **contosomarketing** erstellen und sie im neuen Webhostingplan namens **contoso** platzieren. Wählen Sie für diesen Webhostingplan die Preisebene **Klein Standard** aus. Ausführliche Preiskategorien für Webhostingpläne und die bei jedem Besuch bereitgestellten Funktionen, Preise und Skalierungen finden Sie in den http://go.microsoft.com/?linkid=9845586)[Spezifikationen der Azure-Websites-Webhostingpläne](. 
</br>
Außerdem ist darauf hinzuweisen, dass ein Webhostingplan auch im vorhandenen Azure-Portal erstellt werden kann. Dies erfolgt als Teil des **Schnellerstellungs**-Assistenten, indem Sie **Neuen Webhostingplan erstellen** aus dem Dropdownmenü **WEBHOSTINGPLAN** auswählen:
</br>
</br>
![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)
</br>
</br>
Für dieses Beispiel erstellen wir eine neue Website namens **northwind**, und wir erstellen einen neuen Webhostingplan. Daraufhin wird ein neuer Webhostingplan mit dem Namen **default0** erstellt, der die **northwind**-Website enthält. Alle auf diese Weise erstellten Webhostingpläne verwenden diese Namenskonvention, und Webhostingpläne können nach der Erstellung nicht mehr umbenannt werden. Die auf diese Weise erstellten Webhostingpläne werden außerdem in der Preisebene **Kostenlose** erstellt.
</br>
**Frage**: Wie kann ich eine Website zu einem **Webhostingplan** zuweisen?
</br>
**Antwort**: Websites werden im Rahmen des Erstellungsprozesses zu einem Webhostingplan zugewiesen. Um dies mit der UI in der neuen **Azure-Portalvorschau** durchzuführen, klicken Sie auf **NEU**, und wählen Sie **Website** aus:
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)
</br>
</br>
Wählen Sie dann im Websiteerstellungsfenster den Webhostingplan aus:
</br>
</br>
![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)
</br>
</br>
Eine Website kann auch in einem bestimmten Webhostingplan mit dem vorhandenen Azure-Verwaltungsportal erstellt werden. Diese Erstellung erfolgt als Teil des **Schnellerfassungs**-Assistenten. Nach der Eingabe der Website-URL verwenden Sie das Dropdownmenü **WEBHOSTINGPLAN**, um einen Plan zum Hinzufügen der Website auszuwählen:
</br>
</br>
![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)
</br>
</br>
**Frage**: Wie kann ich eine Website in einen anderen Webhostingplan verschieben?
</br>
**Antwort**: Sie können Websites im Azure-Vorschauportal in einen anderen Webhostingplan verschieben. Websites können zwischen Webhostingplänen in der gleichen geografischen Region, die zu derselben Ressourcengruppe gehören, verschoben werden.
</br>
Um eine Website in einen anderen Plan zu verschieben, navigieren Sie zum Websitefenster der Website, die Sie verschieben möchten.  Klicken Sie dann auf **Webhostingplan**:
</br>
</br>
![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)
</br>
</br>
Das Webhostingplan-Fenster wird geöffnet. An diesem Punkt können Sie entweder einen existierenden Webhostingplan auswählen oder einen neuen erstellen. Pläne in einer anderen geografischen Region oder Ressourcengruppe sind abgeblendet und können nicht ausgewählt werden.
</br>
Beachten Sie, dass jeder Webhostingplan eine eigene Preiseebene hat. Wenn Sie eine Website aus einem Webhostingplan der Ebene **Kostenlos** in einen **Standard**-Webhostingplan verschieben, kann Ihre Website alle Features und Ressourcen der Standard-Ebene nutzen. 
</br>
</br>
**Frage**: Wie kann ich einen Webhostingplan skalieren?
</br>
**Antwort**: Es gibt zwei Möglichkeiten, um einen Webhostingplan zu skalieren. Sie können einerseits den Webhostingplan und damit alle zugeordneten Websites hochskalieren. Durch Ändern der Preisebene eines Webhostingplans unterliegen alle Websites in diesem Webhostingplan den Funktionen und Ressourcen, die von dieser Preisstufe definiert werden. 
</br>
In der folgenden Abbildung sehen Sie die Fenster **Webhostingplan** und **Preisebene**. Wenn Sie auf **Preisebene** im Fenster **Webhostingplan** klicken, wird das Fenster **Preisebene** erweitert, in dem Sie die Preisebene für den Webhostingplan ändern können:
</br>
</br>
![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)
</br>
</br>
Die zweite Möglichkeit, einen Plan zu skalieren, besteht im Erhöhen der Anzahl von Instanzen in Ihrem Webhostingplan. In der folgenden Abbildung sehen Sie die Fenster **Webhostingplan** und **Skalierung**. Durch Klicken auf den Skalierungsbereich im Fenster **Webhostingplan** wird er erweitert und ermöglicht das Ändern der Instanzanzahl des Plans:
</br>
</br>
![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
</br>
</br>
Da der Webhostingplan in der obigen Abbildung für die Verwendung der **Standard**-Preisstufe konfiguriert ist, ist die Option **AutoScale** aktiviert. 
</br>
Das Ausführen dieses Vorgangs im vollständigen Azure-Portal kann auf der Registerkarte **Skalierung** erfolgen, wie unten dargestellt wird:
</br>
</br>
![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
</br>
</br>
**Frage**: Wie kann ich einen Webhostingplan löschen?
</br>
**Antwort**: Um einen Webhostingplan löschen zu können, müssen Sie zunächst alle zugeordneten Websites löschen. Sobald alle Websites in einem Webhostingplan gelöscht wurden, kann ein Webhostingplan aus dem Webhostingplan-Fenster gelöscht werden:
</br>
</br>
![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
</br>
</br>
Im vollständigen Azure-Portal wird durch das Löschen der letzten Website in einem Webhostingplan der zugeordnete Webhostingplan automatisch gelöscht.
</br>
**Frage**: Wie kann ich einen Webhostingplan überwachen?
</br>
**Antwort**: Webhostingpläne können mithilfe der Überwachungs-Webparts im Webhostingplan-Fenster überwacht werden:
</br>
</br>
![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
</br>
</br>
Die Überwachungssteuerelemente können angepasst werden, indem Sie mit der rechten Maustaste auf das Steuerelement klicken und **Abfrage bearbeiten** auswählen:
</br>
</br>
![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
</br>
</br>
Folgende Metriken stehen zur Verfügung:
</br>
* CPU-Prozentsatz
</br>
* Arbeitsspeicherprozentsatz
</br>
* Warteschlangenlänge des Datenträgers 
</br>
* HTTP-Warteschlangenlänge. 
</br>
Diese Metriken zeigen die durchschnittliche Nutzung in den Instanzen, die zu einem Webhostingplan gehören. All diese Metriken können zum Einrichten von Alarmen sowie für Regeln zur automatischen Skalierung verwendet werden.
</br>
## Schlussfolgerungen##
</br>
Webhostingpläne stellen einen Satz an Funktionen und Kapazitäten dar, die Sie über Ihre Websites freigeben können.  Webhostingpläne bieten die Flexibilität, bestimmte Websites zu einem bestimmten Ressourcensatz (virtuellen Computern) zuzuordnen, und die Ressourcenzuteilung und -Nutzung für Websites weiter zu optimieren. 

<!--HONumber=47-->

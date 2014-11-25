<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure Websites Web Hosting Plans In-Depth Overview - Windows Azure feature guide" description="Learn how Web Hosting Plans for Azure Websites work, and how they benefit your management experience." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron="" Tardif="" and="" Yochay="" Kiryaty" />


# Webhosting-Pläne für Azure-Websites: ausführliche Übersicht


Webhostingpläne (WHPs) stellen einen Satz an Funktionen und Kapazitäten dar, die Sie über Ihre Websites freigeben können. Webhostingpläne unterstützen die 4 Preisebenen für Azure-Websites (Kostenlos, Freigegeben, Basic und Standard), wobei jede Ebene eigene Funktionen bietet. Websites im gleichen Abonnement, in der gleichen Ressourcengruppe und am gleichen geografischen Standort können sich einen Webhostingplan teilen. Alle Websites mit gemeinsamem Webhostingplan können den Funktionsumfang nutzen, der durch die Ebene des Plans definiert ist. Alle einem bestimmten Webhostingplan zugeordneten Websites werden auf den Ressourcen ausgeführt, die im Webhostingplan definiert sind. Wenn Ihr Webhostingplan z. B. für die Verwendung zweier virtueller Computer der Größe "klein" konfiguriert ist, werden alle diesem Plan zugeordneten Websites auf beiden virtuellen Computern ausgeführt. Wie bei Azure-Websites üblich, sind die virtuellen Computer, auf denen Ihre Websites ausgeführt werden, vollständig verwaltet und hochverfügbar.

Dieser Artikel beschreibt die Schlüsseleigenschaften wie z. B. Ebene und Skalierung eines Webhostingplans und deren Rolle bei der Verwaltung Ihrer Websites.


## Websites, Webhostingpläne und Ressourcengruppen


Jede Website kann nur einem Webhostingplan gleichzeitig zugeordnet werden. Ein Webhostingplan ist einer Ressourcengruppe zugeordnet. Ressourcengruppen sind ein neues Konzept in Azure, das als Lebenszyklusgrenze für alle darin enthaltenen Ressourcen fungiert. Mit Ressourcengruppen können Sie alle Komponenten einer Anwendung zusammen verwalten.

Sie können mehrere Webhostingpläne in einer Ressourcengruppe haben, und jeder Webhostingplan hat einen separaten Satz von Funktionen, die von den zugeordneten Websites verwendet werden. Die folgende Abbildung veranschaulicht diese Beziehung:


![Ressourcengruppen und Webhostingpläne][Ressourcengruppen und Webhostingpläne]


Mit mehreren Webhostingplänen in einer einzigen Ressourcengruppe können Sie unterschiedliche Websites zu unterschiedlichen Ressourcen zuordnen, hauptsächlich den virtuellen Computern, auf denen Ihre Websites ausgeführt werden. Mit dieser Funktion können Sie die Ressourcen von Entwicklungs-, Test- und Produktionswebsites trennen, indem Sie z. B. einen Webhostingplan mit eigenen Ressourcen für Ihre Produktionswebsites zuordnen, und einen zweiten Webhostingplan für Ihre Entwicklungs- und Testwebsites.

Mit mehreren Webhostingplänen in einer einzigen Ressourcengruppe können Sie außerdem regionsübergreifende Anwendungen definieren. Eine hochverfügbare Website in zwei Regionen umfasst z. B. zwei Websites, je eine pro Region, und je eine Website pro Webhostingplan. Ein einer solchen Situation werden alle Websites einer einzigen Ressourcengruppe zugeordnet, die eine Anwendung definiert. Dank einer einzigen Ansicht einer Ressourcengruppe mit mehreren Webhostingplänen und mehreren Websites können Sie diese Websites schnell und einfach verwalten, steuern und überwachen. Neben der Verwaltung von Website-Ressourcen und Websites für eine Anwendung können Sie auch sonstige verwandte Azure-Ressourcen zuordnen, wie z. B. Azure SQL-Datenbanken und Teamprojekte.


## Wann sollte ich eine neue Ressourcengruppe erstellen, und wann einen neuen Webhostingplan?


Bei der Erstellung einer neuen Website sollten Sie die Erstellung einer neuen Ressourcengruppe erwägen, wenn die neue Website eine neuen Webanwendung darstellt. In diesem Fall sollten Sie eine neue Ressourcengruppe, einen zugeordneten Webhostingplan und eine Website erstellen. Beim Erstellen einer solchen neuen Website im neuen Azure-Vorschauportal mithilfe des Katalogs oder der neuen Option "Website + SQL" erstellt das Portal standardmäßig eine neue Ressourcengruppe und einen Webhostingplan für Ihre Website. Bei Bedarf können Sie diese Standardwerte überschreiben.


![Erstellen eines neuen Webhostingplans][Erstellen eines neuen Webhostingplans]


Sie können jederzeit neue Websites oder andere Ressourcen zu einer vorhandenen Ressourcengruppe hinzufügen. Beim Erstellen einer neuen Website aus dem Kontext einer vorhandenen Ressourcengruppe verwendet der Assistent für neue Websites automatisch die vorhandene Ressource und den vorhandenen Webhostingplan. Diese Standardwerte können Sie bei Bedarf ebenfalls überschreiben. Beim Hinzufügen einer neuen Website zu einer vorhandenen Ressourcengruppe können Sie die Website entweder zu einem vorhandenen Webhostingplan hinzufügen (die Standardoption im neuen Azure-Vorschauportal), oder Sie können einen neuen Webhostingplan erstellen, dem die Website hinzugefügt werden soll.

Mit einem neuen Hostingplan können Sie einen neuen Satz von Ressourcen für Ihre Websites zuweisen und erhalten mehr Kontrolle über die Ressourcenzuordnung, da jeder Webhostingplan einen eigenen Satz virtueller Computer erhält. Da Sie Websites zwischen Webhostingplänen verschieben können, sofern sich diese in derselben Region befinden, ist die Entscheidung über die Erstellung eines neuen Webhostingplans weniger wichtig. Wenn eine Website zu viele Ressourcen verbraucht oder Sie einfach einige Websites auslagern möchten, können Sie einen neuen Webhostingplan erstellen und Ihre Websites dorthin verschieben.

Wenn Sie eine neue Website in einer anderen Region erstellen möchten und in dieser Region noch keinen Webhostingplan haben, müssen Sie in der Region zunächst einen Webhostingplan erstellen, um eine Website zuordnen zu können.

Beachten Sie, dass Sie keine Webhostingpläne oder Websites zwischen Ressourcengruppen verschieben können. Sie können auch keine Website zwischen Webhostingplänen verschieben, die sich in unterschiedlichen Regionen befinden.


## Vorhandene Ressourcengruppe im Azure-Vorschauportal


Wenn Sie bereits vorhandene Azure-Websites haben, werden Sie feststellen, dass all Ihre Websites im Azure-Vorschauportal angezeigt werden. Sie können all Ihre Websites als flache Liste anzeigen, indem Sie im linken Navigationsbereich auf **Durchsuchen** klicken und **Websites** auswählen:


![Anzeige aller Websites als flache Liste][Anzeige aller Websites als flache Liste]


Außerdem können Sie die für Sie erstellten Ressourcengruppen anzeigen, indem Sie im linken Navigationsbereich auf **Durchsuchen** klicken und **Ressourcengruppen** auswählen:


![Anzeige aller erstellten Ressourcengruppen][Anzeige aller erstellten Ressourcengruppen]


Beachten Sie, dass in allen Regionen, in denen Sie bereits Websites haben, eine Standard-Ressourcengruppe automatisch für Sie erstellt wurde. Der automatisch generierte Ressourcengruppenname für Websites ist *Default-Web-<location name>*, wobei der Standortname der Name einer Azure-Region ist (z. B. *Default-Web-WestUS*). In jeder Ressourcengruppe finden Sie all Ihre vorhandenen Websites für die Region der Gruppe. Alle Websites, die Sie in der Vergangenheit erstellt haben und in Zukunft entweder im normalen Azure-Portal oder im Azure-Vorschauportal erstellen werden, ist in beiden Portalen verfügbar.

Da jede Website einem Webhostingplan zugeordnet sein muss, haben wir Standard-Webhostingpläne für Ihre vorhandenen Websites in den einzelnen Regionen nach dem folgenden Muster erstellt:


-   Alle Ihre **kostenlosen** Websites werden einem **Standard**-Webhostingplan zugeordnet, und die Preisebene wird auf **Kostenlos** gesetzt.
    
-   Alle Ihre **gemeinsam genutzten** Websites werden einem **Standard**-Webhostingplan zugeordnet, und die Preisebene wird auf **Gemeinsam genutzt** gesetzt.
    
-   All Ihre **Standard**-Websites werden einem Standard-Webhostingplan zugeordnet und die Preisebene auf **Standard** gesetzt.
    
    Der Name dieses Webhostingplans lautet **DefaultServerFarm**. Dieser Name wurde gewählt, um eine Legacy-API zu unterstützen. Der Name **ServerFarm** kann etwas irreführend sein, da er sich auf einen **Webhostingplan** bezieht. Es ist jedoch wichtig, zu verstehen, dass dies der Name eines Webhostingplans und nicht einer eigenen Entität ist.
    

    ## FAQ für Webhostingpläne

    
    **Frage**: Wie kann ich einen Webhostingplan erstellen?
    
    **Antwort**: Ein Webhostingplan ist ein Container, und daher können Sie keinen leeren Webhostingplan erstellen. Ein Webhostingplan wird jedoch bei der Erstellung einer Website explizit erstellt.
    
    Klicken Sie dazu im **Azure-Vorschauportal** auf **NEU** und wählen Sie **Website** aus, um das Fenster für die Website-Erstellung zu öffnen. In der ersten Abbildung unten sehen Sie das Symbol **NEU** unten links, und im zweiten Bild sehen Sie das Fenster für die **Website**-Erstellung, das **Webhostingplan**-Fenster und das **Preisebene**-Fenster:
    
    
    ![Neue Website erstellen][Neue Website erstellen]
    
    
    ![Fenster für Website, Webhostingplan und Preisebene][Fenster für Website, Webhostingplan und Preisebene]
    
    
    Für dieses Beispiel erstellen wir eine neue Website mit dem Namen **contosomarketing** und platzieren Sie in einem neuen Webhostingplan mit dem Namen **contoso**. Wählen Sie für diesen Webhostingplan die Preisebene **Klein Standard** aus. Weitere Informationen zu den Preisebenen für Webhostingpläne sowie zu Features, Preis- und Skalierungsoptionen der einzelnen Ebenen finden Sie unter [Webhostingpläne für Azure-Websites][Webhostingpläne für Azure-Websites].
    
    Beachten Sie, dass Sie Webhostingpläne auch im existierenden Azure-Portal erstellen können. Wählen Sie dazu im Assistenten für die **Schnellerfassung** den Eintrag **Neuen Webhostingplan erstellen** in der Dropdownloste **WEBHOSTINGPLAN** aus:
    
    
    ![Neuen Webhostingplan im existierenden Portal erstellen][Neuen Webhostingplan im existierenden Portal erstellen]
    
    
    Für dieses Beispiel erstellen wir eine neue Website mit dem Namen **northwind** und erstellen zudem einen neuen Webhostingplan. Daraufhin wird ein neuer Webhostingplan mit dem Namen **default0** erstellt, der die **northwind**-Website enthält. Alle auf diese Weise erstellten Webhostingpläne verwenden diese Namenskonvention, und Webhostingpläne können nach der Erstellung nicht mehr umbenannt werden. Die auf diese Weise erstellten Webhostingpläne werden außerdem in der Preisebene **Kostenlose** erstellt.
    
    **Frage**: Wie kann ich eine Website zu einem **Webhostingplan** zuweisen?
    
    **Antwort**: Websites werden im Rahmen des Erstellungsprozesses zu einem Webhostingplan zugewiesen. Klicken Sie dazu im **Azure-Vorschauportal** auf **NEU** und wählen Sie **Website** aus:
    
    
    ![Neue Website erstellen][1]
    
    
    Wählen Sie anschließend im Fenster für die Website-Erstellung den Hostingplan aus:
    
    
    ![Hostingplan auswählen][Hostingplan auswählen]
    
    
    Websites können auch im existierenden Azure-Portal in einem bestimmten Webhostingplan erstellt werden. Diese Erstellung erfolgt als Teil des **Schnellerfassungs**-Assistenten. Geben Sie die URL der Website ein und wählen Sie in der Dropdownloste **WEBHOSTINGPLAN** einen Plan aus, dem Sie die Website zuordnen möchten:
    
    
    ![Hostingplan im existierenden Portal auswählen][Hostingplan im existierenden Portal auswählen]
    
    
    **Frage**: Wie kann ich eine Website in einen anderen Webhostingplan verschieben?
    
    **Antwort**: Sie können Websites im Azure-Vorschauportal in einen anderen Webhostingplan verschieben. Sie können Websites zwischen Webhostingplänen in derselben geografischen Region verschieben, die zur gleichen Ressourcengruppe gehören.
    
    Um eine Website in einen anderen Plan zu verschieben, navigieren Sie zum Fenster für die Website, die Sie verschieben möchten. Klicken Sie anschließend auf **Webhostingplan**:
    
    
    ![Wählen Sie einen neuen oder existierenden Webhostingplan aus][Wählen Sie einen neuen oder existierenden Webhostingplan aus]
    
    
    Daraufhin öffnet sich das Webhostingplan-Fenster. An diesem Punkt können Sie entweder einen existierenden Webhostingplan auswählen oder einen neuen erstellen. Pläne in anderen geografischen Regionen oder anderen Ressourcengruppen sind ausgegraut und können nicht ausgewählt werden.
    
    Beachten Sie, dass jeder Webhostingplan eine eigene Preisebene hat. Wenn Sie eine Website aus einem Webhostingplan der Ebene **Kostenlos** in einen **Standard**-Webhostingplan verschieben, kann Ihre Website alle Features und Ressourcen der Standard-Ebene nutzen.
    
    
    **Frage**: Wie kann ich einen Webhostingplan skalieren?
    
    **Antwort**: Es gibt zwei Möglichkeiten, um einen Webhostingplan zu skalieren. Sie können einerseits den Webhostingplan und damit alle zugeordneten Websites hochskalieren. Wenn Sie die Preisebene eines Webhostingplans ändern, werden die Features und Ressourcen für alle Websites in diesem Webhostingplan ebenfalls geändert.
    
    In der folgenden Abbildung sehen Sie das Fenster **Webhostingplan** und das Fenster **Preisebene**. Klicken Sie auf den Teil **Preisebene** im Fenster **Webhostingplan**, um das Fenster **Preisebene** zu erweitern, in dem Sie die Preisebene für den Webhostingplan ändern können:
    
    
    ![Fenster Webhostingplan und Preisebene][Fenster Webhostingplan und Preisebene]
    
    
    Alternativ können Sie Ihren Plan hochskalieren, indem Sie die Anzahl der Instanzen in Ihrem Webhostingplan erhöhen. In der folgenden Abbildung sehen Sie die Fenster **Webhostingplan** und **Skalierung**. Klicken Sie auf den Bereich Skalierung im Fenster **Webhostingplan**, um den Bereich zu erweitern und die Anzahl der Instanzen für den Plan zu ändern:
    
    
    ![Ändern der Anzahl der Instanzen eines Webhostingplans][Ändern der Anzahl der Instanzen eines Webhostingplans]
    
    
    Da der Webhostingplan in der obigen Abbildung für die **Standard**-Preisebene konfiguriert ist, ist die Option **AutoScale** aktiviert.
    
    Sie können diesen Vorgang im normalen Azure-Portal in der Registerkarte **Skalierung** ausführen, wie unten gezeigt:
    
    
    ![Ändern der Anzahl der Instanzen für einen Hostingplan im existierenden Portal][Ändern der Anzahl der Instanzen für einen Hostingplan im existierenden Portal]
    
    
    **Frage**: Wie kann ich einen Webhostingplan löschen?
    
    **Antwort**: Um einen Webhostingplan löschen zu können, müssen Sie zunächst alle zugeordneten Websites löschen. Wenn Sie alle Websites in einem Webhostingplan gelöscht haben, können Sie den Webhostingplan im entsprechenden Fenster löschen:
    
    
    ![Löschen eines Webhostingplans][Löschen eines Webhostingplans]
    
    
    Im aktuellen Azure-Portal wird der Webhostingplan automatisch gelöscht, wenn Sie die letzte zugeordnete Website löschen.
    
    **Frage**: Wie kann ich einen Webhostingplan überwachen?
    
    **Antwort**: Sie können Webhostingpläne in den entsprechenden Bereichen im Webhostingplan-Fenster überwachen:
    
    
    ![Überwachen eines Webhostingplans][Überwachen eines Webhostingplans]
    
    
    Sie können die Überwachungskontrolle anpassen, indem Sie mit der rechten Maustaste auf die Kontrolle klicken und **Abfrage bearbeiten** auswählen:
    
    
    ![Bearbeiten der Überwachungskontrollen][Bearbeiten der Überwachungskontrollen]
    
    
    Die folgenden Metriken stehen zur Verfügung:
    

-   CPU-Auslastung
    
-   Speicherauslastung
    
-   Warteschlangenlänge des Datenträgers
    
-   HTTP-Warteschlangenlänge.
    
    Diese Metriken zeigen die durchschnittliche Nutzung in den Instanzen, die zu einem Webhostingplan gehören. All diese Metriken können zum Einrichten von Alarmen sowie für Regeln zur automatischen Skalierung verwendet werden.
    

    ## Schlussfolgerungen

    
    Webhostingpläne stellen einen Satz an Funktionen und Kapazitäten dar, die Sie über Ihre Websites freigeben können. Webhostingpläne bieten die Flexibilität, bestimmte Websites zu einem bestimmten Ressourcensatz (virtuellen Computern) zuzuordnen, und die Ressourcenzuteilung und -Nutzung für Websites weiter zu optimieren.

  [Ressourcengruppen und Webhostingpläne]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png
  [Erstellen eines neuen Webhostingplans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png
  [Anzeige aller Websites als flache Liste]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png
  [Anzeige aller erstellten Ressourcengruppen]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png
  [Neue Website erstellen]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png
  [Fenster für Website, Webhostingplan und Preisebene]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png
  [Webhostingpläne für Azure-Websites]: http://go.microsoft.com/?linkid=9845586
  [Neuen Webhostingplan im existierenden Portal erstellen]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png
  [1]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png
  [Hostingplan auswählen]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png
  [Hostingplan im existierenden Portal auswählen]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png
  [Wählen Sie einen neuen oder existierenden Webhostingplan aus]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png
  [Fenster Webhostingplan und Preisebene]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png
  [Ändern der Anzahl der Instanzen eines Webhostingplans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png
  [Ändern der Anzahl der Instanzen für einen Hostingplan im existierenden Portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png
  [Löschen eines Webhostingplans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png
  [Überwachen eines Webhostingplans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png
  [Bearbeiten der Überwachungskontrollen]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png

<properties 
	pageTitle="Erstellen eines Azure-Suchdiensts im Portal" 
	description="Hinzufügen eines kostenlosen oder standardmäßigen Azure-Suchdiensts zu einem vorhandenen Abonnement über das Verwaltungsportal" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Erstellen eines Azure-Suchdiensts im Portal

Microsoft Azure Search ist ein neuer Dienst, mit dem Sie Suchfunktionen in benutzerdefinierte Anwendungen einbetten können. Er stellt eine Suchmaschine und Speicherplatz für Ihre Suchdaten zur Verfügung. Für den Zugriff auf und die Verwaltung dieser Daten verwenden Sie das Portal, ein .NET SDK oder eine REST-API. Wichtige Features sind automatische Vervollständigung von Abfragen, Fuzzyübereinstimmung, Treffermarkierung, Facettennavigation und Unterstützung mehrerer Sprachen. Weitere Informationen darüber, wie Azure Search funktioniert, finden Sie unter [Einführung in Azure Search](../fundamentals-azure-search-chappell/).

##Hinzufügen von Azure Search zu Ihrem Abonnement  

Als Administrator können Sie Search einem vorhandenen Abonnement wie folgt hinzufügen: kostenfrei, wenn Sie den gemeinsam genutzten Dienst auswählen, oder mit dem Standardpreis, wenn Sie sich für fest zugeordnete Ressourcen entscheiden. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf der Sprungleiste auf **Neu** | **Daten + Speicher** | **Search**.
 
     ![][1]

3. Konfigurieren Sie den Dienstnamen, die Preisstufe, die Ressourcengruppe, das Abonnement und den Speicherort. Diese Einstellungen sind erforderlich und können nicht mehr geändert werden, sobald der Dienst bereitgestellt wird.

     ![][2]

	- Der **Dienstname** muss eindeutig und klein geschrieben sein und darf weder mehr als 14 Zeichen noch Leerzeichen enthalten. Dieser Name wird Bestandteil des Endpunkts Ihres Azure-Suchdiensts. Weitere Informationen zu Namenskonventionen finden Sie unter [Benennungsregeln](https://msdn.microsoft.com/library/azure/dn857353.aspx). 
	
	- Die **Preisstufe** bestimmt Kapazität und Abrechnung. Beide Stufen stellen die gleichen Funktionen bereit, dies allerdings auf unterschiedlichen Ressourcenebenen. 
	
		- **Kostenlos** wird in Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Diese Version bietet ausreichend Kapazität für Lernprogramme und zum Schreiben von Code für Machbarkeitstests, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Eine Bereitstellung eines kostenlosen Diensts beansprucht in der Regel nur wenige Minuten.
		- **Standard** wird auf dedizierten Ressourcen ausgeführt und ist in hohem Maße skalierbar. Zunächst wird ein Standarddienst mit einem Replikat und einer Partition bereitgestellt, Sie können die Kapazität jedoch anpassen, sobald der Dienst erstellt ist. Eine Bereitstellung eines Standarddiensts dauert etwas länger, in der Regel ungefähr 15 Minuten.
	
	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-BLOB-Speicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste auf den Verwaltungsseiten im Portal gruppiert.
	
	- In **Abonnement** können Sie unter mehreren Abonnements wählen, wenn Sie über mehrere Abonnements verfügen.
	
	- **Speicherort** ist die Region des Rechenzentrums. Derzeit müssen sämtliche Ressourcen im selben Rechenzentrum ausgeführt werden. Verteilen von Ressourcen über mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen. 

Achten Sie auf Benachrichtigungen auf der Sprungleiste. Es wird eine Benachrichtigung angezeigt, sobald der Dienst verwendet werden kann.

<a id="sub-2"></a>
##Suchen nach dem Dienstnamen und den API-Schlüsseln (api-key) Ihres Azure-Suchdiensts

Nachdem der Dienst erstellt wurde, können Sie zum Portal zurückkehren und die URL sowie den `api-key` ermitteln. Für Verbindungen mit Ihrem Suchdienst benötigen Sie sowohl die URL als auch einen `api-key`, um den Aufruf zu authentifizieren. 

1. Klicken Sie auf der Sprungleiste auf **Startseite**, und klicken Sie dann auf den Suchdienst, um das Dashboard für den Dienst zu öffnen. 

2. Auf dem Dashboard des Diensts sehen Sie Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel.

  	![][3]

3. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Sie benötigen diese Informationen für die nächste Aufgabe, [Testen der Dienstvorgänge](#sub-4).

<a id="sub-3"></a>
##Upgrade auf die Standardstufe

Viele Kunden beginnen mit dem kostenlosen Dienst und führen dann ein Upgrade auf die Standardstufe durch, wenn die Abfrageleistung oder der Speicher für die Arbeitsauslastungen, die ausgeführt werden müssen, unzureichend geworden ist. Mit der Standardstufe erhalten Sie fest zugeordnete Ressourcen in einem Azure-Rechenzentrum, die ausschließlich von Ihnen verwendet werden können. Für Suchvorgänge benötigen Sie sowohl Speicher als auch Dienstreplikate. Bei der Anmeldung für die Standardsuche können Sie die Dienstkonfiguration optimieren, um mehr von der jeweils wichtigeren Ressource für Ihr Szenario zuzuweisen.

Damit Sie die Standardstufe verwenden können, wiederholen Sie die vorherigen Schritte in diesem Artikel, um einen neuen Suchdienst zu erstellen, wobei Sie die Preisstufe "Standard" auswählen. Das Einrichten fest zugeordneter Ressourcen kann einige Zeit in Anspruch nehmen (bis zu 15 Minuten oder mehr). 

Es gibt kein direktes Upgrade der kostenlosen Version. Beim Wechsel zu Standard und dessen Skalierungspotenzial müssen Sie einen neuen Dienst erstellen. Sie müssen die von Ihrer Suchanwendung verwendeten Indizes und Dokumente erneut laden.

Ein Suchdienst auf Standardstufe wird mit einem Replikat und einer Partition erstellt, kann jedoch problemlos auf höheren Ressourcenebenen neu skaliert werden.

1.	Kehren Sie zum Dashboard des Diensts zurück, nachdem der Dienst erstellt wurde. 

2.	Klicken Sie auf die Kachel **Skalieren**.

3.	Mit den Schiebereglern können Sie Replikate, Partitionen oder beides hinzufügen. 

Zusätzliche Replikate und Partitionen werden in Form von Sucheinheiten abgerechnet. Die insgesamt benötigten Sucheinheiten zum Unterstützen einer bestimmten Ressourcenkonfiguration wird auf der Seite angezeigt, während Sie Ressourcen hinzufügen. 

Unter [Preisdetails](http://go.microsoft.com/fwlink/p/?LinkID=509792) finden Sie Abrechnungsinformationen pro Einheit. Eine Entscheidungshilfe für die Konfiguration von Partitions- und Replikatkombinationen finden Sie unter [Grenzen und Einschränkungen](http://msdn.microsoft.com/library/azure/dn798934.aspx).

<a id="sub-4"></a>
##Testen der Dienstfunktionen

Als letzter Schritt bei der Konfiguration der Suche überprüfen Sie, ob Ihr Dienst einsatzbereit ist und über eine Clientanwendung angesprochen werden kann. Sie können die nachstehenden Links für eine codefreie Vorgehensweise zur Überprüfung der Dienstverfügbarkeit verwenden.

- [Gewusst wie: Verwenden von Chrome Postman mit Azure Search](../search-chrome-postman/)
- [Gewusst wie: Verwenden von Telerik Fiddler mit Azure Search](../search-fiddler/)

<!--Next steps and links -->
<a id="next-steps"></a>
##Nächste Schritte

Im folgenden zusätzlichen Informationsmaterial wird gezeigt, wie Suchanwendungen erstellt und verwaltet werden, die Azure Search verwenden.

- [Gewusst wie: Verwenden von Azure Search in .NET](../search-howto-dotnet-sdk/)

- [Verwalten Ihrer Suchlösung in Microsoft Azure](../search-manage/) 

- [Azure Search auf MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Channel 9-Video: Einführung in Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Suchen nach dem Dienstnamen und den API-Schlüsseln (api-key) Ihres Azure-Suchdiensts]: #sub-2
[Upgrade auf die Standardstufe]: #sub-3
[Testen der Dienstfunktionen]: #sub-4
[Nächste Schritte]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG


<!--HONumber=54--> 
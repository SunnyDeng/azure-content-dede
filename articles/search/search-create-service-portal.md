<properties
	pageTitle="Erstellen eines Azure Search-Diensts im Portal"
	description="Fügen Sie einem vorhandenen Abonnement über das Verwaltungsportal einen Azure Search-Dienst (Free oder Standard) hinzu."
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
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Erstellen eines Azure Search-Diensts im Portal

Microsoft Azure Search ist ein neuer Dienst, mit dem Sie Suchfunktionen in benutzerdefinierte Anwendungen einbetten können. Er stellt eine Suchmaschine und Speicherplatz für Ihre Suchdaten zur Verfügung. Zugriff und Verwaltung können über das Portal, über ein .NET-SDK oder über eine REST-API erfolgen. Zu den zentralen Features zählen Abfragen mit automatischer Vervollständigung, Fuzzyübereinstimmung, Trefferhervorhebung, Facettennavigation und Unterstützung mehrerer Sprachen. Weitere Informationen zu den Funktionen von Search finden unter [Einführung in Azure Search](fundamentals-azure-search-chappell/).

##Hinzufügen von Search zu Ihrem Abonnement  

Bei Verwendung der Shared-Option können Administratoren Search kostenlos zu einem vorhandenen Abonnement hinzufügen. Bei Verwendung fest zugeordneter Ressourcen gilt der Standardtarif.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie über die seitliche Navigationsleiste zu **Neu** > **Daten + Speicher** > **Search**.

     ![][1]

3. Konfigurieren Sie den Dienstnamen, den Tarif, die Ressourcengruppe, das Abonnement und den Ort. Diese Einstellungen sind erforderlich und können nach der Dienstbereitstellung nicht mehr geändert werden.

     ![][2]

	- Der **Dienstname** muss eindeutig sein und aus weniger als 15 Kleinbuchstaben ohne Leerzeichen bestehen. Dieser Name wird Bestandteil des Endpunkts Ihres Azure Search-Diensts. Weitere Informationen zu den Benennungskonventionen finden Sie unter [Benennungsregeln](https://msdn.microsoft.com/library/azure/dn857353.aspx).

	- Der **Tarif** bestimmt die Kapazität und die Abrechnung. Beide Tarife bieten die gleichen Features, aber mit unterschiedlichen Ressourcen.

		- **Free** wird auf Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Diese Version bietet genügend Kapazität für Tutorials sowie zum Schreiben von Code für Machbarkeitsstudien, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Die Bereitstellung eines kostenlosen Diensts dauert in der Regel nur wenige Minuten.
		- **Standard** wird auf fest zugeordneten Ressourcen ausgeführt und ist flexibel skalierbar. Ein Standarddienst wird zunächst mit einem Replikat und einer Partition bereitgestellt, die Kapazität kann nach der Diensterstellung jedoch angepasst werden. Die Bereitstellung eines Standarddiensts dauert länger (üblicherweise etwa 15 Minuten).

	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-Blobspeicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste in den Verwaltungsseiten im Portal gruppiert.

	- Unter **Abonnement** stehen ggf. mehrere Abonnements zur Auswahl (sofern Sie über mehrere Abonnements verfügen).

	- **Ort** ist die Rechenzentrumsregion. Derzeit müssen alle Ressourcen im gleichen Rechenzentrum ausgeführt werden. Die Aufteilung von Ressourcen auf mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen.

Achten Sie auf Benachrichtigungen in der Navigationsleiste. Wenn der Dienst verwendet werden kann, erscheint eine Benachrichtigung.

<a id="sub-2"></a>
##Ermitteln des Dienstnamens und der API-Schlüssel Ihres Azure Search-Diensts

Nach der Diensterstellung können Sie zum Portal zurückkehren, um die URL oder den API-Schlüssel (`api-key`) zu ermitteln. Für Verbindungen mit Ihrem Search-Dienst benötigen Sie sowohl die URL als auch einen API-Schlüssel (`api-key`), um den Aufruf zu authentifizieren.

1. Klicken Sie auf der Navigationsleiste auf **Startseite** und anschließend auf den Search-Dienst, um das Service-Dashboard zu öffnen.

2. Auf dem Service-Dashboard werden Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel angezeigt.

  	![][3]

3. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Diese benötigen Sie für Ihre nächste Aufgabe: [Testen der Dienstfunktionen](#sub-4).

<a id="sub-3"></a>
##Upgraden auf die Standard-Ebene

Viele Kunden beginnen mit dem kostenlosen Dienst und führen dann ein Upgrade auf Standard durch, wenn die Abfrageleistung oder der Speicher für die erforderlichen Workloads nicht mehr ausreicht. Die Standard-Ebene bietet fest zugeordnete Ressourcen in einem Azure-Rechenzentrum, die ausschließlich von Ihnen genutzt werden können. Für Suchvorgänge benötigen Sie sowohl Speicher als auch Dienstreplikate. Bei der Anmeldung für die Standardsuche können Sie die Dienstkonfiguration optimieren, um mehr von der jeweils wichtigeren Ressource für Ihr Szenario zuzuweisen.

Wiederholen Sie zum Verwenden der Standard-Ebene die weiter oben beschriebenen Schritte für die Erstellung eines neuen Search-Diensts, und wählen Sie dabei den Standard-Tarif aus. Die Einrichtung fest zugeordneter Ressourcen kann einige Zeit in Anspruch nehmen (etwa 15 Minuten oder mehr).

Für die kostenlose Version steht kein direktes Upgrade zur Verfügung. Beim Wechsel zu Standard und dessen Skalierungspotenzial müssen Sie einen neuen Dienst erstellen. Sie müssen die von Ihrer Suchanwendung verwendeten Indizes und Dokumente erneut laden.

Ein Search-Dienst auf der Standard-Ebene wird mit je einem Replikat und einer Partition erstellt, kann jedoch schnell und einfach auf höhere Ressourcenebenen skaliert werden.

1.	Kehren Sie nach der Diensterstellung zum Service-Dashboard zurück.

2.	Klicken Sie auf die Kachel **Skalieren**.

3.	Mit den Schiebereglern können Sie Replikate, Partitionen oder beides hinzufügen.

Zusätzliche Replikate und Partitionen werden in Sucheinheiten abgerechnet. Die insgesamt benötigten Sucheinheiten zum Unterstützen einer bestimmten Ressourcenkonfiguration werden auf der Seite angezeigt, während Sie Ressourcen hinzufügen.

Unter [Preisdetails](http://go.microsoft.com/fwlink/p/?LinkID=509792) finden Sie Abrechnungsinformationen pro Einheit. Eine Entscheidungshilfe für die Konfiguration von Partitions- und Replikatkombinationen finden Sie unter [Limits und Einschränkungen](search-limits-quotas-capacity.md).

<a id="sub-4"></a>
##Testen der Dienstfunktionen

Als letzter Schritt bei der Konfiguration der Suche überprüfen Sie, ob Ihr Dienst einsatzbereit ist und über eine Clientanwendung angesprochen werden kann. Unter den folgenden Links finden Sie jeweils eine codefreie Methode zum Überprüfen der Dienstverfügbarkeit:

- [Verwenden von Chrome Postman mit Azure Search](search-chrome-postman.md)
- [Verwenden von Telerik Fiddler mit Azure Search](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
##Nächste Schritte

Unter den folgenden Links finden Sie zusätzliches Material für die Erstellung und Verwaltung von Suchanwendungen mit Azure Search:

- [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)

- [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md)

- [Azure Search bei MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Channel 9-Video: Introduction to Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=Sept15_HO2-->
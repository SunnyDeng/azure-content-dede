<properties
	pageTitle="Erstellen eines Azure Search-Diensts im Portal | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Fügen Sie Azure Search (Free oder Standard) über das klassische Azure-Portal einem vorhandenen Abonnement hinzu. Azure Search ist ein in der Cloud gehosteter Suchdienst für benutzerdefinierte Apps."
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
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Erstellen eines Azure Search-Diensts im klassischen Azure-Portal

Microsoft Azure Search ist ein gehosteter Cloudsuchdienst, mit dem Sie Suchfunktionen in benutzerdefinierte Anwendungen einbetten können. Er stellt eine Suchmaschine und Speicherplatz für Ihre Suchdaten zur Verfügung. Zugriff und Verwaltung können über das klassische Azure-Portal, ein .NET SDK oder eine REST-API erfolgen. Zu den zentralen Features zählen Abfragen mit automatischer Vervollständigung, Fuzzyübereinstimmung, Trefferhervorhebung, Facettennavigation, Bewertungsprofile und Unterstützung mehrerer Sprachen. Weitere Informationen zu den Funktionen von Azure Search finden unter [Was ist Azure Search?](search-what-is-azure-search.md).

## Kostenloses Hinzufügen von Azure Search zu Ihrem Abonnement

Bei Verwendung der Option „Shared“ können Administratoren Azure Search einem vorhandenen Azure-Abonnement kostenlos hinzufügen. Bei Verwendung fest zugeordneter Ressourcen gilt der Standardtarif.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie über die seitliche Navigationsleiste zu **Neu** > **Daten + Speicher** > **Search**.

     ![][1]

3. Konfigurieren Sie den Dienstnamen, den Tarif, die Ressourcengruppe, das Abonnement und den Ort. Diese Einstellungen sind erforderlich und können nach der Dienstbereitstellung nicht mehr geändert werden.

     ![][2]

	- Der **Dienstname** muss eindeutig sein und aus weniger als 15 Kleinbuchstaben ohne Leerzeichen bestehen. Dieser Name wird Bestandteil des Endpunkts Ihres Azure Search-Diensts. Weitere Informationen zu den Benennungsregeln finden Sie unter [Benennungskonventionen](https://msdn.microsoft.com/library/azure/dn857353.aspx).

	- Der **Tarif** bestimmt Kapazität und Abrechnung. Beide Tarife bieten die gleichen Features, aber mit unterschiedlichen Ressourcen.

		- **Free** wird auf Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Diese Version bietet genügend Kapazität für Tutorials sowie zum Schreiben von Code für Machbarkeitsstudien, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Die Bereitstellung eines kostenlosen Diensts dauert in der Regel nur wenige Minuten.
		- **Standard** wird auf fest zugeordneten Ressourcen ausgeführt und ist flexibel skalierbar. Ein Standarddienst wird zunächst mit einem Replikat und einer Partition bereitgestellt, die Kapazität kann nach der Diensterstellung jedoch angepasst werden. Die Bereitstellung eines Standarddiensts dauert länger (üblicherweise etwa 15 Minuten).

	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, der Web-Apps-Funktion in Azure App Service und Azure-Blob-Speicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste in den Verwaltungsseiten im Portal gruppiert.

	- Unter **Abonnement** stehen ggf. mehrere Abonnements zur Auswahl (sofern Sie über mehrere Abonnements verfügen).

	- **Ort** ist die Rechenzentrumsregion. Derzeit müssen alle Ressourcen im gleichen Rechenzentrum ausgeführt werden. Die Aufteilung von Ressourcen auf mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen.

Achten Sie auf Benachrichtigungen in der Navigationsleiste. Wenn der Dienst verwendet werden kann, erscheint eine Benachrichtigung.

<a id="sub-3"></a>
## Hinzufügen eines Suchdiensts im Tarif „Standard“, um fest zugeordnete Ressourcen zu erhalten

Viele Kunden beginnen mit dem kostenlosen Dienst und wechseln dann zum Tarif „Standard“, um größere Workloads zu bewältigen. Mit dem Tarif „Standard“ erhalten Sie fest zugeordnete Ressourcen in einem Azure-Datencenter, die ausschließlich von Ihnen genutzt werden können.

Für Azure Search-Vorgänge benötigen Sie sowohl Speicher als auch Dienstreplikate. Im Gegensatz zum kostenlosen Dienst, der über keine Option zum Hinzufügen von Ressourcen verfügt, können Sie mit dem Tarif „Standard“ eine Skalierung durchführen, um mehr Speicher- oder Abfrageunterstützung hinzufügen, sodass Sie die für Ihr Szenario wichtigere Ressource stärken können.

Um den Tarif „Standard“ zu verwenden, müssen Sie einen neuen Suchdienst auf der entsprechenden Preisebene erstellen. Sie können die vorherigen Schritte in diesem Artikel wiederholen, um einen neuen Azure Search-Dienst zu erstellen. Die Einrichtung fest zugeordneter Ressourcen kann einige Zeit in Anspruch nehmen (etwa 15 Minuten oder mehr).

Für die kostenlose Version steht kein direktes Upgrade zur Verfügung. Beim Wechsel zu Standard und dessen Skalierungspotenzial müssen Sie einen neuen Dienst erstellen. Sie müssen die von Ihrer Suchanwendung verwendeten Indizes und Dokumente erneut laden.

Ein Azure Search-Dienst auf der Standard-Ebene wird mit je einem Replikat und einer Partition erstellt, kann jedoch schnell und einfach auf höhere Ressourcenebenen skaliert werden.

1.	Kehren Sie nach der Diensterstellung zum Service-Dashboard zurück.

2.	Klicken Sie auf die Kachel **Skalieren**.

3.	Mit den Schiebereglern können Sie Replikate, Partitionen oder beides hinzufügen.

Zusätzliche Replikate und Partitionen werden in Sucheinheiten abgerechnet. Die insgesamt benötigten Sucheinheiten zum Unterstützen einer bestimmten Ressourcenkonfiguration werden auf der Seite angezeigt, während Sie Ressourcen hinzufügen.

Unter [Preisdetails](http://go.microsoft.com/fwlink/p/?LinkID=509792) finden Sie Abrechnungsinformationen pro Einheit. Eine Entscheidungshilfe für die Konfiguration von Partitions- und Replikatkombinationen finden Sie unter [Limits und Einschränkungen](search-limits-quotas-capacity.md).

<a id="sub-2"></a>
## Ermitteln des Dienstnamens und der API-Schlüssel Ihres Azure Search-Diensts

Nach der Diensterstellung können Sie zum klassischen Azure-Portal zurückkehren, um die URL oder den API-Schlüssel (`api-key`) zu ermitteln. Für Verbindungen mit Ihrem Azure Search-Dienst benötigen Sie sowohl die URL als auch einen API-Schlüssel (`api-key`), um den Aufruf zu authentifizieren.

1. Klicken Sie auf der Navigationsleiste auf **Startseite** und anschließend auf den Azure Search-Dienst, um das Service-Dashboard zu öffnen.

2. Auf dem Service-Dashboard werden Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel angezeigt.

  	![][3]

3. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Diese benötigen Sie für Ihre nächste Aufgabe: [Testen der Dienstfunktionen](#sub-4).


<a id="sub-4"></a>
## Testen der Dienstfunktionen

Als letzter Schritt bei der Konfiguration von Azure Search überprüfen Sie, ob Ihr Dienst einsatzbereit ist und über eine Clientanwendung angesprochen werden kann. Sie können [Fiddler mit Azure Search](search-fiddler.md) verwenden, um die Dienstverfügbarkeit zu überprüfen.

<!--Next steps and links -->
<a id="next-steps"></a>
## Nächste Schritte

Unter den folgenden Links finden Sie weitere Informationen zur Erstellung und Verwaltung von Suchanwendungen mit Azure Search:

- [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)

- [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md)

- [Azure Search bei MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Channel 9-Video: Introduction to Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=AcomDC_1223_2015-->
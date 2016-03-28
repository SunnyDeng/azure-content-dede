<properties
	pageTitle="Erstellen eines Azure Search-Diensts mit dem Azure-Portal | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erlernen Sie das Bereitstellen eines Azure Search-Diensts über das Azure-Portal."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# Erstellen eines Azure Search-Diensts über das Azure-Portal

In diesem Leitfaden lernen Sie, wie Sie einen Azure Search-Dienst mithilfe des [Azure-Portals](https://portal.azure.com/) erstellen (oder bereitstellen).

In diesem Leitfaden wird davon ausgegangen, dass Sie bereits über ein Azure-Abonnement verfügen und sich beim Azure-Portal anmelden können.

## I. Suchen Sie Azure Search im Azure-Portal.
1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich an.
1. Klicken Sie in der oberen linken Ecke auf das Pluszeichen („+“).
2. Wählen Sie „Daten und Speicher“ aus.
3. Wählen Sie „Azure Search“ aus.

![](./media/search-create-service-portal/find-search.png)

## II. Wählen Sie einen Dienstnamen und einen URL-Endpunkt für den Dienst.
1. Ihr Dienstname wird Teil der Endpunkt-URL für den Azure Search-Dienst. Anhand dieser URL führen Sie Ihre API-Aufrufe zum Verwalten und Verwenden des Search-Diensts durch.
2. Geben Sie den Dienstnamen im Feld „URL“ ein. Der Dienstname:
  * darf nur Kleinbuchstaben, Ziffern oder Bindestriche („-“) enthalten.
  * darf in den ersten beiden und als letztes Zeichen keinen Bindestrich („-“) enthalten.
  * darf keine aufeinander folgenden Bindestriche enthalten („--“).
  * ist auf eine Länge zwischen 2 und 60 Zeichen beschränkt.


## III. Wählen Sie ein Abonnement zum Verwalten Ihres Diensts aus.
Wenn Sie über mehrere Abonnements verfügen, können Sie auswählen, welches davon diesen Azure Search-Dienst enthalten soll.

## IV. Erstellen Sie eine Ressourcengruppe für Ihren Dienst.
Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus. Eine Ressourcengruppe ist eine Sammlung von Azure-Diensten und -Ressourcen, die zusammen verwendet werden. Wenn Sie beispielsweise mit Azure Search eine SQL-Datenbank indizieren, sollten diese beiden Dienste der gleichen Ressourcengruppe angehören.

## V. Wählen Sie den Standort aus, an dem Ihr Dienst gehostet wird.
Als Azure-Dienst kann Azure Search in Rechenzentren auf der ganzen Welt gehostet werden. Beachten Sie, dass die [Preise sich je nach geografischer Lage unterscheiden können](https://azure.microsoft.com/pricing/details/search/).

## VI. Wählen Sie Ihren Tarif aus.
[Azure Search wird derzeit in mehrere Tarifen angeboten](https://azure.microsoft.com/pricing/details/search/): Free, Basic oder Standard. Jeder Tarif verfügt über eigene [Kapazitäten und Grenzwerte](search-limits-quotas-capacity.md).

In diesem Fall haben wir den Standard-Tarif für unseren Dienst gewählt.

## VII. Wählen Sie die Schaltfläche „Erstellen“, um Ihren Dienst bereitzustellen.

![](./media/search-create-service-portal/create-service.png)

## VIII. Skalieren Sie den Dienst.

Sobald die Bereitstellung Ihres Diensts abgeschlossen ist, können Sie ihn Ihren Anforderungen entsprechend skalieren. Wenn Sie den Standard-Tarif für Ihren Azure Search-Dienst ausgewählt haben, können Sie Ihren Dienst in zwei Dimensionen skalieren: Replikate und Partitionen. Wenn Sie den Basic-Tarif ausgewählt haben, können Sie nur Replikate hinzufügen.

*__Partitionen__* ermöglichen Ihrem Dienst das Speichern und Durchsuchen weiterer Dokumente.

Mit *__Replikaten__* kann Ihr Dienst eine höhere Auslastung von Suchabfragen verarbeiten. [Ein Dienst benötigt zwei Replikate, um eine Nur-Lese-SLA zu erreichen, und drei Replikate zum Erreichen einer SLA mit Lese-/Schreibzugriff](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Öffnen Sie im Azure-Portal das Verwaltungsblatt für Ihren Azure Search-Dienst.
2. Wählen Sie auf dem Blatt „Einstellungen“ die Option „Skalieren“ aus.
3. Sie können den Dienst skalieren, indem Sie Replikate oder Partitionen hinzufügen.
  * Der Dienst kann nicht über 36 Sucheinheiten hinaus skaliert werden. Die Gesamtzahl von Sucheinheiten entspricht dem Produkt der Replikate und Partitionen (Replikate * Partitionen = Sucheinheiten insgesamt).
  * Wenn Sie den Basic-Tarif ausgewählt haben, können Sie nur auf drei Replikate skalieren. Basic-Dienste sind an eine einzelne Partition gebunden.

![](./media/search-create-service-portal/scale-service.png)

## Weiter
Nach der Bereitstellung eines Azure Search-Diensts können Sie einen [Azure Search-Index definieren](search-what-is-an-index.md), damit Sie Ihre Daten hochladen und durchsuchen können.

<!---HONumber=AcomDC_0316_2016-->
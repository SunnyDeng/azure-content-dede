<properties
	pageTitle="Batch-Dienst – Kontingente und Limits | Microsoft Azure"
	description="Erfahren Sie mehr über Kontingente, Limits und Einschränkungen für die Verwendung des Azure Batch-Diensts."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>



# Kontingente und Limits für den Azure Batch-Dienst

Dieser Artikel listet die Standard- und Höchstwerte bestimmter Ressourcen auf, die Sie mit dem Azure Batch-Dienst verwenden können. Die meisten dieser Limits sind Kontingente, die Azure auf Ihr Abonnement oder Batch-Konten anwendet.

Wenn Sie Batch-Workloads in der Produktion ausführen möchten, müssen Sie möglicherweise ein oder mehrere Kontingente über den Standardwert erhöhen. Zum Erhöhen eines Kontingents können Sie kostenlos eine Anfrage an den Onlinekundensupport richten.

>[AZURE.NOTE]Bei einem Kontingent handelt es sich um ein Kreditlimit und keine Kapazitätsgarantie. Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support.

## Abonnementkontingente
Ressource|Standardlimit|Maximales Limit
---|---|---
Batch-Konten pro Region und Abonnement|1|20

## Batch-Kontokontingente
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Andere Limits
Ressource|Maximales Limit
---|---
Aufgaben pro Serverknoten|4 x Anzahl der Kerne des Knotens

## Anzeigen von Batch-Kontingenten

Sie können die Kontingente Ihres Batch-Kontos im [Azure-Vorschauportal](https://portal.azure.com) anzeigen.

1. Klicken Sie im Vorschauportal auf **Batch-Konten** und dann auf den Namen Ihres Batch-Kontos.

2. Klicken Sie auf dem Blatt für Ihr Konto auf **Einstellungen** > **Eigenschaften**.

	![Batch-Kontokontingente][account_quotas]

3. Überprüfen Sie auf dem Blatt **Eigenschaften** die Kontingente, die derzeit für das Batch-Konto gelten.

## Erhöhen eines Kontingents

Anhand der folgenden Schritte können Sie die Erhöhung eines Kontingents im Azure-Vorschauportal anfordern (Sie können die Erhöhung auch im [Azure-Portal](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) anfragen).

1. Klicken Sie auf dem Dashboard des Vorschauportals auf **Hilfe und Support**.

2. Klicken Sie auf **Create support request > Grundlagen**.

3. Gehen Sie auf dem Blatt **Grundlagen** folgendermaßen vor:

	a. Wählen Sie in **Problemtyp** die Option **Kontingent** aus.

	b. Wählen Sie Ihr Abonnement aus.

	c. Wählen Sie in **Service** die Option **Batch-Dienst** aus.

	d. Wählen Sie in **Supportplan** die Option **Azure Support Plan - Developer** aus.

	Klicken Sie auf **Weiter**.

4. Gehen Sie auf dem Blatt **Problem** folgendermaßen vor:

	a. Wählen Sie in **Problemtyp** die Option **Batch** aus.

	b. Listen Sie in **Details** die Kontingente, die Sie in einem bestimmten Konto ändern möchten, mit den neuen Grenzwerten auf.

	Klicken Sie auf **Weiter**.

5. Geben Sie auf dem Blatt **Kontaktinformationen** Ihre Kontaktinformationen an, und klicken Sie auf **Weiter**.

6. Klicken Sie auf **Erstellen**, um die Supportanfrage zu übermitteln.

Der Azure-Support tritt mit Ihnen in Kontakt. Die Bearbeitung der Anfrage kann bis zu 2 Werktage in Anspruch nehmen.

## Verwandte Themen

* [Erstellen und Verwalten eines Batch Azure-Kontos](batch-account-create-portal.md)

* [API-Grundlagen für Azure Batch](batch-api-basics.md)

* [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=Nov15_HO1-->
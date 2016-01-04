<properties
	pageTitle="Erstellen eines Azure Batch-Kontos | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein Azure Batch-Konto im Azure-Portal erstellen, um umfangreiche parallele Workloads in der Cloud auszuführen."
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
	ms.date="11/10/2015"
	ms.author="danlep"/>



# Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Portal

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

In diesem Artikel wird beschrieben, wie Sie das [Azure-Portal](https://portal.azure.com) zum Erstellen und Verwalten eines Azure Batch-Kontos und der zugehörigen Einstellungen wie der Kontoschlüssel verwenden. Sie benötigen eine Batch-Konto-URL und den zugehörigen Zugriffsschlüssel, um alle Batch-API-Anforderungen zu authentifizieren. Außerdem ordnen Sie alle Batch-Ressourcen (z. B. Pools, Aufträge und Aufgaben) für Ihre Batch-Workload einem bestimmten Batch-Konto zu.

>[AZURE.NOTE]Derzeit unterstützt das Vorschauportal Funktionen für die Batch-Kontenverwaltung und das Anzeigen einiger Kontoressourcen. Sämtliche Batch-Features stehen Entwicklern über die Batch-APIs zur Verfügung.

## Erstellen eines Batch-Kontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Neu** > **Compute** > **Batch-Dienst**.

	![Batch im Marketplace][marketplace_portal]

3. Prüfen Sie die Informationen, und klicken Sie auf **Erstellen**.

4. Geben Sie die folgenden Informationen auf dem Blatt**Neues Batch-Konto** ein:

	a. Geben Sie unter **Kontoname** einen eindeutigen Namen ein, der in der Batch-Konto-URL verwendet werden soll.

	>[AZURE.NOTE]Der Batch-Kontoname muss in Azure eindeutig sein und zwischen 3 und 24 Zeichen enthalten, die nur Kleinbuchstaben und Zahlen umfassen dürfen.

	b. Wenn Sie über mehrere Abonnements verfügen, klicken Sie auf **Abonnement**, um ein verfügbares Abonnement auszuwählen, in dem das Konto erstellt wird.

	c. Klicken Sie auf **Ressourcengruppe** wählen eine vorhandene Ressourcengruppe für das Konto aus, oder erstellen eine neue.

	d. Wählen Sie in **Speicherort** eine Azure-Region aus, in der Batch verfügbar ist.

	![Erstellen eines Batch-Kontos][account_portal]

5. Klicken Sie auf **Erstellen**, um die Kontoerstellung abzuschließen.

## Verwalten von Zugriffsschlüsseln und Konteneinstellungen
Nachdem das Konto erstellt wurde, finden Sie es im Portal. Sie können dort Zugriffsschlüssel, autorisierte Benutzer und andere Einstellungen verwalten.

Die Batch-Konto-URL wird in Konto **Grundlagen** angezeigt. Die URL liegt im Format `https://<account_name>.<region>.batch.azure.com` vor.

Klicken Sie zum Anzeigen und Verwalten der Zugriffsschlüssel auf das Schlüsselsymbol.

![Batch-Kontoschlüssel][account_keys]

## Weitere wichtige Informationen zum Batch-Konto

* Andere Methoden zum Erstellen und Verwalten von Batch-Konten bieten [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md) und die [Batch Management .NET-Bibliothek](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/).


* Azure berechnet keine Gebühren für ein Batch-Konto. Sie erhalten nur eine Abrechnung für die Verwendung von Azure-Computerressourcen und anderen Diensten, wenn Ihre Workloads ausgeführt werden (siehe [Batch Preise](https://azure.microsoft.com/pricing/details/batch/)).

* Sie können mehrere Batch-Workloads in einem Batch-Konto ausführen oder die Workloads auf Batch-Konten in verschiedenen Azure-Regionen aufteilen.

* Wenn Sie mehrere umfangreiche Batch-Workloads ausführen, sollten Sie bestimmte [Kontingente und Limits für den Batch-Dienst](batch-quota-limit.md) beachten, die für Ihr Azure-Abonnement und jedes Batch-Konto gelten. Sie finden die aktuell gültigen Kontingente für ein Batch-Konto im Vorschauportal unter den Kontoeigenschaften.

## Nächste Schritte

* In der [Übersicht über Azure Batch-Features](batch-api-basics.md) finden Sie weitere Informationen zu den Batch-Konzepten.

* Erste Schritte beim Entwickeln Ihrer ersten Anwendung mit der [.NET-Clientbibliothek für Batch](batch-dotnet-get-started.md)

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_1203_2015-->
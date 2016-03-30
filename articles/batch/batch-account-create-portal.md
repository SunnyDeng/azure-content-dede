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
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="marsma"/>

# Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Portal

> [AZURE.SELECTOR]
- [Azure-Portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Das [Azure-Portal][azure_portal] enthält Tools zum Erstellen und Verwalten eines Azure Batch-Kontos, die Sie für die Verarbeitung paralleler Workloads in großem Umfang verwenden können. In diesem Artikel wird die Erstellung eines Batch-Kontos mit dem Portal beschrieben, und es werden einige der wichtigsten Einstellungen und Eigenschaften eines Batch-Kontos erläutert. Für die Anwendungen und Dienste, die Sie mit Batch entwickeln, sind beispielsweise die URL Ihres Kontos und ein Zugriffsschlüssel zum Kommunizieren mit den APIs des Batch-Diensts erforderlich. Die URL und der Schlüssel sind im Azure-Portal enthalten.

>[AZURE.NOTE] Das Azure-Portal unterstützt derzeit eine Teilmenge der Features, die im Batch-Dienst verfügbar sind, z.B. Kontoerstellung und die Verwaltung von Kontoeinstellungen und Eigenschaften. Der vollständige Featuresatz von Batch, z.B. das Erstellen und Ausführen von Aufträgen und Aufgaben, ist für Entwickler über die Batch-APIs verfügbar.

## Erstellen eines Batch-Kontos

1. Melden Sie sich beim [Azure-Portal][azure_portal] an.

2. Klicken Sie auf **Neu** > **Compute** > **Batch-Dienst**.

	![Batch im Marketplace][marketplace_portal]

3. Prüfen Sie die Informationen auf dem Blatt **Batchdienst**, und klicken Sie dann auf **Erstellen**. Beachten Sie, dass die Auswahl des Bereitstellungsmodells deaktiviert ist. Dies liegt daran, dass für Batch nur das Ressourcengruppen-Bereitstellungsmodell verwendet wird.

	![Batch-Dienst, Blatt „Erstellen“ im Azure-Portal][3]

4. Das Blatt **Neues Batch-Konto** wird angezeigt. Sehen Sie sich unten die Beschreibungen der einzelnen Blattelemente unter den Punkten *a* bis *e* an.

    ![Erstellen eines Batch-Kontos][account_portal]

	a. **Kontoname:** Geben Sie einen eindeutigen Namen für das Batch-Konto an. Dieser Name muss innerhalb der Azure-Region, in der das Konto erstellt wird, eindeutig sein (siehe *Standort* weiter unten). Er darf nur Kleinbuchstaben und Zahlen enthalten und muss 3 bis 24 Zeichen lang sein.

	b. **Abonnement:** Wählen Sie ein Abonnement aus, unter dem das Batch-Konto erstellt werden soll. Wenn Sie nur über ein Abonnement verfügen, ist es standardmäßig ausgewählt.

	c. **Ressourcengruppe:** Wählen Sie eine Ressourcengruppe für das neue Batch-Konto aus, oder erstellen Sie eine neue, falls Ihr Abonnement keine Ressourcengruppen enthält.

	d. **Standort:** Wählen Sie eine Azure-Region aus, in der das Batch-Konto erstellt werden soll. Nur die Bereiche, die von Ihrem Abonnement und der Ressourcengruppe unterstützt werden, werden als Optionen angezeigt.

    e. **Speicherkonto** (optional): Sie können Ihrem neuen Batch-Konto ein Speicherkonto zuordnen (per Verknüpfung). Beim Feature [Anwendungspakete](batch-application-packages.md) von Batch werden das verknüpfte Speicherkonto für die Speicherung und Wiederherstellung von Anwendungspaketen verwendet. Weitere Informationen zu diesem Feature finden Sie unter [Anwendungsbereitstellung mit Azure Batch-Anwendungspaketen](batch-application-packages.md).

     > [AZURE.TIP] Für das erneute Generieren von Schlüsseln in einem verknüpften Speicherkonto sind besondere Punkte zu beachten. Ausführliche Informationen finden Sie unter [Zu berücksichtigende Aspekte für Batch-Konten](#considerations-for-batch-accounts).

5. Klicken Sie auf **Erstellen**, um das Konto zu erstellen.

  Im Portal wird angezeigt, dass das Konto bereitgestellt wird (**Bereitstellen**), und nach Abschluss des Vorgangs wird das Blatt für das Batch-Konto geöffnet.

## Anzeigen der Eigenschaften des Batch-Kontos

Auf dem Blatt für das Batch-Konto werden mehrere Eigenschaften für das Konto angezeigt, und Sie haben Zugriff auf weitere Einstellungen, z.B. Zugriffsschlüssel, Kontingente, Benutzer und die Speicherkontozuordnung.

* **Batch-Konto-URL:** Mit dieser URL haben Sie Zugriff auf Ihr Batch-Konto, wenn Sie APIs wie die [Batch-REST][api_rest]-API oder die [Batch .NET][api_net]-Clientbibliothek verwenden. Es wird das folgende Format verwendet:

  `https://<account_name>.<region>.batch.azure.com`

* **Zugriffsschlüssel:** Klicken Sie zum Anzeigen und Verwalten der Zugriffsschlüssel Ihres Batch-Kontos auf das Schlüsselsymbol, um das Blatt **Schlüssel verwalten** zu öffnen, oder klicken Sie auf **Alle Einstellungen** > **Schlüssel**. Ein Zugriffsschlüssel wird für die Kommunikation mit den Batch-Dienst-APIs benötigt, z.B. [Batch-REST][api_rest] oder die [Batch .NET][api_net]-Clientbibliothek.

 ![Batch-Kontoschlüssel][account_keys]

* **Alle Einstellungen:** Klicken Sie zum Verwalten aller Einstellungen für das Batch-Konto oder zum Anzeigen seiner Eigenschaften auf **Alle Einstellungen**, um das Blatt **Einstellungen** zu öffnen. Dieses Blatt ermöglicht den Zugriff auf alle Einstellungen und Eigenschaften für das Konto, z.B. das Anzeigen der Kontokontingente, Auswählen eines Azure Storage-Kontos für die Verknüpfung mit dem Batch-Konto und das Verwalten von Benutzern.

 ![Batch-Konto, Blätter mit Einstellungen und Eigenschaften][5]

## Zu berücksichtigende Aspekte für Batch-Konten

* Sie können Batch-Konten auch mit den [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md) und der [Batch Management .NET-Bibliothek](batch-management-dotnet.md) erstellen und verwalten.

* Für das eigentliche Batch-Konto werden Ihnen keine Kosten berechnet. Kosten fallen für alle Azure-Computeressourcen an, die von Ihren Batch-Lösungen genutzt werden, sowie für die Ressourcen, die bei der Ausführung Ihrer Workloads von anderen Diensten genutzt werden. Beispielsweise werden Ihnen die Computeknoten in Ihren Pools berechnet. Wenn Sie das Feature [Anwendungspakete](batch-application-packages.md) verwenden, fallen Kosten für die Azure Storage-Ressourcen an, die zum Speichern der Anwendungspaketversionen eingesetzt werden. Weitere Informationen finden Sie unter [Batch – Preise][batch_pricing].

* Sie können mehrere Batch-Workloads in einem Batch-Konto ausführen oder die Workloads auf Batch-Konten in verschiedenen Azure-Regionen aufteilen.

* Wenn Sie mehrere umfangreiche Batch-Workloads ausführen, sollten Sie bestimmte [Kontingente und Limits für den Batch-Dienst](batch-quota-limit.md) beachten, die für Ihr Azure-Abonnement und jedes Batch-Konto gelten. Die aktuell gültigen Kontingente für ein Batch-Konto finden Sie im Portal unter den Kontoeigenschaften.

* Wenn Sie Ihrem Batch-Konto ein Speicherkonto zuordnen, sollten Sie beim Neugenerieren der Zugriffsschlüssel für das Speicherkonto mit Bedacht vorgehen. Sie sollten nur einen einzelnen Speicherkontoschlüssel neu generieren. Klicken Sie auf dem Blatt mit dem verknüpften Speicherkonto auf **Synchronisierungsschlüssel**, warten Sie fünf Minuten, bis die Schlüssel auf die Computeknoten in Ihren Pools verteilt wurden, und führen Sie bei Bedarf dann die Neugenerierung und Synchronisierung des anderen Schlüssels durch. Wenn Sie beide Schlüssel gleichzeitig generieren, können die Computeknoten keinen Schlüssel synchronisieren, und der Zugriff auf das Speicherkonto geht verloren.

  ![Speicherkontoschlüssel erneut generieren][4]

## Nächste Schritte

* Weitere Informationen zu den Konzepten und Features des Batch-Diensts finden Sie unter [Übersicht über Azure Batch-Features](batch-api-basics.md). In diesem Artikel werden die primären Batch-Ressourcen beschrieben, z.B. Pools, Computeknoten, Aufträge und Aufgaben. Außerdem enthält er eine Übersicht über die Features des Diensts, mit denen Sie Computeworkloads in großem Umfang ausführen können.

* Informieren Sie sich über die Grundlagen der Entwicklung einer Batch-fähigen Anwendung mit der [Batch .NET-Clientbibliothek](batch-dotnet-get-started.md). Im [Einführungsartikel](batch-dotnet-get-started.md) werden Sie durch eine funktionierende Anwendung geführt, bei der der Batch-Dienst zum Ausführen einer Workload auf mehreren Knoten verwendet wird, und es geht um die Verwendung von Azure Storage für die Dateibereitstellung und -wiederherstellung von Workloads.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Batch-Dienst, Blatt „Erstellen“ im Azure-Portal"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "Speicherkontoschlüssel erneut generieren"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Batch-Konto, Blätter mit Einstellungen und Eigenschaften"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0323_2016-->
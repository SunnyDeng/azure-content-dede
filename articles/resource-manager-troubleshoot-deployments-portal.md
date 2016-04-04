<properties
   pageTitle="Problembehandlung bei der Bereitstellung mithilfe des Azure-Portals | Microsoft Azure"
   description="Beschreibt, wie Sie mithilfe des Azure-Portals Probleme in der Resource Manager-Bereitstellung erkennen und beheben können."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Problembehandlung beim Bereitstellen von Ressourcengruppen mit dem Azure-Portal

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure-Befehlszeilenschnittstelle](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Wenn Sie einen Fehler beim Bereitstellen von Ressourcen in Azure erhalten haben, müssen Sie die aufgetretenen Fehler beheben. Das Azure-Portal bietet eine Schnittstelle, mit der Sie die Fehler leicht finden und potenzielle Korrekturen ermitteln können.

Sie können die Fehler in Ihrer Bereitstellung anhand der Überwachungsprotokolle oder der Bereitstellungsvorgänge beheben. In diesem Thema werden beide Methoden veranschaulicht.

Einige Fehler lassen sich vermeiden, indem Sie Ihre Vorlage und die Infrastruktur vor der Bereitstellung überprüfen. Weitere Informationen finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

## Verwenden von Überwachungsprotokollen zur Problembehandlung

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Um Fehler für eine Bereitstellung anzuzeigen, gehen Sie folgendermaßen vor:

1. Zeigen Sie Überwachungsprotokolle über das Portal an, indem Sie **Durchsuchen** und **Überwachungsprotokolle** auswählen.

    ![Überwachungsprotokolle auswählen](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Auf dem Blatt **Überwachungsprotokolle** wird eine Zusammenfassung der aktuellen Vorgänge für alle Ressourcengruppen in Ihrem Abonnement angezeigt. Dazu gehören eine grafische Darstellung der Zeit und des Status der Vorgänge sowie eine Liste der Vorgänge.

    ![Aktionen anzeigen](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Sie können beliebige Vorgänge in der Liste auswählen. Wählen Sie den Vorgang mit dem Fehler aus, den Sie untersuchen möchten.

    ![Vorgang auswählen](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. Alle Ereignisse für diesen Vorgang werden angezeigt. Beachten Sie die **Korrelations-IDs** in der Zusammenfassung. Anhand dieser ID werden verwandte Ereignisse überwacht. Sie kann hilfreich sein, wenn Sie bei der Problembehandlung mit dem technischen Support zusammenarbeiten. Sie können ein beliebiges Ereignis auswählen, um Details zu dem Ereignis anzuzeigen.

    ![Ereignis auswählen](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. Die Details zum Ereignis werden angezeigt. Achten Sie insbesondere auf die **Eigenschaften**, um Informationen über den Fehler zu erhalten.

    ![Details für Überwachungsprotokoll anzeigen](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Sie können die Ansicht der Überwachungsprotokolle filtern, um sich auf bestimmte Bedingungen zu konzentrieren. So passen Sie die Ansicht des Überwachungsprotokolls an:

1. Wählen Sie oben auf dem Blatt **Überwachungsprotokolle** die Option **Filtern**.

    ![Protokolle filtern](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. Wählen Sie auf dem Blatt **Filter** Bedingungen aus, um die Ansicht der Überwachungsprotokolle auf die Vorgänge zu beschränken, die Sie anzeigen möchten. Beispielsweise können Sie Vorgänge so filtern, dass nur Fehler für eine bestimmte Ressourcengruppe angezeigt werden.

    ![Filteroptionen festlegen](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. Durch Festlegen eines Zeitraums können Sie die Vorgänge weiter filtern. In der folgenden Abbildung wird die Ansicht auf einen bestimmten 20-minütigen Zeitraum gefiltert.

    ![Zeit festlegen](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

Nachdem Sie die Ansicht der Überwachungsprotokolle aktualisiert haben, werden nur die Vorgänge angezeigt, die die angegebene Bedingung erfüllen. Diese Einstellungen werden bis zur nächsten Anzeige der Überwachungsprotokolle beibehalten, daher müssen Sie die Werte möglicherweise ändern, um die Ansicht der Vorgänge wieder zu erweitern.

Hoffentlich konnten Sie herauszufinden, warum der Fehler bei der Bereitstellung aufgetreten ist. Sie können sich auch die Bereitstellungsvorgänge ansehen, um Informationen über den Status zu erhalten. Dies wird im nächsten Abschnitt gezeigt.

## Verwenden von Bereitstellungsvorgängen zur Problembehandlung

Um die Bereitstellungsvorgänge anzuzeigen, gehen Sie folgendermaßen vor:

1. Beachten Sie den Status der letzten Bereitstellung für die an der Bereitstellung beteiligte Ressourcengruppe. Sie können diesen Status auswählen, um weitere Details anzuzeigen.

    ![Bereitstellungsstatus](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Der Verlauf mit den letzten Bereitstellungen wird angezeigt. Wählen Sie die fehlerhafte Bereitstellung aus.

    ![Bereitstellungsstatus](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Sehen Sie sich die Informationen zur Bereitstellung an, und wählen Sie den fehlerhaften Vorgang aus, um Einzelheiten zum Fehler anzuzeigen.

    ![Bereitstellungsfehler anzeigen](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. Auf dem Blatt **Vorgangsdetails** sehen Sie Informationen zum fehlerhaften Vorgang. Achten Sie besonders auf die Statusmeldung.

    ![Statusmeldung anzeigen](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## Nächste Schritte

- Informationen zur Überwachung anderer Arten von Aktionen anhand der Überwachungsprotokolle finden Sie unter [Überwachen von Vorgängen mit Resource Manager](resource-group-audit.md).
- Informationen zum Überprüfen der Bereitstellung vor der Ausführung finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->
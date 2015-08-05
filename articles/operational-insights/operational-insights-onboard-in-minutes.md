<properties
    pageTitle="Minutenschnelles Onboarding auf Operational Insights"
    description="Mehr Erfahren über die Einrichtung von Operational Insights in wenigen Minuten"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/09/2015"
    ms.author="banders"/>

# Minutenschnelles Onboarding auf Operational Insights


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights in nur wenigen Minuten betriebsbereit machen. Bei der Auswahl eines Operational Insights-Arbeitsbereichs, der einem Konto ähnelt, haben Sie zwei Möglichkeiten:

- Microsoft Operations Management Suite
- Microsoft Azure-Abonnement

Sie können einen Microsoft Operations Management Suite-Arbeitsbereich mithilfe der Microsoft Operations Management Suite-Website erstellen. Oder Sie können ein Microsoft Azure-Abonnement nutzen, um einen Operational Insights-Arbeitsbereich zu erstellen. Derzeit sind beide Arbeitsbereiche funktionell gleichwertig. Der einzige Unterschied zwischen den beiden ist der Name. Wenn Sie ein Azure-Abonnement nutzen, können Sie mit diesem Abonnement auch auf andere Azure-Dienste zugreifen. Unabhängig von der Methode, die Sie zum Erstellen des Arbeitsbereichs wählen, erstellen Sie den Arbeitsbereich entweder mit einem Microsoft-Konto oder einem Unternehmenskonto.

## Anmelden in drei Schritten über die Operations Management Suite

1. Besuchen Sie die [Microsoft Operations Management Suite](http://microsoft.com/oms)-Website, und klicken Sie auf **Kostenlos testen**. Melden Sie sich zur Verwendung mit Office 365 oder anderen Microsoft-Diensten mit Ihrem Microsoft-Konto, z. B. Outlook.com, oder mit einem vom Unternehmen oder der Bildungseinrichtung bereitgestellten Organisationskonto an.
2. Geben Sie einen eindeutigen Namen für den Arbeitsbereich ein. Ein Arbeitsbereich ist ein logischer Container, in dem die Verwaltungsdaten gespeichert werden. Er bietet eine Möglichkeit zum Partitionieren von Daten zwischen verschiedenen Teams in Ihrer Organisation, während die Daten für den Arbeitsbereich exklusiv sind. Geben Sie eine E-Mail-Adresse und die Region an, wo Ihre Daten gespeichert werden sollen. ![Erstellen eines Arbeitsbereichs und Verknüpfen von Abonnements](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. Anschließend können Sie ein neues Azure-Abonnement oder eine Verknüpfung zu einem vorhandenen Azure-Abonnement erstellen. Wenn Sie mit der kostenlosen Testversion fortfahren wollen, klicken Sie auf **Nicht jetzt**.

Sie sind nun bereit, im Operations Management Suite-Portal loszulegen.

Erfahren Sie mehr über das Einrichten des Arbeitsbereichs und das Verknüpfen von vorhandenen Azure-Konten mit erstellten Arbeitsbereichen in der Operations Management Suite unter [Einrichten Ihres Arbeitsbereichs und Verwalten von Einstellungen](operational-insights-setup-workspace.md).

## Schnelles Registrieren mit Microsoft Azure

1. Wechseln Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com), melden Sie sich an, und wählen Sie dann in der Liste der Dienste **Operational Insights** aus.![Azure-Portal](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. Klicken Sie auf **Erstellen eines Arbeitsbereichs**, anschließend auf **Schnellerfassung**, und geben Sie dann unter „Konto“ einen Namen für den Arbeitsbereich ein, wählen Sie eine Preisstufe, und dann einen Speicherort für die Arbeitsbereichsdaten. Wenn Sie über mehrere Abonnements verfügen, können Sie das gewünschte auswählen, und klicken dann auf **Arbeitsbereich erstellen**. ![Azure-Portal](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. Wählen Sie den erstellten Arbeitsbereich, und klicken Sie dann auf **Besuchen Sie Ihr Operational Insights-Konto**, um die Operations Management Suite-Website zu öffnen. ![Besuchen des Kontos](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. Geben Sie auf der Operations Management Suite-Website Ihre E-Mail-Adresse ein und klicken Sie auf **Bestätigen und fortfahren**. Eine Bestätigungs-E-Mail wird an Sie gesendet. Öffnen Sie die E-Mail, und klicken Sie darin auf **Jetzt bestätigen**.
5. Die Operations Management Suite-Website zeigt die Übersichtsseite an. Klicken Sie auf **Erste Schritte**, um den Vorgang fortzusetzen.

Sie sind nun bereit, im Operations Management Suite-Portal loszulegen.

Erfahren Sie mehr über das Einrichten des Arbeitsbereichs und das Verknüpfen von vorhandenen Arbeitsbereichen, die Sie mit der Operations Management Suite zu Azure-Abonnements erstellt haben, unter [Einrichten Ihres Arbeitsbereichs und Verwalten von Einstellungen](operational-insights-setup-workspace.md).

## Erste Schritte mit dem Operations Management Suite-Portal
Um Lösungen zu wählen und die Server zu verbinden, die Sie verwalten möchten, klicken Sie auf die Kachel **Erste Schritte**, und führen Sie die folgenden Schritte aus:

![Erstellen eines Arbeitsbereichs und Verknüpfen von Abonnements](./media/operational-insights-onboard-in-minutes/get-started.png)

- Wählen Sie die Lösungen, die Sie nutzen möchten, und klicken Sie auf **Hinzufügen ausgewählter Lösungen**. ![Lösungen](./media/operational-insights-onboard-in-minutes/solutions.png)
- Wählen Sie die Vorgehensweise aus, um eine Verbindung zur Serverumgebung zum Sammeln von Daten herzustellen:
    - Verbinden Sie alle Windows-Server oder -Clients direkt durch Installieren eines Agents.
    - Verwenden Sie System Center Operations Manager, um die Verwaltungsgruppen oder die gesamte Operations Manager-Bereitstellung anzufügen.
    - Verwenden Sie ein Azure-Speicherkonto, das mit der Erweiterung der virtuellen Windows- oder Linux Azure-Maschine zur Diagnose konfiguriert ist.
- Konfigurieren Sie mindestens ein Protokoll, um Ihre Daten zu füllen. Wählen Sie die IIS-Protokolle und/oder Ereignisprotokolle aus, und anschließend **Speichern** am unteren Rand der Seite. Bei Ereignisprotokollen können Sie den Typ der Meldungen angeben, einschließlich Fehler-, Warn- und Informationsmeldungen zur Überwachung. ![Lösungen](./media/operational-insights-onboard-in-minutes/logs.png)

## Optional können Sie Server durch Installieren eines Agents direkt an die Operations Management Suite anschließen.
1. Klicken Sie in der Ansicht „Erste Schritte“ auf den Knoten **Eine Datenquelle verbinden**, und klicken Sie dann auf **Windows-Agent herunterladen**. Sie können den Agent nur unter Windows Server 2008 SP1 oder höher, oder unter Windows 7 SP1 oder höher installieren. Server müssen eine x64-Architektur besitzen.
2. Installieren Sie den Agent auf einem oder mehreren Servern. Sie können Agents nacheinander installieren oder mithilfe einer automatisierteren Methode mit einem [benutzerdefinierten Skript](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional), oder Sie nutzen eine vorhandene Softwarelösung, die Sie möglicherweise bereits verwenden.
3. Nachdem Sie dem Lizenzvertrag zugestimmt haben und Ihren Installationsordner auswählen, wählen Sie **Agent mit Microsoft Azure Operational Insights verbinden**. ![Einrichtung des Agents](./media/operational-insights-onboard-in-minutes/agent.png)
4. Auf der nächsten Seite müssen Sie Ihre Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel eingeben. Ihr Arbeitsbereichs-ID und der Schlüssel werden auf dem Bildschirm angezeigt, von dem Sie die Agent-Datei heruntergeladen haben.![Anfügen von Servern](./media/operational-insights-onboard-in-minutes/key.png)
5. Während der Installation können Sie auf **Erweitert** klicken, um optional Ihren Proxy-Server einzurichten und Authentifizierungsinformationen bereitzustellen. Klicken Sie auf die Schaltfläche **Weiter**, um zum Informationsbildschirm des Arbeitsbereichs zurückzukehren.
6. Klicken Sie auf **Weiter**, um Ihre Arbeitsbereichs-ID und den Schlüssel zu überprüfen. Wenn Fehler gefunden werden, können Sie auf **Zurück** klicken, um Korrekturen vornehmen. Wenn Ihr Arbeitsbereich-ID und der Schlüssel erfolgreich überprüft wurden, klicken Sie zum Abschließen der Installation des Agents auf **Installieren**.
7. Melden Sie sich wieder im Operations Management Suite-Portal an, und klicken Sie auf die Kachel **Einstellungen** auf der Übersichtsseite. Ein grünes Häkchen wird angezeigt, wenn die Agents mit dem Operations Management Suite-Dienst kommunizieren. Zunächst wird dies ungefähr 5 bis 10 Minuten dauern.

> [AZURE.NOTE]Kapazitätsverwaltungs- und Konfigurationsbewertungs-Lösungen werden von Servern derzeit nicht unterstützt, die direkt mit der Operations Management Suite verbunden sind.

Sie können auch den Agent mit System Center Operations Manager 2012 SP1 und höher verbinden. Zu diesem Zweck wählen Sie **Verbinden des Agents mit System Center Operations Manager**. Wenn Sie diese Option auswählen, senden Sie Daten an den Dienst, ohne dass zusätzliche Hardware oder Last für Ihre Verwaltungsgruppen erforderlich ist.

Weitere Informationen zum direkten Verbinden von Agents mit der Operations Management Suite finden Sie unter [Direktes Verbinden von Computern mit Operational Insightst](operational-insights-direct-agent.md).

## Optional können Sie Server mithilfe von System Center Operations Manager verbinden.

1. Wählen Sie in der Operations Manager-Konsole **Verwaltung**.
2. Erweitern Sie den Knoten **Operational Insights**, und wählen Sie dann **Operational Insights-Verbindung**.
3. Klicken Sie auf den Link **Registrieren bei Operational Insights** rechts oben, und befolgen Sie die angezeigten Anweisungen.
4. Klicken Sie nach Abschluss des Registrierungs-Assistenten auf den Link **Computer/Gruppe hinzufügen**.
5. Im Dialogfeld **Computersuche** können Sie nach Computern oder Gruppen suchen, die von Operations Manager überwacht werden. Wählen Sie Computer oder Gruppen aus, die in Operational Insights aufgenommen werden sollen, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **OK**. Sie können überprüfen, ob der Operational Insights-Dienst Daten empfängt, indem Sie auf die Kachel **Nutzung** im Operations Management Suite-Portal gehen. Daten sollten in ungefähr 5 bis 10 Minuten angezeigt werden.

Weitere Informationen zum Verbinden von Operations Manager mit der Operations Management Suite erhalten Sie unter [Verbinden mit Operational Insights von System Center Operations Manager aus](operational-insights-connect-scom.md).

## Optional, können Sie Daten von Clouddiensten in Microsoft Azure analysieren.

Mit der Operations Management Suite können Sie schnell Ereignis- und IIS-Protokolle für Cloud-Dienste und virtuelle Computer durchsuchen, indem Sie die Diagnose für Azure Cloud-Dienste aktivieren. Weitere Erkenntnisse zu Ihren virtuellen Azure-Computern erhalten Sie auch durch Installieren von Microsoft Monitoring Agent. Weitere Informationen zum Konfigurieren der Azure-Umgebung zur Verwendung mit der Operations Management Suite erhalten Sie unter [Analysieren der Daten von Servern in Microsoft Azure](operational-insights-analyze-data-azure.md).


## Nächste Schritte
- Loslegen mit der Verwendung von [Lösungen](operational-insights-solutions.md)
- Vertraut machen mit der [Suche](operational-insights-search.md)
- Verwenden von [Dashboards](operational-insights-use-dashboards.md) zum Speichern und Anzeigen von benutzerdefinierten Suchvorgängen

<!---HONumber=July15_HO4-->
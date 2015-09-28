<properties
    pageTitle="Einrichten Ihres Arbeitsbereichs und Verwalten von Einstellungen"
    description="Erfahren Sie, wie Sie in Microsoft Azure Operational Insights Ihren Arbeitsbereich einrichten und Einstellungen verwalten."
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
    ms.date="09/10/2015"
    ms.author="banders"/>

# Einrichten Ihres Arbeitsbereichs und Verwalten von Einstellungen

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Zum Erstellen eines neuen Microsoft Azure Operational Insights-Arbeitsbereichs wählen Sie einen Arbeitsbereichsnamen, den Sie Ihrem Konto zuordnen, und einen geografischen Standort. Ein Operational Insights-Arbeitsbereich ist im Wesentlichen ein Container, der Kontoinformationen und einfache Konfigurationsinformationen für das Konto enthält. Sie oder andere Mitglieder Ihrer Organisation können mehrere Operational Insights-Arbeitsbereiche nutzen, um unterschiedliche Mengen von Daten zu verwalten, die in Ihrer gesamten IT-Infrastruktur oder Teilen davon erfasst werden.

Nachdem der Arbeitsbereich erstellt wurde, können Sie andere Aufgaben darin ausführen, z. B. Operational Insights verwalten, Lösungen hinzufügen, Datenquellen verbinden, Protokolle hinzufügen, Speicherkonten auswählen, Ihre Verwendungsdaten im Dashboard anzeigen und die Einstellungen für jeden Arbeitsbereich verwalten.

Der erste Teil des Artikels [Startbereit im Handumdrehen](./operational-insights-onboard-in-minutes.md) beschäftigt sich mit der Vorbereitung. Im weiteren Verlauf des Artikels werden einige der Aktionen ausführlicher beschrieben, die Sie zum Abschließen der ersten Schritte und zum Verwalten des Arbeitsbereichs ausführen müssen.

In den folgenden Abschnitten werden alle häufig verwendeten Aufgaben besprochen:

1. Hinzufügen von Lösungen
2. Verbinden von Datenquellen
3. Hinzufügen und Verwalten von Protokollen
4. Verwalten von Konten und Benutzern

![Schritte](./media/operational-insights-setup-workspace/steps.png)
## 1 Hinzufügen von Lösungen

Microsoft Azure Operational Insights enthält grundlegende Funktionen zur Konfigurationsbewertung. Sie müssen also keine Lösung installieren, um diese Funktionen zu ermöglichen. Allerdings können Sie zusätzliche Funktionen nutzen, indem Sie Lösungen über die Seite für Einstellungen oder Solution Gallery hinzufügen.

Nachdem Sie eine Lösung hinzugefügt haben, werden Daten von den Servern in Ihrer Infrastruktur erfasst und an den Operational Insights-Dienst gesendet. Die Verarbeitung durch den Operational Insights-Dienst kann wenige Minuten, jedoch auch mehrere Stunden dauern. Nach der Verarbeitung der Daten durch den Dienst können Sie diese in Operational Insights anzeigen.

Wenn eine Lösung nicht mehr benötigt wird, können Sie sie problemlos entfernen. Wenn Sie eine Lösung entfernen, werden ihre Daten nicht mehr an Operational Insights gesendet und die Datenmenge, die Ihr Tageskontingent belastet, verringert sich entsprechend.

### Von Microsoft Monitoring Agent unterstützte Lösungen

Zurzeit können Server, die über Microsoft Monitoring Agent direkt mit Microsoft Azure Operational Insights verbunden sind, die meisten verfügbaren Lösungen nutzen, einschließlich der folgenden:

- [System Updates](operational-insights-updates.md)
- [Antimalware](operational-insights-antimalware.md)
- [Change Tracking](operational-insights-change-tracking.md)
- [SQL and Active Directory Assessment](operational-insights-assessment.md)

Die folgenden Lösungen werden hingegen *nicht* mit Microsoft Monitoring Agent unterstützt, sondern erfordern System Center Operations Manager (SCOM).

- [Capacity Management](operational-insights-capacity.md)
- [Alert Management](operational-insights-alerts.md)
- [Configuration Assessment](operational-insights-solutions.md#configuration-assessment)

Unter [Überlegungen zu Operations Manager mit Operational Insights](operational-insights-operations-manager.md) finden Sie Anleitungen zur Verwendung dieser Lösungen mit Operations Manager.

Die Erfassung von IIS-Protokollen wird auf Computern mit folgenden Betriebssystemen unterstützt:

- Windows Server 2012
- Windows Server 2012 R2

### So fügen Sie Lösungen über die Seite „Einstellungen“ hinzu

- Wählen Sie die Lösungen aus, die Sie hinzufügen möchten, und klicken Sie auf **Hinzufügen ausgewählter Lösungen**. Hier werden nicht alle verfügbaren Lösungen angezeigt. Wenn Sie Lösungen hinzufügen möchten, die nicht aufgeführt sind, verwenden Sie das nächste Verfahren. ![Lösungen hinzufügen](./media/operational-insights-setup-workspace/settings-add-sol.png)

### So fügen Sie eine Lösung mit Solutions Gallery hinzu

1. Klicken Sie in Operational Insights auf der Übersichtsseite auf die Kachel **Solutions Gallery**. ![Bild des Symbols für Lösungen](./media/operational-insights-setup-workspace/sol-gallery.png)
2. Auf der Seite "Operational Insights Solutions Gallery" finden Sie Informationen zu jeder der verfügbaren Lösungen. Klicken Sie auf den Namen der Lösung, die Sie Operational Insights hinzufügen möchten.
3. Auf der Seite für die ausgewählte Lösung werden ausführliche Informationen zur Lösung angezeigt. Klicken Sie auf **Hinzufügen**.
4. Klicken Sie auf der Bestätigungsseite auf **Accept**, um den Datenschutzbestimmungen und Nutzungsbedingungen zuzustimmen.
5. Auf der Seite "Overview" in Operational Insights wird eine neue Kachel für die hinzugefügte Lösung angezeigt, und Sie können Sie verwenden, nachdem der Operational Insights-Dienst Ihre Daten verarbeitet hat.

### So entfernen Sie eine Lösung mit Solutions Gallery

1. Klicken Sie in Operational Insights auf der Seite "Overview" auf die Kachel **Solutions Gallery**.
2. Klicken Sie auf der Seite "Operational Insights Solutions Gallery" unterhalb der Lösung, die Sie entfernen möchten, und klicken Sie dann auf **Entfernen**.
3. Klicken Sie auf der Bestätigungsseite auf **Ja**, um die Lösung zu entfernen.

## 2 Verbinden von Datenquellen

Es gibt drei Möglichkeiten zum Verbinden von Datenquellen:

- Verbinden Sie Computer direkt mit Operational Insights. Weitere Informationen finden Sie unter [Direktes Verbinden von Computern mit Operational Insights](./operational-insights-direct-agent.md). ![direkt verbinden](./media/operational-insights-setup-workspace/attach-directly.png)
- Fügen Sie Operations Manager-Verwaltungsgruppen hinzu. Weitere Informationen finden Sie unter [Verbinden mit Operational Insights von System Center Operations Manager aus](./operational-insights-connect-scom.md). ![Operations Manager verbinden](./media/operational-insights-setup-workspace/attach-om.png)
- Fügen Sie ein Azure-Speicherkonto hinzu. Weitere Informationen finden Sie unter [Analysieren der Daten von Servern in Microsoft Azure](./operational-insights-analyze-data-azure.md). ![Azure verbinden](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 Hinzufügen und Verwalten von Protokollen

Bevor Sie Protokolle hinzufügen, müssen Sie eine Lösung installieren, die Protokolldaten nutzt. Anschließend können Sie neue Protokolle zum Sammeln von Ereignissen hinzufügen und auswählen, welche Ereignisebenen oder Schweregrade Sie für die Protokolle sammeln möchten. Sie können Folgendes sammeln:

- Windows-Ereignisprotokolle
- IIS-Protokolle
- Andere Protokolle, die Sie hinzugefügt haben

![Protokolle hinzufügen](./media/operational-insights-setup-workspace/collect-logs.png)

### IIS-Protokolldateiformat

Im Moment wird als IIS-Protokollformat nur W3C unterstützt. Dies ist jedoch das gängigste Format und wird standardmäßig in IIS 7 und IIS 8 verwendet. Wenn Sie also im systemeigenen Format von NCSA oder IIS protokollieren, übernimmt Operational Insights diese Protokolle nicht. Selbst im W3C-Format werden Sie feststellen, dass nicht alle Felder standardmäßig protokolliert werden. Weitere Informationen zum Format finden Sie unter [Auswählen von W3C-Feldern für die Protokollierung (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx)).


> [AZURE.TIP]Um die Protokollsuche optimal zu nutzen, empfiehlt es sich, für jede Website alle Protokollierungsfelder über die **Protokollierung** in IIS auszuwählen. Außerdem sollte der Zeitplan **Protokolldateirollover** für neue Protokolle in **Stündlich** geändert werden. Dadurch werden kleinere Dateien in die Cloud hochgeladen, was weniger Bandbreite in Anspruch nimmt.


### Sammeln von Windows-Ereignisprotokollen von Operations Manager oder direkt verbundenen Agents

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Einstellungen** und anschließend auf die Registerkarte **Protokolle**.
2. Geben Sie den Namen des Ereignisprotokolls ein, aus dem Sie Informationen erfassen möchten. Wenn Sie nicht sicher sind, welchen Namen Sie verwenden sollen, wählen Sie in der **Ereignisanzeige** die Eigenschaften des Windows-Ereignisprotokolls aus, kopieren Sie den Namen im Feld **Vollständiger Name**, und fügen Sie diesen in das Feld **Ereignisse aus folgenden Ereignisprotokollen erfassen** ein.
3. Klicken Sie auf **+**, um das Protokoll hinzuzufügen.
4. Wählen Sie die Ereignisebenen oder den Schweregrad aus, die bzw. den Sie für das Protokoll erfassen möchten. **Überwachungserfolg** und **Überwachungsfehler** werden in dieser Version nicht unterstützt.
5. Wiederholen Sie die vorherigen Schritte für jedes Protokoll, aus dem Sie Informationen sammeln möchten, und klicken Sie dann auf **Speichern**.
6. Ereignisse sollten in wenigen Minuten in Operational Insights angezeigt werden, und anschließend können Sie dann die Daten durchsuchen.

### So sammeln Sie IIS-Protokolle von Operations Manager oder direkt angeschlossenen Agents

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Einstellungen** und anschließend auf die Registerkarte **Protokolle**.
2. Klicken Sie auf der Registerkarte **Protokolle** unter **Ereignisprotokolle** auf **Protokolle aus Operations Manager erfassen**.


### Sammeln von IIS-Protokollen und/oder Windows-Ereignissen mit der Azure-Diagnose
Dies kann über das Azure-Verwaltungsportal (nicht über das Operational Insights-Portal) konfiguriert werden. Wechseln Sie in Ihrem Arbeitsbereich zur Registerkarte **Speicher**. Dort können Sie die Protokollerfassung von diesem Speicherkonto aktivieren.

### Nach dem Konfigurieren der Protokollerfassung
Nach dem Konfigurieren der Protokollerfassung wird Ihre Protokollerfassungsrichtlinie an Agents oder über Verwaltungsgruppen an Agents gesendet, und der Dienst beginnt mit dem Sammeln von Ereignissen.

Über die Seite **Verwendung** können Sie auf einige erste Aufschlüsselungen der von Ihren überwachten Servern gesammelten Protokollereignisse zugreifen.

![Abbildung der Kachel auf der Seite „Verwendung“](./media/operational-insights-setup-workspace/usage.png)


## 4 Verwalten von Konten und Benutzern
Sie verwalten Konten und Benutzer mithilfe der Registerkarte **Konten** auf der Einstellungsseite. Dort können Sie folgende Aufgaben ausführen.

![Registerkarte „Konten“](./media/operational-insights-setup-workspace/manage-users.png)

## Hinzufügen eines Benutzers zu einem vorhandenen Arbeitsbereich


Gehen Sie folgendermaßen vor, um einem Operational Insights-Arbeitsbereich einen Benutzer oder eine Gruppe hinzuzufügen. Der Benutzer oder die Gruppe kann alle Warnungen, die diesem Arbeitsbereich zugeordnet sind, anzeigen und darauf reagieren.

>[AZURE.NOTE]Wenn Sie einen Benutzer oder eine Gruppe aus Ihrem Azure Active Directory-Organisationskonto hinzufügen möchten, müssen Sie zunächst sicherstellen, dass Sie Ihr Operational Insights-Konto mit Ihrer Active Directory-Domäne verknüpft haben. Siehe [Hinzufügen einer Azure Active Directory-Organisation zu einem vorhandenen Arbeitsbereich](#).

### So fügen Sie einen Benutzer einem vorhandenen Arbeitsbereich hinzu
1. Klicken Sie in Operational Insights auf die Kachel **Einstellungen**.
2. Klicken Sie auf Registerkarte **Konten**.
3. Wählen Sie im Abschnitt **Benutzer verwalten** den hinzuzufügenden Kontotyp aus: **Unternehmenskonto** oder **Microsoft-Konto**.
    - Wenn Sie „Microsoft-Konto“ auswählen, geben Sie die E-Mail-Adresse des Benutzers ein, der dem Microsoft-Konto zugeordnet ist.
    - Wenn Sie „Unternehmenskonto“ auswählen, können Sie einen Teil des Benutzer- oder Gruppennamens bzw. des E-Mail-Alias eingeben. Anschließend wird eine Liste der Benutzer und Gruppen angezeigt. Wählen Sie einen Benutzer oder eine Gruppe aus.
        >[AZURE.NOTE]Zum Erzielen einer optimalen Leistung begrenzen Sie die Anzahl von Active Directory-Gruppen, die einem einzelnen Operational Insights-Konto zugeordnet sind, auf zwei - eines für Administratoren und eines für Benutzer. Mehr Gruppen können sich auf die Leistung von Operational Insights auswirken.
7. Wählen Sie den hinzuzufügenden Benutzer- oder Gruppentyp aus: **Administrator** oder **Benutzer**.  
8. Klicken Sie auf **Hinzufügen**.

  Wenn Sie ein Microsoft-Konto hinzufügen, wird eine Einladung zur Teilnahme am Arbeitsbereich an die angegebene E-Mail-Adresse gesendet. Nachdem der Benutzer die Anweisungen in der Einladung zur Teilnahme an Operational Insights befolgt hat, kann er die Warnungen und Kontoinformationen für dieses Operational Insights-Konto anzeigen, und Sie können die Benutzerinformationen auf der Seite **Einstellungen** auf der Registerkarte **Konten** anzeigen. Wenn Sie ein Organisationskonto hinzufügen, kann der Benutzer sofort auf Operational Insights zugreifen. ![Einladung](./media/operational-insights-setup-workspace/manage-users04.png)


### Wie viele Arbeitsbereiche benötige ich?
Ein Arbeitsbereich wird im Azure-Verwaltungsportal als Azure-Ressource angezeigt.

Sie können entweder einen neuen Arbeitsbereich erstellen oder eine Verknüpfung mit einem vorhandenen Arbeitsbereich herstellen, den Sie zuvor für die Verwendung mit System Center Operations Manager geöffnet, aber möglicherweise noch keinem Azure-Abonnement zugeordnet haben (was für die Abrechnung erforderlich ist). Ein Arbeitsbereich stellt die Ebene dar, auf der Daten erfasst, aggregiert, analysiert und im Operational Insights-Portal angezeigt werden. Sie können nach Wunsch mit mehreren Arbeitsbereichen arbeiten, um Daten aus verschiedenen Umgebungen und Systemen zu trennen. Jede Operations Manager-Verwaltungsgruppe (und alle ihre Agents) oder einzelne VMs/Agents können jeweils mit nur einem Arbeitsbereich verbunden werden.

Jedem Arbeitsbereich können mehrere Benutzerkonten zugeordnet sein, wobei jedes Benutzerkonto (Microsoft- oder Organisationskonto) Zugriff auf mehrere Operational Insights-Arbeitsbereiche hat. Standardmäßig wird der Besitzer des Microsoft- oder Organisationskontos, das zum Erstellen des Arbeitsbereichs verwendet wird, zum Administrator des Arbeitsbereichs. Der Administrator kann anschließend zusätzliche Microsoft-Konten einladen oder Benutzer in seinem Azure Active Directory auswählen.

## Verknüpfen eines vorhandenen Arbeitsbereichs mit einem Azure-Abonnement

Es ist möglich, unter [microsoft.com/oms](https://microsoft.com/oms) einen Arbeitsbereich zu erstellen. Es gelten jedoch bestimmte Grenzwerte für diese Arbeitsbereiche. Der wichtigste bei der Verwendung eines kostenlosen Kontos ist die Beschränkung auf 500 MB/Tag für das Hochladen von Daten. Um Änderungen für diesen Arbeitsbereich vorzunehmen, müssen Sie Ihren **vorhandenen Arbeitsbereich mit einem Azure-Abonnement verknüpfen**.

>[AZURE.IMPORTANT]Damit Sie einen Arbeitsbereich verknüpfen können, muss Ihr Azure-Konto bereits Zugriff auf den Arbeitsbereich haben, zu dem Sie eine Verknüpfung herstellen möchten. Anders ausgedrückt muss das Konto, das Sie für den Zugriff auf das Azure-Portal verwenden, **identisch** mit dem Konto sein, mit dem Sie auf Ihren Operational Insights-Arbeitsbereich zugreifen. Wenn dies nicht der Fall ist, finden Sie weitere Informationen unter [Hinzufügen eines Benutzers zu einem vorhandenen Arbeitsbereich](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Melden Sie sich beim Azure-Verwaltungsportal an.
2. Klicken Sie links unten im Portal auf **+ Neu**.
3. Klicken Sie auf **App-Dienste**, führen Sie einen Bildlauf zu **Operational Insights** durch, und wählen Sie diesen Eintrag aus.
4. Klicken Sie auf **Schnellerfassung**.
5. Auf der Liste **Konto** sollte eine Liste Ihrer vorhandenen Arbeitsbereiche enthalten sein, die *noch nicht* mit dem Azure-Abonnement verknüpft sind. Wählen Sie ein Konto aus.

  >[AZURE.NOTE]Wenn der Arbeitsbereich, den Sie verknüpfen möchten, hier nicht angezeigt wird, bedeutet dies, dass Ihr Azure-Abonnement keinen Zugriff auf Ihren Operational Insights-Arbeitsbereich hat. Sie müssen in Ihrem Operational Insights-Arbeitsbereich den Zugriff auf dieses Konto gewähren. Informationen hierzu finden Sie unter [Hinzufügen eines Benutzers zu einem vorhandenen Arbeitsbereich](#add-a-user-to-an-existing-workspace).

  ![Konto verknüpfen](./media/operational-insights-setup-workspace/link-account.png) <p> 6. Füllen Sie die verbleibenden Felder aus, und wählen Sie dann **Arbeitsbereich erstellen**.

## Aktualisieren des Arbeitsbereichs auf einen kostenpflichtigen Datenplan

Es gibt drei Arten von Arbeitsbereichsdatenplänen für Operational Insights: **Kostenlos**, **Standard** und **Premium**. Wenn Sie mit einem Plan vom Typ *Kostenlos* arbeiten, ist Ihre Datenübertragung auf 500 MB begrenzt. Sie müssen für den Arbeitsbereich die **nutzungsbasierte Bezahlung** aktivieren, um Daten über diesen Grenzwert hinaus erfassen zu können. Sie können Ihren Plantyp jederzeit ändern. Weitere Informationen zu den Preisen von Operational Insights finden Sie unter [Preisdetails](https://azure.microsoft.com/de-DE/pricing/details/operational-insights/).

>[AZURE.IMPORTANT]Pläne für Arbeitsbereiche können nur geändert werden, wenn sie mit einem Azure-Abonnement *verknüpft* sind. Wenn Sie den Arbeitsbereich in Azure erstellt haben oder *bereits* eine Verknüpfung mit dem Arbeitsbereich hergestellt haben, können Sie diese Meldung ignorieren. Wenn Sie den Arbeitsbereich unter [opinsights.azure.com](http://opinsights.azure.com) erstellt haben, müssen Sie die Schritte unter [Verknüpfen eines vorhandenen Arbeitsbereichs mit einem Azure-Abonnement](#link-an-existing-workspace-to-an-Azure-subscription) ausführen.

### Ändern des Plantyps

Navigieren Sie im Azure-Verwaltungsportal zum Operational Insights-Arbeitsbereich, auf den Sie aktualisieren möchten:

![Skalierung](./media/operational-insights-setup-workspace/find-workspace.png)

Wählen Sie diesen Arbeitsbereich und dann auf den Registerkarten am oberen Bildschirmrand **SKALIEREN** aus.

![Skalierung auswählen](./media/operational-insights-setup-workspace/select-scale.png)

Wählen Sie abschließend den Plan aus, auf den Sie aktualisieren möchten, und klicken Sie auf **SPEICHERN**. Die Änderungen werden im Portal übernommen, und Sie sind jetzt in der Lage, Daten über die Begrenzung des Tarifs "Kostenlos" hinaus zu erfassen.

![Plan auswählen](./media/operational-insights-setup-workspace/plan-select.png)

## Hinzufügen einer Azure Active Directory-Organisation zu einem vorhandenen Arbeitsbereich

Sie können Ihren Operational Insights-Arbeitsbereich einer Azure Active Directory-Domäne zuordnen. Dies ermöglicht Ihnen, Benutzer aus Active Directory direkt Ihrem Operational Insights-Arbeitsbereich hinzuzufügen, ohne dass ein separates Microsoft-Konto erforderlich ist.

### So fügen Sie eine Azure Active Directory-Organisation einem vorhandenen Arbeitsbereich hinzu

1. Klicken Sie auf der Seite „Einstellungen“ in Operational Insights auf **Konten** und anschließend auf **Organisation hinzufügen**. ![Einladung](./media/operational-insights-setup-workspace/add-org.png)
2. Überprüfen Sie die Informationen zu Organisationskonten, und klicken Sie dann auf **Weiter**.
3. Geben Sie die Identitätsinformationen des Administrators Ihrer Azure Active Directory-Domäne ein, und klicken Sie dann auf **Anmelden**.
4. Klicken Sie auf **Zugriff gewähren**, damit Operational Insights die Identitätsinformationen in Ihrer Active Directory-Domäne verwenden kann. ![verknüpft](./media/operational-insights-setup-workspace/ad-existing01.png)


## Bearbeiten eines vorhandenen Benutzertyps

Sie können die Kontorolle eines Benutzers ändern, der Ihrem Operational Insights-Konto zugeordnet ist. Sie haben die folgenden Rollenoptionen:

 - *Administrator*: Kann Benutzer verwalten, alle Warnungen anzeigen und darauf reagieren sowie Server hinzufügen und entfernen

 - *Benutzer*: Kann alle Warnungen anzeigen und darauf reagieren sowie Server hinzufügen und entfernen

### So bearbeiten Sie ein Konto
1. Wählen Sie in Operational Insights auf der Seite **Einstellungen** auf der Registerkarte **Konten** die Rolle für den Benutzer aus, die Sie ändern möchten.
2. Klicken Sie auf **OK**.

## Entfernen eines Benutzers aus einem Operational Insights-Arbeitsbereich

Gehen Sie folgendermaßen vor, um einen Benutzer aus einem Operational Insights-Arbeitsbereich zu entfernen. Beachten Sie, dass dadurch der Arbeitsbereich des Benutzers nicht geschlossen wird. Stattdessen wird die Zuordnung zwischen diesem Benutzer und dem Arbeitsbereich aufgehoben. Wenn ein Benutzer mehreren Arbeitsbereichen zugeordnet ist, kann sich dieser Benutzer weiter bei Operational Insights anmelden.

### So entfernen Sie einen Benutzer aus einem Arbeitsbereich

1. Klicken Sie in Operational Insights auf der Seite **Einstellungen** auf der Registerkarte **Konten** neben dem Benutzernamen, den Sie entfernen möchten, auf „Entfernen“.
2. Klicken Sie auf **OK**, um zu bestätigen, dass Sie den Benutzer entfernen möchten.

## Schließen Ihres Operational Insights-Arbeitsbereichs

Wenn Sie einen Operational Insights-Arbeitsbereich schließen, werden alle Daten im Zusammenhang mit Ihrem Arbeitsbereich 30 Tage nach Schließen des Arbeitsbereichs aus dem Operational Insights-Dienst gelöscht.

Wenn Sie Administrator sind und mehrere Benutzer mit dem Arbeitsbereich verknüpft sind, wird die Zuordnung zwischen den Benutzern und dem Arbeitsbereich aufgehoben. Wenn die Benutzer anderen Arbeitsbereichen zugeordnet sind, können sie Operational Insights mit diesen Arbeitsbereichen weiter nutzen. Wenn sie jedoch keinen anderen Arbeitsbereichen zugeordnet sind, müssen sie einen neuen Arbeitsbereich erstellen, um Operational Insights verwenden zu können.

### So schließen Sie einen Operational Insights-Arbeitsbereich

1. Klicken Sie in Operational Insights auf der Seite **Einstellungen** auf der Registerkarte **Konten** auf **Arbeitsbereich schließen**.

2. Wählen Sie einen der Gründe für das Schließen des Arbeitsbereichs aus, oder geben Sie einen anderen Grund in das Textfeld ein.

3. Klicken Sie auf **Arbeitsbereich schließen**.

## Zusätzliche Ressourcen
- [Anforderungen des IIS-Protokollformats in Azure Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Sehen Sie sich im [Feedback-Forum](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy) an, welche weiteren Datenquellen und Protokolltypen die Community implementiert haben möchte.

<!---HONumber=Sept15_HO3-->
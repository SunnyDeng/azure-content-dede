<properties
 pageTitle="Erste Schritte mit Azure Scheduler im Azure-Portal | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="02/17/2016"
 ms.author="krisragh"/>

# Erste Schritte mit Azure Scheduler im Azure-Portal

Das Erstellen geplanter Aufträge in Azure Scheduler ist einfach. In diesem Tutorial erfahren Sie, wie ein Auftrag erstellt wird. Außerdem erhalten Sie Informationen zu den Überwachungs- und Verwaltungsfunktionen von Scheduler.

## Erstellen eines Auftrags

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.  

2.  Klicken Sie auf **+ Neu** > geben Sie im Suchfeld _Scheduler_ ein > wählen Sie in den Ergebnissen **Scheduler** > klicken Sie auf **Erstellen**.

   ![][marketplace-create]

3.  In diesem Beispiel erstellen wir einen einfachen Auftrag mit einer GET-Anforderung für „http://www.microsoft.com/“. Geben Sie auf dem Bildschirm **Scheduler-Auftrag** die folgenden Informationen ein:

    1.  **Name:** `getmicrosoft`  

    2.  **Abonnement:** Ihr Azure-Abonnement

    3.  **Auftragssammlung:** Wählen Sie eine vorhandene Auftragssammlung aus, oder klicken Sie auf **Neu erstellen** > geben Sie einen Namen ein.

4.  Definieren Sie dann unter **Aktionseinstellungen** die folgenden Werte:

    1.  **Aktionstyp:** ` HTTP`  

    2.  **Methode:** `GET`

    3.  **URL:** ` http://www.microsoft.com`

   ![][action-settings]

5.  Jetzt definieren wir einen Zeitplan. Der Auftrag kann natürlich auch als einmaliger Auftrag definiert werden, in diesem Beispiel verwenden wir aber einen Wiederholungszeitplan.

    1. **Wiederholung**: `Recurring`

    2. **Start**: Das heutige Datum

    3. **Wiederholen alle:** `12 Hours`

    4. **Endet am**: Zwei Tage nach dem heutigen Datum

   ![][recurrence-schedule]

6.  Klicken Sie auf **Erstellen**.

## Verwalten und Überwachen Aufträgen

Sobald ein Auftrag erstellt wurde, wird er im Azure-Hauptdashboard angezeigt. Wenn Sie auf den Auftrag klicken, wird ein neues Fenster mit folgenden Registerkarten angezeigt:

1.  Eigenschaften  

2.  Aktionseinstellungen

3.  Zeitplan

4.  Verlauf

5.  Benutzer

   ![][job-overview]

### Eigenschaften

Diese schreibgeschützte Eigenschaften beschreiben die Metadaten für den Scheduler-Auftrag.

   ![][job-properties]


### Aktionseinstellungen

Wenn Sie auf dem Bildschirm **Aufträge** auf einen Auftrag klicken, können Sie diesen konfigurieren. Hier können Sie erweiterte Einstellungen konfigurieren, wenn Sie diese nicht bereits im Schnellerstellungs-Assistenten konfiguriert haben.

Sie können die Wiederholungsrichtlinie und die Fehleraktion für alle Aktionstypen ändern.

Bei HTTP- und HTTPS Auftragsaktionstypen können Sie für die Methode ein beliebiges zulässiges HTTP-Verb festlegen. Darüber hinaus können Sie die Header sowie grundlegende Authentifizierungsinformationen hinzufügen, löschen oder ändern.

Bei Speicherwarteschlangen-Aktionstypen können Sie das Speicherkonto, den Warteschlangennamen, das SAS-Token und den Text ändern.

Für Service Bus-Aktionstypen können Sie den Namespace, den Thema-/Warteschlangenpfad, die Authentifizierungseinstellungen, den Transporttyp, die Nachrichteneigenschaften und den Nachrichtentext ändern.

   ![][job-action-settings]

### Zeitplan

Damit können Sie den Zeitplan neu konfigurieren, wenn Sie den im Schnellerstellungs-Assistenten erstellten Zeitplan ändern möchten.

Dies ist eine Möglichkeit zum Erstellen [komplexer Zeitpläne und erweiterter Serien in Ihrem Auftrag](scheduler-advanced-complexity.md).

Sie können die Startzeit (Datum und Uhrzeit), den Wiederholungszeitplan und die Endzeit (Datum und Uhrzeit) ändern (sofern es sich um einen wiederkehrenden Auftrag handelt).

   ![][job-schedule]


### Verlauf

Die Registerkarte **Verlauf** zeigt ausgewählte Metriken für jede Auftragsausführung im System für den ausgewählten Auftrag an. Diese Metriken liefern Echtzeitwerte zum Status von Scheduler:

1.  Status  

2.  Details

3.  Wiederholungsversuche

4.  Vorkommen: 1., 2., 3. usw.

5.  Startzeit der Ausführung

6.  Endzeit der Ausführung

   ![][job-history]

Klicken Sie auf eine Ausführung, um die **Verlaufsdetails** anzuzeigen, einschließlich der vollständigen Antwort für jede Ausführung. In diesem Dialogfeld können Sie auch die Antwort in die Zwischenablage kopieren.

   ![][job-history-details]

### Benutzer

Die rollenbasierte Access Control in Azure (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure Scheduler. Informationen zur Verwendung der Registerkarte „Benutzer“ finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](../active-directory/role-based-access-control-configure.md)


## Weitere Informationen

 [Was ist Azure Scheduler?](scheduler-intro.md)

 [Scheduler-Konzepte, -Terminologie und -Entitätshierarchie](scheduler-concepts-terms.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler](scheduler-advanced-complexity.md)

 [Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/mt629143)

 [Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Scheduler – hohe Verfügbarkeit und Zuverlässigkeit](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Scheduler](scheduler-limits-defaults-errors.md)

 [Ausgehende Authentifizierung von Scheduler](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_0218_2016-->
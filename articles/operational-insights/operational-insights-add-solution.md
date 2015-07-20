<properties
    pageTitle="Operational Insights-Lösungen"
    description="Mit Lösungen können Sie Operational Insights um zusätzliche Funktionen erweitern."
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
    ms.date="07/02/2015"
    ms.author="banders"/>

# Operational Insights-Lösungen

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights enthält das grundlegende Modul zur Konfigurationsbewertung. Allerdings können Sie zusätzliche Funktionen erhalten, indem Sie Lösungen über die Seite "Overview" hinzufügen.

![Bild des Symbols für Lösungen](./media/operational-insights-add-solution/sol-gallery.png)

Nachdem Sie eine Lösung hinzugefügt haben, werden Daten von den Servern in Ihrer Infrastruktur erfasst und an den Operational Insights-Dienst gesendet. Die Verarbeitung durch den Operational Insights-Dienst kann wenige Minuten, jedoch auch mehrere Stunden dauern. Nach der Verarbeitung der Daten durch den Dienst können Sie diese in Operational Insights anzeigen.

Wenn eine Lösung nicht mehr benötigt wird, können Sie sie problemlos entfernen. Wenn Sie eine Lösung entfernen, werden ihre Daten nicht mehr an Operational Insights gesendet und die Datenmenge, die Ihr Tageskontingent belastet, verringert sich entsprechend.

## Von Microsoft Monitoring Agent unterstützte Lösungen

Zurzeit können Server, die über Microsoft Monitoring Agent direkt mit Microsoft Azure Operational Insights verbunden sind, die meisten verfügbaren Lösungen nutzen, einschließlich der folgenden:

- [System Updates](operational-insights-updates.md)

- [Log Management](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Change Tracking](operational-insights-change-tracking.md)

- [SQL and Active Directory Assessment](operational-insights-assessment.md)

Die folgenden Lösungen werden hingegen *nicht* mit Microsoft Monitoring Agent unterstützt und erfordern System Center Operation Manager (SCOM).

- [Capacity Management](operational-insights-capacity.md)

- [Alert Management](operational-insights-alerts.md)

- [Configuration Assessment](operational-insights-solutions.md#configuration-assessment)

Lesen Sie [Überlegungen zu Operations Manager mit Operational Insights](operational-insights-operations-manager.md) für Anleitungen zur Verwendung dieser Lösungen mit Operations Manager.

Die Erfassung von IIS-Protokollen wird auf Computern mit folgenden Betriebssystemen unterstützt:

- Windows Server 2012

- Windows Server 2012 R2

### Hinzufügen von Lösungen


1. Klicken Sie in Operational Insights auf der Seite "Overview" auf die Kachel **Solutions Gallery**.


2. Auf der Seite "Operational Insights Solutions Gallery" finden Sie Informationen zu jeder der verfügbaren Lösungen. Klicken Sie auf den Namen der Lösung, die Sie Operational Insights hinzufügen möchten.


3. Auf der Seite für die ausgewählte Lösung werden ausführliche Informationen zur Lösung angezeigt. Klicken Sie auf **Hinzufügen**.


4. Klicken Sie auf der Bestätigungsseite auf **Accept**, um den Datenschutzbestimmungen und Nutzungsbedingungen zuzustimmen.


5. Auf der Seite "Overview" in Operational Insights wird eine neue Kachel für die hinzugefügte Lösung angezeigt, und Sie können Sie verwenden, nachdem der Operational Insights-Dienst Ihre Daten verarbeitet hat.




### So entfernen Sie eine Lösung



1. Klicken Sie in Operational Insights auf der Seite "Overview" auf die Kachel **Solutions Gallery**.


2. Klicken Sie auf der Seite "Operational Insights Solutions Gallery" unterhalb der Lösung, die Sie entfernen möchten, und klicken Sie dann auf **Entfernen**.


3. Klicken Sie auf der Bestätigungsseite auf **Ja**, um die Lösung zu entfernen.

<!---HONumber=July15_HO2-->
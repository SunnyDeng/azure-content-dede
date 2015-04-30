<properties 
    pageTitle="Intelligence Packs für Operational Insights" 
    description="Mit Intelligence Packs können Sie Operational Insights um zusätzliche Funktionalität erweitern" 
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
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Intelligence Packs für Operational Insights

Microsoft Azure Operational Insights enthält das grundlegende Modul zur Konfigurationsbewertung. Sie können jedoch die Funktionalität erweitern, indem Sie Intelligence Packs von der Übersichtsseite hinzufügen.

![Abbildung des Intelligence Packs-Symbols](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

Nachdem Sie ein Intelligence Pack hinzugefügt haben, werden Daten der Server in Ihrer Infrastruktur gesammelt und an den Operational Insights-Dienst gesendet. Die Verarbeitung durch den Operational Insights-Dienst kann wenige Minuten, jedoch auch mehrere Stunden dauern. Nach der Verarbeitung der Daten durch den Dienst können Sie diese in Operational Insights anzeigen.

Wenn Sie ein Intelligence Pack nicht mehr benötigen, können Sie es problemlos entfernen. Wenn Sie ein Intelligence Pack entfernen, werden die entsprechenden Daten nicht mehr an Operational Insights gesendet, wodurch die Datenmenge sinkt, die von Ihrem täglichen Kontingent genutzt werden.

## Durch Microsoft Monitoring Agent unterstützte Intelligence Packs

Zurzeit können Server, die über Microsoft Monitoring Agent direkt mit Microsoft Azure Operational Insights verbunden sind, die meisten verfügbaren Intelligence Packs nutzen, einschließlich der folgenden:

- [System Updates](operational-insights-updates.md)

- [Log Management](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Change Tracking](operational-insights-change-tracking.md)

- [SQL und Active Directory Assessment](operational-insights-assessment.md)

Folgende Intelligence Packs werden mit Microsoft Monitoring Agent *nicht* unterstützt:

- [Capacity Management](operational-insights-capacity.md)

- [Configuration Assessment](operational-insights-configuration-assessment.md)

Die Erfassung von IIS-Protokollen wird auf Computern mit folgenden Betriebssystemen unterstützt:

- Windows Server 2012

- Windows Server 2012 R2

### Hinzufügen eines Intelligence Packs


1. Klicken Sie auf der Übersichtsseite von Operational Insights auf die Kachel **Intelligence Packs**.


2. Auf der Galerieseite zu den Operational Insights Intelligence Packs finden Sie Informationen zu jedem verfügbaren Intelligence Pack. Klicken Sie auf den Namen des Intelligence Packs, das Sie zu Operational Insights hinzufügen möchten.


3. Auf der Seite des ausgewählten Intelligence Pack werden detaillierte Informationen zu diesem Intelligence Pack angezeigt. Klicken Sie auf **Hinzufügen**.


4. Klicken Sie auf der Bestätigungsseite auf **Zustimmen**, um die Datenschutzerklärung und die Nutzungsbedingungen zu akzeptieren.


5. Auf der Übersichtsseite von Operational Insights wird eine neue Kachel für das hinzugefügte Intelligence Pack angezeigt, und Sie können dieses Pack verwenden, nachdem die Operational Insights-Dienste Ihre Daten verarbeitet haben.




### Entfernen eines Intelligence Packs



1. Klicken Sie auf der Übersichtsseite von Operational Insights auf die Kachel **Intelligence Packs**.


2. Klicken Sie auf der Galerieseite zu den Operational Insights Intelligence Packs unterhalb des Intelligence Packs, das Sie entfernen möchten, und klicken Sie auf **Entfernen**.


3. Klicken Sie auf der Bestätigungsseite auf **Ja**, um das Intelligence Pack zu entfernen.



<!--HONumber=52-->
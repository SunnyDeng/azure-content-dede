<properties
   pageTitle="Aktualisieren von Servern mit System Updates"
   description="Sie erfahren, wie Sie mit der Lösung „System Updates“ in Microsoft Azure Operational Insights fehlende Updates auf Server in Ihrer Infrastruktur anwenden können."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Aktualisieren von Servern mit System Updates

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Mithilfe der Lösung "System Updates" in Microsoft Azure Operational Insights können Sie fehlende Updates auf Server in Ihrer Infrastruktur anwenden. Sie installieren die Lösung, um den Operations Manager-Agent und das Basiskonfigurationsmodul für Operational Insights zu aktualisieren. Updateinformationen werden auf den überwachten Servern gelesen und dann zur Verarbeitung an den Operational Insights-Dienst in der Cloud gesendet. Auf die Updatedaten wird Logik angewendet, und der Cloud-Dienst zeichnet die Daten auf. Wenn ermittelt wird, dass Updates fehlen, werden diese im Dashboard **Updates** angezeigt. Mithilfe des Dashboards **Updates** können Sie mit fehlenden Updates arbeiten und einen Plan für deren Anwendung auf die betreffenden Server entwickeln.

## Verwenden von System Updates zum Aktualisieren von Servers

Bevor Sie System Updates in Microsoft Azure Operational Insights verwenden können, müssen Sie die Lösung installiert haben. Weitere Informationen zum Installieren von Lösungen finden Sie unter [Verwenden von Solutions Gallery zum Hinzufügen oder Entfernen von Lösungen](operational-insights-add-solution.md). Nach der Installation können Sie die Updates, die auf den überwachten Servern fehlen, in Operational Insights über die Kachel **System Update Assessment** auf der Seite **Übersicht** anzeigen.

### So arbeiten Sie mit Updates

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **System Update Assessment**. ![Bild der Seite „Übersicht“](./media/operational-insights-updates/updates01.png)
2. Zeigen Sie auf dem Dashboard **Updates** die Updatekategorien an. ![Bild der Seite „Updates“](./media/operational-insights-updates/updates02.png)
3. Führen Sie auf der Seite einen Bildlauf nach rechts aus, um das Blatt **Typ fehlender Updates** anzuzeigen, und klicken Sie auf **Sicherheitsupdates**. ![Bild der Seite „Updates“](./media/operational-insights-updates/updates03.png)
4. Auf der Seite „Suche“ wird eine Liste mit Sicherheitsupdates angezeigt, die für die Server Ihrer Infrastruktur als fehlend ermittelt wurden. Klicken Sie auf einen Knowledge Base-Artikel (KBID), um weitere Informationen zum fehlenden Update anzuzeigen. In diesem Beispiel ist dies *KBID 3032323*. ![Bild der Seite „Updates“](./media/operational-insights-updates/updates04.png)
5. Im Webbrowser wird der Knowledge Base-Artikel geöffnet, in dem das Update beschrieben wird. ![Bild der Seite „Updates“](./media/operational-insights-updates/updates05.png)
6. Mit den gefundenen Informationen können Sie einen Plan zum Anwenden fehlender Updates erstellen.

<!---HONumber=July15_HO2-->
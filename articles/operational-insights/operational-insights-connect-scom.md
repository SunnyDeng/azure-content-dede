<properties
   pageTitle="Verbinden mit Operational Insights von System Center Operations Manager aus"
   description="Erfahren Sie mehr über das Herstellen einer Verbindung mit Operational Insights über Operations Manager."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# Verbinden mit Operational Insights von System Center Operations Manager aus


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Sie können Operational Insights mit einer vorhandenen System Center Operations Manager-Umgebung verbinden. Dadurch können Sie vorhandene Operations Manager-Agents für die Datensammlung verwenden. Weitere Informationen zur Verwendung von Operations Manager mit Operational Insights finden Sie unter [Überlegungen zu Operations Manager mit Operational Insights](operational-insights-operations-manager.md).

Wenn Sie Operations Manager zum Überwachen der folgenden Arbeitsauslastungen verwenden, müssen Sie dafür ausführende Operations Manager-Konten festlegen. Weitere Informationen zum Festlegen der Konten finden Sie unter [Überlegungen zu Operations Manager mit Operational Insights](operational-insights-operations-manager.md).

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] Ab Operations Manager 2012 SP1 UR6 und Operations Manager 2012 R2 UR2 steht Unterstützung für Operational Insights zur Verfügung. Proxy-Unterstützung wurde in System Center Operations Manager 2012 SP1 UR7 und System Center Operations Manager 2012 R2 UR3 hinzugefügt.

## So verbinden Sie Operations Manager mit Operational Insights und fügen Agents hinzu

1. Klicken Sie in der Operations Manager-Konsole auf **Verwaltung**.

2. Erweitern Sie den Knoten **Operational Insights**, und klicken Sie auf **Operational Insights-Verbindung**.

3. Klicken Sie auf den Link **Registrieren bei Operational Insights**, und befolgen Sie die Anweisungen am Bildschirm.

4. Klicken Sie nach Abschluss des Registrierungs-Assistenten auf **Computer/Gruppe hinzufügen**.
![Operations Manager – Computer/Gruppe hinzufügen](./media/operational-insights-connect-scom/om01.png)
5. Im Dialogfeld **Computersuche** können Sie nach Computern oder Gruppen suchen, die von Operations Manager überwacht werden. Wählen Sie Computer oder Gruppen aus, die in Operational Insights aufgenommen werden sollen, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **OK**.
![Operations Manager – Computer hinzufügn](./media/operational-insights-connect-scom/om02.png)
## Nächste Schritte

[Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)](operational-insights-proxy-firewall.md)

<!-----HONumber=July15_HO5-->
<properties 
   pageTitle="Verbinden mit Operational Insights von System Center Operations Manager aus" 
   description="Erfahren Sie mehr über das Herstellen einer Verbindung mit Operational Insights über Operations Manager." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Verbinden mit Operational Insights von System Center Operations Manager aus 

Sie können Operational Insights mit einer vorhandenen System Center Operations Manager-Umgebung verbinden. Dadurch können Sie vorhandene Operations Manager-Agents als Operational Insights-Agents verwenden.

 >[AZURE.NOTE] Ab Operations Manager 2012 SP1 UR6 und Operations Manager 2012 R2 UR2 steht Unterstützung für Operational Insights zur Verfügung. Proxy-Unterstützung wurde in SCOM 2012 SP1 UR7 und SCOM 2012 R2 UR3 hinzugefügt.

## Verbinden von SCOM mit Operational Insights und Hinzufügen von Agents

1. Klicken Sie in der Operations Manager-Konsole auf **Verwaltung**.

2. Erweitern Sie den Knoten **Operational Insights**, und klicken Sie auf **Operational Insights-Verbindung**.

3. Klicken Sie auf den Link **Registrieren bei Operational Insights**, und befolgen Sie die Anweisungen am Bildschirm. 

4. Klicken Sie nach Abschluss des Registrierungs-Assistenten auf **Computer/Gruppe hinzufügen**.

5. Im Dialogfeld **Computersuche** können Sie nach Computern oder Gruppen suchen, die von Operations Manager überwacht werden. Wählen Sie Computer oder Gruppen aus, die in Operational Insights aufgenommen werden sollen, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

[Konfigurieren von Proxy- und Firewall-Einstellungen (Optional)](https://msdn.microsoft.com/library/azure/dn884643.aspx)


<!--HONumber=52-->
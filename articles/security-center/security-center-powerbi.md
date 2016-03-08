<properties
   pageTitle="Gewinnen von Erkenntnissen aus Azure Security Center-Daten mit Power BI| Microsoft Azure"
   description="Das Azure Security Center Power BI-Inhaltspaket macht es Ihnen leicht, Sicherheitswarnungen und -empfehlungen, angegriffene Ressourcen und Trends ausgehend von einem Datensatz zu finden, der für Ihre Berichte erstellt wurde."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/25/2016"
   ms.author="yurid"/>

# Gewinnen von Erkenntnissen aus Azure Security Center-Daten mit Power BI
Das [Power BI-Dashboard](http://aka.ms/azure-security-center-power-bi) für Azure Security Center ermöglicht Ihnen das Visualisieren, Analysieren und Filtern von Empfehlungen und Sicherheitswarnungen über alle Geräte, einschließlich Ihres Mobilgeräts. Erkennen Sie mit dem Power BI-Dashboard Trends und Angriffsmuster, und zeigen Sie Sicherheitswarnungen nach Ressource oder Quell-IP-Adresse und Sicherheitsrisiken ohne Adresse nach Ressource oder Alter an. Sie können die Sicherheitswarnungen und Empfehlungen aus dem Security Center auch mit anderen Daten kombinieren, z. B. mit [Azure-Überwachungsprotokollen](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) und der [Azure SQL-Datenbanküberwachung](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), die beide Power BI-Dashboards enthalten. Sie können diese Daten aber in Excel exportieren und so einfach Berichte zum Sicherheitszustand Ihrer Cloudressourcen erstellen.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Untersuchen von Azure Security Center-Daten mit Power BI-Diensten
Stellen Sie eine Verbindung mit dem [Azure Security Center-Inhaltspaket](https://app.powerbi.com/groups/me/getdata/services/azure-security-center) in Power BI her, und befolgen Sie die folgenden Schritte:

1\. Klicken Sie zum Fortfahren in der Azure Security Center-Kachel auf **Verbinden**.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig1.png)

2\. Das Fenster **Mit Azure Security Center verbinden** wird geöffnet. Geben Sie im Feld **Azure-Abonnement-ID** Ihr Azure-Abonnement ein, und klicken Sie auf **Weiter**.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig2.png)

3\. Wählen Sie in der Dropdownliste **Authentifizierungsmethode** die Option **OAuth2** aus, und klicken Sie auf **Anmelden**.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig3.png)

4\. Sie werden zu einer Authentifizierungsseite umgeleitet. Geben Sie dort die Anmeldedaten ein, die Sie zum Verbinden mit Azure Security Center verwenden. Nach Abschluss des Authentifizierungsprozesses beginnt Power BI mit dem Import von Daten, um die Berichte zu erstellen. Während dieses Zeitraums erscheint rechts unten im Browser möglicherweise diese Meldung:

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig4.png)

5\. Sobald der Prozess abgeschlossen ist, werden die Berichte wie unten angezeigt in das Azure Security Center Power BI-Dashboard geladen.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig5.png)

So sehen Sie auf einen Blick die Anzahl der Sicherheitswarnungen und Empfehlungen sowie die Zahl der virtuellen Computer, Azure SQL-Datenbanken und Netzwerkressourcen, die von Azure Security Center überwacht werden.

Ein Link zu Azure Security Center leitet Sie zum Azure-Portal weiter. Die Diagramme ermöglichen ein einfaches Visualisieren von Informationen zu Sicherheitsempfehlungen und -warnungen, einschließlich:

- Sicherheitsintegrität von Ressourcen
- Insgesamt ausstehende Vorschläge
- Empfehlungen für virtuelle Computer
- Warnungen im Laufe der Zeit
- Angegriffene Ressourcen
- Angegriffene IPs

Jedes Diagramm liefert Ihnen zusätzliche Einblicke. Wählen Sie eine Kachel aus, um weitere Informationen zu sehen. Die Kachel „Sicherheitsintegrität von Ressourcen“ zeigt beispielsweise zusätzliche Details zu ausstehenden Empfehlungen nach Ressourcen an:

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Wenn Sie auf eine beliebige Zeile in diesem Diagramms klicken, werden die anderen abgeblendet, und Sie können Sie sich nur auf Ihre Auswahl konzentrieren. Klicken Sie im linken Bereich dieser Seite unter der Option **Dashboards** auf **Azure Security Center**, um zum Dashboard zurückzukehren.

> [AZURE.NOTE] Sie können den Bericht bearbeiten und ihn anpassen, zusätzliche Felder hinzufügen oder bestehende Grafiken ändern. Weitere Informationen finden Sie unter [Interact with a report in Editing View in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) (Interagieren mit einem Bericht in der Bearbeitungsansicht in Power BI).

Wenn Sie auf die einzelnen Kacheln **Warnungen im Laufe der Zeit**, **Angegriffene Ressourcen** und **Angreifer-IPs** klicken, ist deren Ausgabe ähnlich. Dies liegt daran, dass der Bericht Informationen zu all diesen drei Variablen unter der Bezeichnung **Angegriffene Ressourcen** zusammenführt:

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig7.png)

Nun können Sie mit den Optionen im Menü **Datei** eine Kopie dieses Berichts speichern, ihn ausdrucken oder ihn im Internet veröffentlichen.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig8.png)


##Verwenden des Azure Security Center-Dashboards für den Zugriff auf Power BI
Auf Power BI-Berichte können Sie auch über die Azure Security Center-Dashboards zugreifen. Gehen Sie folgendermaßen vor, um diese Aufgabe auszuführen:

1\. Klicken Sie im **Azure Security Center**-Dashboard auf die Schaltfläche **In Power BI erkunden**.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig9.png)

2\. Das Blatt **In Power BI erkunden** wird wie unten angezeigt auf der rechten Seite geöffnet.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig10-1.png)

3\. Wählen Sie in der Dropdownliste **Wählen Sie ein in Power BI zu erkundendes Abonnement aus** das Abonnement aus, das Sie verwenden möchten.

4\. Klicken Sie im Feld **Abonnement-ID kopieren** auf die Kopierschaltfläche. 5. Klicken Sie auf die Schaltfläche **Zu Power BI wechseln**. 6. Das Fenster **Mit Azure Security Center verbinden** wird geöffnet. Geben Sie im Feld **Azure-Abonnement-ID** Ihr Azure-Abonnement ein, und klicken Sie auf **Weiter**.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig2.png)

7\. Wählen Sie in der Dropdownliste **Authentifizierungsmethode** die Option **OAuth2** aus, und klicken Sie auf **Anmelden**.

![Mithilfe von Power BI mit Azure Security Center verbinden](./media/security-center-powerbi/security-center-powerbi-fig3.png)

8\. Sie werden zu einer Authentifizierungsseite umgeleitet. Geben Sie dort die Anmeldedaten ein, die Sie zum Verbinden mit Azure Security Center verwenden. Nach Abschluss des Authentifizierungsprozesses beginnt Power BI mit dem Import von Daten, um die Berichte zu erstellen.

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Power BI in Azure Security Center verwenden können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) – erfahren Sie, wie Sie Sicherheitseinstellungen in Azure Security Center konfigurieren.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0302_2016-->
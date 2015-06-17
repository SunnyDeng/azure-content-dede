<properties 
	pageTitle="Ressourcen, Rollen und Zugriffssteuerung in Application Insights" 
	description="&quot;Besitzer&quot;, &quot;Mitwirkende&quot; und &quot;Leser&quot; für die gewonnenen Unternehmensinformationen." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="awills"/>
 
# Ressourcen, Rollen und Zugriffssteuerung in Application Insights

Anhand einer [rollenbasierten Zugriffssteuerung in Microsoft Azure](../role-based-access-control-configure.md) können Sie steuern, wer in Visual Studio [Application Insights][start] über Lese- und Aktualisierungszugriff auf Ihre Daten verfügt.

> [AZURE.IMPORTANT]Weisen Sie Benutzern den Zugriff in der **Ressourcengruppe oder dem Abonnement** zu, dem Ihre Anwendungsressource angehört – nicht in der Ressource selbst. Weisen Sie die Rolle **Mitwirkender der Application Insights-Komponente** zu. Dadurch wird eine einheitliche Steuerung des Zugriffs auf Webtests und Warnungen zusammen mit Ihrer Anwendungsressource sichergestellt. [Weitere Informationen](#access).


## Ressourcen, Gruppen und Abonnements

Zunächst einige Definitionen:

* **Ressource** – Eine Instanz eines Microsoft Azure-Diensts. Die Application Insights-Ressource erfasst, analysiert und zeigt die Telemetriedaten an, die von der Anwendung gesendet werden. Andere Arten von Azure-Ressourcen sind Web-Apps, Datenbanken und virtuelle Computer. 

    Um alle Ressourcen anzuzeigen, wechseln Sie zum [Azure-Portal][portal], melden Sie sich an, und klicken Sie auf "Durchsuchen".

    ![Wählen Sie "Durchsuchen", und wählen Sie dann entweder "Alle", oder filtern Sie nach Application Insights.](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Ressourcengruppe**][group] – Jede Ressource gehört zu einer Gruppe. Eine Gruppe ist eine bequeme Möglichkeit, verwandte Ressourcen insbesondere für die Zugriffssteuerung zu verwalten. Beispielsweise könnten Sie eine Web-App, eine Application Insights-Ressource zur Überwachung der App sowie eine Storage-Ressource zur Aufbewahrung exportierter Daten in eine Ressourcengruppe aufnehmen.

    
    ![Wählen Sie "Durchsuchen", "Ressourcengruppen" und dann eine Gruppe aus.](./media/app-insights-resources-roles-access-control/11-group.png)


* [**Abonnement**](https://manage.windowsazure.com) – Zum Verwenden von Application Insights oder anderen Azure-Ressourcen melden Sie sich bei einem Azure-Abonnement an. Jede Ressourcengruppe gehört zu einem Azure-Abonnement, in dem Sie Ihr Preispaket und, wenn es sich um ein Abonnement für die Organisation handelt, die Mitglieder und deren Zugriffsberechtigungen auswählen.
* [**Microsoft-Konto**][account] – Benutzername und Kennwort, den bzw. das Sie zur Anmeldung bei den Microsoft Azure-Abonnements, XBox Live, Outlook.com und anderen Microsoft-Diensten verwenden.


## <a name="access"></a> Steuern des Zugriffs in der Ressourcengruppe

Es ist wichtig zu wissen, dass es zusätzlich zu den Ressource, die Sie für Ihre Anwendung erstellt haben, auch separate, ausgeblendete Ressourcen für Warnungen und Webtests gibt. Sie sind derselben [Ressourcengruppe](#resource-group) zugeordnet wie Ihre Anwendung. Möglicherweise haben Sie auch andere Azure-Dienste dort aufgenommen, z. B. Websites oder Speicher.

![Ressourcen in Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Zum Steuern des Zugriffs auf diese Ressourcen empfiehlt es sich daher, folgendermaßen vorzugehen:

* Steuern Sie den Zugriff auf **Ressourcengruppen- oder Abonnementebene**.
* Weisen Sie Benutzern die Rolle **Mitwirkender der Application Insights-Komponente** zu. Dadurch können sie Webtests, Warnungen und Application Insights-Ressourcen bearbeiten, ohne dass Zugriff auf alle anderen Dienste in der Gruppe erteilt wird. 

## So erteilen Sie einem anderen Benutzer Zugriffsberechtigungen

Sie benötigen Eigentümerrechte für das Abonnement oder die Ressourcengruppe.

Der Benutzer benötigt ein [Microsoft-Konto][account]. Sie können den Zugriff an Einzelpersonen und auch an Benutzergruppen erteilen, die in Azure Active Directory definiert sind.

#### Navigieren zur Ressourcengruppe

Fügen Sie den Benutzer dort hinzu.

![Öffnen Sie auf dem Ressourcenblatt in Ihrer Anwendung "Essentials", öffnen Sie die Ressourcengruppe, und wählen Sie dort "Einstellungen/Benutzer". Klicken Sie auf "Hinzufügen".](./media/app-insights-resources-roles-access-control/01-add-user.png)

Sie können auch zur nächsthöheren Ebene wechseln und den Benutzer zum Abonnement hinzufügen.

#### Auswählen einer Rolle

![Wählen Sie eine Rolle für den neuen Benutzer.](./media/app-insights-resources-roles-access-control/03-role.png)

Rolle | In der Ressourcengruppe
---|---
Besitzer | Kann alle Elemente ändern, einschließlich des Benutzerzugriffs
Mitwirkender | Kann alle Elemente bearbeiten, einschließlich aller Ressourcen
Mitwirkender der Application Insights-Komponente | Kann Application Insights-Ressourcen, Webtests und Warnungen bearbeiten.
Leser | Kann Inhalte anzeigen, aber nicht ändern.

Zum "Bearbeiten" gehört das Erstellen, Löschen und Aktualisieren von:

* Ressourcen
* Webtests
* Warnungen
* Fortlaufendem Export

#### Auswählen des Benutzers


![Geben Sie die E-Mail-Adresse eines neuen Benutzers ein. Auswählen des Benutzers](./media/app-insights-resources-roles-access-control/04-user.png)

Wenn der gewünschte Benutzer nicht im Verzeichnis enthalten ist, können Sie jeden Benutzer mit einem Microsoft-Konto einladen. (Wenn Benutzer Dienste wie Outlook.com, OneDrive, Windows Phone oder XBox Live verwenden, besitzen sie ein Microsoft-Konto.)



## Benutzer und Rollen

* [Rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control-configure.md)



<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-preview-portal-using-resource-groups.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->
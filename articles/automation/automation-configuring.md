<properties
   pageTitle="Konfigurieren von Azure Automation"
   description="Beschreibt die Schritte, die Sie zum Konfigurieren von Azure Automation für die erste Verwendung ausführen müssen."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="magoedte;bwren" />

# Konfigurieren von Azure Automation

Dieser Artikel beschreibt die Aktionen, die Sie vor der Verwendung von Azure Automation ausführen müssen.

## Automation-Konten

Wenn Sie das erste Mal mit Azure Automation arbeiten, müssen Sie zunächst mindestens ein Automation-Konto erstellen. Mithilfe von Automation-Konten können Sie Ihre Automation-Ressourcen (Runbooks, Objekte, Konfigurationen) von den Automation-Ressourcen in anderen Automation-Konten isolieren. Sie können Automation-Konten dazu verwenden, Automation-Ressourcen in separate logische Umgebungen zu trennen. Beispielsweise können Sie ein Konto für die Entwicklung und ein anderes für die Produktion verwenden.

Die Automation-Ressourcen für jedes Automation-Konto sind mit einer einzelnen Azure-Region verknüpft, die Automation-Konten können jedoch Azure-Dienste in beliebigen Regionen verwalten. Der Hauptgrund für das Erstellen von Automation-Konten in unterschiedlichen Regionen ist der, dass Richtlinien eine Isolierung von Daten und Ressourcen innerhalb einer spezifischen Region vorsehen.

>[AZURE.NOTE] Auf Automation-Konten und die darin enthaltenen Ressourcen, die mit dem Azure-Portal erstellt werden, kann im klassischen Azure-Portal nicht zugegriffen werden. Wenn Sie diese Konten oder die enthaltenen Ressourcen mit Windows PowerShell verwalten möchten, müssen Sie die Azure-Ressourcen-Manager-Module verwenden.
>
>Mit dem klassischen Azure-Portal erstellte Automation-Konten können in beiden Portalen und mit beiden Cmdlet-Sätzen verwaltet werden. Nach dem Erstellen des Kontos macht es keinen Unterschied, wie Sie Ressourcen innerhalb des Kontos erstellen und verwalten. Wenn Sie planen, auch weiterhin das klassische Azure-Portal zu verwenden, sollten Sie dieses anstelle des Azure-Portals für das Erstellen von Automation-Konten verwenden.


Ein Automation-Konto kann angehalten werden, wenn ein Problem mit Ihrem Azure-Konto vorliegt, etwa eine überfällige Zahlung. In diesem Fall ist ein Zugriff auf das Konto nicht möglich, ausgeführte Aufträge werden angehalten, und alle geplanten Aufträge werden deaktiviert. Sie können das Konto anzeigen, innerhalb des Kontos werden jedoch keine Ressourcen angezeigt. Nachdem das Problem behoben wurde und das Automation-Konto wieder aktiv ist, müssen Sie Ihre Zeitpläne aktivieren und alle Runbooks neu starten, die angehalten wurden.


## Konfigurieren der Authentifizierung für Azure-Ressourcen

Wenn Sie mithilfe von [Azure-Cmdlets](http://msdn.microsoft.com/library/azure/jj554330.aspx) auf Azure-Ressourcen zugreifen, müssen Sie eine Authentifizierung für Ihr Azure-Abonnement bereitstellen. In Azure Automation wird dies mit einem Organisationskonto in Azure Active Directory erreicht, das Sie als Administrator für Ihr Abonnement konfigurieren. Sie können anschließend [Anmeldeinformationen](http://msdn.microsoft.com/library/dn940015.aspx) für dieses Benutzerkonto erstellen und diese mit [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) in Ihrem Runbook verwenden.

>[AZURE.NOTE] Microsoft-Konten, vormals als LiveIDs bezeichnet, können nicht mit Azure Automation verwendet werden.

## Erstellen eines neuen Azure Active Directory-Benutzers zum Verwalten eines Azure-Abonnements

1. Melden Sie sich im klassischen Azure-Portal als Dienstadministrator für das Azure-Abonnement an, das Sie verwalten möchten.
2. Wählen Sie **Active Directory** aus.
3. Wählen Sie den Verzeichnisnamen aus, der Ihrem Azure-Abonnement zugeordnet ist. Falls erforderlich, ändern Sie die Zuordnung über **Einstellungen > Abonnements > Verzeichnis bearbeiten**.
4. [Erstellen Sie einen neuen Active Directory-Benutzer](http://msdn.microsoft.com/library/azure/hh967632.aspx). Wählen Sie als **Benutzertyp** die Einstellung **Neuer Benutzer in Ihrer Organisation** aus, und lassen Sie die Option **Multi-Factor Authentication aktivieren** deaktiviert.
5. Notieren Sie sich den vollständigen Namen des Benutzers und das vorläufige Kennwort.
7. Wählen Sie **Einstellungen > Administratoren > Hinzufügen**.
8. Geben Sie den vollständigen Namen des Benutzers ein, den Sie erstellt haben.
9. Wählen Sie das Abonnement aus, das der Benutzer verwalten soll.
10. Melden Sie sich bei Azure ab, und melden Sie sich anschließend mit den soeben erstellten Anmeldeinformationen wieder an. Sie werden aufgefordert, das Kennwort des Benutzers zu ändern.
11. Erstellen Sie ein neues [Azure Automation-Anmeldeinformationsobjekt](automation-credentials.md) für das soeben erstellte Benutzerkonto. Der **Anmeldeinformationstyp** sollte **Windows PowerShell-Anmeldeinformationen** lauten.

## Erstellen eines Automation-Kontos

Ein Automation-Konto ist ein Container für Ihre Azure Automation-Ressourcen. Es bietet eine Möglichkeit, Ihre Umgebungen zu trennen oder Ihre Workflows besser zu organisieren. Wenn Sie bereits ein Automation-Konto erstellt haben, können Sie diesen Schritt überspringen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie auf **Neu** > **Verwaltung** > **Automation-Konto**.

3. Konfigurieren Sie auf dem Blatt **Automation-Konto hinzufügen** die Details für Ihr Automation-Konto.

>[AZURE.NOTE] Wenn ein Automation-Konto über das Azure-Portal erstellt wird, werden weder das Konto noch die zugeordneten Ressourcen zurück in das klassische Azure-Portal übertragen.

Nachfolgend sehen Sie die Liste der zu konfigurierenden Parameter:

|Parameter |Beschreibung |
|:---|:---|
| Name | Name des Automation-Kontos, muss ein eindeutiger Wert sein. |
| Ressourcengruppe | Ressourcengruppen vereinfachen das Anzeigen und Verwalten zugehöriger Azure-Ressourcen. Im Azure-Portal können Sie für Ihr Automation-Konto eine vorhandene Ressourcengruppe wählen oder eine neue Ressourcengruppe erstellen. Im klassischen Azure-Portal dagegen werden alle Automation-Konto in der standardmäßigen Ressourcengruppe platziert. |
| Abonnement | Wählen Sie aus der Liste der verfügbaren Abonnements ein Abonnement aus. |
| Region | Die Region gibt an, wo die Automation-Ressourcen im Konto gespeichert werden. Sie können eine beliebige Region aus der Liste auswählen. Dies wirkt sich nicht auf die Funktionalität Ihres Kontos aus, aber Ihre Runbooks werden möglicherweise schneller ausgeführt, wenn sich Ihre Kontoregion in der Nähe Ihrer anderen Azure-Ressourcen befindet. |
| Kontooptionen | Mit dieser Option können Sie auswählen, welche Ressourcen in Ihrem neuen Automation-Konto erstellt werden. Wenn Sie **Ja** auswählen, wird ein Runbook zu Lernzwecken erstellt. |

![Konto erstellen](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE] Wenn ein über das klassische Azure-Portal erstelltes Automation-Konto mithilfe des Azure-Portals [in eine andere Ressourcengruppe verschoben](../resource-group-move-resources.md) wird, steht das Automation-Konto im klassischen Azure-Portal nicht mehr zur Verfügung.



## Verwenden der Anmeldeinformationen in einem Runbook

Sie können die Anmeldeinformationen in einem Runbook mithilfe der Aktivität [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) abrufen und dann mit [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) eine Verbindung mit Ihrem Azure-Abonnement herstellen. Wenn die Anmeldeinformationen mit einem Administrator für mehrere Azure-Abonnements verknüpft sind, sollten Sie mithilfe von [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) das richtige Abonnement angeben. Dies wird im nachstehenden Windows PowerShell-Beispiel gezeigt, das typischerweise im oberen Bereich der meisten Azure Automation-Runbooks angezeigt wird.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Diese Zeilen sollten nach jedem [Prüfpunkt](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) wiederholt werden. Wenn das Runbook angehalten und später von einem anderen Benutzer fortgesetzt wird, muss die Authentifizierung erneut durchgeführt werden.

## Verwandte Artikel
- [Azure Automation: Authentifizieren bei Azure mit Azure Active Directory](https://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 

<!---HONumber=AcomDC_0302_2016-->
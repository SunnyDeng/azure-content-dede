<properties
   pageTitle="Azure Automation und Verwaltung von Hybridclouds: Automatisieren der Bereitstellung einer VM in Amazon Web Services"
   description="Azure Automation und Verwaltung von Hybridclouds: Automatisieren der Bereitstellung einer VM in Amazon Web Services"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Azure Automation und Verwaltung von Hybridclouds: Automatisieren der Bereitstellung einer VM in Amazon Web Services

Bei unseren Veranstaltungen der [Cloud Roadshow](https://microsoftcloudroadshow.com/cities), auf denen ich Azure Automation präsentiert habe, wurden einige Fragen zu dem Thema gestellt, ob Azure Automation nur für Azure bestimmt ist. Glücklicherweise hatte ich eine Demonstration vorbereitet, über die ich in diesem Blogartikel sprechen werde. Die Hauptbotschaft ist aber, **dass Azure Automation nicht nur für Azure bestimmt ist, sondern für lokale Ressourcen *UND* auch zum Verwalten von Hybridcloudumgebungen!** Wenn Sie also beispielsweise Kunde von Amazon Web Services (AWS) sind, ist Azure Automation auch für Sie geeignet!

## Ziele

In diesem Artikel geht es darum, wie Sie Azure Automation zum Bereitstellen einer VM in AWS nutzen und der VM einen speziellen Namen geben können. Dies wird bei AWS als das „Taggen“ der VM bezeichnet.

## Annahmen

In diesem Artikel setze ich voraus, dass Sie bereits ein Azure Automation-Konto eingerichtet haben und auch bereits über ein AWS-Abonnement verfügen.

Weitere Informationen zum Einrichten eines Azure Automation-Kontos finden Sie auf unserer [Dokumentationsseite](automation-configuring.md).

## Vorbereitung: Speichern der AWS-Anmeldeinformationen

Damit Azure Automation mit AWS „kommunizieren“ kann, müssen Sie Ihre AWS-Anmeldeinformationen abrufen und als Ressourcen in Azure Automation speichern.

Klicken Sie nach dem Anmelden am AWS-Portal auf das kleine Dreieck unter Ihrem Namen und dann auf **Sicherheitsanmeldeinformationen**.

![AWS-Sicherheitsanmeldeinformationen](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "AWS-Sicherheitsanmeldeinformationen")

Klicken Sie auf **Zugriffsschlüssel**.

![AWS-Zugriffsschlüssel](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "AWS-Zugriffsschlüssel")

Kopieren Sie Ihren Zugriffsschlüssel und den geheimen Zugriffsschlüssel. (Sie können optional auch die Schlüsseldatei herunterladen, um sie an einem sicheren Ort zu speichern.)

![AWS-Zugriffsschlüssel erstellen](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "AWS-Zugriffsschlüssel erstellen")

## AWS-Ressourcen in Azure Automation

Im vorherigen Schritt haben Sie Ihre **Zugriffsschlüssel-ID** und den **geheimen Zugriffsschlüssel** kopiert und gespeichert. Wir speichern diese Schlüssel nun in Azure Automation.

Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie **Ressourcen** > **Anmeldeinformationen**. Fügen Sie neue Anmeldeinformationen hinzu, und geben Sie diesen den Namen **AWScred**.

![Image](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "Image")

Geben Sie optional eine Beschreibung an, und stellen Sie sicher, dass Sie Ihre **Zugriffs-ID** im Feld **Benutzername** und Ihren **geheimen Zugriffsschlüssel** in das Feld mit der Bezeichnung **Kennwort** eingeben. Speichern Sie Ihre AWS-Anmeldeinformationen.

## Amazon Web Services-PowerShell-Modul

Unser Runbook für die VM-Bereitstellung nutzt das AWS-PowerShell-Modul zur Durchführung seiner Aufgaben. Das AWS-PowerShell-Modul ist im [PowerShell-Katalog](http://www.powershellgallery.com/packages/AWSPowerShell/) verfügbar und kann mit der Schaltfläche **In Azure Automation bereitstellen** leicht einem Automation-Konto hinzugefügt werden.

![AWS-PS-Modul in AA importieren](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "AWS-PS-Modul in AA importieren")

Wenn Sie auf **In Azure Automation bereitstellen** klicken, gelangen Sie zur Azure-Anmeldeseite. Nachdem Sie Ihre Anmeldeinformationen angegeben haben, wird der folgende Bildschirm angezeigt:

![Assistent zum Importieren von AWS-Modul](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "Assistent zum Importieren von AWS-Modul")

Wählen Sie ein neues oder vorhandenes Automation-Konto aus. Beachten Sie, dass kein Dropdownfeld für ein vorhandenes Konto verfügbar ist. Stellen Sie also sicher, dass Sie keine Rechtschreibfehler einbauen und dass die Region und die Ressourcengruppe für Ihre Automation-Konto stimmen. Führen Sie die weiteren Schritte aus, um die Konfiguration abzuschließen, und klicken Sie auf **Erstellen**.

Wenn Sie zu Ihrem ausgewählten Automation-Konto navigieren und auf **Ressourcen** > **Module** zugreifen, können Sie jetzt das AWS-Modul mit 1.427 Aktivitäten sehen!

>[AZURE.NOTE] Das Importieren eines PowerShell-Moduls in Azure Automation besteht aus zwei Schritten:
>
> 1.  Importieren des Moduls
> 2.  Extrahieren der Cmdlets
>
>Die Aktivitäten werden erst angezeigt, nachdem das Modul das Importieren und das Extrahieren der Cmdlets vollständig abgeschlossen hat. Dies kann einige Minuten dauern.

## Bereitstellen eines AWS-VM-Runbooks

Nachdem wir nun über alle erforderlichen Komponenten verfügen, können wir unser Runbook erstellen, mit dem eine VM in AWS bereitgestellt wird. Wir zeigen auch, dass Sie ein natives PowerShell-Skript in Azure Automation anstelle eines PowerShell-Workflows nutzen können.

Weitere Informationen zu den Unterschieden zwischen nativer PowerShell und einem PowerShell-Workflow finden Sie [hier](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/). Informationen zur Erstellen grafischer Runbooks finden Sie [hier](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/).

Sie können das PowerShell-Skript für unser Runbook aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript) herunterladen.

Sie können das Skript herunterladen, indem Sie eine PowerShell-Sitzung öffnen und Folgendes eingeben: **Save-Script -Name New-AwsVM -Path <Pfad>**.

Wenn Sie das PowerShell-Skript gespeichert haben, können Sie es Azure Automation wie folgt als Runbook hinzufügen:

Klicken Sie unter Ihrem **Automation-Konto** auf **Runbooks**, und wählen Sie die Option **Runbook hinzufügen**. Wählen Sie **Schnellerfassung** (Neues Runbook erstellen), geben Sie einen Namen für Ihr Runbook an, und wählen Sie unter **Runbooktyp** die Option **PowerShell**. Klicken Sie anschließend auf **Erstellen**.

![AWS-Runbook erstellen](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "AWS-Runbook erstellen")

Kopieren Sie das PowerShell-Skript, nachdem der Runbook-Editor geöffnet wurde, und fügen Sie es in den Bereich für die Runbookerstellung ein.

![Image](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "Image")

Beachten Sie beim Herunterladen des PowerShell-Skripts Folgendes:

-   Das Runbook enthält eine Reihe von Standardparameterwerten, wie im Abschnitt „#ToDo“ erwähnt. Evaluieren Sie alle Standardwerte, und aktualisieren Sie diese bei Bedarf.
-   Stellen Sie sicher, dass Sie Ihre AWS-Anmeldeinformationen unter **Ressourcen** mit dem Namen **AWScred** gespeichert haben.
-   Die von Ihnen verwendete Region richtet sich nach Ihrem AWS-Abonnement. Klicken Sie im AWS-Portal, in dem Sie auch nach Ihren Sicherheitsanmeldeinformationen gesucht haben, auf den Pfeil neben Ihrem Konto, um Ihre Region zu überprüfen.

![AWS-Region](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "AWS-Region")

-   Ermitteln Sie, [welche Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) Sie in Ihrem Runbook angeben müssen. Meine Region ist beispielsweise „USA, Westen“ (Oregon), also „us-west-2“.

![AWS-Regionen](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "AWS-Regionen")

-   Sie können eine Liste mit Imagenamen abrufen, wenn Sie PowerShell ISE verwenden, das Modul importieren und die Authentifizierung für AWS durchführen, indem Sie **Get-AutomationPSCredential** in Ihrer ISE-Umgebung durch **$AwsCred = Get-Credential** ersetzen. Sie werden zum Eingeben von Anmeldeinformationen aufgefordert, und Sie können Ihre **Zugriffsschlüssel-ID** und Ihren **geheimen Zugriffsschlüssel** als Benutzernamen und Kennwort eingeben. Sehen Sie sich das folgende Beispiel an:


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


Sie erhalten diese Ausgabe:

![AWS-Images abrufen](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "AWS-Images abrufen")

Kopieren Sie den gewünschten Imagenamen, und fügen Sie ihn in eine Automation-Variable ein, auf die im Runbook per **$InstanceType** verwiesen wird. Da ich eine kostenlose AWS-Ebene verwende, habe ich in meinem Runbook **t2.micro** ausgewählt.

Weitere Informationen zu Amazon EC2-Instanztypen finden Sie [hier](https://aws.amazon.com/ec2/instance-types/).

## Testen des bereitgestellten AWS-VM-Runbooks

Preflight-Prüfliste:

-   Ressourcen für die Authentifizierung gegenüber AWS wurden erstellt und mit dem Namen **AWScred** versehen.
-   Das AWS-PowerShell-Modul wurde in Azure Automation importiert.
-   Es wurde ein neues Runbook erstellt, und Parameterwerte wurden bei Bedarf überprüft und aktualisiert.
-   Die Optionen **Ausführliche Datensätze protokollieren** und optional **Statusdatensätze protokollieren** in den Runbookeinstellungen wurden auf **Ein** festgelegt.

Wir beginnen mit der Erstellung unseres neuen Runbooks, indem wir einen **VMname** angeben.

![New-AwsVM-Runbook starten](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "New-AwsVM-Runbook starten")

Da wir **Ausführliche Datensätze protokollieren** und **Statusdatensätze protokollieren** aktiviert haben, können wir unsere ausgegebenen **Datenströme** unter **Alle Protokolle** verfolgen.

![Datenstromausgabe](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "Datenstromausgabe")

Wir überprüfen dies in AWS:

![Per AWS-Konsole bereitgestellte VM](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "Per AWS-Konsole bereitgestellte VM")

Sehr schön.

In diesem Artikel habe ich ein Beispiel dafür gegeben, wie Azure Automation zum Verwalten von Hybridclouds verwendet werden kann, z. B. durch das Erstellen einer VM in Amazon Web Services und das Anwenden eines benutzerdefinierten Tags, um ihr einen Namen zu geben.

Viel Spaß beim Automatisieren und bis zum nächsten Mal!

Tiander.

<!---HONumber=AcomDC_0204_2016-->
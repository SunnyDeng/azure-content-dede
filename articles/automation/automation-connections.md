<properties 
   pageTitle="Verbindungsobjekte in Azure Automation | Microsoft Azure"
   description="Verbindungsobjekte in Azure Automation enthalten die Informationen, die zur Verbindungsherstellung mit einem externen Dienst oder einer externen Anwendung aus einem Runbook oder einer DSC-Konfiguration erforderlich sind. Dieser Artikel stellt eine ausführliche Beschreibung von Verbindungen bereit und zeigt, wie diese in Textrunbooks und grafischen Runbooks eingesetzt werden."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/27/2016"
   ms.author="bwren" />

# Verbindungsobjekte in Azure Automation

Ein Automation-Verbindungsobjekt enthält die Informationen, die zur Verbindungsherstellung mit einem externen Dienst oder einer externen Anwendung aus einem Runbook oder einer DSC-Konfiguration erforderlich sind. Dies kann Informationen einschließen, die für die Authentifizierung erforderlich sind, beispielsweise ein Benutzername und ein Kennwort oder Verbindungsinformationen wie eine URL oder ein Port. Der Wert einer Verbindung liegt darin, dass alle Eigenschaften zur Verbindungsherstellung mit einer bestimmten Anwendung in einem Objekt gespeichert werden, statt mehrere Variablen zu erstellen. Der Benutzer kann die Werte für eine Verbindung an einem Ort bearbeiten, und Sie können den Namen einer Verbindung in einem einzelnen Parameter an ein Runbook oder eine DSC-Konfiguration übergeben. Auf die Eigenschaften für eine Verbindung kann im Runbook oder in der DSC-Konfiguration über die Aktivität **Get-AutomationConnection** zugegriffen werden.

Wenn Sie eine Verbindung erstellen, müssen Sie einen *Verbindungstyp* angeben. Der Verbindungstyp ist eine Vorlage, die einen Satz von Eigenschaften definiert. Die Verbindung definiert Werte für jede der Eigenschaften, die im zugehörigen Verbindungstyp definiert sind. Verbindungstypen werden in Integrationsmodulen zu Azure Automation hinzugefügt oder mit der [ Azure Automation-API](http://msdn.microsoft.com/library/azure/mt163818.aspx) erstellt. Es stehen beim Erstellen einer Verbindung nur die Verbindungstypen zur Verfügung, die in Ihrem Automation-Konto installiert sind.

>[AZURE.NOTE] Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Dieser Schlüssel wird mit einem Masterzertifikat verschlüsselt und in Azure Automation gespeichert. Vor dem Speichern eines sicheren Objekts wird der Schlüssel für das Automation-Konto mit dem Masterzertifikat verschlüsselt und anschließend zum Verschlüsseln des Objekts verwendet.

## Windows PowerShell-Cmdlets

Die Cmdlets in der folgenden Tabelle werden zum Erstellen und Verwalten von Automation-Verbindungen mit Windows PowerShell verwendet. Sie gehören zum Lieferumfang des [Azure PowerShell-Moduls](../powershell-install-configure.md), das zur Verwendung in Automation-Runbook und in DSC-Konfigurationen zur Verfügung steht.

|Cmdlet|Beschreibung|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|Ruft eine Verbindung ab. Enthält eine Hashtabelle mit den Werten der Felder für die Verbindung.|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|Erstellt eine neue Verbindung.|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|Entfernt eine vorhandene Verbindung.|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|Legt den Wert für ein bestimmtes Feld einer vorhandenen Verbindung fest.|

## Aktivitäten

Die Aktivitäten in der folgenden Tabelle werden für den Zugriff auf Verbindungen in einem Runbook oder einer DSC-Konfiguration verwendet.

|Aktivitäten|Beschreibung|
|---|---|
|Get-AutomationConnection|Ruft eine zu verwendende Verbindung ab. Gibt eine Hashtabelle mit den Eigenschaften der Verbindung zurück.|

>[AZURE.NOTE] Vermeiden Sie die Verwendung von Variablen im Parameter „-Name“ von **Get-AutomationConnection**, da dies die Ermittlung von Abhängigkeiten zwischen Runbooks oder DSC-Konfigurationen und Verbindungsobjekten zur Entwurfszeit erschweren kann.

## Erstellen einer neuen Verbindung

### So erstellen Sie eine neue Verbindung mit dem Azure-Portal

1. Klicken Sie in Ihrem Automation-Konto im oberen Fensterbereich auf **Objekte**.
1. Klicken Sie unten im Fenster auf **Einstellung hinzufügen**.
1. Klicken Sie auf **Verbindung hinzufügen**.
2. Wählen Sie in der Dropdownliste **Verbindungstyp** die Art der Verbindung, die Sie erstellen möchten. Der Assistent zeigt die Eigenschaften für den gewählten Verbindungstyp an.
1. Schließen Sie den Assistenten ab, und aktivieren Sie das Kontrollkästchen, um die neue Verbindung zu speichern.


### So erstellen Sie eine neue Verbindung mit dem Azure-Vorschauportal

1. Klicken Sie in Ihrem Automation-Konto auf **Objekte**, um das Blatt **Objekte** zu öffnen.
1. Klicken Sie auf den Bereich **Verbindungen**, um das Blatt **Verbindungen** zu öffnen.
1. Klicken Sie oben im Blatt auf **Verbindung hinzufügen**.
2. Wählen Sie in der Dropdownliste **Typ** die Art der Verbindung, die Sie erstellen möchten. Das Formular zeigt die Eigenschaften für den gewählten Verbindungstyp an.
1. Geben Sie die erforderlichen Daten ein, und klicken Sie auf **Erstellen**, um die neue Verbindung zu speichern.



### So erstellen Sie eine neue Verbindung mit Windows PowerShell

Erstellen Sie mit dem Windows PowerShell-Cmdlet [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx) eine neue Verbindung. Dieses Cmdlet verfügt über den Parameter **ConnectionFieldValues**, der eine [Hashtabelle](http://technet.microsoft.com/library/hh847780.aspx) erwartet, mit deren Hilfe die Werte für jede der im Verbindungstyp definierten Eigenschaften festgelegt werden.


Die folgenden Beispielbefehle erstellen eine neue Verbindung für [Twilio](http://www.twilio.com), einem Telefoniedienst, der Ihnen das Senden und Empfangen von Textnachrichten ermöglicht. Ein Beispiel für ein Integrationsmodul, das einen Twilio-Verbindungstyp enthält, steht im [Script Center](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) zur Verfügung. Dieser Verbindungstyp definiert Eigenschaften für Konto-SID und Autorisierungstoken, die zum Prüfen Ihres Kontos bei der Verbindungsherstellung mit Twilio erforderlich sind. Sie müssen [dieses Modul herunterladen](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) und in Ihrem Automation-Konto installieren, damit der Beispielcode funktioniert.

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## Verwenden einer Verbindung in einem Runbook oder einer DSC-Konfiguration

Rufen Sie eine Verbindung in einem Runbook oder einer DSC-Konfiguration mit dem Cmdlet **Get-AutomationConnection** ab. Mithilfe dieser Aktivität werden die Werte der verschiedenen Felder in der Verbindung abgerufen und als [Hashtabelle](http://go.microsoft.com/fwlink/?LinkID=324844) zurückgegeben, die anschließend mithilfe geeigneter Befehle im Runbook oder in der DSC-Konfiguration verwendet werden kann.

### Beispiel für ein Textrunbook
Die folgenden Beispielbefehle zeigen, wie Sie die Twilio-Verbindung aus dem vorherigen Beispiel zum Senden einer Textnachricht aus einem Runbook verwenden. Die hier verwendete Send-TwilioSMS-Aktivität verfügt über zwei Parametersätze, die unterschiedliche Methoden für die Authentifizierung beim Twilio-Dienst verwenden. Eine Methode verwendet ein Verbindungsobjekt, die andere verwendet einzelne Parameter für Konto-SID und Autorisierungstoken. In diesem Beispiel werden beide Methoden gezeigt.

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### Beispiel für ein grafisches Runbook

Sie können einem grafischen Runbook eine **Get-AutomationConnection**-Aktivität hinzufügen, indem Sie im Bibliotheksbereich des grafischen Editors mit der rechten Maustaste auf die Verbindung klicken und **Zum Zeichenbereich hinzufügen** auswählen.

![](media/automation-connections/connection-add-canvas.png)

Die folgende Abbildung zeigt ein Beispiel für die Verwendung einer Verbindung in einem grafischen Runbook. Es handelt sich um das obige Beispiel der Verwendung von Twilio zum Senden einer Textnachricht aus einem Textrunbook. In diesem Beispiel wird der Parametersatz **UseConnectionObject** für die **Send-TwilioSMS**-Aktivität verwendet, die ein Verbindungsobjekt zur Authentifizierung beim Dienst nutzt. Es wird eine [Pipelineverknüpfung](automation-graphical-authoring-intro.md#links-and-workflow) verwendet, da der Connection-Parameter ein einzelnes Objekt erwartet.

Für den **To**-Parameter wird anstelle eines Constant-Werts ein PowerShell-Ausdruck verwendet, da dieser Parameter als Typ ein Zeichenfolgenarray erwartet, sodass Sie mehrere Zahlen senden können. Ein PowerShell-Ausdruck ermöglicht es Ihnen, einen einzelnen Wert oder ein Array bereitzustellen.

![](media/automation-connections/get-connection-object.png)

Die folgende Abbildung zeigt das obige Beispiel, verwendet jedoch den Parametersatz **SpecifyConnectionFields**, der anstelle der Verwendung eines Verbindungsobjekts für die Authentifizierung eine einzelne Festlegung der Parameter "AccountSid" und "AuthToken" erwartet. In diesem Fall werden die Felder der Verbindung und nicht das Objekt selbst angegeben.

![](media/automation-connections/get-connection-properties.png)



## Verwandte Artikel

- [Verknüpfungen bei der grafischen Erstellung](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=AcomDC_0128_2016-->
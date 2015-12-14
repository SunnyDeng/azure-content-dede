<properties 
pageTitle="Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services mit PowerShell" 
description="Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen mithilfe der PowerShell" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/17/2015" 
ms.author="saurabh"/>

# Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services mit PowerShell

>[AZURE.SELECTOR]
- [Azure classic portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird.

In diesem Artikel wird beschrieben, wie Sie Remotedesktop mithilfe von PowerShell für Cloud Services-Rollen aktivieren. Informationen zu den erforderlichen Komponenten für diesen Artikel finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md). PowerShell ermöglicht die Remotedesktoperweiterung, sodass Sie Remotedesktop auch nach der Bereitstellung der Anwendung aktivieren können.


## Konfigurieren von Remotedesktop über PowerShell

Mit dem Cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) können Sie Remotedesktop für bestimmte Rollen oder für alle Rollen Ihrer Clouddienstbereitstellung aktivieren. Das Cmdlet ermöglicht die Angabe des Benutzernamens und Kennworts für Remotedesktopbenutzer über den *Credential*-Parameter, der ein PSCredential-Objekt annimmt.

Wenn Sie PowerShell interaktiv verwenden, können Sie das PSCredential-Objekt durch Aufrufen des Cmdlets [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) auf einfache Weise festlegen.

```
	$remoteusercredentials = Get-Credential
```

Dadurch wird ein Dialogfeld angezeigt, in dem Sie den Benutzernamen und das Kennwort für einen Benutzer auf sichere Weise eingeben können.

Da PowerShell hauptsächlich für Automatisierungsszenarios verwendet wird, können Sie das PSCredential-Objekt auch so einrichten, dass keine Benutzerinteraktion erforderlich ist. Dazu müssen Sie zunächst ein sicheres Kennwort einrichten. Sie geben zunächst ein Klartextkennwort an und konvertieren es dann mit [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) in eine sichere Zeichenfolge. Als Nächstes müssen Sie diese sichere Zeichenfolge mit [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx) in eine verschlüsselte Standardzeichenfolge konvertieren. Anschließend können Sie diese verschlüsselte Standardzeichenfolge mit [Set-Content](https://technet.microsoft.com/library/ee176959.aspx) in einer Datei speichern. Beim Erstellen des PSCredential-Objekts können Sie diese Datei auslesen, um das Kennwort auf sichere Weise festzulegen, ohne es in einer Eingabeaufforderung angeben oder im Klartext speichern zu müssen.

Verwenden Sie das folgende PowerShell-Skript zum Erstellen einer sicheren Kennwortdatei:

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

Nachdem die Kennwortdatei (password.txt) erstellt wurde, verwenden Sie nur diese Datei und müssen das Kennwort nicht im Klartext angeben. Wenn Sie das Kennwort aktualisieren möchten, können Sie das oben aufgeführte PowerShell-Skript mit dem neuen Kennwort erneut ausführen und die Datei "password.txt" neu generieren.

>[AZURE.IMPORTANT]Stellen Sie beim Festlegen des Kennworts sicher, dass die [Komplexitätsvoraussetzungen](https://technet.microsoft.com/library/cc786468.aspx) erfüllt sind.

Zum Erstellen des Anmeldeinformationsobjekts aus der sicheren Kennwortdatei müssen Sie den Inhalt der Datei auslesen und mit [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) in eine sichere Zeichenfolge zurückkonvertieren. Neben den Anmeldeinformationen nimmt das Cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) auch den *Expiration*-Parameter an, der einen DateTime-Wert für den Zeitpunkt angibt, an dem das Benutzerkonto abläuft. Diesen Parameter definieren Sie, indem Sie einen statischen Wert für Datum und Uhrzeit angeben oder einfach festlegen, dass das Konto einige Tage nach dem aktuellen Datum abläuft.

Im folgenden PowerShell-Beispiel wird die Remotedesktoperweiterung für einen Clouddienst festgelegt:

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
Sie können optional auch den Bereitstellungsslot und die Bereitstellungsrollen angeben, für die Sie Remotedesktop aktivieren möchten. Wenn diese Parameter nicht angegeben werden, verwendet das Cmdlet standardmäßig den Produktionsbereitstellungsslot und aktiviert Remotedesktop für alle Rollen in der Produktionsbereitstellung.

Die Remotedesktoperweiterung ist immer einer Bereitstellung zugeordnet. Wenn Sie eine neue Bereitstellung für den Dienst erstellen, müssen Sie Remotedesktop für die neue Bereitstellung neu aktivieren. Wenn Remotedesktop immer für Ihre Bereitstellungen aktiviert sein soll, empfiehlt es sich, die PowerShell-Skripts zu integrieren, um Remotedesktop in Ihrem Bereitstellungsworkflow zu aktivieren.


## Remotedesktopverbindung mit einer Rolleninstanz
Mit dem Cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) können Sie eine Remotedesktopverbindung mit einer bestimmten Rolleninstanz Ihres Clouddiensts herstellen. Sie können den *LocalPath*-Parameter für das Cmdlet verwenden, um die RDP-Datei lokal herunterzuladen, oder den *Launch*-Parameter, um das Dialogfeld "Remotedesktopverbindung" für den Zugriff auf die Rolleninstanz des Clouddiensts direkt zu starten.

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## Überprüfen, ob die Remotedesktoperweiterung für einen Dienst aktiviert ist 
Das Cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) zeigt an, ob Remotedesktop für eine Dienstbereitstellung aktiviert ist. Das Cmdlet gibt den Benutzernamen des Remotedesktopbenutzers und die Rollen zurück, für die die Remotedesktoperweiterung aktiviert ist. Sie können optional einen Bereitstellungsslot angeben. Standardeinstellung ist der Produktionsbereitstellungsslot.

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## Entfernen der Remotedesktoperweiterung für einen Dienst 
Wenn Sie die Remotedesktoperweiterung für eine Bereitstellung bereits aktiviert haben und die Remotedesktopeinstellungen aktualisieren möchten, müssen Sie zunächst die Remotedesktoperweiterung entfernen und dann mit den neuen Einstellungen wieder aktivieren. Wenn Sie beispielsweise ein neues Kennwort für das Remotebenutzerkonto festlegen möchten oder wenn das Benutzerkonto abgelaufen ist, müssen Sie die Erweiterung entfernen und dann mit dem neuen Kennwort oder dem neuen Ablaufdatum wieder hinzufügen. Dies ist nur bei vorhandenen Bereitstellungen erforderlich, für die die Remotedesktoperweiterung aktiviert ist. Bei neuen Bereitstellungen können Sie dagegen die Erweiterung einfach direkt anwenden.

Zum Entfernen der Remotedesktoperweiterung aus einer Dienstbereitstellung können Sie das Cmdlet [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) verwenden. Sie können optional auch den Bereitstellungsslot und die Bereitstellungsrollen angeben, für die Sie die Remotedesktoperweiterung entfernen möchten.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE]Mit dem *UninstallConfiguration*-Parameter wird die gesamte Erweiterungskonfiguration deinstalliert, die auf den Dienst angewendet wurde. Die gesamte Erweiterungskonfiguration ist immer der Dienstkonfiguration zugeordnet. Um die Erweiterung mit einer Bereitstellung zu aktivieren, muss der Bereitstellung diese Erweiterungskonfiguration zugeordnet werden. Durch Aufrufen des Cmdlets "Remove" ohne den *UninstallConfiguration*-Parameter wird die Zuordnung der Bereitstellung zur Erweiterungskonfiguration aufgehoben und faktisch die Erweiterung aus der Bereitstellung entfernt. Die Erweiterungskonfiguration ist jedoch weiterhin dem Dienst zugeordnet. Um die Erweiterungskonfiguration vollständig zu entfernen, sollten Sie das Cmdlet "Remove" mit dem *UninstallConfiguration*-Parameter aufrufen.



## Zusätzliche Ressourcen

[Konfigurieren von Clouddiensten](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_1203_2015-->
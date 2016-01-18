<properties
	pageTitle="Festlegen von Kennwortablaufrichtlinien in Azure Active Directory | Microsoft Azure"
	description="Erfahren Sie, wie Sie Ablaufrichtlinien überprüfen und für einzelne oder mehrere Azure Active Directory-Kennwörter ablaufen ändern, wie sie ablaufen."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="curtand"/>


# Festlegen von Kennwortablaufrichtlinien in Azure Active Directory
> [AZURE.NOTE]Dieses Thema enthält Onlinehilfeinhalte für Clouddienste wie Microsoft Intune und Office 365, die auf Microsoft Azure Active Directory als Identitäts- und Verzeichnisdienste zurückgreifen.

Als globaler Administrator für einen Microsoft-Clouddienst können Sie mit dem Microsoft Azure Active Directory-Modul für Windows PowerShell festlegen, dass Benutzerkennwörter nicht ablaufen. Sie können auch Windows PowerShell-Cmdlets verwenden, um die Konfiguration zu entfernen, die angibt, dass Kennwörter nicht ablaufen, oder um anzuzeigen, welche Benutzerkennwörter nicht ablaufen.

  >[AZURE.NOTE]Nur Kennwörter für Benutzerkonten, die nicht über die Verzeichnissynchronisierung synchronisiert werden, kann so konfiguriert werden, dass sie nicht ablaufen. Weitere Informationen über die Verzeichnissynchronisierung finden Sie in der Liste der Themen unter [Fahrplan zur Verzeichnissynchronisierung](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Damit Sie Windows PowerShell-Cmdlets verwenden können, müssen Sie sie zunächst installieren.

## Was möchten Sie tun?

- [Überprüfen der Ablaufrichtlinie für ein Kennwort](#how-to-check-expiration-policy-for-a-password)

- [Festlegen, dass ein Kennwort abläuft](#set-a-password-to-expire)

- [Festlegen, dass ein Kennwort nicht abläuft](#set-a-password-not-to-expire)

## Überprüfen der Ablaufrichtlinie für ein Kennwort

1.  Stellen Sie mit den Anmeldeinformationen des Unternehmensadministrators eine Verbindung mit Windows PowerShell her.

2.  Führen Sie einen der folgenden Schritte aus:

	- Um festzustellen, ob für das Kennwort eines bestimmten Benutzers festgelegt ist, dass es nie abläuft, führen Sie das folgende Cmdlet mit dem Benutzerprinzipalnamen (UPN, z. B. aprilr@contoso.onmicrosoft.com) oder der Benutzer-ID des zu überprüfenden Benutzers aus: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

	- Um die Einstellung "Kennwort läuft nie ab" für alle Benutzer anzuzeigen, führen Sie das folgende Cmdlet aus: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## Festlegen, dass ein Kennwort abläuft

1.  Stellen Sie mit den Anmeldeinformationen des Unternehmensadministrators eine Verbindung mit Windows PowerShell her.

2.  Führen Sie einen der folgenden Schritte aus:

	- Um für das Kennwort eines Benutzers festzulegen, dass es abläuft, führen Sie das folgende Cmdlet mit dem Benutzerprinzipalnamen (UPN) oder der Benutzer-ID des Benutzers aus: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$false`

	- Um für die Kennwörter aller Benutzer in der Organisation festzulegen, dass sie ablaufen, verwenden Sie das folgende Cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$false`

## Festlegen, dass ein Kennwort nicht abläuft

1. Stellen Sie mit den Anmeldeinformationen des Unternehmensadministrators eine Verbindung mit Windows PowerShell her.

2.  Führen Sie einen der folgenden Schritte aus:

	- Um für das Kennwort eines Benutzers festzulegen, dass es nie abläuft, führen Sie das folgende Cmdlet mit dem Benutzerprinzipalnamen (UPN) oder der Benutzer-ID des Benutzers aus: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$true`

	- Um für die Kennwörter aller Benutzer in einer Organisation festzulegen, dass sie nie ablaufen, führen Sie das folgende Cmdlet aus: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$true`

<!---HONumber=AcomDC_0107_2016-->
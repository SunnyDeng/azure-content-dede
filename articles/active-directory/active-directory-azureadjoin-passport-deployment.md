<properties
	pageTitle="Microsoft Passport for Work im Unternehmen aktivieren | Microsoft Azure"
	description="Bereitstellungsanweisungen zum Aktivieren von Microsoft Passport in Ihrer Organisation."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="femila"/>


# Microsoft Passport for Work im Unternehmen aktivieren

Nach der Verknüpfung von in die Domäne eingebundenen Windows 10-Geräten mit Azure Active Directory (Azure AD) gehen Sie wie folgt vor, um Microsoft Passport for Work im Unternehmen zu aktivieren.

## Bereitstellen von System Center Configuration Manager, Version 1509 für Technical Preview
Für das Bereitstellen von Benutzerzertifikaten anhand von Microsoft Passport-Schüsseln sind folgende Elemente erforderlich:

- **System Center Configuration Manager, Version 1509 für Technical Preview** Weitere Informationen erhalten Sie unter [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) und [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx) (System Center Configuration Manager: Team-Blog).
- **Public Key-Infrastruktur (PKI):** Für das Aktivieren von Microsoft Passport for Work mit Benutzerzertifikaten ist eine PKI erforderlich. Wenn diese nicht vorhanden ist oder Sie sie nicht für Benutzerzertifikate verwenden möchten, können Sie folgendermaßen vorgehen:
 - **Bereitstellen eines Domänencontrollers:** Stellen Sie einen neuen Domänencontroller mit Windows Server 2016 (Build 10551 oder höher) bereit, und befolgen Sie die Schritte zum [Installieren eines Replikatdomänencontrollers in einer vorhandenen Domäne](https://technet.microsoft.com/library/jj574134.aspx) bzw. zum [Installieren einer neuen Active Directory-Gesamtstruktur beim Erstellen einer neuen Umgebung](https://technet.microsoft.com/library/jj574166). (Die ISO-Dateien stehen unter [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true) zum Download bereit.)

## Konfigurieren von Microsoft Passport for Work über Gruppenrichtlinien in Active Directory

 Mithilfe von Active Directory-Gruppenrichtlinien unter Windows Server lassen sich in die Domäne eingebundene Windows 10-Geräte so konfigurieren, dass bei der Windows-Anmeldung eines Benutzers Microsoft Passport-Anmeldeinformationen bereitgestellt werden:

1. 	Öffnen Sie den Server-Manager, und navigieren Sie zu **Extras** > **Gruppenrichtlinienverwaltung**.
2.	Navigieren Sie von der Gruppenrichtlinienverwaltung zu dem Domänenknoten, der der Domäne entspricht, in der Sie Azure AD Join aktivieren möchten.
3.	Klicken Sie mit der rechten Maustaste auf Ihr **Gruppenrichtlinienobjekt**, und wählen Sie dann **Neu** aus. Geben Sie Ihrem Gruppenrichtlinienobjekt einen Namen, z. B. „Microsoft Passport aktivieren“. Klicken Sie auf **OK**.
4.	Klicken Sie mit der rechten Maustaste auf Ihr neues Gruppenrichtlinienobjekt, und wählen Sie dann **Bearbeiten** aus.
5.	Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Passport for Work**.
6.	Klicken Sie mit der rechten Maustaste auf **Passport for Work aktivieren**, und wählen Sie dann **Bearbeiten** aus.
7.	Aktivieren Sie das Optionsfeld **Aktiviert**, und klicken Sie dann auf **Übernehmen**. Klicken Sie auf **OK**.
8.	Sie können das Gruppenrichtlinienobjekt jetzt mit einem Speicherort Ihrer Wahl verknüpfen. Um diese Richtlinie für alle in die Domäne eingebundenen Windows 10-Geräte in Ihrer Organisation zu aktivieren, verknüpfen Sie die Gruppenrichtlinie mit der Domäne. Beispiel:
 - Eine bestimmte Organisationseinheit in Active Directory, in der in die Domäne eingebundene Windows 10-Computer platziert werden
 - Eine bestimmte Sicherheitsgruppe mit in die Domäne eingebundenen Windows 10-Computern, die automatisch bei Azure AD registriert werden

## Konfigurieren von Microsoft Passport for Work mithilfe von PowerShell über Configuration Manager

Führen Sie den folgenden PowerShell-Befehl aus:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Konfigurieren des Zertifikatprofils zum Verwenden des Registrierungszertifikats „Passport for Work“ im Configuration Manager
Konfigurieren Sie für das Verwenden der zertifikatbasierten Passport for Work-Anmeldung/Microsoft Hello das Zertifikatprofil (**Assets & Compliance** -> **Kompatibilitätseinstellungen** -> **Company Resource Access** -> **Zertifikatprofile**). Wählen Sie eine Vorlage mit Smartcard-Anmeldung und erweiterter Schlüsselverwendung (Extended Key Usage, EKU) aus.

## Einrichten einer geplante Aufgabe zum Anfordern der Zertifikatauswertung
Die geplante Aufgabe ist eine kurzfristige Lösung. Der Administrator muss dazu eine geplante Aufgabe erstellen, die auf das Erstellen eines Passport for Work-Containers lauscht und eine Zertifikatauswertung anfordert. Der geplante Task wird ausgelöst, wenn der Passport for Work-Container aktiviert wird. Durch den Task wird die Verzögerung bei der Einrichtung von Container und PIN und ihre Verfügbarkeit für die Verwendung bei der nächsten Anmeldung reduziert.

**Um die geplante Aufgabe zu erstellen, können Sie die Benutzeroberfläche oder den folgenden Befehl verwenden:**

    schtasks /create /xml %0\..<EnrollCertificate.xml> /tn <Task Name>

Hier der Beispiel-XML-Code:

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

 Dabei entspricht die Bereichs-ID „ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd“ der ID für das Zertifikatprofil, das im Schritt „Konfigurieren des Zertifikatprofils zum Verwenden des Registrierungszertifikats ‚Passport for Work‘ im Configuration Manager“ erstellt wurde. Auf die Bereichs-ID folgt die Version: 8.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0309_2016-->
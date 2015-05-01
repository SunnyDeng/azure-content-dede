<properties 
   pageTitle="Ausführende Operations Manager-Konten für Operational Insights"
   description="Erfahren Sie mehr über die Konfiguration von ausführenden Operations Manager-Konten, die mit Operational Insights verwendet werden"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Ausführende Operations Manager-Konten für Operational Insights

Microsoft Azure Operational Insights verwendet den Operations Manager-Agent und die Verwaltungsgruppe zum Erfassen und Senden von Daten an den Operational Insights-Dienst. Operational Insights beruht auf Management Packs für Arbeitsauslastungen, um Dienste bereitzustellen, die einen Mehrwert schaffen. Jede Arbeitsauslastung erfordert spezifische Berechtigungen zum Ausführen von Management Packs in einem anderen Sicherheitskontext, z. B. ein Domänenkonto. Sie müssen Anmeldeinformationen angeben, indem Sie ein ausführendes Operations Manager-Konto konfigurieren.

In den folgenden Abschnitten wird beschrieben, wie ausführende Operations Manager-Konten für die folgenden Arbeitsauslastungen festgelegt werden:

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

## Festlegen des ausführenden Kontos für SQL Assessment

 Wenn Sie bereits das SQL Server Management Pack verwenden, sollten Sie das entsprechende ausführende Konto verwenden.

### So konfigurieren Sie das ausführende SQL-Konto in der Betriebskonsole

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie **Ausführen als-Profil für Operational Insights SQL Assessment**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für SQL Server enthält, oder klicken Sie auf **Neu**, um eines zu erstellen.
	>[AZURE.NOTE] Der Typ des ausführenden Kontos muss "Windows" sein. Das ausführende Konto muss auch Teil der lokalen Administratorengruppe auf allen Windows-Servern sein, auf denen SQL Server-Instanzen gehostet werden.

5. Klicken Sie auf **Speichern**.

6. Ändern und führen Sie dann das folgende T-SQL-Beispiel auf jeder SQL Server-Instanz aus, um dem ausführenden Konto die erforderlichen Mindestberechtigungen zum Ausführen von SQL Assessment zu erteilen. Dies ist jedoch nicht erforderlich, wenn ein ausführendes Konto bereits Teil der Serverrolle "sysadmin" auf SQL Server-Instanzen ist.

---
    -- Ersetzen Sie <Benutzername> durch den jeweiligen Benutzernamen, der als ausführendes Konto verwendet wird.
    USE master
    
    -- Erstellen Sie den Anmeldenamen für den Benutzer. Kommentieren Sie diese Codezeile aus, wenn die Anmeldung bereits erstellt wurde.
    CREATE LOGIN [<Benutzername>] FROM WINDOWS
    
    -- Erteilen Sie dem Benutzer Berechtigungen.
    GRANT VIEW SERVER STATE TO [<Benutzername>]
    GRANT VIEW ANY DEFINITION TO [<Benutzername>]
    GRANT VIEW ANY DATABASE TO [<Benutzername>]
    
    -- Fügen Sie Datenbankbenutzer für alle Datenbanken auf der SQL Server-Instanz hinzu. Dies ist erforderlich für die Verbindung mit einzelnen Datenbanken.
    -- HINWEIS: Dieser Befehl muss jedes Mal ausgeführt werden, wenn SQL Server-Instanzen neue Datenbanken hinzugefügt werden.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<Benutzername>] FOR LOGIN [<Benutzername>];'

### So konfigurieren Sie das ausführende SQL-Konto mithilfe von Windows PowerShell

Öffnen Sie ein PowerShell-Fenster, und führen Sie das folgende Skript aus, nachdem Sie es mit Ihren Informationen aktualisiert haben:

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## Festlegen des ausführenden Kontos für Virtual Machine Manager

Stellen Sie sicher, dass das ausführende Konto Berechtigungen für die folgenden Aktionen besitzt:

- Verwenden des VMM Windows PowerShell-Moduls

- Abfragen der VMM-Datenbank

- Remoteverwaltung der VMM-Agents, die auf Virtualisierungshosts ausgeführt werden

Führen Sie die folgenden Schritte aus, um das Konto festzulegen, wenn Sie Operational Insights mit Operations Manager verbinden.

### So legen Sie Anmeldeinformationen für VMM fest

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie **Ausführen als-Konto für Operational Insights VMM**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für VMM enthält, oder klicken Sie auf **Neu**, um eines zu erstellen.
	>[AZURE.NOTE] Der Typ des ausführenden Kontos muss "Windows" sein.

5. Klicken Sie auf **Speichern**.


## Festlegen des ausführenden Kontos für Lync Server

 Das ausführende Konto muss ein Mitglied sowohl der lokalen Administratorengruppe als auch der Lync-Sicherheitsgruppe RTCUniversalUserAdmins sein.

### So legen Sie die Anmeldeinformationen für ein Lync-Konto fest

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie **Ausführen als-Konto für Operational Insights Lync**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Konto aus, das sowohl ein Mitglied der lokalen Administratorengruppe als auch der Lync-Sicherheitsgruppe RTCUniversalUserAdmins ist. 
	>[AZURE.NOTE] Der Typ des ausführenden Kontos muss "Windows" sein.

5. Klicken Sie auf **Speichern**.


## Festlegen des ausführenden Kontos für SharePoint


### So legen Sie die Anmeldeinformationen für ein SharePoint-Konto fest

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie **Ausführen als-Konto für Operational Insights SharePoint**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für SharePoint enthält, oder klicken Sie auf **Neu**, um eines zu erstellen. 
	>[AZURE.NOTE] Der Typ des ausführenden Kontos muss "Windows" sein.

5. Klicken Sie auf **Speichern**.



<!--HONumber=52-->
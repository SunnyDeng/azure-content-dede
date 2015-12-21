<properties 
pageTitle="Häufige Startaufgaben für Clouddienste | Microsoft Azure" 
description="Enthält einige Beispiele für häufige Startaufgaben, die Sie vielleicht in der Web- oder Workerrolle des Clouddiensts ausführen möchten." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="12/07/2015" 
ms.author="adegeo"/>

# Allgemeine Startaufgaben für Clouddienste

Dieser Artikel enthält einige Beispiele für häufiger ausgeführte Startaufgaben, die Sie vielleicht im Clouddienst ausführen möchten. Mit Startaufgaben können Sie Vorgänge ausführen, bevor eine Rolle gestartet wird. Zu den Vorgängen, die Sie vielleicht ausführen möchten, gehören das Installieren von Komponenten, das Registrieren von COM-Komponenten, das Festlegen von Registrierungsschlüsseln und das Starten eines lang andauernden Prozesses.

In [diesem Artikel](cloud-services-startup-tasks.md) erfahren Sie etwas über die Funktionsweise von Startaufgaben und insbesondere darüber, wie die Einträge zum Definieren einer Startaufgabe erstellt werden.

Viele der hier verwendeten Aufgaben verwenden die

>[AZURE.NOTE]Startaufgaben gelten nicht für virtuelle Computer, sondern nur für Web- und Workerrollen von Clouddiensten.


## Definieren von Umgebungsvariablen vor dem Starten einer Rolle

Sie können Umgebungsvariablen für eine komplette Rolle definieren, indem Sie der Rollendefinition in der Dienstdefinitionsdatei das [Runtime]-Element hinzufügen.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

Wie Sie Umgebungsvariablen für eine bestimmte Aufgabe benötigen, die nicht von anderen Aufgaben freigegeben ist, können Sie das [Environment]-Element innerhalb des [Task]-Elements verwenden.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Variablen können auch einen [gültigen Azure XPath-Wert](https://msdn.microsoft.com/library/azure/hh404006.aspx) verwenden, um auf etwas für die Bereitstellung zu verweisen. Anstatt das `value`-Attribut zu verwenden, definieren Sie ein untergeordnetes [RoleInstanceValue]-Element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Konfigurieren des IIS-Startvorgangs mit "AppCmd.exe"

Das Befehlszeilentool [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) kann zum Verwalten von IIS-Einstellungen beim Start von Azure verwendet werden. *AppCmd.exe* bietet einen praktischen Zugriff über die Befehlszeile auf Konfigurationseinstellungen in den Azure-Startaufgaben. Mit *AppCmd.exe* können Einstellungen von Websites für Anwendungen und Websites hinzugefügt, geändert oder entfernt werden.

Es gibt jedoch einige Dinge, die bei der Verwendung von *AppCmd.exe* als Startaufgabe zu beachten sind:

- Startaufgaben können zwischen Neustarts mehrmals ausgeführt werden. Dies kann beispielsweise vorkommen, wenn die Rolle zyklisch ausgeführt wird.
- Einige Aktionen von *AppCmd.exe* können Fehler generieren, wenn sie mehr als einmal ausgeführt werden. Bei dem Versuch, *Web.config* zweimal einen Abschnitt hinzuzufügen, könnte ein Fehler generiert werden.
- Startaufgaben schlagen fehl, wenn sie einen von Null abweichenden Exitcode oder **errorlevel** zurückgeben. Dies kann auftreten, wenn *AppCmd.exe* einen Fehler erzeugt.

Aus den genannten Gründen ist es häufig ratsam, den **errorlevel** nach dem Aufruf von *AppCmd.exe* zu prüfen, was einfach ist, wenn Sie den Aufruf von *AppCmd.exe* in eine *CMD*-Datei einbetten. Wenn Sie eine bekannte **errorlevel**-Antwort erkennen, können Sie diese ignorieren oder zurückgeben. Dies wird im folgenden Beispiel veranschaulicht.

Die von *AppCmd.exe* zurückgegeben Errorlevel sind in der Datei "Winerror.h" aufgeführt und können auch auf [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx) nachgelesen werden.

### Beispiel

In diesem Beispiel wird ein Komprimierungsabschnitt und ein Komprimierungseintrag für JSON mit Fehlerbehandlung und-Protokollierung in der Datei *Web.config* hinzugefügt.

Die relevanten Abschnitte der Datei [ServiceDefinition.csdef] sind hier gezeigt, insbesondere das Festlegen des [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task)-Attributs auf `elevated`, um *AppCmd.exe* ausreichende Berechtigungen zum Ändern der Einstellungen in der Datei *Web.config* zu gewähren:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Die Batchdatei "Startup.cmd" verwendet "AppCmd.exe", um in der Datei *Web.config* einen Komprimierungsabschnitt und einen Komprimierungseintrag für JSON hinzuzufügen. Der erwartete **errorlevel** 183 wird mithilfe des Befehlszeilenprogramms VERIFY.EXE auf Null (0) festgelegt. Unerwartete Errorlevel werden in "StartupErrorLog.txt" protokolliert.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## Hinzufügen von Firewallregeln

In Azure sind tatsächlich zwei Firewalls vorhanden. Die erste Firewall kontrolliert Verbindungen zwischen dem virtuellen Computer und der Außenwelt. Dies wird durch das [EndPoints]-Element in der Datei [ServiceDefinition.csdef] gesteuert.

Die zweite Firewall kontrolliert Verbindungen zwischen einem virtuellen Computer und den Prozessen innerhalb dieses virtuellen Computers. Dies wird durch das Befehlszeilentool `netsh advfirewall firewall` gesteuert und bildet den Schwerpunkt dieses Artikels.

Azure erstellt Firewallregeln für die in den Rollen gestarteten Prozesse. Wenn Sie beispielsweise einen Dienst oder ein Programm starten, erstellt Azure automatisch die erforderlichen Firewallregeln, die diesem Dienst das Kommunizieren mit dem Internet ermöglichen. Wenn Sie einen Dienst erstellen, der durch einen anderen Prozess außerhalb der Rolle gestartet wird (z. B. ein COM+-Dienst oder ein Programm, das über die Windows-Aufgabenplanung gestartet wird), müssen Sie manuell eine Firewallregel für den Zugriff auf diesen Dienst erstellen. Diese Firewallregeln können unter Verwendung einer Startaufgabe erstellt werden.

Eine Startaufgabe, die eine Firewallregel erstellt, muss einen [executionContext][Task] mit der Einstellung **elevated** besitzen. Fügen Sie folgende Startaufgabe der Datei [ServiceDefinition.csdef] hinzu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Um die Firewallregel hinzuzufügen, müssen Sie die entsprechenden `netsh advfirewall firewall`-Befehle in der Startbatchdatei verwenden. In diesem Beispiel erfordert die Startaufgabe Sicherheit und Verschlüsselung für TCP-Port 80.

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## Blockieren bestimmter IP-Adressen

Sie können einen Azure-Webrollenzugriff auf einen Satz angegebener IP-Adressen beschränken, indem Sie die IIS-Datei **web.config** ändern und eine Befehlsdatei erstellen, die den Abschnitt **ipSecurity** der Datei **ApplicationHost.config** entsperrt.

Erstellen Sie zunächst eine Befehlsdatei, die ausgeführt wird, wenn die Rolle gestartet wird, und den Abschnitt **ipSecurity** der Datei **ApplicationHost.config** entsperrt. Erstellen Sie einen neuen Ordner auf der Stammebene der Webrolle mit dem Namen **startup**, und erstellen Sie in diesem Ordner die Batchdatei **startup.cmd**. Legen Sie die Eigenschaften dieser Datei auf **Immer kopieren** fest, um sicherzustellen, dass sie bereitgestellt wird.

Fügen Sie folgende Startaufgabe in der Datei [ServiceDefinition.csdef] hinzu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Fügen Sie diesen Befehl in der Datei **startup.cmd** hinzu:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

Dadurch wird die Batchdatei **startup.cmd** bei jeder Initialisierung der Webrolle ausgeführt, sodass der erforderliche **ipSecurity**-Abschnitt entsperrt wird.

Ändern Sie abschließend den Abschnitt [system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) in der Datei **web.config** Ihrer Webrolle, um eine Liste der IP-Adressen hinzuzufügen, denen Zugriff gewährt wird, wie im folgenden Beispiel gezeigt:

Diese Beispielkonfiguration **erlaubt** allen IP-Adressen Zugriff auf den Server, mit Ausnahme der beiden definierten.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Diese Beispielkonfiguration **verweigert** allen IP-Adressen den Zugriff auf den Server, mit Ausnahme der beiden definierten.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## Erstellen einer PowerShell-Startaufgabe

Windows PowerShell-Skripts können nicht direkt aus der Datei [ServiceDefinition.csdef] aufgerufen werden, aber sie können aus einer Startbatchdatei aus aufgerufen werden.

PowerShell führt standardmäßig keine nicht signierten Skripts aus. Wenn Sie die Skripts nicht signieren, müssen Sie Windows PowerShell zum Ausführen von nicht signierten Skripts konfigurieren. Zum Ausführen von nicht signierten Skripts muss **ExecutionPolicy** auf **Unrestricted** festgelegt werden. Die verwendete **ExecutionPolicy**-Einstellung basiert auf der Version von Windows PowerShell.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


Wenn Sie ein Gastbetriebssystem mit PowerShell 2.0 oder 1.0 verwenden, können Sie die Ausführung von Version 2 erzwingen und bei Nichtverfügbarkeit Version 1 verwenden.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## Erstellen von Dateien im lokalen Speicher über eine Startaufgabe

Sie können eine lokale Speicherressource zum Speichern von Dateien verwenden, die durch die Startaufgabe erstellt werden und auf die später von der Anwendung aus zugegriffen wird.

Zum Erstellen der lokalen Speicherressource fügen Sie in der Datei [ServiceDefinition.csdef] einen [LocalResources]-Abschnitt und dann das untergeordnete [LocalStorage]-Element hinzu. Geben Sie der lokalen Speicherressource einen eindeutigen Namen und eine für die Startaufgabe angemessene Größe.

Um eine lokale Speicherressource in der Startaufgabe zu verwenden, müssen Sie eine Umgebungsvariable erstellen, um auf den Speicherort der lokalen Ressource zu verweisen. Dann können die Startaufgabe und die Anwendung Schreib-Lese-Vorgänge auf Dateien in der lokalen Speicherressource ausführen.

Die relevanten Abschnitte in der Datei **ServiceDefinition.csdef** werden hier gezeigt:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Beispielsweise verwendet diese Batchdatei **Startup.cmd** die Umgebungsvariable **PathToStartupStorage** zum Erstellen der Datei **MyTest.txt** am lokalen Speicherort.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

Sie können mithilfe der [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx)-Methode aus dem Azure-SDK auf den lokalen Speicher zugreifen. Anschließend können Sie standardmäßige Lese- und Schreibvorgänge zum Lesen und Schreiben des Inhalts der lokalen Speicherressource verwenden.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## Unterschiede zwischen der Ausführung im Emulator und in der Cloud

Sie können mit der Startaufgabe andere Schritte ausführen, wenn sie in der Cloud ausgeführt wird, als wenn sie im Serveremulator ausgeführt wird. Beispiel: Sie möchten eine neue Kopie der SQL-Daten nur bei Ausführung im Emulator verwenden. Oder Sie benötigen eine bestimmte Leistungsoptimierung für die Cloud, die im Emulator nicht benötigt wird.

Diese Möglichkeit, unterschiedliche Aktionen auf dem Serveremulator und in der Cloud auszuführen, kann durch das Erstellen einer Umgebungsvariable in der Datei [ServiceDefinition.csdef] und das anschließende Testen der Umgebungsvariable in der Startaufgabe erzielt werden.

Zum Erstellen der Umgebungsvariable fügen Sie das [Variable]/[RoleInstanceValue]-Element hinzu und legen als XPath-Wert `/RoleEnvironment/Deployment/@emulated` fest. Der Wert der Umgebungsvariable **%ComputeEmulatorRunning%** ist bei der Ausführung im Serveremulator `"true"` und bei der Ausführung in der Cloud `"false"`.


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

Nun kann jede Aufgabe die Umgebungsvariable **%ComputeEmulatorRunning%** zur Ausführung unterschiedlicher Aktionen nutzen, je nachdem, ob die Rolle in der Cloud oder im Emulator ausgeführt wird. Dies ist ein CMD-Shell-Skript, das diese Umgebungsvariable überprüft.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## Ermitteln, ob die Aufgabe bereits ausgeführt wurde

Die Rolle kann zyklisch ausgeführt werden, ohne einen Neustart zu bewirken, wodurch die Startaufgaben erneut ausgeführt werden. Es gibt kein Flag, das anzeigt, ob eine Aufgabe bereits auf dem Host des virtuellen Computers ausgeführt wurde. Bei einigen Aufgaben spielt es sicher keine Rolle, ob sie mehrere Male ausgeführt werden. Jedoch können Situationen auftreten, in denen die mehrmalige Ausführung einer Aufgabe verhindert werden muss.

Die einfachste Methode, um zu erkennen, dass eine Aufgabe bereits ausgeführt wurde, ist das Erstellen einer Datei im **%TEMP%**-Ordner, wenn die Aufgabe erfolgreich ist, um dann zu Beginn der Aufgabe zu prüfen, ob die Datei bereits angelegt wurde. Dies ist ein Beispiel CMD-Shell-Skript, das dieses Verfahren umsetzt.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0


## Bewährte Methoden für Aufgaben
Hier sind einige bewährten Methoden, die Sie beim Konfigurieren einer Aufgabe für Ihre Web- oder Workerrolle befolgen sollten.

### Protokollieren Sie stets alle Startaktivitäten

Visual Studio bietet keinen Debugger zum schrittweisen Durchlaufen von Batchdateien, daher ist es ratsam, möglichst viele Daten über die Ausführung der Batchdateien zu erhalten. Beim Protokollieren der Ausgabe von Batchdateien können sowohl **stdout** als auch **stderr** wichtige Informationen beim Debuggen und Korrigieren von Batchdateien liefern. Um sowohl **stdout** als auch**stderr** in der Datei "StartupLog.txt" in dem Verzeichnis zu protokollieren, auf das die **%TEMP%**-Umgebungsvariable verweist, fügen Sie den Text `>>  "%TEMP%\\StartupLog.txt" 2>&1` am Ende der Zeilen ein, die Sie protokollieren möchten. So führen Sie z. B. "setup.exe" im **%PathToApp1Install%**-Verzeichnis aus

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Falls Sie die gesamte Ausgabe der Startaufgabe protokollieren möchten, ohne `>> "%TEMP%\StartupLog.txt" 2>&1` am Ende jeder Zeile einzufügen, sind zwei Startbatchdateien erforderlich. Die erste Batchdatei ruft die zweite Batchdatei mit Umleitung auf, um alle Aktivitäten der zweiten Batchdatei zu protokollieren. Dies ist erforderlich, damit die richtige Umleitung erfolgt.

Im Folgenden wird gezeigt, wie die gesamte Ausgabe einer Startbatchdatei umgeleitet wird. In diesem Beispiel erstellt die Datei "ServerDefinition.csdef" eine Startaufgabe, die "Startup1.cmd" aufruft. "Startup1.cmd" ruft "Startup2.cmd" auf und leitet dabei die gesamte Ausgabe in "%TEMP%\\StartupLog.txt" um.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd:

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd:

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### Legen Sie einen geeigneten ExecutionContext für die Startaufgaben fest

Legen Sie geeignete Berechtigungen für die Startaufgabe fest. Manchmal müssen Startaufgaben mit erhöhten Rechten ausgeführt werden, selbst wenn die Rolle mit normalen Berechtigungen ausgeführt wird.

Das [executionContext][Task]-Attribut legt die Berechtigungsstufe der Startaufgabe fest. Der Wert `executionContext="limited"` bedeutet, dass die Startaufgabe die gleiche Berechtigungsstufe wie die Rolle erhält. Der Wert `executionContext="elevated"` bedeutet, dass die Startaufgabe über Administratorrechte verfügt, wodurch die Startaufgabe Administratoraufgaben ausführen kann, ohne dass Sie der Rolle Administratorrechte zuweisen müssen.

Ein Beispiel für eine Startaufgabe, die erhöhte Rechte erfordert, ist eine Startaufgabe, die **AppCmd.exe** zum Konfigurieren von IIS verwendet. **AppCmd.exe** erfordert `executionContext="elevated"`.

### Verwenden Sie einen geeigneten taskType

Das [taskType][Task]-Attribut bestimmt, wie die Startaufgabe ausgeführt wird. Es gibt drei Werte: **simple**, **background** und **foreground**. Die background- und foreground-Aufgaben werden asynchron gestartet. Die simple-Aufgaben werden dann synchron jeweils nacheinander ausgeführt.

Bei **simple**-Startaufgaben lässt sich die Reihenfolge, in der die Aufgaben abgearbeitet werden, durch die Reihenfolge der Aufgaben in der Datei "ServiceDefinition.csdef" festlegen. Wenn eine **simple**-Aufgabe mit einem von Null abweichenden Exitcode beendet wird, dann wird die Startprozedur beendet, und die Rolle wird nicht gestartet.

Der Unterschied zwischen **background**-Startaufgaben und **foreground**-Startaufgaben ist, dass bei **foreground**-Aufgaben die Rolle ausgeführt wird, bis die **foreground**-Aufgaben beendet wurden. Dies bedeutet auch, dass bei einem Aufhängen oder Abstürzen der **foreground**-Aufgabe die Rolle solange nicht erneut zyklisch ausgeführt werden kann, bis die **foreground**-Aufgabe zwangsweise beendet wird. Aus diesem Grund werden für asynchrone Startaufgaben **background**-Aufgaben empfohlen, sofern nicht speziell diese Funktion der **foreground**-Aufgabe benötigt wird.

### Beenden Sie Batchdateien mit "EXIT /B 0"

Die Rolle wird nur gestartet, wenn die **errorlevel**-Werte aller "simple"-Startaufgabe Null (0) sind. Nicht alle Programme legen den **errorlevel** (Exitcode) richtig fest, daher sollte die Batchdatei mit einem `EXIT /B 0` enden, wenn alles ordnungsgemäß ausgeführt wurde.

Ein Fehlen von `EXIT /B 0` am Ende einer Startbatchdatei ist eine häufige Ursache, wenn Rollen nicht gestartet werden.

### Gehen Sie von einer mehrfachen Ausführung der Startaufgaben aus

Nicht jede zyklische Ausführung einer Rolle bewirkt einen Neustart, aber bei jeder zyklischen Ausführungen einer Rolle werden alle Startaufgaben ausgeführt. Dies bedeutet, dass Startaufgaben mehrmals ohne Probleme zwischen Neustarts ausgeführt werden müssen. Dies wird [oben](#detect-that-your-task-has-already-run) ausführlich erläutert.

### Verwenden Sie lokalen Speicher für Dateien, auf die von der Rolle aus zugegriffen werden soll

Wenn während der Startaufgabe Dateien kopiert oder erstellt werden sollen, auf die dann von Ihrer Rolle aus zugegriffen werden muss, müssen diese Dateien im lokalen Speicher abgelegt werden. Lesen Sie dazu den [Abschnitt](#create-files-in-local-storage-from-a-startup-task) oben.

## Nächste Schritte

Lesen Sie [Clouddienstmodell und -paket](cloud-services-model-and-package.md).

Erfahren Sie mehr über die Funktionsweise von [Aufgaben](cloud-services-startup-tasks.md).

Führen Sie das [Erstellen und Bereitstellen](cloud-services-how-to-create-deploy-portal.md) Ihres Clouddienstpakets durch.


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=AcomDC_1210_2015-->
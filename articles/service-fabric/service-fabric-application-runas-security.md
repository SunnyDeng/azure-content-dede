<properties
   pageTitle="Grundlegendes zu RunAs-Sicherheitsrichtlinien für eine Service Fabric-Anwendung | Microsoft Azure"
   description="Eine Übersicht der Ausführung einer Service Fabric-Anwendung unter System- und lokalen Sicherheitskonten einschließlich dem „SetupEntryPoint“, an dem eine Anwendung verschiedene Aktionen mit bestimmten Berechtigungen ausführen muss, bevor sie gestartet wird. "
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/21/2015"
   ms.author="mfussell"/>

# RunAs: Ausführen einer Service Fabric-Anwendung mit verschiedenen Sicherheitsberechtigungen
Service Fabric bietet die Möglichkeit zum Schützen von Anwendungen, die im Cluster unter verschiedenen Benutzerkonten ausgeführt werden, was auch als „RunAs“ (Ausführen als) bezeichnet wird. Darüber hinaus werden auch die Ressourcen geschützt, die von Anwendungen mit dem Benutzerkonto genutzt werden, wie z. B. Dateien, Verzeichnisse und Zertifikate.

Standardmäßig werden Service Fabric-Anwendungen unter dem Konto ausgeführt, unter dem der Prozess „Fabric.exe“ ausgeführt wird. Darüber hinaus gibt es die Möglichkeit zur Ausführung von Anwendungen unter einem lokalen Benutzerkonto, das im Manifest der Anwendung angegeben wird. Unterstützte Kontotypen für RunAs sind **LocalUser**, **NetworkService**, **LocalService** und **LocalSystem**.

> [AZURE.NOTE]Domänenkonten werden in Windows Server-Bereitstellungen unterstützt, in denen Active Directory verfügbar ist.

Benutzergruppen können definiert und erstellt werden, in denen hinzugefügte Benutzer gemeinsam verwaltet werden können. Dies ist besonders nützlich, wenn es für verschiedene Diensteinstiegspunkte mehrere Benutzer gibt, die auf Gruppenebene bestimmte allgemeine Berechtigungen benötigen.

## Festlegen der RunAs-Richtlinie für den „SetupEntryPoint“

Wie im [Anwendungsmodell](service-fabric-application-model.md) beschrieben, ist **SetupEntryPoint** ein privilegierter Einstiegspunkt, der mit den gleichen Anmeldeinformationen wie Service Fabric (meist mit dem Konto *Network*) vor jedem anderen Einstiegspunkt ausgeführt wird. Die ausführbare Datei, die von **EntryPoint** angegeben wird, ist in der Regel der Diensthost mit langer Laufzeit. Durch Festlegen eines separaten „SetupEntryPoint“ wird daher vermieden, dass der Diensthost über längere Zeiträume mit erhöhten Rechten hinweg ausgeführt werden muss. Die von **EntryPoint** angegebene ausführbare Datei wird ausgeführt, nachdem **SetupEntryPoint** erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (und beginnt wieder mit **SetupEntryPoint**), sofern er beendet wird oder abstürzen sollte.

Nachstehend sehen Sie ein einfaches Beispiel eines Dienstmanifests mit dem „SetupEntryPoint“ und dem primären „EntryPoint“ des Diensts.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Konfigurieren der RunAs-Richtlinie

Nach der Konfiguration des Diensts mit einem „SetupEntryPoint“ können Sie im Anwendungsmanifest die Sicherheitsberechtigungen ändern, unter denen dieser ausgeführt wird. Das folgende Beispiel zeigt, wie Sie den Dienst so konfigurieren, dass dieser mit den Berechtigungen des Administratorkontos ausgeführt wird.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Erstellen Sie zunächst, den Abschnitt **Principals** mit einem Benutzernamen, wie z. B. „SetupAdminUser“. Dies bedeutet, dass der Benutzer Mitglied der Gruppe „Administratoren“ des Systems ist.

Konfigurieren Sie als Nächstes im Abschnitt **ServiceManifestImport** eine Richtlinie, gemäß der dieser Prinzipal für den **SetupEntryPoint** gilt. Dadurch wird Service Fabric informiert, dass die Datei „MySetup.bat“ als RunAs mit Administratorrechten ausgeführt werden soll. Angesichts der Tatsache, dass Sie dem primären Einstiegspunkt *keine* Richtlinie zugewiesen haben, wird der Code in „MyServiceHost.exe“ unter dem Konto „NetworkService“ ausgeführt. Dies ist das Standardkonto, unter dem alle Diensteinstiegspunkte als RunAs ausgeführt werden.

Lassen Sie uns nun die Datei „MySetup.bat“ zum Visual Studio-Projekt hinzufügen, um die Administratorrechte zu testen. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Dienstprojekt, und fügen Sie einen neuen Aufruf der Datei „MySetup.bat“ hinzu. Als Nächstes muss sichergestellt werden, dass diese Datei in das Dienstpaket einbezogen wird, was standardmäßig nicht der Fall ist. Um sicherzustellen, dass die Datei „MySetup.bat“ im Paket enthalten ist, wählen Sie die Datei aus. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, wählen Sie „Eigenschaften“ aus, und stellen Sie im Dialogfeld „Eigenschaften“ sicher, dass die **In Ausgabeverzeichnis kopieren** auf **Kopieren, wenn neuer** festgelegt ist. Dies wird im folgenden Screenshot gezeigt.

![Visual Studio – Batchdatei für „CopyToOutput“ für „SetupEntryPoint“][Image1]

Öffnen Sie nun die Datei „MySetup.bat“, und fügen Sie die folgenden Befehle hinzu.
~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment" /v TestVariable /f
~~~

Als Nächstes müssen Sie die Projektmappe erstellen und in einem lokalen Entwicklungscluster bereitstellen. Nachdem der Dienst gestartet wurde, können Sie im Service Fabric-Explorer erkennen, dass „MySetup.bat“ auf zwei Arten erfolgreich war. Öffnen Sie eine PowerShell-Eingabeaufforderung, und geben Sie Folgendes ein:
~~~
 [Environment]::GetEnvironmentVariable("TestVariable","Machine")
~~~
Like this
~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine") MyValue
~~~

Notieren Sie sich den Namen des Knotens, auf dem der Dienst im Service Fabric-Explorer bereitgestellt und gestartet wurde, z. B. Knoten 1, und navigieren Sie zum Arbeitsordner der Anwendungsinstanz, um die Datei „out.txt“ zu finden, die den Wert **TestVariable** zeigt. Wenn die Bereitstellung z. B. auf Knoten 2 erfolgt wäre, können Sie für „MyApplicationType“ zu diesem Pfad navigieren.

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  Starten von PowerShell-Befehlen aus „SetupEntryPoint“
Zum Ausführen von PowerShell aus **SetupEntryPoint** können Sie „PowerShell.exe“ in einer Batchdatei ausführen, die auf eine PowerShell-Datei verweist. Fügen Sie zuerst dem Dienstprojekt eine PowerShell-Datei hinzu, z. B. „MySetup.ps1“. Denken Sie daran, die Eigenschaft *Kopieren, wenn neuer* so festzulegen, dass diese Datei in das Dienstpaket einbezogen wird. Das folgende Beispiel zeigt eine einfache Batchdatei zum Starten einer PowerShell-Datei namens „MySetup.ps1“, die die Systemumgebungsvariable *TestVariable* festlegt.

MySetup.bat to launch PowerShell file.
~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\\MySetup.ps1"
~~~

Fügen Sie in der PowerShell-Datei Folgendes hinzu, um eine Systemumgebungsvariable festzulegen:
~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

## Anwenden der RunAs-Richtlinie auf Dienste 
In den vorangegangenen Schritten wurde erläutert, wie eine RunAs-Richtlinie auf einen „SetupEntryPoint“ angewendet wird. Nun wollen wir uns ein wenig näher anschauen, wie verschiedene Prinzipale erstellt werden, die als Dienstrichtlinien angewendet werden können.

### Erstellen lokaler Benutzergruppen
Benutzergruppen können definiert und erstellt werden, denen Benutzer hinzugefügt werden können. Dies ist besonders nützlich, wenn es für verschiedene Diensteinstiegspunkte mehrere Benutzer gibt, die auf Gruppenebene bestimmte allgemeine Berechtigungen benötigen. Das folgende Beispiel zeigt eine lokale Gruppe namens **LocalAdminGroup** mit Administratorrechten. Zwei Benutzer, „Customer1“ und „Customer2“, wurden dieser lokalen Gruppe hinzugefügt.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Erstellen lokaler Benutzer
Sie können einen lokalen Benutzer erstellen, der zum Schützen eines Diensts in der Anwendung dienen kann. Wenn ein Konto des Typs „LocalUser“ im Abschnitt „Principals“ des Anwendungsmanifests angegeben wird, erstellt Service Fabric lokale Benutzerkonten auf Computern, auf denen die Anwendung bereitgestellt ist. Diese Konten haben standardmäßig nicht denselben Namen, der im Anwendungsmanifest angegeben ist (z. B. „Customer3“ im nachstehenden Beispiel), sondern werden mit zufälligen Kennwörtern dynamisch generiert.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~
 
<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Zuweisen von Richtlinien zu den Dienstcodepaketen
Der Abschnitt **RunAsPolicy** für **ServiceManifestImport** gibt im Abschnitt „Principals“ das Konto an, das zum Ausführen eines Codepakets verwendet werden soll, und ordnet Codepakete aus dem Dienstmanifest Benutzerkonten im Abschnitt „Principals“ zu. Sie können dies für die Einstiegspunkte des Typs „Setup“ oder „Main“ angeben oder „All“ angeben, damit diese Einstellung für beide gilt. Das folgende Beispiel zeigt unterschiedliche angewendete Richtlinien.

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Wenn **EntryPointType** nicht angegeben ist, wird die Standardeinstellung auf „EntryPointType = Main“ festgelegt. Das Angeben von **SetupEntryPoint** ist besonders nützlich, wenn Sie einen Einrichtungsvorgang mit hohen erforderlichen Berechtigungen unter einem Systemkonto und den eigentlichen Dienstcode unter einem Konto mit niedrigeren Berechtigungen ausführen möchten.

### Anwenden einer Standardrichtlinie auf alle Dienstcodepakete
Der Abschnitt **DefaultRunAsPolicy** wird verwendet, um ein Standardbenutzerkonto für alle Codepakete anzugeben, für die keine bestimmte **RunAsPolicy** definiert ist. Wenn die meisten Codepakete, die in von einer Anwendung verwendeten Dienstmanifesten angegeben sind, unter demselben RunAs-Benutzerkonto ausgeführt werden müssen, müssen Sie lediglich eine RunAs-Standardrichtlinie mit diesem Benutzerkonto definieren, anstatt eine **RunAsPolicy** für jedes Codepaket anzugeben. Das folgende Beispiel gibt an, dass wenn für ein Codepaket keine **RunAsPolicy** angegeben ist, das Codepaket unter dem Standardkonto „MyDefaultAccount“ ausgeführt werden soll, das im Abschnitt „Principals“ angegeben ist.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Zuweisen von „SecurityAccessPolicy“ für HTTP- und HTTPS-Endpunkte
Wenn Sie eine RunAs-Richtlinie auf einen Dienst anwenden und im Dienstmanifest Endpunktressourcen mit dem HTTP-Protokoll deklariert sind, müssen Sie eine **SecurityAccessPolicy** angeben. Diese Richtlinie soll sicherstellen, dass es für Ports, die diesen Endpunkten zugeordnet sind, ordnungsgemäße Zugriffssteuerungslisten für das RunAs-Benutzerkonto gibt, unter dem der Dienst ausgeführt wird. Andernfalls hat HTTP.SYS keinen Zugriff auf den Dienst, sodass bei Aufrufen vom Client Fehler auftreten. Das nachstehende Beispiel wendet das Konto „Customer3“ auf den Endpunkt *ServiceEndpointName* an und gewährt ihm Vollzugriffsrechte.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Für den Endpunkt HTTPS müssen Sie zusätzlich den Namen des Zertifikats angeben, das an den Client mit einer **EndpointBindingPolicy** zurückgegeben wird. Das Zertifikat ist im Abschnitt „certificates“ im Anwendungsmanifest definiert.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Vollständiges Beispiel eines Anwendungsmanifests
Das folgende Anwendungsmanifest zeigt viele der zuvor beschriebenen Einstellungen.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <User Name="Customer1" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

* [Informationen zum Anwendungsmodell](service-fabric-application-model.md)
* [Angeben von Ressourcen in einem Dienstmanifest](service-fabric-service-manifest-resources.md)
* [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)

[Image1]: media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=Nov15_HO4-->
<properties 
    pageTitle="Was ist ein Clouddienstmodell, und wie kann es in Azure gepackt werden?" 
    description="Beschreibung des Clouddienstmodells (.csdef, .cscfg) und der Paketdatei (.cspkg) in Azure" 
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
    ms.date="07/06/2015" 
    ms.author="adegeo"/>

# Was ist das Clouddienstmodell, und wie kann es gepackt werden?
Ein Clouddienst wird aus drei Komponenten erstellt: aus der Dienstdefinition _(.csdef)_, der Dienstkonfiguration _(.cscfg)_ und einem Dienstpaket _(.cspkg)_. Die beiden Dateien **ServiceDefinition.csdef** und **ServiceConfig.cscfg** sind XML-basiert und beschreiben die Struktur und Konfiguration des Clouddiensts; zusammen werden sie als Modell bezeichnet. Die Datei **ServicePackage.cspkg** ist eine Zip-Datei, die aus der Datei **ServiceDefinition.csdef** generiert wird und unter anderem alle erforderlichen binärbasierten Abhängigkeiten enthält. In Azure wird ein Clouddienst aus der Datei **ServicePackage.cspkg** sowie der Datei **ServiceConfig.cscfg** erstellt.

Wenn der Clouddienst in Azure ausgeführt wird, können Sie ihn über die Datei **ServiceConfig.cscfg** neu konfigurieren, die Definition können Sie jedoch nicht ändern.

## Zu welchen Punkten möchten Sie mehr erfahren?

* Ich benötige weitere Informationen zu den Dateien [ServiceDefinition.csdef](#csdef) und [ServiceConfig.cscfg](#cscfg).
* Damit bin ich bereits vertraut, ich benötige [einige Beispiele](#next-steps) zur Konfiguration.
* Ich möchte die Datei [ServicePackage.cspkg](#cspkg) erstellen. 
* Ich verwende Visual Studio und möchte ...
    * [Erstellen eines Azure-Projekts mit Visual Studio][vs_create]
    * [Einen vorhandenen Clouddienst neu konfigurieren][vs_reconfigure]
    * [Ein Clouddienstprojekt bereitstellen][vs_deploy]
    * [Eine Remotedesktopverbindung für eine Clouddienstinstanz einrichten][remotedesktop]

<a name="csdef"></a>
## ServiceDefinition.csdef
Die Datei **ServiceDefinition.csdef** gibt die Einstellungen an, mit denen ein Clouddienst in Azure konfiguriert wird. Das [Azure-Dienstdefinitionsschema (CSDEF-Datei)](https://msdn.microsoft.com/library/azure/ee758711.aspx) gibt das zulässige Format für eine Dienstdefinitionsdatei an. Im folgenden Beispiel sind die Einstellungen dargestellt, die für die Web- und Workerrollen definiert werden können:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Ausführliche Informationen zu dem hier verwendeten XML-Schema finden Sie unter [Dienstdefinitionsschema][]. Im Folgenden werden jedoch einige Elemente kurz erläutert:

>**Sites** enthält die Definitionen für Websites oder Webanwendungen, die in IIS7 gehostet werden.
>
>**InputEndpoints** enthält die Definitionen für Endpunkte, die für Verbindungen mit dem Clouddienst verwendet werden.
>
>**InternalEndpoints** enthält die Definitionen für Endpunkte, die von Rolleninstanzen für die Kommunikation verwendet werden.
>
>**ConfigurationSettings** enthält die Einstellungsdefinitionen für Funktionen einer bestimmten Rolle.
>
>**Certificates** enthält die Definitionen für Zertifikate, die für eine Rolle erforderlich sind. Das Codebeispiel oben enthält ein Zertifikat, das für die Konfiguration von Azure Connect verwendet wird.
>
>**LocalResources** enthält die Definitionen für lokale Speicherressourcen. Eine lokale Speicherressource ist ein reserviertes Verzeichnis im Dateisystem des virtuellen Computers, in dem eine Instanz einer Rolle ausgeführt wird.
>
>**Imports** enthält die Definitionen für importierte Module. Im Codebeispiel oben werden die Module für die Remotedesktopverbindung und Azure Connect verwendet.
>
>**Startup** enthält Aufgaben, die beim Starten der Rolle ausgeführt werden. Die Aufgaben werden in einer CMD-Datei oder einer ausführbaren Datei definiert.



<a name="cscfg"></a>
## ServiceConfiguration.cscfg
Die Konfiguration der Einstellungen für den Clouddienst wird durch die Werte in der Datei **ServiceConfiguration.cscfg** festgelegt. Sie geben in dieser Datei die Anzahl der Instanzen an, die bereitgestellt werden sollen. Die Werte für die Konfigurationseinstellungen, die Sie in der Dienstdefinitionsdatei definiert haben, werden der Dienstkonfigurationsdatei hinzugefügt. Die Fingerabdrücke für alle Verwaltungszertifikate, die dem Clouddienst zugeordnet sind, werden der Datei ebenfalls hinzugefügt. Das [Azure-Dienstkonfigurationsschema (CSCFG-Datei)](https://msdn.microsoft.com/library/azure/ee758710.aspx) gibt das zulässige Format für eine Dienstkonfigurationsdatei an.

Die Dienstkonfigurationsdatei wird nicht mit der Anwendung gepackt, sondern als gesonderte Datei in Azure hochgeladen und zum Konfigurieren des Clouddiensts verwendet. Sie können eine neue Dienstkonfigurationsdatei hochladen, ohne den Clouddienst erneut bereitzustellen. Die Konfigurationswerte für den Clouddienst können geändert werden, während der Clouddienst ausgeführt wird. Im folgenden Beispiel sind die Konfigurationseinstellungen dargestellt, die für die Web- und Workerrollen definiert werden können:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Ausführliche Informationen zu dem hier verwendeten XML-Schema finden Sie unter [Dienstkonfigurationsschema](https://msdn.microsoft.com/library/azure/ee758710.aspx). Im Folgenden werden die Elemente jedoch kurz erläutert:

>**Instances** konfiguriert die Anzahl der ausgeführten Instanzen für die Rolle. Um zu verhindern, dass der Clouddienst während Aktualisierungen nicht verfügbar ist, wird empfohlen, dass Sie mehr als eine Instanz der Rollen mit Webzugriff bereitstellen. Auf diese Weise befolgen Sie die Richtlinien in der [Azure-Vereinbarung zum Servicelevel (SLA)](http://azure.microsoft.com/support/legal/sla/), in der eine Verfügbarkeit der externen Verbindungen von 99,95 % für Rollen mit Internetzugriff garantiert wird, wenn mindestens zwei Rolleninstanzen für einen Dienst bereitgestellt werden.

>**ConfigurationSettings** konfiguriert die Einstellungen für die ausgeführten Instanzen einer Rolle. Der Name der `<Setting>`-Elemente muss mit den Einstellungsdefinitionen in der Dienstdefinitionsdatei übereinstimmen.

>**Certificates** konfiguriert die Zertifikate, die vom Dienst verwendet werden. Im Codebeispiel oben wird das Zertifikat für das RemoteAccess-Modul definiert. Der Wert des *thumbprint*-Attributs muss auf den Fingerabdruck des zu verwendenden Zertifikats festgelegt werden.

<p/>

 >[AZURE.NOTE]Der Fingerabdruck des Zertifikats kann der Konfigurationsdatei mit einem Texteditor hinzugefügt oder in Visual Studio auf der Registerkarte **Zertifikate** auf der Seite **Eigenschaften** der Rolle eingefügt werden.



## Definieren von Ports für Rolleninstanzen
In Azure ist nur ein Einstiegspunkt für eine Webrolle zulässig. Das bedeutet, dass der gesamte Datenverkehr über eine IP-Adresse erfolgt. Sie können Ihre Websites für die gemeinsame Nutzung eines Ports konfigurieren, indem Sie den Hostheader so konfigurieren, dass Anforderungen an den richtigen Ort geleitet werden. Sie können Ihre Anwendungen zudem so konfigurieren, dass sie bekannte Ports in der IP-Adresse abhören.

Im folgenden Beispiel ist die Konfiguration für eine Webrolle mit einer Website und Webanwendung dargestellt. Die Website ist als Standardeinstiegspunkt auf Port 80 konfiguriert. Die Webanwendungen sind so konfiguriert, dass sie Anforderungen von einem alternativen Hostheader mit dem Namen "mail.mysite.cloudapp.net" empfangen.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## Ändern der Konfiguration einer Rolle
Sie können die Konfiguration des Clouddiensts aktualisieren, während er in Azure ausgeführt wird, ohne den Dienst offline zu schalten. Zum Ändern der Konfigurationsinformationen können Sie entweder eine neue Konfigurationsdatei hochladen oder die vorhandene Konfigurationsdatei bearbeiten und auf den ausgeführten Dienst anwenden. An der Konfiguration eines Diensts können die folgenden Änderungen vorgenommen werden:

- **Ändern der Werte von Konfigurationseinstellungen:** Wenn sich eine Konfigurationseinstellung ändert, kann eine Rolleninstanz entweder die Änderung anwenden, während die Instanz online ist, oder die Instanz ordnungsgemäß neu starten und die Änderung anwenden, während die Instanz offline geschaltet ist.

- **Ändern der Diensttopologie von Rolleninstanzen:** Topologieänderungen wirken sich nicht auf ausgeführte Instanzen aus, sofern keine Instanz entfernt wird. Alle verbleibenden Instanzen müssen im Allgemeinen nicht neu gestartet werden. Sie können jedoch festlegen, dass Rolleninstanzen als Reaktion auf eine Topologieänderung neu gestartet werden.

- **Ändern des Zertifikatfingerabdrucks:** Sie können ein Zertifikat nur aktualisieren, wenn eine Rolleninstanz offline ist. Wenn ein Zertifikat hinzugefügt, gelöscht oder geändert wird, während eine Rolleninstanz online ist, schaltet Azure die Instanz ordnungsgemäß offline, um das Zertifikat zu aktualisieren, und schaltet sie nach der Änderung wieder online.

### Behandeln von Konfigurationsänderungen mit Dienstlaufzeitereignissen
Die [Azure-Laufzeitbibliothek](https://msdn.microsoft.com/library/azure/dn511024.aspx) enthält den [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx)-Namespace, der Klassen für die Interaktion mit der Azure-Umgebung aus dem Code bereitstellt, der in einer Instanz einer Rolle ausgeführt wird. Die [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx)-Klasse definiert die folgenden Ereignisse, die vor und nach einer Konfigurationsänderung ausgelöst werden:

- **[Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)-Ereignis:** Dieses Ereignis wird ausgelöst, bevor die Konfigurationsänderung auf eine angegebene Instanz einer Rolle angewendet wird, sodass Sie die Möglichkeit haben, die Rolleninstanzen bei Bedarf offline zu schalten.
- **[Changed](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)-Ereignis:** Wird ausgelöst, nachdem die Konfigurationsänderung auf eine angegebene Instanz einer Rolle angewendet wurde.

> [AZURE.NOTE]Da die Instanzen einer Rolle bei Zertifikatänderungen immer offline geschaltet werden, werden die RoleEnvironment.Changing- oder RoleEnvironment.Changed-Ereignisse nicht ausgelöst.

<a name="cspkg"></a>
## ServicePackage.cspkg
Um eine Anwendung als Clouddienst in Azure bereitzustellen, müssen Sie zunächst die Anwendung im entsprechenden Format packen. Als Alternative zu Visual Studio können Sie das Befehlszeilentool **CSPack** (wird mit dem [Azure SDK](http://azure.microsoft.com/downloads/) installiert) verwenden, um die Paketdatei zu erstellen.

**CSPack** verwendet den Inhalt der Dienstdefinitionsdatei und Dienstkonfigurationsdatei, um den Inhalt des Pakets zu definieren. **CSPack** generiert eine Anwendungspaketdatei (.cspkg), die Sie über das [Azure-Verwaltungsportal](cloud-services-how-to-create-deploy/#how-to-deploy-a-cloud-service) in Azure hochladen können. Standardmäßig erhält das Paket den Namen `[ServiceDefinitionFileName].cspkg`, durch Verwendung der Option `/out` von **CSPack** können Sie jedoch auch einen anderen Namen angeben.

###### Speicherort des CSPack-Tools (unter Windows)
| SDK-Version | Path |
| ----------- | ---- |
| 1.7 und höher | C:\Programme\Microsoft SDKs\Azure\.NET SDK\[SDK-Version]\bin\ |
| Vor 1.6 | C:\Programme\Azure SDK\[SDK-Version]\bin\ |

>[AZURE.NOTE]"CSPack.exe" ist (unter Windows) durch Ausführen der Verknüpfung **Microsoft Azure-Eingabeaufforderung** verfügbar, die mit dem SDK installiert wird.
>  
>Führen Sie das Programm "CSPack.exe" aus, um die Dokumentation zu allen möglichen Schaltern und Befehlen anzuzeigen.

<p />

>[AZURE.TIP]Führen Sie den Clouddienst lokal im **Microsoft Azure-Serveremulator** aus, und verwenden Sie dabei die Option **/copyonly**. Mit dieser Option werden die Binärdateien für die Anwendung in ein Verzeichnislayout kopiert, aus dem sie im Serveremulator ausgeführt werden können.

### Beispielbefehl zum Packen eines Clouddiensts
Mit dem folgenden Codebeispiel wird ein Anwendungspaket erstellt, das die Informationen für eine Webrolle enthält. Mit dem Befehl werden die zu verwendende Dienstdefinitionsdatei, das Verzeichnis, in dem sich die Binärdateien befinden, und der Name der Paketdatei angegeben.

    cspack [DirectoryName][ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Wenn die Anwendung eine Webrolle und eine Workerrolle enthält, wird der folgende Befehl verwendet:

    cspack [DirectoryName][ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Die Variablen sind dabei wie folgt definiert:

| Variable | Wert |
| ------------------------- | ----- |
| [DirectoryName] | Das Unterverzeichnis unter dem Stammverzeichnis des Projekts, das die CSDEF-Datei des Azure-Projekts enthält.|
| [ServiceDefinition] | Der Name der Dienstdefinitionsdatei. Standardmäßig heißt diese Datei "ServiceDefinition.csdef". |
| [OutputFileName] | Der Name der generierten Paketdatei. In der Regel wird der Name der Anwendung verwendet. Wenn kein Dateiname angegeben wird, wird das Anwendungspaket unter dem Namen "[ApplicationName].cspkg" erstellt.|
| [RoleName] | Der Name der Rolle entsprechend der Definition in der Dienstdefinitionsdatei.|
| [RoleBinariesDirectory] | Der Speicherort der Binärdateien für die Rolle.|
| [VirtualPath] | Die physischen Verzeichnisse für die einzelnen virtuellen Pfade, die im Abschnitt "Sites" der Dienstdefinition definiert sind.|
| [PhysicalPath] | Die physischen Verzeichnisse des Inhalts für die einzelnen virtuellen Pfade, die im Knoten "Site" der Dienstdefinition definiert sind.|
| [RoleAssemblyName] | Der Name der Binärdatei für die Rolle.| 


## Nächste Schritte

Ich erstelle ein Clouddienstpaket und möchte ...

<!--
* [Configure Sizes for Cloud Services](!!!!!https://msdn.microsoft.com/library/azure/ee814754.aspx)  
* [Configure Local Storage Resources](!!!!!https://msdn.microsoft.com/library/azure/ee758708.aspx)
-->

* [Eine Remotedesktopverbindung für eine Clouddienstinstanz einrichten][remotedesktop]
* [Ein Clouddienstprojekt bereitstellen][deploy]

Ich verwende Visual Studio und möchte ...

* [Erstellen eines Azure-Projekts mit Visual Studio][vs_create]
* [Einen vorhandenen Clouddienst neu konfigurieren][vs_reconfigure]
* [Ein Clouddienstprojekt bereitstellen][vs_deploy]
* [Eine Remotedesktopverbindung für eine Clouddienstinstanz einrichten][vs_remote]


[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: https://msdn.microsoft.com/de-de/library/gg443832.aspx
[vs_deploy]: https://msdn.microsoft.com/de-de/library/ee460772.aspx
[vs_reconfigure]: https://msdn.microsoft.com/library/ee405486.aspx
[vs_create]: https://msdn.microsoft.com/de-de/library/ee405487.aspx

<!---HONumber=July15_HO4-->
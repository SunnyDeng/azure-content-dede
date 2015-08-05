<properties 
pageTitle="Einrichten einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services" 
description="Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen." 
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

# Einrichten einer Remotedesktopverbindung für eine Rolle in Azure
Nachdem Sie einen Clouddienst zur Ausführung Ihrer Anwendung erstellt haben, können Sie per Remotezugriff auf eine Rolleninstanz in dieser Anwendung zugreifen, um Einstellungen zu konfigurieren oder die Problembehandlung durchzuführen. Der Clouddienst muss für Remotedesktop konfiguriert worden sein.

* Benötigen Sie ein Zertifikat zum Aktivieren der Remotedesktopauthentifizierung? [Erstellen](cloud-services-certs-create.md) Sie ein Zertifikat, und konfigurieren Sie es entsprechend den folgenden Schritten.
* Haben Sie Remotedesktop für Ihren Clouddienst bereits eingerichtet? [Stellen Sie eine Verbindung](#remote-into-role-instances) mit dem Clouddienst her.

## Einrichten einer Remotedesktopverbindung für Webrollen oder Workerrollen
Um eine Remotedesktopverbindung für eine Webrolle oder Workerrolle zu aktivieren, können Sie die Verbindung im Dienstmodell für die Anwendung einrichten, oder Sie können die Verbindung im Azure-Verwaltungsportal einrichten, nachdem die Instanzen bereits ausgeführt werden.

### Einrichten der Verbindung im Dienstmodell
Der Dienstdefinitionsdatei, die das **RemoteAccess**-Modul und das **RemoteForwarder**-Modul in das Dienstmodell importiert, muss das **Imports**-Element hinzugefügt werden. Beim Erstellen eines Azure-Projekts mithilfe von Visual Studio werden die Dateien für das Dienstmodell automatisch erstellt.

Das Dienstmodell besteht aus der Datei [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) und der Datei [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg). Die Definitionsdatei wird mit den Rollenbinärdateien gepackt, wenn die Bereitstellung der Anwendung für den Clouddienst vorbereitet wird. Die Datei "ServiceConfiguration.cscfg" wird mit dem Anwendungspaket bereitgestellt und in Azure verwendet, um festzulegen, wie die Anwendung ausgeführt wird.

Nachdem Sie Ihr Projekt erstellt haben, können Sie mithilfe von Visual Studio [eine Remotedesktopverbindung aktivieren](https://msdn.microsoft.com/library/gg443832.aspx).

Nach der Konfiguration der Verbindung sollte die Dienstdefinitionsdatei dem folgenden Beispiel mit hinzugefügtem `<Imports>`-Element ähneln.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```

Die Dienstkonfigurationsdatei sollte dem folgenden Beispiel ähneln und die Werte enthalten, die Sie beim Einrichten der Verbindung angegeben haben. Beachten Sie die Elemente `<ConfigurationSettings>` und `<Certificates>`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```

Beim [Packen](cloud-services-model-and-package.md#cspkg) und [Veröffentlichen](cloud-services-how-to-create-deploy-portal.md) der Anwendung müssen Sie sicherstellen, dass das Kontrollkästchen **Remotedesktop für alle Rollen aktivieren** aktiviert ist. [Dieser](https://msdn.microsoft.com/library/ff683672.aspx) Artikel enthält eine Aufstellung der häufig ausgeführten Aufgaben bei der Verwendung von Visual Studio und Clouddiensten.

### Einrichten der Verbindung auf ausgeführten Instanzen
Auf der Seite "Konfigurieren" des Clouddiensts können Sie Einstellungen für Remotedesktopverbindungen aktivieren oder ändern. Weitere Informationen finden Sie unter [Konfigurieren des Remotezugriffs auf Rolleninstanzen](cloud-services-how-to-configure.md).




## Remotezugriff auf Rolleninstanzen
Für den Zugriff auf Instanzen von Webrollen oder Workerrollen müssen Sie eine Remotedesktopprotokoll (RDP)-Datei verwenden. Sie können die Datei über das Verwaltungsportal herunterladen oder programmgesteuert abrufen.

### Herunterladen der RDP-Datei über das Verwaltungsportal

Mit den folgenden Schritten können Sie die RDP-Datei vom Verwaltungsportal abrufen und dann über die Remotedesktopverbindung mithilfe dieser Datei eine Verbindung mit der Instanz herstellen:

1.  Wählen Sie auf der Seite "Instanzen" die Instanz aus, und klicken Sie dann auf der Befehlsleiste auf **Verbinden**.
2.  Klicken Sie auf **Speichern**, um die RDP-Datei auf dem lokalen Computer zu speichern.
3.  Öffnen Sie **Remotedesktopverbindung**, und klicken Sie dann auf **Optionen anzeigen** und auf **Öffnen**.
4.  Navigieren Sie zu dem Speicherort, an dem Sie die RDP-Datei gespeichert haben, wählen Sie die Datei aus, und klicken Sie dann auf **Öffnen** und auf **Verbinden**. Befolgen Sie die Anweisungen, um die Verbindung herzustellen.

### Abrufen der RDP-Datei mithilfe von PowerShell
Sie können die RDP-Datei mit dem Cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) abrufen.

### Herunterladen der Datei mithilfe von Visual Studio
In Visual Studio können Sie mit dem Server-Explorer eine Remotedesktopverbindung erstellen.

1.  Erweitern Sie in Server-Explorer den Knoten **Azure\Clouddienste\[Name des Clouddiensts]**.
2.  Erweitern Sie entweder den Knoten **Staging** oder **Produktion**.
3.  Erweitern Sie die jeweilige Rolle.
4.  Klicken Sie mit der rechten Maustaste auf eine der Rolleninstanzen, klicken Sie auf **Mithilfe von Remotedesktop verbinden**, und geben Sie dann den Benutzernamen und das Kennwort ein.

### Programmgesteuertes Herunterladen der RDP-Datei über die Dienstverwaltungs-REST-API
Sie können den REST-Vorgang [Download RDP File](https://msdn.microsoft.com/library/jj157183.aspx) verwenden, um die RDP-Datei herunterzuladen. Sie können dann die RDP-Datei mit der Remotedesktopverbindung verwenden, um auf den Clouddienst zuzugreifen.

## Nächste Schritte
Möglicherweise müssen Sie die Anwendung [packen ](cloud-services-model-and-package.md) oder [hochladen (bereitstellen)](cloud-services-how-to-create-deploy-portal.md).

<!---HONumber=July15_HO4-->
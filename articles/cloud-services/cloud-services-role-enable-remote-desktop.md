<properties 
pageTitle="Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services" 
description="Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen." 
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
ms.date="10/14/2015" 
ms.author="saurabh"/>

# Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services

>[AZURE.SELECTOR]
- [Azure classic portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird.

Sie können eine Remotedesktopverbindung in Ihrer Rolle während der Entwicklung aktivieren, indem Sie die Remotedesktopmodule in ihre Dienstdefinition aufnehmen, oder Sie können Remotedesktop über die Remotedesktoperweiterung aktivieren. Der bevorzugte Ansatz ist die Verwendung der Remotedesktoperweiterung, da sie Remotedesktop damit auch nach der Bereitstellung der Anwendung aktivieren können, ohne die Anwendung erneut bereitzustellen.


## Konfigurieren von Remotedesktop über das Portal
Das Portal ermöglicht die Remotedesktoperweiterung, sodass Sie Remotedesktop auch nach der Bereitstellung der Anwendung aktivieren können. Auf der Seite **Konfigurieren** des Clouddiensts können Sie Remotedesktop aktivieren, das lokale Administratorkonto, das zum Herstellen einer Verbindung mit den virtuellen Computern verwendet wird, das bei der Authentifizierung verwendete Zertifikat oder das Ablaufdatum ändern bzw. festlegen.


1. Klicken Sie auf **Cloud Services** und dann auf den Namen des Clouddiensts, und klicken Sie dann auf **Konfigurieren**.

2. Klicken Sie auf **Remote**.
    
    ![Clouddienste remote](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING]Alle Rolleninstanzen werden neu gestartet, wenn Sie Remotedesktop erstmals aktivieren und auf OK (Häkchen) klicken. Um einen Neustart zu verhindern, muss in der Rolle das Zertifikat installiert sein, mit dem das Kennwort verschlüsselt wird. Zum Verhindern eines Neustarts [laden Sie ein Zertifikat für den Clouddienst hoch](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) und kehren dann zu diesem Dialogfeld zurück.
    

3. Wählen Sie unter **Rollen** die Rolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

4. Nehmen Sie die folgenden Änderungen vor:
    
    - Um Remotedesktop zu aktivieren, aktivieren Sie das Kontrollkästchen **Remotedesktop aktivieren**. Um Remotedesktop zu deaktivieren, deaktivieren Sie das Kontrollkästchen.
    
    - Erstellen Sie ein Konto, das für Remotedesktopverbindungen mit den Rolleninstanzen verwendet wird.
    
    - Aktualisieren Sie das Kennwort für das bestehende Konto.
    
    - Wählen Sie ein hochgeladenes Zertifikat für die Authentifizierung aus (laden Sie das Zertifikat mit **Hochladen** auf der Seite **Zertifikate** hoch), oder erstellen Sie ein neues Zertifikat.
    
    - Ändern Sie das Ablaufdatum für die Remotedesktopkonfiguration.

5. Wenn Sie die Konfigurationsupdates beendet haben, klicken Sie auf **OK** (Häkchen).


## Remotezugriff auf Rolleninstanzen
Nach der Aktivierung von Remotedesktop in den Rollen können Sie mit verschiedenen Tools remote auf eine Rolleninstanz zugreifen.

So verbinden Sie eine Rolleninstanz über das Portal:
    
  1.   Klicken Sie auf **Instanzen**, um die Seite **Instanzen** zu öffnen.
  2.   Wählen Sie eine Rolleninstanz aus, in der Remotedesktop konfiguriert ist.
  3.   Klicken Sie auf **Verbinden**, und folgen Sie den Anweisungen, um den Desktop zu öffnen. 
  4.   Klicken Sie auf **Öffnen** und dann auf **Verbinden**, um die Remotedesktopverbindung zu starten. 


### Remotezugriff auf eine Rolleninstanz mithilfe von Visual Studio

In Server-Explorer von Visual Studio:

1. Erweitern Sie den Knoten **Azure\\Clouddienste\\[Name des Clouddienstes]**.
2. Erweitern Sie entweder **Staging** oder **Produktion**.
3. Erweitern Sie die jeweilige Rolle.
4. Klicken Sie mit der rechten Maustaste auf eine der Rolleninstanzen, klicken Sie auf **Mithilfe von Remotedesktop verbinden...**, und geben Sie dann den Benutzernamen und das Kennwort ein. 

![Server-Explorer von Remotedesktop](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### Abrufen der RDP-Datei mithilfe von PowerShell
Sie können die RDP-Datei mit dem Cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) abrufen. Sie können dann die RDP-Datei mit der Remotedesktopverbindung verwenden, um auf den Clouddienst zuzugreifen.

### Programmgesteuertes Herunterladen der RDP-Datei über die Dienstverwaltungs-REST-API
Sie können den REST-Vorgang [Download RDP File](https://msdn.microsoft.com/library/jj157183.aspx) verwenden, um die RDP-Datei herunterzuladen.



## Konfigurieren von Remotedesktop in der Dienstdefinitionsdatei

Diese Methode ermöglicht das Aktivieren von Remotedesktop für die Anwendung während der Entwicklung. Dieser Ansatz erfordert die Speicherung verschlüsselter Kennwörter in Ihrer Dienstkonfigurationsdatei, und alle Aktualisierungen der Remotedesktopkonfiguration würden eine erneute Bereitstellung der Anwendung erfordern. Wenn Sie diese Nachteile umgehen möchten, sollten Sie den oben beschriebenen Ansatz mit der Remotedesktoperweiterung verwenden.

Sie können Visual Studio verwenden, um mithilfe Ansatzes mit der Dienstdefinitionsdatei eine [Remotedesktopverbindung zu aktivieren](https://msdn.microsoft.com/library/gg443832.aspx). Die folgenden Schritte beschreiben die erforderlichen Änderungen an den Dienstmodelldateien, um Remotedesktop zu aktivieren. Visual Studio nimmt diese Änderungen bei der Veröffentlichung automatisch vor.

### Einrichten der Verbindung im Dienstmodell 
Verwenden Sie das **Imports**-Element zum Importieren der **RemoteAccess**-und **RemoteForwarder**-Module in die Datei [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef).

Die Dienstdefinitionsdatei sollte dem folgenden Beispiel mit hinzugefügtem `<Imports>`-Element ähneln.

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
Die Datei [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) sollte dem folgenden Beispiel mit ähneln; beachten Sie die hinzugefügtem Elemente `<ConfigurationSettings>` und `<Certificates>`. Das angegebene Zertifikat muss [zum Clouddienst hochgeladen werden](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service).

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


## Zusätzliche Ressourcen

[Konfigurieren von Clouddiensten](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_1203_2015-->
<properties urlDisplayName="Service Management API" pageTitle="Gewusst wie: Verwenden der Dienstverwaltungs-API f&uuml;r virtuelle Computer &ndash; Azure" metaKeywords="" description="Erfahren Sie, wie Sie die Azure-Dienstverwaltungs-API f&uuml;r einen virtuellen Linux-Computer verwenden." metaCanonical="" services="virtual-machines" documentationCenter="" title="Verwenden der Dienstverwaltungs-AP" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Verwenden der Dienstverwaltungs-AP

## Initialisierung

Zum Aufrufen der Azure IaaS Dienstverwaltungs-API von NodeJS wird das `azure`-Modul verwendet.

    var azure = require('azure');

Erstellen Sie zuerst eine Instanz von ServiceManagementService. Alle Aufrufe an die API verwenden dieses Objekt. Zu diesem Zeitpunkt werden die Abonnement-ID, Anmeldedaten und andere Verbindungsoptionen festgelegt. Wenn Sie mehrere Abonnements verwalten möchten, erstellen Sie mehrere ServiceManagementService-Instanzen.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   subscriptionid ist eine erforderliche Zeichenfolge. Dabei sollte es sich um die Abonnement-ID des Kontos handeln, auf das zugegriffen wird.
-   auth ist ein optionales Objekt, das den privaten Schlüssel und das öffentliche Zertifikat angibt, die mit diesem Konto verwendet werden.

    -   keyfile - Dateipfad der PEM-Datei, die einen privaten Schlüssel hat. Wird ignoriert, wenn "keyvalue" angegeben ist.
    -   keyvalue - tatsächlicher Wert des privaten Schlüssels, wie er in der PEM-Datei gespeichert ist.
    -   certfile - Dateipfad der PEM-Datei, die ein öffentliches Zertifikat hat. Wird ignoriert, wenn "certvalue" angegeben ist.
    -   certvalue - tatsächlicher Wert des öffentlichen Zertifikats, wie er in der PEM-Datei gespeichert ist.
    -   Wenn die oben aufgelisteten Werte nicht angegeben sind, werden die Werte der Prozessumgebungsvariablen `CLIENT_AUTH_KEYFILE` und `CLIENT_AUTH_CERTFILE` gelesen und verwendet. Falls auch diese nicht festgelegt sind, werden die Standardwerte für die Dateien probiert: priv.pem und pub.pem.
    -   Sollte es nicht möglich sein, den privaten Schlüssel und das öffentliche Zertifikat zu laden, wird ein Fehler ausgegeben.
-   options ist ein optionales Objekt, das verwendet werden kann, um die vom Client verwendeten Eigenschaften zu kontrollieren.

    -   host - Hostname des Azure-Verwaltungsservers. Falls nicht angegeben, wird der Standardhost verwendet.
    -   apiversion - Versionszeichenfolge zur Verwendung im HTTP-Header. Falls nicht angegeben, wird die Standardversion verwendet.
    -   serializetype - XML oder JSON. Falls nicht angegeben, wird die Standardserialisierung verwendet.

Optional kann ein Tunneling-Proxy verwendet werden, um die HTTPS-Anforderung über den Proxy zu ermöglichen. Wenn der IaasClient erstellt wird, verarbeitet er die Umgebungsvariable `HTTPS_PROXY`. Falls diese auf eine gültige URL eingestellt ist, werden der Hostname und der Port aus der URL übernommen und in nachfolgenden Anforderungen zur Identifizierung des Proxy verwendet.

        iaasClient.SetProxyUrl(proxyurl)

SetProxyUrl kann aufgerufen werden, um Proxyhost und Port explizit festzulegen. Dies hat denselben Effekt wie das Festlegen der Umgebungsvariablen `HTTPS_PROXY`, und die Umgebungseinstellung wird überschrieben.

## Rückruf

Alle APIs haben ein erforderliches Rückruf-Argument "callback". Die Fertigstellung der Anforderung wird durch das Aufrufen der in "callback" übergebenen Funktion signalisiert.

    callback(rsp)

-   callback verfügt über einen einzigen Parameter - das Antwortobjekt.
-   isSuccessful - wahr oder falsch
-   statusCode - HTTP-Statuscode von der Antwort
-   response - in ein Javascript-Objekt gelesene Antwort. Wird angegeben, wenn "isSuccessful = true".
-   error - Javascript-Objekt, das Fehlerinformationen enthält, wenn "isSuccessful = false".
-   body - der eigentliche Text der Antwort als Zeichenfolge
-   headers - die eigentlichen HTTP-Header der Antwort
-   reqopts - Node-HTTP-Anforderungsoptionen zum Durchführen der Anforderung

Beachten Sie, dass in manchen Fällen die Fertigstellung lediglich bedeutet, dass die Anforderung akzeptiert wurde. Verwenden Sie in diesem Fall **GetOperationStatus**, um den endgültigen Status zu erhalten.

## APIs

**iaasClient.GetOperationStatus(requested, callback)**

-   Viele Verwaltungsaufrufe werden zurückgegeben, bevor der Vorgang abgeschlossen ist. Diese geben den Wert 202 für akzeptiert zurück und platzieren eine Anforderung im ms-request-id HTPP-Header. Um die Fertigstellung der Anforderung abzufragen, verwenden Sie diese API, und übergeben Sie den angeforderten Wert.
-   callback ist erforderlich.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename ist eine erforderliche Zeichenfolge für das Image.
-   callback ist erforderlich.
-   Das Antwortobjekt enthält Eigenschaften des benannten Image, falls erfolgreich.

**iaasClient.ListOSImages(callback)**

-   callback ist erforderlich.
-   Das Antwortobjekt enthält eine Reihe von Image-Objekten, falls erfolgreich.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName ist eine erforderliche Zeichenfolge für das Image.
-   mediaLink ist eine erforderliche Zeichenfolge für den zu verwendenden mediaLink.
-   imageOptions ist ein optionales Objekt. Es kann folgende Eigenschaften enthalten:

    -   Kategorie
    -   Label - Standardmäßig "imageName", falls nichts festgelegt ist.
    -   Verzeichnis
    -   RoleSize
-   callback ist erforderlich. (Wenn imageOptions nicht festgelegt ist, kann dies der dritte Parameter sein.)
-   Das Antwortobjekt enthält Eigenschaften des erstellten Image, falls erfolgreich.

**iaasClient.ListHostedServices(callback)**

-   callback ist erforderlich.
-   Das Antwortobjekt enthält eine Reihe von gehosteten Dienstobjekten, falls erfolgreich.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   callback ist erforderlich.
-   Das Antwortobjekt enthält Eigenschaften des gehosteten Diensts, falls erfolgreich.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   serviceOptions ist ein optionales Objekt. Es kann folgende Eigenschaften enthalten:

    -   Description - Standardmäßig wird "Service host" verwendet.
    -   Label - Standardmäßig serviceName, falls nichts festgelegt ist.
    -   Location - Die Region, in der der Dienst erstellt werden soll.
-   callback ist erforderlich.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   callback ist erforderlich.
-   Das Antwortobjekt enthält Speicherzugriffsschlüssel, falls erfolgreich.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   callback ist erforderlich.
-   Das Antwortobjekt enthält Eigenschaften der benannten Bereitstellung, falls erfolgreich.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentSlot st eine erforderliche Zeichenfolge für den Slot (Staging oder Produktion).
-   callback ist erforderlich.
-   Das Antwortobjekt enthält Eigenschaften der Bereitstellungen im Slot, falls erfolgreich.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   VMRole ist ein erforderliches Objekt, das die Eigenschaften der Rolle enthält, die für die Bereitstellung erstellt werden soll.
-   deployOptions ist ein optionales Objekt. Es kann folgende Eigenschaften enthalten:

    -   DeploymentSlot - Standardmäßig "Production".
    -   Label - Standardmäßig deploymentName, falls nichts festgelegt ist.
    -   UpgradeDomainCount - Kein Standard.
-   callback ist erforderlich.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleName ist eine erforderliche Zeichenfolge der Rolle.
-   callback ist erforderlich.
-   Das Antwortobjekt enthält Eigenschaften der benannten Rolle, falls erfolgreich.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   VMRole ist ein erforderliches Objekt, das die Eigenschaften der Rolle enthält, die zur Bereitstellung hinzugefügt werden soll.
-   callback ist erforderlich.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleName ist eine erforderliche Zeichenfolge der Rolle.
-   VMRole ist ein erforderliches Projekt, das die Eigenschaften enthält, die in der Rolle geändert werden sollen.
-   callback ist erforderlich.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleName ist eine erforderliche Zeichenfolge der Rolle.
-   callback ist erforderlich.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleName ist eine erforderliche Zeichenfolge der Rolle.
-   Datadisk ist ein erforderlich Objekt, das angibt, wie der Datenträger erstellt wird.
-   callback ist erforderlich.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleName ist eine erforderliche Zeichenfolge der Rolle.
-   LUN ist die Nummer zur Identifizierung des Datenträgers.
-   Datadisk ist ein erforderlich Objekt, das angibt, wie der Datenträger geändert wird.
-   callback ist erforderlich.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleName ist eine erforderliche Zeichenfolge der Rolle.
-   LUN ist die Nummer zur Identifizierung des Datenträgers.
-   callback ist erforderlich.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleInstance ist eine erforderliche Zeichenfolge der Rolleninstanz.
-   callback ist erforderlich.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleInstance ist eine erforderliche Zeichenfolge der Rolleninstanz.
-   callback ist erforderlich.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName ist eine erforderliche Zeichenfolge für den gehosteten Dienst.
-   deploymentName ist eine erforderliche Zeichenfolge für die Bereitstellung.
-   roleInstance ist eine erforderliche Zeichenfolge der Rolleninstanz.
-   captOptions ist ein erforderliches Objekt, das die Erfassungsaktionen definiert.

    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   callback ist erforderlich.

## Datenobjekte

Die APIs übernehmen beim Erstellen oder Ändern von Bereitstellungen, Rollen oder Datenträgern Objekte als Eingaben. Andere APIs geben ähnliche Objekte an Get- oder List-Vorgänge zurück.
In diesem Abschnitt werden die Objekteigenschaften beschrieben.
Deployment

-   Name - Zeichenfolge
-   DeploymentSlot - "Staging" oder "Production"
-   Status - Zeichenfolge - nur Ausgabe
-   PrivateID - Zeichenfolge - nur Ausgabe
-   Label - Zeichenfolge
-   UpgradeDomainCount - Zahl
-   SdkVersion - Zeichenfolge
-   Locked - wahr oder falsch
-   RollbackAllowed - wahr oder falsch
-   RoleInstance - Objekt - nur Ausgabe
-   Role - VMRole-Objekt
-   InputEndpointList - Reihe von InputEndpoint

**VMRole**

-   RoleName - Zeichenfolge. Zum Erstellen erforderlich.
-   RoleSize - Zeichenfolge. Zum Erstellen optional.
-   RoleType - Zeichenfolge. Standardmäßig "PersistentVMRole", falls beim Erstellen nicht angegeben.
-   OSDisk - Objekt. Zum Erstellen erforderlich.
-   DataDisks - Reihe von Objekten. Zum Erstellen optional.
-   ConfigurationSets - Reihe von Konfigurationsobjekten.

**RoleInstance**

-   RoleName - Zeichenfolge
-   InstanceName - Zeichenfolge
-   InstanceStatus - Zeichenfolge
-   InstanceUpgradeDomain - Zahl
-   InstanceFaultDomain - Zahl
-   InstanceSize - Zeichenfolge
-   IpAddress - Zeichenfolge

**OSDisk**

-   SourceImageName - Zeichenfolge. Zum Erstellen erforderlich.
-   DisableWriteCache - wahr oder falsch
-   DiskName - Zeichenfolge
-   MediaLink - Zeichenfolge

**DataDisk**

-   DisableReadCache - wahr oder falsch
-   EnableWriteCache - wahr oder falsch
-   DiskName - Zeichenfolge
-   MediaLink - Zeichenfolge
-   LUN - Zahl (0-15)
-   LogicalDiskSizeInGB - Zahl
-   SourceMediaLink - Zeichenfolge
-   Es gibt 3 Möglichkeiten, den Datenträger während der Erstellung anzugeben - nach Name, Medium oder Größe. Die angegebenen Optionen bestimmen die Funktionsweise. Ausführliche Informationen dazu finden Sie in der RDFE API-Dokumentation.

**ProvisioningConfiguration**

-   ConfigurationSetType - "ProvisioningConfiguration"
-   AdminPassword - Zeichenfolge
-   MachineName - Zeichenfolge
-   ResetPasswordOnFirstLogon - wahr oder falsch

**NetworkConfiguration**

-   ConfigurationSetType - "NetworkConfiguration"
-   InputEndpoints - Reihe von ExternalEndpoints

**InputEndpoint**

-   RoleName
-   Vip
-   Port

**ExternalEndpoint**

-   LocalPort
-   Name
-   Port
-   Protokoll

## Beispielcode

Dies ist ein Beispiel für einen Javascript-Code, der einen gehosteten Dienst und eine Bereitstellung erstellt und dann den Fertigstellungsstatus der Bereitstellung abfragt.

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });

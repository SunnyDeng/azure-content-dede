<properties 
   pageTitle="Standardmäßige Größe des TEMP-Ordners ist für eine Rolle zu gering | Microsoft Azure"
   description="Eine Clouddienstrolle verfügt über begrenzten Speicherplatz für den Ordner TEMP. Dieser Artikel enthält einige Vorschläge dazu, wie Sie vermeiden, dass kein Speicherplatz mehr vorhanden ist."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# Standardmäßige Größe des TEMP-Ordners ist für eine Clouddienst-Web-/Workerrolle zu gering

Das standardmäßige temporäre Verzeichnis einer Clouddienst-Web- oder -Workerrolle hat eine maximale Größe von 100 MB. Es kann vorkommen, dass dieser Platz auch belegt wird. In diesem Artikel wird beschrieben, wie Sie verhindern, dass der Speicherplatz des temporären Verzeichnisses erschöpft ist.

>[AZURE.NOTE]Dies gilt nur für Azure SDK 1.0 bis SDK 1.4 mit Web- und Workerrollen.

## Kontaktieren des Azure-Kundensupports

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).


## Wie kann es passieren, dass kein Speicherplatz mehr übrig ist?
Die standardmäßigen Windows-Umgebungsvariablen TEMP und TMP stehen für Code zu Verfügung, der in der Anwendung ausgeführt wird. Sowohl TEMP als auch TMP zeigen auf ein einzelnes Verzeichnis mit einer maximalen Größe von 100 MB. Alle Daten, die in diesem Verzeichnis gespeichert werden, werden nicht über den Lebenszyklus des gehosteten Diensts beibehalten. Wenn die Rolleninstanzen in einem gehosteten Dienst recycelt werden, wird das Verzeichnis bereinigt.

## Vorschläge zum Beheben des Problems
Implementieren Sie eine der folgenden Alternativen:

- Konfigurieren Sie eine lokale Speicherressource, und greifen Sie direkt darauf zu, anstatt **TEMP** oder **TMP** zu verwenden. Rufen Sie die [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx)-Methode auf, um aus Code, der in Ihrer Anwendung ausgeführt wird, auf eine lokale Speicherressource zuzugreifen. Weitere Informationen zum Einrichten lokaler Speicherressourcen finden Sie unter [Konfigurieren lokaler Speicherressourcen](cloud-services-configure-local-storage-resources.md).

- Konfigurieren Sie eine lokale Speicherressource, und lassen Sie die Verzeichnisse TEMP und TMP auf den Pfad der lokalen Speicherressource verweisen. Führen Sie diese Änderung in der [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)-Methode durch.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie die Zielverzeichnisse für TEMP und TMP über die OnStart-Methode ändern:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore" 
            //                  cleanOnRoleRecycle="false" 
            //                  sizeInMB="1024" />
            // </LocalResources>
            
            string customTempLocalResourcePath = 
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);
            
            // The rest of your startup code goes here…
            
            return base.OnStart();
        }
    }
}
```

## Nächste Schritte

Sehen Sie sich weitere [Artikel zur Problembehandlung](..\?tag=top-support-issue&service=cloud-services) für Clouddienste an.

<!---HONumber=Oct15_HO4-->
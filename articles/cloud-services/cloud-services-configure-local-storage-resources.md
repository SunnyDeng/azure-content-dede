<properties
pageTitle="Konfigurieren lokaler Speicherressourcen in Azure Cloud Services"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# Konfigurieren lokaler Speicherressourcen

Eine lokale Speicherressource ist ein reserviertes Verzeichnis im Dateisystem des virtuellen Computers, in dem eine Instanz einer Rolle ausgeführt wird. Sie können Informationen in der Instanz des virtuellen Computers speichern, sodass Code, der in der Instanz ausgeführt wird, auf die lokale Speicherressource zugreifen kann, wenn Daten aus einer Datei gelesen oder geschrieben werden müssen. Beispielsweise kann eine lokale Speicherressource zum Zwischenspeichern von Daten verwendet werden, auf die möglicherweise erneut zugegriffen werden muss, während der Dienst in Azure ausgeführt wird. Sie können die lokale Speicherressource auch zum Speichern von Dateien während des Startvorgangs konfigurieren. Weitere Informationen zum Konfigurieren von lokalen Speicherressourcen während des Startvorgangs finden Sie unter [Verwenden von lokalem Speicher zum Speichern von Dateien während des Startvorgangs](https://msdn.microsoft.com/library/azure/hh974419.aspx).

Eine lokale Speicherressource wird in der Dienstdefinitionsdatei deklariert. Sie können eine beliebige Anzahl lokaler Speicherressourcen für eine Rolle deklarieren. Jede lokale Speicherressource wird für jede Instanz dieser Rolle reserviert. Die Mindestmenge an Speicherplatz, die Sie einer lokalen Speicherressource zuordnen können, ist 1 MB. Die maximale Menge, die Sie einer beliebigen lokalen Ressource zuordnen können, hängt von der Größe des virtuellen Computers ab, der für die Rolle angegeben wird. Für jede virtuelle Computergröße gibt es eine entsprechende Gesamtspeicherplatzzuweisung. Der gesamte Speicherplatz, der allen lokalen Speicherressourcen zugeordnet ist, die für eine Rolle deklariert sind, darf nicht die Gesamtgröße überschreiten, die dieser virtuellen Computergröße zugewiesen ist. Weitere Informationen zur maximalen Menge an lokalem Speicherplatz, der den einzelnen virtuellen Computergrößen zugewiesen ist, finden Sie unter [Konfigurieren von Größen für Clouddienste](https://msdn.microsoft.com/library/azure/ee814754.aspx).

> [AZURE.NOTE]
>
-   Beachten Sie, dass der Entwickler sicherstellen muss, dass die Größe des Speicherplatzes, der für eine lokale Speicherressource angefordert wird, nicht die Gesamtgröße übersteigt, die einem virtuellen Computer zugewiesen ist. Wenn Sie eine lokale Speicherressource größer als das zulässige Maximum konfigurieren, tritt erst dann ein Fehler auf, wenn Sie einen Schreibvorgang versuchen, der das erlaubte Maximum überschreitet. In diesem Fall misslingt der Schreibvorgang, und eine Fehlermeldung zu fehlendem Speicherplatz wird angezeigt. Das Verarbeitungsmodell für Azure ist "Versuch und Irrtum". Wenn Sie einen Fehler aufgrund von fehlendem Speicherplatz erhalten, können Sie den Fehler korrigieren, indem Sie Speicherplatz freigeben. Sie können dann den Schreibvorgang wiederholen.
-   Sie können angeben, dass eine lokale Speicherressource beibehalten werden, wenn eine Instanz wiederverwendet wird. Doch Daten, die im lokalen Dateisystem des virtuellen Computers gespeichert werden, sind nicht garantiert beständig. Wenn Ihre Rolle beständig gespeicherte Daten erfordert, empfiehlt es sich, dass Sie ein Azure-Laufwerk zum Speichern von Daten verwenden. Azure-Laufwerke werden vom Azure Blob-Dienst unterstützt, sodass sie garantiert beständig sind.  
>


## Hinzufügen einer lokalen Speicherressource

Fügen Sie zum Deklarieren einer lokalen Speicherressource in der Dienstdefinitionsdatei das Element **LocalResources** als untergeordnetes Element des Elements **WebRole** oder **WorkerRole** hinzu. Fügen Sie dann ein Element **LocalStorage** hinzu, das die Ressource darstellt. Das Element **LocalStorage** verwendet drei Attribute:

-   *name*
-   *sizeInMB*: Gibt die gewünschte Größe dieser lokalen Speicherressource an.
-   *cleanOnRoleRecycle*: Gibt an, ob die lokale Speicherressource bereinigt werden soll, wenn eine Rolleninstanz wiederverwendet wird, oder ob sie im gesamten Lebenszyklus der Rolle beibehalten werden soll. Der Standardwert lautet **true**.

Die folgende Dienstdefinitionsdatei zeigt zwei lokale Speicherressourcen, die für eine Webrolle deklariert sind:

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

Unter [Azure-Dienstdefinitionsschema \(CSDEF-Datei\)](https://msdn.microsoft.com/library/azure/ee758711.aspx) finden Sie weitere Informationen zur Dienstdefinitionsdatei.

> [AZURE.NOTE]Bei Verwendung der Azure-Tools für Microsoft Visual Studio können Sie eine lokale Speicherressource auf den Seiten mit den **Eigenschaften** der Rolle definieren. Weitere Informationen finden Sie unter [Konfigurieren der Azure-Anwendung mit Visual Studio](https://msdn.microsoft.com/library/ee405486.aspx).

## Programmgesteuertes Zugreifen auf eine lokale Speicherressource

Für den Zugriff auf die lokale Speicherressource muss die Anwendung den Pfad aus der Methode [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) abrufen. Anschließend können Sie standardmäßige Lese- und Schreibvorgänge zum Lesen und Schreiben des Inhalts der lokalen Speicherressource verwenden. Das folgende Beispiel zeigt z. B., wie der Inhalt einer Datei namens **MyTest.txt** aus der lokalen Speicherressource gelesen und auf der Startseite einer MVC 3-Anwendung angezeigt wird:

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## Zugreifen auf eine lokale Speicherressource zur Laufzeit

Die verwaltete Azure-Bibliothek bietet Klassen für den Zugriff auf die lokale Speicherressource aus Code, der in einer Rolleninstanz ausgeführt wird. Die [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx)-Methode gibt einen Verweis auf ein benanntes [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx)-Objekt zurück.

Da das **LocalResource**-Objekt ein Verzeichnis darstellt, können Sie dessen Daten mithilfe der standardmäßigen Datei-E/A-Klassen von .NET lesen und schreiben. Um den Pfad zum Verzeichnis der lokalen Speicherressource zu bestimmen, verwenden Sie die [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx)-Eigenschaft. Diese Eigenschaft gibt den vollständigen Pfad zur lokalen Speicherressource einschließlich des benannten Ressourcenverzeichnisses zurück. Wird Ihr Dienst z. B. in der Entwicklungsumgebung ausgeführt, ist die lokale Speicherressource in Ihrem lokalen Dateisystem definiert, und die **RootPath**-Eigenschaft würde einen Wert wie den folgenden zurückgeben:


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

Wenn Ihr Dienst in Azure bereitgestellt ist, enthält der Pfad zur lokalen Speicherressource die Bereitstellung-ID, und die **RootPath**-Eigenschaft gibt einen Wert zurück, der dem folgenden ähnelt:


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

In einer Rolleninstanz ausgeführter Code kann auf eine lokale Speicherressource, die für diese Rolle definiert ist, vom Zeitpunkt des Onlineschaltens bis zum Zeitpunkt des Offlineschaltens zugreifen.

## Nächste Schritte

- [Einrichten eines Clouddiensts für Azure](https://msdn.microsoft.com/library/azure/hh124108.aspx)

<!---HONumber=August15_HO7-->
<properties
   pageTitle="Resource Manager SDK für Java| Microsoft Azure"
   description="Enthält eine Übersicht über die Resource Manager Java SDK-Authentifizierung und Beispiele zur Verwendung."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>
   
# Azure Resource Manager SDK für Java
   
Azure Resource Manager (ARM) Preview SDKs sind für mehrere Sprachen und Plattformen verfügbar. Jede dieser Sprachimplementierungen ist über die entsprechenden Ökosystem-Paket-Manager und GitHub verfügbar.

Der Code in diesen SDKs wird jeweils über [Azure RESTful-API-Spezifikationen](https://github.com/azure/azure-rest-api-specs) generiert. Dies sind Open-Source-Spezifikationen, die auf der Swagger v2-Spezifikation basieren. Der SDK-Code ist Code, der über ein Open-Source-Projekt mit dem Namen [AutoRest](https://github.com/azure/autorest) generiert wird. AutoRest transformiert diese RESTful-API-Spezifikationen in Clientbibliotheken in mehreren Sprachen. Falls Sie Aspekte des generierten Codes in den SDKs verbessern möchten, können Sie den gesamten Satz der Tools zum Erstellen der SDKs verwenden. Sie sind frei zugänglich und verfügbar und basieren auf dem gängigen API-Spezifikationsformat.

Das Azure Resource Manager Java SDK wird im [Azure Java SDK-Repository](https://github.com/azure/azure-sdk-for-java) bei GitHub gehostet. Beachten Sie, dass sich das SDK zum Zeitpunkt der Erstellung dieses Artikels in der Vorschauphase befindet. Folgende Pakete sind verfügbar:

* Computeverwaltung: (azure-mgmt-compute)
* DNS-Verwaltung: (azure-mgmt-dns)
* Netzwerkverwaltung: (azure-mgmt-network)
* Ressourcenverwaltung: (azure-mgmt-resources)
* SQL-Verwaltung: (azure-mgmt-sql)
* Speicherverwaltung: (azure-mgmt-storage)
* Traffic Manager-Verwaltung: (azure-mgmt-traffic-manager)
* Utilities und Hilfsprogramme: (azure-mgmt-utility)
* WebSites/WebApps-Verwaltung: (azure-mgmt-websites)

Die Wiki-Seite [Azure SDK for Java Features](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features) (Features des Azure-SDK für Java) enthält eine aktuelle Liste.

## Voraussetzungen
1. Java v1.6+
2. [Maven](https://maven.apache.org/): Ist erforderlich, wenn Sie für das SDK entwickeln möchten.

## Abrufen des SDK
[Maven](https://maven.apache.org/): Verteilte JARs sind die empfohlene Vorgehensweise für die ersten Schritte mit dem Azure Java SDK. Sie können diese Abhängigkeiten vielen Java-Tools für die Abhängigkeitsverwaltung (Maven, Gradle, Ivy usw.) hinzufügen. Folgen Sie diesem [Link](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22), um eine Liste mit den in Maven verfügbaren Bibliotheken zu erhalten.

Alternativ hierzu können Sie das SDK per Git auch direkt von der Quelle abrufen. Geben Sie Folgendes ein, um den Quellcode des SDK über Git abzurufen:

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(In den Beispielen in dieser Übersicht wird Maven als Quelle für die SDK-Pakete verwendet.)

Das SDK enthält Beispiele für einige Szenarien: Authentifizierung, Bereitstellung einer VM und mehr. Sie finden alle Beispiele im GitHub-Repository [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples).

## Hilfsklassen

Das SDK enthält Hilfsklassen für einige der wichtigsten Pakete. Die Hilfsklassen werden im Paket „azure-mgmt-utility“ implementiert:

* AuthHelper – Hilfsklasse für die Authentifizierung
* ComputeHelper – Computehilfsklasse
* NetworkHelper – Netzwerk-Hilfsklasse
* ResourceHelper – Hilfsklasse für Bereitstellungen
* StorageHelper – Speicherhilfsklasse

**Abhängigkeitsinformationen zu Maven**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

Hierfür wird Version 0.9.1 des Hilfsprogrammpakets verwendet.

## Authentifizierung

Die Authentifizierung für ARM wird per Azure Active Directory (AD) durchgeführt. Zum Herstellen einer Verbindung mit einer API müssen Sie sich zuerst gegenüber Azure AD authentifizieren, um ein Authentifizierungstoken zu erhalten, das Sie für jede Anforderung übergeben können. Um dieses Token zu erhalten, müssen Sie zuerst eine so genannte Azure AD-Anwendung und einen Dienstprinzipal erstellen, der für die Anmeldung verwendet wird. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals](./resource-group-create-service-principal-portal.md).

Nach dem Erstellen des Dienstprinzipals sollten Sie über Folgendes verfügen:
* Client-ID (GUID)
* Geheimer Clientschlüssel (Zeichenfolge)
* Mandanten-ID (GUID) oder Domänenname (Zeichenfolge) Mit diesen Werten können Sie ein Active Directory-Zugriffstoken abrufen, das eine Stunde lang gültig ist.

Das Java SDK enthält die AuthHelper-Hilfsklasse, mit der das Zugriffstoken erstellt wird, wenn die Client-ID, der geheime Schlüssel und die Mandanten-ID vorliegen. Im folgenden Beispiel wird in der [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java)-Klasse die AuthHelper-Methode *getAccessTokenFromServicePrincipalCredentials* verwendet, um das Zugriffstoken abzurufen:

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## Erstellen eines virtuellen Computers 
Das Hilfsprogrammpaket enthält die Hilfsklasse [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) zum Erstellen eines virtuellen Computers. Einige Beispiele für die Arbeit mit virtuellen Computern finden Sie im Paket „azure-mgmt-samples“ unter [compute](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute).

Unten ist ein einfacher Ablauf zum Erstellen eines virtuellen Computers angegeben. In diesem Beispiel werden der Speicher und das Netzwerk von der Hilfsklasse als Teil der VM-Erstellung erstellt:

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## Bereitstellen einer Vorlage
Die [ResourceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java)-Klasse wurde erstellt, um den Prozess der Bereitstellung einer ARM-Vorlage mit dem Java SDK zu vereinfachen.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```

Weitere Beispiele finden Sie in den Beispielpaketen unter [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments).

## Weitere nützliche Informationen und Hilfe
Dokumentation zum Azure-SDK für Java: [Java docs](http://azure.github.io/azure-sdk-for-java/) (Java-Dokumente) Falls Sie Fehler im SDK finden, bitten wir Sie, unter [Issues](https://github.com/Azure/azure-sdk-for-java/issues) (Probleme) einen Eintrag vorzunehmen oder unter [Stack Overflow for Azure Java SDK](http://stackoverflow.com/questions/tagged/azure-java-sdk) (Stack Overflow für Azure Java-SDK) nachzuschlagen.

<!---HONumber=AcomDC_0316_2016-->
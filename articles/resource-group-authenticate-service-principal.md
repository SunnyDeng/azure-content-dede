<properties
   pageTitle="Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager"
   description="Hierin wird beschrieben, wie einem Dienstprinzipal über rollenbasierte Zugriffskontrolle Zugriffsberechtigungen gewährt werden und wie der Dienstprinzipal authentifiziert wird. Hierin wird gezeigt, wie diese Aufgaben mit PowerShell und der Azure-Befehlszeilenschnittstelle ausgeführt werden."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager

In diesem Thema erfahren Sie, wie Sie einem Dienstprinzipal (z. B. einem automatisierten Prozess, einer Anwendung oder einem Dienst) den Zugriff auf andere Ressourcen in Ihrem Abonnement gestatten. Mithilfe des Azure-Ressourcen-Managers können Sie die rollenbasierte Zugriffssteuerung verwenden, um einem Dienstprinzipal zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren. In diesem Thema gezeigt, wie dem Dienstprinzipal mithilfe von PowerShell und der Azure-Befehlszeilenschnittstelle eine Rolle zugewiesen und wie der Dienstprinzipal authentifiziert wird.


## Konzepte
1. Azure Active Directory (AAD): Dies ist ein Clouddienst zur Identitäts- und Zugriffsverwaltung. Weitere Informationen finden Sie unter [What is Azure Active Directory?](./active-directory-whatis.md) (Was ist Azure Active Directory?).
2. Dienstprinzipal: Dies ist eine Anwendungsinstanz in einem Verzeichnis.
3. AD-Anwendung: Dies ist ein Verzeichnisdatensatz, von dem eine Anwendung bei AAD identifiziert wird. Weitere Informationen finden Sie unter [Grundlagen der Authentifizierung im Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Authentifizieren eines Dienstprinzipals und Gewähren von Zugriffsrechten mithilfe von PowerShell

Wenn Azure PowerShell noch nicht installiert ist, lesen Sie [How to install and configure Azure PowerShell](./powershell-install-configure.md) (Installieren und Konfigurieren von Azure PowerShell).

Zunächst wird ein Dienstprinzipal erstellt. Dazu muss im Verzeichnis eine Anwendung erstellt werden. Das Erstellen einer neuen Anwendung im Verzeichnis wird in diesem Abschnitt erläutert.

1. Erstellen Sie eine neue AAD-Anwendung, indem Sie den Befehl **New-AzureADApplication** ausführen. Geben Sie einen Anzeigenamen für die Anwendung an, außerdem die URI zu einer Seite mit einer Beschreibung der Anwendung (der Link wird nicht geprüft), die URIs, von denen die Anwendung identifiziert wird, und das Kennwort für Ihre Anwendungsidentität.

        PS C:> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

   Die Azure AD-Anwendung wird zurückgegeben.

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


>[AZURE.NOTE]Die Eigenschaft **ApplicationId** wird benötigt, um Dienstprinzipale zu erstellen, Rollenzuweisungen vorzunehmen und JWT-Token abzurufen. Speichern Sie die Ausgabe, oder erfassen Sie sie in einer Variablen.

3. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung.

        PS C:> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

   Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

4. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim Parameter **ServicePrincipalName** entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Managing and Auditing Access to Resources](./resource-group-rbac.md) (Verwalten und Überwachen des Zugriffs auf Ressourcen).

        PS C:> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

5. Rufen Sie das Abonnement ab, in dem die Rollenzuweisung erstellt wurde. Dieses Abonnement wird später verwendet, um die **TenantId** des Mandanten abzurufen, in dem sich die Rollenzuweisung des Dienstprinzipals befindet.

        PS C:> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

   Wenn die Rollenzuweisung in einem anderen Abonnement als dem aktuell ausgewählten erstellt wurde, können Sie einen der Parameter **SubscriptoinId** oder **SubscriptionName** angeben, um ein anderes Abonnement abzurufen.

6. Erstellen Sie ein neues **PSCredential**-Objekt, das Ihre Anmeldeinformationen enthält, indem Sie den Befehl **Get-Credential** ausführen.

        PS C:> $creds = Get-Credential

   Sie werden zum Eingeben Ihrer Anmeldeinformationen aufgefordert.

   ![][1]

   Verwenden Sie als Benutzernamen die **ApplicationId** oder die **IdentifierUris**, die Sie beim Erstellen der Anwendung verwendet haben. Verwenden Sie das Kennwort, das Sie beim Erstellen des Kontos angegeben haben.

7. Verwenden Sie die Anmeldeinformationen, die Sie als Eingabe für das Cmdlet **Add-AzureAccount** verwendet haben, von welchem der Dienstprinzipal angemeldet wird:

        PS C:> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

   Die Authentifizierung des Dienstprinzipals für die ADD-Anwendung, die Sie erstellt haben, ist hiermit abgeschlossen.


## Authentifizieren eines Dienstprinzipals und Gewähren von Zugriffsrechten mithilfe der Azure-Befehlszeilenschnittstelle 

Wenn noch keine Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows installiert ist, lesen [Install and Configure the Azure CLI](xplat-cli-install.md) (Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle).

Sie müssen bereits über eine AD-Anwendung und einen Dienstprinzipal verfügen, um die folgenden Schritte ausführen zu können. Informationen zum Einrichten einer AD-Anwendung und eines Dienstprinzipals mithilfe des klassischen Azure-Portals finden Sie unter [Create a new Azure Service Principal using the Azure classic portal](./resource-group-create-service-principal-portal.md) (Erstellen eines neuen Azure- Dienstprinzipals mithilfe des klassischen Azure-Portals).

1. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim Parameter **ServicePrincipalName** entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Managing and Auditing Access to Resources](./resource-group-rbac.md) (Verwalten und Überwachen des Zugriffs auf Ressourcen).

        azure role assignment create --objectId {service-principal-object-id} -o Reader -c /subscriptions/{subscriptionId}/

2. Ermitteln Sie die **TenantId** des Mandanten, der die Rollenzuweisung des Dienstprinzipals enthält, indem Sie die Konten auflisten und in der Ausgabe nach der Eigenschaft **TenantId** suchen.

        azure account list

3. Melden Sie sich an, und verwenden Sie dabei den Dienstprinzipal als Identität. Verwenden Sie als Benutzernamen die **ApplicationId**, die Sie beim Erstellen der Anwendung verwendet haben. Verwenden Sie das Kennwort, das Sie beim Erstellen des Kontos angegeben haben.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

  Die Authentifizierung des Dienstprinzipals für die ADD-Anwendung, die Sie erstellt haben, ist hiermit abgeschlossen.

## Nächste Schritte
Erste Schritte

- [Azure Resource Manager Overview](./resource-group-overview.md)(Übersicht über den Azure-Ressourcen-Manager)  
- [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md)
- [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](./xplat-cli-azure-resource-manager.md)  
- [Using the Azure Portal to manage your Azure resources](./resource-group-portal.md)(Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen)  
  
Erstellen und Bereitstellen von Anwendungen
  
- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md)(Erstellen von Vorlagen im Azure-Ressourcen-Manager)  
- [Deploy an application with Azure Resource Manager Template](./resource-group-template-deploy.md)(Bereitstellen einer Anwendung mit einer Vorlage im Azure-Ressourcen-Manager)  
- [Troubleshooting Resource Group Deployments in Azure](./resource-group-deploy-debug.md)(Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure)  
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md)(Vorlagenfunktionen im Azure-Ressourcen-Manager)  
- [Advanced Template Operations](./resource-group-advanced-template.md)(Erweiterte Anwendungen mit Vorlagen)  
- [Deploy Azure Resources Using .NET Libraries and a Template](./arm-template-deployment.md)(Bereitstellen von Azure-Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage)
  
Organisieren von Ressourcen
  
- [Using tags to organize your Azure resources](./resource-group-using-tags.md)(Verwenden von Tags zum Organisieren von Azure-Ressourcen)  
  
Zugriffsverwaltung und -überwachung
  
- [Managing and Auditing Access to Resources](./resource-group-rbac.md)(Verwalten und Überwachen des Zugriffs auf Ressourcen)  
- [Create a new Azure Service Principal using the Azure portal](./resource-group-create-service-principal-portal.md)(Erstellen eines neuen Azure-Dienstprinzipals mithilfe des Azure-Portals)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!--HONumber=52-->

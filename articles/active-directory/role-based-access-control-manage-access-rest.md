<properties
	pageTitle="Verwalten der rollenbasierten Zugriffssteuerung mit der REST-API"
	description="Verwalten der rollenbasierten Zugriffssteuerung mit der REST-API"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Verwalten der rollenbasierten Zugriffssteuerung mit der REST-API

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Auflisten aller Rollenzuweisungen

Listet alle Rollenzuweisungen mit dem angegebenen Bereich und den zugehörigen Unterbereichen auf.

Zum Auflisten von Rollenzuweisungen benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleAssignments/read` für den Bereich. Für alle integrierten Rollen wird der Zugriff auf diesen Vorgang gewährt. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **GET**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollenzuweisungen auflisten möchten. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{api-version}* durch „2015-07-01“.

Ersetzen Sie *{filter}* durch die Bedingung, die Sie zum Filtern der Liste mit den Rollenzuweisungen anwenden möchten. Die folgenden Bedingungen werden unterstützt.


| Bedingung | *{Filter}* | Replace |
|-----------|------------|---------|
| Auflisten der Rollenzuweisungen nur für den angegebenen Bereich, ohne die Rollenzuweisungen der Unterbereiche | `atScope()` | |
| Auflisten der Rollenzuweisungen nur für spezielle Benutzer, Gruppen oder Anwendungen | `principalId%20eq%20'{objectId}'` | Ersetzen Sie *{objectId}* durch die Azure AD-Objekt-ID des Benutzer-, Gruppen- oder Dienstprinzipals. Beispiel: `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| Auflisten von Rollenzuweisungen nur für bestimmte Benutzer (einschließlich der Zuweisungen von Gruppen, deren Mitglied der Benutzer ist) | `assignedTo('{objectId}')` | Ersetzen Sie *{objectId}* durch die Azure AD-Objekt-ID des Benutzers. Beispiel: `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |



### Antwort

Statuscode: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## Abrufen von Informationen zu einer Rollenzuweisung

Dient zum Abrufen von Informationen zu einer einzelnen Rollenzuweisung, die über den Bezeichner der Rollenzuweisung angegeben wird.

Um Informationen zu einer Rollenzuweisung zu erhalten, benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleAssignments/read`. Für alle integrierten Rollen wird der Zugriff auf diesen Vorgang gewährt. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **GET**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollenzuweisungen auflisten möchten. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-assignment-id}* durch den GUID-Bezeichner der Rollenzuweisung.

Ersetzen Sie *{api-version}* durch „2015-07-01“.

### Antwort

Statuscode: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## Erstellen einer Rollenzuweisung

Dient zum Erstellen einer Rollenzuweisung im angegebenen Bereich für den angegebenen Prinzipal mit Gewährung der angegebenen Rolle.

Zum Erstellen einer Rollenzuweisung benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleAssignments/write`. Von den integrierten Rollen verfügen nur *Besitzer* und *Benutzerzugriffsadministrator* über Zugriff auf diesen Vorgang. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **PUT**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollenzuweisungen erstellen möchten. Wenn Sie eine Rollenzuweisung für einen übergeordneten Bereich erstellen, erben alle untergeordneten Bereiche die gleiche Rollenzuweisung. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-assignment-id}* durch eine neue GUID. Sie wird als GUID-Bezeichner der neuen Rollenzuweisung verwendet.

Ersetzen Sie *{api-version}* durch „2015-07-01“.

Geben Sie für den Anforderungstext die Werte im folgenden Format an:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Elementname | Erforderlich | Typ | Beschreibung |
|------------------|----------|--------|-------------|
| roleDefinitionId | Ja | String | Der Bezeichner der Rolle, die zugewiesen werden soll. Das Format des Bezeichners lautet: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId | Ja | String | objectId des Azure AD-Prinzipals (Benutzer-, Gruppen- oder Dienstprinzipal), dem die Rolle zugewiesen werden soll. |

### Antwort

Statuscode: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## Löschen einer Rollenzuweisung

Dient zum Löschen einer Rollenzuweisung im angegebenen Bereich.

Zum Löschen einer Rollenzuweisung benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleAssignments/delete`. Von den integrierten Rollen verfügen nur *Besitzer* und *Benutzerzugriffsadministrator* über Zugriff auf diesen Vorgang. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **DELETE**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollenzuweisungen erstellen möchten. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-assignment-id}* durch die GUID der Rollenzuweisungs-ID.

Ersetzen Sie *{api-version}* durch „2015-07-01“.

### Antwort

Statuscode: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## Auflisten aller Rollen

Dient zum Auflisten aller Rollen, die für die Zuweisung im angegebenen Bereich verfügbar sind.

Zum Auflisten von Rollen benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleDefinitions/read` für den Bereich. Für alle integrierten Rollen wird der Zugriff auf diesen Vorgang gewährt. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **GET**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollen auflisten möchten. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{api-version}* durch „2015-07-01“.

Ersetzen Sie *{filter}* durch die Bedingung, die Sie zum Filtern der Liste mit den Rollen anwenden möchten. Die folgenden Bedingungen werden unterstützt.

| Bedingung | *{Filter}* | Replace |
|-----------|------------|---------|
| Dient zum Auflisten der Rollen, die im angegebenen Bereich und in allen untergeordneten Bereichen verfügbar sind. | `atScopeAndBelow()` | |
| Suchen nach einer Rolle mit dem genauen Anzeigenamen | `roleName%20eq%20'{role-display-name}'` | Ersetzen Sie *{role-display-name}* durch die URL-codierte Form des genauen Anzeigenamens der Rolle. Beispiel: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |



### Antwort

Statuscode: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Abrufen von Informationen zu einer Rolle

Dient zum Abrufen von Informationen zu einer bestimmten Rolle, die mit dem Bezeichner der Rollendefinition angegeben wird. Informationen zum Abrufen von Informationen zu einer einzelnen Rolle über den Anzeigenamen finden Sie unter „Auflisten aller Rollen“ und „roleName-Filter“.

Um Informationen zu einer Rolle zu erhalten, benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleDefinitions/read`. Für alle integrierten Rollen wird der Zugriff auf diesen Vorgang gewährt. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **GET**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollenzuweisungen auflisten möchten. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-definition-id}* durch den GUID-Bezeichner der Rollendefinition. Ersetzen Sie *{api-version}* durch „2015-07-01“.

### Antwort

Statuscode: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Erstellen einer benutzerdefinierten Rolle
Dient zum Erstellen einer benutzerdefinierten Rolle.

Um eine benutzerdefinierte Rolle zu erstellen, benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleDefinitions/write` für alle dazugehörigen `AssignableScopes`-Elemente. Von den integrierten Rollen verfügen nur *Besitzer* und *Benutzerzugriffsadministrator* über Zugriff auf diesen Vorgang. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **PUT**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den ersten *AssignableScope* der benutzerdefinierten Rolle. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-definition-id}* durch eine neue GUID. Sie wird als GUID-Bezeichner der neuen benutzerdefinierten Rolle verwendet.

Ersetzen Sie *{api-version}* durch „2015-07-01“.

Geben Sie für den Anforderungstext die Werte im folgenden Format an:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Elementname | Erforderlich | Typ | Beschreibung |
|--------------|----------|------|-------------|
| Name | Ja | String | GUID-Bezeichner der benutzerdefinierten Rolle |
| properties.roleName | Ja | String | Anzeigename der benutzerdefinierten Rolle. Maximale Größe von 128 Zeichen. |
| properties.description | Nein | String | Beschreibung der benutzerdefinierten Rolle. Maximale Größe von 1024 Zeichen. |
| properties.type | Ja | String | Legen Sie dies auf „CustomRole“ fest. |
| properties.permissions.actions | Ja | String | Ein Array mit Aktionszeichenfolgen, mit denen die Vorgänge angegeben werden, auf die die benutzerdefinierte Rolle Zugriff gewährt. |
| properties.permissions.notActions | Nein | String | Ein Array mit Aktionszeichenfolgen zum Angeben der Vorgänge, die von den Vorgängen ausgeschlossen werden sollen, für die die benutzerdefinierte Rolle Zugriff gewährt. |
| properties.assignableScopes | Ja | String | Ein Array von Bereichen, in denen die benutzerdefinierte Rolle für die Zugriffsverwaltung verwendet werden kann. |

### Antwort

Statuscode: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Aktualisieren einer benutzerdefinierten Rolle

Dient zum Ändern einer benutzerdefinierten Rolle.

Um eine benutzerdefinierte Rolle zu ändern, benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleDefinitions/write` für alle dazugehörigen `AssignableScopes`-Elemente. Von den integrierten Rollen verfügen nur *Besitzer* und *Benutzerzugriffsadministrator* über Zugriff auf diesen Vorgang. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **PUT**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den ersten *AssignableScope* der benutzerdefinierten Rolle. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-definition-id}* durch den GUID-Bezeichner der benutzerdefinierten Rolle, die aktualisiert werden soll.

Ersetzen Sie *{api-version}* durch „2015-07-01“.

Geben Sie für den Anforderungstext die Werte im folgenden Format an:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Elementname | Erforderlich | Typ | Beschreibung |
|--------------|----------|------|-------------|
| Name | Ja | String | GUID-Bezeichner der benutzerdefinierten Rolle, die aktualisiert werden soll |
| properties.roleName | Ja | String | Anzeigename der aktualisierten benutzerdefinierten Rolle |
| properties.description | Nein | String | Beschreibung der aktualisierten benutzerdefinierten Rolle |
| properties.type | Ja | String | Legen Sie dies auf „CustomRole“ fest. |
| properties.permissions.actions | Ja | String | Ein Array mit Aktionszeichenfolgen, mit denen die Vorgänge angegeben werden, auf die die aktualisierte benutzerdefinierte Rolle Zugriff gewährt. |
| properties.permissions.notActions | Nein | String | Ein Array mit Aktionszeichenfolgen zum Angeben der Vorgänge, die von den Vorgängen ausgeschlossen werden sollen, für die die aktualisierte benutzerdefinierte Rolle Zugriff gewährt. |
| properties.assignableScopes | Ja | String | Ein Array von Bereichen, in denen die aktualisierte benutzerdefinierte Rolle für die Zugriffsverwaltung verwendet werden kann. |

### Antwort

Statuscode: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Löschen einer benutzerdefinierten Rolle

Dient zum Löschen einer benutzerdefinierten Rolle.

Um eine benutzerdefinierte Rolle zu löschen, benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleDefinitions/delete` für alle dazugehörigen `AssignableScopes`-Elemente. Von den integrierten Rollen verfügen nur *Besitzer* und *Benutzerzugriffsadministrator* über Zugriff auf diesen Vorgang. Weitere Informationen zu Rollenzuweisungen und zum Verwalten des Zugriffs für Azure-Ressourcen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](role-based-access-control-configure.md).

### Anforderung

Verwenden Sie die **DELETE**-Methode mit dem folgenden URI:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Führen Sie für den URI folgende Schritte aus, um Ihre Anforderung anzupassen:

Ersetzen Sie *{scope}* durch den Bereich, für den Sie die Rollendefinition löschen möchten. Die folgenden Beispiele zeigen, wie Sie den Bereich für unterschiedliche Ebenen angeben:

| Ebene | *{Scope}* |
|-------|-----------|
| Abonnement | /subscriptions/{subscription-id} |
| Ressourcengruppe | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| Ressource | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Ersetzen Sie *{role-definition-id}* durch die GUID-Rollendefinitions-ID der benutzerdefinierten Rolle, die gelöscht werden soll.

Ersetzen Sie *{api-version}* durch „2015-07-01“.

### Antwort

Statuscode: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0128_2016-->
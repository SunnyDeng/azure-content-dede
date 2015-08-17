<properties
	pageTitle="Bewährte Methoden für die Verarbeitung von Status in Azure-Ressourcen-Manager-Vorlagen"
	description="Zeigt empfohlene Vorgehensweisen zum Verwenden komplexer Objekte, um Statusdaten für Azure-Ressourcen-Manager-Vorlagen und verknüpfte Vorlagen freizugeben."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="mmercuri"/>

# Freigeben des Status in Azure-Ressourcen-Manager-Vorlagen

In diesem Thema wird beschrieben, wie Sie den Status innerhalb einer Azure-Ressourcen-Manager-Vorlage und über verknüpfte Vorlagen hinweg verwalten und freigeben.

## Verwenden komplexer Objekte zum Freigeben des Status

Zusätzlich zu einwertigen Parametern können Sie in einer Azure-Ressourcen-Manager-Vorlage komplexe Objekte als Parameter verwenden. Bei komplexen Objekten können Sie Sammlungen von Daten für einen bestimmten Bereich implementieren und auf diese verweisen, z. B. T-Shirt-Größen wie etwa M oder XL (zur Beschreibung eines virtuellen Computers), Netzwerkeinstellungen, Betriebssystemeinstellungen und Verfügbarkeitseinstellungen.

Das folgende Beispiel zeigt, wie Sie Variablen definieren, die komplexe Objekte für die Darstellung von Datensammlungen enthalten. Die Sammlungen definieren Werte, die für die Größe des virtuellen Computers sowie Netzwerkeinstellungen, Betriebssystemeinstellungen und Verfügbarkeitseinstellungen verwendet werden.

    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "slaveCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    },
    "osSettings": {
      "scripts": [
        "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
      ],
      "imageReference": {
				"publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    },
    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    },
    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Sie können später in der Vorlage auf diese Variablen verweisen. Die Möglichkeit zum Verweisen auf benannte Variablen und deren Eigenschaften vereinfacht die Vorlagensyntax und erleichtert es den Benutzern, den Kontext zu verstehen. Das folgende Beispiel definiert eine Ressource, die unter Verwendung der oben gezeigten Objekte zum Festlegen von Werten bereitgestellt werden soll. Beachten Sie beispielsweise, dass die Größe des virtuellen Computers durch Abrufen des Werts für `variables('tshirtSize').vmSize` festgelegt wird. Der Wert für die Festplattengröße wird aus `variables('tshirtSize').diskSize` abgerufen. Darüber hinaus wird der URI für eine verknüpfte Vorlage mit dem Wert für `variables('tshirtSize').vmTemplate` festgelegt.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
	    "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## Übergeben des Status an eine Vorlage und deren verknüpfte Vorlagen

Sie können Statusinformationen mithilfe folgender Parameter in einer Vorlage und deren verknüpften Vorlagen freigeben:

- Parameter, die Sie während der Bereitstellung direkt an die Hauptvorlage übergeben
- Parameter, statische Variablen und generierte Variablen, welche die Hauptvorlage gemeinsam mit ihren verknüpften Vorlagen nutzt

### Allgemeine Parameter, die in der Hauptvorlage bereitgestellt werden

Die folgende Tabelle führt häufig verwendete Parameter in Vorlagen auf.

**Häufig verwendete Parameter, die an die Hauptvorlage übergeben werden**

Name | Wert | Beschreibung
---- | ----- | -----------
location | Zeichenfolge aus einer eingeschränkten Liste von Azure-Regionen | Der Speicherort, an dem die Ressourcen bereitgestellt werden.
storageAccountNamePrefix | String | Eindeutiger DNS-Name für das Speicherkonto, in dem die VM-Datenträger platziert werden.
domainName | String | Domänenname der öffentlich zugänglichen Jumpbox-VM im Format **{domainName}.{location}.cloudapp.com**. Beispiel: **mydomainname.westus.cloudapp.azure.com**
adminUsername | String | Benutzername für die virtuellen Computer
adminPassword | String | Kennwort für die virtuellen Computer
tshirtSize | Zeichenfolge aus einer eingeschränkten Liste von angebotenen T-Shirt-Größen | Die Größe der benannten Skalierungseinheit, die bereitgestellt werden soll. Beispielsweise "Small", "Medium", "Large".
virtualNetworkName | String | Der Name des virtuellen Netzwerks, das der Nutzer verwenden möchte.
enableJumpbox | Zeichenfolge aus einer eingeschränkten Liste (aktiviert/deaktiviert) | Parameter, der angibt, ob eine Jumpbox für die Umgebung aktiviert werden soll. Werte: "aktiviert", "deaktiviert".

### An verknüpfte Vorlagen gesendete Parameter.

Bei der Verbindung mit verknüpften Vorlagen wird häufig eine Mischung aus statischen und generierten Variablen verwendet.

#### Statische Variablen

Mithilfe statischer Variablen werden häufig Basiswerte wie z. B. URLs bereitgestellt, die in einer Vorlage oder als Werte verwendet werden, um Werte für dynamische Variablen zu erstellen.

Im unten stehenden Vorlagenauszug gibt *templateBaseUrl*den Stammspeicherort für die Vorlage in GitHub zurück. Die nächste Zeile erstellt eine neue Variable*sharedTemplateUrl*, die den Wert von *TemplateBaseUrl* mit dem bekannten Namen der freigegebenen Ressourcenvorlage verkettet. Darunter wird eine komplexe Objektvariable verwendet, um eine T-Shirt-Größe zu speichern. Hierbei wird *templateBaseUrl* verkettet, um den Speicherort der Vorlage für die bekannte Konfiguration anzugeben, der in der Eigenschaft *vmTemplate* gespeichert ist.

Der Vorteil dieses Ansatzes ist, dass Sie die Vorlage problemlos verschieben, verzweigen oder als Grundlage für eine neue Vorlage verwenden können. Wenn sich der Speicherort für die Vorlage ändert, müssen Sie nur die statische Variable an einer Stelle ändern: der Hauptvorlage. Diese übergibt die Änderung an alle anderen Vorlagen.

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### Generierte Variablen

Neben den statischen Variablen werden einige Variablen dynamisch generiert. In diesem Abschnitt werden einige häufig verwendete Arten von generierten Variablen beschrieben.

##### tshirtSize

Beim Aufrufen der Hauptvorlage können Sie aus einer bestimmten Anzahl von Optionen eine T-Shirt-Größe auswählen – in der Regel Werte wie *Small*, *Medium* und *Large*.

In der Hauptvorlage wird diese Option als Parameter wie z. B *tshirtSize* angezeigt:

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

Innerhalb der Hauptvorlage sind für jede Größe entsprechende Variablen vorhanden. Wenn "Small", "Medium" und "Large" als Größen verfügbar sind, enthält der Variablenabschnitt Variablen namens *tshirtSizeSmall*, *tshirtSizeMedium* und *tshirtSizeLarge*.

Wie das folgende Beispiel zeigt, definieren diese Variablen die Eigenschaften einer bestimmten T-Shirt-Größe. Jede Variable identifiziert den VM-Typ, die Datenträgergröße, die zu verknüpfende zugeordnete Ressourcenvorlage für die Skalierungseinheit, die Anzahl von Instanzen, die Speicherkontodetails und den Jumpboxstatus.

Das Namenspräfix des Speicherkontos wird aus einem von einem Benutzer bereitgestellten Parameter abgerufen, und die verknüpfte Vorlage ist die Verkettung der Basis-URL für die Vorlage mit dem Dateinamen einer bestimmten Skalierungseinheit-Ressourcenvorlage.

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
			"diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

Die Variable *tshirtSize* wird weiter unten im Variablenabschnitt verwendet. Das Ende der von Ihnen angegebenen T-Shirt-Größe (*Small*, *Medium*, *Large*) wird mit dem Text *tshirtSize* verkettet, um die zugeordnete komplexe Objektvariable für diese T-Shirt-Größe abzurufen:

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

Diese Variable wird an die verknüpfte Ressourcenvorlage für die Skalierungseinheit übergeben.

##### networkSettings

In Vorlagen für Kapazitäten, Funktionen oder End-to-End-Lösungen erstellen verknüpfte Vorlagen in der Regel Ressourcen, die in einem Netzwerk vorhanden sind. Eine einfache Lösung ist es, ein komplexes Objekt zum Speichern von Netzwerkeinstellungen zu verwenden und diese an verknüpfte Vorlagen zu übergeben.

Hier finden Sie ein Beispiel für die Kommunikation der Netzwerkeinstellungen.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

In verknüpften Vorlagen erstellte Ressourcen werden häufig in einer Verfügbarkeitsgruppe platziert. Im folgenden Beispiel ist der Name der Verfügbarkeitsgruppe ebenso angegeben wie die Anzahl der zu verwendenden Fehler- und Updatedomänen.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Wenn Sie mehrere Verfügbarkeitsgruppen benötigen (z. B. eine für Masterknoten, eine weitere für Datenknoten), können Sie einen Namen als Präfix verwenden, mehrere Verfügbarkeitsgruppen angeben oder gemäß dem oben gezeigten Modell eine Variable für eine bestimmte T-Shirt-Größe erstellen.

##### storageSettings

Speicherdetails werden häufig mit verknüpften Vorlagen freigegeben. Im folgenden Beispiel stellt ein*storageSettings*-Objekt Details zu den Speicherkonto- und Containernamen bereit.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

Bei verknüpften Vorlagen müssen Sie möglicherweise Betriebssystemeinstellungen an verschiedene Arten von Knoten über verschiedene Arten von bekannten Konfigurationen hinweg übergeben. Ein komplexes Objekt ist eine einfache Möglichkeit zum Speichern und Freigeben von Betriebssysteminformationen und erleichtert zudem die Unterstützung mehrerer Betriebssystemoptionen für die Bereitstellung.

Das folgende Beispiel zeigt ein Objekt für *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

Die generierte Variable *machineSettings* ist ein komplexes Objekt, das verschiedene Hauptvariablen zum Erstellen eines neuen virtuellen Computers enthält: Administratorbenutzername und -kennwort, ein Präfix für die VM-Namen und einen Verweis auf ein Betriebssystemimage, wie unten gezeigt:

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Beachten Sie, dass *osImageReference* die Werte aus der in der Hauptvorlage definierten Variable *osSettings* abruft. Dies bedeutet, dass Sie das Betriebssystem eines virtuellen Computers problemlos ändern können – vollständig oder basierend auf den Einstellungen eines Vorlagenutzers.

##### vmScripts

Das *vmScripts*-Objekt enthält Details zu den Skripts zum Herunterladen und Ausführen auf einer VM-Instanz, einschließlich der externen und internen Verweise. Externe Verweise schließen die Infrastruktur ein. Interne Verweise umfassen die installierte Software und die Konfiguration.

Mithilfe der *scriptsToDownload*-Eigenschaft listen Sie die Skripts auf, die auf den virtuellen Computer heruntergeladen werden sollen.

Wie das folgende Beispiel zeigt, enthält dieses Objekt auch Verweise auf Befehlszeilenargumente für verschiedene Arten von Aktionen. Zu diesen Aktionen gehört die Ausführung der Standardinstallation für jeden einzelnen Knoten, eine Installation, die nach der Bereitstellung aller Knoten ausgeführt wird, sowie ggf. die Ausführung weiterer vorlagenspezifischer Skripts.

Dieses Beispiel stammt aus einer Vorlage zum Bereitstellen von MongoDB, wofür ein Arbiter erforderlich ist, um Hochverfügbarkeit sicherzustellen. *arbiterNodeInstallCommand* wurde zu *vmScripts* hinzugefügt, um den Arbiter zu installieren.

Sie finden die Variablen, die den spezifischen Text zum Ausführen des Skripts mit den richtigen Werten definieren, im Variablenabschnitt.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## Zurückgeben eines Status aus einer Vorlage

Sie können nicht nur Daten in eine Vorlage übergeben, Sie können auch Daten wieder in der aufrufenden Vorlage freigeben. Im **outputs**-Abschnitt einer verknüpften Vorlage können Sie Schlüssel-Wert-Paare bereitstellen, die von der Quellvorlage genutzt werden können.

Das folgende Beispiel zeigt, wie die private IP-Adresse übergeben wird, die in einer verknüpften Vorlage generiert wurde.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Innerhalb der Hauptvorlage können Sie diese Daten mit folgender Syntax verwenden:

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## Nächste Schritte
- [Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md) (in englischer Sprache)
- [Funktionen von Azure-Ressourcen- Manager-Vorlagen](resource-group-template-functions.md)

<!---HONumber=August15_HO6-->
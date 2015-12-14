<properties
   	pageTitle="Bereitstellen von Hadoop-, HBase- oder Storm-Clustern unter Linux in HDInsight mit cURL und der Azure-REST-API | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Linux-basierte HDInsight-Cluster mit cURL, Azure-Ressourcen-Manager-Vorlagen und der Azure-REST-API erstellen. Sie können den Cluster-Typ (Hadoop, HBase oder Storm) angeben oder Skripts verwenden, um benutzerdefinierte Komponenten zu installieren."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/23/2015"
   	ms.author="larryfr"/>

#Erstellen von Linux-basierten Clustern in HDInsight mithilfe von cURL und der Azure-REST-API

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-create-linux-cluster-selector.md)]

Mit der Azure-REST-API können Sie Verwaltungsvorgänge für Dienste durchführen, die auf der Azure-Plattform gehostet werden. Beispielsweise können Sie neue Ressourcen wie etwa Linux-basierte HDInsight-Cluster erstellen. In diesem Dokument erfahren Sie, wie Sie Azure-Ressourcen-Manager-Vorlagen erstellen, um einen HDInsight-Cluster sowie den zugehörigen Speicher zu konfigurieren. Sie erfahren auch, wie Sie anschließend die Vorlage mithilfe von cURL in der Azure-REST-API bereitstellen, um einen neuen HDInsight-Cluster zu erstellen.

> [AZURE.IMPORTANT]Bei den Schritten in diesem Dokument wird die Standardanzahl von Workerknoten (4) für einen HDInsight-Cluster verwendet. Wenn Sie mehr als 32 Workerknoten planen, entweder bei Erstellung des Clusters oder durch eine Skalierung des Clusters nach der Erstellung, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.
>
> Weitere Informationen zu Knotengrößen und damit verbundenen Kosten finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

##Voraussetzungen

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure-Befehlszeilenschnittstelle__. Die Azure-Befehlszeilenschnittstelle wird verwendet, um einen Dienstprinzipal zu erstellen, der zum Generieren von Authentifizierungstoken für Anforderungen an die Azure-REST-API verwendet wird.

    Informationen zum Installieren der Befehlszeilenschnittstelle finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

- __cURL__. Dieses Hilfsprogramm ist über Ihr Paketverwaltungssystem verfügbar oder kann hier heruntergeladen werden: [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE]Wenn Sie PowerShell zum Ausführen der Befehle in diesem Dokument verwenden, müssen Sie zuerst den `curl`-Alias entfernen, der standardmäßig erstellt wird. Wenn Sie den `curl`-Befehl von einer PowerShell-Eingabeaufforderung ausführen, verwendet dieser Alias das PowerShell-Cmdlet "Invoke-WebRequest" anstelle von cURL und gibt für viele der in diesem Dokument angegebenen Befehle Fehler zurück.
    > 
    > Um diesen Alias zu entfernen, verwenden Sie folgenden Befehl in der PowerShell-Eingabeaufforderung:
    >
    > ```Remove-item alias:curl`
    >
    > Nachdem der Alias entfernt wurde, können Sie die auf Ihrem System installierte cURL-Version verwenden.

##Erstellen einer Vorlage

Bei Azure-Ressourcen-Manager-Vorlagen handelt es sich um JSON-Dokumente, mit denen eine __Ressourcengruppe__ und alle darin enthaltenen Ressourcen (z. B. HDInsight) beschrieben werden. Dieser vorlagenbasierte Ansatz ermöglicht Ihnen das Definieren aller für HDInsight benötigten Ressourcen in einer einzigen Vorlage sowie das Verwalten von Änderungen an der gesamten Gruppe durch __Bereitstellungen__, mit denen Änderungen an der Gruppe vorgenommen werden.

Vorlagen werden üblicherweise in zwei Teilen bereitgestellt: die Vorlage selbst und eine Parameterdatei, die Sie mit Werten auffüllen, die speziell für Ihre Konfiguration gelten. Beispiel: Clustername, Administratorname und -kennwort. Wenn Sie die REST-API direkt verwenden, müssen Sie diese Werte in eine Datei kombinieren. Das Format dieses JSON-Dokuments sieht folgendermaßen aus:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Folgendes ist beispielsweise eine Kombination aus den Vorlagen- und Parameterdateien von [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password). Damit wird ein Linux-basierter Cluster erstellt, bei dem das SSH-Benutzerkonto durch ein Kennwort geschützt ist.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Dieses Beispiel wird in den Schritten im vorliegenden Dokument verwendet. Sie müssen den Platzhalter _values_ im Abschnitt __Parameters__ am Ende des Dokuments durch die Werte ersetzen, die Sie für Ihren Cluster verwenden möchten.

##Anmelden bei Ihrem Azure-Abonnement

Führen Sie die Schritte aus, die unter [Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure Befehlszeilenschnittstelle (Azure-CLI)](xplat-cli-connect.md) dokumentiert sind, und stellen Sie über die __login__-Methode eine Verbindung mit Ihrem Abonnement her.

##Erstellen eines Dienstprinzipals

> [AZURE.IMPORTANT]Wenn Sie die Schritte in dem unten verlinkten Artikel befolgen, müssen Sie Folgendes ändern:
> 
> * Wenn Sie in den Schritten aufgefordert werden, den Wert __reader__ zu verwenden, müssen Sie stattdessen __owner__ verwenden. Dadurch wird ein Dienstprinzipal erstellt, der Änderungen an den Diensten in Ihrem Abonnement vornehmen kann – dies ist erforderlich, um einen HDInsight-Cluster zu erstellen.
>
> Sie müssen auch die folgenden in diesem Prozess verwendeten Informationen speichern:
> 
> * Abonnement-ID: wird bei Verwendung von `azure account list` empfangen
> * Mandanten-ID: wird bei Verwendung von `azure account list` empfangen
> * Anwendungs-ID: wird bei Erstellung des Dienstprinzipals zurückgegeben
> * Kennwort für den Dienstprinzipal: wird bei Erstellung des Dienstprinzipals verwendet

Befolgen Sie die Schritte im Abschnitt _Authentifizieren des Dienstprinzipals mit einem Kennwort – Azure-Befehlszeilenschnittstelle_ des Dokuments [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli). Damit wird ein neuer Dienstprinzipal erstellt, der zur Authentifizierung der Anforderung zur Clustererstellung verwendet werden kann.

##Abrufen eines Authentifizierungstokens

Verwenden Sie folgenden Code, um ein neues Token aus Azure abzurufen. Ersetzen Sie __TENANTID__, __APPLICATIONID__ und __PASSWORD__ durch die Informationen, die Sie während der Erstellung des Dienstprinzipals gespeichert haben:

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument enthält.

> [AZURE.IMPORTANT]Das von dieser Anforderung zurückgegebene JSON-Dokument enthält ein Element namens __access\_token__. Der Wert dieses Elements ist das Zugriffstoken, das zur Authentifizierung der in den nächsten Abschnitten dieses Dokuments verwendeten Anforderungen erforderlich ist.

##Erstellen einer Ressourcengruppe

Gehen Sie wie folgt vor, um eine neue Ressourcengruppe zu erstellen. Sie müssen zuerst die Gruppe erstellen, bevor Sie die einzelnen Ressourcen, wie z. B. den HDInsight-Cluster, erstellen können.

* Ersetzen Sie __SUBSCRIPTIONID__ durch die Abonnement-ID, die Sie während der Erstellung des Dienstprinzipals erhalten haben.
* Ersetzen Sie __ACCESSTOKEN__ durch das Zugriffstoken, das Sie im vorherigen Schritt erhalten haben.
* Ersetzen Sie __DATACENTERLOCATION__ durch das Datencenter, in dem Sie die Ressourcengruppe und die Ressourcen erstellen möchten. Beispiel: "USA, Mitte/Süden". 
* Ersetzen Sie __GROUPNAME__ durch den Namen, den Sie für diese Gruppe verwenden möchten.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument mit Informationen zur Gruppe enthält. Das `"provisioningState"`-Element enthält den Wert `"Succeeded"`.

##Erstellen einer Bereitstellung

Gehen Sie wie folgt vor, um die Clusterkonfiguration (Vorlage und Parameterwerte) in der Ressourcengruppe bereitzustellen.

* Ersetzen Sie __SUBSCRIPTIONID__ und __ACCESSTOKEN__ durch die oben verwendeten Werte. 
* Ersetzen Sie __GROUPNAME__ durch den Namen der Ressourcengruppe, die Sie im vorherigen Abschnitt erstellt haben.
* Ersetzen Sie __DEPLOYMENTNAME__ durch den Namen, den Sie für diese Bereitstellung verwenden möchten.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{Textzeichenfolge auf die Vorlage und Parameter festlegen}"

> [AZURE.NOTE]Wenn Sie das JSON-Dokument mit der Vorlage und den Parameter in eine Datei gespeichert haben, können Sie anstelle von '-d "{ Vorlage und Parameter}"' Folgendes verwenden:
>
> ```--data-binary "@/path/to/file.json"```

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument mit Informationen zum Bereitstellungsvorgang enthält.

> [AZURE.IMPORTANT]Beachten Sie, dass die Bereitstellung zu diesem Zeitpunkt übermittelt, aber noch nicht abgeschlossen wurde. Es kann mehrere Minuten dauern (in der Regel etwa 15), bis die Bereitstellung abgeschlossen ist.

##Überprüfen des Bereitstellungsstatus

Gehen Sie wie folgt vor, um den Status der Bereitstellung zu prüfen.

* Ersetzen Sie __SUBSCRIPTIONID__ und __ACCESSTOKEN__ durch die oben verwendeten Werte. 
* Ersetzen Sie __GROUPNAME__ durch den Namen der Ressourcengruppe, die Sie im vorherigen Abschnitt erstellt haben.

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

Damit wird ein JSON-Dokument mit Informationen zum Bereitstellungsvorgang zurückgegeben. Das `"provisioningState"`-Element enthält den Status der Bereitstellung; wenn dieses Element den Wert `"Succeeded"` enthält, wurde die Bereitstellung erfolgreich abgeschlossen. Jetzt kann Ihr Cluster verwendet werden.

##Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich erstellt haben, nutzen Sie die folgenden Informationen, um zu erfahren, wie Sie mit Ihrem Cluster arbeiten.

###Hadoop-Cluster

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

###HBase-Cluster

* [Erste Schritte mit HBase in HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Entwickeln von Java-Anwendungen für HBase in HDInsight](hdinsight-hbase-build-java-maven-linux)

###Storm-Cluster

* [Entwickeln von Java-Topologien für Storm in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten in Storm in HDInsight](hdinsight-storm-develop-python.md)
* [Bereitstellen und Überwachen von Topologien mit Storm in HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_1203_2015-->
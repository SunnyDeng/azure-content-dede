<properties
	pageTitle="Azure-Ressourcen-Manager-Richtlinien | Microsoft Azure"
	description="Beschreibt die Verwendung von Azure-Ressourcen-Manager-Richtlinien, um Verstöße in unterschiedlichen Gültigkeitsbereichen wie Abonnements, Ressourcengruppen oder einzelnen Ressourcen zu verhindern."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/10/2015"
	ms.author="gauravbh;tomfitz"/>

# Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung

Sie können nun den Azure-Ressourcen-Manager zum Steuern des Zugriffs über benutzerdefinierte Richtlinien verwenden. Mithilfe von Richtlinien können Sie Benutzer in Ihrer Organisation hindern, gegen Konventionen zu verstoßen, die für das Verwalten der Ressourcen Ihrer Organisation gelten.

Sie erstellen Richtliniendefinitionen, die die Aktionen oder Ressourcen beschreiben, die spezifisch verweigert werden. Sie weisen diese Richtliniendefinitionen dem gewünschten Ziel zu, z. B. einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource.

In diesem Artikel wird die grundlegende Struktur der Richtliniendefinitionssprache erläutert, mit der Sie Richtlinien erstellen. Anschließend wird beschrieben, wie Sie diese Richtlinien auf verschiedene Ziele anwenden. Abschließend finden Sie einige Beispiele dafür, wie Sie dies auch über die REST-API erreichen können.

Die Richtlinie ist zurzeit als Vorschau verfügbar.

## Worin unterscheidet sich dies von der rollenbasierten Zugriffssteuerung (RBAC)?

Es gibt einige wichtige Unterschiede zwischen Richtlinien und rollenbasierter Zugriffssteuerung. Als Erstes müssen Sie jedoch verstehen, dass Richtlinien und RBAC zusammenarbeiten. Um Richtlinien verwenden zu können, muss der Benutzer über die RBAC authentifiziert werden. Im Gegensatz zur RBAC stellen Richtlinien ein Standardsystem für das Zulassen und explizite Verweigern dar.

Die RBAC konzentriert sich auf die Aktionen, die ein **Benutzer** in verschiedenen Bereichen ausführen darf. Beispiel: Ein bestimmter Benutzer wird der Rolle „Mitwirkender“ für eine Ressourcengruppe in einem bestimmten Bereich hinzugefügt, sodass der Benutzer Änderungen an der Ressourcengruppe vornehmen darf.

Richtlinien konzentrieren sich auf Aktionen für **Ressourcen** in verschiedenen Bereichen. Beispielsweise können Sie über Richtlinien die Arten von Ressourcen steuern, die bereitgestellt werden können, oder die Standorte beschränken, an denen die Ressourcen bereitgestellt werden können.

## Häufige Szenarios

Ein allgemeines Szenario ist der Bedarf an Tags auf Abteilungsebene zum Zweck der verbrauchsbasierten Kostenzuteilung. Eine Organisation könnte beispielsweise Vorgänge nur zulassen, wenn die richtige Kostenstelle zugeordnet ist, andernfalls soll die Anfrage abgelehnt werden. Dadurch ist es möglich, die Abrechnung für die durchgeführten Vorgänge mit der richtigen Kostenstelle durchzuführen.

In einem weiteren gängigen Szenario möchte die Organisation die Standorte steuern, an denen Ressourcen erstellt werden. Oder sie möchte möglicherweise den Zugriff auf Ressourcen steuern, indem nur bestimmte Arten von Ressourcen bereitgestellt werden dürfen.

Auf ähnliche Weise kann eine Organisation den Dienstkatalog steuern oder die gewünschten Benennungskonventionen für Ressourcen erzwingen.

Mithilfe von Richtlinien können diese Szenarios so einfach wie unten beschrieben umgesetzt werden.

## Struktur von Richtliniendefinitionen

Richtliniendefinitionen werden mit JSON erstellt. Sie enthalten eine oder mehrere Bedingungen/logische Operatoren, die Aktionen und deren Auswirkungen definieren und damit festlegen, was geschieht, wenn die Bedingungen erfüllt sind.

Grundsätzlich enthält eine Richtlinie Folgendes:

**Bedingung/logische Operatoren**: eine Reihe von Bedingungen, die über einen Satz von logischen Operatoren beeinflusst werden.

**Effekt**: beschreibt die Auswirkungen, wenn die Bedingung erfüllt ist – verweigern oder überwachen. Ein Überwachungseffekt gibt ein Warnereignis im Dienstprotokoll aus. Administratoren können z. B. eine Richtlinie erstellen, die eine Überwachung verursacht, wenn jemand einen großen virtuellen Computer erstellt, und dann später die Protokolle überprüfen.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Logische Operatoren

Die unterstützten logischen Operatoren sind zusammen mit der Syntax nachfolgend aufgeführt:

| Name des Operators | Syntax |
| :------------- | :------------- |
| Not | "not" : {&lt;Bedingung oder Operator &gt;} |
| Und | "allOf" : [ {&lt;Bedingung1&gt;},{&lt;Bedingung2&gt;}] |
| Oder | "anyOf" : [ {&lt;Bedingung1&gt;},{&lt;Bedingung2&gt;}] |

Geschachtelte Bedingungen werden nicht unterstützt.

## Bedingungen

Eine Bedingung prüft, ob ein **Feld** oder eine **Quelle** bestimmte Kriterien erfüllt. Die Namen unterstützter Bedingungen sind zusammen mit der Syntax nachstehend aufgeführt:

| Name der Bedingung | Syntax |
| :------------- | :------------- |
| Equals | "equals" : "&lt;Wert&gt;" |
| Like | "like" : "&lt;Wert&gt;" |
| Contains | "contains" : "&lt;Wert&gt;"|
| Geben Sie in | "in" : [ "&lt;Wert1&gt;","&lt;Wert2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;Schlüsselname&gt;" |


## Felder und Quellen

Bedingungen werden mithilfe von Feldern und Quellen gebildet. Ein Feld stellt Eigenschaften in der Anforderungsnutzlast der Ressource dar. Eine Quelle stellt Merkmale der Anforderung selbst dar.

Die folgenden Felder und Quellen werden unterstützt:

Felder: **name**, **kind**, **type**, **location**, **tags**, **tags.***.

Quellen: **action**

Weitere Informationen zu Aktionen finden Sie unter [RBAC – Integrierte Rollen](active-directory/role-based-access-built-in-roles.md).

## Beispiele für Richtliniendefinitionen

Im Folgenden erfahren Sie, wie Sie die Richtlinie für die oben genannten Szenarios definieren.

### Verbrauchsbasierte Kostenzuteilung: Anfordern von Abteilungstags

Die Richtlinie unten verweigert alle Anforderungen, die kein Tag mit dem Schlüssel "costCenter" enthalten.

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### Geografische Compliance: Erzwingen von Ressourcenpfaden

Das folgende Beispiel zeigt eine Richtlinie, die alle Anfragen ablehnt, deren Standort nicht Nordeuropa oder Westeuropa ist.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Dienstverwaltung: Auswählen des Dienstkatalogs

Das folgende Beispiel veranschaulicht die Verwendung der Quelle. Es zeigt, dass Aktionen nur für Dienste des Typs "Microsoft.Resources/*", "Microsoft.Compute/*", "Microsoft.Storage/*" und "Microsoft.Network/*" zulässig sind. Alle anderen Anforderungen werden verweigert.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Benennungskonvention

Das folgende Beispiel veranschaulicht die Verwendung von Platzhalterzeichen, die von der Bedingung "like" unterstützt werden. Die Bedingung gibt an, dass die Anforderung verweigert wird, wenn der Name nicht mit dem angegebenen Muster (Namenspräfix*Namenssuffix) übereinstimmt.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

## Richtlinienzuweisung

Richtlinien können auf verschiedene Gültigkeitsbereiche wie Abonnements, Ressourcengruppen und einzelne Ressourcen angewendet werden. Richtlinien werden von allen untergeordneten Ressourcen geerbt. Wenn also eine Richtlinie auf eine Ressourcengruppe angewendet wird, gilt sie auch für alle Ressourcen in der Ressourcengruppe.

## Erstellen einer Richtlinie

Dieser Abschnitt enthält Informationen zum Erstellen einer Richtlinie mithilfe der REST-API.

### Erstellen der Richtliniendefinition mit der REST-API

Sie können eine Richtlinie mit der REST-API erstellen, wie unter [Policy Definitions](https://msdn.microsoft.com/library/azure/mt588471.aspx) (in englischer Sprache) beschrieben. Die REST-API ermöglicht es Ihnen, Richtliniendefinitionen zu erstellen und zu löschen sowie Informationen zu vorhandenen Definitionen abzurufen.

So erstellen Sie eine neue Richtlinie

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Der Anforderungstext sollte dem folgenden ähneln:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


Die Richtliniendefinition kann als eines der oben aufgeführten Beispiele definiert werden. Verwenden Sie die API-Version *2015-10-01-preview*. Weitere Beispiele und Informationen finden Sie unter [Policy Definitions](https://msdn.microsoft.com/library/azure/mt588471.aspx) (in englischer Sprache).

### Erstellen der Richtliniendefinition mit der PowerShell

Sie können eine neue Richtliniendefinition mithilfe des Cmdlets "New-AzureRmPolicyDefinition" erstellen, wie unten gezeigt. Das unten angeführte Beispiel erstellt eine Richtlinie, um Ressourcen nur in Nordeuropa und Westeuropa zuzulassen.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

Die Ausgabe der Ausführung wird im "$policy"-Objekt gespeichert, weil dieses später während der Richtlinienzuweisung verwendet werden kann. Als Richtlinienparameter kann auch der Pfad zu einer JSON-Datei, die die Richtlinie enthält, angegeben werden, anstatt die Richtlinie inline anzugeben, wie unten gezeigt.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Anwenden einer Richtlinie

### Zuweisen von Richtlinien mit der REST-API

Sie können die Richtliniendefinition mithilfe von [Policy Assignments](https://msdn.microsoft.com/library/azure/mt588466.aspx) (Richtlinienzuweisungen, in englischer Sprache) auf den gewünschten Bereich anwenden. Die REST-API ermöglicht es Ihnen, Richtlinienzuweisungen zu erstellen und zu löschen sowie Informationen zu vorhandenen Zuweisungen abzurufen.

Führen Sie zum Erstellen einer neuen Richtlinienzuweisung Folgendes aus:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

"{policy-assignment}" ist der Name der Richtlinienzuweisung. Verwenden Sie die API-Version *2015-10-01-preview*.

Der Anforderungstext sollte dem folgenden ähneln:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

Weitere Beispiele und Informationen finden Sie unter [Policy Assignments](https://msdn.microsoft.com/library/azure/mt588466.aspx) (Richtlinienzuweisungen, in englischer Sprache).

### Zuweisen von Richtlinien mit der PowerShell

Sie können die oben erstellte Richtlinie mithilfe der PowerShell für den gewünschten Bereich erstellen, indem Sie das Cmdlet "New-AzureRmPolicyAssignment" verwenden, wie unten gezeigt:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Hierbei ist "$policy" das Richtlinienobjekt, das als Ergebnis der Ausführung des Cmdlets "New-AzureRmPolicyDefinition" zurückgegeben wurde, wie oben gezeigt. Der Bereich ist in diesem Fall der Name der von Ihnen angegebenen Ressourcengruppe.

Wenn Sie die oben genannte Richtlinienzuweisung entfernen möchten, können Sie hierzu wie folgt vorgehen:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Sie können Richtliniendefinitionen mithilfe der Cmdlets "Get-AzureRmPolicyDefinition", "Set-AzureRmPolicyDefinition" bzw. "Remove-AzureRmPolicyDefinition" abrufen, ändern oder entfernen.

Ähnlich können Sie Richtlinienzuweisungen mithilfe der Cmdlets "Get-AzureRmPolicyAssignment", "Set-AzureRmPolicyAssignment" bzw. "Remove-AzureRmPolicyAssignment" abrufen, ändern oder entfernen.

<!---HONumber=Nov15_HO3-->
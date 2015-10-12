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
	ms.date="09/29/2015"
	ms.author="gauravbh;tomfitz"/>

# Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung

Sie können nun den Azure-Ressourcen-Manager zum Steuern des Zugriffs über benutzerdefinierte Richtlinien verwenden. Eine Richtlinie stellt eine oder mehrere Verstöße dar, die für den gewünschten Bereich verhindert werden können. Ein Bereich kann in diesem Fall ein Abonnement, eine Ressourcengruppe oder eine einzelne Ressource sein.

Richtlinien sind standardmäßig ein Zulassungssystem. Eine Richtlinie wird durch eine Richtliniendefinition definiert und durch die Richtlinienzuweisung angewendet. Mit Richtlinienzuweisungen können Sie den Gültigkeitsbereich für die Richtlinie festlegen.

In diesem Artikel wird die grundlegende Struktur der Richtliniendefinitionssprache erläutert, mit der Sie Richtlinien erstellen. Anschließend wird beschrieben, wie Sie diese Richtlinien auf verschiedene Bereiche anwenden. Abschließend finden Sie einige Beispiele dafür, wie Sie dies auch über die REST-API durchführen können. Die PowerShell-Unterstützung wird auch kurz angesprochen.

## Häufige Szenarios

Ein allgemeines Szenario ist der Bedarf an Tags auf Abteilungsebene zum Zweck der verbrauchsbasierten Kostenzuteilung. Eine Organisation möchte beispielsweise Vorgänge nur zulassen, wenn die richtige Kostenstelle zugeordnet ist. Andernfalls würde die Anforderung abgelehnt werden. Dadurch ist es möglich, die Abrechnung für die durchgeführten Vorgänge mit der richtigen Kostenstelle durchzuführen.

In einem weiteren gängigen Szenario möchte die Organisation die Standorte steuern, an denen Ressourcen erstellt werden. Oder sie möchte möglicherweise den Zugriff auf Ressourcen steuern, indem nur bestimmte Arten von Ressourcen bereitgestellt werden dürfen.

Auf ähnliche Weise kann eine Organisation den Dienstkatalog steuern oder die gewünschten Benennungskonventionen für Ressourcen erzwingen.

Mithilfe von Richtlinien können diese Szenarios so einfach wie unten beschrieben umgesetzt werden.

## Struktur von Richtliniendefinitionen

Richtliniendefinitionen werden mit JSON erstellt. Sie enthalten eine oder mehrere Bedingungen/logische Operatoren, die Aktionen und deren Auswirkungen definieren und damit festlegen, was geschieht, wenn die Bedingungen erfüllt sind.

Grundsätzlich enthält eine Richtlinie Folgendes:

**Bedingung/logische Operatoren**: eine Reihe von Bedingungen, die über einen Satz von logischen Operatoren beeinflusst werden.

**Effekt**: beschreibt die Auswirkungen, wenn die Bedingung erfüllt ist – verweigern oder überwachen. Ein Überwachungseffekt gibt ein Warnereignis im Dienstprotokoll aus. Ein Administrator kann z. B. eine Richtlinie erstellen, die eine Überwachung festlegt, wenn ein Benutzer einen großen virtueller Computer erstellt. Er kann dann diese Protokolle später überprüfen.

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
| Nicht | "not" : {&lt;Bedingung&gt;} |
| Und | "allOf" : [ {&lt;Bedingung1&gt;},{&lt;Bedingung2&gt;}] |
| Oder | "anyOf" : [ {&lt;Bedingung1&gt;},{&lt;Bedingung2&gt;}] |


## Bedingungen

Die unterstützten Bedingungen sind zusammen mit der Syntax nachfolgend aufgeführt:

| Name der Bedingung | Syntax |
| :------------- | :------------- |
| Equals | "equals" : "&lt;Wert&gt;" |
| Like | "like" : "&lt;Wert&gt;" |
| Contains | "contains" : "&lt;Wert&gt;"|
| Geben Sie in | "in" : [ "&lt;Wert1&gt;","&lt;Wert2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;Schlüsselname&gt;" |


## Felder und Quellen

Die Bedingungen werden mithilfe von Feldern und Quellen gebildet. Die folgenden Felder und Quellen werden unterstützt:

Felder: **name**, **kind**, **type**, **location**, **tags**, **tags.***.

Quellen: **action**

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
          "in" : ["north europe" , "west europe"]
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

Dieser Abschnitt enthält Informationen zum Erstellen einer Richtlinie mithilfe von REST-API und PowerShell.

### Erstellen der Richtliniendefinition mit der REST-API

Sie können eine Richtlinie mit der REST-API für Richtlinien erstellen. Die REST-API ermöglicht es Ihnen, Richtlinien zu erstellen und zu löschen sowie Informationen zu vorhandenen Richtlinien abzurufen.

So erstellen Sie eine neue Richtlinie

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Im Folgenden finden Sie ein Beispiel für einen Anforderungstext:

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


Die Richtliniendefinition kann als eines der oben aufgeführten Beispiele definiert werden. Verwenden Sie die API-Version *2015-10-01-preview*. Weitere Beispiele und Informationen finden Sie in der REST-API für Richtlinien.

## Anwenden einer Richtlinie

### Zuweisen von Richtlinien mit der REST-API

Sie können die Richtliniendefinition über die REST-API für die Richtlinienzuweisung auf den gewünschten Bereich anwenden. Sie können darüber hinaus mit der REST-API Richtlinienzuweisungen erstellen und löschen und Informationen zu vorhandenen Zuweisungen abrufen.

Führen Sie zum Erstellen einer neuen Richtlinienzuweisung Folgendes aus:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

"{policy-assignment}" ist der Name der Richtlinienzuweisung. Verwenden Sie die API-Version *2015-10-01-preview*. Weitere Beispiele und Informationen finden Sie in der REST-API für die Richtlinienzuweisung.

Im Folgenden finden Sie ein Beispiel für einen Anforderungstext:

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

<!---HONumber=Oct15_HO1-->
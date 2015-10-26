<properties
   pageTitle="Definieren von Abhängigkeiten in Azure-Ressourcen-Manager-Vorlagen"
   description="Beschreibt, wie während der Bereitstellung festlegt wird, dass eine Ressource von einer anderen Ressource abhängt."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="mmercuri"/>

# Definieren von Abhängigkeiten in Azure-Ressourcen-Manager-Vorlagen

Für eine bestimmte Ressource kann es mehrere Upstream- und untergeordnete Abhängigkeiten geben, die für den Erfolg Ihrer Topologie entscheidend sind. Sie können Abhängigkeiten von anderen Ressourcen mit den **dependsOn**- und **resources**-Eigenschaften einer Ressource definieren. Eine Abhängigkeit kann auch mithilfe der **reference**-Funktion angegeben werden.

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 Es gibt auch Ressourcenlinks, die Beziehungen zwischen Ressourcen definieren können und die Definition dieser Beziehungen in Ressourcengruppen unterstützen.

## dependsOn

Voraussetzung für einen virtuellen Computer kann sein, dass eine Datenbankressource erfolgreich bereitgestellt wurde. In einem anderen Fall können Sie davon abhängig sein, dass mehrere Knoten im Cluster installiert werden, bevor Sie einen virtuellen Computer mit dem Clusterverwaltungstool bereitstellen.

Innerhalb der Vorlage bietet die dependsOn-Eigenschaft die Möglichkeit, diese Abhängigkeit für eine Ressource zu definieren. Der Wert kann eine durch Trennzeichen getrennte Liste von Ressourcennamen sein. Die Abhängigkeiten zwischen den Ressourcen werden ausgewertet, und die Ressourcen werden in ihrer Abhängigkeitsreihenfolge bereitgestellt. Wenn Ressourcen nicht voneinander abhängig sind, wird versucht, sie parallel bereitzustellen.

Sie könnten geneigt sein, dependsOn zu verwenden, um Abhängigkeiten zwischen Ressourcen zuzuordnen, es ist jedoch wichtig zu verstehen, warum Sie es tun, da es sich auf die Leistung Ihrer Bereitstellung auswirken kann. Beispiel: Wenn Sie so dokumentieren möchten, wie Ressourcen miteinander verbunden sind, ist dependsOn nicht der richtige Ansatz. Der Lebenszyklus von dependsOn dient nur für die Bereitstellung und ist nach der Bereitstellung nicht verfügbar. Einmal bereitgestellt, gibt es keine Möglichkeit, diese Abhängigkeiten abzufragen. Durch die Verwendung von dependsOn besteht das Risiko einer Beeinträchtigung der Leistung, indem Sie versehentlich das Bereitstellungsmodul veranlassen könnten, keine Parallelität zu verwenden, wo dies andernfalls möglicherweise der Fall wäre. Verwenden Sie stattdessen [Ressourcenverknüpfungen](resource-group-link-resources.md) zum Dokumentieren und Bereitstellen von Abfragefunktionen für Beziehungen zwischen Ressourcen.

Dieses Element ist nicht erforderlich, wenn die reference-Funktion verwendet wird, um eine Darstellung einer Ressource abzurufen, da ein reference-Objekt eine Abhängigkeit von der Ressource bedeutet. Wenn die Option besteht, zwischen einem Verweis oder dependsOn zu wählen, empfiehlt sich die Verwendung der reference-Funktion und impliziter Verweise. Der Grund ist wieder die Leistung. Mit Verweisen werden implizite Abhängigkeiten definiert, die bekanntermaßen erforderlich sind, da in der Vorlage auf sie verwiesen wird. Durch ihr Vorhandensein sind sie relevant, vermeiden wiederum das Optimieren der Leistung und das potenzielle Risiko, das Bereitstellungsmodul unnötigerweise von Parallelität abzuhalten.

## resources

Mit der resources-Eigenschaft können Sie untergeordnete Ressourcen angeben, die mit der definierten Ressource verknüpft sind. Untergeordnete Ressourcen können nur mit fünf Ebenen definiert werden. Es ist wichtig zu beachten, dass keine implizite Abhängigkeit zwischen einer untergeordneten Ressource und der übergeordneten Ressource erstellt wird. Wenn die untergeordnete Ressource nach der übergeordneten Ressource bereitgestellt werden muss, müssen Sie diese Abhängigkeit explizit mit der dependsOn-Eigenschaft angeben.

## reference-Funktion

Mit der reference-Funktion kann ein Ausdruck seinen Wert von anderen JSON-Name/Wertpaaren oder Laufzeitressourcen ableiten. reference-Ausdrücke deklarieren implizit, dass eine Ressource von einer anderen abhängt. Die durch **propertyPath** unten dargestellte Eigenschaft ist optional. Wenn sie nicht angegeben ist, wird auf die Ressource verwiesen.

    reference('resourceName').propertyPath

Sie können dieses Element oder das dependsOn-Element verwenden, um Abhängigkeiten anzugeben, aber Sie müssen nicht beide für dieselbe abhängige Ressource verwenden. Es empfiehlt sich, den impliziten Verweis zu verwenden, um das Risiko zu vermeiden, dass versehentlich ein unnötiges dependsOn-Element das Bereitstellungsmodul davon abhält, Aspekte der Bereitstellung parallel auszuführen.

Weitere Informationen finden Sie unter [reference-Funktion](../resource-group-template-functions/#reference).

## Nächste Schritte

- Weitere Informationen zum Erstellen von Azure-Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md). 
- Eine Liste der verfügbaren Funktionen in einer Vorlage finden Sie unter [Vorlagenfunktionen](resource-group-template-functions.md).

<!---HONumber=Oct15_HO3-->
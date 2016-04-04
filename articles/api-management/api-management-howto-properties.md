<properties 
	pageTitle="Verwenden von Eigenschaften in Azure API Management-Richtlinien" 
	description="Erfahren Sie, wie Eigenschaften in Azure API Management-Richtlinien verwendet werden." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/18/2016" 
	ms.author="sdanie"/>


# Verwenden von Eigenschaften in Azure API Management-Richtlinien

API Management-Richtlinien sind eine leistungsfähige Funktion des Systems, mit der Herausgeber das Verhalten der API über eine Konfiguration ändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Richtlinienanweisungen können mithilfe von literalen Textwerten, Richtlinienausdrücken und Eigenschaften erstellt werden.

Jede API Management-Dienstinstanz weist eine Eigenschaftensammlung von Schlüssel-Wert-Paaren auf, die für die gesamte Dienstinstanz gelten. Diese Eigenschaften können zum Verwalten konstanter Zeichenfolgenwerte für alle API-Konfigurationen und -Richtlinien verwendet werden. Jede Eigenschaft verfügt über die folgenden Attribute.


| Attribut | Typ | Beschreibung |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Name | string | Der Name der Eigenschaft. Er kann nur Buchstaben, Ziffern, Punkte, Gedankenstriche und Unterstriche enthalten. |
| Wert | string | Der Wert der Eigenschaft. Er darf nicht leer sein oder nur aus Leerzeichen bestehen. |
| Geheimer Schlüssel | Boolescher Wert | Bestimmt, ob der Wert ein geheimer Schlüssel ist und ob er verschlüsselt werden sollte. |
| Tags | Array von Zeichenfolgen | Optionale Tags, die zum Filtern der Eigenschaftenliste verwendet werden können, wenn sie bereitgestellt werden. |

Eigenschaften werden im Herausgeberportal auf der Registerkarte **Eigenschaften** konfiguriert. Im folgenden Beispiel werden drei Eigenschaften konfiguriert.

![Eigenschaften][api-management-properties]

Eigenschaftswerte können Literalzeichenfolgen und [Richtlinienausdrücke](https://msdn.microsoft.com/library/azure/dn910913.aspx) enthalten. Die folgende Tabelle zeigt die drei vorherigen Eigenschaften und ihre Attribute. Der Wert von `ExpressionProperty` ist ein Richtlinienausdruck, der eine Zeichenfolge zurückgibt, die das aktuelle Datum und die Uhrzeit enthält. Die Eigenschaft `ContosoHeaderValue` ist als geheimer Schlüssel markiert, sodass ihr Wert nicht angezeigt wird.

| Name | Wert | Geheimer Schlüssel | Tags |
|--------------------|----------------------------|--------|---------|
| ContosoHeader | TrackingId | False | Contoso |
| ContosoHeaderValue | •••••••••••••••••••••• | True | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | False | |

## So verwenden Sie eine Eigenschaft

Um eine Eigenschaft in einer Richtlinie zu verwenden, platzieren Sie den Namen der Eigenschaft in ein doppeltes Paar geschweifter Klammern wie `{{ContosoHeader}}`, wie im folgenden Beispiel gezeigt.

	<set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

In diesem Beispiel dient `ContosoHeader` als Name eines Headers in einer `set-header`-Richtlinie und `ContosoHeaderValue` als Wert dieses Headers. Wenn diese Richtlinie in einer Anforderung oder Antwort an das API Management-Gateway ausgewertet wird, werden `{{ContosoHeader}}` und `{{ContosoHeaderValue}}` durch ihre jeweiligen Eigenschaftswerte ersetzt.

Eigenschaften können als vollständige Attribut- oder Elementwerte verwendet werden, wie im vorherigen Beispiel gezeigt, sie können aber auch in einen literalen Textausdruck eingefügt oder mit einem Teil davon kombiniert werden, wie im folgenden Beispiel gezeigt: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Eigenschaften können auch Richtlinienausdrücke enthalten. Im folgenden Beispiel wird `ExpressionProperty` verwendet.

	<set-header name="CustomHeader" exists-action="override">
		<value>{{ExpressionProperty}}</value>
	</set-header>

Wenn diese Richtlinie ausgewertet wird, wird `{{ExpressionProperty}}` durch ihren Wert ersetzt: `@(DateTime.Now.ToString())`. Da der Wert ein Richtlinienausdruck ist, wird der Ausdruck ausgewertet, und die Ausführung der Richtlinie wird fortgesetzt.

Sie können dies im Entwicklerportal durch Aufrufen eines Vorgangs testen, der eine Richtlinie mit entsprechenden Eigenschaften aufweist. Im folgenden Beispiel wird ein Vorgang mit den beiden vorherigen `set-header`-Beispielrichtlinien mit Eigenschaften aufgerufen. Beachten Sie, dass die Antwort zwei benutzerdefinierte Header enthält, die mithilfe von Richtlinien mit Eigenschaften konfiguriert wurden.

![Entwicklerportal][api-management-send-results]

Beim Betrachten der [Verfolgung mit dem API-Inspektor](api-management-howto-api-inspector.md) für einen Aufruf, der die beiden vorherigen Beispielrichtlinien mit Eigenschaften enthält, sehen Sie beide `set-header`-Richtlinien mit den eingefügten Eigenschaftswerten und die Auswertung des Richtlinienausdrucks für die Eigenschaft, die den Richtlinienausdruck enthält.

![Verfolgung mit dem API-Inspektor][api-management-api-inspector-trace]

Beachten Sie, dass Eigenschaftswerte zwar Richtlinienausdrücke enthalten können, Eigenschaftswerte aber keine anderen Eigenschaften enthalten können. Wenn Text, der einen Eigenschaftsverweis enthält, als Eigenschaftswert verwendet wird, z. B. `Property value text {{MyProperty}}`, wird dieser Eigenschaftsverweis nicht ersetzt, und er wird als Bestandteil des Eigenschaftswerts eingebunden.

## So erstellen Sie eine Eigenschaft

Klicken Sie zum Erstellen einer Eigenschaft auf der Registerkarte **Eigenschaften** auf **Eigenschaft hinzufügen**.

![Eigenschaft hinzufügen][api-management-properties-add-property-menu]

**Name** und **Wert** sind erforderliche Werte. Wenn dieser Eigenschaftswert ein geheimer Schlüssel ist, aktivieren Sie das Kontrollkästchen **Dies ist ein geheimer Schlüssel**. Geben Sie ein oder mehrere optionale Tags ein, um Ihre Eigenschaften besser organisieren zu können, und klicken Sie auf **Speichern**.

![Eigenschaft hinzufügen][api-management-properties-add-property]

Wenn eine neue Eigenschaft gespeichert wird, wird der Name der neuen Eigenschaft in das Textfeld **Eigenschaft suchen** eingetragen, und die neue Eigenschaft wird angezeigt. Um alle Eigenschaften anzuzeigen, löschen Sie den Inhalt des Textfelds **Eigenschaft suchen**, und drücken Sie EINGABETASTE.

![Eigenschaften][api-management-properties-property-saved]

Informationen zum Erstellen einer Eigenschaft mithilfe der REST-API finden Sie unter [Erstellen einer Eigenschaft mit der REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## So bearbeiten Sie eine Eigenschaft

Klicken Sie zum Bearbeiten einer Eigenschaft neben der zu bearbeitenden Eigenschaft auf **Bearbeiten**.

![Eigenschaft bearbeiten][api-management-properties-edit]

Nehmen Sie die gewünschten Änderungen vor, und klicken Sie auf **Speichern**. Wenn Sie den Namen der Eigenschaft ändern, werden alle Richtlinien, die auf diese Eigenschaft verweisen, automatisch aktualisiert, sodass sie den neuen Namen verwenden.

![Eigenschaft bearbeiten][api-management-properties-edit-property]

Informationen zum Bearbeiten einer Eigenschaft mithilfe der REST-API finden Sie unter [Bearbeiten einer Eigenschaft mit der REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## So löschen Sie eine Eigenschaft

Klicken Sie zum Löschen einer Eigenschaft neben der zu löschenden Eigenschaft auf **Löschen**.

![Eigenschaft löschen][api-management-properties-delete]

Klicken Sie zur Bestätigung auf **Ja, löschen**.

![Bestätigen des Löschens][api-management-delete-confirm]

>[AZURE.IMPORTANT] Wenn von Richtlinien auf die Eigenschaft verwiesen wird, können Sie sie erst erfolgreich löschen, nachdem Sie die Eigenschaft aus allen Richtlinien entfernt haben, die sie verwenden.

Informationen zum Löschen einer Eigenschaft mithilfe der REST-API finden Sie unter [Löschen einer Eigenschaft mit der REST-API](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## So suchen und filtern Sie Eigenschaften

Die Registerkarte **Eigenschaften** umfasst Such- und Filterfunktionen, die Ihnen beim Verwalten Ihrer Eigenschaften helfen. Geben Sie zum Filtern der Eigenschaftenliste nach einem Eigenschaftennamen einen Suchbegriff in das Textfeld **Eigenschaft suchen** ein. Um alle Eigenschaften anzuzeigen, löschen Sie den Inhalt des Textfelds **Eigenschaft suchen**, und drücken Sie EINGABETASTE.

![Suche][api-management-properties-search]

Geben Sie zum Filtern der Eigenschaftenliste nach Tagwerten ein oder mehrere Tags in das Textfeld **Nach Tags filtern** ein. Um alle Eigenschaften anzuzeigen, löschen Sie den Inhalt des Textfelds **Nach Tags filtern**, und drücken Sie EINGABETASTE.

![Filter][api-management-properties-filter]

## Nächste Schritte

-	Weitere Informationen zum Arbeiten mit Richtlinien
	-	[Richtlinien in Azure API Management](api-management-howto-policies.md)
	-	[Richtlinienreferenz](https://msdn.microsoft.com/library/azure/dn894081.aspx)
	-	[Richtlinienausdrücke](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## Überblicksvideo ansehen

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

<!---HONumber=AcomDC_0323_2016-->
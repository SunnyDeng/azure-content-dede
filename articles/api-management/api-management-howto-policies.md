<properties 
	pageTitle="Richtlinien in Azure API Management" 
	description="Erfahren Sie, wie Sie Richtlinien in API Management erstellen, bearbeiten und konfigurieren." 
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
	ms.date="03/04/2016" 
	ms.author="sdanie"/>


#Richtlinien in Azure API Management

Richtlinien sind ein umfassendes Werkzeug in Azure API Management, mit dem Anbieter das Verhalten der API über eine Konfiguration verändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Aufrufbeschränkungen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind vorkonfiguriert verfügbar.

In der [Richtlinienreferenz][] finden Sie eine komplette Liste der Richtlinienanweisungen und deren Einstellungen.

Richtlinien werden im Gateway, das sich zwischen API-Consumer und der verwalteten API befindet, angewendet. Das Gateway empfängt alle Anfragen und leitet diese normalerweise unverändert an die zugrunde liegende API weiter. Richtlinien können jedoch Änderungen an der eingehenden Anfrage und an der ausgehenden Antwort vornehmen.

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung][] und [Variable festlegen][], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][] und [Richtlinienausdrücke][].

## <a name="scopes"> </a>Konfigurieren von Richtlinien
Richtlinien können entweder global oder im Geltungsbereich eines [Produkts][], einer [API][] oder einer [Operation][] konfiguriert werden. Um Richtlinien zu konfigurieren, navigieren Sie im Herausgeberportal zum Richtlinien-Editor.

![Menü "Richtlinien"][policies-menu]

Der Richtlinien-Editor umfasst drei Hauptabschnitte: den Geltungsbereich der Richtlinie (oben), die Richtliniendefinition zum Bearbeiten der Richtlinie (links) und die Liste der Anweisungen (rechts):

![Richtlinien-Editor][policies-editor]

Um eine Richtlinie zu konfigurieren, müssen Sie zunächst deren gewünschten Geltungsbereich auswählen. Im folgenden Screenshot wird das Produkt **Starter** ausgewählt. Das rechteckige Symbol neben dem Richtliniennamen gibt an, dass eine Richtlinie bereits auf dieser Ebene angewendet wird.

![Umfang][policies-scope]

Da bereits eine Richtlinie angewendet wurde, wird die Konfiguration in der Definitionsansicht angezeigt.

![Konfigurieren][policies-configure]

Die Richtlinie wird zunächst schreibgeschützt angezeigt. Klicken Sie auf die Aktion **Richtlinie bearbeiten**, um die Definition zu bearbeiten.

![Bearbeiten][policies-edit]

Die Richtliniendefinition ist ein einfaches XML-Dokument, das eine Sequenz eingehender und ausgehender Anweisungen beschreibt. Das XML-Dokument kann direkt im Definitionsfenster bearbeitet werden. Auf der rechten Seite sehen Sie eine Liste mit Anweisungen, und die für den aktuellen Geltungsbereich anwendbaren Anweisungen sind aktiviert und hervorgehoben, wie Sie am Beispiel der Anweisung **Limit Call Rate** im obigen Screenshot sehen können.

Wenn Sie auf eine aktivierte Anweisung klicken, wird der entsprechende XML-Ausschnitt an der Cursorposition in der Definitionsansicht eingefügt.

In der [Richtlinienreferenz][] finden Sie eine komplette Liste der Richtlinienanweisungen und deren Einstellungen.

Um eine neue Anweisung zur Einschränkung eingehender Anfragen auf bestimmte IP-Adressen zu erstellen, platzieren Sie den Cursor innerhalb des `inbound`-XML-Elements, und klicken Sie auf die Anweisung **Restrict caller IPs**.

![Einschränkungsrichtlinien][policies-restrict]

Daraufhin wird ein XML-Ausschnitt in das `inbound`-Element eingefügt, der Anweisungen zur Konfiguration der Anweisung enthält.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

Ändern Sie den XML-Ausschnitt wie folgt, um nur eingehende Anfragen von der IP-Adresse 1.2.3.4 zu erlauben:

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Speichern][policies-save]

Konfigurieren Sie die Anweisungen für die Richtlinie, und klicken Sie auf **Speichern**. Daraufhin werden die Änderungen sofort an das API Management-Gateway weitergeleitet.

##<a name="sections"> </a>Grundlegendes zur Richtlinienkonfiguration

Richtlinien sind Serien von Anweisungen, die in der Reihenfolge für Anfrage und Antwort ausgeführt werden. Die Konfiguration ist entsprechend in `inbound`-, `backend`-, `outbound`- und `on-error`-Abschnitte unterteilt. Ein Beispiel hierfür sehen Sie in der folgenden Konfiguration.

	<policies>
	  <inbound>
	    <!-- statements to be applied to the request go here -->
	  </inbound>
	  <backend>
	    <!-- statements to be applied before the request is forwarded to 
	         the backend service go here -->
	  </backend>
	  <outbound>
	    <!-- statements to be applied to the response go here -->
	  </outbound>
	  <on-error>
	    <!-- statements to be applied if there is an error condition go here -->
	  </on-error>
	</policies> 

Wenn bei der Verarbeitung einer Anfrage ein Fehler auftritt, werden alle verbleibenden Schritte in den `inbound`-, `backend`- oder `outbound`-Abschnitten übersprungen und die Ausführung bei den Anweisungen im `on-error`-Abschnitt fortgesetzt. Durch Platzieren von Richtlinienanweisungen im `on-error`-Abschnitt können Sie den Fehler überprüfen, indem Sie die `context.LastError`-Eigenschaft verwenden, die Fehlerantwort mit der `set-body`-Richtlinie untersuchen und anpassen sowie konfigurieren, was geschieht, wenn ein Fehler auftritt. Es gibt Fehlercodes für integrierte Schritte und für Fehler, die während der Verarbeitung von Richtlinienanweisungen auftreten können. Weitere Informationen finden Sie unter [Error handling in API Management policies](https://msdn.microsoft.com/library/azure/mt629506.aspx) (in englischer Sprache).

Da Richtlinien auf unterschiedlichen Ebenen angegeben werden können (global, Produkt, API und Operation), können Sie in der Konfiguration die Reihenfolge angeben, in der die Anweisungen der Definition mit Bezug zur übergeordneten Richtlinie ausgeführt werden sollen.

Richtlinienbereiche werden in der folgenden Reihenfolge ausgewertet.

1. Globaler Bereich
2. Produktbereich
3. API-Bereich
4. Vorgangsbereich

Die Anweisungen in diesen Bereichen werden entsprechend der Platzierung des `base`-Elements ausgewertet, sofern es vorhanden ist.

Wenn Sie z. B. eine Richtlinie auf der globalen Ebene und eine Richtlinie für eine API konfiguriert haben, dann werden immer beide Richtlinien angewendet, wenn diese API aufgerufen wird. API Management ermöglicht eine deterministische Festlegung der Reihenfolge kombinierter Richtlinienanweisungen über das Basiselement.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

In der obigen Beispiel-Richtliniendefinition wird die `cross-domain`-Anweisung vor allen übergeordneten Richtlinien ausgeführt, auf die wiederum die `find-and-replace`-Richtlinie folgt.

Wenn in einer Richtlinienanweisung dieselbe Richtlinie zweimal angezeigt wird, wird die zuletzt ausgewertete Richtlinie angewendet. Auf diese Weise können Sie Richtlinien außer Kraft setzen, die in einem übergeordneten Bereich definiert sind. Um die Richtlinien im aktuellen Bereich im Richtlinien-Editor anzuzeigen, klicken Sie auf **Recalculate effective policy for selected scope**.

Beachten Sie, dass eine globale Richtlinie über keine übergeordnete Richtlinie verfügt und dass daher die Verwendung des `<base>`-Elements in der Richtlinie keinerlei Auswirkung hat.

## Nächste Schritte

Sehen Sie sich das folgende Video zu Richtlinienausdrücken an.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Richtlinienreferenz]: api-management-policy-reference.md
[Produkts]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis
[Operation]: api-management-howto-add-operations.md

[Erweiterte Richtlinien]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Ablaufsteuerung]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Variable festlegen]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Richtlinienausdrücke]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!---HONumber=AcomDC_0309_2016-->
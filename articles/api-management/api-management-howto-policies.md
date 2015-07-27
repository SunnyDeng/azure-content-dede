<properties 
	pageTitle="Richtlinien in Azure API Management" 
	description="Erfahren Sie, wie Sie Richtlinien in API Management erstellen, bearbeiten und konfigurieren." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="sdanie"/>


#Richtlinien in Azure API Management

Richtlinien sind ein umfassendes Werkzeug in Azure API Management, mit dem Anbieter das Verhalten der API über eine Konfiguration verändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Aufrufbeschränkungen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind vorkonfiguriert verfügbar.

In der [Richtlinienreferenz][] finden Sie eine komplette Liste der Richtlinienanweisungen und deren Einstellungen.

Richtlinien werden im Proxy angewendet, der sich zwischen API-Consumer und der verwalteten API befindet. Der Proxy empfängt alle Anfragen und leitet diese normalerweise unverändert an die zugrunde liegende API weiter. Richtlinien können jedoch Änderungen an der eingehenden Anfrage und an der ausgehenden Antwort vornehmen.

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung][] und [Variable festlegen][], basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien][] und [Richtlinienausdrücke][].

## <a name="scopes"> </a>Konfigurieren von Richtlinien
Richtlinien können entweder global oder im Geltungsbereich eines [Produkts][], einer [API][] oder einer [Operation][] konfiguriert werden. Um Richtlinien zu konfigurieren, navigieren Sie zum Richtlinien-Editor im Veröffentlichungsportal.

![Menü "Richtlinien"][policies-menu]

Der Richtlinien-Editor umfasst drei Hauptabschnitte: den Geltungsbereich der Richtlinie (oben), die Richtliniendefinition zum Bearbeiten der Richtlinie (links) und die Liste der Anweisungen (rechts):

![Richtlinien-Editor][policies-editor]

Um eine Richtlinie zu konfigurieren, müssen Sie zunächst deren gewünschten Geltungsbereich auswählen. Im folgenden Screenshot wird das Starter-Produkt ausgewählt. Das rechteckige Symbol neben dem Richtliniennamen gibt an, dass eine Richtlinie bereits auf dieser Ebene angewendet wird.

![Umfang][policies-scope]

Da bereits eine Richtlinie angewendet wurde, wird die Konfiguration in der Definitionsansicht angezeigt.

![Konfigurieren][policies-configure]

Die Richtlinie wird zunächst schreibgeschützt angezeigt. Klicken Sie auf die Aktion **Richtlinie bearbeiten**, um die Definition zu bearbeiten.

![Bearbeiten][policies-edit]

Die Richtliniendefinition ist ein einfaches XML-Dokument, das eine Sequenz eingehender und ausgehender Anweisungen beschreibt. Das XML-Dokument kann direkt im Definitionsfenster bearbeitet werden. Auf der rechten Seite sehen Sie eine Liste mit Anweisungen, und die für den aktuellen Geltungsbereich anwendbaren Anweisungen sind aktiviert und hervorgehoben, wie Sie am Beispiel der Aufrufbeschränkung im obigen Screenshot sehen können.

Wenn Sie auf eine aktivierte Anweisung klicken, wird der entsprechende XML-Ausschnitt an der Cursorposition in der Definitionsansicht eingefügt.

In der [Richtlinienreferenz][] finden Sie eine komplette Liste der Richtlinienanweisungen und deren Einstellungen.

Um eine neue Anweisung zur Einschränkung eingehender Anfragen auf bestimmte IP-Adressen zu erstellen, platzieren Sie den Cursor innerhalb des inbound-XML-Elements und klicken Sie auf die Anweisung Aufrufer-IP einschränken.

![Einschränkungsrichtlinien][policies-restrict]

Daraufhin wird ein XML-Ausschnitt in das inbound-Element eingefügt, der Anweisungen zur Konfiguration der Anweisung enthält.

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

Ändern Sie den XML-Ausschnitt wie folgt, um nur eingehende Anfragen von der IP-Adresse 1.2.3.4 zu erlauben:

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Speichern][policies-save]

Konfigurieren Sie die Anweisungen für die Richtlinie und klicken Sie auf Speichern. Daraufhin werden die Änderungen sofort an den API Management-Proxy weitergeleitet.

##<a name="sections"> </a>Grundlegendes zur Richtlinienkonfiguration

Richtlinien sind Serien von Anweisungen, die in der Reihenfolge für Anfrage und Antwort ausgeführt werden. Die Konfiguration ist entsprechend in eine eingehende (Anfrage) und ausgehende (Antwort) Richtlinie unterteilt.

	<policies>
		<inbound>
			<!-- statements to be applied to the request go here -->
		</inbound>
		<outbound>
			<!-- statements to be applied to the response go here -->
		</outbound>
	</policies>

Da Richtlinien auf unterschiedlichen Ebenen angegeben werden können (global, Produkt, API und Operation), können Sie in der Konfiguration die Reihenfolge angeben, in der die Anweisungen dieser Definition mit Bezug zur übergeordneten Richtlinie ausgeführt werden sollen.

Wenn Sie z. B. eine Richtlinie auf der globalen Ebene und eine Richtlinie für eine API konfiguriert haben, dann werden immer beide Richtlinien angewendet, wenn diese API aufgerufen wird. API Management ermöglicht eine deterministische Festlegung der Reihenfolge kombinierter Richtlinienanweisungen über das Basiselement.

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

In der obigen Beispiel-Richtliniendefinition würde die domänenübergreifende Anweisung vor allen übergeordneten Richtlinien ausgeführt, die wiederum von der "Suchen und ersetzen"-Richtlinie gefolgt werden.

Hinweis: Eine globale Richtlinie besitzt keine übergeordnete Richtlinie, und die Verwendung des `<base>`-Elements in der Richtlinie hat keinerlei Auswirkung.

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

<!---HONumber=July15_HO3-->
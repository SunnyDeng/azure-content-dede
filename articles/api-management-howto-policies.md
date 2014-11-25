<properties pageTitle="Policies in Azure API Management" metaKeywords="" description="Learn how to create, edit, and configure policies in API Management." metaCanonical="" services="" documentationCenter="API Management" title="Policies in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Richtlinien in der Azure API-Verwaltung

Richtlinien sind ein umfassendes Werkzeug in der API-Verwaltung (Vorschau), mit dem Anbieter das Verhalten der API in Form von Konfigurationen verändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Aufrufbeschränkungen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind vorkonfiguriert verfügbar.

In der [Richtlinienreferenz][Richtlinienreferenz] finden Sie eine komplette Liste der Richtlinienanweisungen und deren Einstellungen.

Richtlinien werden im Proxy angewendet, der sich zwischen API-Consumer und der verwalteten API befindet. Der Proxy empfängt alle Anfragen und leitet diese normalerweise unverändert an die zugrunde liegende API weiter. Richtlinien können jedoch Änderungen an der eingehenden Anfrage und an der ausgehenden Antwort vornehmen.

## Konfigurieren von Richtlinien

Richtlinien können entweder global oder im Geltungsbereich eines [Produkts][Produkts], einer [API][API] oder einer [Operation][Operation] konfiguriert werden. Um Richtlinien zu konfigurieren, navigieren Sie zum Richtlinien-Editor im Veröffentlichungsportal.

![Richtlinienmenü][Richtlinienmenü]

Der Richtlinien-Editor besteht aus drei Hauptbereichen: der Richtlinien-Geltungsbereich (oben), die Richtliniendefinition, in der Richtlinien bearbeitet werden (links) und die Liste der Anweisungen (rechts):

![Richtlinieneditor][Richtlinieneditor]

Um eine Richtlinie zu konfigurieren, müssen Sie zunächst deren gewünschten Geltungsbereich auswählen. Im folgenden Screenshot wird das 15-tägige kostenlose Testprodukt ausgewählt. Das rechteckige Symbol neben dem Richtliniennamen gibt an, dass eine Richtlinie bereits auf dieser Ebene angewendet wird.

![Bereich][Bereich]

Da bereits eine Richtlinie angewendet wurde, wird die Konfiguration in der Definitionsansicht angezeigt.

![Configure][Configure]

Die Richtlinie wird zunächst schreibgeschützt angezeigt. Klicken Sie auf die Aktion Richtlinie bearbeiten, um die Definition zu bearbeiten.

![Bearbeiten][Bearbeiten]

Die Richtliniendefinition ist ein einfaches XML-Dokument, das eine Sequenz eingehender und ausgehender Anweisungen beschreibt. Das XML-Dokument kann direkt im Definitionsfenster bearbeitet werden. Auf der rechten Seite sehen Sie eine Liste mit Anweisungen, und die für den aktuellen Geltungsbereich anwendbaren Anweisungen sind aktiviert und hervorgehoben, wie Sie am Beispiel der Aufrufbeschränkung im obigen Screenshot sehen können.

Wenn Sie auf eine aktivierte Anweisung klicken, wird der entsprechende XML-Ausschnitt an der Cursorposition in der Definitionsansicht eingefügt.

In der [Richtlinienreferenz][Richtlinienreferenz] finden Sie eine komplette Liste der Richtlinienanweisungen und deren Einstellungen.

Um eine neue Anweisung zur Einschränkung eingehender Anfragen auf bestimmte IP-Adressen zu erstellen, platzieren Sie den Cursor innerhalb des „inbound“-XML-Elements und klicken Sie auf die Anweisung Aufrufer-IP einschränken.

![Einschränkungsrichtlinien][Einschränkungsrichtlinien]

Daraufhin wird ein XML-Ausschnitt in das „inbound“-Element eingefügt, der Anweisungen zur Konfiguration der Anweisung enthält.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Ändern Sie den XML-Ausschnitt wie folgt, um nur eingehende Anfragen von der IP-Adresse 1.2.3.4 zu erlauben:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Speichern][Speichern]

Konfigurieren Sie die Anweisungen für die Richtlinie und klicken Sie auf Speichern. Daraufhin werden die Änderungen sofort an den Proxy der API-Verwaltung weitergeleitet.

# Erläuterungen zur Richtlinienkonfiguration

Richtlinien sind Serien von Anweisungen, die in der Reihenfolge für Anfrage und Antwort ausgeführt werden. Die Konfiguration ist entsprechend in eine eingehende (Anfrage) und ausgehende (Antwort) Richtlinie unterteilt.

    <policies>
        <inbound>
            <!-- statements to be applied to the request go here -->
        </inbound>
        <outboud>
            <!-- statements to be applied to the response go here -->
        <outbound>
    </policies>

Da Richtlinien auf unterschiedlichen Ebenen angegeben werden können (global, Produkt, API und Operation), können Sie in der Konfiguration die Reihenfolge angeben, in der die Anweisungen dieser Definition mit Bezug zur übergeordneten Richtlinie ausgeführt werden sollen.

Wenn Sie z. B. eine Richtlinie auf der globalen Ebene und eine Richtlinie für eine API konfiguriert haben, dann werden immer beide Richtlinien angewendet, wenn diese API aufgerufen wird. Die API-Verwaltung ermöglicht eine deterministische Festlegung der Reihenfolge kombinierter Richtlinienanweisungen über das Basiselement.

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

In der obigen Beispiel-Richtliniendefinition würde die domänenübergreifende Anweisung vor allen übergeordneten Richtlinien ausgeführt, die wiederum von der „Suchen und ersetzen“-Richtlinie gefolgt werden.

Hinweis: Globale Richtlinien haben keine übergeordneten Richtlinien, da das *base*-Element entweder immer *no-op* ist oder keinen Effekt hat.

  [Richtlinienreferenz]: ../api-management-policy-reference
  [Produkts]: ../api-management-howto-add-products
  [API]: ../api-management-howto-add-products/#add-apis
  [Operation]: ../api-management-howto-add-operations
  [Richtlinienmenü]: ./media/api-management-howto-policies/api-management-policies-menu.png
  [Richtlinieneditor]: ./media/api-management-howto-policies/api-management-policies-editor.png
  [Bereich]: ./media/api-management-howto-policies/api-management-policies-scope.png
  [Configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
  [Bearbeiten]: ./media/api-management-howto-policies/api-management-policies-edit.png
  [Einschränkungsrichtlinien]: ./media/api-management-howto-policies/api-management-policies-restrict.png
  [Speichern]: ./media/api-management-howto-policies/api-management-policies-save.png

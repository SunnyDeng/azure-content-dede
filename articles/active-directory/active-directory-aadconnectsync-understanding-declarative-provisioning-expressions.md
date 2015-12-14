<properties
	pageTitle="Azure AD Connect-Synchronisierung: Grundlegendes zu Ausdrücken für die deklarative Bereitstellung | Microsoft Azure"
	description="Erläutert die Ausdrücke für die deklarative Bereitstellung."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Grundlegendes zu Ausdrücken für die deklarative Bereitstellung

Die Azure AD Connect-Synchronisierung basiert auf der erstmals in Forefront Identity Manager 2010 eingeführten deklarativen Bereitstellung, die Ihnen ermöglicht, Ihre gesamte Geschäftslogik für die Identitätsintegration zu implementieren, ohne kompilierten Code schreiben zu müssen.

Ein wesentlicher Bestandteil der deklarativen Bereitstellung ist die in den Attributflüssen verwendete Ausdruckssprache. Die verwendete Sprache ist eine Teilmenge von Microsoft ® Visual Basic ® for Applications (VBA). Diese Sprache wird in Microsoft Office verwendet, und Benutzer mit Erfahrungen mit VBScript werden sie wiedererkennen. Die Ausdruckssprache für die deklarative Bereitstellung verwendet nur Funktionen und ist keine strukturierte Sprache; es gibt keine Methoden oder Anweisungen. Funktionen werden stattdessen verschachtelt, um den Programmablauf auszudrücken.

Weitere Informationen finden Sie unter [Willkommen bei der VBA-Sprachreferenz für Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Die Attribute sind stark typisiert. Eine Funktion, die ein einwertiges Zeichenfolgenattribut erwartet, wird weder mehrwertige Attribute noch Attribute eines anderen Typs akzeptieren. Zudem muss die Groß- und Kleinschreibung beachtet werden. Sowohl bei Funktions- als auch bei Attributnamen muss die Groß- und Kleinschreibung richtig sein, ansonsten wird ein Fehler ausgegeben.

## Sprachdefinitionen und Bezeichner

- Funktionen verfügen über einen Namen, gefolgt von Argumenten in Klammern: FunctionName(argument 1,argument N).
- Attribute werden durch eckige Klammern gekennzeichnet: [attributeName].
- Parameter werden durch Prozentzeichen gekennzeichnet: %ParameterName%.
- Zeichenfolgenkonstanten sind in Anführungszeichen eingeschlossen. Beispiel: "Contoso". Hinweis: Verwenden Sie keine typografischen („“), sondern gerade Anführungszeichen ("").
- Numerische Werte werden ohne Anführungszeichen ausgedrückt und im Dezimalformat vorliegen. Hexadezimalwerten weisen das Präfix "&H" auf. Beispiel: 98052, &HFF.
- Boolesche Werte werden mit Konstanten ausgedrückt: True, False.
- Integrierte Konstanten werden nur mit ihrem Namen ausgedrückt: NULL, CRLF, IgnoreThisFlow

### Funktionen
Bei der deklarativen Bereitstellung werden viele Funktionen verwendet, um das Transformieren von Attributwerten zu ermöglichen. Diese können geschachtelt werden, damit das Ergebnis einer Funktion an eine andere Funktion übergeben wird.

Funktionen können auch auf ein mehrwertiges Attribut angewendet werden. In diesem Fall wird die Funktion für jeden einzelnen Wert ausgeführt. Dabei wird die gleiche Funktion auf jeden Wert angewendet. Mit `Trim([proxyAddresses])` wird beispielsweise die Trim-Funktion für jeden Wert im Attribut „proxyAddress“ ausgeführt.

Die vollständige Liste der Funktionen finden Sie in der [Funktionsreferenz](active-directory-aadconnectsync-functions-reference.md).

### Parameter

Ein Parameter wird entweder durch einen Connector oder einen Administrator definiert, der PowerShell zum Festlegen verwendet. Parameter enthalten üblicherweise Werte, die sich je nach System unterscheiden, z. B. der Name der Domäne, in der sich der Benutzer befindet. Diese Werte können in Attributflüssen verwendet werden.

Der Active Directory Connector stellt folgende Parameter für eingehende Synchronisierungsregeln bereit:

| Parametername | Kommentar |
| --- | --- |
| Domain.Netbios | NetBIOS-Format der Domäne, die gerade importiert wird, z. B. „FABRIKAMSALES“ |
| Domain.FQDN | FQDN-Format der Domäne, die gerade importiert wird, z. B. „sales.fabrikam.com“ |
| Domain.LDAP | LDAP-Format der Domäne, die gerade importiert wird, z. B. „DC=sales,DC=fabrikam,DC=com“ |
| Forest.Netbios | NetBIOS-Format des Gesamtstrukturnamens, der gerade importiert wird, z. B. „FABRIKAMCORP“ |
| Forest.FQDN | FQDN-Format des Gesamtstrukturnamens, der gerade importiert wird, z. B. „fabrikam.com“ |
| Forest.LDAP | LDAP-Format des Gesamtstrukturnamens, der gerade importiert wird, z. B. „DC=fabrikam,DC=com“ |

Das System stellt den folgenden Parameter bereit, mit dem der Bezeichner des derzeit ausgeführten Connectors abgerufen wird:

`Connector.ID`

Ein Beispiel, in dem die Metaverseattributdomäne mit dem NetBIOS-Namen der Domäne aufgefüllt wird, in der sich der Benutzer befindet:

`domain <- %Domain.Netbios%`

### Operatoren

Folgende Operatoren können verwendet werden:

- **Vergleich**: <, <=, <>, =, >, >=
- **Mathematik**: +, -, *, -
- **Zeichenfolge**: & (concatenate)
- **Logischer Ausdruck**: && (and), || (or)
- **Auswertungsreihenfolge**: ( )

Operatoren werden von links nach rechts ausgewertet und haben bei der Auswertung die gleiche Priorität. Das bedeutet, dass der Multiplikator (*) nicht vor der Subtraktion (-) ausgewertet wird. „2*(5+3)“ ist nicht dasselbe wie „2*5+3“. Die Klammern werden verwendet, um die Reihenfolge der Auswertung zu ändern, wenn die Auswertungsreihenfolge von links nach rechts nicht geeignet ist.

## Häufige Szenarios

### Länge der Attribute

Zeichenfolgenattribute sind standardmäßig als indizierbar festgelegt, die maximale Länge beträgt 448 Zeichen. Wenn Sie mit Zeichenfolgenattributen arbeiten, die möglicherweise mehr Zeichen enthalten, stellen Sie sicher, dass Folgendes in den Attributfluss einbezogen wird:

`attributeName <- Left([attributeName],448)`

### Ändern von "userPrincipalSuffix"

Das userPrincipalName-Attribut in Active Directory ist den Benutzern nicht immer bekannt und eignet sich möglicherweise nicht als Anmelde-ID. Der Installationsassistent für die Azure AD Connect-Synchronisierung ermöglicht die Auswahl eines anderen Attributs wie z. B. „mail“. In einigen Fällen muss das Attribut jedoch berechnet werden. Das Unternehmen Contoso beispielsweise verfügt über zwei Azure AD-Verzeichnisse, eins für die Produktion und eins für Testzwecke. Benutzer im Testmandanten sollen nur das Suffix der Anmelde-ID ändern.

`userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"`

In diesem Ausdruck werden alle Zeichen links des ersten @-Zeichens (Word) mit einer festgelegten Zeichenfolge verkettet.

### Konvertieren eines einwertigen Attributs in ein mehrwertiges Attribut

Einige Attribute in Active Directory weisen im Schema mehrere Werte auf, obwohl sie in "Active Directory-Benutzer und -Computer" wie einwertige Attribute aussehen. Das description-Attribut ist ein Beispiel hierfür.

`description <- IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Falls das Attribut einen Wert besitzt, wird in diesem Ausdruck das erste Element (Item) im Attribut genommen, die voran- und nachgestellten Leerzeichen (Trim) werden entfernt, und die ersten 448 Zeichen (Left) der Zeichenfolge werden beibehalten.

## Erweitertes Konzept

### "NULL" im Vergleich zu "IgnoreThisFlow"

Bei eingehenden Synchronisierungsregeln sollte immer die Konstante **NULL** verwendet werden. Dies weist darauf hin, dass der Attributfluss keine Werte beiträgt und eine andere Regel einen Wert beitragen kann. Wenn keine Regel einen Wert beiträgt, wird das Metaverseattribut entfernt.

Bei ausgehenden Synchronisierungsregeln können zwei verschiedene Konstanten verwendet werden: NULL und IgnoreThisFlow. Beide weisen darauf hin, dass der Attributfluss keine Werte beiträgt, der Unterschied liegt jedoch darin, was passiert, wenn auch keine andere Regel einen Wert beitragen kann. Wenn im verbundenen Verzeichnis ein Wert vorhanden ist, führt eine NULL-Konstante einen Löschvorgang für das Attribut durch und entfernt es. Bei "IgnoreThisFlow" dagegen wird der vorhandene Wert beibehalten.

#### ImportedValue

Die Funktion ImportedValue unterscheidet sich von allen anderen Funktionen, da der Attributname in Anführungszeichen statt in eckige Klammern eingeschlossen werden muss: ImportedValue("proxyAddresses").

Üblicherweise verwendet ein Attribut während der Synchronisierung den erwarteten Wert, selbst wenn er noch nicht exportiert wurde oder während des Exports ein Fehler empfangen wurde ("top of the tower"). Bei einer eingehenden Synchronisierung wird angenommen, dass ein Attribut, das ein verbundenes Verzeichnis noch nicht erreicht hat, dieses schließlich erreichen wird. In einigen Fällen ist es wichtig, nur Werte zu synchronisieren, die vom verbundenen Verzeichnis bestätigt wurden. In diesem Fall wird die Funktion "ImportedValue" verwendet ("hologram and delta import tower").

Ein Beispiel dafür befindet sich in der einsatzbereiten Synchronisierungsregel "In from AD – User Common from Exchange", wobei in Hybrid-Exchange der durch Exchange online hinzugefügte Wert nur synchronisiert werden sollte, wenn bestätigt wurde, dass der Wert erfolgreich exportiert wurde:

`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues("proxyAddresses")))`

Eine vollständige Liste der Funktionen finden Sie unter [Azure AD Connect-Synchronisierung: Funktionsreferenz](active-directory-aadconnectsync-functions-reference.md).


## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=AcomDC_1203_2015-->
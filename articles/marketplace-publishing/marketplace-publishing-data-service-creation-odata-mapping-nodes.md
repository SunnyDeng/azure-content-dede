<properties
   pageTitle="Leitfaden für das Erstellen eines Datendiensts für den Marketplace | Microsoft Azure"
   description="Detaillierte Anweisungen zum Erstellen, Zertifizieren und Bereitstellen eines Datendiensts für den Erwerb im Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace-publishing"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="11/30/2015"
      ms.author="hascipio; avikova" />

# Grundlegende Informationen zum Knotenschema zum Zuordnen eines vorhandenen Webdiensts zu OData über CSDL
Dieses Dokument verdeutlicht die Knotenstruktur für die Zuordnung eines OData-Protokolls zu CSDL. Ein wichtiger Aspekt ist, dass die Knotenstruktur aus wohlgeformtem XML-Code besteht. Darum können Sie das Schema von Stamm, übergeordnetem und untergeordnetem Element anwenden, wenn Sie die OData-Zuordnung entwerfen.

## Ignorierte Elemente
Im Folgenden werden die CSDL-Elemente (XML-Knoten) auf hoher Ebene aufgeführt, die während des Imports von Metadaten des Webdiensts nicht vom Azure Marketplace-Back-End verwendet werden. Sie können vorhanden sein, werden aber ignoriert.

| Element | Umfang |
|----|----|
| Using-Element | Knoten, untergeordnete Knoten und alle Attribute |
| Documentation-Element | Knoten, untergeordnete Knoten und alle Attribute |
| ComplexType | Knoten, untergeordnete Knoten und alle Attribute |
| Association-Element | Knoten, untergeordnete Knoten und alle Attribute |
| Extended-Eigenschaft | Knoten, untergeordnete Knoten und alle Attribute |
| EntityContainer | Nur die folgenden Attribute werden ignoriert: *extends* und *AssociationSet* |
| Schema | Nur die folgenden Attribute werden ignoriert: *Namespace* |
| FunctionImport | Nur die folgenden Attribute werden ignoriert: *Mode* (Standardwert "ln" wird vorausgesetzt) |
| EntityType | Nur die folgenden untergeordneten Knoten werden ignoriert: *Key* und *PropertyRef* |

Im Folgenden werden die Änderungen (hinzugefügte und ignorierte Elemente) der verschiedenen CSDL-XML-Knoten im Detail beschrieben.

## FunctionImport-Knoten
Ein FunctionImport-Knoten stellt eine URL (Einstiegspunkt) dar, die einen Dienst für den Endbenutzer verfügbar macht. Der Knoten beschreibt immer, wie die URL adressiert wird, welche Parameter für den Endbenutzer verfügbar sind, und wie diese Parameter bereitgestellt werden.

Details zu diesem Knoten finden Sie unter [http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx][MSDNFunctionImportLink].

[MSDNFunctionImportLink]: 'http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx'

Im Folgenden werden die zusätzlichen Attribute (oder Ergänzungen von Attributen) aufgeführt, die von den FunctionImport-Knoten verfügbar gemacht werden:

**D:BaseUri** – Die URI-Vorlage für die REST-Ressource, die in Marketplace verfügbar ist. Marketplace verwendet die Vorlage zum Erstellen von Abfragen für den REST-Webdienst. Die URI-Vorlage enthält Platzhalter für die Parameter in Form von {Parametername}, wobei "Parametername" der Name des Parameters ist. Beispiel: apiVersion={API-Version}. Parameter können als URI-Parameter oder als Teil des URI-Pfads angezeigt werden. Bei der Darstellung im Pfad sind sie immer erforderlich (können nicht als potenzielle NULL-Werte gekennzeichnet werden). *Beispiel:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Name** – Der Name der importierten Funktion. Darf nicht mit anderen in der CSDL definierten Namen identisch sein. Beispiel: Name="GetModelUsageFile"

**EntitySet** *(optional)* – Wenn die Funktion eine Auflistung von Entitätstypen zurückgibt, muss der Wert von **EntitySet** der Entitätenmenge entsprechen, zu der die Auflistung gehört. Andernfalls darf das Attribut **EntitySet** nicht verwendet werden. *Beispiel:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(optional)* – Gibt den Typ der Elemente an, die vom URI zurückgegeben werden. Verwenden Sie dieses Attribut nicht, wenn die Funktion keinen Wert zurückgibt. Folgende Typen werden unterstützt:

 - **Collection (<Entity type name>)**: Gibt eine Auflistung definierter Entitätstypen an. Der Name ist im Name-Attribut des Knotens "EntityType" vorhanden. Ein Beispiel ist "Collection (WXC. HourlyResult)".
 - **Raw (<mime type>)**: Gibt ein unformatiertes Dokument/einen unformatierten Blob an, das/der an den Benutzer zurückgegeben wird. Ein Beispiel ist "Raw(image/jpeg)". Andere Beispiele:

  - ReturnType="Raw(text/plain)"
  - ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** – Gibt an, wie Auslagern durch die REST-Ressource ausgeführt wird. Die Parameterwerte werden in geschweiften Klammern verwendet, z. B. page={$page}&itemsperpage={$size}. Die verfügbaren Optionen sind:

- **None**: Auslagern ist nicht verfügbar
- **Skip**: Auslagern wird über logisches "Skip" und "Take" (oben) ausgedrückt. "Skip" springt über M Elemente, und "Take" gibt dann die nächsten N Elemente zurück. Parameterwert: "$skip"
- **Take**: "Take" gibt die nächsten N Elemente zurück. Parameterwert: "$take"
- **PageSize**: Auslagern wird über eine logische Seite und Größe (Elemente pro Seite) ausgedrückt. "Page" steht für die aktuelle Seite, die zurückgegeben wird. Parameterwert: "$page"
- **Size**: "Size" gibt die Anzahl der Elemente an, die für jede Seite zurückgegeben werden. Parameterwert: "$size"

**D:AllowedHttpMethods** *(optional)* – Gibt an, welches Verb von der REST-Ressource behandelt wird. Schränkt außerdem das akzeptierte Verb auf den angegebenen Wert ein. Standard = "POST". *Beispiel:* `d:AllowedHttpMethods="GET"` Die verfügbaren Optionen sind:

- **GET**: Wird in der Regel verwendet, um Daten zurückzugeben
- **POST**: Wird in der Regel verwendet, um neue Daten einzufügen
- **PUT**: Wird in der Regel verwendet, um Daten zu aktualisieren
- **DELETE**: Wird verwendet, um Daten zu löschen

Zusätzliche untergeordnete (nicht in der CSDL-Dokumentation behandelte) Knoten innerhalb des Knotens "FunctionImport" sind:

**d:RequestBody** *(optional)* – Der Anforderungstext wird verwendet, um anzugeben, dass die Anforderung einen gesendeten Text erwartet. Parameter können im Anforderungstext angegeben werden. Sie werden in geschweiften Klammern ausgedrückt, z. B. {Parametername}. Diese Parameter werden von der Benutzereingabe dem Text zugeordnet, der an den Inhaltsanbieterdienst übertragen wird. Das requestBody-Element verfügt über ein Attribut mit dem Namen "httpMethod". Das Attribut lässt zwei Werte zu:

- **POST**: Wird verwendet, wenn die Anforderung ein "HTTP-POST" ist
- **GET**: Wird verwendet, wenn die Anforderung ein "HTTP GET" ist

	Beispiel:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** und **d:Namespace** – Dieser Knoten beschreibt die in XML definierten Namespaces, die vom Funktionsimport (URI-Endpunkt) zurückgegeben werden. Der XML-Code, der vom Back-End-Dienst zurückgegeben wird, kann eine beliebige Anzahl von Namespaces enthalten, um den zurückgegebenen Inhalt zu differenzieren. **Alle diese Namenspaces müssen aufgelistet werden, wenn sie in d:Map- oder d:Match-XPath-Abfragen verwendet werden.** Der d:Namespaces-Knoten enthält eine Reihe/Liste von d:Namespace-Knoten. Jeder von ihnen führt einen Namespace auf, der in der Back-End-Dienst-Antwort verwendet wird. Im Folgenden werden die Attribute des Knotens "d:Namespace" aufgeführt:

-	**d:Prefix**: Das Präfix für den Namespace, wie in den XML-Ergebnissen, die vom Dienst zurückgegeben werden, z. B. "f:FirstName", "f:LastName", wobei "f" das Präfix ist.
- **d:Uri**: Der vollständige URI des Namespace, der im Ergebnisdokument verwendet wird. Er entspricht dem Wert, in den das Präfix zur Laufzeit aufgelöst wird.

**d:ErrorHandling** *(optional)* – Dieser Knoten enthält die Bedingungen für die Fehlerbehandlung. Jede Bedingung wird anhand des Ergebnisses überprüft, das vom Dienst für den Inhaltsanbieter zurückgegeben wird. Wenn eine Bedingung dem vorgeschlagenen HTTP-Fehlercode entspricht, wird eine Fehlermeldung an den Endbenutzer zurückgegeben.

**d:ErrorHandling** *(optional)* und **d:Condition** *(optional)* – Ein Bedingungsknoten enthält eine Bedingung, die anhand des vom Inhaltsanbieterdienst zurückgegebenen Ergebnisses überprüft wird. Folgende Attribute sind **erforderlich** :

- **d:Match**: Ein XPath-Ausdruck, der überprüft, ob ein bestimmter Knoten/Wert im Ausgabe-XML-Code des Inhaltsanbieters vorhanden ist. Der XPath-Ausdruck wird auf die Ausgabe angewandt und sollte "True" zurückgeben, wenn die Bedingung zutrifft, oder andernfalls "False".
- **d:HttpStatusCode**: Der HTTP-Statuscode, der von Marketplace zurückgegeben werden sollte, falls die Bedingung zutrifft. Marketplace signalisiert Fehler über HTTP-Statuscodes an den Benutzer. Eine Liste der HTTP-Statuscodes finden Sie unter http://en.wikipedia.org/wiki/HTTP_status_code.
- **d:ErrorMessage**: Fehlermeldung, die – mit dem HTTP-Statuscode – an den Endbenutzer zurückgegeben wird. Dies sollte eine benutzerfreundliche Fehlermeldung in Klartext sein.

**d:Title** *(optional)* – Ermöglicht, den Titel der Funktion zu beschreiben. Hierher stammt der Wert für den Titel:

- Das optionale Zuordnungsattribut (ein XPath-Ausdruck) gibt an, wo Sie den Titel in der Antwort finden, die von der Serviceanforderung zurückgegeben wird.
- – Oder – Der Titel, der als Wert des Knotens angegeben ist.

**d:Rights** *(optional)* – Berechtigungen (z. B. das Urheberrecht), die der Funktion zugeordnet sind. Hierher stammt der Wert für die Rechte:

- Das optionale Zuordnungsattribut (ein XPath-Ausdruck) gibt an, wo Sie die Rechte in der Antwort finden, die von der Serviceanforderung zurückgegeben wird.
-	– Oder – Die Rechte, die als Wert des Knotens angegeben sind.

**d:Description** *(optional)* – Eine kurze Beschreibung der Funktion. Hierher stammt der Wert für die Beschreibung:

- Das optionale Zuordnungsattribut (ein XPath-Ausdruck) gibt an, wo Sie die Beschreibung in der Antwort finden, die von der Serviceanforderung zurückgegeben wird.
- – Oder – Die Beschreibung, die als Wert des Knotens angegeben ist.

**d:EmitSelfLink** – *Lesen Sie hierzu das obige Beispiel "FunctionImport für 'Auslagern' durch zurückgegebene Daten".*

**d:EncodeParameterValue** – Optionale Erweiterung für "OData"

**d:QueryResourceCost** – Optionale Erweiterung für "OData"

**d:Map** – Optionale Erweiterung für "OData"

**d:Headers** – Optionale Erweiterung für "OData"

**d:Headers** – Optionale Erweiterung für "OData"

**d:Value** – Optionale Erweiterung für "OData"

**d:HttpStatusCode** – Optionale Erweiterung für "OData"

**d:ErrorMessage** – Optionale Erweiterung für "OData"

## Parameter-Knoten

Dieser Knoten stellt einen Parameter dar, der als Teil der URI-Vorlage/des Anforderungstexts verfügbar gemacht wird, der im Knoten "FunctionImport" angegeben wurde.

Eine sehr hilfreiche Dokumentseite mit näheren Informationen über den Knoten "Parameter-Element" finden Sie unter [http://msdn.microsoft.com/library/ee473431.aspx](http://msdn.microsoft.com/library/ee473431.aspx) (Verwenden Sie die Dropdownliste **Andere Versionen**, um ggf. eine andere Version zum Anzeigen der Dokumentation auszuwählen). *Beispiel:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Parameterattribut | Ist erforderlich | Wert |
|----|----|----|
| Name | Ja | Der Name des Parameters. Groß-/Kleinschreibung wird berücksichtigt! Muss mit der BaseUri-Schreibweise übereinstimmen. **Beispiel:** `<Property Name="IsDormant" Type="Byte" />` |
| Typ | Ja | Der Parametertyp. Der Wert muss ein **EDMSimpleType** oder ein komplexer Typ sein, der im Gültigkeitsbereich des Modells liegt. Weitere Informationen finden Sie unter "6 unterstützte Parameter-/Eigenschaftstypen". (Groß-/Kleinschreibung wird berücksichtigt! Erstes Zeichen in Großbuchstaben, der Rest in Kleinbuchstaben.) Lesen Sie auch [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][MSDNParameterLink]. **Beispiel:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode | Nein | **In**, "Out" oder "InOut", je nachdem, ob der Parameter ein Eingabe-, Ausgabe- oder Eingabe-/Ausgabeparameter ist. (Nur "IN" ist im Azure Marketplace verfügbar.) **Beispiel:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | Nein | Die maximal zulässige Länge des Parameters. **Beispiel:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision | Nein | Die Genauigkeit des Parameters. **Beispiel:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Skalieren | Nein | Der Maßstab des Parameters. **Beispiel:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: 'http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx'

Folgende Attribute wurden der CSDL-Spezifikation hinzugefügt:

| Parameterattribut | Beschreibung |
|----|----|
| **d:Regex** *(optional)* | Mit einer Regexanweisung wird der Eingabewert für den Parameter überprüft. Der Wert wird abgelehnt, wenn der Eingabewert nicht mit der Anweisung übereinstimmt. Auf diese Weise kann auch eine Reihe von möglichen Werten angegeben werden, z. B. ^ [0-9] +? $, um nur Zahlen zuzulassen. **Beispiel:** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters" d:SampleValues="George|John|Thomas|James"/>` |
| **d:Enum** *(optional)* | Eine Liste für den Parameter gültiger Werte, wo der senkrechte Strich als Trennzeichen verwendet wird. Der Typ der Werte muss dem definierten Typ des Parameters entsprechen. Beispiel: `english|metric|raw`. "Enum" wird als auswählbare Dropdownliste von Parametern in der Benutzeroberfläche (Dienst-Explorer) angezeigt. **Beispiel:** `<Parameter Name="Duration" Type="String" Mode="In" Nullable="true" d:Enum="1year|5years|10years"/>` |
| **d:Nullable** *(optional)* | Ermöglicht, festzulegen, ob ein Parameter potenziell den Wert NULL haben kann. Der Standardwert ist: "True". Allerdings können Parameter, die als Teil des Pfads in der URI-Vorlage verfügbar gemacht werden, nicht NULL sein. Die Benutzereingabe wird überschrieben, wenn das Attribut für diese Parameter auf "False" festgelegt wird. Ermöglicht, festzulegen, ob ein Parameter potenziell den Wert NULL haben kann. Der Standardwert ist: "True". Allerdings können Parameter, die als Teil des Pfads in der URI-Vorlage verfügbar gemacht werden, nicht NULL sein. Die Benutzereingabe wird überschrieben, wenn das Attribut für diese Parameter auf "False" festgelegt wird. **Beispiel:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(optional)* | Ein Beispielwert, der als Hinweis für den Client in der Benutzeroberfläche angezeigt werden soll. Es können mehrere Werte hinzugefügt werden, die mit dem senkrechten Strich getrennt werden, "|". **Beispiel:** `<Parameter Name="BikeOwner" Type="String" Mode="In" d:SampleValues="George|John|Thomas|James"/>` |
| **d:UriTemplate** | |

## EntityType-Knoten

Dieser Knoten entspricht einem der Typen, die dem Endbenutzer über den Marketplace zurückgegeben werden. Er enthält auch die Zuordnung aus der Ausgabe, die vom Inhaltsanbieterdienst an die Werte zurückgegeben wird, die an den Endbenutzer zurückgegeben werden.

Nähere Informationen über diesen Knoten finden Sie unter [http://msdn.microsoft.com/library/bb399206.aspx](http://msdn.microsoft.com/library/bb399206.aspx) (Verwenden Sie die Dropdownliste **Andere Versionen**, um ggf. eine andere Version zum Anzeigen der Dokumentation auszuwählen).

| Attributname | Ist erforderlich | Wert |
|----|----|----|
| Name | Ja | Der Name des Entitätstyps. **Beispiel:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | Nein | Der Name eines anderen Entitätstyps, der der Basistyp des Entitätstyps ist, der definiert wird. **Beispiel:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Folgende Attribute wurden der CSDL-Spezifikation hinzugefügt:

**d:Map** – Ein XPath-Ausdruck, der auf die Ausgabe des Diensts angewandt wird. Hier wird davon ausgegangen, dass die Ausgabe des Diensts einen Satz von Elementen enthält, die sich wiederholen, wie ein ATOM-Feed, wo sich eine Gruppe von Eintragsknoten wiederholt. Jeder dieser sich wiederholenden Knoten enthält einen Datensatz. Der XPath-Ausdruck wird dann angegeben, um auf den einzelnen sich wiederholenden Knoten im Ergebnis des Inhaltsanbieterdiensts hinzuweisen, der die Werte für einen einzelnen Datensatz enthält. Beispielausgabe des Diensts:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

Der XPath-Ausdruck würde "/foo/bar" lauten, da jeder bar-Knoten der sich wiederholende Knoten in der Ausgabe ist und den eigentlichen Inhalt enthält, der an den Endbenutzer zurückgegeben wird.

**Key** – Dieses Attribut wird vom Marketplace ignoriert. REST-basierte Webdienste machen in der Regel keinen Primärschlüssel verfügbar.


## Property-Knoten

Dieser Knoten enthält eine Eigenschaft des Datensatzes.

Nähere Informationen über diesen Knoten finden Sie unter [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (Verwenden Sie die Dropdownliste **Andere Versionen**, um ggf. eine andere Version zum Anzeigen der Dokumentation auszuwählen). *Beispiel:* `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name" 	Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
		...
        </EntityType>`

| Attributname | Erforderlich | Wert |
|----|----|----|
| Name | Ja | Der Name der Eigenschaft. |
| Typ | Ja | Der Typ des Eigenschaftswerts. Der Typ des Eigenschaftswerts muss ein **EDMSimpleType** oder ein komplexer Typ sein (erkennbar am vollqualifizierten Namen), der im Gültigkeitsbereich des Modells liegt. Weitere Informationen finden Sie unter "Konzeptionelle Modelltypen (CSDL)". |
| Nullable | Nein | **True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert kann. Hinweis: In der CSDL-Version, die durch den Namespace [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) gekennzeichnet ist, muss ein komplexer Typ die Eigenschaft Nullable="False" haben. |
| DefaultValue | Nein | Der Standardwert der Eigenschaft. |
|MaxLength | Nein | Maximale Länge des Eigenschaftswerts. |
| FixedLength | Nein | **True** oder **False** abhängig davon, ob der Eigenschaftswert als Zeichenfolge mit fester Länge gespeichert wird. |
| Precision | Nein | Bezieht sich auf die maximale Anzahl von Ziffern, die im numerischen Wert beibehalten werden soll. |
| Skalieren | Nein | Maximale Anzahl an Dezimalstellen, die im numerischen Wert beibehalten werden soll. |
| Unicode | Nein | **True** oder **False** abhängig davon, ob der Eigenschaftswert als Unicode-Zeichenfolge gespeichert wird. |
| Collation | Nein | Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird. |
| ConcurrencyMode | Nein | **None** (Standardwert) oder **Fixed**. Wenn der Wert auf **Fixed** festgelegt ist, wird der Wert der Eigenschaft zu Überprüfungen auf vollständige Parallelität verwendet. |

Folgende zusätzliche Attribute wurden der CSDL-Spezifikation hinzugefügt:

**d:Map** – XPath-Ausdruck, der auf die Dienstausgabe angewandt wird und eine Eigenschaft aus der Ausgabe extrahiert. Der angegebene XPath-Ausdruck ist relativ zum sich wiederholenden Knoten, der im XPath-Ausdruck des EntityType-Knotens ausgewählt wurde. Sie können auch einen absoluten XPath-Ausdruck angeben, um zu ermöglichen, eine statische Ressource in jeden der Ausgabeknoten einzubeziehen, z. B. eine Urheberrechtserklärung, die nur einmal in der ursprünglichen Dienstausgabe vorhanden ist, jedoch in jeder Zeile der OData-Ausgabe vorhanden sein sollte. Beispiel des Diensts:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Mit dem XPath-Ausdruck "./bar/baz0" kann hier der baz0-Knoten aus dem Inhaltsanbieterdienst abgerufen werden.

**d:CharMaxLength** – Sie können die maximale Länge für den Zeichenfolgentyp angeben. Siehe "DataService CSDL"-Beispiel.

**d:IsPrimaryKey** – Gibt an, ob die Spalte der Primärschlüssel in der Tabelle/Sicht ist. Siehe "DataService CSDL"-Beispiel.

**d:isExposed** – Bestimmt, ob das Tabellenschema verfügbar gemacht wird (in der Regel "True"). Siehe "DataService CSDL"-Beispiel.

**d:IsView** *(optional)* – "True", wenn dies auf einer Sicht statt einer Tabelle basiert. Siehe "DataService CSDL"-Beispiel.

**d:Tableschema** – Siehe "DataService CSDL"-Beispiel.

**d:ColumnName** – Ist der Name der Spalte in der Tabelle/Sicht. Siehe "DataService CSDL"-Beispiel.

**d:IsReturned** – Ist der boolesche Wert, der bestimmt, ob der Dienst diesen Wert für den Client verfügbar macht. Siehe "DataService CSDL"-Beispiel.

**d:IsQueryable** – Ist der boolesche Wert, der bestimmt, ob die Spalte in einer Datenbankabfrage verwendet werden kann. Siehe "DataService CSDL"-Beispiel.

**d:OrdinalPosition** – Die numerische Position x der Spalte in der Darstellung in der Tabelle oder Sicht, wobei x einen Wert von 1 bis zur Anzahl der Spalten in der Tabelle annehmen kann. Siehe "DataService CSDL"-Beispiel.

**d:DatabaseDataType** – Der Datentyp der Spalte in der Datenbank, d. h. SQL-Datentyp. Siehe "DataService CSDL"-Beispiel.

## Unterstützte Parameter-/Eigenschaftstypen
Die folgenden Parameter- und Eigenschaftstypen werden unterstützt. (Groß-/Kleinschreibung wird berücksichtigt)

| Primitive Typen | Beschreibung |
|----|----|
| Null | Stellt das Fehlen eines Werts dar. |
| Boolean | Stellt das mathematische Konzept der Binärwertlogik dar.|
| Byte | 8-Bit-Ganzzahlwert ohne Vorzeichen|
|DateTime| Stellt Datum und Uhrzeit mit Werten zwischen 00:00:00 Uhr, 1. Januar 1753 n. Chr. bis 23:59:59 Uhr, 31. Dezember 9999 n. Chr. dar.|
|Decimal | Stellt numerische Werte mit fester Genauigkeit und Dezimalstellenanzahl dar. Dieser Typ kann numerische Werte zwischen -10^255 + 1 und +10^255 - 1 beschreiben.|
| Double | Stellt eine Gleitkommazahl mit 15 Stellen Genauigkeit dar, die Werte im ungefähren Bereich von ± 2,23e - 308 bis ±1,79e + 308 darstellen kann. **Verwenden Sie "Decimal", um Probleme beim Exportieren nach Excel zu vermeiden.**|
| Single | Stellt eine Gleitkommazahl mit 7 Stellen Genauigkeit dar, die Werte im ungefähren Bereich von ±1,18e - 38 bis ±3,40e + 38 darstellen kann.|
|Guid |Stellt einen eindeutigen 16-Byte-Bezeichnerwert (128 Bit) dar. |
|Int16|Stellt einen 16-Bit-Ganzzahlwert mit Vorzeichen dar. |
|Int32|Stellt einen 32-Bit-Ganzzahlwert mit Vorzeichen dar. |
|Int64|Stellt einen 64-Bit-Ganzzahlwert mit Vorzeichen dar. |
|String | Stellt Zeichendaten mit fester oder variabler Länge dar. |


## Weitere Informationen
- Wenn Sie sich über den gesamten OData-Zuordnungsprozess und seinen Zweck informieren möchten, lesen Sie den Artikel [OData-Zuordnung im Datendienst](marketplace-publishing-data-service-creation-odata-mapping.md), um Definitionen, Strukturen und Anweisungen kennen zu lernen.
- Wenn Sie an Beispielen interessiert sind, lesen Sie den Artikel [Beispiele für die OData-Zuordnung im Datendienst](marketplace-publishing-data-service-creation-odata-mapping-examples.md), um Beispielcode kennen zu lernen und Codesyntax und Kontext zu verstehen.
- Um zum vorgeschriebenen Pfad für die Veröffentlichung eines Datendiensts im Azure Marketplace zurückzukehren, lesen Sie den Artikel [Leitfaden zur Datendienstveröffentlichung](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_1203_2015-->
<properties
   pageTitle="Azure Data Catalog – Entwicklerkonzepte | Microsoft Azure"
   description="Eine Einführung in die wichtigsten Konzepte im konzeptionellen Modell von Azure Data Catalog, die durch die Catalog-REST-API verfügbar gemacht werden."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags 
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/10/2016"
   ms.author="derrickv"/>

# Azure Data Catalog – Entwicklerkonzepte

**Azure Data Catalog** von Microsoft ist ein vollständig verwalteter Clouddienst mit Funktionen für die Datenquellenermittlung und das Crowdsourcing von Datenquellenmetadaten. Entwickler können den Dienst über die REST-APIs nutzen. Eine erfolgreiche Integration von **Azure Data Catalog** setzt voraus, dass die Entwickler mit den implementierten Konzepten vertraut sind.

## Wichtige Begriffe

Das konzeptionelle Modell von **Azure Data Catalog** basiert auf vier zentralen Konzepten: **Katalog**, **Benutzer**, **Assets** und **Anmerkungen**.

![Konzept][1]

*Abbildung 1: Vereinfachte Darstellung des konzeptionellen Modells von Azure Data Catalog*

### Katalog

Ein**Katalog** ist der Container der obersten Ebene und wird für sämtliche von der Organisation gespeicherte Metadaten verwendet. Pro Azure-Konto ist jeweils ein **Katalog** zulässig. Kataloge sind mit einem Azure-Abonnement verknüpft, pro Konto kann aber nur ein einzelner **Katalog** erstellt werden, auch wenn ein Konto über mehrere Abonnements verfügen kann.

Der Katalog enthält **Benutzer** und **Assets**.

### Benutzer

Benutzer sind Sicherheitsprinzipale, die über Berechtigungen zum Ausführen von Aktionen im Katalog verfügen (Durchsuchen des Katalogs, Hinzufügen, Bearbeiten oder Entfernen von Elementen usw.).

Ein Benutzer kann verschiedene Rollen haben. Weitere Informationen zu Rollen finden Sie im Abschnitt „Rollen und Autorisierung“.

Einzelne Benutzer und Sicherheitsgruppen können hinzugefügt werden.

Azure Data Catalog verwendet Azure Active Directory für die Identitäts- und Zugriffsverwaltung. Jeder Katalogbenutzer muss einem Active Directory für das Konto angehören.

### Assets

Ein **Katalog** enthält Datenassets. **Assets** sind die Einheit der vom Katalog verwalteten Granularität.

Die Granularität eines Assets variiert je nach Datenquelle. Bei SQL Server oder Oracle Database kann ein Asset eine Tabelle oder Sicht sein. Bei SQL Server Analysis Services kann ein Asset ein Kennzahlenobjekt, ein Dimensionsobjekt oder ein Key Performance Indicator (KPI) sein. Bei SQL Server Reporting Services ist ein Asset ein Bericht.

Ein **Asset** ist das Element, das Sie einem Katalog hinzufügen oder daraus entfernen. Es ist die Ergebniseinheit, die von **Search** zurückgegeben wird.

Ein **Asset** setzt sich aus dem Namen, dem Speicherort und dem Typ sowie aus Anmerkungen zur genaueren Beschreibung zusammen.

### Anmerkungen

Anmerkungen sind Elemente, die Metadaten für Assets darstellen.

Beispiele für Anmerkungen sind Beschreibungen, Tags, Schemas, Dokumentationen usw. Eine vollständige Liste der Asset- und Anmerkungstypen finden Sie im Abschnitt "Assetobjektmodell".

## Crowdsourcing von Anmerkungen und Benutzerperspektive (Meinungsvielfalt)

Ein zentraler Aspekt von Azure Data Catalog ist die Crowdsourcing-Unterstützung für Metadaten im System. Im Gegensatz zu einem Wiki-Ansatz, bei dem nur eine einzelne Meinung möglich ist und vom letzten Autor bestimmt wird, können beim Azure Data Catalog-Modell gleichzeitig mehrere Meinungen im System vorhanden sein.

Dieser Ansatz spiegelt die Realität von Unternehmensdaten wider, da er für ein bestimmtes Asset verschiedene Perspektiven von unterschiedlichen Benutzern zulässt:

-	Ein Datenbankadministrator kann Informationen zu verfügbaren Vereinbarungen zum Servicelevel oder das verfügbare Verarbeitungszeitfenster für ETL-Massenvorgänge angeben.
-	Ein Data Steward kann Informationen zu den Geschäftsprozessen, bei denen das Asset angewendet wird, oder die Klassifizierungen angeben, die das Unternehmen auf das Asset angewendet hat.
-	Ein Finanzanalyst kann Informationen zur Verwendung der Daten im Rahmen der Berichterstellung am Ende des Zeitraums angeben.

Um dieses Beispiel zu ermöglichen, kann jeder Benutzer (Datenbankadministrator, Data Steward und Analyst) einer einzelnen, im Katalog registrierten Tabelle eine Beschreibung hinzufügen. Alle Beschreibungen werden im System gespeichert und im Azure Data Catalog-Portal angezeigt.

Dieses Muster wird für die meisten Elemente im Objektmodell verwendet. Aus diesem Grund handelt es sich bei Objekttypen in der JSON-Nutzlast häufig um Arrays für Eigenschaften, obwohl Sie eigentlich einen Singleton erwarten würden.

Am Assetstamm findet sich beispielsweise ein Array mit Beschreibungsobjekten. Die Array-Eigenschaft heißt „descriptions“. Eine Beschreibungsobjekt besitzt drei Eigenschaften: „description“, „tags“ und „friendlyName“. Für jeden Benutzer, der eine oder mehrere dieser Eigenschaften eingibt, wird ein Beschreibungsobjekt für die angegebenen Werte erstellt.

Die UX kann dann entscheiden, wie die Kombination dargestellt wird. Für die Anzeige stehen drei verschiedene Muster zur Verfügung.

-	Das einfachste Muster ist „Alles anzeigen“. Bei diesem Muster werden alle Objekte in einer Art von Listenansicht angezeigt. Diese Variante wird von der UX des Azure Data Catalog-Portals für die Beschreibung verwendet.
-	Ein weiteres Muster ist „Zusammenführen“. Bei diesem Muster werden alle Werte der verschiedenen Benutzer zusammengeführt. Duplikate werden entfernt. Beispiele für dieses Muster in der UX des Azure Data Catalog-Portals sind die Eigenschaften „tags“ und „experts“.
-	Ein drittes Muster ist „Letzter Autor ist ausschlaggebend“. Bei diesem Muster wird nur der zuletzt eingegebene Wert angezeigt. Ein Beispiel für dieses Muster ist „friendlyName“.

## Assetobjektmodell

Wie bereits im Abschnitt mit den wichtigen Begriffen angedeutet, enthält das Objektmodell von **Azure Data Catalog** Elemente, bei denen es sich um Assets oder Anmerkungen handeln kann. Elemente besitzen optionale oder erforderliche Eigenschaften. Einige Eigenschaften gelten für alle Elemente. Einige Eigenschaften gelten für alle Assets. Einige Eigenschaften gelten nur für bestimmte Assettypen.

### Allgemeine Eigenschaften

Diese Eigenschaften gelten für alle Stammassettypen und für alle Anmerkungstypen.

> [AZURE.NOTE] Bei den Eigenschaften, deren Name mit einem doppelten Unterstrich beginnt, handelt es sich um Systemtypen.

|**Eigenschaftenname**|**Datentyp**|**Kommentare**
|---|---|---
|modifiedTime|DateTime|Der Zeitpunkt der letzten Stammänderung. Wird vom Client festgelegt. (Dieser Wert wird nicht vom Server gepflegt.)
|__id|String|Die ID des Elements (schreibgeschützt). Diese ID ist für das Asset in einem Katalog garantiert eindeutig. |__type|String|Der Typ des Assets (schreibgeschützt).
|__\_\_creatorId|String|Eine Zeichenfolge, die vom Ersteller des Assets verwendet wird, um das Asset eindeutig zu identifizieren.

### Allgemeine Stammeigenschaften

Diese Eigenschaften gelten für alle Stammassettypen.

|**Eigenschaftenname**|**Datentyp**|**Kommentare**
|---|---|---
|name|String|Ein Name, der sich aus den Speicherortinformationen der Datenquelle ableitet.
|dsl|Speicherort der Datenquelle|Beschreibt die Datenquelle eindeutig und ist einer der Bezeichner für das Asset. (Siehe Abschnitt zu dualer Identität.) Die Struktur des Speicherorts der Datenquelle variiert je nach Quelltyp.
|dataSource|DataSourceInfo|Weitere Informationen zum Typ des Assets.
|lastRegisteredBy|SecurityPrincipal|Beschreibt den Benutzer, der dieses Asset zuletzt registriert hat. Enthält sowohl die eindeutige ID für den Benutzer („Upn“) als auch einen Anzeigenamen („lastName“ und „firstName“).
|lastRegisteredTime|dateTime|Der Zeitpunkt der letzten Registrierung dieses Assets im Katalog.
|containerId|String|Die ID des Containerassets für die Datenquelle. Diese Eigenschaft wird für den Containertyp nicht unterstützt.

### Stammassettypen

Stammassettypen stellen die verschiedenen Typen von Datenassets dar, die im Katalog registriert werden können.

|**Assettyp**|**Zusätzliche Eigenschaften**|**Datentyp**|**Kommentare**
|---|---|---|---
|Tabelle|||Eine Tabelle stellt beliebige Tabellendaten dar. Dazu zählen SQL-Tabellen, SQL-Ansichten, tabellarische Analysis Services-Tabellen, mehrdimensionale Analysis Services-Dimensionen, Oracle-Tabellen usw.
|Kennzahl|||Dieser Typ stellt eine Analysis Services-Kennzahl dar.
||Kennzahl|Column|Metadaten zur Beschreibung der Kennzahl.
||isCalculated|Boolean|Gibt an, ob es sich um eine berechnete Kennzahl handelt.
||measureGroup|String|Der physische Container für die Kennzahl.
||goalExpression|String|Ein numerischer MDX-Ausdruck oder eine Berechnung, die den Zielwert des KPI zurückgibt.
||valueExpression|String|Ein numerischer MDX-Ausdruck, der den tatsächlichen Wert des KPI zurückgibt.
||statusExpression|String|Ein MDX-Ausdruck, der den Status des KPI zu einem bestimmten Zeitpunkt darstellt.
||trendExpression|String|Ein MDX-Ausdruck, der den Wert des KPI im Zeitverlauf auswertet. Der Trend kann ein beliebiges zeitbasiertes Kriterium sein, das in einem bestimmten Geschäftskontext hilfreich ist.
||measureGroup|String|Der physische Container für die Kennzahl.
|Bericht|||Dieser Typ stellt einen SQL Server Reporting Services-Bericht dar.
||CreatedBy|String| |
||CreatedDate|String| |
|Container|||Dieser Typ stellt einen Container für andere Objekte dar, z. B. eine SQL-Datenbank, ein Azure-Blobcontainer oder ein Analysis Services-Modell.

### Anmerkungstypen

Anmerkungstypen stellen Typen von Metadaten dar, die anderen Typen innerhalb des Katalogs zugewiesen werden können.

|**Anmerkungstyp**|**Zusätzliche Eigenschaften**|**Datentyp**|**Kommentare**
|---|---|---|---
|Beschreibung|||Jeder Benutzer des Systems kann eine eigene Beschreibung und eigene Tags hinzufügen. Nur dieser Benutzer kann das Beschreibungsobjekt bearbeiten. (Administratoren und Assetbesitzer können das Beschreibungsobjekt zwar löschen, aber nicht bearbeiten.) Diese Elemente werden separat verwaltet. Es gibt also ein Array mit Beschreibungen für die einzelnen Assets (eine für jeden Benutzer, der einen Beitrag zu dem Asset geleistet hat, sowie ggf. eine weitere mit Informationen, die sich aus der Datenquelle ableiten).
||friendlyName|string|Ein Anzeigename, der anstelle des aus der Datenquelle abgeleiteten Namens verwendet werden kann. Dies ist hilfreich für die Anzeige und für die Suche.
||tags|string|Ein Array mit Tags für das Asset.
||description|string|Eine kurze Beschreibung des Assets (zwei bis drei Zeilen).
|Schema|||Das Schema beschreibt die Struktur der Daten. Es führt die Namen und Typen von Attributen (Spalte, Attribut, Feld usw.) sowie andere Metadaten auf. Diese Informationen werden alle von der Datenquelle abgeleitet. In der Regel ist pro Asset ein einzelnes Schemaelement vorhanden.
||columns|Column|Ein Array mit Spaltenobjekten. Diese beschreiben die Spalte mit aus der Datenquelle abgeleiteten Informationen.
|SchemaDescription|||Enthält für jedes im Schema definierte Attribut eine Beschreibung und eine Gruppe von Tags. Jeder Benutzer des Systems kann eine eigene Beschreibung und eigene Tags hinzufügen. Nur dieser Benutzer kann das Beschreibungsobjekt bearbeiten. (Administratoren und Assetbesitzer können das SchemaDescription-Objekt zwar löschen, aber nicht bearbeiten.) Diese Elemente werden separat verwaltet. Es gibt also ein Array mit SchemaDescription-Objekten für die einzelnen Assets (eins für jeden Benutzer, der einen Beitrag zu dem Asset geleistet hat, sowie ggf. ein weiteres mit Informationen, die sich aus der Datenquelle ableiten). „SchemaAttributes“ ist nur lose an das Schema gebunden und daher unter Umständen nicht immer synchronisiert. Dies kann dazu führen, dass in „SchemaDescription“ Spalten beschrieben werden, die nicht mehr im Schema vorhanden sind, oder dass nicht auf eine kürzlich hinzugefügte Spalte verwiesen wird. Die Synchronisierung ist Sache des Autors. Die Datenquelle kann auch beschreibende Informationen enthalten. Hierbei handelt es sich dann um ein zusätzliches SchemaDescription-Objekt, das beim Ausführen des Tools erstellt wird.
||columnDescriptions|ColumnDescription|Ein ColumnDescriptions-Array zum Beschreiben der Spalten im Schema.
|Experten|||Enthält eine Liste mit Benutzern, die als Experten für das Dataset gelten. Expertenmeinungen (also Beschreibungen) werden in der UX zuerst aufgelistet. Jeder Benutzer kann eine eigene Expertenliste angeben. Nur dieser Benutzer kann das Expertenobjekt bearbeiten. (Administratoren und Assetbesitzer können das Expertenobjekt zwar löschen, aber nicht bearbeiten.)
||experts|string|Array mit E-Mail-Adressen.
|Vorschau|||Die Vorschau enthält eine Momentaufnahme der Top-20-Datenzeilen für das Asset. Sie ist nur für einige Assettypen sinnvoll. (Mit anderen Worten: Eine Vorschau ist sinnvoll für eine Tabelle, aber nicht für eine Kennzahl).
||preview|Objekt|Ein Array mit Objekten, das eine Spalte darstellt. Jedes Objekt besitzt eine Eigenschaftenzuordnung zu einer Spalte mit einem Spaltenwert für die Zeile.
|AccessInstruction|||Enthält Informationen zur Zugriffsanforderung auf die Datenquelle. Diese Information umfasst, was im Feld "Zugriff anfordern" im Catalog-Portal angezeigt wird.
||mimeType|string|Der MIME-Typ des Inhalts.
||Inhalt|string|Die Anweisungen für den Zugriff auf dieses Datenasset. Dies kann eine URL, eine E-Mail-Adresse oder einen Satz von Anweisungen sein.
|TableDataProfile|||
||numberOfRows|int|Die Anzahl der Zeilen im Dataset
||size|lang|Die Größe des Datasets in Bytes.
||schemaModifiedTime|string|Der Zeitpunkt der letzten Schemaänderung
||dataModifiedTime|string|Der Zeitpunkt der letzten Datasetänderung (Daten wurden hinzugefügt, geändert oder gelöscht)
|ColumnsDataProfile|||
||columns|ColumnDataProfile|Die Anzahl der Zeilen im Dataset
|Dokumentation|||Einem bestimmten Asset kann nur eine Dokumentation zugeordnet sein.
||mimeType|string|Der MIME-Typ des Inhalts.
||Inhalt|string|Der Inhalt der Dokumentation.


### Allgemeine Typen

Allgemeine Typen können als Typen für Eigenschaften verwendet werden, sind aber keine Elemente.

|**Allgemeiner Typ**|**Eigenschaften**|**Datentyp**|**Kommentare**
|---|---|---|---
|DataSourceInfo||||
||sourceType|string|Beschreibt den Typ der Datenquelle, d. h. SQL Server, Oracle Database usw.
||objectType|string|Beschreibt den Typ des Objekts in der Datenquelle (beispielsweise Tabelle oder Sicht für SQL Server).
||formatType|string|Beschreibt die Struktur der Daten. Mögliche Werte sind „structured“ und „unstructured“.
|SecurityPrincipal||||
||upn|string|Eindeutige E-Mail-Adresse des Benutzers.
||firstName|string|Der Vorname des Benutzers (für die Anzeige).
||lastName|string|Der Nachname des Benutzers (für die Anzeige).
|Column||||
||name|string|Der Name der Spalte oder des Attributs.
||type|string|Der Datentyp der Spalte oder des Attributs. Die zulässigen Typen hängen vom Datenquellentyp des Assets ab. Nur eine Teilmenge von Typen wird unterstützt.
||maxLength|int|Die maximal zulässige Länge für die Spalte oder das Attribut. Abgeleitet von der Datenquelle. Gilt nur für einige Datenquellentypen.
||Precision|byte|Die Genauigkeit für die Spalte oder das Attribut. Abgeleitet von der Datenquelle. Gilt nur für einige Datenquellentypen.
||isNullable|Boolean|Gibt an, ob die Spalte einen Nullwert enthalten kann. Abgeleitet von der Datenquelle. Gilt nur für einige Datenquellentypen.
||expression|string|Wenn es sich bei dem Wert um eine berechnete Spalte handelt, enthält dieses Feld den Ausdruck, der den Wert angibt. Abgeleitet von der Datenquelle. Gilt nur für einige Datenquellentypen.
||defaultValue|Objekt|Der Standardwert, der eingefügt wird, wenn in der insert-Anweisung für das Objekt kein Wert angegeben wird. Abgeleitet von der Datenquelle. Gilt nur für einige Datenquellentypen.
|ColumnDescription||||
||tags|string|Ein Array mit Tags zum Beschreiben der Spalte.
||description|string|Eine Beschreibung der Spalte.
||columnName|string|Der Name der Spalte, auf die sich die Informationen beziehen.
|ColumnDataProfile||||
||columnName|string|Der Name der Spalte
||Typ|string|Der Typ der Spalte
||min|string|Der Mindestwert im Dataset
||max|string|Der Höchstwert im Dataset
||avg|double|Der durchschnittliche Wert im Dataset
||stdev|double|Die Standardabweichung für das Dataset
||nullCount|int|Die Anzahl der Nullwerte im Dataset
||distinctCount|int|Die Anzahl der unterschiedlichen Werte im Dataset

## Rollen und Autorisierung

Microsoft Azure Data Catalog bietet Autorisierungsfunktionen für asset- und anmerkungsbezogene CRUD-Vorgänge.

## Wichtige Begriffe

Azure Data Catalog verwendet zwei Autorisierungsmechanismen:

- Rollenbasierte Autorisierung
- Berechtigungsbasierte Autorisierung

### Rollen

Drei Rollen stehen zur Verfügung: **Administrator**, **Besitzer** und **Mitwirkender**. Jede Rolle hat ihren eigenen Umfang und ihre eigenen Rechte. Diese werden in der folgenden Tabelle zusammengefasst.

|**Rolle**|**Umfang**|**Rechte**
|---|---|---
|Administrator|Katalog (also alle Assets/Anmerkungen im Katalog)|Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions
|Besitzer|Jedes Asset (Stammelement)|Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions
|Mitwirkender|Jedes einzelne Asset und jede einzelne Anmerkung|Read Update Delete ViewRoles – Hinweis: Wird dem Mitwirkenden die Leseberechtigung für das Objekt entzogen, werden ihm auch die anderen Rechte entzogen.

> [AZURE.NOTE] Die Rechte **Read**, **Update**, **Delete** und **ViewRoles** gelten für jedes Element (Asset oder Anmerkung), die Rechte **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** und **ViewPermissions** gelten dagegen nur für das Stammasset.
>
>Das Recht **Delete** gilt für ein Element sowie für alle dazugehörigen untergeordneten Elemente. Beim Löschen eines Assets werden also beispielsweise auch sämtliche Anmerkungen für dieses Asset gelöscht.

### Berechtigungen

Eine Berechtigung ist eine Liste mit Zugriffssteuerungseinträgen. Jeder Zugriffssteuerungseintrag weist einem Sicherheitsprinzipal eine Gruppe von Rechten zu. Berechtigungen können nur für ein Asset (Stammelement) angegeben werden und gelten für das Asset und alle dazugehörigen untergeordneten Elemente.

In der Vorschauversion von **Azure Data Catalog** wird in der Berechtigungsliste nur das Recht **Read** unterstützt, um die Einschränkung der Sichtbarkeit eines Assets zu ermöglichen.

Standardmäßig ist jeder authentifizierte Benutzer zum Lesen aller Elemente im Katalog berechtigt, es sei denn, die Sichtbarkeit ist auf die Gruppe von Prinzipalen in den Berechtigungen beschränkt.

## REST-API

Mit den Ansichtselementanforderungen **PUT** und **POST** können Sie Rollen und Berechtigungen steuern. Neben der Elementnutzlast können zwei Systemeigenschaften angegeben werden: **\_\_roles** und **\_\_permissions**.

> [AZURE.NOTE]
>
> **__**\_\_permissions** gilt nur für ein Stammelement.
>
> Die Rolle **Besitzer** gilt nur für ein Stammelement.
>
> Bei der Erstellung eines Elements im Katalog wird standardmäßig der entsprechende **Mitwirkende** als derzeit authentifizierter Benutzer festgelegt. Soll ein Element für jeden Benutzer aktualisierbar sein, muss der **Mitwirkende** in der Eigenschaft **\_\_roles** bei der Erstveröffentlichung des Elements auf den speziellen Sicherheitsprinzipal <Everyone> festgelegt werden (siehe Beispiel weiter unten). Der **Mitwirkende** kann nicht geändert werden (auch nicht vom **Administrator** oder **Besitzer**) und bleibt während der gesamten Lebensdauer eines Elements unverändert. Der einzige unterstützte Wert zum expliziten Festlegen des **Mitwirkenden** ist <Everyone>. Der **Mitwirkende** kann also nur ein Benutzer sein, der ein Element erstellt hat, oder <Everyone>.

### Beispiele
**Festlegen des Mitwirkenden auf <Everyone> beim Veröffentlichen eines Elements.**

Die Objekt-ID des speziellen Sicherheitsprinzipals <Everyone> lautet „00000000-0000-0000-0000-000000000201“.

**POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2015-07.1.0-Preview

Anforderungen an **Azure Data Catalog (ADC)** geben möglicherweise eine HTTP 302-Antwort zurück, um die Umleitung an einen anderen Endpunkt anzugeben. Als Reaktion auf eine 302-Antwort muss der Aufrufer die Anforderung möglicherweise mit der URL, die vom Antwortheader des Standorts angegeben wurde, neu ausstellen.


> [AZURE.NOTE] Einige HTTP-Clientimplementierungen können Anforderungen als Reaktion auf eine 302-Antwort vom Server neu ausstellen, in der Regel werden die **Autorisierungsheader** aber aus der Anforderung entfernt. Der Autorisierungsheader ist für Anforderungen an ADC erforderlich. Vergewissern Sie sich daher, dass der Autorisierungsheader noch bereitgestellt ist, wenn eine Anforderung an einen von ADC festgelegten Umleitungsstandort neu ausgestellt wird. Der folgende Beispielcode verdeutlicht dies mithilfe des Objekts „.NET HttpWebRequest“.


**Body**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**Zuweisen von Besitzern und Einschränken der Sichtbarkeit für ein vorhandenes Stammelement.**

**PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE] Bei „PUT“ muss im Text keine Elementnutzlast angegeben werden: „PUT“ kann zur reinen Aktualisierung von Rollen und/oder Berechtigungen verwendet werden.

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png

<!---HONumber=AcomDC_0316_2016-->
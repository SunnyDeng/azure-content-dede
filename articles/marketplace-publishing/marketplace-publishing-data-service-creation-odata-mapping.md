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

# Zuordnen eines vorhandenen Webdiensts zu OData über CSDL

Dieser Artikel enthält eine Übersicht darüber, wie eine CSDL für die Zuordnung eines vorhandenen Diensts zu einem kompatiblen OData-Dienst verwendet wird. Dabei wird die Erstellung des Zuordnungsdokuments (CSDL) erläutert, das die vom Client über einen Dienstaufruf gesendete Eingabeanforderung und die über einen kompatiblen OData-Feed an den Client zurückgesendeten Ausgabe(daten) transformiert. Microsoft Azure Marketplace macht die Dienste mithilfe des OData-Protokolls für Endbenutzer verfügbar. Dienste, die von Inhaltsanbietern (Datenbesitzern) verfügbar gemacht werden, werden in einer Vielzahl von Formaten wie REST, SOAP usw. zur Verfügung gestellt.

## Beschreibung der CSDL und ihrer Struktur
Eine CSDL (Conceptual Schema Definition Language) ist eine Spezifikation, die festlegt, wie ein Web- oder Datenbankdienst in gängiger XML-Sprache für den Azure Marketplace beschrieben wird.

Einfache Übersicht über den **Anforderungsfluss**:

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Der **Datenfluss** verläuft in entgegengesetzter Richtung:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Abbildung 1** veranschaulicht, wie ein Client über den Azure Marketplace Daten von einem Inhaltsanbieter (Ihrem Dienst) erhält. Die CSDL wird von der Zuordnungs-/-Transformationskomponente verwendet, um die Anforderungs- und Datenübergabe zwischen dem/den Dienst(en) des Inhaltsanbieters und dem anfordernden Client zu verarbeiten.

*Abbildung 1: Detaillierter Fluss vom anfordernden Client zum Inhaltsanbieter über den Azure Marketplace*

  ![Abbildung](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Hintergrundinformationen zum Atom-, Atom Pub- und OData-Protokoll, auf dem die Azure Marketplace-Erweiterungen basieren, finden Sie unter [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx).

Auszug aus dem vorangehenden Link: *"Der Zweck von Open Data Protocol (nachstehend OData) ist die Bereitstellung eines REST-basierten Protokolls für CRUD-Vorgänge (Create, Read, Update und Delete) für Ressourcen, die als Datendienste verfügbar gemacht werden. Ein "Datendienst" ist ein Endpunkt, an dem Daten aus mindestens einer "Sammlung" verfügbar gemacht werden. Jede dieser Sammlungen enthält 0 oder mehr "Einträge" bestehend aus typisierten Name-Wert-Paaren. OData wird von Microsoft im Rahmen der OASIS (Organization for die Advancement of Structured Information Standards)-Standards veröffentlicht. Jeder interessierte Benutzer kann also Server, Clients oder Tools ohne Lizenzgebühren oder Einschränkungen erstellen."*

### Die folgenden drei wichtigen Elemente müssen von der CSDL definiert werden:

- Der **Endpunkt** des Service Providers. Die Webadresse (URI) des Diensts.
- Die **Datenparameter**, die als Eingabe an den Service Provider übergeben werden. Die Definitionen der Parameter, die bis hinunter zum Datentyp an den Dienst des Inhaltsanbieters gesendet werden.
- Das **Schema** der an den anfordernden Dienst zurückgegebenen Daten. Das Schema der Daten, die über den Dienst des Inhaltsanbieters bereitgestellt werden, z. B. Container, Sammlungen/Tabellen, Variablen/Spalten und Datentypen.

Das folgende Diagramm zeigt eine Übersicht des Ablaufs ab dem Punkt, an dem der Client in die OData-Anweisung eintritt (Aufruf des Inhaltsanbieter-Webdiensts) bis zu dem Punkt, an dem die Ergebnisse/Daten zurückgegeben werden.

  ![Abbildung](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### Schritte:

1. Der Client sendet eine Anforderung inklusive der in XML definierten Eingabeparameter über einen Dienstaufruf an den Azure Marketplace.
2. CSDL wird verwendet, um den Dienstaufruf zu überprüfen.
	- Der formatierte Dienstaufruf wird anschließend vom Azure Marketplace an den Inhaltsanbieterdienst gesendet.
3. Der Webdienst wird ausgeführt und führt die Aktion des HTTP-Verbs (d. h. GET) aus. Die Daten werden an den Azure Marketplace zurückgegeben, wo die angeforderten Daten (falls vorhanden) im XML-Format für den Client verfügbar gemacht werden, wobei die in der CSDL definierte Zuordnung verwendet wird.
4. Die Daten (falls vorhanden) werden im XML- oder JSON-Format an den Client gesendet.

## Definitionen

### OData-ATOM-Pub

Eine Erweiterung des ATOM Pub-Protokolls, bei dem jeder Eintrag eine Zeile eines Resultsets darstellt. Die Inhaltskomponente des Eintrags wird erweitert, um die Werte der Zeile – als Schlüssel-Wert-Paare – aufzunehmen. Weitere Informationen finden Sie unter: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### CSDL – Conceptual Schema Definition Language

Ermöglicht die Definition von Funktionen (SPROCs) und Entitäten, die über eine Datenbank verfügbar gemacht werden. Weitere Informationen finden Sie unter: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)

> [AZURE.TIP]Klicken Sie auf den Dropdownpfeil **Weitere Versionen**, und wählen Sie eine Version aus, falls der Artikel nicht angezeigt wird.

### EDM – Entry Data Model
- Übersicht: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx](OverviewLink)
[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
- Vorschau: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx](PreviewLink)
[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
- Datentypen: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx](DataTypesLink)
[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Im Folgenden sehen Sie den detaillierten Links-nach-Rechts-Ablauf ab dem Punkt, an dem der Client in die OData-Anweisung eintritt (Aufruf des Inhaltsanbieter-Webdienst) bis zu dem Punkt, an dem die Ergebnisse/Daten zurückgegeben werden:

  ![Abbildung](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## CSDL-Grundlagen

Eine CSDL (Conceptual Schema Definition Language) ist eine Spezifikation, die festlegt, wie ein Web- oder Datenbankdienst in gängiger XML-Sprache für den Azure Marketplace beschrieben wird. Die CSDL beschreibt die wichtigsten Teile, die die **Übergabe von Daten aus der Datenquelle an den Azure Marketplace** ermöglichen. Die Hauptkomponenten werden im Folgenden beschrieben:

- Schnittstelleninformationen, die alle öffentlich verfügbaren Funktionen (FunctionImport-Knoten) beschreiben.
- Datentypinformationen für alle Nachrichtenanforderungen (Eingang) und Nachrichtenantworten (Ausgang) (EntityContainer-/EntitySet-/EntityType-Knoten)
- Bindungsinformationen zum verwendeten Transportprotokoll (Header-Knoten)
- Adressinformationen für die Suche nach dem angegebenen Dienst (BaseURI-Attribut)

CSDL stellt also kurz gesagt einen plattform- und sprachunabhängigen Vertrag zwischen dem Dienstanforderer und dem Service Provider dar. Mithilfe der CSDL kann ein Client einen Web-/Datenbankdienst suchen und eine seiner öffentlich verfügbaren Funktionen aufrufen.

### Beziehung einer CSDL zu einer Datenbank oder Sammlung
**Die CSDL-Spezifikation**

CSDL ist eine XML-Grammatik zur Beschreibung eines Webdiensts. Die Spezifikation selbst wird in 4 Hauptelemente unterteilt: EntitySet, FunctionImport, NameSpace und EntityType.

Um diese Abstraktion verständlicher zu machen, setzen wir eine CSDL in eine Tabelle um.

Beachten Sie Folgendes:

  CSDL stellt also kurz gesagt einen plattform- und sprachunabhängigen Vertrag zwischen dem **Dienstanforderer** und dem **Service Provider** dar. Mithilfe der CSDL kann der **Client** einen** Web-/Datenbankdienst** suchen und eine seiner öffentlich verfügbaren **Funktionen** aufrufen.

Bei einem Datendienst können Sie sich die vier Teile einer CSDL als Datenbank, Tabelle, Spalte und gespeicherte Prozedur vorstellen.

Sie werden bei einem Datendienst wie folgt in Beziehung gesetzt:

- EntityContainer ~= Datenbank
- EntitySet ~= Tabelle
- EntityType ~= Spalten
- FunctionImport ~= gespeicherte Prozedur

**HTTP-Verben zulässig** - GET: Gibt Werte (eine Sammlung) aus der Datenbank zurück. - POST: wird zur Übergabe von Daten an und optional zur Rückgabe von Werten aus der Datenbank verwendet (erstellt einen neuen Eintrag in der Sammlung, gibt ID/URI zurück). - DELETE: Löscht Daten (eine Sammlung) aus der Datenbank. - PUT: Aktualisiert Daten in einer Datenbank (ersetzt eine Sammlung oder erstellt eine neue)

## Metadaten-/Zuordnungsdokument

Das Metadaten-/Zuordnungsdokument wird für die Zuordnung vorhandener Webdienste eines Inhaltsanbieters verwendet, damit sie vom Azure Marketplace-System als OData-Webdienst verfügbar gemacht werden können. Es basiert auf CSDL und implementiert einige CSDL-Erweiterungen, um die Anforderungen REST-basierter Webdienste zu erfüllen, die über den Azure Marketplace verfügbar gemacht werden. Die Erweiterungen finden Sie im [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04)-Namespace.

Im Folgenden ein CSDL-Beispiel: (Kopieren Sie die folgende Beispiel-CSDL, fügen Sie sie in einen XML-Editor ein, und passen Sie sie an Ihren Dienst an. Fügen Sie es dann auf der Registerkarte "DataService" unter "CSDL-Zuordnung" ein, wenn Sie den Dienst im [Azure Marketplace-Veröffentlichungsportal](https://publish.windowsazure.com) erstellen.)

**Begriffe:** Beziehung der CSDL-Begriffe zu PPUI ([Publishing Portal](https://publish.windowsazure.com) UI)-Begriffen. - Der "Title" des Angebots in der PPUI bezieht sich auf MyWebOffer. - MyCompany in der PPUI bezieht sich auf "Firmenname" in der UI des [Verkäuferdashboards](https://sellerdashboard.microsoft.com). - API bezieht sich auf einen Web- oder Datendienst (einen Plan in der PPUI).

**Hierarchie:** Ein Unternehmen (Inhaltsanbieter) besitzt Angebot(e) mit einem Plan/mehreren Plänen. Dabei handelt es sich um Dienst(e), die in einer API aufgeführt sind.

### CSDL-Beispiel "WebService"

Stellt eine Verbindung mit einem Dienst her, der den Endpunkt einer Webanwendung (z. B. eine C#-Anwendung) verfügbar macht.

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
        	<!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

        	<!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"	d:IsPrimaryKey="True" Type="Int32"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"	Type="Double"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"	Type="String"	Nullable="false" d:Map="./City"/>
        <Property Name="State"	Type="String"	Nullable="false" d:Map="./State"/>
        <Property Name="Zip"	Type="Int32"	Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"	Type="DateTime"	Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP]Weitere CSDL-Webdienstbeispiele finden Sie im Artikel [Beispiele für die Zuordnung eines vorhandenen Webdiensts zu OData über CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md).

###CSDL-Beispiel "DataService"

Stellt eine Verbindung mit einem Dienst her, der eine Datenbanktabelle oder -sicht als Endpunkt verfügbar macht. Das folgende Beispiel zeigt zwei APIs mit CSDL einer datenbankbasierten API (anstelle von Tabellen können Sichten verwendet werden).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        	Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
        	Map is the schema.tabel or schema.view
        	dals.TableName is the table Name
        	Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
        	dals:IsExposed determines if the table schema is exposed (generally true).
        	dals:IsView (optional) true if this is based on a view rather than a table
        	dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
        	dals:ColumnName is the name of the column in the table /view.
        	Type is the emd.SimpleType
        	Nullable determines if NULL is a valid output value
        	dals.CharMaxLenght is the maximum length of the output value
        	Name is the name of the Property and is exposed to the client facing UI
        	dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
        	IsQueryable is the Boolean that determines if the column can be used in a database query
        	(For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
        	dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
        	dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## Weitere Informationen
- Wenn Sie mehr über bestimmte Knoten und ihre Parameter erfahren möchten, lesen Sie den Artikel [OData-Zuordnungsknoten für Datendienste](marketplace-publishing-data-service-creation-odata-mapping-nodes.md). Dort finden Sie Definitionen und Erläuterungen, Beispiele und Kontext für Anwendungsfälle.
- Wenn Sie an Beispielen interessiert sind, lesen Sie den Artikel [Beispiele für die OData-Zuordnung im Datendienst](marketplace-publishing-data-service-creation-odata-mapping-examples.md), um Beispielcode kennenzulernen sowie Codesyntax und Kontext zu verstehen.
- Um zum vorgeschriebenen Pfad für die Veröffentlichung eines Datendiensts im Azure Marketplace zurückzukehren, lesen Sie den Artikel [Leitfaden zum Veröffentlichen von Datendiensten](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_1203_2015-->
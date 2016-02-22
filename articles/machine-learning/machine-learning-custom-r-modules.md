<properties 
	pageTitle="Erstellen von benutzerdefinierten R-Modulen in Azure Machine Learning | Microsoft Azure" 
	description="Schnellstart für das Erstellen von benutzerdefinierten R-Modulen in Azure Machine Learning." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/08/2016" 
	ms.author="bradsev;ankarloff" />


# Benutzerdefinierte R-Module in Azure Machine Learning  


Dieses Thema erläutert, wie ein benutzerdefiniertes R-Modul in Azure Machine Learning bereitgestellt wird. Es wird beschrieben, was ein benutzerdefiniertes R-Modul ist und welche Dateien verwendet werden, um es zu definieren. Es veranschaulicht, wie die Dateien erstellt werden, die ein Modul definieren, und wie dieses Modul zur Bereitstellung in einem Machine Learning-Arbeitsbereich registriert wird. Die in der Definition des benutzerdefinierten Moduls verwendeten Elemente und Attribute werden dann ausführlicher beschrieben. Ebenfalls wird erläutert, wie zusätzliche Funktionen und Dateien sowie mehrere Ausgaben zu verwenden sind.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Was ist ein benutzerdefiniertes R-Modul?
Ein **benutzerdefiniertes Modul** ist ein vom Benutzer bestimmtes Modul, dass in Ihrem Arbeitsbereich hochgeladen und als Teil eines Azure Machine Learning Experiments ausgeführt werden kann. Ein **benutzerdefiniertes R-Modul** ist ein benutzerdefiniertes Modul, das eine vom Benutzer erstellte R-Funktion ausführt. R ist eine Programmiersprache für statistische Verarbeitung und Grafiken, die häufig von Statistikern und Datenanalysten für die Implementierung von Algorithmen verwendet wird. Derzeit ist R die einzige Sprache, die von benutzerdefinierten Modulen unterstützt wird, doch ist für zukünftige Versionen die Unterstützung weiterer Sprachen vorgesehen.

Benutzerdefinierte Module haben insofern einen **erstrangigen Status** in Azure Machine Learning, als dass sie genau wie andere Module verwendet werden können. Sie können mit anderen Modulen ausgeführt werden, einschließlich in veröffentlichten Experimenten oder Visualisierungen. Benutzer haben die Kontrolle über den vom Modul implementierten Algorithmus, die verwendeten Eingangs- und Ausgangsports, die Parameter für die Modellierung und andere Laufzeitmerkmale. Ein benutzerdefiniertes Modul ist nur in dem Arbeitsbereich verfügbar, in dem es erstellt wurde, und kann nicht für Community-Experimente veröffentlicht werden.

## Dateien in einem benutzerdefinierten R-Modul
Ein benutzerdefiniertes R-Modul wird durch eine ZIP-Datei definiert, die mindestens zwei Dateien enthält:

* Eine **Quelldatei**, die die vom Modul offengelegte R-Funktion implementiert
* Eine **XML-Definitionsdatei**, die die benutzerdefinierte Modul-Schnittstelle beschreibt

Weitere Zusatzdateien können auch in der ZIP-Datei eingeschlossen werden und Funktionen bereitstellen, auf die vom benutzerdefinierten Modul zugegriffen werden kann. Diese Option wird weiter unten erläutert.

## Schnellstart-Beispiel: Definieren, Zusammenfassen und Registrieren eines benutzerdefinierten R-Moduls
In diesem Beispiel wird veranschaulicht, wie die erforderlichen Dateien für ein benutzerdefiniertes R-Modul erstellt und in einer ZIP-Datei komprimiert werden und dann das Modul im Machine Learning-Arbeitsbereich registriert wird. Das Beispiel-ZIP-Paket und die Beispiel-Dateien können [hier](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409) heruntergeladen werden.

Denken Sie zum Beispiel an ein benutzerdefiniertes Modul **Add Rows**, das die Standardimplementierung des Moduls "Add Rows" ändert, welches zum Verketten von Zeilen (Vorkommen) aus zwei DataSets (DataFrames) verwendet wird. Das Standardmodul "Add Rows" hängt die Zeilen des zweiten Eingabe-DataSets mit dem "rbind"-Algorithmus am Ende des ersten Eingabe-DataSets an. Die benutzerdefinierte `CustomAddRows`Funktion akzeptiert auf ähnliche Weise zwei DataSets, aber auch einen zusätzlichen booleschen "swap"-Parameter als Eingabe. Wenn der "swap"-Parameter **FALSE** ist, wird dasselbe DataSet wie bei der Standard-Implementierung zurückgegeben. Wenn der "swap"-Parameter jedoch **TRUE** ist, werden stattdessen Zeilen des ersten Eingabe-DataSet an das Ende des zweiten DataSet angehängt. Die Datei, die die R `CustomAddRows` Funktion implementiert, die vom Modul **Custom Add Rows** offengelegt wird, enthält den folgenden R-Code.

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
	{
		if (swap)
		{
			return (rbind(dataset2, dataset1));
		}
		else
		{
			return (rbind(dataset1, dataset2));
		} 
	} 

Um diese `CustomAddRows`Funktion als Azure Machine Learning-Modul offenzulegen, muss eine XML-Datei erstellt werden, um anzugeben, wie das Modul **Custom Add Rows** aussehen und sich verhalten soll.

	<!-- Defined a module using an R Script -->
	<Module name="Custom Add Rows">
	    <Owner>Microsoft Corporation</Owner>
	    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is false, and vice versa when Swap is true.</Description>
	
	<!-- Specify the base language, script file and R function to use for this module. -->		
	    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />  
		
	<!-- Define module input and output ports -->
	<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
	    <Ports>
			<Input id="dataset1" name="Dataset 1" type="DataTable">
				<Description>First input dataset</Description>
			</Input>
			<Input id="dataset2" name="Dataset 2" type="DataTable">
				<Description>Second input dataset</Description>
			</Input>
			<Output id="dataset" name="Dataset" type="DataTable">
				<Description>Combined dataset</Description>
			</Output>
	    </Ports>
		
	<!-- Define module parameters -->
	    <Arguments>
			<Arg id="swap" name="Swap" type="bool" >
				<Description>Swap input datasets.</Description>
			</Arg>
	    </Arguments>
	</Module>

 
Beachten Sie, dass der Wert der **id**-Attribute der **Input**- und **Arg**-Elemente in der XML-Datei genau mit den Funktionsparameternamen des R-Codes übereinstimmen muss (*dataset1*, *dataset2* und *swap* im Beispiel). Entsprechend muss der Wert des **entryPoint**-Attributs des **Language**-Elements genau mit dem Namen der Funktion im R-Skript übereinstimmen (*CustomAddRows* im Beispiel). Im Gegensatz dazu muss das **ID**-Attribut für die **Ausgabe**-Elemente nicht den Variablen im R-Skript entsprechen. Wenn mehr als eine Ausgabe erforderlich ist, einfach eine Liste der R-Funktion zurückgeben, auf der die Ergebnisse in derselben Reihenfolge platziert sind, wie die Ausgaben in der XML-Datei deklariert sind.

Speichern Sie diese beiden Dateien als *CustomAddRows.R* und *CustomAddRows.xml* und zippen Sie sie zusammen in einer *CustomAddRows.zip* Datei.

Um sie im Machine Learning-Arbeitsbereich zu registrieren, besuchen Sie den Arbeitsbereich in Machine Learning Studio, klicken Sie unten auf die Schaltfläche **+NEW** und wählen Sie **MODULE -> FROM ZIP PACKAGE**, um das neue Modul "Custom Add Rows" hochzuladen.

![ZIP-Datei hochladen](./media/machine-learning-custom-r-modules/upload-from-zip-package.png)

Auf das Modul **Custom Add Rows** kann jetzt von Machine Learning-Experimenten aus zugegriffen werden.

## Elemente in der XML-Definitionsdatei

### Modul-Elemente
Das**Modul**-Element wird verwendet, um ein benutzerdefiniertes Modul in der XML-Datei zu definieren. Mehrere Module können in einer XML-Datei definiert werden, in der mehrere **Modul**-Elemente verwendet werden. Jedes Modul in Ihrem Arbeitsbereich muss einen eindeutigen Namen haben. Registrieren Sie ein benutzerdefiniertes Modul mit dem gleichen Namen, den ein vorhandenes benutzerdefiniertes Modul hat. Dadurch wird das vorhandene Modul durch das neue ersetzt. Benutzerdefinierte Module können jedoch mit dem gleichen Namen registriert sein, den ein vorhandenes Azure Machine Learning-Modul hat, das in der benutzerdefinierten Kategorie der Modul-Palette angezeigt wird.

	<Module name="Custom Add Rows" isDeterministic="false"> 
		<Owner>Microsoft Corporation</Owner>
		<Description>Appends one dataset to another...</Description>/> 


Innerhalb des**Modul**-Elements können Sie ein optionales **Besitzer**-Element angeben, das in das Modul eingebettet ist, sowie auch ein **Beschreibungs**-Element, das als Textformat in Direkthilfe für das Modul angezeigt wird, und - wenn Sie auf das Modul zeigen - auf der Machine Learning-Benutzeroberfläche.

**Regeln für Zeichengrenzen in den Modul-Elementen**:

* Der Wert des **Namen**-Attributs im **Modul**-Element darf maximal 64 Zeichen lang sein. 
* Der Inhalt des **Beschreibungs**-Elements darf 128 Zeichen nicht überschreiten.
* Der Inhalt des **Besitzer**-Elements darf 32 Zeichen nicht überschreiten.


**Anzeigen, ob die Ergebnisse eines Moduls deterministisch oder nicht deterministisch sind**

Standardmäßig gelten alle Module als deterministisch. Das heißt, bei einer unveränderlichen Gruppe von Parametern sollte das Modul jedes Mal, wenn es ausgeführt wird, die gleichen Ergebnisse zurückgeben. Bei diesem Verhalten wird Azure Machine Learning Studio die Module nicht erneut ausführen, die als deterministisch gekennzeichnet sind, es sei denn, ein Parameter oder die Eingabedaten haben sich geändert. Zwischengespeicherte Ergebnisse werden zurückgegeben, wodurch Experimente schneller ausgeführt werden.

Wenn Ihr Modul jedoch eine Funktion verwendet, die jedes Mal, wenn sie ausgeführt wird, unterschiedliche Ergebnisse zurückgibt – beispielsweise RAND oder eine Funktion, die das aktuelle Datum oder die Uhrzeit zurückgibt – können Sie das Modul als nicht deterministisch festlegen, indem Sie das optionale Attribut **IsDeterministic** auf **false** setzen. Das Modul wird jedes Mal, wenn das Experiment ausgeführt wird, erneut ausgeführt, auch wenn die Moduleingabe und die Parameter sich nicht geändert haben.

### Definition des "Language"-Elements
Das**Sprachen**-Element in Ihrer XML-Definitionsdatei wird verwendet, um die Sprache des benutzerdefinierten Moduls anzugeben. R ist derzeit die einzige unterstützte Sprache. Der Wert des **SourceFile**-Attributs muss der Name der R-Datei sein, die die aufzurufende Funktion enthält, wenn das Modul ausgeführt wird. Diese Datei muss im Zip-Paket sein. Der Wert des **EntryPoint**-Attributs ist der Name der aufgerufenen Funktion und muss mit einer in der Quelldatei definierten gültigen Funktion übereinstimmen.

	<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### Ports
Die Eingabe- und Ausgabeports für ein benutzerdefiniertes Modul sind in den untergeordneten Elementen des Abschnitts **Ports** der XML-Definitionsdatei angegeben. Die Reihenfolge dieser Elemente bestimmt das Layout (UX) für die Benutzer. Die erste untergeordnete **Eingabe** oder **Ausgabe**, die im **Ports** -Element der XML-Datei aufgeführt wird, wird der Eingabe-Port außen links in der Machine Learning UX sein. Jeder Eingabe- und Ausgabeport verfügt möglicherweise über ein untergeordnetes **Beschreibungs**-Element, das den Text angibt, der gezeigt wird, wenn ein Benutzer den Mauszeiger über den Port in der Machine Learning-Benutzeroberfläche bewegt.

**Port-Regeln**:

* Maximale Anzahl von **Eingabe- und Ausgabe-Ports** beträgt jeweils 8.

### Input-Elemente
Eingabeports ermöglichen Benutzern Daten an Ihren Arbeitsbereich und Ihre R-Funktion zu übergeben. Die unterstützten **Datentypen** für Eingabe-Ports lauten folgendermaßen:

**DataTable:** Dieser Typ wird als data.frame an Ihre R-Funktion übergeben. Tatsächlich werden alle Typen (z. B. CSV-Dateien oder ARFF-Dateien), die von Machine Learning unterstützt werden und mit **DataTable** kompatibel sind, automatisch in eine data.frame konvertiert.

		<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
        	<Description>Input Dataset 1</Description>
       	</Input>

Das**ID**-Attribut, das jedem **DataTable** Eingangs-Port zugeordnet ist, muss einen eindeutigen Wert haben, der mit seinem entsprechenden benannten Parameter in der R-Funktion übereinstimmen muss. Für optionale **DataTable**-Ports, die nicht als Eingabe in einem Experiment übergeben werden, wird der Wert **NULL** an die R-Funktion übergeben und optionale Zip-Ports werden ignoriert, wenn die Eingabe nicht verbunden ist. Das **IsOptional**-Attribut ist für die Typen **DataTable** und **Zip** optional und standardmäßig *False*.
	   
**Zip**: Benutzerdefinierte Module können ZIP-Dateien als Eingabe akzeptieren. Diese Eingabe wird im R-Arbeitsverzeichnis Ihrer Funktion entpackt.

		<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
        	<Description>Zip files will be extracted to the R working directory.</Description>
       	</Input>

Für benutzerdefinierte R-Module muss die ID für einen Zip-Port nicht mit den Parametern der R-Funktion übereinstimmen, da die Zip-Datei automatisch in das R-Arbeitsverzeichnis extrahiert wird.

**Eingabe-Regeln:**

* Der Wert des **ID**-Attributs des **Eingabe**-Elements muss ein gültiger R-Variablenname sein.
* Der Wert vom **ID**-Attribut des **Eingabe**-Elements darf 64 Zeichen nicht überschreiten.
* Der Wert des **Namen**-Attributs vom **Eingabe**-Element darf nicht länger als 64 Zeichen sein.
* Der Inhalt des **Beschreibungs**-Elements darf 128 Zeichen nicht überschreiten.
* Der Wert des **Typen**-Attributs vom **Eingabe**-Element muss *Zip* oder *DataTable* sein.
* Der Wert des **IsOptional** -Attributs vom **Eingabe**-Element ist nicht erforderlich (und standardmäßig *false*, wenn nicht angegeben); aber wenn er angegeben wird, muss er *true* oder *false* sein.

### Ausgabe-Elemente

**Standard-Ausgabe-Ports:** Ausgabe-Ports sind den Rückgabewerten Ihrer R-Funktion zugeordnet, die anschließend von nachfolgenden Modulen verwendet werden können. *DataTable* ist zurzeit der einzige unterstützte Standardtyp für Ausgabeports. (Unterstützung für *Learners* und *Transforms* in Kürze zu erwarten.) Eine *DataTable*-Ausgabe wird wie folgt definiert:

	<Output id="dataset" name="Dataset" type="DataTable">
		<Description>Combined dataset</Description>
	</Output>

Für Ausgaben in benutzerdefinierten R-Modulen muss der Wert des **ID**-Attributs mit nichts im R-Skript übereinstimmen, jedoch eindeutig sein. Für ein einzelne Modulausgabe muss der Rückgabewert der R-Funktion ein *data.frame* sein. Um mehr als ein Objekt eines unterstützten Datentyps auszugeben, müssen die entsprechende Ausgangs-Ports in der XML-Definitionsdatei angegeben und die Objekte als Liste zurückgegeben werden. Die Ausgangsobjekte werden den Ausgangs-Ports von links nach rechts zugewiesen und entsprechen der Reihenfolge, in der die Objekte in der zurückgegebenen Liste platziert werden.

Wenn Sie beispielsweise das Moul **Custom Add Rows** ändern möchten, um die ursprünglichen beiden DataSets, *dataset1* und *dataset2*, zusätzlich zu den beiden neu verknüpften *DataSets* ausgeben möchten (in Reihenfolge von links nach rechts als *dataset*, *dataset1*, *dataset2*), definieren Sie die Ausgabe-Ports in der Datei „CustomAddRows.xml“ wie folgt:

	<Ports> 
		<Output id="dataset" name="Dataset Out" type="DataTable"> 
			<Description>New Dataset</Description> 
		</Output> 
		<Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
			<Description>First Dataset</Description> 
		</Output> 
		<Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
			<Description>Second Dataset</Description> 
		</Output> 
		<Input id="dataset1" name="Dataset 1" type="DataTable"> 
			<Description>First Input Table</Description>
		</Input> 
		<Input id="dataset2" name="Dataset 2" type="DataTable"> 
			<Description>Second Input Table</Description> 
		</Input> 
	</Ports> 


Geben Sie dann die Liste der Objekte in einer Liste in der richtigen Reihenfolge in "CustomAddRows.R" zurück:

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
		if (swap) { dataset <- rbind(dataset2, dataset1)) } 
		else { dataset <- rbind(dataset1, dataset2)) 
		} 
	return (list(dataset, dataset1, dataset2)) 
	} 
	
**Visualisierungs-Ausgabe:** Sie können auch einen Ausgabeport des Typs *Visualisierung* angeben, der die Ausgabe vom R-Grafikgerät und die Konsolenausgabe anzeigt. Dieser Port ist nicht Teil der R-Funktionsausgabe und hat keinerlei Auswirkungen auf die Reihenfolge der anderen Ausgabe-Porttypen. Um den benutzerdefinierten Modulen einen Visualisierungsport hinzuzufügen, fügen Sie ein **Ausgabe**-Element mit einem *Visualisierungswert* für sein **Typen**-Attribut hinzu.

	<Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>
	
**Ausgabe-Regeln:**

* Der Wert des **ID**-Attributs vom **Ausgabe**-Element muss ein gültiger R-Variablenname sein.
* Der Wert vom **ID**-Attribut des **Ausgabe**-Elements darf 32 Zeichen nicht überschreiten.
* Der Wert des **Namen**-Attributs vom **Ausgabe**-Element darf nicht länger als 64 Zeichen sein.
* Der Wert des **Typen**-Attribut vom **Ausgabe**-Element muss *Visualisierung* sein.

### Argumente
Zusätzliche Daten können an die R-Funktion über die Modulparameter übergeben werden, die im **Argumente**-Element definiert sind. Diese Parameter werden im rechten äußersten Eigenschaftsbereich der Machine Learning-Benutzeroberfläche angezeigt, wenn das Modul ausgewählt ist. Argumente können jeder der unterstützten Typen sein oder Sie können bei Bedarf eine benutzerdefinierte Enumeration erstellen. Ähnlich wie die **Ports**-Elemente können **Argumente** -Elemente ein optionales **Beschreibungs**-Element besitzen, das den Text angibt, der angezeigt wird, wenn Sie mit der Maus auf den Namen des Parameters zeigen. Optionale Eigenschaften für ein Modul, wie z. B. der defaultValue, MinValue und MaxValue, können jedem Argument als Attribute eines **Eigenschaften**-Elements hinzugefügt werden. Gültige Eigenschaften für die **Eigenschaften**-Elemente hängen vom Argumenttyp ab und werden mit den unterstützten Argumenttypen beschrieben. Wie bei Eingaben und Ausgaben ist es wichtig, dass jedem Parameter eindeutige ID-Werte zugeordnet sind. Darüber hinaus müssen die ID-Werte den benannten Parametern in der R-Funktion entsprechen. In unserem Schnellstart-Beispiel war der zugeordnete ID-Parameter *swap*.

### Arg-Element
Ein Modul-Parameter wird mithilfe des untergeordneten**Arg**-Elements vom Abschnitt **Argumente** der XML-Definitionsdatei definiert. Wie bei den untergeordneten Elementen im Abschnitt **Ports** definiert die Reihenfolge der Parameter im Abschnitt **Argumente** das Layout in der UX. Die Parameter werden von oben nach unten in der Benutzeroberfläche in der gleichen Reihenfolge, in der sie in der XML-Datei definiert sind, angezeigt. Die von Machine Learning für Parameter unterstützten Typen sind unten aufgeführt.

**int** – ein Parameter des Typs Ganzzahl (32 Bit).

		<Arg id="intValue1" name="Int Param" type="int">
			<Properties min="0" max="100" default="0" />
			<Description>Integer Parameter</Description>
       </Arg>



* *Optionale Eigenschaften*: **min**, **max** und **Standard**

**double** – ein Parameter des Typs "double".

       <Arg id="doubleValue1" name="Double Param" type="double">
           <Properties min="0.000" max="0.999" default="0.3" />
		   <Description>Double Parameter</Description>
       </Arg>


* *Optionale Eigenschaften*: **min**, **max** und **Standard**

**bool ** – ein boolescher Parameter, der durch ein Kontrollkästchen in der UX dargestellt wird.

		<Arg id="boolValue1" name="Boolean Param" type="bool">
			<Properties default="true" />
			<Description>Boolean Parameter</Description>
		</Arg>



* *Optionale Eigenschaften*: **Standard** -false, wenn nicht festgelegt

**string**: eine Standardzeichenfolge.

        <Arg id="stringValue1" name="My string Param" type="string">
		   <Properties default="Default string value." isOptional="true" />
           <Description>String Parameter 1</Description>
        </Arg>


* *Optionale Eigenschaften*: **Standard** und **IsOptional** – eine optionale Zeichenfolge ohne einen Standardwert, der als Null an die R-Funktion übergeben wird, wenn ein Wert von einem Benutzer nicht anderweitig bereitgestellt wird.

**ColumnPicker**: ein Spaltenauswahl-Parameter. Dieser Typ wird in der UX als Spaltenauswahl gerendert. Das **Eigenschaft**-Element wird hier verwendet, um die ID des Ports anzugeben, von dem Spalten ausgewählt werden, wobei der Port-Zieltyp *DataTable* sein muss. Das Ergebnis der Spaltenauswahl wird als eine Liste von Zeichenfolgen mit den Namen der ausgewählten Spalten an die R-Funktion übergeben.

		<Arg id="colset" name="Column set" type="ColumnPicker">	  
		  <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
		  <Description>Column set</Description>
		</Arg>


* *Erforderliche Eigenschaften*: **PortId** - entspricht die ID eines Eingabe-Elements mit dem Typ *DataTable*.
* *Optionale Eigenschaften*:
	* **AllowedTypes** - Filtern die Spaltentypen, von denen der Benutzer wählen kann. Folgende Werte sind gültig: 
		* 	Numeric
		* 	Boolean
		* 	Kategorisch
		* 	String
		* 	Bezeichnung
		* 	Funktion
		* 	Punkte
		* 	Alle

	* **Standard** - gültige Standardauswahl für die Spaltenauswahl umfassen:
		* Keine
		* NumericFeature
		* NumericLabel
		* NumericScore
		* NumericAll
		* BooleanFeature
		* BooleanLabel
		* BooleanScore
		* BooleanAll
		* CategoricalFeature
		* CategoricalLabel
		* CategoricalScore
		* CategoricalAll
		* StringFeature
		* StringLabel
		* StringScore
		* StringAll
		* AllLabel
		* AllFeature
		* AllScore
		* Alle

                            							
**Dropdown**: eine vom Benutzer angegebene aufgezählte (Dropdown)-Liste. Angabe der Dropdown-Elemente werden innerhalb des**Eigenschaften**-Elements mithilfe eines **Elementen**-Element angegeben. Die **ID** für jedes **Element** muss eindeutig sein und eine gültige R-Variable und der Name des Elements entsprechen sowohl dem Text, der Benutzern angezeigt wird, und dem Wert, der an die R-Funktion übergeben wird.

	<Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>	

* *Optionale Eigenschaften*:
	* **Standard** – der Wert für die Standardeigenschaft muss mit dem ID-Wert eines der **Element**- Elemente übereinstimmen.


### Zusätzliche Dateien

Jede Datei in der ZIP-Datei des benutzerdefinierten Moduls ist während der Ausführungszeit verfügbar. Wenn eine Verzeichnisstruktur vorhanden ist, wird diese beibehalten. Dies bedeutet, dass die Dateibereitstellung lokal und bei der Ausführung von Azure Machine Learning gleich funktioniert. Beachten Sie bitte, dass alle Dateien in das Verzeichnis "src" extrahiert werden, sodass alle Pfade das Präfix "src/" haben sollten.

Angenommen Sie möchten alle Zeilen mit NAs und alle doppelten Zeilen in einem DataSet entfernen, bevor Sie es an CustomAddRows ausgeben, und bereits eine R-Funktion geschrieben haben, die dies in einer Datei „RemoveDupNARows.R“ ausführt:

	RemoveDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Sie können die zusätzliche Datei "RemoveDupNARows.R" in der "CustomAddRows"-Funktion bereitstellen:

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
		source("src/RemoveDupNARows.R")
			if (swap) { 
				dataset <- rbind(dataset2, dataset1))
	 		} else { 
	  			dataset <- rbind(dataset1, dataset2)) 
	 		} 
		dataset <- removeDupNARows(dataset)
		return (dataset)
	}

Laden Sie anschließend eine ZIP-Datei mit "CustomAddRows.R", "CustomAddRows.xml" und "RemoveDupNARows.R" als benutzerdefiniertes R-Modul hoch.

## Ausführungsumgebung ##
Die Ausführungsumgebung für das R-Skript verwendet die gleiche Version von R wie das Modul **Execute R Script** und kann die gleichen Standardpakete verwenden. Sie können Ihrem benutzerdefiniertem Modul zusätzliche R-Pakete hinzufügen, indem Sie diese in das benutzerdefinierte Zip-Paket-Modul aufnehmen und sie, wie Sie es in Ihrer eigenen R-Umgebung tun würden, in Ihr R-Skript hochladen.

**Einschränkungen der Ausführungsumgebung** umfassen:

* Nicht beständiges Dateisystem: Dateien, die beim Ausführen des benutzerdefinierten Moduls geschrieben werden, können über mehrere Läufe des gleichen Moduls hinweg nicht beibehalten werden.
* Kein Netzwerkzugriff



 

<!---HONumber=AcomDC_0211_2016-->
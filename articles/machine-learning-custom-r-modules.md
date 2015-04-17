<properties 
	pageTitle="Erstellen von benutzerdefinierten R-Modulen in Azure Machine Learning | Azure" 
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
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Erstellen von benutzerdefinierten R-Modulen in Azure Machine Learning

In diesem Thema wird erläutert, wie ein benutzerdefiniertes R-Modul in Azure Machine Learning erstellt wird. Es wird beschrieben, was ein benutzerdefiniertes R-Modul ist und welche Dateien verwendet werden, um es zu definieren. Es veranschaulicht, wie diese Dateien erstellt und das Modul zur Bereitstellung in einem Machine Learning-Arbeitsbereich registriert werden. Die in der Definition des benutzerdefinierten Moduls verwendeten Elemente und Attribute werden dann ausführlicher beschrieben. Wie zusätzliche Funktionen und Dateien sowie mehrere Ausgaben verwenden, wird ebenfalls erläutert. 

## Was ist ein benutzerdefiniertes R-Modul?
Benutzerdefinierte Module können in den Arbeitsbereich eines Benutzers hochgeladen und als Teil eines Azure Machine Learning-Experiments ausgeführt werden. Ein benutzerdefiniertes R-Modul ist ein benutzerdefiniertes Modul, das eine vom Benutzer erstellte R-Funktion in Machine Learning ausführt. R ist eine Programmiersprache für statistische Verarbeitung und Grafiken, die häufig von Statistikern und Datenanalysten für die Implementierung von Algorithmen verwendet wird. Für die Machine Learning-Sprachunterstützung ist standardmäßig die Sprache R in benutzerdefinierten Modulen festgelegt.

Benutzerdefinierte Module haben insofern erstrangigen Status in Azure Machine Learning, dass sie genau wie andere Module verwendet werden können. Sie können mit anderen Modulen ausgeführt, in veröffentlichten Experimente eingeschlossen oder visuell dargestellt werden. Benutzer haben die Kontrolle über den vom Modul implementierten Algorithmus, die verwendeten Eingangs- und Ausgangsports, die Parameter für die Modellierung und andere Laufzeitmerkmale.


## Dateien in einem benutzerdefinierten R-Modul
Ein benutzerdefiniertes R-Modul wird durch eine ZIP-Datei definiert, die mindestens zwei Dateien enthält:

* Eine Datei, die die vom Modul offengelegte R-Funktion implementiert
* Eine XML-Definitionsdatei, die das benutzerdefinierte Modul beschreibt

Weitere Zusatzdateien können auch in der ZIP-Datei eingeschlossen werden und Funktionen bereitstellen, auf die vom benutzerdefinierten Modul zugegriffen werden kann. Diese Option wird weiter unten erläutert.

## Schnellstart-Beispiel
In diesem Beispiel wird veranschaulicht, wie die erforderlichen Dateien für ein benutzerdefiniertes R-Modul erstellt und in einer ZIP-Datei komprimiert und dann das Modul im Machine Learning-Arbeitsbereich registriert wird.

Beispiel: Das benutzerdefinierte Modul "My Add Rows" ändert die Standardimplementierung des Moduls "Add Rows" zum Verketten von Zeilen (Vorkommen) aus zwei DataSets (DataFrames). Das Modul "Add Rows" hängt die Zeilen des zweiten Eingabe-DataSets mit dem "rbind"-Algorithmus am Ende des ersten Eingabe-DataSets an. Die angepasste `myAddRows`-Funktion akzeptiert zwei DataSets, aber auch einen zusätzlichen booleschen "swap"-Parameter als Eingabe. Wenn der "swap"-Parameter **FALSE** ist, wird dasselbe DataSet wie bei der Standard-Implementierung zurückgegeben. Wenn der "swap"-Parameter jedoch **TRUE** ist, werden stattdessen Zeilen der ersten Eingabe-DataSet an das Ende des zweiten DataSet angehängt. Die Datei, die die vom Modul "My Add Rows" offengelegte R-Funktion "myAddRows" implementiert, enthält den folgenden R-Code.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

Um diese `myAddRows`-Funktion als Azure Machine Learning-Modul offenzulegen, muss eine XML-Datei erstellt werden, um anzugeben, wie das Modul "My Add Rows" aussehen und sich verhalten soll. 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
Beachten Sie, dass es wichtig ist, dass der Inhalt des ID-Elements in der XML-Datei genau mit dem Namen der Funktion übereinstimmt. 

Speichern Sie diese beiden Dateien als *myAddRows.R* und *myAddRows.xml*, und archivieren Sie als ZIP-Datei zusammen in der Datei *myAddRows.zip*.

Um sie im Machine Learning-Arbeitsbereich zu registrieren, besuchen Sie den Arbeitsbereich in Machine Learning Studio, klicken Sie unten auf die Schaltfläche **+NEW**, und wählen Sie **MODULE -> FROM ZIP PACKAGE**, um das neue benutzerdefinierte Modul "My Add Rows" hochzuladen.

![](http://i.imgur.com/RFJhCls.png)

Auf das Modul "My Add Rows" kann jetzt von Machine Learning-Experimenten aus zugegriffen werden.

## Elemente in der XML-Definitionsdatei

### Eingänge und Ausgänge
Die Eingänge und Ausgänge für ein benutzerdefiniertes Modul sind in untergeordneten Elementen des Abschnitts "Ports" der XML-Definitionsdatei angegeben. Die Reihenfolge dieser Eingangs- und Ausgangselemente bestimmt das Layout (UX) für die Benutzer. Das erste untergeordnete Element im "Ports"-Element  der XML-Datei ist der Eingangs-Port ganz links in der Machine Learning-UX. Die unterstützten Datentypen für Eingangs- und Ausgangs-Ports lauten folgendermaßen: 

**DataTable**: Dieser Typ wird als data.frame an die R-Funktion übergeben. Tatsächlich werden alle von Machine Learning unterstützten Typen (z. B. CSV-Dateien oder ARFF-Dateien),  die mit DataTable kompatibel sind, automatisch in ein data.frame konvertiert. 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip**: Benutzerdefinierte Module können ZIP-Dateien als Eingabe akzeptieren. Diese Eingabe wird in das Ausführungsverzeichnis Ihrer Funktion entpackt.

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

Es ist erforderlich, dass die den einzelnen Eingangs- und Ausgangs-Ports zugeordneten ID-Attribute eindeutige Werte aufweisen und dass diese Werte mit den benannten Parametern in der R-Funktion übereinstimmen. Darüber hinaus muss ein Standardwert für die Attribute angegeben werden, deren Eingaben optional sind. Das "IsOptional"-Attribut ist für die Typen "DataTable" und "Zip" optional und standardmäßig "False". Dieser Standardwert gibt an, dass der Eingabetyp nicht optional ist.


### Parameter
Parameter für ein benutzerdefiniertes Modul sind in den untergeordneten Elementen des Abschnitts "Arguments" in der XML-Definitionsdatei angegeben. Wie bei den untergeordneten Elementen im Abschnitt "Ports" definiert die Reihenfolge der Parameter im Abschnitt "Arguments" das Layout in der UX. Der erste aufgeführte Parameter ist der erste Funktionsparameter. Die von Machine Learning für Parameter unterstützten Typen sind unten aufgeführt. Die Standardwerte der optionalen Attribute müssen in der XML-Definition angegeben werden. Dabei handelt es sich um die Werte, die verwendet werden, wenn der Parameterwert bei Verwendung der Funktion nicht angegeben wird. Für jeden Typ wird angegeben, welche Attribute optional sind.


**int** - ein Parameter des Typs ganze Zahl (32 Bit).

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

"IsOptional", "MinValue" und "MaxValue" sind für int optional.

**double** - ein Parameter des Typs "double".

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
"IsOptional", "min", "max", "default" sind für double optional.

**bool** - ein boolescher Parameter, der durch ein Kontrollkästchen in der UX dargestellt wird.

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

"default" ist für "bool" optional.

**string**: eine Standardzeichenfolge.

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

"default" und "IsOptional" sind für "string" optional.

**ColumnPickerFor**: ein Spaltenauswahl-Parameter. Dieser Typ wird in der UX als Spaltenauswahl gerendert. Die ID der DataTable, aus der Spalten ausgewählt werden, sollte den Tabellenteil im Wert für das Typ-Attribut ersetzen. Die Variable wird Ihrer Funktion als Liste von strings übergeben. 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

Wenn z. B. eine DataTable mit der ID "dataset1" vorhanden ist, wäre der Typ 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<Dropdownlistentyp-ID>**: eine Aufzählungsliste (Dropdownliste). Der ausgewählte Wert wird als string an die R-Funktion übergeben. Für diesen Typ müssen zuerst im Abschnitt "Arguments" gültige aufgelistete Werte definiert werden.

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

Wie bei Eingaben und Ausgaben ist es wichtig, dass jedem Parameter eindeutige ID-Werte zugeordnet sind. Darüber hinaus müssen die ID-Werte den benannten Parametern in der R-Funktion entsprechen. In unserem Schnellstart-Beispiel war der zugeordnete ID-Parameter *swap*.

### Definition des "Language"-Elements
Das "Language"-Element in der XML-Definitionsdatei definiert bestimmte Funktionen der Sprache. Für R Module ist dies in der Regel 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

Hiermit werden die jeweilige Sprache, die Datei, in der die Funktion definiert ist, und der Einstiegspunkt in dieser Definition angegeben. Das "AddDisplayOutputPort"-Attribut ist optional für das "Language"-Element. Wenn Sie einen Ausgangs-Port hinzufügen möchten, der für Visualisierungen in Form von grafischen Darstellungen/Diagrammen verwendet werden kann, wählen Sie wie beim Modul "Execute R Script" (Link wird nachgereicht) *true* für das "AddDisplayOutputPort"-Tag, dann wird ein zusätzlicher Ausgangsanschluss angezeigt. 

### Zusätzliche Funktionalität

Es gibt mehrere Attribute, die in diesem Beispiel nicht verfügbar gemacht werden, jedoch von Autoren benutzerdefinierter Module verwendet werden können. Beispielsweise kann ein Modul deterministisches oder nicht deterministisches Verhalten aufweisen. Ein deterministisches Modul wird kein zweites Mal mit denselben Eingabedaten und derselben Parameterkonfiguration ausgeführt. Stattdessen werden zwischengespeicherte Ergebnisse verwendet und an alle nachgeschalteten Module weitergegeben. Ein Beispiel eines deterministischen Moduls in Azure Machine Learning ist das Modul "Add Rows". Ein Beispiel eines nicht deterministischen Moduls ist "Reader". Damit Ihr benutzerdefiniertes Modul ein nicht deterministisches Verhalten aufweist, ändern Sie die Standardeinstellung, indem Sie Ihrer Definition das folgende Attribut hinzufügen:

	<IsDeterministic>false</IsDeterministic>

### Zusätzliche Dateien

Jede Datei in der ZIP-Datei des benutzerdefinierten Moduls ist während der Ausführungszeit verfügbar. Wenn eine Verzeichnisstruktur vorhanden ist, wird diese beibehalten. Dies bedeutet, dass die Dateibereitstellung lokal und bei der Ausführung von Azure Machine Learning gleich funktioniert. 

Angenommen, Sie möchten Zeilen mit NAs und doppelte Zeilen in einem DataSet entfernen, bevor Sie es an MyAddRows weitergeben, und Sie haben bereits in der Datei "removeDupNARows.R" eine R-Funktion geschrieben, die diese Funktion ausführt:

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Sie können die zusätzliche Datei "removeDupNARows.R" in der "myAddRows"-Funktion bereitstellen:

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

Anschließend laden Sie eine ZIP-Datei mit "myAddRows.R", "myAddRows.xml" und "removeDupNARows.R" als benutzerdefiniertes R-Modul hoch.

### Mehrere Ausgaben

Um mehr als ein Objekt eines unterstützten Datentyps auszugeben, müssen die entsprechende Ausgangs-Ports in der XML-Definitionsdatei angegeben und die Objekte als Liste zurückgegeben werden. Die Ausgangsobjekte werden den Ausgangs-Ports von links nach rechts zugewiesen und entsprechen der Reihenfolge, in der die Objekte in der zurückgegebenen Liste platziert werden.
 
Wenn Sie beispielsweise "Dataset", "Dataset1" und "Dataset2" von links nach rechts an die Ausgangs-Ports dataset, dataset1 und dataset2 ausgeben möchten, definieren Sie die Ausgangs-Ports in der Datei "myAddRows.xml" wie folgt:

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

Geben Sie dann die Liste der Objekte in einer Liste in der richtigen Reihenfolge in "myAddRows.R" zurück:

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## Ausführungsumgebung

TBD

## Nächste Schritte

TBD

<!--HONumber=49-->
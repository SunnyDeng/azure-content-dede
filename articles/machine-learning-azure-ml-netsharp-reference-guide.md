<properties title="Guide to the Net# Neural Networks Specification Language for Azure ML" pageTitle="Erläuterungen zur Net#-Spezifikationssprache für neuronale Netzwerke für Azure ML " description="Syntax für die Net#-Spezifikationssprache für neuronale Netzwerke und Beispiele zum Erstellen eines benutzerdefinierten neuralen Networkmodells in Microsoft Azure ML mithilfe von Net# " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jeannt" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/08/2014" ms.author="jeannt" />



# Erläuterungen zur Net#-Spezifikationssprache für neuronale Netzwerke


Net#(c) ist eine von Microsoft entwickelte Sprache zum Definieren von neuronalen Netzwerkarchitekturen für neuronale Netzwerkmodule in Microsoft Azure Machine Learning. In diesem Artikel lernen Sie Folgendes:  

-	Grundlegende Konzepte eines neuronalen Netzwerks
-	Anforderungen neuronaler Netzwerke und Definieren der primären Komponenten
-	Syntax und Schlüsselwörter der Net#-Spezifikationssprache
-	Beispiele von mit Net erstellten benutzerdefinierten neuronalen Netzwerken#  

###Neuronale Netzwerke - Grundlagen
Die Struktur eines neuronalen Netzwerks besteht aus ***Knoten***, die in ***Schichten*** organisiert sind, sowie gewichteten ***Verbindungen*** (bzw. ***Flanken***) zwischen Knoten. Die Verbindungen sind gerichtet, jede Verbindung hat einen ***Quellknoten*** und einen ***Zielknoten***.  

Jede ***trainierbare Schicht*** (nämlich entweder eine verdeckte Schicht oder eine Ausgabeschicht) hat mindestens ein ***Verbindungsbündel***. Ein Verbindungsbündel besteht aus einer Quellschicht und einer Spezifikation der Verbindungen von dieser Quellschicht. Alle Verbindungen in einem bestimmten Bündel nutzen dieselbe ***Quellschicht*** und dieselbe ***Zielschicht***. In Net# gehört ein Verbindungsbündel zur Zielschicht des Bündels.  
 
Net# unterstützt verschiedene Arten von Verbindungsbündeln, sodass Sie anpassen können, wie Eingaben verdeckten Schichten und Ausgaben zugeordnet werden.   

Das Standardbündel ist ein **vollständiges Bündel**, bei dem jeder Knoten in der Quellschicht mit jedem Knoten in der Zielschicht verbunden ist.  

Darüber hinaus unterstützt Net# die folgenden vier Arten erweiterter Verbindungsbündel:  

-	**Gefilterte Bündel**. Der Benutzer kann unter Verwendung der Positionen des Quell- und des Zielschichtknotens ein Prädikat definieren. Wenn das Prädikat "true" (wahr) ist, sind die Knoten verbunden.
-	**Konvolutionsbündel**. Der Benutzer kann kleine Knotenumgebungen in der Quellschicht definieren. Jeder Knoten in der Zielschicht ist mit einer Knotenumgebung in der Quellschicht verbunden.
-	**Poolingbündel** und **Antwortnormalisierungsbündel**. Diese ähneln insofern den Konvolutionsbündeln, als der Benutzer kleine Knotenumgebungen in der Quellschicht definiert. Der Unterschied besteht darin, dass die Gewichtungen der Flanken in diesen Bündeln nicht trainierbar sind; stattdessen wird eine vordefinierte Funktion auf die Quellknotenwerte angewendet, um den Zielknotenwert zu ermitteln.  

Die Verwendung von Net# zum Definieren der Struktur eines neuronalen Netzwerks macht es möglich, komplexe Strukturen wird Deep Neural Networks (DNNs) oder Konvolutionen beliebiger Dimensionen zu definieren, die bekanntermaßen das Lernen aus Daten wie Bildern, Audio und Video verbessern.  

###Unterstützte Anpassungen
Die Architektur der neuronalen Netzwerkmodelle, die Sie in Microsoft Azure Machine Learning erstellen, kann mithilfe von Net# umfassend angepasst werden. Sie können:  

-	verdeckte Schichten erstellen und die Anzahl der Knoten in jeder Schicht steuern;
-	angeben, wie Schichten miteinander verbunden werden sollen;
-	spezielle Konnektivitätsstrukturen wie Konvolutionen und Bündel mit gemeinsamer Gewichtung definieren;
-	verschiedene Aktivierungsfunktionen angeben.  

Einzelheiten zur Syntax der Spezifikationssprache finden Sie unter "Strukturspezifikation".  
 
Beispiele für die Definition neuronaler Netzwerke für einige gängige Aufgaben aus dem Bereich maschinelles Lernen (sowohl einfache als auch komplexe) finden Sie unter "Beispiele".  

###Allgemeine Anforderungen
-	Es müssen genau eine Ausgabeschicht, mindestens eine Eingabeschicht und null oder mehr verdeckte Schichten vorhanden sein. 
-	Jede Schicht verfügt über eine feste Anzahl von Knoten, die konzeptionell in einem rechteckigen Array beliebiger Dimensionen angeordnet sind. 
-	Eingabeschichten sind keine trainierten Parameter zugeordnet; sie stellen den Eintrittspunkt von Instanzdaten in das Netzwerk dar. 
-	Trainierbaren Schichten (nämlich verdeckten Schichten und Ausgabeschichten) sind trainierte Parameter zugeordnete, die als Gewichtungen und Biase bezeichnet werden. 
-	Die Quell- und Zielknoten müssen sich in verschiedenen Schichten befinden. 
-	Verbindungen müssen azyklisch sein, anders ausgedrückt, sie dürfen keine Kette von Verbindungen bilden, die zurück zum ursprünglichen Quellknoten führen.
-	Die Ausgabeschicht darf keine Quellschicht eines Verbindungsbündels sein.  

##Strukturspezifikation
Die Strukturspezifikation eines neuronalen Netzwerks besteht aus drei Abschnitten: der **Konstantendeklaration**, der **Schichtdeklaration** und der **Verbindungsdeklaration** sowie einer optionalen **Deklaration der gemeinsamen Nutzung**. Die Abschnitte können in beliebiger Reihenfolge angegeben werden.  

###Konstantendeklaration 
Eine Konstantendeklaration ist optional und bietet die Möglichkeit, Werte zu definieren, die an anderer Stelle in der Definition des neuronalen Netzwerks verwendet werden. Die Deklarationsanweisung besteht aus einem Bezeichner (ID), gefolgt von einem Gleichheitszeichen und einem Wertausdruck.   

Mit der folgenden Anweisung wird beispielsweise eine Konstante **x** definiert:  


    Const X = 28;  

Um zwei oder mehr Konstanten gleichzeitig zu definieren, schließen Sie die Bezeichnernamen und -werte in geschweifte Klammern ein, und verwenden Sie Semikolons als Trennzeichen, wie in diesem Beispiel gezeigt:  

    Const { X = 28; Y = 4; }  

Bei der rechten Seite eines Zuweisungsausdrucks kann es sich um eine ganze Zahl, eine reelle Zahl, einen booleschen Wert (true/false) oder einen mathematischen Ausdruck handeln. Beispiel:  
 

	Const { X = 17 * 2; Y = true; }  

###Schichtdeklaration
Die Schichtdeklaration ist erforderlich und definiert die Größe und die Quelle der Schicht, einschließlich ihrer Verbindungsbündel und Attribute. Die Deklarationsanweisung beginnt mit den Namen der Schicht (input, hidden oder output), gefolgt von den Dimensionen der Schicht (ein Tupel positiver ganzer Zahlen). Beispiel:  

	input Data[784];
	hidden Hidden[5,20] from Data all;
	output Result[2] from Hidden all;  

-	Das Produkt der Dimensionen ist die Anzahl von Knoten in der Schicht. In diesem Beispiel liegen zwei Dimensionen [5,20] vor, d.h., die Schicht hat  100 Knoten.
-	Die Schichten können in beliebiger Reihenfolge deklariert werden, mit einer Ausnahme: Wenn mehrere Eingabeschichten definiert werden, muss die Reihenfolge ihrer Deklaration mit der Reihenfolge von Features in den Eingabedaten übereinstimmen.  

<!-- REMOVED THIS CONTENT UNTIL THIS FEATURE IS SUPPORTED IN THE PRODUCT
To specify that the number of nodes in a layer be determined automatically, use the **auto** keyword. The **auto** keyword has different effects, depending on the layer:  

-	In an input layer declaration, the number of nodes is the number of features in the input data.
-	In a hidden layer declaration, the number of nodes is the number specified by the parameter value for **Number of hidden nodes**. 
-	In an output layer declaration, the number of nodes is 2 for two-class classification, 1 for regression, and equal to the number of output nodes for multi-class classification.   

For example, the following network definition allows the size of all layers to be automatically determined:  

	input Data auto;
	hidden Hidden auto from Data all;
	output Result auto from Hidden all;  
-->

Eine Schichtdeklaration für eine trainierbare Schicht (nämlich verdeckten Schichten oder Ausgabeschichten) kann optional die Ausgabefunktion (auch als Aktivierungsfunktion bezeichnet) enthalten, die standardmäßig **sigmoid** lautet. Die folgenden Ausgabefunktionen werden unterstützt:  

-	sigmoid
-	linear
-	softmax
-	rlinear
-	square
-	sqrt
-	srlinear
-	abs
-	tanh 
-	brlinear  

Die folgende Deklaration verwendet beispielsweise die Funktion **softmax**:  

	output Result [100] softmax from Hidden all;  

###Verbindungsdeklaration
Unmittelbar nachdem Sie die trainierbare Schicht deklariert haben, müssen Sie Verbindungen zwischen den definierten Verbindungen deklarieren. Die Deklaration des Verbindungsbündels beginnt mit dem Schlüsselwort **from**, gefolgt vom Namen der Quellschicht des Bündels und der Art des zu erstellenden Verbindungsbündels.   

Derzeit werden fünf Arten von Verbindungsbündeln unterstützt:  

-	**Vollständige Bündel**; diese werden mit dem Schlüsselwort **all** angegeben.
-	**Gefilterte** Bündel; diese werden mit dem Schlüsselwort **where** gefolgt von einem Prädikatausdruck angegeben.
-	**Konvolutionsbündel**; diese werden mit dem Schlüsselwort **convolve** gefolgt von den Konvolutionsattributen angegeben.
-	**Poolingbündel**; diese werden mit dem Schlüsselwort **max pool** oder **mean pool** angegeben.
-	**Antwortnormalisierungsbündel**; diese werden mit dem Schlüsselwort **response norm** angegeben.  	

####Vollständige Bündel  

Ein vollständiges Verbindungsbündel enthält eine Verbindung von jedem Knoten in der Quellschicht zu jedem Knoten in der Zielschicht. Dies ist der standardmäßige Netzwerkverbindungstyp.  

####Gefilterte Bündel
Die Spezifikation eines gefilterten Verbindungsbündels enthält ein Prädikat, das syntaktisch ganz ähnlich wie ein C#-Lambdaausdruck ausgedrückt wird. Im folgenden Beispiel werden zwei gefilterte Bündel definiert:  

	input Pixels [10, 20];
	hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-	Im Prädikat für "ByRow" ist **s** ein Parameter, der einen Index in das rechteckige Knotenarray der Eingabeschicht "Pixels" darstellt, und **d** ist ein Parameter, der einen Index in das Knotenarray der verdeckten Schicht "ByRow" darstellt. Der Typ von **s** und **d** ist jeweils ein Tupel ganzer Zahlen der Länge zwei. Konzeptionell erstreckt sich **s** über alle Paare von ganzen Zahlen mit 0 <= s[0] < 10 und 0 <= s[1] < 20, während **d** sich über alle Paare von ganzen Zahlen mit 0 <= d[0] < 10 und 0 <= d[1] < 12 erstreckt. .
-	Auf der rechten Seite des Prädikatausdrucks befindet sich eine Bedingung. In diesem Beispiel gibt es für jeden Wert von **s** und **d**, bei dem die Bedingung "true" ist, eine Flanke vom Quellschichtknoten zum Zielschichtknoten. Der Filterausdruck gibt somit an, dass das Bündel immer dann, wenn s[0] gleich d[0] ist, eine Verbindung von dem durch **s** definierten Knoten zu dem durch **d** definierten Knoten enthält.  

Optional können Sie einen Satz Gewichtungen für ein gefiltertes Bündel angeben. Der Wert für das Attribut **Weights** muss ein Tupel von Gleitkommawerten sein, dessen Länge der Anzahl der vom Bündel definierten Verbindungen entspricht. Standardmäßig werden Gewichtungen nach dem Zufallsprinzip generiert.  

Gewichtungswerte werden nach dem Zielknotenindex gruppiert. Das heißt, wenn der erste Zielknoten mit K Quellknoten verbunden ist, sind die ersten K Elemente des Tupels **Weights** die Gewichtungen für den ersten Zielknoten in der Reihenfolge des Quellindex. Gleiches gilt für die restlichen Zielknoten.  

####Konvolutionsbündel
Wenn die Trainingsdaten eine homogene Struktur aufweisen, werden Konvolutionsverbindungen üblicherweise eingesetzt, um übergeordnete Merkmale der Daten zu ermitteln. So kann beispielsweise in Bild-, Audio- oder Videodaten die räumliche oder zeitliche Dimensionalität ziemlich einheitlich sein.  

Konvolutionsbündel verwenden rechteckige **Kernel**, die durch die Dimensionen geschoben werden. Im Wesentlichen definiert jeder Kernel einen Satz von Gewichtungen, die in lokalen Umgebungen angewendet werden; diese werden als **Kernelanwendungen** bezeichnet. Jede Kernelanwendung entspricht einem Knoten in der Quellschicht, der als **zentraler Knoten** bezeichnet wird. Die Gewichtungen eines Kernels werden von vielen Verbindungen gemeinsam verwendet. In einem Konvolutionsbündel ist jeder Kernel rechteckig, und alle Kernelanwendungen sind gleich groß.  

Konvolutionsbündel unterstützen die folgenden Attribute: **InputShape** definiert die Dimensionalität der Quellschicht für die Zwecke dieses Konvolutionsbündels. Der Wert muss ein Tupel positiver ganzer Zahlen sein. Das Produkt der ganzen Zahlen muss gleich der Anzahl der Knoten in der Quellschicht sein, eine weitergehende Übereinstimmung mit der für die Quellschicht deklarierten Dimensionalität ist jedoch nicht erforderlich. Die Länge dieses Tupels stellt den Wert für die **Arität** des Konvolutionsbündels dar. (Arität bezieht sich in der Regel auf die Anzahl der Argumente oder Operanden, die eine Funktion enthalten kann.)  

Die Form und Positionen der Kernel können mit den Attributen **KernelShape**, **Stride**, **Padding**, **LowerPad** und **UpperPad** definiert werden:   

-	**KernelShape**: (erforderlich) Definiert die Dimensionalität jedes Kernels für das Konvolutionsbündel. Der Wert muss ein Tupel positiver ganzer Zahlen sein, dessen Länge der Arität des Bündels entspricht. Jede Komponente dieses Tupels darf nicht größer sein als die entsprechende Komponente von **InputShape**. .
-	**Stride**: (optional) Definiert die gleitenden Schrittgrößen der Konvolution (eine Schrittgröße für jede Dimension), d. h. den Abstand zwischen den zentralen Knoten. Der Wert muss ein Tupel positiver ganzer Zahlen sein, dessen Länge der Arität des Bündels entspricht. Jede Komponente dieses Tupels darf nicht größer sein als die entsprechende Komponente von **KernelShape**. Der Standardwert ist ein Tupel, in dem alle Komponenten gleich eins sind. 
-	**Padding**: (optional) Legt fest, ob die Eingabe unter Verwendung des Standardauffüllungsschemas aufgefüllt werden soll. Der Wert kann entweder ein einzelner boolescher Wert oder ein Tupel boolescher Werte sein, dessen Länge der Arität des Bündels entspricht. Ein einzelner boolescher Wert wird zu einem Tupel der richtigen Länge erweitert, in dem alle Komponenten gleich dem angegebenen Wert sind. Wenn der Wert einer Dimension "true" ist, wird die Quelle in dieser Dimension logisch mit Nullwertzellen aufgefüllt, um zusätzliche Kernelanwendungen zu unterstützen, sodass die zentralen Knoten des ersten und letzten Kernels in dieser Dimension den ersten und letzten Knoten in dieser Dimension in der Zielschicht darstellen. Die Anzahl von "Dummy"-Knoten in jeder Dimension wird somit automatisch bestimmt, um genau (InputShape[d] - 1) / Stride[d] + 1 Kernel in die aufgefüllte Quellschicht aufzunehmen. Wenn der Wert einer Dimension "false" ist, werden die Kernel so definiert, dass die Anzahl der ausgelassenen Knoten auf jeder Seite gleich ist (bis zu einer Differenz von 1). Der Standardwert dieses Attributs ist ein Tupel, in dem Komponenten "false" sind.
-	**UpperPad** und **LowerPad**: (optional) Ermöglichen die Steuerung der zu verwendenden Auffüllungsmenge. Diese Attribute können nur dann definiert werden, wenn **Padding** ***nicht*** definiert wurde. Die Werte sollten Tupel ganzer Zahlen sein, deren Längen der Arität des Bündels entsprechen. Wenn diese Attribute angegeben werden, werden am unteren und oberen Ende jeder Dimension der Eingabeschicht "Dummy"-Knoten hinzugefügt. Die Anzahl der Knoten, die dem unteren und oberen Ende der Dimension hinzugefügt werden, wird durch **LowerPad**[i] und **UpperPad**[i] festgelegt. Um sicherzustellen, dass Kernel nur "realen" Knoten und nicht "Dummy"-Knoten entsprechen, müssen die folgenden Bedingungen erfüllt sein:
	-	Jede Komponente des **LowerPad** muss kleiner als KernelShape[d]/2 sein. 
	-	Jede Komponente des **UpperPad** muss kleiner als KernelShape[d]/2 sein. 
	-	Der Standardwert dieser Attribute ist ein Tupel, in dem alle Komponenten gleich 0 sind. 
-	**Sharing**: (optional) Definiert die gemeinsame Nutzung von Gewichtungen für jede Dimension der Konvolution. Der Wert kann entweder ein einzelner boolescher Wert oder ein Tupel boolescher Werte sein, dessen Länge der Arität des Bündels entspricht. Ein einzelner boolescher Wert wird zu einem Tupel der richtigen Länge erweitert, in dem alle Komponenten gleich dem angegebenen Wert sind. Der Standardwert ist ein Tupel, in dem alle Werte "true" sind. 
-	**MapCount**: (optional) Definiert die Anzahl der Featurezuordnungen für das Konvolutionsbündel. Der Wert kann entweder eine einzelne positive ganze Zahl oder ein Tupel positiver ganzer Zahlen sein, dessen Länge der Arität des Bündels entspricht. Ein einzelner ganzzahliger Wert wird zu einem Tupel der richtigen Länge erweitert, in dem die ersten Komponenten gleich dem angegebenen Wert und alle übrigen Komponenten gleich eins sind. Der Standardwert ist eins. Die Gesamtanzahl an Featurezuordnungen ist das Produkt der Komponenten des Tupels. Die Faktorisierung dieser Gesamtanzahl über alle Komponenten legt fest, wie die Featurezuordnungswerte in den Zielknoten gruppiert werden. 
-	**Weights**: (optional) Definiert die anfänglichen Gewichtungen für das Bündel. Der Wert muss ein Tupel von Gleitkommawerten sein, dessen Länge der Anzahl der Kernel mal der Anzahl der Gewichtungen pro Kernel entspricht, wie unten definiert. Die Standardgewichtungen werden nach dem Zufallsprinzip generiert.  

Weitere Informationen zu Konvolutionsnetzwerken und ihren Anwendungsmöglichkeiten finden Sie in den folgenden Artikeln (in englischer Sprache):  

-	[http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html )
-	[http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-	[http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

####Poolingbündel
Ein **Poolingbündel** wendet Geometrie ähnlich wie Konvolutionskonnektivität an, verwendet jedoch vordefinierte Funktionen für Quellknotenwerte, um die Zielknotenwerte abzuleiten. Daher haben Poolingbündel keinen trainierbaren Zustand (Gewichtungen oder Biase). Poolingbündel unterstützen alle Konvolutionsattribute mit Ausnahme von **Sharing**, **MapCount** und **Weights**.  

Normalerweise überlappen sich die Kernel, die von nebeneinander liegenden Poolingeinheiten zusammengefasst werden, nicht. Wenn Stride[d] in jeder Dimension gleich KernelShape[d] ist, entsteht die herkömmliche lokale Poolingschicht, die üblicherweise in neuronalen Konvolutionsnetzwerken verwendet wird. Jeder Zielknoten berechnet entweder das Maximum oder den Mittelwert der Aktivitäten seines Kernels in der Quellschicht.  

Im folgenden Beispiel wird ein Poolingbündel veranschaulicht:  

	hidden P1 [5, 12, 12]
	  from C1 max pool {
	    InputShape  = [ 5, 24, 24];
	    KernelShape = [ 1,  2,  2];
	    Stride      = [ 1,  2,  2];
	  }  

-	Die Arität des Bündels ist 3 (die Länge der Tupel **InputShape**, **KernelShape** und **Stride**). 
-	Die Anzahl der Knoten in der Quellschicht beträgt 5 * 24 * 24 = 2880. 
-	Die ist eine herkömmliche lokale Poolingschicht, da **KernelShape** und **Stride** gleich sind. 
-	Die Anzahl der Knoten in der Zielschicht beträgt 5 * 12 * 12 = 1440.  
	
Weitere Informationen zu Poolingschichten finden Sie in den folgenden Artikeln (in englischer Sprache):  

-	[http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Abschnitt 3.4)
-	[http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-	[http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
	
####Antwortnormalisierungsbündel
Die **Antwortnormalisierung** ist ein lokales Normalisierungsschema, das erstmals von Geoffrey Hinton et al in einem Dokument namens "ImageNet Classiﬁcation with Deep Convolutional Neural Networks" (siehe Abschnitt 3.3) verwendet wurde. Die Antwortnormalisierung unterstützt die Generalisierung in neuronalen Netzwerken. Wenn ein Neuron auf einem sehr hohen Aktivierungsniveau auslöst, unterdrückt eine lokale Antwortnormalisierungsschicht das Aktivierungsniveau der umgebenden Neuronen. Hierzu werden die drei Parameter ***α***, ***β*** und ***k*** sowie eine Konvolutionsstruktur (bzw. eine Umgebungsform) verwendet. Jedes Neuron in der Zielschicht ***y*** entspricht einem Neuron ***x*** in der Quellschicht. Das Aktivierungsniveau ***y*** wird durch die folgende Formel angegeben, wobei ***f*** das Aktivierungsniveau eines Neurons und ***Nx*** der Kernel bzw. der Satz mit den Neuronen in der Umgebung von ***x*** ist, wie durch die Konvolutionsstruktur definiert:  

![][1]  

Antwortnormalisierungsbündel unterstützen alle Konvolutionsattribute mit Ausnahme von **Sharing**, **MapCount** und **Weights**.  
 
-	Wenn der Kernel Neuronen in derselben Zuordnung wie ***x*** enthält, wird das Normalisierungsschema als **zuordnungsinterne Normalisierung** bezeichnet. Um diese Art von Normalisierung zu definieren, muss die erste Koordinate in **InputShape** den Wert 1 haben.
-	Wenn der Kernel Neuronen an derselben räumlichen Position wie ***x*** enthält, die Neuronen sich jedoch in anderen Zuordnungen befinden, wird das Normalisierungsschema als **zuordnungsübergreifende Normalisierung** bezeichnet. Diese Art der Antwortnormalisierung implementiert eine Form von lateraler Hemmung, inspiriert durch den in echten Neuronen gefundenen Typ; dies sorgt für einen Wettbewerb um hohe Aktivierungsniveaus zwischen den in unterschiedlichen Zuordnungen berechneten Neuronenausgaben. Um eine zuordnungsübergreifende Normalisierung zu definieren, muss die erste Koordinate eine ganze Zahl größer als eins und nicht größer als die Anzahl der Zuordnungen sein; der Rest der Koordinaten muss den Wert 1 haben.  

Da Antwortnormalisierungsbündel zur Bestimmung des Zielknotenwerts eine vordefinierte Funktion auf Quellknotenwerte anwenden, haben sie keinen trainierbaren Zustand (Gewichtungen oder Biase).   

**Alert:** Die Knoten in der Zielschicht entsprechen Neuronen, die die zentralen Knoten der Kernel darstellen. Beispiel: Wenn KernelShape[d] ungerade ist, entspricht KernelShape[d]/2 dem zentralen Kernelknoten. Wenn KernelShape[d] gerade ist, befindet sich der zentrale Knoten an KernelShape[d]/2 - 1. Daher gilt: Wenn **Padding**[d] "false" ist, haben der erste und der letzte KernelShape[d]/2-Knoten keine entsprechenden Knoten in der Zielschicht. Um diese Situation zu vermeiden, definieren Sie **Padding** als [true, true, ..., true].  

Zusätzlich zu den vier oben beschriebenen Attributen unterstützen Antwortnormalisierungsbündel auch die folgenden Attribute:  

-	**Alpha**: (erforderlich) Gibt einen Gleitkommawert an, der ***α*** in der vorstehenden Formel entspricht. 
-	**Beta**: (erforderlich) Gibt einen Gleitkommawert an, der ***β*** in der vorstehenden Formel entspricht. 
-	**Offset**: (erforderlich) Gibt einen Gleitkommawert an, der ***k*** in der vorstehenden Formel entspricht. Der Standardwert lautet 1.  

Das folgende Beispiel definiert ein Antwortnormalisierungsbündel mit diesen Attributen:  

	hidden RN1 [5, 10, 10]
	  from P1 response norm {
	    InputShape  = [ 5, 12, 12];
	    KernelShape = [ 1,  3,  3];
	    Alpha = 0.001;
	    Beta = 0.75;
	  }  

-	Die Quellschicht enthält fünf Zuordnungen, jede mit einer Dimension von 12x12, also insgesamt 1440 Knoten. 
-	Der Wert von **KernelShape** gibt an, das es sich hierbei um eine Schicht mit zuordnungsinterner Normalisierung handelt, bei der die Umgebung ein 3x3-Rechteck ist. 
-	Der Standardwert von **Padding** ist "false", daher hat die Zielschicht nur zehn Knoten in jeder Dimension. Um einen Knoten in die Zielschicht entsprechend jedem Knoten in der Zielschicht einzuschließen, fügen Sie Padding = [true, true, true] hinzu, und ändern Sie die Größe von RN1 in [5, 12, 12].  

###Deklaration der gemeinsamen Nutzung 
In Net# wird optional die Definition mehrerer Bündel mit gemeinsamen Gewichtungen unterstützt. Die Gewichtungen zweier beliebiger Bündel können gemeinsam genutzt werden, solange die Struktur der Bündel identisch ist. Die folgende Notation beschreibt die Syntax für die Definition von Bündeln mit gemeinsamen Gewichtungen:  

	share-declaration:
	    share    {    layer-list    }
	    share    {    bundle-list    }
	   share    {    bias-list    }
	
	layer-list:
	    layer-name    ,    layer-name
	    layer-list    ,    layer-name
	
	bundle-list:
	   bundle-spec    ,    bundle-spec
	    bundle-list    ,    bundle-spec
	
	bundle-spec:
	   layer-name    =>     layer-name
	
	bias-list:
	    bias-spec    ,    bias-spec
	    bias-list    ,    bias-spec
	
	bias-spec:
	    1    =>    layer-name
	
	layer-name:
	    identifier  

Die folgende Deklaration der gemeinsamen Nutzung beispielsweise nennt Schichtnamen und gibt an, dass sowohl die Gewichtungen als auch die Biase gemeinsam genutzt werden sollen:  

	Const {
	  InputSize = 37;
	  HiddenSize = 50;
	}
	input {
	  Data1 [InputSize];
	  Data2 [InputSize];
	}
	hidden {
	  H1 [HiddenSize] from Data1 all;
	  H2 [HiddenSize] from Data2 all;
	}
	output Result [2] {
	  from H1 all;
	  from H2 all;
	}
	share { H1, H2 } // share both weights and biases  

-	Die Eingabefeatures werden in zwei gleich große Eingabeschichten partitioniert. 
-	Anschließend berechnen die verdeckten Schichten Features höherer Ebene in den zwei Eingabeschichten. 
-	Die Deklaration der gemeinsamen Nutzung gibt an, dass H1 und H2 auf die gleiche Weise aus den jeweiligen Eingaben berechnet werden müssen.  
 
Alternativ kann dies wie folgt mit zwei separaten Deklarationen der gemeinsamen Nutzung angegeben werden:  

	share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

	share { 1 => H1, 1 => H2 } // share biases  

Die Kurzform kann nur verwendet werden, wenn die Schichten ein einziges Bündel enthalten. Generell gilt, dass die gemeinsame Nutzung nur möglich ist, wenn die relevante Struktur identisch ist, d. h. die gleiche Größe, die gleiche Konvolutionsgeometrie usw. aufweist.  

##Beispiele für die Net#-Verwendung
In diesem Abschnitt finden Sie einige Beispiele dafür, wie Sie mit Net# verdeckte Schichten hinzufügen, die Interaktion der verdeckten Schichten mit anderen Schichten definieren und Konvolutionsnetzwerke erstellen können.   

###Definieren eines einfachen benutzerdefinierten neuronalen Netzwerks: "Hello World"-Beispiel
Dieses einfache Beispiel zeigt, wie ein neuronales Netzwerkmodell mit einer einzigen verdeckten Schicht erstellt wird.  

	input Data [100];
	hidden H [200] from Data all;
	output Out [10] sigmoid from H all;  

Das Beispiel veranschaulicht die Verwendung einiger grundlegender Befehle und ihre Reihenfolge:  

-	In der ersten Zeile wird die Eingabeschicht namens "Data" definiert. Sie hat 100 Knoten, von denen jeder ein Feature in den Eingabebeispielen darstellt.
-	In der zweiten Zeile wird die verdeckte Schicht erstellt. Die verdeckte Schicht hat 200 Knoten und erhält den Namen "H". Diese Schicht ist vollständig mit der Eingabeschicht verbunden.
-	In der dritten Zeile wird die Ausgabeschicht definiert. Sie erhält den Namen "O" und enthält 10 Ausgabeknoten. Neuronale Klassifizierungsnetzwerke haben nur einen Ausgabeknoten pro Klasse. Das Schlüsselwort **sigmoid** gibt die auf die Ausgabeschicht angewendete Ausgabefunktion an.   

###Definieren mehrerer verdeckter Schichten: Computervisionsbeispiel
Das folgende Beispiel zeigt die Definition eines etwas komplexeren neuronalen Netzwerks mit mehreren benutzerdefinierten verdeckten Schichten.  

	// Define the input layers 
	input Pixels [10, 20];
	input MetaData [7];
	
	// Define the first two hidden layers, using data only from the Pixels input
	hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
	
	// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
	hidden Gather [100] 
	{
	  from ByRow all;
	  from ByCol all;
	}
	
	// Define the output layer and its sources
	output Result [10]  
	{
	  from Gather all;
	  from MetaData all;
	}  

Dieses Beispiel veranschaulicht mehrere Features der Spezifikationssprache für neuronale Netzwerke:  

-	Die Struktur hat zwei Eingabeschichten: "Pixels" und "MetaData".
-	Die Schicht "Pixels" ist eine Quellschicht für zwei Verbindungsbündel mit den Zielschichten "ByRow" und "ByCol".
-	Die Schichten "Gather" und "Result" sind Zielschichten in mehreren Verbindungsbündeln.
-	Die Ausgabeschicht "Result" ist eine Zielschicht in zwei Verbindungsbündeln; eins hat die verdeckte Schicht der zweiten Ebene (Gather) als Zielschicht, das andere hat die Eingabeschicht "MetaData" als Zielschicht.
-	Die verdeckten Schichten "ByRow" und "ByCo" geben gefilterte Konnektivität mithilfe von Prädikatausdrücken an. Genauer gesagt, der Knoten in "ByRow" an Position [x, y] ist mit den Knoten in "Pixels" verbunden, deren erste Indexkoordinate gleich der ersten Koordinate des Knotens (x) ist. Entsprechend ist der Knoten in "ByCol" an Position [x, y] mit den Knoten in "Pixels" verbunden, deren zweite Indexkoordinate innerhalb einer der zweiten Koordinaten des Knotens (y) liegt.  

###Definieren eines Konvolutionsnetzwerks für Multiklassenklassifikation: Ziffernerkennungsbeispiel
Die Definition des folgenden Netzwerks zur Erkennung von Ziffern veranschaulicht einige fortgeschrittene Techniken für die Anpassung eines neuronalen Netzwerks.  

	input Image [29, 29];
	hidden Conv1 [5, 13, 13] from Image convolve 
	{
	   InputShape  = [29, 29];
	   KernelShape = [ 5,  5];
	   Stride      = [ 2,  2];
	   MapCount    = 5;
	}
	hidden Conv2 [50, 5, 5]
	from Conv1 convolve 
	{
	   InputShape  = [ 5, 13, 13];
	   KernelShape = [ 1,  5,  5];
	   Stride      = [ 1,  2,  2];
	   Sharing     = [false, true, true];
	   MapCount    = 10;
	}
	hidden Hid3 [100] from Conv2 all;
	output Digit [10] from Hid3 all;  


-	Die Struktur umfasst eine einzige Eingabeschicht: "Image".
-	Das Schlüsselwort **convolve** gibt an, dass "Conv1" und "Conv2" Konvolutionsschichten sind. Auf jede dieser Schichtdeklarationen folgt eine Liste der Konvolutionsattribute.
-	Das Netzwerk hat eine dritte verdeckte Schicht, "Hid3"; diese ist vollständig mit der zweiten verdeckten Schicht "Conv2" verbunden.
-	Die Ausgabeschicht "Digit" ist nur mit der dritten verdeckten Schicht (Hid3) verbunden. Das Schlüsselwort **all** gibt an, dass die Ausgabeschicht vollständig mit "Hid3" verbunden ist.
-	Die Arität der Konvolution ist 3 (die Länge der Tupel **InputShape**, **KernelShape**, **Stride** und **Sharing**). 
-	Die Anzahl der Gewichtungen pro Kernel ist 1 + **KernelShape**\[0] * **KernelShape**\[1] * **KernelShape**\[2] = 1 + 1 * 5 * 5 = 26. Oder 26 * 50 = 1300.
-	Sie können die Knoten in jeder verdeckten Schicht wie folgt berechnen:
	-	**NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
	-	**NodeCount**\[1] = (13 - 5) / 2 + 1 = 5. 
	-	**NodeCount**\[2] = (13 - 5) / 2 + 1 = 5. 
-	Die Gesamtanzahl Knoten kann anhand der deklarierten Dimensionalität der Schicht [50, 5, 5] wie folgt berechnet werden: **MapCount** * **NodeCount**\[0] * **NodeCount**\[1] * **NodeCount**\[2] = 10 * 5 * 5 * 5
-	Da **Sharing**[d] nur für d == 0 ist, beträgt die Anzahl der Kernel **MapCount** * **NodeCount**\[0] = 10 * 5 = 50. 

[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif

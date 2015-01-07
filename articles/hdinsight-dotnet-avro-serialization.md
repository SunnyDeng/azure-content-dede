<properties urlDisplayName="HDInsight Microsoft .NET Library for Serialization with Avro" pageTitle="Serialisieren von Daten mit Microsoft Avro Library | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize data with the Microsoft Avro Library " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


# Serialisieren von Daten mit Microsoft Avro Library

## Übersicht
In diesem Thema erfahren Sie, wie Sie mit der <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro-Bibliothek</a> Objekte und andere Datenstrukturen in Datenströme serialisieren, um sie in einer Datenbank oder einer Datei zu speichern, und wie Sie sie wieder deserialisieren, um die ursprünglichen Objekte wiederherzustellen. 

## Themen in diesem Artikel

- [Apache Avro](#apacheAvro)
- [Das Hadoop-Szenario](#hadoopScenario)
- [Serialisierung in der Microsoft Avro-Bibliothek](#serializationMAL) 
- [Microsoft Avro-Bibliothek - Voraussetzungen](#prerequisites)
- [Microsoft Avro-Bibliothek - Installation](#installation)
- [Microsoft Avro-Bibliothek - Quellcode](#sourceCode)
- [Kompilieren des Schemas mit der Microsoft Avro-Bibliothek](#compiling)
- [Kompilieren des Schemas für die Microsoft Avro-Bibliothek](#samples)


## <a name="apacheAvro"></a>Apache Avro
Die <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro-Bibliothek</a> implementiert das Apache Avro-Datenserialisierungssystem für die Microsoft .NET-Umgebung. Apache Avro stellt ein kompaktes binäres Datenaustauschformat für die Serialisierung bereit. Unter Verwendung von <a href="http://www.json.org" target="_blank">JSON</a> definiert es ein sprachunabhängiges Schema, das die Interoperabilität von Sprachen sicherstellt. Die in einer Sprache serialisierten Daten können in einer anderen Sprache gelesen werden. Derzeit werden C, C++, C#, Java, PHP, Python und Ruby unterstützt. Detaillierte Informationen über das Format finden Sie in der <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro-Spezifikation</a>. Beachten Sie, dass die aktuelle Version der Microsoft Avro-Bibliothek den Remoteprozeduraufruf-Teil (RPC) dieser Spezifikation nicht unterstützt.

Die serialisierte Darstellung eines Objekts im Avro-System besteht aus zwei Teilen: Schema und tatsächlichem Wert. Das Avro-Schema beschreibt das sprachunabhängige Datenmodell der serialisierten Daten mit JSON. Daneben liegt eine Binärdarstellung der Daten vor.  Dank der Trennung von Schema und Binärdarstellung kann jedes Objekt ohne Mehraufwand für die einzelnen Werte geschrieben werden, wodurch eine schnelle Serialisierung und eine kleine Darstellung sichergestellt werden. 

## <a name="hadoopScenario"></a>Das Hadoop-Szenario 
Das Apache Avro-Serialisierungsformat wird verbreitet in Azure HDInsight und anderen Apache Hadoop-Umgebungen eingesetzt. Avro bietet eine einfache Methode zur Darstellung komplexer Datenstrukturen in einem Hadoop MapReduce-Job. Das Format von Avro-Dateien (Avro Object Container File) ist darauf ausgelegt, das verteilte MapReduce-Programmiermodell zu unterstützen. Das wichtigste Merkmal, das die Verteilung ermöglicht, ist die "Teilbarkeit" der Dateien; hier bedeutet "teilbar", dass nach einem beliebigen Punkt in der Datei gesucht und ab einem bestimmten Block gelesen werden kann. 
 
## <a name="serializationMAL"></a> Serialisierung in der Microsoft Avro-Bibliothek
Die .NET-Bibliothek für Avro unterstützt zwei Methoden der Objektserialisierung:

- **Reflexion**: Das JSON-Schema für die Typen wird automatisch aus den Datenvertragsattributen der zu serialisierenden .NET-Typen erstellt. 
- **Generischer Datensatz**: Das JSON-Schema wird explizit in einem durch die [**AvroRecord**]-Klasse(http://msdn.microsoft.com/de-de/library/microsoft.hadoop.avro.avrorecord.aspx) dargestellten Datensatz angegeben, wenn keine .NET-Typen zum Beschreiben des Schemas für die zu serialisierenden Daten vorhanden sind. 

Wenn das Datenschema sowohl dem Writer als auch dem Reader des Datenstroms bekannt ist, können die Daten ohne das zugehörige Schema gesendet werden. Wenn eine Avro-Containerdatei verwendet wird, wird das Schema in der Datei gespeichert. Andere Parameter wie z. B. der Codec für die Datenkomprimierung können angegeben werden. Diese Szenarios werden in den nachfolgenden Codebeispielen ausführlicher erläutert und veranschaulicht.


## <a name="prerequisites"></a> Microsoft Avro-Bibliothek - Voraussetzungen
- <a href="http://www.microsoft.com/de-de/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4.0</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (Version 6.0.4 oder höher) 

Beachten Sie, dass die Newtonsoft.Json.dll-Abhängigkeit bei der Installation der Microsoft Avro -Bibliothek automatisch heruntergeladen wird. Das Installationsverfahren wird im folgenden Abschnitt erläutert.

## <a name="installation"></a> Microsoft Avro-Bibliothek - Installation
Die Microsoft Avro -Bibliothek wird als NuGet-Paket verteilt, das mit dem folgenden Verfahren in Visual Studio installiert werden kann: 

- Wählen Sie die Registerkarte **Projekt** -> **NuGet-Pakete verwalten...**
- Suchen Sie im Feld **Onlinesuche** nach "Microsoft.Hadoop.Avro".
- Klicken Sie neben **Microsoft Avro-Bibliothek** auf die Schaltfläche **Installieren**. 

Beachten Sie, dass die Newtonsoft.Json.dll-Abhängigkeit (>= 6.0.4) automatisch mit der Microsoft Avro-Bibliothek heruntergeladen wird.

Besuchen Sie nach Wunsch die Homepage der  <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro-Bibliothek,</a> um die aktuellen Versionshinweise zu lesen.
 
## <a name="sourceCode"></a>Microsoft Avro-Bibliothek - Quellcode

Der Quellcode der Microsoft Avro-Bibliothek steht auf der Homepage der <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro-Bibliothek zur Verfügung</a>.

## <a name="compiling"></a>Kompilieren des Schemas mit der Microsoft Avro-Bibliothek 

Die Microsoft Avro-Bibliothek enthält ein Hilfsprogramm zur Codegenerierung, mit dem C#-Typen basierend auf dem zuvor definierten JSON-Schema automatisch erstellt werden können. Das Hilfsprogramm zur Codegenerierung wird nicht als ausführbare Binärdatei verteilt, kann aber über das folgende Verfahren einfach erstellt werden:

1. Laden Sie die ZIP-Datei mit der neuesten Version des HDInsight SDK-Quellcodes aus dem  <a href="http://hadoopsdk.codeplex.com/SourceControl/latest" target="_blank">Microsoft .NET SDK für Hadoop herunter.</a>  (Klicken Sie auf das Symbol **Herunterladen**).
2. Extrahieren Sie das HDInsight SDK in ein Verzeichnis auf dem Computer, auf dem .NET Framework 4.0 installiert ist und der mit dem Internet verbunden ist, um die benötigten abhängigen NuGet-Pakete herunterzuladen. Im Folgenden gehen wir davon aus, dass der Quellcode in C:\SDK extrahiert wurde.
3. Wechseln Sie zum Ordner C:\SDK\src\Microsoft.Hadoop.Avro.Tools, und führen Sie build.bat aus. (Die Datei ruft MS Build in der 32-Bit-Distribution von .NET Framework auf. Wenn Sie die 64-Bit-Version verwenden möchten, bearbeiten Sie build.bat im Anschluss an die Kommentare in der Datei.) Vergewissern Sie sich, dass der Build erfolgreich erfolgt. (Auf bestimmten Systemen kann MS Build Warnungen erzeugen, die sich jedoch nicht auf das Hilfsprogramm auswirken, solange keine Buildfehler vorliegen.)
4. Das kompilierte Hilfsprogramm befindet sich in C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Um sich mit der Befehlszeilensyntax vertraut zu machen, führen Sie den folgenden Befehl in dem Ordner aus, indem sich das Hilfsprogramm zur Codegenerierung befindet: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Zum Testen des Hilfsprogramms können Sie C#-Klassen anhand der Beispieldatei des JSON-Schemas generieren, die mit dem Quellcode zur Verfügung gestellt wird. Führen Sie den folgenden Befehl aus:

	Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Hiermit sollen zwei C#-Dateien im aktuellen Verzeichnis erzeugt: SensorData.cs und Location.cs.

Um sich mit der Logik vertraut zu machen, die das Hilfsprogramm zur Codegenerierung beim Konvertieren des JSON-Schemas in C#-Typen verwendet, sehen Sie sich die Datei GenerationVerification.feature in C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc an.

Beachten Sie, dass Namespaces mittels der Logik aus dem JSON-Schema extrahiert werden, die in der im vorhergehenden Absatz angegebenen Datei beschrieben ist. Aus dem Schema extrahierte Namespaces haben Vorrang vor allem, was mithilfe des Parameters /n über die Befehlszeile des Hilfsprogramms angegeben wird. Wenn Sie die im Schema enthaltenen Namespaces überschreiben möchten, verwenden Sie den Parameter /nf. Um beispielsweise alle Namespaces von SampleJSONSchema.avsc in my.own.nspace zu ändern, führen Sie den folgenden Befehl aus:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Anleitung für die Beispiele für die Microsoft Avro-Bibliothek
Sechs Beispiele in diesem Thema veranschaulichen ein anderes von der Microsoft Avro-Bibliothek unterstütztes Szenario. Die Microsoft Avro-Bibliothek unterstützt alle Datenströme. In diesen Beispielen werden Daten aus Gründen der Einfachheit und Konsistenz nicht mit Dateidatenströmen oder Datenbanken, sondern mit Speicherdatenströmen bearbeitet. Welches Verfahren in einer Produktionsumgebung angewendet wird, hängt von den spezifischen Anforderungen des jeweiligen Szenarios, der Datenquelle und dem Datenvolumen, den Leistungsbeschränkungen und anderen Faktoren ab.

In den ersten beiden Beispielen wird gezeigt, wie Daten mithilfe von Reflexion und generischen Datensätzen in Speicherdatenstrom-Puffer serialisiert und aus diesen deserialisiert werden. Das Schema in diesen beiden Fällen wird von den Readern und Writern Out-of-band gemeinsam genutzt. 

Im dritten und vierten Beispiel wird gezeigt, wie Daten mithilfe von Avro-Objektcontainerdateien serialisiert und deserialisiert werden. Wenn Daten in einer Avro-Containerdatei gespeichert werden, wird das zugehörige Schema stets mit den Daten gespeichert, da das Schema für die Deserialisierung freigegeben werden muss.

Das Beispiel mit den ersten vier Beispielen kann von der Website mit <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">Azure-Codebeispielen</a> heruntergeladen werden.

Das fünfte Beispiel zeigt die Verwendung eines benutzerdefinierten Komprimierungscodecs für Avro-Objektcontainerdateien. Ein Beispiel mit dem Code für dieses Beispiel kann von der Website mit <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">Azure-Codebeispielen</a> heruntergeladen werden.

Das sechste Beispiel zeigt, wie mithilfe der Avro-Serialisierung Daten in Azure-BLOB-Speicher hochgeladen und anschließend mithilfe von Hive in einem HDInsight (Hadoop)-Cluster analysiert werden. Es kann von der Website mit den <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Azure-Codebeispielen</a> heruntergeladen werden.

Es folgen Links zu den sechs in diesem Thema behandelten Beispielen:

 * <a href="#Scenario1">**Serialisierung mit Reflexion**</a>: Das JSON-Schema für die zu serialisierenden Typen wird automatisch aus den Datenvertragsattributen erstellt.
 * <a href="#Scenario2">**Serialisierung mit generischem Datensatz**</a>: Das JSON-Schema wird explizit in einem Datensatz angegeben, wenn kein .NET-Typ für die Reflexion verfügbar ist.
 * <a href="#Scenario3">**Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion**:</a>: Das JSON-Schema wird automatisch erstellt und mithilfe einer Avro-Objektcontainerdatei zusammen mit den serialisierten Daten genutzt.
 * <a href="#Scenario4">**Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz**</a>: Das JSON-Schema wird vor der Serialisierung explizit angegeben und mithilfe einer Avro-Objektcontainerdatei zusammen mit den Daten genutzt.
 * <a href="#Scenario5">**Serialisierung unter Verwendung von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec**</a>: Das Beispiel zeigt, wie eine Avro-Objektcontainerdatei mit einer benutzerdefinierten .NET-Implementierung des Deflate-Datenkomprimierungscodecs erstellt wird.
 * <a href="#Scenario6">**Verwenden von Avro zum Hochladen von Daten für den Microsoft Azure HDInsight-Dienst**</a>: Veranschaulicht, wie die Avro-Serialisierung mit dem HDInsight-Dienst interagiert. Zum Ausführen dieses Beispiels sind ein aktives Azure-Abonnement und Zugriff auf einen Microsoft Azure HDInsight-Cluster erforderlich.

<h3> <a name="Scenario1"></a>Beispiel 1: Serialisierung mit Reflexion</h3>
 
Das JSON-Schema für die Typen kann von der Microsoft Avro-Bibliothek automatisch mithilfe von Reflexion aus den Datenvertragsattributen der zu serialisierenden .C#-Objekte erstellt werden. Die Microsoft Avro-Bibliothek erstellt einen [**IAvroSeralizer<T>**],(http://msdn.microsoft.com/de-de/library/dn627341.aspx) um die zu serialisierenden Felder zu identifizieren.

In diesem Beispiel werden Objekte (eine **SensorData**-Klasse mit einer **Location**-Mitgliedsstruktur) in einen Speicherdatenstrom serialisiert. Dieser Datenstrom wird wiederum deserialisiert. Anschließend wird das Ergebnis mit der ursprünglichen Instanz verglichen, um zu bestätigen, dass das wiederhergestellte **SensorData**-Objekt mit dem Original identisch ist.

In diesem Beispiel wird davon ausgegangen, dass das Schema von Readern und Writern gemeinsam verwendet wird, sodass das Avro-Objektcontainerformat nicht erforderlich ist. Ein Beispiel für das Serialisieren und Deserialisieren von Daten in Speicherpuffer mithilfe von Reflexion unter Verwendung des Objektcontainerformats, wenn das Schema mit den Daten serialisiert werden muss, finden Sie unter <a href="#Scenario3">Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion</a>

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using Reflection
            //No explicit schema definition is required - schema of serialized objects is automatically built
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set using sample Class and struct 
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using Reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


<h3> <a name="Scenario2"></a>Beispiel 2: Serialisierung mit generischem Datensatz</h3>

Ein JSON-Schema kann explizit in einem generischen Datensatz angegeben werden, wenn die Verwendung von Reflexion nicht möglich ist, weil die Daten nicht mithilfe von .NET-Klassen mit einem Datenvertrag dargestellt werden können. Diese Methode ist im Allgemeinen langsamer als die Verwendung von Reflexion. In diesen Fällen kann das Schema für die Daten auch dynamisch, d. h. zum Zeitpunkt der Kompilierung bekannt sein muss. Ein Beispiel für ein solches dynamisches Szenario sind Daten, die als CSV-Dateien (Comma Separated Values, mit Trennzeichen getrennte Werte) dargestellt werden und deren Schema erst bekannt wird, wenn sie zur Laufzeit in das Avro-Format transformiert werden.

In diesem Beispiel wird gezeigt, wie ein [**AvroRecord**](http://msdn.microsoft.com/de-de/library/microsoft.hadoop.avro.avrorecord.aspx) erstellt und verwendet wird, um ein JSON-Schema explizit anzugeben, wie dieses mit den Daten gefüllt und anschließend serialisiert und deserialisiert wird. Anschließend wird das Ergebnis mit der ursprünglichen Instanz verglichen, um zu bestätigen, dass der wiederhergestellte Datensatz mit dem Original identisch ist.

In diesem Beispiel wird davon ausgegangen, dass das Schema von Readern und Writern gemeinsam verwendet wird, sodass das Avro-Objektcontainerformat nicht erforderlich ist. Ein Beispiel für das Serialisieren und Deserialisieren von Daten in Speicherpuffer mithilfe eines generischen Datensatzes unter Verwendung des Objektcontainerformats, wenn das Schema in die serialisierten Daten eingeschlossen werden muss, finden Sie unter dem Beispiel <a href="#Scenario4">Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz</a> .


	namespace Microsoft.Hadoop.Avro.Sample
	{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set using Generic Record.
        //Generic Record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of Generic Record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a Memory Stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample Class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using Generic Record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
	}
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


<h3> <a name="Scenario3"></a>Beispiel 3: Serialisierung unter Verwendung von Objektcontainerdateien und Serialisierung mit Reflexion</h3>

Dieses Beispiel ähnelt dem Szenario im  <a href="#Scenario1"> ersten Beispiel,</a> in dem das Schema implizit mit Reflexion angegeben wird. Der Unterschied besteht darin, dass bei diesem Beispiel nicht davon ausgegangen wird, dass das Schema dem Reader bekannt ist, der die Deserialisierung durchführt. Die zu serialisierenden **SensorData**-Objekte und das zugehörige implizit angegebene Schema werden in einer Objektcontainerdatei gespeichert, die durch die [**AvroContainer**]-Klasse(http://msdn.microsoft.com/de-de/library/microsoft.hadoop.avro.container.avrocontainer.aspx) dargestellt wird. 

Die Daten werden in diesem Beispiel mit [**SequentialWriter<SensorData>**] serialisiert(http://msdn.microsoft.com/de-de/library/dn627340.aspx) und mit [**SequentialReader<SensorData>**] deserialisiert(http://msdn.microsoft.com/de-de/library/dn627340.aspx). Anschließend wird das Ergebnis mit den ursprünglichen Instanzen verglichen, um die Identität sicherzustellen.

Die Daten in der Objektcontainerdatei werden mit dem [**Deflate**][deflate-100]-Standardkomprimierungscodec von .NET Framework 4.0 komprimiert. Siehe das <a href="#Scenario5"> fünfte Beispiel</a> in diesem Thema, um zu erfahren, wie sie eine neuere und erweiterte Version des in .NET Framework 4.5 verfügbaren [**Deflate**][deflate-110]-Komprimierungscodecs verwenden.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with Deflate codec
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will be compressed using Deflate codec
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.
  

<h3> <a name="Scenario4"></a>Beispiel 4: Serialisierung unter Verwendung von Objektcontainerdateien und Serialisierung mit generischem Datensatz</h3>

Dieses Beispiel hat Ähnlichkeit mit dem <a href="#Scenario2"> zweiten Beispiel,</a> in dem das Schema explizit mit JSON angegeben wird. Der Unterschied besteht darin, dass bei diesem Beispiel nicht davon ausgegangen wird, dass das Schema dem Reader bekannt ist, der die Deserialisierung durchführt. 

Der Testdatensatz wird unter Verwendung eines explizit definierten JSON-Schemas in einer Liste von [**AvroRecord**](http://msdn.microsoft.com/de-de/library/microsoft.hadoop.avro.avrorecord.aspx) -Objekten gesammelt und anschließend in einer Objektcontainerdatei gespeichert, die durch die [**AvroContainer**]-Klasse(http://msdn.microsoft.com/de-de/library/microsoft.hadoop.avro.container.avrocontainer.aspx) dargestellt wird. Diese Containerdatei erstellt einen Writer, mit dem die Daten in unkomprimierter Form in einen Speicherdatenstrom serialisiert werden, der anschließend in einer Datei gespeichert wird. Beim Erstellen des Readers wird mit dem [**Codex.Null**](http://msdn.microsoft.com/de-de/library/microsoft.hadoop.avro.container.codec.null.aspx) -Parameter angegeben, dass diese Daten nicht komprimiert werden. 

Anschließend werden die Daten aus der Datei gelesen und in eine Objektsammlung deserialisiert. Diese Sammlung wird mit der ursprünglichen Liste von Avro-Datensätzen verglichen, um zu bestätigen, dass sie identisch sind.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
            //Serialized data is not compressed
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file
                //Create a MemoryStream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will not be compressed (Null compression codec)
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializng the data
                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Generic Record to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




<h3> <a name="Scenario5"></a>Beispiel 5: Serialisierung unter Verwendung von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec</h3>

Das fünfte Beispiel zeigt die Verwendung eines benutzerdefinierten Komprimierungscodecs für Avro-Objektcontainerdateien. Ein Beispiel mit dem Code für dieses Beispiel kann von der Website mit [Azure-Codebeispielen](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) heruntergeladen werden.

Die [Avro-Spezifikation](http://avro.apache.org/docs/current/spec.html#Required+Codecs) ermöglicht die Verwendung eines optionalen Komprimierungscodecs (zusätzlich zu den Standardcodecs  **Null** und **Deflate**). In diesem Beispiel sind neue Codecs wie Snappy (der in der [Avro-Spezifikation] als unterstützter optionaler Codec aufgeführt wird) nicht vollständig implementiert(http://avro.apache.org/docs/current/spec.html#snappy)). Das Beispiel zeigt die Verwendung der .NET Framework 4.5-Implementierung des [**Deflate**][deflate-110]-Codecs, die einen besseren Komprimierungsalgorithmus auf Basis der [zlib]-Komprimierungsbibliothek(http://zlib.net/) als die .NET Framework 4.0-Standardversion bereitstellt.


    // 
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        #region Defining objects for serialization
        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET Codec class contains two methods 
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //which are the key ones for data compression.
        //To enable a custom codec one needs to implement these methods for the required codec

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //can not be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from Stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features 
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //Codec class uses classes for comressed and decompressed streams defined above
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified Codec Factory to be used in Reader
        //It will catch the attempt to use "deflate" and provide Custom Codec 
        //For all other cases it will rely on the base class (CodecFactory)
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
            //
            //This sample uses Memory Stream for all operations related to serialization, deserialization and
            //Object Container manipulation, though File Stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Here the custom Codec is introduced. For convenience the next commented code line shows how to use built-in Deflate.
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
                    //when Codec Factory is explicitly specified
                    //You may comment the line below if you want to use built-in Deflate (see next comment)
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    //Here the custom Codec Factory is introduced.
                    //For convenience the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File using Custom Codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h3> <a name="Scenario6"></a> Beispiel 6: Verwenden von Avro zum Hochladen von Daten für den Microsoft Azure HDInsight-Dienst</h3>

Das sechste Beispiel veranschaulicht verschiedene Programmierungstechniken im Zusammenhang mit der Interaktion mit dem Microsoft Azure HDInsight-Dienst. Ein Beispiel mit dem Code für dieses Beispiel kann von der Website mit [Azure-Codebeispielen](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) heruntergeladen werden.

Das Beispiel bewirkt Folgendes:

* Herstellen einer Verbindung mit einem vorhandenen HDInsight Storm-Cluster.
* Serialisieren mehrerer CSV-Dateien und Hochladen des Ergebnisses in Azure-BLOB-Speicher. (Die CSV-Dateien werden zusammen mit dem Beispiel verteilt und stellen AMEX Stock-Verlaufsdaten dar, die von [Infochimps](http://www.infochimps.com/) für den Zeitraum 1970-2010 verteilt wurden. Das Beispiel liest CSV-Dateidaten, konvertiert die Datensätze in Instanzen der **Stock**-Klasse und serialisiert sie anschließend mithilfe von Reflexion. Die Definition des Typs Stock wird anhand eines JSON-Schemas mithilfe des Hilfsprogramms für die Codegenerierung in der Microsoft Avro-Bibliothek erstellt.
* Erstellen einer neuen externen Tabelle mit dem Namen **Stocks** in Hive, die anschließend mit den im vorangegangenen Schritt hochgeladenen Daten verknüpft werden.
* Ausführen einer Abfrage mithilfe von Hive der Tabelle **Stocks**.

Darüber hinaus führt das Beispiel vor und nach der Ausführung wichtiger Vorgänge ein Aufräumverfahren durch. Während des Aufräumens werden alle zugehörigen Azure-BLOB-Daten und -Ordner entfernt, und die Hive-Tabelle wird gelöscht. Sie können Aufräumverfahren auch über die Befehlszeile des Beispiels aufrufen. 

Für das Beispiel ist Folgendes erforderlich:

* Ein aktives Microsoft Azure-Abonnement samt Abonnement-ID.
* Ein Verwaltungszertifikat für das Abonnement mit dem entsprechenden privaten Schlüssel. Das Zertifikat muss auf dem Computer, auf dem das Beispiel ausgeführt wird, im privaten Speicher des aktuellen Benutzers installiert werden.
* Ein aktiver Azure HDInsight-Cluster.
* Ein Azure-Speicherkonto, das mit dem HDInsight-Cluster aus der vorhergehenden Vorbedingung verknüpft ist, zusammen mit dem entsprechenden primären oder sekundären Zugriffsschlüssel.

Alle Informationen aus den Vorbedingungen müssen vor der Ausführung des Beispiels in dessen Konfigurationsdatei eingegeben werden. Dies kann auf zwei Arten erreicht werden:

* Bearbeiten der Datei app.config im Stammverzeichnis des Beispiels und anschließendes Erstellen des Beispiels oder 
* Erstellen des Beispiels und anschließende Bearbeitung der Datei AvroHDISample.exe.config im Buildverzeichnis 

In beiden Fällen müssen alle Bearbeitungen im Einstellungsabschnitt **<appSettings>** erfolgen. Befolgen Sie die Kommentare in der Datei.
Das Beispiel wird über die Befehlszeile mittels des folgenden Befehls ausgeführt (wobei davon ausgegangen wird, dass die ZIP-Datei mit dem Beispiel in C:\AvroHDISample extrahiert wurde. Verwenden Sie andernfalls den entsprechenden Dateipfad).

    AvroHDISample run C:\AvroHDISample\Data

Führen Sie zum Aufräumen des Clusters den folgenden Befehl aus:

    AvroHDISample clean




[deflate-100]: http://msdn.microsoft.com/de-de/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/de-de/library/system.io.compression.deflatestream(v=vs.110).aspx



<!--HONumber=35.1-->

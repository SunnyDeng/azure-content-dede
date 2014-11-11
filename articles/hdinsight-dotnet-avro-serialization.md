<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Microsoft .NET Library for Serialization with Avro" pageTitle="Serialize data with the Microsoft .NET Library for Avro | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize data with the Microsoft .NET Library for Avro " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro

## Übersicht

In diesem Thema erfahren Sie, wie Sie mit der Microsoft .NET-Bibliothek für Avro Objekte und andere Datenstrukturen in Datenströme serialisieren, um sie in einer Datenbank oder einer Datei zu speichern, und wie Sie sie wieder deserialisieren, um die ursprünglichen Objekte wiederherzustellen.

### Apache Avro

Die Microsoft .NET-Bibliothek für Avro implementiert das Apache Avro-Datenserialisierungssystem für die Microsoft .NET-Umgebung. Apache Avro stellt ein kompaktes binäres Datenaustauschformat für die Serialisierung bereit. Unter Verwendung von [JSON][JSON] definiert es ein sprachunabhängiges Schema, das die Interoperabilität von Sprachen sicherstellt. Die in einer Sprache serialisierten Daten können in einer anderen Sprache gelesen werden. Derzeit werden C, C++, C#, Java, PHP, Python und Ruby unterstützt. Detaillierte Informationen über das Format finden Sie in der [Apache Avro-Spezifikation][Apache Avro-Spezifikation]. Beachten Sie, dass die aktuelle Version der Microsoft .NET-Bibliothek für Avro den Teil zu Remoteprozeduraufrufen (Remote Procedure Calls, RPC) dieser Spezifikation nicht unterstützt.

Die serialisierte Darstellung eines Objekts im Avro-System besteht aus zwei Teilen: Schema und tatsächlichem Wert. Das Avro-Schema beschreibt das sprachunabhängige Datenmodell der serialisierten Daten mit JSON. Daneben liegt eine Binärdarstellung der Daten vor. Dank der Trennung von Schema und Binärdarstellung kann jedes Objekt ohne Mehraufwand für die einzelnen Werte geschrieben werden, wodurch eine schnelle Serialisierung und eine kleine Darstellung sichergestellt werden.

### Das Hadoop-Szenario

Das Apache Avro-Serialisierungsformat wird verbreitet in Azure HDInsight und anderen Apache Hadoop-Umgebungen eingesetzt. Avro bietet eine einfache Methode zur Darstellung komplexer Datenstrukturen in einem Hadoop MapReduce-Job. Das Format von Avro-Dateien ist dafür ausgelegt, das verteilte MapReduce-Programmiermodell zu unterstützen. Das wichtigste Merkmal, das die Verteilung ermöglicht, ist die "Teilbarkeit" der Dateien; hier bedeutet "teilbar", dass nach einem beliebigen Punkt in der Datei gesucht und ab einem bestimmten Block gelesen werden kann.

### Serialisierung in der Microsoft .NET-Bibliothek für Avro

Die .NET-Bibliothek für Avro unterstützt zwei Methoden der Objektserialisierung:

-   **Reflexion**: Das JSON-Schema für die Typen wird automatisch aus den Datenvertragsattributen der zu serialisierenden .NET-Typen erstellt.
-   **Generischer Datensatz**: Das JSON-Schema wird explizit in einem durch die [**AvroRecord**][**AvroRecord**]-Klasse dargestellten Datensatz angegeben, wenn keine .NET-Typen zum Beschreiben des Schemas für die zu serialisierenden Daten vorhanden sind.

Wenn das Datenschema sowohl dem Writer als auch dem Reader des Datenstroms bekannt ist, können die Daten ohne das zugehörige Schema gesendet werden. Anderenfalls muss das Schema unter Verwendung einer Avro-Containerdatei zur gemeinsamen Nutzung freigegeben werden. Andere Parameter wie z. B. der Codec für die Datenkomprimierung können angegeben werden. Diese Szenarios werden in den nachfolgenden Codebeispielen ausführlicher erläutert und veranschaulicht.

### Microsoft .NET-Bibliothek für Avro – Voraussetzungen

-   [Microsoft .NET Framework 4.0][Microsoft .NET Framework 4.0]
-   [Newtonsoft Json.NET][Newtonsoft Json.NET] (Version 5.0.5 oder höher)

Beachten Sie, dass die Newtonsoft.Json.dll-Abhängigkeit bei der Installation der Microsoft .NET-Bibliothek für Avro automatisch heruntergeladen wird; das Installationsverfahren wird im folgenden Abschnitt erläutert.

### Microsoft .NET-Bibliothek für Avro – Installation

Die Microsoft .NET-Bibliothek für Avro wird als NuGet-Paket verteilt, das mit dem folgenden Verfahren aus Visual Studio installiert werden kann:

-   Wählen Sie die Registerkarte **Projekt** -\> **NuGet Pakete verwalten...**
-   Suchen Sie im Feld **Onlinesuche** nach "Microsoft.Hadoop.Avro".
-   Klicken Sie auf die Schaltfläche **Installieren** neben **Microsoft .NET-Bibliothek für Avro**.

Beachten Sie, dass die Newtonsoft.Json.dll-Abhängigkeit (\>= .5.0.5) automatisch mit der Microsoft .NET-Bibliothek für Avro heruntergeladen wird.

## Erläuterungen zu den Beispielen

Jedes der Beispiele in diesem Thema veranschaulicht ein anderes von der Microsoft .NET-Bibliothek für Avro unterstütztes Szenario.

In den ersten zwei Beispielen wird gezeigt, wie Daten mithilfe von Reflexion und generischen Datensätzen in Speicherdatenstrom-Puffer serialisiert und aus diesen deserialisiert werden. In diesen zwei Fällen wird davon ausgegangen, dass das Schema von den Readern und Writern extern gemeinsam genutzt wird, sodass das Schema nicht zusammen mit den Daten in eine Avro-Containerdatei serialisiert werden muss.

Im dritten und vierten Beispiel wird gezeigt, wie Daten mithilfe von Reflexion und generischem Datensatz unter Verwendung von Avro-Objektcontainerdateien in Speicherdatenstrom-Puffer serialisiert und aus diesen deserialisiert werden. Wenn Daten in einer Avro-Containerdatei gespeichert werden, wird das zugehörige Schema stets mit den Daten gespeichert, da das Schema für die Deserialisierung freigegeben werden muss.

Das Beispiel mit den ersten vier Beispielen kann von der Website mit [Azure-Codebeispielen][Azure-Codebeispielen] heruntergeladen werden.

Das fünfte und letzte Beispiel zeigt die Verwendung eines benutzerdefinierten Komprimierungscodecs für Objektcontainerdateien. Ein Beispiel mit dem Code für dieses Beispiel kann von der Website mit [Azure-Codebeispielen][1] heruntergeladen werden.

Die Microsoft .NET-Bibliothek für Avro unterstützt alle Datenströme. In diesen Beispielen werden Daten aus Gründen der Einfachheit und Konsistenz nicht mit Dateidatenströmen oder Datenbanken, sondern mit Speicherdatenströmen bearbeitet. Welches Verfahren in einer Produktionsumgebung angewendet wird, hängt von den spezifischen Anforderungen des jeweiligen Szenarios, der Datenquelle und dem Datenvolumen, den Leistungsbeschränkungen und anderen Faktoren ab.

-   [**Serialisierung mit Reflexion**][**Serialisierung mit Reflexion**]: Das JSON-Schema für die zu serialisierenden Typen wird automatisch aus den Datenvertragsattributen erstellt.
-   [**Serialisierung mit generischem Datensatz**][**Serialisierung mit generischem Datensatz**]: Das JSON-Schema wird explizit in einem Datensatz angegeben, wenn kein .NET-Typ für die Reflexion verfügbar ist.
-   [**Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion**][**Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion**]: Das JSON-Schema wird implizit mit den Daten serialisiert und unter Verwendung einer Avro-Containerdatei freigegeben.
-   [**Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz**][**Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz**]: Das JSON-Schema wird explizit mit den Daten serialisiert und unter Verwendung einer Avro-Containerdatei freigegeben.
-   [**Serialisierung unter Verwendung von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec**][**Serialisierung unter Verwendung von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec**]: Das JSON-Schema wird mit den Daten serialisiert und unter Verwendung einer Avro-Containerdatei mit einer benutzerdefinierten .NET-Implementierung des Deflate-Datenkomprimierungscodecs freigegeben.

## <a name="Scenario1"></a>Serialisierung mit Reflexion

Das JSON-Schema für die Typen kann von der Microsoft .NET-Bibliothek für Avro automatisch mithilfe von Reflexion aus den Datenvertragsattributen der zu serialisierenden .C#-Objekte erstellt werden. Die Microsoft .NET-Bibliothek für Avro erstellt einen [**IAvroSeralizer<t>**][**IAvroSeralizer<t>**], um die zu serialisierenden Felder zu identifizieren.

In diesem Beispiel werden Objekte (eine **SensorData**-Klasse mit einer **Location**-Mitgliedsstruktur) in einen Speicherdatenstrom serialisiert; dieser Datenstrom wird wiederum deserialisiert. Anschließend wird das Ergebnis mit der ursprünglichen Instanz verglichen, um zu bestätigen, dass das wiederhergestellte **SensorData**-Objekt mit dem Original identisch ist.

In diesem Beispiel wird davon ausgegangen, dass das Schema von Readern und Writern gemeinsam verwendet wird, sodass das Avro-Objektcontainerformat nicht erforderlich ist. Ein Beispiel für das Serialisieren und Deserialisieren von Daten in Speicherpuffer mithilfe von Reflexion unter Verwendung des Objektcontainerformats, wenn das Schema mit den Daten serialisiert werden muss, finden Sie unter [Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion][**Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion**].

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
        //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario2"></a>Serialisierung mit generischem Datensatz

Ein JSON-Schema kann explizit in einem generischen Datensatz angegeben werden, wenn die Verwendung von Reflexion nicht möglich ist, weil die Daten nicht mithilfe von .NET-Klassen mit einem Datenvertrag dargestellt werden können. Diese Methode ist im Allgemeinen langsamer als die Verwendung von Reflexion und Serialisierern für eine bestimmte C#-Klasse. In diesen Fällen kann das Schema für die Daten auch dynamisch sein, da es erst zum Zeitpunkt der Kompilierung bekannt sein muss. Ein Beispiel für ein solches dynamisches Szenario sind Daten, die als CSV-Dateien (Comma Separated Values, mit Trennzeichen getrennte Werte) dargestellt werden und deren Schema erst bekannt wird, wenn sie zur Laufzeit in das Avro-Format transformiert werden.

In diesem Beispiel wird gezeigt, wie ein [**AvroRecord**][**AvroRecord**] erstellt und verwendet wird, um ein JSON-Schema explizit anzugeben, wie dieses mit den Daten gefüllt und anschließend serialisiert und deserialisiert wird. Anschließend wird das Ergebnis mit der ursprünglichen Instanz verglichen, um zu bestätigen, dass der wiederhergestellte Datensatz mit dem Original identisch ist.

In diesem Beispiel wird davon ausgegangen, dass das Schema von Readern und Writern gemeinsam verwendet wird, sodass das Avro-Objektcontainerformat nicht erforderlich ist. Ein Beispiel für das Serialisieren und Deserialisieren von Daten in Speicherpuffer unter Verwendung eines generischen Datensatzes mit dem Objektcontainerformat, wenn das Schema in die serialisierten Daten eingeschlossen werden muss, finden Sie im Beispiel [Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz][**Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz**].

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
    //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario3"></a>Serialisierung unter Verwendung von Objektcontainerdateien und Serialisierung mit Reflexion

Dieses Beispiel ähnelt dem Szenario im [ersten Beispiel][**Serialisierung mit Reflexion**], in dem das Schema implizit mit Reflexion angegeben wird; der Unterschied besteht darin, dass bei diesem Beispiel nicht davon ausgegangen wird, dass das Schema dem Reader bekannt ist, der die Deserialisierung durchführt. Die zu serialisierenden **SensorData**-Objekte und das zugehörige implizit angegebene Schema werden in einer Objektcontainerdatei gespeichert, die durch die [**AvroContainer**][**AvroContainer**]-Klasse dargestellt wird.

Die Daten werden in diesem Beispiel mit [**SequentialWriter<sensordata>**][**SequentialWriter<sensordata>**] serialisiert und mit [**SequentialReader<sensordata>**][**SequentialWriter<sensordata>**] deserialisiert. Anschließend wird das Ergebnis mit den ursprünglichen Instanzen verglichen, um die Identität sicherzustellen.

Die Datei in der Objektcontainerdatei werden mit dem [**Deflate**][**Deflate**]-Standardkomprimierungscodec von .NET Framework 4.0 komprimiert. Im [letzten Beispiel][**Serialisierung unter Verwendung von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec**] dieses Themas erfahren Sie, wie sie eine neuere und erweiterte Version des in .NET Framework 4.5 verfügbaren [**Deflate**][2]-Komprimierungscodecs verwenden.

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
        //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario4"></a>Serialisierung unter Verwendung von Objektcontainerdateien und Serialisierung mit generischem Datensatz

Dieses Beispiel ähnelt dem Szenario im [zweiten Beispiel][**Serialisierung mit generischem Datensatz**], in dem das Schema explizit mit JSON angegeben wird; der Unterschied besteht darin, dass bei diesem Beispiel nicht davon ausgegangen wird, dass das Schema dem Reader bekannt ist, der die Deserialisierung durchführt.

Der Testdatensatz wird unter Verwendung eines explizit definierten JSON-Schemas in einer Liste von [**AvroRecord**][**AvroRecord**]-Objekten gesammelt und anschließend in einer Objektcontainerdatei gespeichert, die durch die [**AvroContainer**][**AvroContainer**]-Klasse dargestellt wird. Diese Containerdatei erstellt einen Writer, mit dem die Daten in unkomprimierter Form in einen Speicherdatenstrom serialisiert werden, der anschließend in einer Datei gespeichert wird. Beim Erstellen des Readers wird mit dem [**Codex.Null**][**Codex.Null**]-Parameter angegeben, dass diese Daten nicht komprimiert werden.

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
        //the usage of Microsoft .NET Library for Avro
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

## <a name="Scenario5"></a>Serialisierung unter Verwendung von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec

Im folgenden Beispiel wird gezeigt, wie ein benutzerdefinierter Komprimierungscodec für Avro-Objektcontainerdateien verwendet wird. Die [Avro-Spezifikation][Avro-Spezifikation] ermöglicht die Verwendung eines optionalen Komprimierungscodecs (zusätzlich zu den Standardcodecs **Null** und **Deflate**). In diesem Beispiel sind neue Codecs wie Snappy (der in der [Avro-Spezifikation][3] als unterstützter optionaler Codec aufgeführt wird) nicht vollständig implementiert. Das Beispiel zeigt die Verwendung der .NET Framework 4.5-Implementierung des [**Deflate**][2]-Codecs, die einen besseren Komprimierungsalgorithmus auf Basis der [zlib][zlib]-Komprimierungsbibliothek als die Standardversion aus .NET Framework 4.0 bereitstellt.

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
        //the usage of Microsoft .NET Library for Avro
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

  [JSON]: http://www.json.org
  [Apache Avro-Spezifikation]: http://avro.apache.org/docs/current/spec.html
  [Microsoft .NET Framework 4.0]: http://www.microsoft.com/de-de/download/details.aspx?id=17851
  [Newtonsoft Json.NET]: http://james.newtonking.com/json
  [Azure-Codebeispielen]: http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923
  [1]: http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111
  [**Serialisierung unter Verwendung von Objektcontainerdateien mit Reflexion**]: #Scenario3
  [**Serialisierung unter Verwendung von Objektcontainerdateien mit generischem Datensatz**]: #Scenario4
  [2]: http://msdn.microsoft.com/de-de/library/system.io.compression.deflatestream(v=vs.110).aspx
  [Avro-Spezifikation]: http://avro.apache.org/docs/current/spec.html#Required+Codecs
  [3]: http://avro.apache.org/docs/current/spec.html#snappy
  [zlib]: http://zlib.net/

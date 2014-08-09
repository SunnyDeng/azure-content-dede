<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Avro.NET Serialization" pageTitle="Serialize Data with Avro.NET | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro.NET to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize Data with Avro.NET " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Serialisieren von Daten mit Avro.NET
====================================

Übersicht
---------

In diesem Thema wird gezeigt, wie die Microsoft Avro-Bibliothek (Avro.NET) zum Serialisieren von Objekten und anderen Datenstrukturen in Bytestreams verwendet wird, um sie im Arbeitsspeicher, einer Datenbank oder einer Datei zu speichern. Die Bibliothek implementiert das Apache Avro-Datenserialisierungssystem unter Verwendung von JSON und definiert sprachenunabhängige Schemata, die ein kompaktes binäres Datenaustauschformat bereitstellen, das von vielen Sprachen verarbeitet werden kann (derzeit von C, C++, C\#, Java, PHP, Python und Ruby), wenn die Objekte mittels der Deserialisierung wiederhergestellt werden sollen.

Das Apache Avro-Serialisierungsformat wird verbreitet in Hadoop-Umgebungen einschließlich Microsoft HDInsight eingesetzt. Detaillierte Informationen über das Format finden Sie in der Apache Avro-Spezifikation.

Die Microsoft HDInsight Avro.NET-Bibliothek unterstützt zwei Verfahren der Serialisierung von Objekten: Reflection und Generic Record.

Voraussetzungen
---------------

Die Microsoft .NET-Bibliothek für Avro wird als NuGet-Paket verteilt. Gehen Sie zur Installation in Visual Studio folgendermaßen vor: Wählen Sie die Registerkarte **Projekt** -\> **NuGet-Pakete verwalten...**, suchen Sie im Feld **Onlinesuche** nach "Avro", und klicken Sie anschließend auf **Installieren** neben **Microsoft .NET Library for Avro**. Die Abhängigkeit Newtonsoft.Json (\>= .5.0.5) wird automatisch heruntergeladen.

### Übersicht

Szenarien:

-   [Serialisierung unter Verwendung von Reflection](#Scenario1)

-   [Serialisierung mithilfe von Avro-Objektcontainern](#Scenario2)

-   [Serialisierung mithilfe von Objektcontainerdateien und Serialisierung mit Reflection](#Scenario3)

-   [Serialisierung mithilfe von Objektcontainerdateien und Serialisierung mit Generic Record](#Scenario4)

-   [Serialisierung mithilfe von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec](#Scenario5)

Serialisierung unter Verwendung von Reflection
----------------------------------------------

Im Beispiel unten werden eine Klasse und eine Struktur serialisiert, deserialisiert und schließlich mit den Ausgangsinstanzen verglichen, um die Identität sicherzustellen. Das JSON-Schema für die Typen wird automatisch von der Microsoft Avro-Bibliothek erstellt, wobei die Datenvertragsattribute berücksichtigt werden. Die Microsoft Avro-Bibliothek verwendet [Data Contract Serializer](http://msdn.microsoft.com/de-de/library/ms731072(v=vs.110).aspx) um die serialisierten Felder zu identifizieren.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //In Serialisierungsbeispielen verwendete Beispielklasse
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //In Serialisierungsbeispielen verwendete Beispielstruktur
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Diese Klasse enthält alle Methoden, die die
    //Verwendung der Microsoft Avro-Bibliothek demonstrieren.
    public class AvroSample
    {

    //Als Objekt dargestellten Beispieldatensatz mit Reflection serialisieren und deserialisieren
    //Keine explizite Schemadefinition erforderlich - Schema serialisierter Objekte wird automatisch erstellt
    public void SerializeDeserializeObjectUsingReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION\n");
    Console.WriteLine("Serializing Sample Data Set...");

    //Neue AvroSerializer-Instanz erstellen und eine benutzerdefinierte Serialisierungsstrategie AvroDataContractResolver festlegen,
    //um nur Eigenschaften zu serialisieren, die das Attribut DataContract/DateMember besitzen
    var avroSerializer = AvroSerializer.Create<SensorData>();

    //Puffer für Speicherstream erstellen
    using (var buffer = new MemoryStream())
    {
    //Datensatz mit Beispielklasse und -struktur erstellen 
    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

    //Daten für den angegebenen Stream serialisieren
    avroSerializer.Serialize(buffer, expected);

      
    Console.WriteLine("Deserializing Sample Data Set...");

    //Stream zum Deserialisieren der Daten vorbereiten
    buffer.Seek(0, SeekOrigin.Begin);

    //Daten des Streams deserialisieren und in den für die Serialisierung verwendeten Typ umwandeln
    var actual = avroSerializer.Deserialize(buffer);

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Zum Schluss überprüfen, ob die deserialisierten Daten den ursprünglichen entsprechen
    bool isEqual = this.Equal(expected, actual);

    Console.WriteLine("Result of Data Set Identity Comparison is {0}" , isEqual);

    }
    }

    //
    //Hilfsmethoden
    //

    //Zwei SensorData-Objekte vergleichen
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }


      
    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Eine Instanz der AvroSample-Klasse erstellen und Methoden aufrufen,
    //die unterschiedliche Serialisierungsansätze veranschaulichen
    AvroSample Sample = new AvroSample();

    //Serialisierung in Speicher mithilfe von Reflection
    Sample.SerializeDeserializeObjectUsingReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // Das Beispiel soll folgende Ausgabe anzeigen: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

Serialisierung mithilfe von Avro-Objektcontainern
-------------------------------------------------

Die .NET-Klassen, welche die Daten darstellen, sind u. U. nicht immer verfügbar. Das Schema der Daten kann dynamisch sein, z. B. wenn die Daten als CSV-Dateien mit unbekanntem in einem erweiterten Schema dargestellt werden und sie in das Avro-Format transformiert werden müssen. In solchen Fällen muss eine Instanz der speziellen Klasse (AvroRecord) mit explizit festgelegtem JSON-Schema erstellt werden, die Felder müssen mit den erforderlichen Daten gefüllt werden, und die Daten müssen serialisiert werden. Das folgende Beispiel zeigt, wie ein Schema definiert, der entsprechende Datensatz erstellt und mit den Daten gefüllt wird und wie die Daten anschließend serialisiert und deserialisiert werden. Abschließend werden die ursprünglichen und die deserialisierten Objekt auf Identität überprüft.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Diese Klasse enthält alle Methoden, die die
    //Verwendung der Microsoft Avro-Bibliothek demonstrieren.
    public class AvroSample
    {

    //Beispieldatensatz mit Generic Record serialisieren und deserialisieren.
    //Generic Record ist eine spezielle Klasse, deren Schema in JSON explizit definiert wird.
    //Alle serialisierten Daten müssen den Feldern von Generic Record zugeordnet werden,
    //das daraufhin wiederum serialisiert wird.
    public void SerializeDeserializeObjectUsingGenericRecords()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Schema in JSON definieren
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

    //Basierend auf dem Schema einen generischen Serialisierer erstellen
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Puffer für Speicherstream erstellen
    using (var stream = new MemoryStream())
    {
    //Generischen Datensatz zur Darstellung der Daten erstellen
    dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location.Floor = 1;
    location.Room = 243;

    dynamic expected = new AvroRecord(serializer.WriterSchema);
    expected.Location = location;
    expected.Value = new byte[] { 1, 2, 3, 4, 5 };

    Console.WriteLine("Serializing Sample Data Set...");

    //Die Daten serialisieren
    serializer.Serialize(stream, expected);

    stream.Seek(0, SeekOrigin.Begin);

    Console.WriteLine("Deserializing Sample Data Set...");

    //Die Daten in einen generischen Datensatz deserialisieren
    dynamic actual = serializer.Deserialize(stream);

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Die Ergebnisse abschließend überprüfen
    bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
    isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
    isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
    }
    }

    static void Main()
    {

    string sectionDivider = "---------------------------------------- ";

    //Eine Instanz der AvroSample-Klasse erstellen und Methoden aufrufen,
    //die unterschiedliche Serialisierungsansätze veranschaulichen
    AvroSample Sample = new AvroSample();

    //Serialisierung in Speicher mithilfe von Generic Record
    Sample.SerializeDeserializeObjectUsingGenericRecords();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // Das Beispiel soll folgende Ausgabe anzeigen: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

Serialisierung mithilfe von Objektcontainerdateien und Serialisierung mit Reflection
------------------------------------------------------------------------------------

Dieses Beispiel hat Ähnlichkeit mit Beispiel 1: Eine Klasse und eine Struktur werden serialisiert und in einer Objektcontainerdatei gespeichert, die sich im aktuellen Verzeichnis der Beispielanwendung befindet. Anschließend wird diese Datei gelesen, die Daten werden deserialisiert und schließlich mit den Ausgangsinstanzen verglichen, um die Identität der Daten sicherzustellen. Die Daten in der Objektcontainerdatei werden mit dem Komprimierungscodec deflate (Link?) komprimiert.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

    //In Serialisierungsbeispielen verwendete Beispielklasse
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //In Serialisierungsbeispielen verwendete Beispielstruktur
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }

    [DataMember]
    public int Room { get; set; }
    }

    //Diese Klasse enthält alle Methoden, die die
    //Verwendung der Microsoft Avro-Bibliothek demonstrieren.
    public class AvroSample
    {

    //Serialisiert und deserialisiert den Beispieldatensatz mit Reflection und Avro-Objektcontainerdateien
    //Serialisierte Daten werden mit deflate-Codec komprimiert
    public void SerializeDeserializeUsingObjectContainersReflection()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

    //Pfad der Avro-Objektcontainerdatei
    string path = "AvroSampleReflectionDeflate.avro";

    //Datensatz mit Beispielklasse und -struktur erstellen
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Daten werden serialisiert und in Datei gespeichert
    //Puffer für Speicherstream wird erstellt
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //SequentialWriter-Instanz für SensorData-Typ erstellen, die eine Sequenz von SensorData-Objekten als Stream serialisieren kann
    //Daten werden mit dem deflate-Code komprimiert
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Daten werden mit dem sequenziellen Schreibvorgang als Stream serialisiert
    testData.ForEach(writer.Write);
    }
    }

    //Stream als Datei speichern
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Daten werden gelesen und deserialisiert
    //Puffer für Speicherstream wird erstellt
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Daten werden aus Objektcontainerdatei gelesen
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Stream zum Deserialisieren der Daten vorbereiten
    buffer.Seek(0, SeekOrigin.Begin);

    //SequentialReader für SensorData-Typ erstellen, der alle serialisierten Objekte des gegebenen Streams deserialisiert
    //Er ermöglicht das erneute Durchlaufen der deserialisierten Objekte, da er die IEnumerable<T>-Schnittstelle implementiert
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;

    //Zum Schluss überprüfen, ob die deserialisierten Daten den ursprünglichen entsprechen
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

    //Datei löschen
    RemoveFile(path);
    }

    //
    //Hilfsmethoden
    //

    //Zwei SensorData-Objekte vergleichen
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Speicherstream mit dem angegebenen Pfad in einer neuen Datei speichern
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

    //Dateiinhalt mit angegebenem Pfad in einen Speicherstream lesen
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

    //Datei mit angegebenem Pfad löschen
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

    //Eine Instanz der AvroSample-Klasse erstellen und Methoden aufrufen,
    //die unterschiedliche Serialisierungsansätze veranschaulichen
    AvroSample Sample = new AvroSample();

    //Serialisierung mithilfe von Reflection in Avro-Objektcontainerdatei
    Sample.SerializeDeserializeUsingObjectContainersReflection();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // Das Beispiel soll folgende Ausgabe anzeigen:
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

Serialisierung mithilfe von Objektcontainerdateien und Serialisierung mit Generic Record
----------------------------------------------------------------------------------------

Dieses Beispiel hat Ähnlichkeit mit Beispiel 2: ein Beispieldatensatz wird mit AvroRecord mit explizit definiertem JSON-Schema serialisiert. Die serialisierten Daten werden in einer neuen Objektcontainerdatei gespeichert, die sich im aktuellen Verzeichnis der Beispielanwendung befindet. Anschließend wird diese Datei gelesen, die Daten werden deserialisiert und schließlich mit den Ausgangsinstanzen verglichen, um die Identität der Daten sicherzustellen.

Die Daten in der Objektcontainerdatei werden nicht komprimiert (Komprimierungscodec Null wird verwendet).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //Diese Klasse enthält alle Methoden, die die
    //Verwendung der Microsoft Avro-Bibliothek demonstrieren.
    public class AvroSample
    {

    //Serialisiert und deserialisiert den Beispieldatensatz mit Generic Record und Avro-Objektcontainerdateien
    //Serialisierte Daten werden nicht komprimiert
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

    //Pfad der Avro-Objektcontainerdatei
    string path = "AvroSampleGenericRecordNullCodec.avro";

    Console.WriteLine("Defining the Schema and creating Sample Data Set...");

    //Schema in JSON definieren
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

    //Basierend auf dem Schema einen generischen Serialisierer erstellen
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;

    //Generischen Datensatz zur Darstellung der Daten erstellen
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

    //Daten werden serialisiert und in Datei gespeichert
    //Speicherstream-Puffer erstellen
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //SequentialWriter-Instanz für SensorData-Typ erstellen, die eine Sequenz von SensorData-Objekten als Stream serialisieren kann
    //Daten werden nicht komprimiert (Komprimierungscodec Null)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Daten werden mit dem sequenziellen Schreibvorgang als Stream serialisiert
    testData.ForEach(streamWriter.Write);
    }
    }

    Console.WriteLine("Saving serialized data to file...");

    //Stream als Datei speichern
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Daten werden gelesen und deserialisiert
    //Puffer für Speicherstream erstellen
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Daten werden aus Objektcontainerdatei gelesen
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Stream zum Deserialisieren der Daten vorbereiten
    buffer.Seek(0, SeekOrigin.Begin);

    //SequentialReader für SensorData-Typ erstellen, der alle serialisierten Objekte des gegebenen Streams deserialisiert
    //Er ermöglicht das erneute Durchlaufen der deserialisierten Objekte, da er die IEnumerable<T>-Schnittstelle implementiert
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;

    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

    //Die Ergebnisse abschließend überprüfen
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

    //Datei löschen
    RemoveFile(path);
    }

    //
    //Hilfsmethoden
    //

    //Speicherstream mit dem angegebenen Pfad in einer neuen Datei speichern
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

    //Dateiinhalt mit angegebenem Pfad in einen Speicherstream lesen
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

    //Datei mit angegebenem Pfad löschen
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

    //Eine Instanz der AvroSample-Klasse erstellen und Methoden aufrufen,
    //die unterschiedliche Serialisierungsansätze veranschaulichen
    AvroSample Sample = new AvroSample();

    //Serialisierung mithilfe von Generic Record in Avro-Objektcontainerdatei
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // Das Beispiel soll folgende Ausgabe anzeigen:
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

Serialisierung mithilfe von Objektcontainerdateien mit benutzerdefiniertem Komprimierungscodec
----------------------------------------------------------------------------------------------

Die [Avro-Spezifikation](http://avro.apache.org/docs/current/spec.html#Required+Codecs) ermöglicht die Verwendung optionaler Komprimierungscodecs (zusätzlich zu Null und Deflate). Im folgenden Beispiel wird gezeigt, wie ein benutzerdefinierter Komprmierungscodec für Objektcontainerdateien verwendet wird. Im Beispiel wird kein "grundsätzlich" anderer Codec implementiert (wie z. B. Snappy, der in der Avro-Spezifikation als unterstützter optionaler Codec genannt wird). Vielmehr wird die [Deflate](http://msdn.microsoft.com/de-de/library/system.io.compression.deflatestream(v=vs.110).aspx)-Implementierung von .NET Framework 4.5 (mit bessserem auf zlib beruhenden Komprimierungsalgorithmus) verwendet, den viele Entwickler eventuell für vorteilhaft halten.

    // Zur Kompilierung dieses Codes muss der Parameter Target Framework auf ".NET Framework 4.5" festgelegt werden,
    // damit die gewünschte Implementierung des Deflate-Komprimierungsalgorithmus verwendet wird.
    // Stellen Sie sicher, dass das C#-Projekt entsprechend eingerichtet ist.
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
    //In Serialisierungsbeispielen verwendete Beispielklasse
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }

    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }

    //In Serialisierungsbeispielen verwendete Beispielstruktur
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
    //Avro.NET Codec-Klasse enthält zwei Methoden 
    //GetCompressedStreamOver(Stream unkomprimiert) und GetDecompressedStreamOver(Stream komprimiert),
    //die für die Datenkomprimierung entscheidend sind.
    //Damit ein benutzerdefinierter Codec verwendet werden kann, müssen diese Methoden für den gewünschten Codec implementiert werden.

    #region Defining Compression and Decompression Streams
    //DeflateStream (Klasse aus dem Namespace System.IO.Compression, der den Deflate-Algorithmus implementiert)
    //kann für Avro nicht direkt verwendet werden, da es wichtige Operationen wie Seek nicht unterstützt.
    //Daher müssen zwei aus dem Stream vererbte Klassen (eine für den
    //komprimierten und eine für den dekomprimierten Stream) implementiert werden,
    //welche die Deflate-Komprimierung verwenden und alle erforderlichen Features implementieren 
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
    //Eigentliche Codec-Klasse definieren, welche die erforderlichen Methoden zum Bearbeiten von Streams enthält:
    //GetCompressedStreamOver(Stream unkomprimiert) und GetDecompressedStreamOver(Stream komprimiert)
    //Codec-Klasse verwendet oben definierte Klassen für komprimierten und dekomprimierten Stream
    internal sealed class DeflateCodec45 : Codec
    {

    //Wir verwenden lediglich eine andere IMPLEMENTIERUNG von Deflate, daher bleibt der CodecName "deflate"
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
    //In Reader zu verwendende modifizierte Codec Factory definieren
    //Sie fängt den Versuch ab, "deflate" zu verwenden, und stellt benutzerdefinierten Codec bereit 
    //In allen übrigen Fällen beruht sie auf der Basisklasse (CodecFactory)
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
    //Diese Klasse enthält Methoden, die die
    //Verwendung der Microsoft Avro-Bibliothek demonstrieren.
    public class AvroSample
    {

    //Serialisiert und deserialisiert den Beispieldatensatz mit Reflection und Avro-Objektcontainerdateien
    //Serialisierte Daten werden mit dem benutzerdefinierten Komprimierungscodec komprimiert (Deflate von .NET Framework 4.5)
    //
    //Dieses Beispiel verwendet Speicherstream für alle Operationen, die mit Serialisierung, Deserialisierung und
    //Objektcontainer-Bearbeitung verknüpft sind, obwohl Dateistream ohne weiteres verwendet werden könnte.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {

    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

    //Pfad der Avro-Objektcontainerdatei
    string path = "AvroSampleReflectionDeflate45.avro";

    //Datensatz mit Beispielklasse und -struktur erstellen
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };

    //Daten werden serialisiert und in Datei gespeichert
    //Puffer für Speicherstream wird erstellt
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");

    //SequentialWriter-Instanz für SensorData-Typ erstellen, die eine Sequenz von SensorData-Objekten als Stream serialisieren kann
    //Hier wird der benutzerdefinierte Codec eingeführt. In der nächsten kommentierten Codezeile wird gezeigt, wie der integrierte Deflate-Codec verwendet wird.
    //Da im Beispiel unterschiedliche IMPLEMENTIERUNGEN von Deflate eingesetzt werden, können die integrierten und benutzerdefinierten Codecs
    //in Lese-Schreib-Operationen gegeneinander ausgetauscht werden
    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Daten werden mit dem sequenziellen Schreibvorgang als Stream serialisiert
    testData.ForEach(writer.Write);
    }
    }

    //Stream als Datei speichern
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }

    //Daten werden gelesen und deserialisiert
    //Puffer für Speicherstream wird erstellt
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");

    //Daten werden aus Objektcontainerdatei gelesen
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }

    Console.WriteLine("Deserializing Sample Data Set...");

    //Stream zum Deserialisieren der Daten vorbereiten
    buffer.Seek(0, SeekOrigin.Begin);

    //Wegen der SequentialReader<T>-Konstruktorsignatur wird eine AvroSerializerSettings-Instanz benötigt,
    //wenn Codec Factory explizit angegeben wird.
    //Sie können die folgende Zeile auskommentieren, wenn Sie den integrierten Deflate-Codec verwenden möchten (siehe nächsten Kommentar)
    AvroSerializerSettings settings = new AvroSerializerSettings();

    //SequentialReader für SensorData-Typ erstellen, der alle serialisierten Objekte des gegebenen Streams deserialisiert
    //Er ermöglicht das erneute Durchlaufen der deserialisierten Objekte, da er die IEnumerable<T>-Schnittstelle implementiert
    //Hier wird der benutzerdefinierte Codec Factory eingeführt.
    //In der nächsten kommentierten Codezeile wird gezeigt, wie der integrierte Deflate-Codec verwendet wird
    //(in diesem Fall wird kein expliziter Codec Factory-Parameter benötigt).
    //Da im Beispiel unterschiedliche IMPLEMENTIERUNGEN von Deflate eingesetzt werden, können die integrierten und benutzerdefinierten Codecs
    //in Lese-Schreib-Operationen gegeneinander ausgetauscht werden
    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;

    //Zum Schluss überprüfen, ob die deserialisierten Daten den ursprünglichen entsprechen
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

    //Datei löschen
    RemoveFile(path);
    }
    #endregion

    #region Helper Methods

    //Zwei SensorData-Objekte vergleichen
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }

    //Speicherstream mit dem angegebenen Pfad in einer neuen Datei speichern
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

    //Dateiinhalt mit angegebenem Pfad in einen Speicherstream lesen
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

    //Datei mit angegebenem Pfad löschen
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

    //Eine Instanz der AvroSample-Klasse erstellen und Methoden aufrufen,
    //die unterschiedliche Serialisierungsansätze veranschaulichen
    AvroSample Sample = new AvroSample();

    //Serialisierung mithilfe von Reflection in Avro-Objektcontainerdatei unter Verwendung von benutzerdefiniertem Codec
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // Das Beispiel soll folgende Ausgabe anzeigen:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
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

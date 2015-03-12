## <a name="what-is"> </a>Was ist der Tabellenspeicherdienst?

Der Azure Tabellenspeicherdienst erlaubt die Speicherung großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher zur Annahme authentifizierter Anrufe von innerhalb und außerhalb der Azure-Cloud. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Der Tabellenspeicherdienst wird hauptsächlich für folgende Zwecke verwendet:

-   Speicherung strukturierter Daten in TB-Größe zur Verarbeitung webbasierter Anwendungen
-   Speicherung von Datensätzen, die keine komplexen Verknüpfungen, Fremdschlüssel oder gespeicherte Prozeduren erfordern und für schnellen Zugriff denormalisiert werden können
-   Schnelle Datenabfrage mithilfe eines gruppierten Index
-   Datenzugriff über das OData-Protokoll und LINQ-Abfragen mit WCF Date Service .NET-Bibliotheken

Sie können den Tabellenspeicherdienst verwenden, um sehr große Mengen strukturierter nicht relationaler Daten zu speichern und abzufragen. Ihre Tabellen werden mit dem steigenden Bedarf wachsen.

## <a name="concepts"></a>Konzepte

Der Tabellenspeicherdienst umfasst die folgenden Komponenten:

![Table1][Table1]

-   **URL-Format:** Der Code adressiert Tabellen in einem Konto mithilfe dieses Adressformats:
    http://`<storage account>`.table.core.windows.net/`<table>`     
    Über diese Adresse können Azure-Tabellen direkt mit dem OData-Protokoll adressiert werden. Weitere Informationen finden Sie unter [OData.org][]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/dn249410.aspx) erhalten Sie ausführliche Informationen über die Speicherkapazität des Kontos.

-   **Tabelle**: Eine Tabelle ist eine Sammlung von Entitäten. Tabellen erzwingen kein Schema für Entitäten. Das bedeutet, dass eine einzelne Tabelle Entitäten mit verschiedenen Eigenschaftensätzen enthalten kann. Die Anzahl von Tabellen, die ein 
	Speicherkonto enthalten kann, ist nur durch die Kapazität des Speicherkontos begrenzt.

-   **Entität**: Eine Entität ist ein Satz von Eigenschaften, der einer Datenbankzeile ähnelt. Eine Entität kann bis zu 1 MB groß sein.

-   **Eigenschaften**: Eine Eigenschaft ist ein Name-Wert-Paar. Jede Entität kann bis zu 252 Eigenschaften zur Datenspeicherung enthalten. Jede Entität weist außerdem 3 Systemeigenschaften auf, die einen Partitionsschlüssel, einen Zeilenschlüssel und einen Zeitstempel definieren. Entitäten mit demselben Partitionsschlüssel können schneller abgefragt und in atomischen Vorgängen eingesetzt/aktualisiert werden. Der Zeilenschlüssel einer Entität ist ihr eindeutiger Bezeichner innerhalb einer Partition.


  
  [Table1]: ./media/storage-java-how-to-use-table-storage/table1.png
  [OData.org]: http://www.odata.org/
<!--HONumber=42-->

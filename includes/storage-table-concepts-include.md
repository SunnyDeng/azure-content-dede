## Was ist der Tabellenspeicherdienst?

Der Azure-Tabellenspeicherdienst erlaubt die Speicherung großer Mengen von
strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher zur Annahme
authentifizierter Aufrufe von innerhalb und außerhalb der Azure-Cloud. Azure-
Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Der Tabellenspeicherdienst
wird hauptsächlich für folgende Zwecke verwendet:

-   Speicherung strukturierter Daten in TB-Größe zur Verarbeitung webbasierter
    Anwendungen
-   Speicherung von Datensätzen, für die keine komplexen Verknüpfungen, Fremdschlüssel oder
    gespeicherte Prozeduren erforderlich sind und die für den schnellen Zugriff denormalisiert werden können
-   Schnelle Datenabfrage mithilfe eines gruppierten Index
-   Datenzugriff über das OData-Protokoll und LINQ-Abfragen mit WCF
    Data Service .NET-Bibliotheken

Sie können mit dem Tabellenspeicherdienst große Mengen von
strukturierten nicht relationalen Daten speichern und abfragen, und Ihre Tabellen werden mit steigendem Bedarf
skaliert.

## Konzepte des Tabellenspeicherdiensts

Der Tabellenspeicherdienst umfasst die folgenden Komponenten:

![Table1][Table1]

-   **URL-Format:** Der Code adressiert Tabellen in einem Konto mithilfe dieses
    Adressformats:   
    http://`<storage account>`.table.core.windows.net/`<table>`  
      
    Über diese Adresse können Azure-Tabellen direkt mit dem
    OData-Protokoll adressiert werden. Weitere Informationen finden Sie unter [OData.org][]

-   **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen
    über ein Speicherkonto. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx).

-   **Tabelle**: Eine Tabelle ist eine Sammlung von Entitäten. Tabellen erzwingen kein
    Schema für Entitäten, d. h., eine einzelne Tabelle kann
    Entitäten enthalten, die unterschiedliche Eigenschaften aufweisen. Die Anzahl von Tabellen, die ein 
	Speicherkonto enthalten kann, ist nur durch die 
    Kapazität des Speicherkontos begrenzt.

-   **Entität**: Eine Entität ist ein Satz von Eigenschaften, der einer
    Datenbankzeile ähnelt. Eine Entität kann bis zu 1 MB groß sein.

-   **Eigenschaften**: Eine Eigenschaft ist ein Name-Wert-Paar. Jede Entität kann
    bis zu 252 Eigenschaften zur Datenspeicherung enthalten. Jede Entität weist außerdem
    3 Systemeigenschaften auf, die einen Partitionsschlüssel, einen Zeilenschlüssel und einen
    Zeitstempel angeben. Entitäten mit demselben Partitionsschlüssel können schneller
    abgefragt und in atomaren Operationen eingefügt/aktualisiert werden. Der Zeilenschlüssel einer Entität
    ist ihr eindeutiger Bezeichner innerhalb einer Partition.


  
  [Table1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/

<!--HONumber=49-->
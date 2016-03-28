### JsonFormat angeben

Wenn das Format auf **JsonFormat** festgelegt ist, können Sie die folgenden **optionalen** Eigenschaften im Abschnitt **Format** angeben.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| filePattern | Geben Sie das Muster der in jeder JSON-Datei gespeicherten Daten an. Zulässige Werte sind: **setOfObjects** und **arrayOfObjects**. Der **Standard**wert ist: **setOfObjects**. Weitere Informationen zu diesen Mustern finden Sie in den nachstehenden Abschnitten.| Nein |
| encodingName | Geben Sie den Codierungsnamen an. Eine Liste der gültigen Codierungsnamen finden Sie unter: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx)-Eigenschaft. Beispiel: Windows-1250 oder Shift-JIS. Der **Standard**wert ist: **UTF-8**. | Nein | 
| nestingSeparator | Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird. Der **Standard**wert ist **. (Punkt)**. | Nein | 


#### Dateimuster setOfObjects

Jede Datei enthält ein einzelnes Objekt oder mehrere durch Zeilen getrennte/verkettete Objekte. Wenn diese Option in einem Ausgabedataset ausgewählt wird, erzeugt die Kopie eine einzelne JSON-Datei mit jedem Objekt pro Zeile (durch Zeilen getrennt).

**einzelnes Objekt**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}

**durch Zeilen getrennte JSON**

	{"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
	{"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
	{"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**verkettete JSON**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}
	{
		"time": "2015-04-29T07:13:21.0220000Z",
		"callingimsi": "466922202613463",
		"callingnum1": "123436380",
		"callingnum2": "789037573",
		"switch1": "US",
		"switch2": "UK"
	}
	{
		"time": "2015-04-29T07:13:21.4370000Z",
		"callingimsi": "466923101048691",
		"callingnum1": "678901578",
		"callingnum2": "345626404",
		"switch1": "Germany",
		"switch2": "UK"
	}


#### Dateimuster arrayOfObjects 

Jede Datei enthält ein Array von Objekten.

	[
	    {
	        "time": "2015-04-29T07:12:20.9100000Z",
	        "callingimsi": "466920403025604",
	        "callingnum1": "678948008",
	        "callingnum2": "567834760",
	        "switch1": "China",
	        "switch2": "Germany"
	    },
	    {
	        "time": "2015-04-29T07:13:21.0220000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789037573",
	        "switch1": "US",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:21.4370000Z",
	        "callingimsi": "466923101048691",
	        "callingnum1": "678901578",
	        "callingnum2": "345626404",
	        "switch1": "Germany",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "789037573",
	        "callingnum2": "789044691",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789044691",
	        "switch1": "US",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:24.2120000Z",
	        "callingimsi": "466922201102759",
	        "callingnum1": "345698602",
	        "callingnum2": "789097900",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:25.6320000Z",
	        "callingimsi": "466923300236137",
	        "callingnum1": "567850552",
	        "callingnum2": "789086133",
	        "switch1": "China",
	        "switch2": "Germany"
	    }
	]

### JsonFormat-Beispiel

Wenn Sie eine JSON-Datei mit dem folgenden Inhalt haben:

	{
		"Id": 1,
		"Name": {
			"First": "John",
			"Last": "Doe"
		},
		"Tags": ["Data Factory”, "Azure"]
	}

und Sie wollen sie im folgenden Format in eine Azure SQL-Tabelle kopieren:

ID | Name.First | Name.Middle | Name.Last | Tags
--- | ---------- | ----------- | --------- | ----
1 | John | null | Doe | [„Data Factory”, „Azure“]

Das Eingabedataset vom Typ JsonFormat ist wie folgt definiert: (Teildefinition ausschließlich mit den relevanten Teilen)

	"properties": {
		"structure": [
			{"name": "Id", "type": "Int"},
			{"name": "Name.First", "type": "String"},
			{"name": "Name.Middle", "type": "String"},
			{"name": "Name.Last", "type": "String"},
			{"name": "Tags", "type": "string"}
		],
		"typeProperties":
		{
			"folderPath": "mycontainer/myfolder",
			"format":
			{
				"type": "JsonFormat",
				"filePattern": "setOfObjects",
				"encodingName": "UTF-8",
				"nestingSeparator": "."
			}
		}
	}

Wenn die Struktur nicht definiert ist, vereinfacht die Kopieraktivität die Struktur standardmäßig und kopiert alles.

#### Unterstützte JSON-Struktur
Beachten Sie Folgendes:

- Jedes Objekt mit einer Sammlung von Name/Wert-Paaren wird einer Zeile mit Daten in einem Tabellenformat zugeordnet. Objekte können geschachtelt werden, und Sie können definieren, wie die Struktur in einem Dataset mit dem Schachtelungstrennzeichen (.) standardmäßig vereinfacht werden soll. Ein Beispiel finden Sie im Abschnitt [JsonFormat-Beispiel](#jsonformat-example) weiter oben.  
- Wenn die Struktur im Data Factory-Dataset definiert ist, erkennt die Kopieraktivität das Schema des ersten Objekts und vereinfacht das gesamte Objekt. 
- Wenn die JSON-Eingabe ein Array hat, konvertiert die Kopieraktivität den gesamten Array-Wert in eine Zeichenfolge. Sie können dies überspringen, indem Sie [Spaltenzuordnung oder -filterung](#column-mapping-with-translator-rules) verwenden.
- Wenn auf derselben Ebene doppelte Namen vorkommen, wählt die Kopieraktivität den letzten aus.
- Bei Eigenschaftennamen wird zwischen Groß- und Kleinschreibung unterschieden. Zwei Eigenschaften mit demselben Namen, aber unterschiedlicher Schreibweise, werden als zwei getrennte Eigenschaften behandelt. 

<!---HONumber=AcomDC_0316_2016-->
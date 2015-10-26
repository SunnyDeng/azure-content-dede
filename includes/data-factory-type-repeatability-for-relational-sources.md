## Wiederholbarkeit beim Kopieren

Beim Kopieren von Daten aus und in relationale Speicher müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden.

**Hinweis:** Ein Slice kann in Azure Data Factory automatisch gemäß der angegebenen Wiederholungsrichtlinie wiederholt werden. Es wird empfohlen, eine Wiederholungsrichtlinie zum Schutz vor vorübergehenden Fehlern festzulegen. Wiederholbarkeit ist ein wichtiger Aspekt, den Sie beim Verschieben von Daten berücksichtigen sollten.

**Als Quelle:**

In den meisten Fällen möchten Sie beim Lesen aus relationalen Speichern nur die Daten lesen, die dem Slice entsprechen. Sie können dies beispielsweise erreichen, indem Sie die Variablen "WindowStart" und "WindowEnd" verwenden, die in Azure Data Factory verfügbar sind. Im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md) erfahren Sie mehr über die Variablen und Funktionen in Azure Data Factory. Beispiel:
	
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
	  },

Mit der obigen Abfrage werden Daten aus "MyTable" gelesen, die zum Dauerbereich für den Slice gehören. Bei einer erneuten Ausführung dieses Slices wäre dieses Verhalten auch immer sichergestellt.

In anderen Fällen möchten Sie möglicherweise die gesamte Tabelle lesen (beispielsweise bei einer einmaligen Verschiebung) und könnten "sqlReaderQuery" wie folgt definieren:

	
	"source": {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	          },
	

<!---HONumber=Oct15_HO3-->
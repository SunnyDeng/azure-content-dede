<properties
	pageTitle="Benutzerdefinierte Analysemodule (Azure Search-REST-API Version 2015-02-28-Preview) | Microsoft Azure"
	description="Benutzerdefinierte Analysemodule (Azure Search-REST-API Version 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# Analysemodule (Azure Search-REST-API Version 2015-02-28-Preview)

> [AZURE.NOTE] Die Unterstützung für benutzerdefinierte Analysemodule befindet sich derzeit in der Vorschauphase. Sie müssen die Version „2015-02-28-Preview“ der REST-API für den Azure Search-Dienst verwenden, um dieses Feature zu nutzen. Beachten Sie, dass Vorschaufeatures derzeit nicht Teil des .NET SDK sind. Die Verwendung der REST-API ist momentan also Ihre einzige Programmieroption.

## Übersicht

Einfach ausgedrückt besteht die Rolle eines Moduls für die Volltextsuche darin, Dokumente so zu verarbeiten und zu speichern, dass sie effizient abgefragt und abgerufen werden können. In erster Linie geht es um das Extrahieren wichtiger Wörter aus Dokumenten, das Einfügen der Wörter in einen Index und das anschließende Verwenden des Index zum Suchen nach Dokumenten, die mit Wörtern einer bestimmten Abfrage übereinstimmen. Der Prozess zur Extrahierung von Wörtern aus Dokumenten und Suchabfragen wird als lexikalische Analyse bezeichnet. Komponenten, die eine lexikalische Analyse durchführen, werden als Analysemodule (Analyzers) bezeichnet. In Azure Search können Sie aus einem Satz von [vordefinierten sprachunabhängigen Analysemodulen](#Analyzers) und [sprachspezifischen Analysemodulen](https://msdn.microsoft.com/library/azure/dn879793.aspx) wählen. Sie haben auch die Möglichkeit, Ihre eigenen benutzerdefinierten Analysemodule zu definieren. Mit einem benutzerdefinierten Analysemodul können Sie die Kontrolle über den Prozess der Konvertierung von Text in indizierbare/durchsuchbare Token übernehmen. Es handelt sich um eine benutzerdefinierte Konfiguration, die aus einem einzelnen vordefinierten Tokenizer und einem oder mehreren Tokenfiltern besteht. Der Tokenizer ist für das Aufteilen von Text in Token verantwortlich, und mit den Tokenfiltern werden die Token geändert, die vom Tokenizer ausgegeben werden.

Beispiele für häufige Szenarien, die mit benutzerdefinierten Analysemodulen ermöglicht werden:

- Phonetische Suche: Es wird ein phonetischer Filter hinzugefügt, um Suchen basierend auf der Aussprache eines Worts durchführen zu können, anstatt anhand der Schreibweise.
- Deaktivieren der lexikalischen Analyse: Verwenden Sie die Schlüsselwortanalyse, um durchsuchbare Felder zu erstellen, die nicht analysiert werden.
- Schnelle Präfix-/Suffixsuche: Fügen Sie den „Edge N-gram“-Tokenfilter hinzu, um Präfixe von Wörtern zu indizieren und so den schnellen Abgleich von Präfixen zu ermöglichen. Kombinieren Sie dies mit dem Reverse-Tokenfilter, um den Suffixabgleich durchzuführen.
- Benutzerdefinierte Tokenisierung: Verwenden Sie beispielsweise den Whitespace-Tokenizer, um Sätze mit Leerzeichen als Trennzeichen in Token zu unterteilen.
- ASCII-Faltung: Fügen Sie den Filter für die ASCII-Faltung hinzu, um diakritische Zeichen wie „ö“ oder „ê“ in Suchbegriffen zu normalisieren.

Sie können mehrere benutzerdefinierte Analysemodule definieren, um die Kombination aus Filtern zu variieren. Für jedes Feld können aber nur ein Analysemodul für die Indizierungsanalyse und ein Analysemodul für die Suchanalyse verwendet werden.
 
Diese Seite enthält eine Liste mit unterstützten Analysemodulen, Tokenizern und Tokenfiltern. Außerdem finden Sie hier eine Beschreibung von Änderungen an der Indexdefinition mit einem Verwendungsbeispiel. Eine Einführung und Untersuchung von Szenarien finden Sie unter [Benutzerdefinierte Analysemodule in Azure Search](link). Weitere Hintergrundinformationen zur zugrunde liegenden Technologie, die für die Implementierung von Azure Search genutzt wird, finden Sie unter [Analysis package summary (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html) (Zusammenfassung des Analysemodulpakets (Lucene)).


## Standardanalysemodul

Das Standardanalysemodul ist das Analysemodul [Apache Lucene Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html).

## Validierungsregeln

Die Namen von benutzerdefinierten Analysemodulen, Tokenizern und Tokenfiltern müssen eindeutig sein und dürfen nicht den Namen von vordefinierten Analysemodulen, Tokenizern oder Tokenfiltern entsprechen.
 

## Indexdefinition mit benutzerdefinierten Analysemodulen 

Sie definieren benutzerdefinierte Analysemodule während der Erstellung des Index. Die Syntax der benutzerdefinierten Analysekomponenten in der Indexdefinition ist unten dargestellt. Ein vollständiges Beispiel finden Sie [hier](#Example).

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] Benutzerdefinierte Analysemodule, die Sie erstellen, werden im Azure-Portal nicht verfügbar gemacht. Die einzige Möglichkeit, ein benutzerdefiniertes Analysemodul hinzuzufügen, ist die Verwendung von Code, mit dem beim Definieren eines Index Aufrufe an die REST-API durchgeführt werden.

##Indexattribute für benutzerdefinierte Analysemodule, Tokenizer und Tokenfilter 

In diesem Abschnitt sind die Konfigurationseigenschaften für den Abschnitt mit den Analysemodulen, Tokenizern und Tokenfiltern einer Indexdefinition angegeben.

###Analysemodule

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Der Name darf nur Buchstaben, Zahlen, Leerzeichen, Bindestriche oder Unterstriche enthalten, nur mit alphanumerischen Zeichen beginnen und enden und ist auf 128&#160;Zeichen beschränkt.
      </td>
    </tr>
    <tr>
      <td>
        <b>Typ</b>
      </td>
      <td>
		Gültige Werte sind entweder „#Microsoft.Azure.Search.CustomAnalyzer“ oder ein Analysemodulname aus der Liste mit den unterstützten Analysemodulen. Siehe Spalten <b>analyzer_type</b> in der Tabelle [Analysemodule](#Analysemodule) weiter unten.
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (nur gültig für #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Erforderlich. Dies muss einer der vordefinierten Tokenizer sein, die unten in der Tabelle [Tokenizer](#Tokenizer) aufgeführt sind, oder einer der benutzerdefinierten Tokenizer, die in der Indexdefinition definiert sind.
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenfilter</b> (nur gültig für #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Bei allen Tokenfiltern handelt es sich entweder um einen der vordefinierten Tokenfilter, die in der Tabelle [Tokenfilter](#Tokenfilter) weiter unten aufgeführt sind, oder um einen der benutzerdefinierten Tokenfilter, die in der Indexdefinition definiert sind.
      </td>
	</tr>
    <tr>
      <td>
        <b>Optionen</b>
      </td>
      <td>
		Dies müssen [gültige Optionen](#Analysemodule) eines vordefinierten (nicht benutzerdefinierten) Analysemoduls sein.
      </td>
	</tr>
  </tbody>
</table>

###Tokenizer

Ein Tokenizer unterteilt fortlaufenden Text in eine Sequenz aus Token, z. B. beim Aufteilen eines Satzes in Wörter. Sie können genau einen Tokenizer pro benutzerdefiniertem Analysemodul angeben. Wenn Sie mehr als einen Tokenizer benötigen, können Sie mehrere benutzerdefinierte Analysemodule erstellen und diese Feld für Feld in Ihrem Indexschema zuweisen. Für ein benutzerdefiniertes Analysemodul kann ein vordefinierter Tokenizer mit standardmäßigen oder benutzerdefinierten Optionen verwendet werden.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Der Name darf nur Buchstaben, Zahlen, Leerzeichen, Bindestriche oder Unterstriche enthalten, nur mit alphanumerischen Zeichen beginnen und enden und ist auf 128&#160;Zeichen beschränkt.
      </td>
    </tr>
    <tr>
      <td>
        <b>Typ</b>
      </td>
      <td>
		Der Tokenizername aus der Liste mit den unterstützten Tokenizern. Siehe Spalte <b>tokenizer_type</b> in der Tabelle [Tokenizer](#Tokenizer) unten.
      </td>
	</tr>
     <tr>
      <td>
        <b>Optionen</b>
      </td>
      <td>
		Müssen [gültige Optionen](#Tokenizer) eines bestimmten Tokenizertyps sein.
      </td>
	</tr>
  </tbody>
</table>

###Tokenfilter

Ein Tokenfilter wird verwendet, um die von einem Tokenizer generierten Token herauszufiltern oder zu ändern. Sie können beispielsweise einen Filter für Kleinbuchstaben angeben, mit dem alle Zeichen in Kleinbuchstaben konvertiert werden. Sie können in einem benutzerdefinierten Analysemodul mehrere Tokenfilter verwenden. Tokenfilter werden in der Reihenfolge ausgeführt, in der sie aufgeführt sind.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Der Name darf nur Buchstaben, Zahlen, Leerzeichen, Bindestriche oder Unterstriche enthalten, nur mit alphanumerischen Zeichen beginnen und enden und ist auf 128&#160;Zeichen beschränkt.
      </td>
    </tr>
    <tr>
      <td>
        <b>Typ</b>
      </td>
      <td>
		Der Tokenfiltername aus der Liste mit den unterstützten Tokenfiltern. Siehe Spalte <b>token_filter_type</b> in der Tabelle [Tokenfilter](#Tokenfilter) unten.
      </td>
	</tr>
     <tr>
      <td>
        <b>Optionen</b>
      </td>
      <td>
		Die müssen [gültige Optionen](#Tokenfilter) eines bestimmten Tokenfiltertyps sein.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##Beispiel für eine Indexdefinition

Dieses Beispiel für eine Indexdefinition enthält ein Feld, für das das benutzerdefinierte Analysemodul „my\_analyzer“ verwendet wird. Hierfür werden wiederum der angepasste Standardtokenizer „my\_standard\_tokenizer“ und zwei Tokenfilter verwendet: ein Kleinbuchstabenfilter (lowercase) und der angepasste Filter „my\_asciifolding“ für die ASCII-Faltung.

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##Analysemodule

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>Beschreibung</td>
		<td>Optionen</td>
	</tr>
	</thead>
    <tr>
      <td>[Schlüsselwort](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>Behandelt den gesamten Inhalt eines Felds wie ein einzelnes Token. Dies ist nützlich für Daten wie Postleitzahlen, IDs und einige Produktnamen.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[Muster](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>Trennt Text anhand eines Musters mit regulären Ausdrücken auf flexible Weise in Begriffe.</td>
	  <td>
		- lowercase - type: bool - Wenn Kleinbuchstaben gewünscht sind, standardmäßig „true“ - [Muster](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - type: string - Muster mit regulären Ausdrücken für Abgleich von Tokentrennzeichen, Standard: \w+ - [Flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - type: string - Flags für reguläre Ausdrücke, Standard: leere Zeichenfolge - stopwords - type: string array - Liste mit Stoppwörtern, Standard: leere Liste
	  </td>
    </tr>
    <tr>
      <td>[Einfach](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>Trennt Text an Stellen ohne Buchstaben und konvertiert in Kleinbuchstaben.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[Snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>Ein Standardanalysemodul mit [Snowball-Filter für Wortstammerkennung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		- language - type: string - Zulässige Werte: danish, dutch, english, finnish, french, german, hungarian, italian, norwegian, portuguese, russian, spanish, swedish - stopwords - type: string array - Liste mit Stoppwörtern, Standard: leere Liste
	  </td>
    </tr>
    <tr>
      <td>[Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>Lucene-Standardanalysemodul – Besteht aus Standardtokenizer, Kleinbuchstabenfilter und Stoppfilter</td>
	  <td>
		- maxTokenLength - type: int - Maximale Tokenlänge, Standard: 255. Token, die die maximale Länge überschreiten, werden geteilt. - stopwords - type: string array - Liste mit Stoppwörtern, Standard: leere Liste
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.Lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Standardanalysemodul mit Filter für ASCII-Faltung</td>
	  <td></td>
    </tr>
    <tr>
      <td>[Stopp](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>Trennt Text an Stellen ohne Buchstaben und wendet die Filter für Kleinbuchstaben und Stoppwörter an.</td>
	  <td>
		- stopwords - type: string array - Liste mit Stoppwörtern, Standard: leere Liste
	  </td>
    </tr>
    <tr>
      <td>[Leerzeichen](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Ein Analysemodul, für das der Tokenizer für Leerzeichen verwendet wird.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Tokenizer

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>Beschreibung</td>
		<td>Optionen</td>
	</tr>
	</thead>
    <tr>
      <td>[Klassisch](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>Auf Grammatik basierender Tokenizer, der zur Verarbeitung von Dokumenten in den meisten europäischen Sprachen geeignet ist.</td>
	  <td>
		- maxTokenLength - type: int - Maximale Tokenlänge, Standard: 255. Token, die die maximale Länge überschreiten, werden geteilt. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>Tokenisiert die Edge-Eingabe in N-Gramme der angegebenen Größe(n).</td>
	  <td>
		- minGram - type: int - Standard: 1 - maxGram - type: int - Standard: 2 - tokenChars - type: string array - Buchstabenklassen, die in den Token beibehalten werden, zulässige Werte: letter, digit, whitespace, punctuation, symbol
	  </td>	  
    </tr>
    <tr>
      <td>[Schlüsselwort](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>Gibt die gesamte Eingabe als einzelnes Token aus.</td>
	  <td>
		- bufferSize - type: int - Lesepuffergröße, Standard: 256
	  </td>
    </tr>
    <tr>
      <td>[Buchstabe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>Trennt Text an Stellen ohne Buchstaben.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[Kleinbuchstaben](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>Trennt Text an Stellen ohne Buchstaben und konvertiert in Kleinbuchstaben.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>Tokenisiert die Eingabe in N-Gramme der angegebenen Größe(n).</td>
	  <td>
		- minGram - type: int - Standard: 1 - maxGram - type: int - Standard: 2 - tokenChars - type: string array - Buchstabenklassen, die in den Token beibehalten werden, zulässige Werte: letter, digit, whitespace, punctuation, symbol
	  </td>
    </tr>
    <tr>
      <td>[Pfadhierarchie](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>Dies ist ein Tokenizer für pfadähnliche Hierarchien.</td>
	  <td>
		- delimiter - type: string - Standard: „/“ - replacement - type: string - Ersetzt das Trennzeichen (falls festgelegt), Standard: delimiter - bufferSize - type: int - Standard: 1024 - reverse - type: bool - Generiert Token in umgekehrter Reihenfolge (falls „true“), Standard: true - skip - type: bool - Zu überspringende Anfangstoken, Standard: 0
	  </td>
    </tr>
    <tr>
      <td>[Muster](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>Bei diesem Tokenizer wird ein Musterabgleich mit regulären Ausdrücken zum Erstellen unterschiedlicher Token verwendet.</td>
	  <td>
		 - pattern - type: string - Muster mit regulären Ausdrücken, Standard: \w+ - [Flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - type: string - Flags mit regulären Ausdrücken, Standard: leere Zeichenfolge - group - type: int - In Token zu extrahierende Gruppe, Standard: -1 (split)
	  </td>
    </tr>
    <tr>
      <td>[Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>Teilt den Text gemäß den [Unicode-Textsegmentierungsregeln](http://unicode.org/reports/tr29/) auf.</td>
	  <td>
		 - maxTokenLength - type: int - Maximale Tokenlänge, Standard: 255. Token, die die maximale Länge überschreiten, werden geteilt. 
	  </td>
    </tr>
    <tr>
      <td>[UAX-URL-E-Mail](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>Tokenisiert URLs und E-Mails als ein Token.</td>
	  <td>
		- maxTokenLength - type: int - Maximale Tokenlänge, Standard: 255. Token, die die maximale Länge überschreiten, werden geteilt.
	  </td>
    </tr>
    <tr>
      <td>[Leerzeichen](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>Teilt Text bei Leerzeichen auf.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##Tokenfilter

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>Beschreibung</td>
		<td>Optionen</td>
	</tr>
	</thead>
    <tr>
      <td>[Arabisch-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>Entfernt alle Zeichen nach dem Apostroph (einschließlich des Apostrophs)</td>
    </tr>
    <tr>
      <td>[Apostroph](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>Entfernt alle Zeichen nach einem Apostroph.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[ASCII-Faltung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>Konvertiert alphabetische, numerische und symbolische Unicode-Zeichen, die nicht unter den ersten 127&#160;ASCII-Zeichen sind (Unicode-Block „Standardlateinisch“), in ihre ASCII-Äquivalente, falls vorhanden.</td>
	  <td>
		- preserveOriginal - type: bool - Bei „true“ wird das ursprüngliche Token beibehalten, Standard: false
	  </td>	  
    </tr>
    <tr>
      <td>[CJK-Bigramm](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>Bildet Bigramme aus CJK-Begriffen, die von StandardTokenizer generiert werden.</td>
	  <td>
		- ignoreScripts - type: bool - Zu ignorierende Skripts, zulässige Werte: han, hiragana, katakana, hangul. Standard: leere Liste - outputUnigrams type: bool - Auf „true“ festlegen, wenn immer sowohl Unigramme als auch Bigramme ausgegeben werden sollen, Standard: false
	  </td>	  
    </tr>
    <tr>
      <td>[CJK-Breite](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>Normalisiert CJK-Breitenunterschiede. Faltet ASCII-Varianten mit voller Breite in die Entsprechung in Standardlateinisch und Katakana-Varianten mit halber Breite in die Kana-Entsprechung.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Klassisch](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>Entfernt die englischen Possessivformen und Punkte von Akronymen.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Häufige Gramme](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>Erstellt beim Indizieren Bigramme für häufig auftretende Begriffe. Einzelne Begriffe werden weiterhin indiziert (mit überlagerten Bigrammen).</td>
	  <td>
		- commonWords - type: string array - Satz mit häufigen Wörtern, Standard: leere Liste - ignoreCase - type: bool - Bei „true“ wird Groß/Kleinschreibung beim Abgleich häufiger Wörter nicht berücksichtigt, Standard: false - queryMode - type: bool - Generiert Bigramme und entfernt dann häufige Wörter und einzelne Begriffe, auf die ein häufiges Wort folgt, Standard: false
	  </td>	  
    </tr>
    <tr>
      <td>[Trennzeichen/Nutzlast-Filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>Zeichen vor dem Trennzeichen sind das „Token“, die Zeichen danach sind die Nutzlast. Wenn das Trennzeichen beispielsweise „|“ lautet, ist für die Zeichenfolge „hello|world“ das Wort „hello“ das Token und das Wort „world“ die Nutzlast. Sie können auch einen Encoder einbinden, um die Nutzlast in geeigneter Weise zu konvertieren (von Zeichen in Byte).</td>
	  <td>
		- delimiter - type: char - Standard: „|“ - encoding - type: string - Zulässige Werte: int, float, identity. Standard: float
	  </td>	  
    </tr>
    <tr>
      <td>[Wörterbuchtrennung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>Teilt zusammengesetzte Wörter auf, die in vielen germanischen Sprachen vorkommen.</td>
	  <td>
		- wordList - type: string array - Liste mit den abzugleichenden Wörtern, Standard: leere Liste - minWordSize - type: int - Nur Wörter, die länger als diese Angabe sind, werden verarbeitet, Standard: 5 - minSubwordSize - type: int - Nur Teilwörter, die länger als diese Angabe sind, werden ausgegeben, Standard: 2 - maxSubwordSize - type: int - Nur Teilwörter, die kürzer als diese Angabe sind, werden ausgegeben, Standard: 15 - onlyLongestMatch - type: bool - Der Ausgabe wird nur das längste übereinstimmende Teilwort hinzugefügt, Standard: false 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>Generiert N-Gramme der angegebenen Größe(n), und zwar vom Anfang oder vom Ende eines Eingabetokens.</td>
	  <td>
		- minGram - type: int - Standard: 1 - maxGram - type: int - Standard: 2 - side - type: string - Gibt an, von welcher Seite der Eingabe aus das N-Gramm generiert werden soll. Zulässige Werte: front, back
	  </td>	  
    </tr>   
    <tr>
      <td>[Elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>Dient zum Entfernen von Elisionen. Beispielsweise wird „l'avion“ (das Flugzeug) in „avion“ (Flugzeug) konvertiert.</td>
	  <td>
		- articles - type: string array -Satz von zu entfernenden Artikeln, Standard: leere Liste
	  </td>	  
    </tr>
    <tr>
      <td>[Deutsch-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>Normalisiert deutsche Zeichen gemäß der Heuristik des [German2-Snowball-Algorithmus](http://snowball.tartarus.org/algorithms/german2/stemmer.html).</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Hindi-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>Normalisiert Text in Hindi, um einige Unterschiede in Bezug auf Rechtschreibvarianten zu beseitigen.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Indisch-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>Normalisiert die Unicode-Darstellung von Text in indischen Sprachen.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Beibehaltung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>Ein Tokenfilter, bei dem nur Token mit Text aus einer angegebenen Wortliste beibehalten werden.</td>
	  <td>
		- keepWords - type: string array - Liste mit beizubehaltenden Wörtern, Standard: leere Liste - keepWordsCase - type: bool - Bei „true“ werden zuerst alle Wörter in Kleinbuchstaben konvertiert, Standard: false
	  </td>	  
    </tr>
    <tr>
      <td>[Beibehaltungstypen](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>Behält Token bei, deren Typen in der angegebenen Liste mit den zulässigen Typen enthalten sind.</td>
	  <td>
		- types - type: string array - Liste mit beizubehaltenden Typen
	  </td>	  
    </tr>
    <tr>
      <td>[Schlüsselwortmarkierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>Markiert Begriffe als Schlüsselwörter.</td>
	  <td>
		- keywords - type: string array - Liste mit Wörtern, die als Schlüsselwörter markiert werden sollen, Standard: leere Liste - ignoreCase - type: bool - Bei „true“ werden zuerst alle Wörter in Kleinbuchstaben konvertiert, Standard: false
	  </td>	  
    </tr>
    <tr>
      <td>[Schlüsselwortwiederholung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>Gibt jedes eingehende Token zweimal aus: einmal als Schlüsselwort und einmal nicht als Schlüsselwort.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>Dies ist ein kstem-Hochleistungsfilter für Englisch.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Länge](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>Entfernt Wörter, die zu lang oder zu kurz sind.</td>
	  <td>
		- min - type: int - Minimale Zeichenzahl, Standard: 0 - max - type: int - Maximale Zeichenzahl, Standard: max. ganzzahliger Wert
	  </td>	  
    </tr>
    <tr>
      <td>[Limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>Begrenzt die Anzahl von Token während der Indizierung.</td>
	  <td>
		- maxTokenCount - type: int - Maximale Anzahl von zu produzierenden Token, Standard: 1 - consumeAllTokens - type: bool - Gibt an, ob auch dann alle Token der Eingabe genutzt werden müssen, wenn maxTokenCount erreicht ist, Standard: false
	  </td>	  
    </tr>
    <tr>
      <td>[Kleinbuchstaben](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>Normalisiert Tokentext zu Kleinbuchstaben.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>Generiert N-Gramme der angegebenen Größe(n).</td>
	  <td>
		- minGram - type: int - Standard: 1 - maxGram - type: int - Standard: 2
	  </td>	  
    </tr>
    <tr>
      <td>[Mustererfassung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Verwendet reguläre Java-Ausdrücke zum Ausgeben mehrerer Token: eines für jede Erfassungsgruppe in einem oder mehreren Mustern.</td>
	  <td>
		- patterns - type: string array - Liste mit Mustern für den Abgleich der einzelnen Token - preserveOriginal - type: bool - Auf „true“ festlegen, um auch dann das ursprüngliche Token zurückzugeben, wenn sich für eines der Muster eine Übereinstimmung ergibt.
	  </td>	  
    </tr>
    <tr>
      <td>[Persisch-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>Wendet die Normalisierung für Persisch an.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Phonetisch]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>Dient zum Erstellen von Token für phonetische Übereinstimmungen.</td>
	  <td>
		- encoder - type: string - Zu verwendender phonetischer Encoder, zulässige Werte: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, cologne, nysiis, koelnerphonetik, haasephonetik, beidermorse. Standard: metaphone - replace - type: bool - „true“, wenn codierte Token ursprüngliche Token ersetzen sollen, „false“, wenn diese als Synonyme hinzugefügt werden sollen, Standard: true
	  </td>	  
    </tr>
    <tr>
      <td>[Porter-Stamm](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>Transformiert den Tokendatenstrom gemäß [Porter-Algorithmus zur Wortstammerkennung](http://tartarus.org/~martin/PorterStemmer/).</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Umkehrung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>Kehrt die Tokenzeichenfolge um.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Skandinavisch-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>Normalisiert die Verwendung austauschbarer skandinavischer Zeichen.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Skandinavische Faltung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>Führt für die skandinavischen Zeichen die Faltungen åÅäæÄÆ->a und öÖøØ->o durch. Außerdem wird die Verwendung der doppelten Vokale aa, ae, ao, oe und oo beseitigt und nur der erste Vokal beibehalten.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[Shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>Erstellt Kombinationen von Token als einzelnes Token.</td>
	  <td>
		- maxShingleSize - type: int - Standard: 2 - minShingleSize - type: int - Standard: 2 - outputUnigrams - type: bool - Bei „true“ enthält der Ausgabedatenstrom die Eingabetoken (Unigramme) und „Shingles“ (N-Gramme), Standard: true - outputUnigramsIfNoShingles - type: bool - Bei „true“ wird das Verhalten von outputUnigrams außer Kraft gesetzt, und „false“ gilt für die Fälle, in denen keine „Shingles“ verfügbar sind, Standard: false - tokenSeparator - type: string - Zu verwendende Zeichenfolge beim Verknüpfen benachbarter Token zur Bildung eine Shingle-Elements, Standard: " " - filterToken - type: string - Einzufügende Zeichenfolge für jede Position, an der kein Token vorhanden ist, Standard: "_"
	  </td>	  
    </tr>
    <tr>
      <td>[Snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Snowball-Tokenfilter zur Wortstammerkennung</td>
	  <td>
		- language - type: string - Zulässige Werte: Armenian, Basque, Catalan, Danish, Dutch, English, Finnish, French, German, German2, Hungarian, Italian, Kp, Lovins, Norwegian, Porter, Portuguese, Romanian, Russian, Spanish, Swedish, Turkish
	  </td>	  
    </tr>
	<tr>
      <td>[Sorani-Normalisierung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Normalisiert die Unicode-Darstellung von Sorani-Text.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>Wortstammerkennung</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>Sprachspezifischer Filter zur Wortstammerkennung</td>
	  <td>
		- language - type: string - Zulässige Werte: - [arabic](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenian](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [basque](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brazilian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [bulgarian](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [czech](http://portal.acm.org/citation.cfm?id=1598600) - [danish](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [dutch](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [english](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finnish](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [french](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [german](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [hungarian](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [indonesian](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irish](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italian](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [norwegian](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portuguese](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [romanian](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [russian](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [spanish](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [swedish](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turkish](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[Außerkraftsetzung der Wortstammerkennung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>Alle Begriffe mit Wortstamm im Wörterbuch werden als Schlüsselwörter gekennzeichnet, damit sie im weiteren Verlauf nicht mit Modulen für die Wortstammerkennung verarbeitet werden. Muss vor Filtern für die Wortstammerkennung angeordnet werden.</td>
	  <td>
		- rules - type: string array - Regeln für die Wortstammerkennung im folgenden Format: „Wort => Stamm“, z.&#160;B. „ran => run“, Standard: leere Liste
	  </td>	  
    </tr>
	<tr>
      <td>[Stopp](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>Entfernt Stoppwörter aus einem Tokendatenstrom.</td>
	  <td>
		- stopwords - type: string array - Liste mit Stoppwörtern, Standard: leere Liste. - stopwords_list - type: string - Vordefinierte Liste mit Stoppwörtern, zulässige Wörter: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_, Standard: _english_ - ignoreCase - type: bool - Bei „true“ werden alle Wörter zuerst in Kleinbuchstaben konvertiert, Standard: false - removeTrailing - type: bool - Bei „true“ wird der letzte Suchbegriff ignoriert, wenn es ein Stoppwort ist, Standard: true
	  </td>	  
    </tr>
	<tr>
      <td>[Synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>Gleicht Synonyme aus einzelnen oder mehreren Wörtern in einem Tokendatenstrom ab.</td>
	  <td>
		- synonyms - type: string array - Liste mit Synonymen in einem der folgenden beiden Formate: - incredible, unbelievable, fabulous => amazing - Alle Begriffe auf der linken Seite des Symbols „=>“ werden durch alle Begriffe auf der rechten Seite ersetzt - incredible, unbelievable, fabulous, amazing - Kommagetrennte Liste mit gleichwertigen Wörtern. Legen Sie die Option „expand“ fest, um die Interpretation dieser Liste zu ändern. - ignoreCase - type: bool - Buchstabenfaltung der Eingabe für Abgleich, Standard: false - expand - type: bool - Bei „true“ werden alle Wörter in der Liste mit den Synonymen (wenn =>-Notation nicht verwendet wird) einander zugeordnet. Die Liste: incredible, unbelievable, fabulous, amazing ist äquivalent zu: incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing - Bei „false“ ist die folgende Liste: incredible, unbelievable, fabulous, amazing äquivalent zu: incredible, unbelievable, fabulous, amazing => incredible   
	  </td>	  
    </tr>
	<tr>
      <td>[Entfernung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>Entfernt voran- und nachgestellte Leerzeichen aus Token.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[Zuschneidung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>Schneidet die Begriffe auf eine bestimmte Länge zu.</td>
	  <td>
		- length - type: int - erforderliche Option
	  </td>	  
    </tr>
	<tr>
      <td>[Eindeutigkeit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>Filtert Token heraus, die denselben Text wie das vorherige Token aufweisen.</td>
	  <td>
		- onlyOnSamePosition - type: bool - Entfernt Duplikate nur an derselben Position (falls festgelegt), Standard: true
	  </td>	  
    </tr>
    <tr>
      <td>[Großbuchstaben](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>Normalisiert Tokentext in Großbuchstaben.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[Wortaufteilung](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>Teilt Wörter in Teilwörter auf und führt optional Transformationen für Teilwortgruppen durch.</td>
	  <td>
		- generateWordParts - type: bool - Bewirkt, dass Teile von Wörtern generiert werden, z.&#160;B. „AzureSearch“ -> „Azure“ „Search“, Standard: true - generateNumberParts - type: bool - Bewirkt, dass Teilwörter mit Zahlen generiert werden, Standard: true - catenateWords - type: bool - Bewirkt, dass maximale Ausführungen von Wortteilen verkettet werden, z.&#160;B. „Azure-Search“ -> „AzureSearch“, Standard: false - catenateNumbers - type: bool - Bewirkt, dass maximale Ausführungen von Zahlenteilen verkettet werden, z.&#160;B. „1-2“ -> „12“, Standard: false - catenateAll - type: bool - Bewirkt, dass alle Teile von Teilwörtern verkettet werden, z.&#160;B. „Azure-Search-1“ -> „AzureSearch1“, Standard: false - splitOnCaseChange - type: bool - Bei „true“ werden Wörter bei caseChange geteilt, z.&#160;B. „AzureSearch“ -> „Azure“ „Search“, Standard: true - preserveOriginal - Bewirkt, dass Originalwörter beibehalten und der Liste mit den Teilwörtern hinzugefügt werden, Standard: false - splitOnNumerics - type: bool - Bei „true“ wird bei Zahlen getrennt, z.&#160;B. „Azure1Search“ -> „Azure“ „8“ „Search“, Standard: true - stemEnglishPossessive - type: bool - Bewirkt, dass das nachgestellte „'s“ für jedes Teilwort entfernt wird, Standard: true - protectedWords - type: string array - Token, die vor der Trennung geschützt sind, Standard: leere Liste
	  </td>	  
    </tr>
  </tbody>
</table>

**Siehe auch** [REST-API für Azure Search-Dienst](http://msdn.microsoft.com/library/azure/dn798935.aspx) auf der MSDN-Website <br/> [Create Index (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) auf der MSDN-Website<br/>

<!---HONumber=AcomDC_0224_2016-->
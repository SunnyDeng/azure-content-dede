<properties
	pageTitle="Azure AD Connect-Synchronisierung: Funktionsreferenz"
	description="Referenz der Ausdrücke für die deklarative Bereitstellung in der Azure AD Connect-Synchronisierung."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect-Synchronisierung: Funktionsreferenz


In der Azure Active Directory Connect-Synchronisierung werden Funktionen verwendet, um Attributwerte während der Synchronisierung zu ändern. <br> Die Syntax der Funktionen wird im folgenden Format ausgedrückt: <br> `<output type> FunctionName(<input type> <position name>, ..)`

Wenn eine Funktion überladen ist und mehrere Syntaxen akzeptiert, werden alle gültigen Syntaxen aufgeführt.<br> Die Funktionen sind stark typisiert und überprüfen, ob der übergebene Typ dem dokumentierten Typ entspricht.<br> Wenn der Typ nicht übereinstimmt, wird ein Fehler ausgegeben.

Die Typen werden mit der folgenden Syntax ausgedrückt:

- **bin** – binär
- **Bool** – boolesch
- **dt** – UTC-Datum/-Uhrzeit
- **Enum** – Enumeration von bekannten Konstanten
- **exp** – Ausdruck, der zu einem booleschen Wert ausgewertet werden soll
- **Mvbin** – mehrwertig binär
- **Mvstr**– mehrwertiger Verweis
- **num** – numerisch
- **ref** – einwertiger Verweis
- **str** – einwertige Zeichenfolge
- **Var** – eine Variante \(fast\) jedes anderen Typs
- **void** – gibt keinen Wert zurück



## Funktionsreferenz

----------
**Konvertierung:**

[CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [CGuid](#cguid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromBase64](#convertfrombase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [CNum](#cnum) &nbsp;&nbsp;&nbsp;&nbsp; [CRef](#cref) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromGuid](#StringFromGuid) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromSid](#stringfromsid)

**Datum/Uhrzeit:**

[DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate)

**Verzeichnis**

[DNComponent](#dncomponent) &nbsp;&nbsp;&nbsp;&nbsp; [DNComponentRev](#dncomponentrev) &nbsp;&nbsp;&nbsp;&nbsp; [EscapeDNComponent](#escapedncomponent)

**Prüfung:**

[IsBitSet](#isbitset) &nbsp;&nbsp;&nbsp;&nbsp; [IsDate](#isdate) &nbsp;&nbsp;&nbsp;&nbsp; [IsEmpty](#isempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsGuid](#isguid) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsNumeric](#isnumeric) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring)

**Mathematisch:**

[BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [BitOr](#bitor) &nbsp;&nbsp;&nbsp;&nbsp; [RandomNum](#randomnum)

**Mehrwertig**

[Contains](#contains) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [ItemOrNull](#itemornull) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)

**Programmablauf:**

[Error](#error) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)


**Text**

[GUID](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [InStrRev](#instrrev) &nbsp;&nbsp;&nbsp;&nbsp; [LCase](#lcase) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Len](#len) &nbsp;&nbsp;&nbsp;&nbsp; [LTrim](#ltrim) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [PadLeft](#padleft) &nbsp;&nbsp;&nbsp;&nbsp; [PadRight](#padright) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [ReplaceChars](#replacechars) &nbsp;&nbsp;&nbsp;&nbsp; [Right](#right) &nbsp;&nbsp;&nbsp;&nbsp; [RTrim](rtrim) &nbsp;&nbsp;&nbsp;&nbsp; [Trim](#trim) &nbsp;&nbsp;&nbsp;&nbsp; [UCase](#ucase) &nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

----------
### BitAnd

**Beschreibung:**<br> Die Funktion "BitAnd" legt angegebene Bits auf einem Wert fest.
 
**Syntax:**<br> `num BitAnd(num value1, num value2)`

- value1, value2: Numerische Werte, die mit AND verknüpft werden sollen.
 
**Hinweise:**<br> Diese Funktion konvertiert beide Parameter in die binäre Darstellung und setzt ein Bit auf:
 
- 0 – wenn ein oder beide entsprechenden Bits in *Mask* und *Flag* 0 sind.
- 1 – wenn beide entsprechenden Bits 1 sind. 

Anders gesagt: sie gibt in allen Fällen 0 zurück, außer wenn die entsprechenden Bits beider Parameter 1 sind.
 
**Beispiel:**<br> `BitAnd(&HF, &HF7)`<br> Gibt 7 zurück, da durch die Verknüpfung mit AND die Hexadezimalwerte "F" und "F7" zu diesem Wert ausgewertet werden.
 
----------
### BitOr

**Beschreibung:**<br> Die Funktion "BitOr" legt angegebene Bits auf einem Wert fest.

**Syntax:** <br> `num BitOr(num value1, num value2)`

- value1, value2: Numerische Werte, die mit OR verknüpft werden sollen. 

**Hinweise:**<br> Diese Funktion konvertiert beide Parameter in die binäre Darstellung und setzt ein Bit auf 1, wenn eine oder beide entsprechenden Bits in Mask und Flag 1 sind, und auf 0, wenn beide entsprechenden Bits 0 sind. <br> Anders gesagt, gibt sie in allen Fällen 1 zurück, außer wenn die entsprechenden Bits der beiden Parameter 0 sind.

----------
### CBool

**Beschreibung:**<br> Die Funktion "CBool" gibt einen booleschen Wert zurück, der auf dem ausgewerteten Ausdruck basiert.

**Syntax:** <br> `bool CBool(exp Expression)`

**Hinweise:**<br> Wenn die Auswertung des Ausdrucks einen Wert ungleich 0 ergibt, dann gibt "CBool" True zurück, andernfalls False.


**Beispiel:**<br> `CBool([attrib1] = [attrib2])` <br>

Gibt True zurück, wenn beide Attribute den gleichen Wert haben.
 
 


----------
### CDate

**Beschreibung:**<br> Die Funktion "CDate" gibt einen UTC-DateTime-Wert aus einer Zeichenfolge zurück. "DateTime" ist kein synchronisierungsspezifischer Attributtyp, wird jedoch von einigen Funktionen verwendet.

**Syntax:**<br> `dt CDate(str value)`

- value: Eine Zeichenfolge mit Datum, Uhrzeit und optional einer Zeitzone.

**Hinweise:**<br> Die zurückgegebene Zeichenfolge ist immer ein UTC-Wert.

**Beispiel:**<br> `CDate([employeeStartTime])` <br> Gibt einen DateTime-Wert auf Basis der Anfangszeit des Mitarbeiters zurück.

`CDate("2013-01-10 4:00 PM -8")` <br> Gibt einen DateTime-Wert zurück, der "2013-01-11 12:00 AM" darstellt.
 



----------
### CGuid

**Beschreibung:**<br> Die Funktion "CGuid" konvertiert die Zeichenfolgendarstellung einer GUID in die binäre Darstellung.
 
**Syntax:**<br> `bin CGuid(str GUID)GUID`

- Eine in diesem Muster formatierte Zeichenfolge: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx oder {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
 



----------
### Contains

**Beschreibung:**<br> Die Funktion "Contains" sucht in einem mehrwertigen Attribut nach einer Zeichenfolge.
 
**Syntax:**<br> `num Contains (mvstring attribute, str search)` – Groß-/Kleinschreibung<br> `num Contains (mvstring attribute, str search, enum Casetype)`<br> `num Contains (mvref attribute, str search)` – Groß-/Kleinschreibung

- attribute: Das zu durchsuchende mehrwertige Attribut<br>
- search: Die im Attribut zu findende Zeichenfolge<br>
- Casetype: CaseInsensitive \(keine Berücksichtigung von Groß-/Kleinschreibung\) oder CaseSensitive \(Berücksichtigung von Groß-/Kleinschreibung\)<br>

Gibt den Index in dem mehrwertigen Attribut zurück, in dem die Zeichenfolge gefunden wurde. Wenn die Zeichenfolge nicht gefunden wird, wird 0 zurückgegeben.
 

**Hinweise:**<br> Bei mehrwertigen Zeichenfolgenattributen werden in den Werten Teilzeichenfolgen gefunden.<br> Für Verweisattribute muss die Suchzeichenfolge genau mit dem Wert übereinstimmen, der als Übereinstimmung betrachtet wird.
 
**Beispiel:**<br> `IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br> Wenn das Attribut "proxyAddresses" eine primäre E-Mail-Adresse besitzt \(angegeben durch "SMTP:" \[in Großbuchstaben\]\), wird das Attribut "proxyAddress" zurückgegeben. Andernfalls wird ein Fehler zurückgegeben.
 
 


----------
### ConvertFromBase64

**Beschreibung:**<br> Die Funktion "ConvertFromBase64" konvertiert den angegebenen Base64-codierten Wert in eine reguläre Zeichenfolge.
 
**Syntax:**<br> `str ConvertFromBase64(str source)` – setzt Unicode zur Codierung voraus <br> `str ConvertFromBase64(str source, enum Encoding)`

- source: Base64-codierte Zeichenfolge<br>
- Codierung: Unicode, ASCII, UTF8.

**Beispiel:**<br> `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br> `ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Beide Beispiele geben \*Hello world!\* zurück.
 
 


----------
### ConvertFromUTF8Hex

**Beschreibung:**<br> Die Funktion "ConvertFromUTF8Hex" konvertiert den angegebenen UTF8-Hex-codierten Wert in eine Zeichenfolge.
 
**Syntax:**<br> `str ConvertFromUTF8Hex(str source)`

- source: UTF8-2-Byte-codierte Zeichenfolge
 
**Hinweise:**<br> Der Unterschied zwischen dieser Funktion und "ConvertFromBase64\(,UTF8\)" ist, dass das Ergebnis vom DN-Attribut verwertet werden kann.<br> Dieses Format wird von Azure Active Directory als DN verwendet.
 
**Beispiel:**<br> `ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br> Gibt \*Hello world!\* zurück.
 
 


----------
### ConvertToBase64

**Beschreibung:** <br> Die Funktion "ConvertToBase64" konvertiert eine Zeichenfolge in eine Unicode-Base64-Zeichenfolge.<br> Konvertiert den Wert eines Arrays von ganzen Zahlen in die entsprechende mit Base64-Ziffern codierte Zeichenfolgendarstellung.

**Syntax:** <br> `str ConvertToBase64(str source)`
 
**Beispiel:** <br> `ConvertToBase64("Hello world!")` <br> Gibt "SABlAGwAbABvACAAdwBvAHIAbABkACEA" zurück.
 
 


----------
### ConvertToUTF8Hex

**Beschreibung:**<br> Die Funktion "ConvertToUTF8Hex" konvertiert eine Zeichenfolge in einen UTF8-Hex-codierten Wert.
 
**Syntax:**<br> `str ConvertToUTF8Hex(str source)`
 
**Hinweise:**<br> Das Ausgabeformat dieser Funktion wird von Azure Active Directory als DN-Attributformat verwendet.
 
**Beispiel:** <br> `ConvertToUTF8Hex("Hello world!")` <br> Gibt 48656C6C6F20776F726C6421 zurück.
 
 


----------
### Count

**Beschreibung:**<br> Die Funktion "Count" gibt die Anzahl der Elemente in einem mehrwertigen Attribut zurück.
 
**Syntax:** <br> `num Count(mvstr attribute)`
 



----------
### CNum

**Beschreibung:** <br> Die Funktion "CNum" nimmt eine Zeichenfolge entgegen und gibt einen numerischen Datentyp zurück.
 
**Syntax:** <br> `num CNum(str value)`
 



----------
### CRef

**Beschreibung:** <br> Konvertiert eine Zeichenfolge in ein Verweisattribut.
 
**Syntax:** <br> `ref CRef(str value)`
 
**Beispiel:** <br> `CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`
 
 


----------
### CStr

**Beschreibung:** <br> Die Funktion "CStr" konvertiert in einen Zeichenfolgendatentyp.
 
**Syntax:** <br> `str CStr(num value)` <br> `str CStr(ref value)` <br> `str CStr(bool value)` <br>

- value: Kann ein numerischer Wert, ein Verweisattribut oder ein boolescher Wert sein. 
 
**Beispiel:** <br> `CStr([dn]) <br>` Könnte "cn=Joe,dc=contoso,dc=com" zurückgeben.
 
 


----------
### DateAdd

**Beschreibung:** <br> Gibt einen date-Wert zurück, der ein Datum enthält, dem ein angegebenes Zeitintervall hinzugefügt wurde.
 
**Syntax:** <br> `dt DateAdd(str interval, num value, dt date)`

- interval: Zeichenfolgenausdruck, der das Zeitintervall angibt, das hinzugefügt werden soll. Die Zeichenfolge muss einen der folgenden Werte aufweisen:
 - yyyy: Jahr
 - q: Quartal
 - m: Monat
 - y: Tag des Jahres
 - d: Tag
 - w: Wochentag
 - ww: Woche
 - h: Stunde
 - n: Minute
 - s: Sekunde
- value: Die Anzahl der Einheiten, die Sie hinzufügen möchten. Der Wert kann positiv \(für Datumsangaben in der Zukunft\) oder negativ \(für Datumsangaben in der Vergangenheit\) sein. 
- date: DateTime-Wert, der das Datum darstellt, dem das Intervall hinzugefügt wird.
 
**Beispiel:** <br> `DateAdd("m", 3, CDate("2001-01-01"))` <br> Fügt 3 Monate hinzu und gibt einen DateTime-Wert zurück, der "2001-04-01" darstellt.
 
 


----------
### DateFromNum

**Beschreibung:** <br> Die Funktion "DateFromNum" konvertiert einen Wert im AD-Datumsformat in einen DateTime-Typ.
 
**Syntax:** <br> `dt DateFromNum(num value)`
 
**Beispiel:** <br> `DateFromNum([lastLogonTimestamp])` <br> `DateFromNum(129699324000000000)` <br> Gibt einen DateTime-Wert zurück, der "2012-01-01 23:00:00" darstellt.
 



----------
### DNComponent

**Beschreibung:** <br> Die Funktion "DNComponent" gibt den Wert einer angegebenen DN-Komponente von links zurück.
 
**Syntax:** <br> `str DNComponent(ref dn, num ComponentNumber)`

- dn: Das zu interpretierende Verweisattribut
- ComponentNumber: Die Komponente im zurückzugebenden DN
 
**Beispiel:** <br> `DNComponent([dn],1)` <br> Falls der DN "Cn = Joe,ou =..." lautet, wird "Joe" zurückgegeben.
 



----------
### DNComponentRev

**Beschreibung:** <br> Die Funktion "DNComponentRev" gibt den Wert einer angegebenen DN-Komponente von rechts \(vom Ende\) zurück.
 
**Syntax:** <br> `str DNComponentRev(ref dn, num ComponentNumber)` <br> `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn: Das zu interpretierende Verweisattribut
- ComponentNumber: Die Komponente im zurückzugebenden DN
- Options: DC – alle Komponenten mit "dc=" ignorieren.
 
**Beispiel:** <br> `If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)` <br> `DNComponentRev([dn],1,”DC”)` <br> Beide geben "US" zurück.
 
 


----------
### Error

**Beschreibung:** <br> Die Funktion "Error" wird verwendet, um einen benutzerdefinierten Fehler zurückzugeben.
 
**Syntax:** <br> `void Error(str ErrorMessage)`
 
**Beispiel:** <br> `IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))` <br> Wenn das Attribut "accountName" nicht vorhanden ist, wird ein Fehler für das Objekt ausgegeben.
 
 


----------
### EscapeDNComponent

**Beschreibung:** <br> Die Funktion "EscapeDNComponent" versieht eine Komponente eines DN mit Escapezeichen, sodass sie im LDAP dargestellt werden kann.
 
**Syntax:** <br> `str EscapeDNComponent(str value)`
 
**Beispiel:** <br> `EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%` <br> Stellt sicher, dass das Objekt in einem LDAP-Verzeichnis erstellt werden kann, auch wenn das Attribut "displayName" Zeichen enthält, die in LDAP mit Escapezeichen versehen werden müssen.
 
 


----------
### FormatDateTime

**Beschreibung:**<br> Die Funktion "FormatDateTime" wird verwendet, um einen DateTime-Wert in eine Zeichenfolge in einem angegebenen Format zu formatieren.
 
**Syntax:** <br> `str FormatDateTime(dt value, str format)`

- value: Ein Wert im DateTime-Format <br>
- format: Eine Zeichenfolge, die das Format darstellt, in das konvertiert werden soll.
 
**Hinweise:** <br> Die möglichen Werte für das Format finden Sie hier: [Benutzerdefinierte Datums-/Zeitformate (Format-Funktion)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)
 
**Beispiel:** <br>
 
`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)` <br> Ergibt "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br> Kann "20140905081453.0Z" ergeben.
 
 


----------
### GUID

**Beschreibung:** <br> Die Funktion "GUID" generiert eine neue zufällige GUID.
 
**Syntax:** <br> `str GUID()`
 



----------
### IIF

**Beschreibung:** <br> Die Funktion "IIF" gibt basierend auf einer angegebenen Bedingung einen Wert aus einem Satz möglicher Werte zurück.
 
**Syntax:** <br> `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition: Ein beliebiger Wert oder Ausdruck, der als True oder False ausgewertet werden kann.
- valueIfTrue: Ein Wert, der zurückgegeben wird, falls die Bedingung als True ausgewertet wird.
- valueIfFalse: Ein Wert, der zurückgegeben wird, falls die Bedingung als False ausgewertet wird.

**Beispiel:** <br> `IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br> Gibt den Alias eines Benutzers mit der Ergänzung "t-" am Anfang zurück, wenn der Benutzertyp "Intern" lautet, andernfalls den unveränderten Alias des Benutzers.
 
 


----------
### InStr

**Beschreibung:** <br> Die Funktion "InStr" findet das erste Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.
 
**Syntax:** <br>
 
`num InStr(str stringcheck, str stringmatch)` <br> `num InStr(str stringcheck, str stringmatch, num start)` <br> `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: Zu durchsuchende Zeichenfolge <br>
- stringmatch: Zu findende Zeichenfolge <br>
- start: Startposition zum Finden der Teilzeichenfolge <br>
- compare: VbTextCompare oder VbBinaryCompare
 
**Hinweise:** <br> Gibt die Position zurück, an der die Teilzeichenfolge gefunden wurde, bzw. 0, wenn sie nicht gefunden wurde.

**Beispiel:** <br> `InStr("The quick brown fox","quick")` <br> Wird als 5 ausgewertet.

`InStr("repEated","e",3,vbBinaryCompare)` <br> Wird als 7 ausgewertet.
 
 


----------
### InStrRev

**Beschreibung:** <br> Die Funktion "InStrRev" findet das letzte Vorkommen einer Teilzeichenfolge in einer Zeichenfolge.
 
**Syntax:** <br> `num InstrRev(str stringcheck, str stringmatch)` <br> `num InstrRev(str stringcheck, str stringmatch, num start)` <br> `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: Zu durchsuchende Zeichenfolge <br>
- stringmatch: Zu findende Zeichenfolge <br>
- start: Startposition zum Finden der Teilzeichenfolge <br>
- compare: VbTextCompare oder VbBinaryCompare

**Hinweise:** <br> Gibt die Position zurück, an der die Teilzeichenfolge gefunden wurde, bzw. 0, wenn sie nicht gefunden wurde.

**Beispiel:** <br> `InStrRev("abbcdbbbef","bb")` <br> Gibt 7 zurück.
 
 


----------
### IsBitSet

**Beschreibung:** <br> Die Funktion "IsBitSet" testet, ob ein Bit gesetzt ist oder nicht.
 
**Syntax:** <br> `bool IsBitSet(num value, num flag)`

- value: Ein numerischer Wert, der ausgewertet wird. flag: Ein numerischer Wert, der das Bit angibt, das ausgewertet werden soll.
 
**Example:** <br> `IsBitSet(&HF,4)` <br> Gibt True zurück, weil Bit "4" im Hexadezimalwert "F" gesetzt ist.
 



----------
### IsDate

**Beschreibung:** <br> Die Funktion "IsDate" gibt True zurück, wenn der Ausdruck als DateTime-Typ ausgewertet werden kann.
 
**Syntax:** <br> `bool IsDate(var Expression)`
 
**Hinweise:** <br> Wird verwendet, um zu bestimmen, ob "CDate\(\)" erfolgreich eingesetzt werden kann.
 



----------
###IsEmpty

**Beschreibung:**<br> Die Funktion "IsEmpty" gibt True zurück, wenn das Attribut in CS oder MV vorhanden ist, jedoch zu einer leeren Zeichenfolge ausgewertet wird.
 
**Syntax:** <br> `bool IsEmpty(var Expression)`
 



----------
###IsGuid

**Beschreibung:** <br> Die Funktion "IsGuid" gibt True zurück, wenn die Zeichenfolge in eine GUID konvertiert werden kann.
 
**Syntax:** <br> `bool IsGuid(str GUID)`
 
**Hinweise:** <br> Eine GUID ist als eine Zeichenfolge definiert, die einem dieser Muster entspricht: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx oder {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Wird verwendet, um zu bestimmen, ob "CGuid\(\)" erfolgreich eingesetzt werden kann.
 
**Beispiel:** <br> `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br> Wenn "StrAttribute" ein GUID-Format hat, binäre Darstellung zurückgeben, andernfalls einen Nullwert.
 
 


----------
###IsNull

**Beschreibung:** <br> Die Funktion "IsNull" gibt True zurück, wenn die Auswertung des Ausdrucks einen Nullwert ergibt.
 
**Syntax:** <br> `bool IsNull(var Expression)`
 
**Hinweise:** <br> Für ein Attribut wird ein Nullwert durch das Fehlen des Attributs ausgedrückt.
 
**Beispiel:** <br> `IsNull([displayName])` <br> Gibt True zurück, wenn das Attribut nicht in CS oder MV vorhanden ist.
 
 


----------
###IsNullOrEmpty

**Beschreibung:**<br> Die Funktion "IsNullOrEmpty" gibt True zurück, wenn der Ausdruck einem Nullwert oder einer leeren Zeichenfolge entspricht.
 
**Syntax:** <br> `bool IsNullOrEmpty(var Expression)`
 
**Hinweise:** <br> Für ein Attribut wird dies als True ausgewertet, wenn das Attribut nicht vorhanden ist oder zwar vorhanden, jedoch eine leere Zeichenfolge ist.<br> Die Umkehrung dieser Funktion heißt "IsPresent".
 
**Beispiel:** <br> `IsNull([displayName])` <br> Gibt True zurück, wenn das Attribut nicht vorhanden oder eine leere Zeichenfolge in CS oder MV ist.
 
 


----------
### IsNumeric

**Beschreibung:** <br> Die Funktion "IsNumeric" gibt einen booleschen Wert zurück, der angibt, ob ein Ausdruck als Zahlentyp ausgewertet werden kann.
 
**Syntax:** <br> `bool IsNumeric(var Expression)`
 
**Hinweise:** <br> Wird verwendet, um zu bestimmen, ob "CNum\(\)" erfolgreich zum Analysieren des Ausdrucks eingesetzt werden kann.




----------
### IsString

**Beschreibung:** <br> Die Funktion "IsString" gibt True zurück, wenn der Ausdruck als Zeichenfolgentyp ausgewertet werden kann.
 
**Syntax:** <br> `bool IsString(var expression)`
 
**Hinweise:** <br> Wird verwendet, um zu bestimmen, ob "CStr\(\)" erfolgreich zum Analysieren des Ausdrucks eingesetzt werden kann.
 



----------
### IsPresent

**Beschreibung:** <br> Die Funktion "IsPresent" gibt True zurück, wenn der Ausdruck zu einer Zeichenfolge ausgewertet wird, die kein Nullwert und nicht leer ist.
 
**Syntax:** <br> `bool IsPresent(var expression)`
 
**Hinweise:** <br> Die Umkehrung dieser Funktion heißt "IsNullOrEmpty".
 
**Beispiel:** <br>
 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`
  



----------
### Item

**Beschreibung:** <br> Die Funktion "Item" gibt ein Element aus einer mehrwertigen Zeichenfolge/einem mehrwertigen Attribut zurück.
 
**Syntax:** <br> `var Item(mvstr attribute, num index)`

- attribute: Mehrwertiges Attribut <br>
- index: Index für ein Element in der mehrwertigen Zeichenfolge
 
**Hinweise:** <br> Die Funktion "Item" ist sinnvoll mit der Funktion "Contains" einsetzbar, da letztere den Index für ein Element im mehrwertigen Attribut zurückgibt.

Gibt einen Fehler aus, wenn der Index außerhalb des gültigen Bereichs liegt.
 
**Beispiel:** <br> `Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)` <br> Gibt die primäre E-Mail-Adresse zurück.
 
 


----------
### ItemOrNull

**Beschreibung:** <br> Die Funktion "ItemOrNull" gibt ein Element aus einer mehrwertigen Zeichenfolge/einem mehrwertigen Attribut zurück.
 
**Syntax:** <br> `var ItemOrNull(mvstr attribute, num index)`

- attribute: Mehrwertiges Attribut <br>
- index: Index für ein Element in der mehrwertigen Zeichenfolge
 
**Hinweise:** <br> Die Funktion "ItemOrNull" ist sinnvoll mit der Funktion "Contains" einsetzbar, da letztere den Index für ein Element im mehrwertigen Attribut zurückgibt.

Wenn der Index außerhalb des gültigen Bereichs liegt, wird ein Nullwert zurückgegeben.
 



----------
### Join

**Beschreibung:** <br> Die Funktion "Join" nimmt eine Zeichenfolge mit mehreren Werten entgegen und gibt eine einwertige Zeichenfolge zurück, zwischen deren einzelne Elemente das angegebene Trennzeichen eingefügt ist.
 
**Syntax:** <br> `str Join(mvstr attribute)` <br> `str Join(mvstr attribute, str Delimiter)`

- attribute: Mehrwertiges Attribut mit Zeichenfolgen, die verknüpft werden sollen. <br>
- delimiter: Eine beliebige Zeichenfolge, die die Teilzeichenfolgen in der zurückgegebenen Zeichenfolge trennt. Wenn nicht angegeben, wird das Leerzeichen \(" "\) verwendet. Wenn "delimiter" eine Zeichenfolge der Länge 0 \(""\) oder "Nothing" ist, werden alle Elemente in der Liste ohne Trennzeichen verkettet.
 
**Hinweise:**<br> Zwischen den Funktionen "Join" und "Split" besteht Parität. Die Funktion "Join" nimmt ein Array von Zeichenfolgen entgegen, verknüpft sie mit einer Trennzeichenfolge und gibt eine einzige Zeichenfolge zurück. Die Funktion "Split" nimmt eine Zeichenfolge entgegen, trennt sie mit dem Trennzeichen und gibt ein Array von Zeichenfolgen zurück. Ein wichtiger Unterschied ist jedoch, dass "Join" Zeichenfolgen mit einer beliebigen Trennzeichenfolge verketten kann, "Split" aber nur Zeichenfolgen mit einem einzigen Trennzeichen trennen kann.
 
**Beispiel:** <br> `Join([proxyAddresses],”,”)` <br> Könnte zurückgeben: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com”
 
 


----------
### LCase

**Beschreibung:** <br> Die Funktion "LCase" konvertiert alle Zeichen in einer Zeichenfolge in Kleinbuchstaben.
 
**Syntax:** <br> `str LCase(str value)`
 
**Beispiel:** <br> `LCase(“TeSt”)` <br> Gibt "test" zurück.
 
 


----------
### Left

**Beschreibung:** <br> Die Funktion "Left" gibt eine angegebene Anzahl von Zeichen von der linken Seite einer Zeichenfolge zurück.
 
**Syntax:** <br> `str Left(str string, num NumChars)`

- string: Zeichenfolge, aus der Zeichen zurückgegeben werden <br>
- numChars: Diese Zahl gibt die Anzahl der Zeichen an, die vom Anfang der Zeichenfolge \(links\) zurückgeben werden
 
**Hinweise:** <br> Eine Zeichenfolge, die die ersten "numChars" Zeichen in der Zeichenfolge enthält:

- Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
- Wenn "numChars" \< 0, wird die Eingabezeichenfolge zurückgegeben.
- Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn die Zeichenfolge weniger Zeichen enthält als in "numChars" angegeben, wird eine identische \(alle in Parameter 1 enthaltenen Zeichen enthaltende\) Zeichenfolge zurückgegeben.
 
**Beispiel:** <br> `Left(“John Doe”, 3)` <br> Gibt "Joh" zurück.
 



----------
### Len

**Beschreibung:** <br> Die Funktion "Len" gibt die Anzahl der Zeichen in einer Zeichenfolge zurück.
 
**Syntax:** <br> `num Len(str value)`
 
**Beispiel:** <br> `Len(“John Doe”)` <br> Gibt 8 zurück.
 



----------
### LTrim

**Beschreibung:** <br> Die Funktion "LTrim" entfernt führende Leerzeichen aus einer Zeichenfolge.
 
**Syntax:** <br> `str LTrim(str value)`
 
**Beispiel:** <br> `LTrim(“ Test ”)` <br> Gibt "Test" zurück.
 
 


----------
### Mid

**Beschreibung:** <br> Die Funktion "Mid" gibt eine angegebene Anzahl von Zeichen ab einer angegebenen Position in einer Zeichenfolge zurück.
 
**Syntax:** <br> `str Mid(str string, num start, num NumChars)`

- string: Zeichenfolge, aus der Zeichen zurückgegeben werden <br>
- start: Eine Zahl zur Angabe der Ausgangsposition in der Zeichenfolge, ab der Zeichen zurückgegeben werden
- numChars: Eine Zahl zur Angabe der Zeichen, die ab der Position in der Zeichenfolge zurückgegeben werden
 

**Hinweise:** <br> Gibt "numChars" Zeichen ab der Ausgangsposition in der Zeichenfolge zurück.<br> Eine Zeichenfolge, die "numChars" Zeichen ab der Ausgangsposition in der Zeichenfolge enthält:

- Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
- Wenn "numChars" \< 0, wird die Eingabezeichenfolge zurückgegeben.
- Wenn "start" \> Länge der Zeichenfolge, wird die Eingabezeichenfolge zurückgegeben
- Wenn "start" \< = 0, wird die Eingabezeichenfolge zurückgegeben
- Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn nicht "numChars" Zeichen ab der Ausgangsposition in der Zeichenfolge vorhanden sind, werden so viele Zeichen wie vorhanden zurückgegeben.
 
**Beispiel:** <br>
 
`Mid(“John Doe”, 3, 5)` <br> Gibt "hn Do" zurück.

`Mid(“John Doe”, 6, 999)` <br> Gibt "Doe" zurück.
 
 


----------
### Now

**Beschreibung:** <br> Die Funktion "Now" gibt einen DateTime-Wert zurück, der das aktuelle Datum und die aktuelle Uhrzeit gemäß Systemdatum und -uhrzeit Ihres Computers angibt.
 
**Syntax:** <br> `dt Now()`
 



----------
### NumFromDate

**Beschreibung:** <br> Die Funktion "NumFromDate" gibt ein Datum im AD-Datumsformat zurück.
 
**Syntax:** <br> `num NumFromDate(dt value)`
 

**Beispiel:** <br> `NumFromDate(CDate("2012-01-01 23:00:00"))` <br> Gibt 129699324000000000 zurück.
 
 


----------
### PadLeft

**Beschreibung:** <br> Die Funktion "PadLeft" füllt eine Zeichenfolge nach links bis zu einer angegebenen Länge mit einem bereitgestellten Auffüllzeichen auf.
 
**Syntax:** <br> `str PadLeft(str string, num length, str padCharacter)`

- string: Die aufzufüllende Zeichenfolge <br>
- length: Eine ganze Zahl, die die gewünschte Länge der Zeichenfolge angibt <br>
- padCharacter: Eine Zeichenfolge, die aus einem einzelnen Zeichen besteht, das als Auffüllzeichen verwendet werden soll
 


----------
### Hinweise 
 
- Wenn die Länge der Zeichenfolge "length" unterschreitet, wird "padCharacter" wiederholt an den Anfang der Zeichenfolge \(links\) gesetzt, bis ihre Länge "length" entspricht.
- "padCharacter" kann ein Leerzeichen sein, jedoch kein Nullwert.
- Wenn die Länge der Zeichenfolge gleich oder größer als "length" ist, wird die Zeichenfolge unverändert zurückgegeben.
- Wenn die Länge der Zeichenfolge größer als oder gleich "length" ist, wird eine zur Zeichenfolge identische Zeichenfolge zurückgegeben.
- Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird eine neue Zeichenfolge in gewünschter Länge zurückgegeben, die die mit "padCharacter" aufgefüllte Zeichenfolge enthält.
- Wenn die Zeichenfolge einen Nullwert aufweist, gibt die Funktion eine leere Zeichenfolge zurück.

**Beispiel:** <br> `PadLeft(“User”, 10, “0”)` <br> Gibt "000000User" zurück.
 
 


----------
### PadRight

**Beschreibung:** <br> Die Funktion "PadRight" füllt eine Zeichenfolge nach rechts bis zu einer angegebenen Länge mit einem bereitgestellten Auffüllzeichen auf.
 
**Syntax:** <br> `str PadRight(str string, num length, str padCharacter)`

- string: Die aufzufüllende Zeichenfolge. 
- length: Eine ganze Zahl, die die gewünschte Länge der Zeichenfolge angibt.
- padCharacter: Eine Zeichenfolge, die aus einem einzelnen Zeichen besteht, das als Auffüllzeichen verwendet werden soll
 
**Hinweise:**
 
- Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird "padCharacter" wiederholt an das Ende der Zeichenfolge \(rechts\) gesetzt, bis ihre Länge "length" entspricht.
- "padCharacter" kann ein Leerzeichen sein, jedoch kein Nullwert.
- Wenn die Länge der Zeichenfolge gleich oder größer als "length" ist, wird die Zeichenfolge unverändert zurückgegeben.
- Wenn die Länge der Zeichenfolge größer als oder gleich "length" ist, wird eine zur Zeichenfolge identische Zeichenfolge zurückgegeben.
- Wenn die Länge der Zeichenfolge kleiner als "length" ist, wird eine neue Zeichenfolge in gewünschter Länge zurückgegeben, die die mit "padCharacter" aufgefüllte Zeichenfolge enthält.
- Wenn die Zeichenfolge einen Nullwert aufweist, gibt die Funktion eine leere Zeichenfolge zurück.
 

**Beispiel:** <br> `PadRight(“User”, 10, “0”)` <br> Gibt "User000000" zurück.
 
 


----------
### PCase

**Beschreibung:** <br> Die Funktion "PCase" konvertiert das erste Zeichen jedes durch Leerzeichen getrennten Worts in einer Zeichenfolge in einen Großbuchstaben und alle anderen Zeichen in Kleinbuchstaben.
 
**Syntax:** <br> `String PCase(string)`
 
**Beispiel:** <br> `PCase(“TEsT”)` <br> Gibt "Test" zurück.
 
 


----------
### RandomNum

**Beschreibung:** <br> Die Funktion "RandomNum" gibt eine Zufallszahl innerhalb eines angegebenen Intervalls zurück.
 
**Syntax:** <br> `num RandomNum(num start, num end)`

- start: Eine Zahl, die die untere Grenze des zu generierenden Zufallswerts angibt <br>
- end: Eine Zahl, die die obere Grenze des zu generierenden Zufallswerts angibt
 
**Beispiel:** <br> `Random(100,999)` <br> Gibt 734 zurück.
 
 


----------
### RemoveDuplicates

**Beschreibung:** <br> Die Funktion "RemoveDuplicates" stellt sicher, dass in einer übergebenen mehrwertigen Zeichenfolge jeder Wert eindeutig ist.
 
**Syntax:** <br> `mvstr RemoveDuplicates(mvstr attribute)`
 
**Beispiel:** <br> `RemoveDuplicates([proxyAddresses])` <br> Gibt ein bereinigtes Attribut "proxyAddress" zurück, aus dem alle doppelten Werte entfernt wurden.
 
 


----------
### Replace

**Beschreibung:** <br> Die Funktion "Replace" ersetzt alle Vorkommen einer Zeichenfolge durch eine andere Zeichenfolge.
 
**Syntax:** <br> `str Replace(str string, str OldValue, str NewValue)`

- string: Eine Zeichenfolge, in der Werte ersetzt werden sollen <br>
- OldValue: Die Zeichenfolge, die gesucht und ersetzt werden soll <br>
- NewValue: Die Zeichenfolge, die die gesuchte Zeichenfolge ersetzen soll
 

**Hinweise:** <br> Die Funktion erkennt die folgenden speziellen Moniker:

- \\n – neue Zeile 
- \\r – Wagenrücklauf
- \\t – Tabulator
 

**Beispiel:** <br>
 
`Replace([address],”\r\n”,”, “)` <br> Ersetzt CRLF durch Komma und Leerzeichen, was folgendermaßen aussehen könnte: "One Microsoft Way, Redmond, WA, USA".
 
 


----------
### ReplaceChars

**Beschreibung:** <br> Die Funktion "ReplaceChars" ersetzt alle Vorkommen von Zeichen, die in der Zeichenfolge "ReplacePattern" gefunden werden.

**Syntax:** <br> `str ReplaceChars(str string, str ReplacePattern)`

- string: Eine Zeichenfolge, in der Zeichen ersetzt werden sollen
- ReplacePattern: Eine Zeichenfolge, die ein Wörterbuch mit zu ersetzenden Zeichen enthält 
 
Das Format lautet "{source1}:{target1},{source2}:{target2},{sourceN},{targetN}", wobei "source" das zu findende Zeichen und "target" die Zeichenfolge ist, durch die es ersetzt wird.
 

**Hinweise:**
 
- Die Funktion ersetzt alle Vorkommen von definierten Quellen durch die Ziele. 
- Die Quelle muss genau ein Zeichen \(Unicode\) sein. 
- Die Quelle kann nicht leer sein oder mehrere Zeichen enthalten \(Analysefehler\).
- Das Ziel kann mehrere Zeichen enthalten, z. B. ö:oe, β:ss.
- Das Ziel kann leer sein, um anzugeben, dass das Zeichen entfernt werden soll. 
- Bei der Quelle wird die Groß-/Kleinschreibung beachtet, und es muss eine genaue Übereinstimmung vorliegen.
- Die reservierten Zeichen "," \(Komma\) und ":" \(Doppelpunkt\) können mit dieser Funktion nicht ersetzt werden. 
- Leerzeichen und andere Whitespaces in der Zeichenfolge "ReplacePattern" werden ignoriert.
 

**Beispiel:** <br> '%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o'

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br> Gibt "Raksmorgas" zurück

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br> Gibt "ONeil" zurück, der Apostroph ist als zu entfernen definiert.
 



----------
### Right

**Beschreibung:** <br> Die Funktion "Right" gibt eine angegebene Anzahl von Zeichen von der rechten Seite \(Ende\) einer Zeichenfolge zurück.
 
**Syntax:** <br> `str Right(str string, num NumChars)`

- string: Zeichenfolge, aus der Zeichen zurückgegeben werden 
- numChars: Diese Zahl gibt die Anzahl der Zeichen an, die vom Ende der Zeichenfolge \(rechts\) zurückgeben werden
 
**Hinweise:** <br> "numChars" Zeichen werden ab der letzten Position der Zeichenfolge zurückgegeben.

Eine Zeichenfolge, die die letzten "numChars" Zeichen in der Zeichenfolge enthält:

- Wenn "numChars" = 0, wird eine leere Zeichenfolge zurückgegeben.
- Wenn "numChars" \< 0, wird die Eingabezeichenfolge zurückgegeben.
- Wenn die Zeichenfolge einen Nullwert aufweist, wird eine leere Zeichenfolge zurückgegeben.

Wenn die Zeichenfolge weniger Zeichen enthält als in "numChars" angegeben, wird eine identische Zeichenfolge zurückgegeben.

**Beispiel:** <br> `Right(“John Doe”, 3)` <br> Gibt "Doe" zurück.
 



----------
### RTrim

**Beschreibung:** <br> Die Funktion "RTrim" entfernt nachstehende Leerzeichen aus einer Zeichenfolge.
 
**Syntax:** <br> `str RTrim(str value)`

**Beispiel:** <br> `RTrim(“ Test ”)` <br> Gibt "Test" zurück.




----------
### Split

**Beschreibung:** <br> Die Funktion "Split" nimmt eine durch Trennzeichen getrennte Zeichenfolge entgegen und wandelt sie in eine mehrwertige Zeichenfolge um.
 

**Syntax:** <br> `mvstr Split(str value, str delimiter)` \<br? `mvstr Split(str value, str delimiter, num limit)`

- value: Die Zeichenfolge mit Trennzeichen
- delimiter: Einzelnes Zeichen, das als Trennzeichen verwendet werden soll 
- limit: Maximale Anzahl der Werte, die zurückgegeben werden
 
**Beispiel:** <br> `Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br> Gibt eine mehrwertige Zeichenfolge mit 2 Elementen zurück, die für das Attribut "proxyAddress" nützlich ist.
 



----------
### StringFromGuid

**Beschreibung:** <br> Die Funktion "StringFromGuid" nimmt eine binäre GUID entgegen und konvertiert sie in eine Zeichenfolge.
 
**Syntax:** <br> `str StringFromGuid(bin GUID)`
 



----------
### StringFromSid

**Beschreibung:** <br> Die Funktion "StringFromSid" konvertiert ein Bytearray oder ein mehrwertiges Bytearray, das eine Sicherheits-ID enthält, in eine Zeichenfolge oder mehrwertige Zeichenfolge.
 
**Syntax:** <br> `str StringFromSid(bin ObjectSID)` <br> `mvstr StringFromSid(mvbin ObjectSID)`
 



----------
### Switch

**Beschreibung:** <br> Mit der Funktion "Switch" wird ein einzelner Wert basierend auf ausgewerteten Bedingungen zurückgegeben.

**Syntax:** <br> `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: Variantenausdruck, den Sie auswerten möchten 
- value: Wert, der zurückgegeben werden soll, wenn der entsprechende Ausdruck True ergibt
 
**Hinweise:** <br> Die Argumentliste der Funktion "Switch" enthält Paare von Ausdrücken und Werten. Die Ausdrücke werden von links nach rechts ausgewertet, und der Wert, der mit dem ersten als True ausgewerteten Ausdruck verknüpft ist, wird zurückgegeben. Wenn die Teile nicht richtig paarweise angegeben werden, tritt ein Laufzeitfehler auf.

Wenn z. B. "expr1" True ist, gibt "Switch" "value1" zurück. Wenn "expr-1" False, aber "expr-2" True ist, gibt "Switch" "value-2" zurück usw.

"Switch" gibt "Nothing" zurück, wenn Folgendes zutrifft: – Keiner der Ausdrücke ist True. – Der erste Ausdruck, der True ist, verfügt über einen entsprechenden Wert, der ein Nullwert ist.

"Switch" wertet alle Ausdrücke aus, auch wenn die Funktion nur einen von ihnen zurückgibt. Aus diesem Grund sollten Sie auf unerwünschte Nebeneffekte achten. Wenn z. B. die Berechnung eines beliebigen Ausdrucks zu einer Division durch 0 führt, tritt ein Fehler auf.

Ein Wert kann auch die Funktion "Error" sein, die eine benutzerdefinierte Zeichenfolge zurückgibt.

**Beispiel:** <br> `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br> Gibt die Sprache zurück, die in einigen wichtigen Städten gesprochen wird, andernfalls wird "Error" zurückgegeben.




----------
### Trim

**Beschreibung:** <br> Die Funktion "Trim" entfernt führende und nachstehende Leerzeichen aus einer Zeichenfolge.
 
**Syntax:** <br> `str Trim(str value)` <br> `mvstr Trim(mvstr value)`
 
**Beispiel:** <br> `Trim(“ Test ”)` <br> Gibt "Test" zurück.

`Trim([proxyAddresses])` <br> Entfernt führende und nachstehende Leerzeichen für jeden Wert im Attribut "proxyAddress".




----------
### UCase

**Beschreibung:** <br> Die Funktion "UCase" konvertiert alle Zeichen in einer Zeichenfolge in Großbuchstaben.

**Syntax:** <br> `str UCase(str string)`
 
**Beispiel:** <br> `UCase(“TeSt”)` <br> Gibt "TEST" zurück.
 
 


----------
### Word

**Beschreibung:** <br> Die Funktion "Word" gibt ein Wort, das in einer Zeichenfolge enthalten ist, auf der Basis von Parametern zurück, die die zu verwendenden Trennzeichen und die Nummer des zurückzugebenden Worts beschreiben.
 
**Syntax:** <br> `str Word(str string, num WordNumber, str delimiters)`

- string: Zeichenfolge, aus der ein Wort zurückgegeben wird
- WordNumber: Eine Zahl, die die Nummer des zurückzugebenden Worts angibt 
- delimiters: Eine Zeichenfolge, die das/die Trennzeichen angibt, das/die verwendet werden soll\(en\), um Wörter zu identifizieren
 
**Hinweise:** <br> Alle Folgen von Zeichen in einer Zeichenfolge, die durch ein Zeichen getrennt werden, das in "delimiters" enthalten ist, werden als Wörter identifiziert:

- Wenn "WordNumber" \< 1, wird eine leere Zeichenfolge zurückgegeben
- Wenn "string" einen Nullwert hat, wird eine leere Zeichenfolge zurückgegeben

Wenn "string" weniger als "WordNumber" Wörter enthält, oder "string" keine Wörter enthält, die durch Trennzeichen identifiziert werden, wird eine leere Zeichenfolge zurückgegeben.
 

**Beispiel:** <br> `Word(“The quick brown fox”,3,” “)` <br> Gibt "brown" zurück.

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br> Gibt "has" zurück.


## Zusätzliche Ressourcen

* [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)
 
 
<!--Image references-->

<!-----HONumber=July15_HO5-->
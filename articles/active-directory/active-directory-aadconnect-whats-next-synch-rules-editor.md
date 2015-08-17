
<properties 
	pageTitle="Verwenden des Synchronisierungsregel-Editors von Azure AD Connect" 
	description="Erfahren Sie, wie Sie den Synchronisierungsregel-Editor von Azure AD Connect verwenden." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Synchronisierungsregel-Editor von Azure AD Connect


## Verwenden des Synchronisierungsregel-Editors

In Azure AD Connect können Sie den Objekt- und Attributfluss zwischen Azure AD und Ihren lokalen Verzeichnissen konfigurieren und optimieren, indem Sie Synchronisierungsregeln konfigurieren.

Eine Regel ist ein Konfigurationsobjekt mit einem Satz von Attributen, die übertragen werden, wenn eine Bedingung erfüllt ist. Damit wird auch beschrieben, welche Beziehung ein Objekt in einem Connectorbereich zu einem Objekt im Metaverse hat. Dies wird als Verknüpfung oder Übereinstimmung bezeichnet. Die Synchronisierungsregeln verfügen über eine Rangordnung, die die jeweilige Beziehung untereinander anzeigt. Eine Synchronisierungsregel mit einem niedrigeren numerischen Wert in der Rangfolge hat eine höhere Priorität, im Falle eines Attributflusskonflikts hat die höhere Priorität bei der Konfliktauflösung Vorrang. Synchronisierungsregeln können mithilfe des Synchronisierungsregel-Editors konfiguriert werden.

Als Beispiel betrachten wir die Synchronisierungsregel "In von AD – User AccountEnabled". Wir markieren diese Zeile im Synchronisierungsregel-Editor und wählen "Bearbeiten" aus. Eine Synchronisierungsregel verfügt über vier Konfigurationsabschnitte: Beschreibung, Bereichsfilter, Verknüpfungsregeln und Transformationen.

### Beschreibung
Der erste Bereich bietet grundlegende Informationen wie Name und Beschreibung.

<center>![Verknüpfungsregel](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync1.png) </center>

Es sind auch Informationen dazu enthalten, auf welches verbundene System sich diese Regel bezieht, auf welchen Objekttyp sie im verbundenen System angewendet wird und welcher Metaverse-Objekttyp vorliegt. Der Metaverse-Objekttyp ist immer eine Person, unabhängig davon, ob der Quellobjekttyp ein Benutzer, iNetOrgPerson oder ein Kontakt ist. Der Metaverse-Objekttyp sollte nie geändert werden, daher wird er als generischer Typ erstellt. Für den Verknüpfungstyp kann "Join", "StickyJoin" oder "Provision" festgelegt werden. Diese Einstellung arbeitet mit dem Bereich der Verknüpfungsregeln zusammen. Dies wird später behandelt.

### Bereichsfilter

Der Bereich "Bereichsfilter" wird zum Konfigurieren verwendet, wann eine Synchronisierungsregel angewendet werden soll. Da der Name der betrachteten Synchronisierungsregel darauf hinweist, dass sie nur für aktivierte Benutzer angewendet werden sollte, wird der Gültigkeitsbereich so konfiguriert, dass für das AD-Attribut "userAccountControl" das Bit 2 nicht gesetzt sein darf. Wenn wir einen Benutzer in Active Directory finden, wenden wir diese Regel an, wenn für "userAccountControl" der Dezimalwert "512" festgelegt ist (aktivierter normaler Benutzer). Sie wird jedoch nicht angewendet, wenn für den gefundenen Benutzer der Wert "514" für "userAccountControl" festgelegt ist (deaktivierter normaler Benutzer).

<center>![Verknüpfungsregel](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

Der Bereichsfilter verfügt über Gruppen und Klauseln, die geschachtelt werden können. Alle Klauseln innerhalb einer Gruppe müssen erfüllt werden, damit eine Synchronisierungsregel angewendet wird. Wenn mehrere Gruppen definiert sind, muss mindestens eine Gruppe die Anforderungen erfüllen, damit die Regel angewendet wird. Das bedeutet, dass zwischen Gruppen ein logisches ODER und innerhalb einer Gruppe ein logisches UND ausgewertet wird. Ein Beispiel hierfür befindet sich in der ausgehenden Synchronisierungsregel "Aus zu AAD – Gruppenverknüpfung", die nachfolgend gezeigt wird. Es gibt zwei Synchronisierungsfiltergruppen: eine für Sicherheitsgruppen (securityEnabled EQUAL True) und eine für Verteilergruppen (securityEnabled EQUAL False).

<center>![Verknüpfungsregel](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

Diese Regel wird zur Definition verwendet, welche Gruppen für AAD bereitgestellt werden sollen. Verteilergruppen müssen E-Mail-aktiviert sein, damit sie mit AAD synchronisiert werden können, aber für Sicherheitsgruppen ist dies nicht erforderlich. Wie Sie ebenfalls erkennen können, werden zudem viele zusätzliche Attribute ausgewertet.

###Verknüpfungsregeln
Der dritte Bereich wird für die Konfiguration verwendet, wie sich Objekte im Connectorbereich auf Objekte im Metaverse beziehen. Die von uns zuvor betrachtete Regel verfügt über keine Konfiguration für Verknüpfungsregeln, daher betrachten wir stattdessen die Regel "Ein von AD – Benutzerverknüpfung".

<center>![Verknüpfungsregel](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

Der Inhalt der Verknüpfungsregeln hängt von der vergleichenden Option ab, die im Installations-Assistenten ausgewählt ist. Für eine eingehende Regel beginnt die Auswertung mit einem Objekt im Quellconnectorbereich, und jede Gruppe in den Verknüpfungsregeln wird nacheinander ausgewertet. Wenn die Auswertung eines Quellobjekts mit einer der Verknüpfungsregeln ergibt, dass es genau mit einem Objekt im Metaverse übereinstimmt, dann werden die Objekte verknüpft. Wenn alle Regeln ausgewertet wurden und es keine Übereinstimmung gibt, wird der Verknüpfungstyp auf der Beschreibungsseite verwendet. Wenn für diese Einstellung "Provision" festgelegt ist, wird im Ziel (dem Metaverse) ein neues Objekt erstellt. Die Bereitstellung eines neuen Objekts im Metaverse wird auch als Entwurf eines Objekts im Metaverse bezeichnet. Die Verknüpfungsregeln werden nur einmal ausgewertet. Wenn ein Connectorbereichsobjekt und ein Metaverse-Objekt verknüpft werden, bleiben sie verknüpft, so lange der Gültigkeitsbereich der Synchronisierungsregel weiterhin gegeben ist. Bei der Auswertung von Synchronisierungsregeln muss sich nur eine Synchronisierungsregel mit Verknüpfungsregeln im Gültigkeitsbereich befinden. Wenn mehrere Synchronisierungsregeln mit Verknüpfungsregeln für ein Objekt gefunden werden, wird ein Fehler ausgelöst. Aus diesem Grund besteht die bewährte Methode darin, nur eine Synchronisierungsregel mit definierter Verknüpfung zu besitzen, wenn sich mehrere Synchronisierungsregeln im Gültigkeitsbereich eines Objekts befinden. In der standardmäßigen Konfiguration für Azure AD Connect können diese Regeln durch eine Suche nach den Namen ermittelt werden, die das Wort "Join" am Ende des Namens enthalten. Eine Synchronisierungsregel ohne definierte Verknüpfungsregeln wendet die Attributflüsse an, wenn eine andere Synchronisierungsregel die Objekte verknüpft oder ein neues Objekt im Ziel bereitgestellt hat.

###Transformationen
Der Transformationsbereich definiert alle Attributflüsse, die sich auf das Zielobjekt beziehen, wenn die Objekte verknüpft werden und der Bereichsfilter zutrifft. Wenn wir zur Synchronisierungsregel "In von AD – User AccountEnabled" zurückkehren, finden wir die folgenden Transformationen:

<center>![Verknüpfungsregel](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

Zur Orientierung: In einer Kontoressourcen-Gesamtstrukturbereitstellung erwarten wir, ein aktiviertes Konto in der Kontogesamtstruktur und ein deaktiviertes Konto in der Ressourcengesamtstruktur mit Exchange- und Lync-Einstellungen vorzufinden. Die von uns betrachtete Synchronisierungsregel enthält die für die Anmeldung erforderlichen Attribute und diese sollen von der Gesamtstruktur übermittelt werden, in der wir ein aktiviertes Konto gefunden haben. All diese Attributflüsse werden in einer Synchronisierungsregel kombiniert. Eine Transformation kann unterschiedliche Typen aufweisen: "Constant", "Direct" und "Expression". Ein konstanter Ablauf übermittelt immer einen bestimmten Wert. Im obigen Fall wird im Metaverse-Attribut "accountEnabled" immer der Wert "True" festgelegt. Ein direkter Ablauf übermittelt den Wert des Attributs der Quelle an das Zielattribut. Der dritte Ablauftyp ist "Expression", der fortgeschrittenere Konfigurationen gestattet. Die Ausdruckssprache ist VBA (Visual Basic for Applications), daher sollten Benutzer, die über Microsoft Office- oder VBScript-Kenntnisse verfügen, mit diesem Format vertraut sein. Attribute werden in eckige Klammern eingeschlossen: "[Attributname]". Bei Attributnamen und Funktionsnamen wird die Groß-/Kleinschreibung berücksichtigt, aber der Synchronisierungsregel-Editor wertet die Ausdrücke aus und zeigt eine Warnung an, wenn der Ausdruck nicht zulässig ist. Alle Ausdrücke werden in einer einzelnen Zeile mit geschachtelten Funktionen angegeben. Zur Veranschaulichung der Leistung der Konfigurationssprache folgt hier der Ablauf für "pwdLastSet", jedoch mit zusätzlich eingefügten Kommentaren:

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

Das Thema der Transformation ist umfangreich und bietet einen großen Teil der benutzerdefinierten Konfiguration, die mit Azure AD Connect möglich ist. Die benutzerdefinierte Konfiguration wird in dieser Übersicht nicht behandelt, aber wir betrachten später in diesem Dokument einige weitere Attribute.
 

<!---HONumber=August15_HO6-->
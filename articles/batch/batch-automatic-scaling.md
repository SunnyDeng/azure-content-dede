
<properties
	pageTitle="Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool | Microsoft Azure"
	description="Aktivieren Sie das automatische Skalieren in einem Cloudpool, um die Anzahl von Computeknoten im Pool dynamisch anzupassen."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="08/26/2015"
	ms.author="davidmu"/>

# Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool

Das automatische Skalieren von Computeknoten in einem Azure Batch-Pool ist die dynamische Anpassung der Verarbeitungsleistung, die von der Anwendung beansprucht wird. Durch diese einfache Anpassung sparen Sie Zeit und Geld. Weitere Informationen zu Computeknoten und Pools finden Sie unter [Azure Batch – Technische Übersicht](batch-technical-overview.md).

Automatisches Skalieren findet statt, wenn diese Funktion für einen Pool aktiviert und dem Pool eine Formel zugeordnet ist. Die Formel wird verwendet, um die Anzahl der Computeknoten zu bestimmen, die erforderlich sind, um die Anwendung zu bearbeiten. Unter Bezugnahme auf Samplewerte, die regelmäßig erfasst werden, wird die Anzahl der verfügbaren Computeknoten im Pool alle 15 Minuten auf Basis der zugeordneten Formel angepasst.

Automatisches Skalieren kann beim Erstellen eines Pools festgelegt oder später für einen bereits vorhandenen Pool aktiviert werden. Die Formel kann in einem Pool, in dem automatisches Skalieren zuvor aktiviert wurde, auch nachträglich aktualisiert werden. Es empfiehlt sich stets, eine Formel vor der Zuweisung zu einem Pool auszuwerten, und es ist wichtig, den Status der Durchläufe des automatischen Skalierens zu überwachen. Auf diese Themen wird weiter unten näher eingegangen.

> [AZURE.NOTE]Jedes Azure Batch-Konto ist auf eine bestimmte Anzahl von Computeknoten beschränkt, die für die Verarbeitung verwendet werden können. Das System erstellt Knoten nur bis zu diesem Limit und erreicht daher möglicherweise nicht die durch eine Formel vorgegebene Anzahl.

## Automatisches Skalieren von Computeressourcen

Die von Ihnen definierten Skalierungsformeln bestimmen die Anzahl der im nächsten Verarbeitungsintervall verfügbaren Computeknoten in einem Pool. Bei einer Formel für das automatische Skalieren handelt es sich einfach um einen Zeichenfolgenwert, der dem [AutoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx)-Element eines Pools in einem Anforderungstext (REST-API) oder der [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx)-Eigenschaft (.NET-API) eines Pools zugewiesen ist. Diese Formelzeichenfolge darf eine Größe von 8 KB nicht überschreiten und kann bis zu 100 durch Semikolons getrennte Anweisungen sowie Zeilenumbrüche und Kommentare enthalten.

Die Anweisungen in einer Formel sind frei gebildete Ausdrücke. Sie können vom System definierte Variablen, benutzerdefinierte Variablen, konstante Werte und unterstützte Vorgänge für diese Variablen oder Konstanten enthalten.

	VAR = Expression(system-defined variables, user-defined variables);

Komplexe Formeln werden mithilfe mehrerer Anweisungen und Variablen erstellt:

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]Eine Formel für das automatische Skalieren besteht aus [Batch-REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)-API-Variablen, -Typen, -Vorgängen und -Funktionen. Diese werden auch dann in Formelzeichenfolgen verwendet, wenn Sie mit der [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)-Bibliothek arbeiten.

### Variablen

In einer Formel können sowohl vom System definierte als auch benutzerdefinierte Variablen verwendet werden.

Sie können den Wert dieser **vom System definierten Variablen** *abrufen* und *festlegen*, um die Anzahl der Computeknoten in einem Pool zu verwalten.

<table>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Die vorgegebene Anzahl dedizierter Computeknoten für den Pool. Der Wert kann anhand der tatsächlichen Anforderungen laufender Aufgaben geändert werden.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>Dieser Vorgang wird ausgeführt, wenn Computeknoten aus einem Pool entfernt werden. Mögliche Werte:
      <br/>
      <ul>
        <li><p><b>requeue</b>: Aufgaben sofort beenden und wieder in die Auftragswarteschlange einfügen, damit sie neu geplant werden.</p></li>
        <li><p><b>terminate</b>: Aufgaben sofort beenden und aus der Auftragswarteschlange entfernen.</p></li>
        <li><p><b>taskcompletion</b>: auf derzeit ausgeführte Aufgaben warten und den Knoten dann aus dem Pool entfernen.</p></li>
        <li><p><b>retaineddata</b>: warten, bis alle lokal vorgehaltenen Aufgabendaten des Pools gelöscht wurden, bevor der Knoten aus dem Pool gelöscht wird.</p></li>
      </ul></td>
   </tr>
</table>

Sie können die Werte dieser **vom System definierten Variablen** *abrufen*, um anhand der Metriken aus den Samplewerten der Computeknoten Anpassungen vorzunehmen. Diese Variablen sind schreibgeschützt.

<table>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>Die durchschnittliche prozentuale CPU-Auslastung</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>Die Anzahl der verbrauchten Sekunden</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>Die durchschnittliche Anzahl genutzter Megabyte</td>
  <tr>
    <td>$DiskBytes</td>
    <td>Die durchschnittliche Anzahl der auf den lokalen Datenträgern genutzten Gigabyte</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>Die Anzahl der gelesenen Byte</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>Die Anzahl der geschriebenen Byte</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>Die Anzahl der Datenträger-Operationen</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>Die Anzahl der ausgeführten Datenträger-Schreibvorgänge</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>Die Anzahl der eingehenden Byte</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>Die Anzahl der ausgehenden Byte</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>Die Anzahl der Computeknoten</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>Die Anzahl der Aufgaben, die sich in einem aktiven Zustand befinden</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>Die Anzahl der Aufgaben, die sich in einem Ausführungszustand befinden</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>Die Anzahl der Aufgaben, die erfolgreich abgeschlossen wurden</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>Die Anzahl der Aufgaben, bei denen Fehler aufgetreten sind</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>Die aktuelle Anzahl der zugewiesenen Computeknoten</td>
  </tr>
</table>

### Typen

Folgende **Typen** werden in Formeln unterstützt.

- double
- doubleVec
- string
- timestamp – "timestamp" ist eine Verbundstruktur, die folgende Member enthält:
	- year
	- Monat (1-12)
	- Tag (1-31)
	- weekday (als Zahl, z. B. 1 für Montag)
	- hour (im 24-Stunden-Format, z. B. 13 für 13 Uhr)
	- Minute (00-59)
	- Sekunde (00-59)
- timeInterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

### Vorgänge

Folgende **Vorgänge** sind für die oben aufgeführten Typen zulässig.

<table>
  <tr>
    <th>Vorgang</th>
    <th>Zulässige Operatoren</th>
  </tr>
  <tr>
    <td>double &lt;Operator> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;Operator> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;Operator> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;Operator> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;Operator> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;Operator> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;Operator> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;Operator> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;Operator> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;Operator>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;Operator>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;Operator> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;Operator> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;Operator> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;Operator> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;Operator> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>Testen nur double (nicht null ist True, null ist False)</td>
    <td>? :</td>
  </tr>
</table>

### Funktionen

Folgende vordefinierte **Funktionen** stehen zum Definieren einer Formel für das automatische Skalieren zur Verfügung.

<table>
  <tr>
    <th>Funktion</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>Der Durchschnittswert aller Werte in der doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>Die Länge des Vektors, der aus der doubleVecList erstellt wurde.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Logarithmus zur Basis 2.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Komponentenweiser Logarithmus zur Basis 2. Ein vec(double) muss explizit für einzelne double-Parameter übergeben werden, sonst wird implizit die Version double lg(double) verwendet.</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Natürlicher Logarithmus.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Komponentenweiser Logarithmus zur Basis 2. Ein vec(double) muss explizit für einzelne double-Parameter übergeben werden, sonst wird implizit die Version double lg(double) verwendet.</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Logarithmus zur Basis 10.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Komponentenweiser Logarithmus zur Basis 10. Ein vec(double) muss explizit für einzelne double-Parameter übergeben werden, sonst wird die Version double log(double) Version verwendet.</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>Der maximale Wert in der doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>Der minimale Wert in der doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>Die Zweiernorm des Vektors, der aus der doubleVecList erstellt wurde.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>Das Perzentil-Element des Vektors v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Ein Zufallswert zwischen 0,0 und 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>Der Unterschied zwischen dem Minimal- und Maximalwert in doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>Die Stichproben-Standardabweichung der Werte in der doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Beendet die Auswertung des Ausdrucks für das automatische Skalieren.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>Die Summe aller Komponenten von DoubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>Der Zeitstempel der aktuellen Zeit, wenn keine Parameter übergeben werden, oder andernfalls der Zeitstempel der DateTime-Zeichenfolge. Unterstützte dateTime-Formate sind W3CDTF und RFC1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>Der Wert des Elements an Position i im Vektor v mit einem Anfangsindex von 0.</td>
  </tr>
</table>

Einige der in der obigen Tabelle beschriebenen Funktionen akzeptieren eine Liste als Argument. Die durch Komma getrennte Liste ist eine beliebige Kombination aus *double* und *doubleVec*. Beispiel:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

Der *doubleVecList*-Wert wird vor der Auswertung in einen einzelnen *doubleVec* konvertiert. Falls beispielsweise v = [1, 2, 3] ist, dann entspricht der Aufruf avg(v) dem Aufruf avg(1,2,3), und avg (v, 7) entspricht dem Aufruf avg(1,2,3,7).

### Abrufen von Sampledaten

Die oben beschriebenen, vom System definierten Variablen sind Objekte, die Methoden für den Zugriff auf die zugeordneten Daten bereitstellen. Der folgende Ausdruck zeigt z. B. eine Anforderung zum Abrufen der letzten fünf Minuten der CPU-Auslastung:

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Folgende Methoden können zum Abrufen von Sampledaten verwendet werden.

<table>
  <tr>
    <th>Methode</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>Liefert die Gesamtzahl der Samplewerte im Metrikverlauf zurück.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Liefert einen Vektor aus Samplewerten zurück. Beispiel:</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: legt die Anzahl der Samplewerte fest, die von der jüngsten Datenerfassung benötigt werden.</p>
				  <p>Eine Datenerfassung entspricht 5&#160;Sekunden Metrikdaten. GetSample(1) gibt den letzten verfügbaren Samplewert zurück, aber Sie sollten diese Funktion nicht für Metriken wie $CPUPercent verwenden, da nicht festgestellt werden kann, wann der Samplewert erfasst wurde. Möglicherweise ist der Wert aktuell, er kann jedoch aufgrund von Systemeigenschaften auch deutlich älter sein. Es ist besser, ein Zeitintervall zu verwenden, wie unten dargestellt.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: legt einen Zeitrahmen für das Erfassen von Beispieldaten und optional den Prozentsatz der Samplewerte fest, die im angeforderten Bereich liegen müssen.</p>
          <p>$CPUPercent.GetSample(TimeInterval\_Minute*10), sollte 200&#160;Samplewerte zurückliefern, wenn alle Samplewerte der letzten zehn Minuten im CPUPercent-Verlauf vorhanden sind. Wenn der Verlauf der letzten Minute noch nicht vorhanden ist, werden nur 180&#160;Samplewerte zurückgegeben.</p>
					<p>$CPUPercent.GetSample (TimeInterval\_Minute\ * 10, 80) wird erfolgreich ausgeführt, $CPUPercent.GetSample(TimeInterval_Minute*10, 95) schlägt fehl.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: legt einen Zeitrahmen für die Datenerfassung mit einer Startzeit und Endzeit fest.</p></li></ul>
		  <p>Beachten Sie, dass zwischen dem Erfassen eines Samplewerts und dessen Verfügbarkeit für eine Formel eine Verzögerung besteht. Dies muss beim Verwenden der GetSample-Methode berücksichtigt werden (siehe "GetSamplePercent" unten).</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Liefert den Zeitraum der Samplingwerte in einem vergangenen Sample-DataSet zurück.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Liefert den Zeitstempel des ältesten verfügbaren Samplewerts für die Metrik zurück.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Gibt den Prozentsatz der Samplewerte zurück, die ein Verlauf derzeit für ein bestimmtes Zeitintervall enthält. Beispiel:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Da die GetSample-Methode fehlschlägt, wenn der Prozentsatz der Samplewerte geringer ist als der angegebene samplePercent-Wert, können Sie mithilfe der GetSamplePercent-Methode die Anzahl zunächst prüfen und dann eventuell eine andere Aktion ausführen, wenn nicht genug Samplewerte vorhanden sind, ohne die Auswertung für das automatische Skalieren anzuhalten.</p></td>
  </tr>
</table>

### Metriken

Sie können beim Definieren einer Formel sowohl Ressourcen- als auch Aufgabenmetriken verwenden und mithilfe dieser **Metriken** die Computeknoten in einem Pool verwalten.

<table>
  <tr>
    <th>Metrik</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>Ressource</td>
    <td><p>Ressourcenmetriken basieren auf CPU-Auslastung, Bandbreitenauslastung, Speicherauslastung und der Anzahl von Computeknoten. Folgende vom System definierten Variablen (oben unter **Variablen** beschrieben) werden in Formeln verwendet, um die Computeknoten in einem Pool zu verwalten:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Folgende vom System definierten Variablen werden für Anpassungen anhand von Ressourcenmetriken für den Knoten verwendet:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td>Aufgabe</td>
    <td><p>Anhand des Aufgabenstatus, beispielsweise "Aktiv", "Ausstehend" oder "Abgeschlossen".</p>
    <p>Folgende vom System definierten Variablen werden für Anpassungen anhand von Aufgabenmetriken verwendet:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## Erstellen einer Formel für das automatische Skalieren

Zum Erstellen einer Formel für das automatische Skalieren formulieren Sie mithilfe der oben aufgeführten Komponenten Anweisungen und kombinieren diese zu einer vollständigen Formel. In diesem Beispiel wird eine Formel erstellt, indem wir zunächst die Anforderungen für die Formel wie folgt definieren:

1. Die vorgegebene Anzahl von Computeknoten in einem Pool soll erhöht werden, wenn die CPU-Auslastung hoch ist.
2. Die vorgegebene Anzahl von Computeknoten in einem Pool soll reduziert werden, wenn die CPU-Auslastung gering ist.
3. Die maximale Anzahl von Knoten soll immer auf 400 beschränkt sein.

Für die *Erhöhung* der Anzahl von Knoten bei hoher CPU-Auslastung definieren wir eine Anweisung, die eine benutzerdefinierte Variable ($TotalNodes) mit einem Wert auffüllt, der 110 % der aktuellen Knotenanzahl darstellt, wenn die minimale durchschnittliche CPU-Auslastung in den vergangenen 10 Minuten über 70 % lag:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

Die nächste Anweisung legt dieselbe Variable auf 90 % der aktuellen Knotenanzahl fest, wenn die durchschnittliche CPU-Nutzung in den vergangenen 60 Minuten *unter* 20 % lag, damit die vorgegebene Anzahl von Knoten bei geringer CPU-Auslastung gesenkt wird. Beachten Sie, dass diese Anweisung ebenfalls auf die benutzerdefinierte Variable *$TotalNodes* aus der obigen Anweisung verweist.

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

Beschränken wir nun die vorgegebene Anzahl dedizierter Computeknoten auf **maximal** 400:

	$TargetDedicated = min(400, $TotalNodes)

Die vollständige Formel lautet:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## Erstellen eines Pools mit aktiviertem automatischen Skalieren

Zum Aktivieren des automatischen Skalierens beim Erstellen eines Pools verwenden Sie eines der folgenden Verfahren:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): Dieses Azure PowerShell-Cmdlet gibt die Formel für das automatische Skalieren mit dem AutoScaleFormula-Parameter an.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): Nachdem diese .NET-Methode zum Erstellen eines Pools aufgerufen wurde, legen Sie die [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx)-Eigenschaft und die [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx)-Eigenschaft für den Pool fest, um das automatische Skalieren zu aktivieren.
- [Hinzufügen eines Pools zu einem Konto](https://msdn.microsoft.com/library/azure/dn820174.aspx): Das enableAutoScale-Element und das autoScaleFormula-Element werden in dieser REST-API-Anforderung verwendet, um beim Erstellen des Pools das automatische Skalieren für diesen einzurichten.

> [AZURE.NOTE]Wenn Sie beim Erstellen eines Pools mithilfe eines der oben genannten Verfahren das automatische Skalieren einrichten, wird der *targetDedicated*-Parameter für den Pool nicht angegeben (und darf auch nicht angegeben werden). Beachten Sie auch, dass Sie für eine manuelle Anpassung der Größe eines für das automatische Skalieren aktivierten Pools (z. B. mit [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx)) zunächst das automatische Skalieren deaktivieren müssen, bevor Sie die Größe des Pools ändern können.

Der folgende Codeausschnitt zeigt das Erstellen eines für das automatische Skalieren aktivierten [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) mithilfe der [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)-Bibliothek, dessen Formel die vorgegebene Anzahl von Knoten für Montage auf 5 und für alle anderen Wochentage auf 1 festlegt. Im Codeausschnitt ist "myBatchClient" eine ordnungsgemäß initialisierte Instanz von [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx):

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## Aktivieren des automatischen Skalierens nach der Erstellung eines Pools

Wenn Sie bereits anhand des *targetDedicated*-Parameters einen Pool mit einer festgelegten Anzahl von Computeknoten eingerichtet haben, können Sie den vorhandenen Pool zu einem späteren Zeitpunkt so aktualisieren, dass automatisch skaliert wird. Gehen Sie dazu auf eine der beiden folgenden Arten vor:

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx): Diese .NET-Methode erfordert die ID eines vorhandenen Pools und die auf den Pool anzuwendende Formel für das automatische Skalieren.
- [Enable automatic scaling on a pool](https://msdn.microsoft.com/library/azure/dn820173.aspx) (Aktivieren des automatischen Skalierens für einen Pool, in englischer Sprache): Diese REST-API-Anforderung erfordert die ID des vorhandenen Pools im URI und die Formel für das automatische Skalieren im Anforderungstext.

> [AZURE.NOTE]Wenn beim Erstellen des Pools für den *targetDedicated*-Parameter ein Wert angegeben wurde, wird dieser beim Auswerten der Formel für das automatische Skalieren ignoriert.

Dieser Codeausschnitt zeigt, wie das automatische Skalieren mithilfe der [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)-Bibliothek für einen vorhandenen Pool aktiviert wird. Beachten Sie, dass sowohl für das Aktivieren als auch für das Aktualisieren der Formel für einen vorhandenen Pool dasselbe Verfahren verwendet wird. Wenn das automatische Skalieren bereits aktiviert wurde, wird mithilfe des Verfahrens die Formel für den angegebenen Pool *aktualisiert*. In dem Codeausschnitt wird davon ausgegangen, dass "myBatchClient" eine ordnungsgemäß initialisierte Instanz von [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) und "mypool" die ID eines vorhandenen [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) ist.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Auswerten der Formel für das automatische Skalieren

Es ist stets empfehlenswert, eine Formel auszuwerten, bevor Sie sie in Ihrer Anwendung verwenden. Die Formel wird ausgewertet, indem Sie einen Testlauf der Formel für einen vorhandenen Pool durchführen. Verwenden Sie hierzu Folgendes:

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) oder [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx): Diese .NET-Methoden erfordern die ID eines vorhandenen Pools sowie die Zeichenfolge mit der Formel für das automatische Skalieren. Die Ergebnisse des Aufrufs befinden sich in einer Instanz der [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx)-Klasse.
- [Evaluate an automatic scaling formula](https://msdn.microsoft.com/library/azure/dn820183.aspx) (Auswerten einer Formel für das automatische Skalieren, in englischer Sprache): Bei dieser REST-API-Anforderung wird die Pool-ID im URI und die Formel für das automatische Skalieren im *autoScaleFormula*-Element des Anforderungstexts angegeben. Die bei der Anfrage generierte Antwort enthält Fehlerinformationen, die in Zusammenhang mit der Formel stehen können.

> [AZURE.NOTE]Damit Sie eine Formel für das automatische Skalieren auswerten können, müssen Sie zuerst das automatische Skalieren für den Pool mithilfe einer gültigen Formel aktivieren.

In diesem Codeausschnitt, der die [Batch-Bibliothek für .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) verwendet, werten wir eine Formel aus, bevor diese auf den [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) angewendet wird.

		// First obtain a reference to the existing pool
		CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

		// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
		if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
		{
			// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
			string myFormula = @"
				$CurTime=time();
				$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
				$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
				$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
				$TargetDedicated=$IsWorkingWeekdayHour?20:10;
			";

			// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
			// the pool.
			AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

			if (eval.AutoScaleRun.Error == null)
			{
				// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
				// variable as evaluated by the the autoscaling formula.
				Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

				// Apply the formula to the pool since it evaluated successfully
				client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
			}
			else
			{
				// Evaluation failed, output the message associated with the error
				Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
			}
		}

Eine erfolgreiche Auswertung der Formel in diesem Codeausschnitt sollte eine Ausgabe ähnlich der folgenden ergeben:

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## Abrufen von Informationen über Durchläufe des automatischen Skalierens

Die Ergebnisse der Durchläufe des automatischen Skalierens sollten in regelmäßigen Abständen überprüft werden, damit Sie sicher sein können, dass die Formel wie erwartet funktioniert. Gehen Sie dazu auf eine der beiden folgenden Arten vor:

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): Bei Verwendung der .NET-Bibliothek stellt diese Eigenschaft eines Pools eine Instanz der [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx)-Klasse bereit, die folgende Eigenschaften für den neuesten Durchlauf des automatischen Skalierens zur Verfügung stellt:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Get information about a pool](https://msdn.microsoft.com/library/dn820165.aspx) (Abrufen von Informationen zu einem Pool, in englischer Sprache): Diese REST-API-Anforderung gibt Informationen zum Pool zurück, einschließlich des neuesten Durchlaufs des automatischen Skalierens.

## Beispielformeln

Die folgenden Beispiele zeigen mehrere Möglichkeiten auf, wie Computeressourcen in einem Pool mithilfe von Formeln automatisch skaliert werden können.

### Beispiel 1

Möglicherweise möchten Sie die Größe des Pools basierend auf Wochentag und Uhrzeit anpassen und die Anzahl der Knoten im Pool entsprechend erhöhen bzw. reduzieren:

		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;

Diese Formel ruft zunächst die aktuelle Uhrzeit ab. Wenn es sich um einen Wochentag (1 bis 5) handelt und der Wert innerhalb der Geschäftszeiten (8:00 Uhr bis 18:00 Uhr) liegt, wird die Zielgröße des Pools auf 20 Knoten festgelegt. Andernfalls wird die Poolgröße auf 10 Knoten festgelegt.

### Beispiel 2

In diesem Beispiel wird die Größe des Pools basierend auf der Anzahl der Aufgaben in der Warteschlange angepasst. Beachten Sie, dass in Formelzeichenfolgen sowohl Kommentare als auch Zeilenumbrüche verwendet werden können.

	    // Get pending tasks for the past 15 minutes.
	    $Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
	    // If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
		// last sample point and the history average.
	    $Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
	    // If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
	    $TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
	    // The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
		// should be adjusted according to your use case.
	    $TargetDedicated = max(0,min($TargetVMs,20));
	    // Set node deallocation mode - keep nodes active only until tasks finish
	    $NodeDeallocationOption = taskcompletion;

### Beispiel 3

In diesem Beispiel wird die Größe des Pools ebenfalls basierend auf der Anzahl der Aufgaben angepasst. Diese Formel berücksichtigt jedoch darüber hinaus den [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx)-Wert, der für den Pool festgelegt wurde. Dies ist besonders nützlich in Situationen, in denen eine parallele Ausführung von Aufgaben auf Computeknoten gewünscht wird.

		// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
		$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
		$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
		// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
		// property on this pool is set to 4, adjust this number for your use case)
		$Cores = $TargetDedicated * 4;
		$ExtraVMs = ($Tasks - $Cores) / 4;
		$TargetVMs = ($TargetDedicated+$ExtraVMs);
		// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
		$TargetDedicated = max(0,min($TargetVMs,3));
		// Keep the nodes active until the tasks finish
		$NodeDeallocationOption = taskcompletion;

## Nächste Schritte

1. Möglicherweise müssen Sie auf einen Computeknoten zugreifen, um die Effizienz der Anwendung vollständig bewerten zu können. Um den Remotezugriff nutzen zu können, muss dem Knoten, auf den zugegriffen werden soll, ein Benutzerkonto hinzugefügt werden, und es muss eine RDP-Datei für den Knoten abgerufen werden.
    - Fügen Sie das Benutzerkonto auf eine der folgenden Arten hinzu:
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): Dieses PowerShell-Cmdlet verwendet den Poolnamen, den Namen des Computeknotens, den Kontonamen und das Kennwort als Parameter.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx): Diese .NET-Methode erstellt eine Instanz der [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx)-Klasse, in der Kontoname und Kennwort für den Computeknoten festgelegt werden können. Anschließend wird auf der Instanz [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) aufgerufen, um den Benutzer auf dem Knoten zu erstellen.
        * [Add a user account to a node](https://msdn.microsoft.com/library/dn820137.aspx) (Hinzufügen eines Benutzerkontos zu einem Knoten, in englischer Sprache): Der Name des Pools und des Computeknotens werden im URI angegeben. Der Kontoname und das Kennwort werden im Anforderungstext dieser REST-API-Anforderung an den Knoten gesendet.
    - Abrufen der RDP-Datei:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx): Diese .NET-Methode erfordert die ID des Pools, die Knoten-ID und den Namen der zu erstellenden RDP-Datei.
        * [Get a remote desktop protocol file from a node](https://msdn.microsoft.com/library/dn820120.aspx) (Abrufen einer RDP-Datei von einem Knoten, in englischer Sprache): Diese REST-API-Anforderung erfordert den Namen des Pools und den Namen des Computeknotens. Die Antwort enthält den Inhalt der RDP-Datei.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): Dieses PowerShell-Cmdlet ruft die RDP-Datei aus dem angegebenen Computeknoten ab und speichert sie am festgelegten Speicherort oder in einen Stream.
2.	Einige Anwendungen erzeugen große Datenmengen, die nur schwer zu verarbeiten sind. Eine Möglichkeit zur Lösung dieses Problems ist die Verwendung [effizienter Listenabfragen](batch-efficient-list-queries.md).

<!---HONumber=Oct15_HO3-->
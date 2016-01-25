
<properties
	pageTitle="Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool | Microsoft Azure"
	description="Aktivieren Sie das automatische Skalieren in einem Cloudpool, um die Anzahl von Computeknoten im Pool dynamisch anzupassen."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="01/08/2015"
	ms.author="marsma"/>

# Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool

Die automatische Skalierung in Azure Batch ist die automatische Anpassung der Verarbeitungskapazität Ihrer Anwendung, indem Computeknoten während der Ausführung eines Auftrags dynamisch hinzugefügt oder entfernt werden. Dank dieser automatischen Anpassung sparen Sie Zeit und Geld.

Die automatische Skalierung wird für einen Pool von Computeknoten aktiviert, indem eine *Formel für automatische Skalierung* dem Pool zugeordnet wird, wie z. B. mithilfe der [PoolOperations.EnableAutoScale][net_enableautoscale]-Methode in der [Batch .NET](batch-dotnet-get-started.md)-Bibliothek. Der Batch-Dienst verwendet dann diese Formel, um die Anzahl von Computeknoten zu bestimmen, die zum Ausführen des Workloads erforderlich sind. Unter Bezugnahme auf Stichprobenwerte von Dienstmetriken, die regelmäßig erfasst werden, wird die Anzahl von Computeknoten im Pool auf Basis der zugeordneten Formel in festlegbaren Abständen angepasst.

Die automatische Skalierung kann bei der Erstellung eines Pools oder für einen vorhandenen Pool aktiviert werden. Sie können auch für einen Pool, für den die automatische Skalierung bereits aktiviert ist, die zugewiesene Formel ändern. Der Batch-Dienst bietet die Möglichkeit, die Formeln auszuwerten, bevor sie Pools zugewiesen werden, und den Status automatischer Skalierungen zu überwachen.

## Formeln für die automatische Skalierung

Bei einer Formel für das automatische Skalieren handelt es sich um einen Zeichenfolgenwert, der dem [autoScaleFormula][rest_autoscaleformula]-Element eines Pools (REST-API von Batch) oder der [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula]-Eigenschaft (.NET-API von Batch) eines Pools zugewiesen ist. Wenn die von Ihnen definierten Formeln einem Pool zugewiesen werden, bestimmen sie für das nächste Verarbeitungsintervall die Anzahl der in einem Pool verfügbaren Computeknoten (mehr zu Intervallen weiter unten). Die Formelzeichenfolge darf eine Größe von 8 KB nicht überschreiten und kann bis zu 100 durch Semikolons getrennte Anweisungen sowie Zeilenumbrüche und Kommentare enthalten.

Sie können sich Formeln für die automatische Skalierung als eine Batch-„Sprache“ für die automatische Skalierung vorstellen. Formelanweisungen sind frei definierte Ausdrücke, die vom System und vom Benutzer definierte Variablen sowie Konstanten enthalten können. Mithilfe vordefinierter Typen, Operatoren und Funktionen können verschiedene Operationen auf diese Werte angewendet werden. Eine Anweisung kann beispielsweise wie folgt aussehen:

`VAR = Expression(system-defined variables, user-defined variables);`

Formeln enthalten im Allgemeinen mehrere Anweisungen, die Operationen auf Werte anwenden, die in vorherigen Anweisungen abgerufen wurden:

```
VAR₀ = Expression₀(system-defined variables);
VAR₁ = Expression₁(system-defined variables, VAR₀);
```

Unter Verwendung der Anweisungen in Ihrer Formel ist es Ihr Ziel, die Anzahl von Computeknoten zu bestimmen, auf die der Pool skaliert werden soll, d. h. die **Zielanzahl** **dedizierter Knoten**. Diese dedizierte Zielanzahl ist ggf. höher oder niedriger als bzw. identisch mit der aktuellen Anzahl von Knoten im Pool. Der Batch-Dienst wertet die Formel für die automatische Skalierung in einem bestimmten Intervall aus ([Intervalle für die automatische Skalierung](#interval) werden weiter unten besprochen). Anschließend wird die Anzahl von Zielknoten im Pool an den Wert angepasst, den Ihre Formel für die automatische Skalierung zum Auswertungszeitpunkt ausgibt.

Kurzbeispiel: Diese zweizeilige Formel für die automatische Skalierung gibt an, dass die Anzahl von Knoten gemäß der Anzahl von aktiven Aufgaben bis zu einem Höchstwert von 10 Computeknoten angepasst werden soll:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

In den nächsten Abschnitten dieses Artikels werden die verschiedenen Entitäten erläutert, mit denen Ihre Formeln für die automatische Skalierung gebildet werden. Dazu zählen Variablen, Operatoren, Operationen und Funktionen. Sie erfahren, wie Sie in Batch verschiedene Computeressourcen und Aufgabenmetriken abrufen, damit Sie die Knotenanzahl in Ihrem Pool basierend auf Ressourcennutzung und Aufgabenstatus intelligent anpassen können. Sie erfahren dann, wie Sie eine Formel erstellen und die automatische Skalierung für einen Pool aktivieren, wozu Sie sowohl die REST-API als auch die .NET-API von Batch verwenden. Zum Abschluss werden verschiedene Beispielformeln gezeigt.

> [AZURE.NOTE]Jedes Azure Batch-Konto ist auf eine bestimmte Anzahl von Computeknoten beschränkt, die für die Verarbeitung verwendet werden können. Der Batch-Dienst erstellt Knoten nur bis zu diesem Limit und erreicht daher möglicherweise nicht die durch eine Formel vorgegebene Anzahl. Unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md) finden Sie Informationen zum Anzeigen und Erhöhen von Kontokontingenten.

## <a name="variables"></a>Variablen

In Formeln für die automatische Skalierung können sowohl vom System definierte als auch benutzerdefinierte Variablen verwendet werden. In der obigen zweizeiligen Beispielformel ist `$TargetDedicated` eine vom System definierte Variable, `$averageActiveTaskCount` ist benutzerdefiniert. Die folgenden Tabellen enthalten sowohl Variablen mit Lese-/Schreibzugriff als auch schreibgeschützte Variablen, die vom Batch-Dienst definiert werden.

Mithilfe von *get* und *set* können Sie diese **vom System definierten Variablen** abrufen und festlegen, um die Anzahl von Computeknoten in einem Pool zu verwalten:

<table>
  <tr>
    <th>Variablen (Lese-/ Schreibzugriff)</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Die <b>Zielanzahl</b> <b>dedizierter Computeknoten</b> für den Pool. Dies ist die Anzahl von Computeknoten, auf die der Pool skaliert werden soll. Es handelt sich um einen „Zielwert“, da ein Pool die Zielanzahl von Knoten möglicherweise nicht erreicht. Dies ist in folgenden Fällen möglich: 1.) Die Zielanzahl von Knoten wird durch eine nachfolgende Auswertung für die automatische Skalierung erneut geändert, ehe der Pool das anfängliche Ziel erreicht hat, oder 2.) ein Knoten oder Kernkontingent eines Batch-Kontos wird ausgeschöpft, ehe die Zielanzahl von Knoten erreicht wird.</td>
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

Sie können die Werte dieser **vom System definierten Variablen** *abrufen*, um anhand der Metriken des Batch-Diensts Anpassungen vorzunehmen:

<table>
  <tr>
    <th>Variablen (schreibgeschützt)</th>
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

> [AZURE.TIP]Die obigen vom System definierten schreibgeschützten Variablen sind *Objekte*, die verschiedene Methoden für den Zugriff auf ihre dazugehörigen Daten bereitstellen. Weitere Informationen finden Sie weiter unten unter [Erfassen von Stichprobendaten](#getsampledata).

## Typen

Folgende **Typen** werden in Formeln unterstützt.

- double
- doubleVec
- doubleVecList
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

## Vorgänge

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

## Funktionen

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

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Der *doubleVecList*-Wert wird vor der Auswertung in einen einzelnen *doubleVec* konvertiert. Falls beispielsweise `v = [1,2,3]` ist, entspricht der Aufruf `avg(v)` dem Aufruf `avg(1,2,3)`, und `avg(v, 7)` entspricht dem Aufruf `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Erfassen von Stichprobendaten

Formeln für die automatische Skalierung arbeiten mit Metrikdaten in Form von Stichproben, die vom Batch-Dienst bereitgestellt werden. Dabei wird die Poolgröße basierend auf den Werten, die Ihre Formel aus dem Dienst abruft, erhöht oder verringert. Die obigen vom System definierten Variablen sind Objekte, die verschiedene Methoden für den Zugriff auf die zum jeweiligen Objekt gehörigen Daten bereitstellen. Der folgende Ausdruck zeigt z. B. eine Anforderung zum Abrufen der letzten fünf Minuten der CPU-Auslastung:

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>Methode</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Die <b>GetSample()</b>-Methode gibt einen Vektor aus Stichprobenwerten zurück.
	<p>Eine Stichprobe entspricht in 30&#160;Sekunden erfassten Metrikdaten. Stichproben werden als alle 30&#160;Sekunden erfasst, doch wie weiter unten angemerkt, gibt es eine Verzögerung zwischen dem Zeitpunkt der Erfassung einer Stichprobe und ihrer Verfügbarkeit für eine Formel. Daher stehen möglicherweise nicht alle Stichproben für einen bestimmten Zeitraum für die Bewertung durch eine Formel zur Verfügung.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: Gibt die Anzahl der Stichproben an, die aus den letzten erfassten Stichproben abgerufen werden soll.</p>
				  <p>„GetSample(1)“ gibt die letzte verfügbare Stichprobe zurück. Für Metriken wie „$CPUPercent“ sollte diese allerdings nicht verwendet werden, da es unmöglich feststellbar ist, <em>wann</em> die Stichprobe erfasst wurde. Sie kann aktuell oder aber aufgrund von Systemproblemen auch wesentlich älter sein. Es ist besser, wie unten dargestellt, ein Intervall zu verwenden.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: Legt einen Zeitrahmen für das Erfassen von Stichprobendaten und optional den Prozentsatz der Stichproben fest, die im angeforderten Zeitrahmen liegen müssen.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval\_Minute*10)</em> sollte 20&#160;Stichproben zurückgeben, wenn alle Stichproben der letzten zehn Minuten im Verlauf von „CPUPercent“ vorhanden sind. Wenn jedoch die letzte Minute des Verlaufs nicht verfügbar ist, werden nur 18&#160;Stichproben zurückgegeben, was bedeutet, dass:<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> misslingen würde, da nur 90&#160;% der Stichproben verfügbar sind, und<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> Erfolg hätte.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: legt einen Zeitrahmen für die Datenerfassung mit einer Startzeit und Endzeit fest.</p></li></ul>
		  <p>Wie bereits erwähnt, gibt es eine Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichprobe und ihrer Verfügbarkeit für eine Formel. Diese Verzögerung muss bei Verwendung der <em>GetSample</em>-Methode berücksichtigt werden (siehe <em>GetSamplePercent</em> im Anschluss).</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Liefert den Zeitraum der Stichprobenwerte in einem vergangenen Stichproben-Dataset zurück.</td>
  </tr>
	<tr>
		<td>Count()</td>
		<td>Liefert die Gesamtzahl der Stichprobenwerte im Metrikverlauf zurück.</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Liefert den Zeitstempel des ältesten verfügbaren Stichprobenwerts für die Metrik zurück.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Gibt den Prozentsatz an Stichprobenwerten zurück, die für ein bestimmtes Intervall verfügbar sind. Beispiel:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Da die GetSample-Methode nicht erfolgreich ist, wenn der Prozentsatz der Stichprobenwerte geringer ist als der angegebene samplePercent-Wert, können Sie mithilfe der GetSamplePercent-Methode die Anzahl zunächst prüfen. Dann können Sie eventuell eine andere Aktion ausführen, wenn nicht genug Stichprobenwerte vorhanden sind, ohne die Auswertung für das automatische Skalieren anzuhalten.</p></td>
  </tr>
</table>

### Stichproben, Prozentsatz für die Stichprobe und die *GetSample()*-Methode

Das Abrufen von Metrikdaten zu Aufgaben und Ressourcen sowie das Anpassen der Poolgröße basierend auf diesen Daten ist der Hauptzweck einer Formel für die automatische Skalierung. Daher ist es wichtig, sich damit vertraut zu machen, wie Formeln für die automatische Skalierung mit Metrikdaten bzw. „Stichproben“ interagieren.

**Stichproben**

Der Batch-Dienst nimmt regelmäßig *Stichproben* von Aufgaben- und Ressourcenmetriken und stellt diese Ihren Formeln für die automatische Skalierung zur Verfügung. Der Batch-Dienst erfasst diese Stichproben alle 30 Sekunden. Allerdings kommt es meist zu etwas Latenz und dadurch zu einer Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichproben und dem Zeitpunkt, zu dem sie ihren Formeln für die automatische Skalierung zur Verfügung gestellt (und von diesen gelesen) werden können. Darüber hinaus werden Stichproben aufgrund verschiedener Faktoren wie Netzwerk- oder anderer Infrastrukturprobleme für ein bestimmtes Intervall nicht erfasst, was zu „fehlenden“ Stichproben führt.

**Prozentsatz für die Stichprobe**

Beim Übergeben eines `samplePercent`-Werts an die `GetSample()`-Methode oder Aufrufen der `GetSamplePercent()`-Methode bezieht sich „Percent“ auf einen Vergleich zwischen der gesamten *möglichen* Anzahl der vom Batch-Dienst erfassten Stichproben und der Anzahl von Stichproben, die tatsächlich für Ihre Formel für die automatische Skalierung *verfügbar* sind.

Lassen Sie uns als Beispiel eine Zeitspanne von 10 Minuten betrachten. Da Stichproben in einer Zeitspanne von 10 Minuten alle 30 Sekunden erfasst werden, ist 20 die maximale Anzahl der von Batch erfassten Stichproben (zwei pro Minute). Doch aufgrund der inhärenten Latenz des Berichterstellungsmechanismus oder anderer Probleme in der Azure-Infrastruktur stehen Ihrer Formel für die automatische Skalierung möglicherweise nur 15 Stichproben zur Verfügung. Dies bedeutet, dass in diesem zehnminütigen Zeitraum nur **75 %** der möglichen Gesamtanzahl erfasster Stichproben tatsächlich für Ihre Formel verfügbar sind.

**GetSample() und Stichprobenbereiche**

Je nachdem, ob Ihre Formeln für die automatische Skalierung sich vergrößern oder verkleinern, werden Ihren Pools Knoten hinzugefügt oder daraus entfernt. Da Knoten Kosten verursachen, wollen Sie sicherstellen, dass Ihre Formeln basierend auf genügend Daten intelligente Entscheidungen treffen. Aus diesem Grund wird empfohlen, dass Sie eine Trendanalyse auf Ihre Formeln anwenden und Ihre Pools auf Basis eines *Bereichs* erfasster Stichproben vergrößern oder verkleinern.

Verwenden Sie hierzu `GetSample(interval look-back start, interval look-back end)`, um einen **Vektor** von Stichproben zurückzugeben:

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Wenn die obige Zeile von Batch ausgewertet wird, gibt sie einen Bereich von Stichproben als Vektor von Werten zurück. Beispiel:

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

Nachdem Sie den Vektor der Stichproben erfasst haben, können Sie Funktionen wie `min()`, `max()` und `avg()` verwenden, um aussagekräftige Werte aus dem erfassten Bereich abzuleiten.

Aus Sicherheitsgründen können Sie ein *Ausfallen* einer Formelauswertung erzwingen, wenn für einen bestimmten Zeitraum weniger als ein bestimmter Prozentsatz von Stichproben verfügbar ist. Das Erzwingen des Ausfalls einer Formelauswertung weist Batch an, die weitere Auswertung der Formel zu beenden, sofern der angegebene Prozentsatz von Stichproben nicht zur Verfügung steht. An der Größe des Pools erfolgt dann keinerlei Änderung. Wenn Sie einen erforderlichen Prozentsatz von Stichproben anfordern wollen, damit die Auswertung Erfolg hat, geben Sie diesen in `GetSample()` als dritten Parameter an. Hier wird ein Mindestprozentsatz von 75 % angegeben:

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

Aufgrund der zuvor erwähnten Verzögerung bei der Verfügbarkeit von Stichproben müssen Sie stets einen Zeitraum mit einer Startzeit wählen, die mehr als eine Minute zurückliegt. Der Grund ist, dass es ca. eine Minute dauert, bis Stichproben das System durchlaufen haben, weshalb Stichproben im Bereich `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` häufig nicht verfügbar sind. In diesem Fall können Sie den Prozentsatzparameter von `GetSample()` angeben, um einen bestimmten Prozentsatz von Stichproben zu erzwingen.

> [AZURE.IMPORTANT]Es wird **ausdrücklich empfohlen**, sich **nicht *nur* auf `GetSample(1)` in Ihren Formeln für die automatische Skalierung zu verlassen**. Der Grund ist, dass `GetSample(1)` im Wesentlichen den Batch-Dienst anweist, die letzte vorhandene Stichprobe unabhängig vom Zeitpunkt ihrer Erfassung bereitzustellen. Da es sich nur um ein Stichprobe handelt, die ggf. schon älter ist, ist diese möglicherweise nicht für den aktuellen Aufgaben- oder Ressourcenstatus repräsentativ. Wenn Sie `GetSample(1)` verwenden, stellen Sie sicher, dass dieser Wert zu einer längeren Anweisung gehört und nicht der einzige Datenpunkt ist, auf dem Ihre Formel basiert.

## Metriken

Beim Definieren einer Formel können Sie sowohl **Ressourcen-** als auch **Aufgabenmetriken** nutzen, um die Zielanzahl dedizierter Knoten im Pool basierend auf den Metrikdaten anzupassen, die sie abrufen und auswerten. Im Abschnitt [Variablen](#variables) finden Sie weitere Informationen zu den einzelnen Metriken.

<table>
  <tr>
    <th>Metrik</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td><b>Ressource</b></td>
    <td><p><b>Ressourcenmetriken</b> sind Metriken, die auf der CPU, Bandbreite und Arbeitsspeicherauslastung von Computeknoten sowie auf der Anzahl von Knoten basieren.</p>
		<p> Folgende vom System definierten Variablen eignen sich für Anpassungen basierend auf der Knotenanzahl:</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>Diese vom System definierten Variablen eignen sich für Anpassungen basierend auf der Ressourcenverwendung von Knoten:</p>
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
    <td><b>Aufgabe</b></td>
    <td><p><b>Aufgabenmetriken</b> basieren auf dem Aufgabenstatus (wie etwa „Aktiv“, „Ausstehend“ oder „Abgeschlossen“). Folgende vom System definierten Variablen bieten sich für das Anpassen der Poolgröße basierend auf Aufgabenmetriken an:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## Erstellen einer Formel für das automatische Skalieren

Zum Erstellen einer Formel für das automatische Skalieren formulieren Sie mithilfe der oben aufgeführten Komponenten Anweisungen und kombinieren diese zu einer vollständigen Formel. In diesem Beispiel wird eine Formel erstellt, indem wir zunächst die Anforderungen für die Formel wie folgt definieren:

1. Die vorgegebene Anzahl von Computeknoten in einem Pool soll erhöht werden, wenn die CPU-Auslastung hoch ist.
2. Die vorgegebene Anzahl von Computeknoten in einem Pool soll reduziert werden, wenn die CPU-Auslastung gering ist.
3. Die maximale Anzahl von Knoten soll immer auf 400 beschränkt sein.

Für die *Erhöhung* der Anzahl von Knoten bei hoher CPU-Auslastung definieren wir eine Anweisung, die eine benutzerdefinierte Variable ($TotalNodes) mit einem Wert auffüllt, der 110 % der aktuellen Knotenanzahl darstellt, wenn die minimale durchschnittliche CPU-Auslastung in den vergangenen 10 Minuten über 70 % lag:

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

Die nächste Anweisung legt dieselbe Variable auf 90 % der aktuellen Knotenanzahl fest, wenn die durchschnittliche CPU-Nutzung in den vergangenen 60 Minuten *unter* 20 % lag, damit die vorgegebene Anzahl von Knoten bei geringer CPU-Auslastung gesenkt wird. Beachten Sie, dass diese Anweisung ebenfalls auf die benutzerdefinierte Variable *$TotalNodes* aus der obigen Anweisung verweist.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

Beschränken wir nun die vorgegebene Anzahl dedizierter Computeknoten auf **maximal** 400:

`$TargetDedicated = min(400, $TotalNodes)`

Die vollständige Formel lautet:

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE]Eine Formel für das automatische Skalieren besteht aus [Batch-REST][rest_api]-API-Variablen, -Typen, -Vorgängen und -Funktionen. Diese werden auch dann in Formelzeichenfolgen verwendet, wenn Sie mit der [Batch .NET][net_api]-Bibliothek arbeiten.

## Erstellen eines Pools mit aktiviertem automatischen Skalieren

Zum Aktivieren des automatischen Skalierens beim Erstellen eines Pools verwenden Sie eines der folgenden Verfahren:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): Dieses Azure PowerShell-Cmdlet gibt die Formel für das automatische Skalieren mit dem AutoScaleFormula-Parameter an.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): Nachdem diese .NET-Methode zum Erstellen eines Pools aufgerufen wurde, legen Sie die [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx)-Eigenschaft und die [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx)-Eigenschaft für den Pool fest, um das automatische Skalieren zu aktivieren.
- [Hinzufügen eines Pools zu einem Konto](https://msdn.microsoft.com/library/azure/dn820174.aspx): Das enableAutoScale-Element und das autoScaleFormula-Element werden in dieser REST-API-Anforderung verwendet, um beim Erstellen des Pools das automatische Skalieren für diesen einzurichten.

> [AZURE.IMPORTANT]Wenn Sie mithilfe einer der genannten Methoden einen für die automatische Skalierung aktivierten Pool erstellen, darf der *targetDedicated*-Parameter **nicht** für den Pool angegeben werden. Beachten Sie auch, dass Sie für eine manuelle Anpassung der Größe eines für das automatische Skalieren aktivierten Pools (z. B. mit [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]) zunächst das automatische Skalieren **deaktivieren** müssen, bevor Sie die Größe des Pools ändern können.

Der folgende Codeausschnitt zeigt das Erstellen eines für das automatische Skalieren aktivierten [CloudPool][net_cloudpool] mithilfe der [Batch .NET][net_api]-Bibliothek, dessen Formel die vorgegebene Anzahl von Knoten für Montage auf 5 und für alle anderen Wochentage auf 1 festlegt. Darüber hinaus wird das Intervall für die automatische Skalierung auf 30 Minuten festgelegt (siehe [Intervall für die automatische Skalierung](#interval) weiter unten). In diesem und anderen C#-Ausschnitten in diesem Artikel ist „myBatchClient“ eine ordnungsgemäß initialisierte Instanz von [BatchClient][net_batchclient].

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### <a name="interval"></a>Intervall für die automatische Skalierung

Standardmäßig passt der Batch-Dienst die Poolgröße gemäß seiner Formel für die automatische Skalierung alle **15 Minuten** an. Dieses Intervall kann jedoch mithilfe der folgenden Pooleigenschaften konfiguriert werden:

- REST-API – [autoScaleEvaluationInterval][rest_autoscaleinterval]
- .NET-API – [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

Das kürzeste Intervall ist 5 Minuten, das längste 168 Stunden. Wenn ein Intervall außerhalb dieses Bereichs angegeben wird, gibt der Batch-Dienst einen Fehler des Typs „Unzulässige Anforderung (400)“ zurück.

> [AZURE.NOTE]Die automatische Skalierung ist derzeit nicht als Reaktion im Zeitraum unter einer Minute auf Änderungen vorgesehen, sondern dient zum allmählichen Anpassen der Größe Ihres Pools während der Ausführung Ihres Workloads.

## Aktivieren des automatischen Skalierens nach der Erstellung eines Pools

Wenn Sie bereits anhand des *targetDedicated*-Parameters einen Pool mit einer festgelegten Anzahl von Computeknoten eingerichtet haben, können Sie den vorhandenen Pool zu einem späteren Zeitpunkt so aktualisieren, dass automatisch skaliert wird. Gehen Sie dazu auf eine der beiden folgenden Arten vor:

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale]\: Diese .NET-Methode erfordert die ID eines vorhandenen Pools und die auf den Pool anzuwendende Formel für das automatische Skalieren.
- [Enable automatic scaling on a pool][rest_enableautoscale] (Aktivieren des automatischen Skalierens für einen Pool, in englischer Sprache): Diese REST-API-Anforderung erfordert die ID des vorhandenen Pools im URI und die Formel für das automatische Skalieren im Anforderungstext.

> [AZURE.NOTE]Wenn beim Erstellen des Pools für den *targetDedicated*-Parameter ein Wert angegeben wurde, wird dieser beim Auswerten der Formel für das automatische Skalieren ignoriert.

Dieser Codeausschnitt zeigt, wie das automatische Skalieren mithilfe der [Batch .NET][net_api]-Bibliothek für einen vorhandenen Pool aktiviert wird. Beachten Sie, dass sowohl für das Aktivieren als auch für das Aktualisieren der Formel für einen vorhandenen Pool dasselbe Verfahren verwendet wird. Wenn das automatische Skalieren bereits aktiviert wurde, wird mithilfe des Verfahrens die Formel für den angegebenen Pool *aktualisiert*. Der Codeausschnitt nimmt an, dass „mypool“ die ID eines vorhandenen [CloudPool][net_cloudpool] ist.

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

In diesem Codeausschnitt, der die [Batch-Bibliothek für .NET][net_api] verwendet, werten wir eine Formel aus, bevor diese auf den [CloudPool][net_cloudpool] angewendet wird.

```
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
```

Eine erfolgreiche Auswertung der Formel in diesem Codeausschnitt sollte eine Ausgabe ähnlich der folgenden ergeben:

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## Abrufen von Informationen über Durchläufe des automatischen Skalierens

Überprüfen Sie regelmäßig die Ergebnisse der automatischen Skalierungsläufe, um sicherzustellen, dass die Formel wie erwartet funktioniert.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): Bei Verwendung der .NET-Bibliothek stellt diese Eigenschaft eines Pools eine Instanz der [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx)-Klasse bereit, die folgende Eigenschaften für den neuesten Durchlauf des automatischen Skalierens zur Verfügung stellt:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Get information about a pool](https://msdn.microsoft.com/library/dn820165.aspx) (Abrufen von Informationen zu einem Pool, in englischer Sprache): Diese REST-API-Anforderung gibt Informationen zum Pool zurück, einschließlich des neuesten Durchlaufs des automatischen Skalierens.

## <a name="examples"></a>Beispielformeln

Die folgenden Beispiele zeigen mehrere Möglichkeiten auf, wie Computeressourcen in einem Pool mithilfe von Formeln automatisch skaliert werden können.

### Beispiel 1: Zeitbasierte Anpassung

Möglicherweise möchten Sie die Größe des Pools basierend auf Wochentag und Uhrzeit anpassen und die Anzahl der Knoten im Pool entsprechend erhöhen bzw. reduzieren:

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

Diese Formel ruft zunächst die aktuelle Uhrzeit ab. Wenn es sich um einen Wochentag (1 bis 5) handelt und der Wert innerhalb der Geschäftszeiten (8:00 Uhr bis 18:00 Uhr) liegt, wird die Zielgröße des Pools auf 20 Knoten festgelegt. Andernfalls wird die Poolgröße auf 10 Knoten festgelegt.

### Beispiel 2: Aufgabenbasierte Anpassung

In diesem Beispiel wird die Größe des Pools basierend auf der Anzahl der Aufgaben in der Warteschlange angepasst. Beachten Sie, dass in Formelzeichenfolgen sowohl Kommentare als auch Zeilenumbrüche verwendet werden können.

```
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
```

### Beispiel 3: Berücksichtigung paralleler Aufgaben

In diesem Beispiel wird die Größe des Pools ebenfalls basierend auf der Anzahl der Aufgaben angepasst. Diese Formel berücksichtigt jedoch darüber hinaus den [MaxTasksPerComputeNode][net_maxtasks]-Wert, der für den Pool festgelegt wurde. Dies ist besonders nützlich in Situationen, in denen eine [parallele Ausführung von Aufgaben](batch-parallel-node-tasks.md) In Ihrem Pool aktiviert wurde.

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Beispiel 4: Festlegen einer anfänglichen Poolgröße

Dieses Beispiel zeigt einen C#-Codeausschnitt für die automatische Skalierung, die die Poolgröße für einen anfänglichen Zeitraum auf eine bestimmte Anzahl von Knoten festlegt. Anschließend wird die Poolgröße basierend auf der Anzahl ausgeführter und aktiver Aufgaben nach Ablauf des anfänglichen Zeitraums angepasst.

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

Die Formel im obigen Codeausschnitt weist folgende Merkmale auf:

- Die anfängliche Poolgröße wird auf 4 Knoten festgelegt.
- Die Größe des Pools wird innerhalb der ersten 10 Minuten des Lebenszyklus des Pools nicht angepasst.
- Nach 10 Minuten wird die maximale Anzahl ausgeführter und aktiver Aufgaben in den letzten 60 Minuten abgerufen.
  - Falls beide Werte 0 sind (was heißt, dass in den letzten 60 Minuten keine Aufgaben ausgeführt wurden oder aktiv waren), wird die Poolgröße auf 0 festgelegt.
  - Wenn einer der Werte größer als null ist, erfolgt keine Änderung.

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

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/de-DE/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0114_2016-->
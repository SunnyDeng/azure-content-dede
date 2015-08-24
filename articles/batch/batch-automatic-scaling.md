
<properties
	pageTitle="Automatisches Skalieren von Rechenknoten in einem Azure Batch-Pool"
	description="Für das automatische Skalieren muss diese Funktion für den Pool aktiviert werden und eine Formel mit dem Pool verknüpft werden, die zur Berechnung der für die Bearbeitung der Anwendung erforderlichen Rechenknoten verwendet wird. "
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
	ms.date="08/05/2015"
	ms.author="davidmu"/>

# Automatisches Skalieren von Rechenknoten in einem Azure Batch-Pool

Das automatische Skalieren von Rechenknoten in einem Azure Batch-Pool ist eine dynamische Anpassung der Verarbeitungsleistung, die von der Anwendung beansprucht wird. Durch diese einfache Anpassung sparen Sie Zeit und Geld. Weitere Informationen zu Rechenknoten und Pools finden Sie unter [Azure Batch – Technische Übersicht](batch-technical-overview.md).

Automatisches Skalieren findet statt, wenn diese Funktion für einen Pool aktiviert und dem Pool eine Formel zugeordnet ist. Die Formel wird verwendet, um die Anzahl der Rechenknoten zu bestimmen, die erforderlich sind, um die Anwendung zu bearbeiten. Automatisches Skalieren kann beim Erstellen eines Pools oder später für einen bereits vorhandenen Pool festgelegt werden. Die Formel kann in einem Pool, in dem automatisches Skalieren aktiviert wurde, auch nachträglich aktualisiert werden.

Wenn automatisches Skalieren aktiviert ist, wird die Anzahl der verfügbaren Rechenknoten alle 15 Minuten auf Basis der Formel angepasst. Die Formel bezieht sich auf Sample-Werte, die regelmäßig erfasst, jedoch erst mit einer gewissen Verzögerung nach dem Erfassen von der Formel berücksichtigt werden können. Diese Verzögerung muss bei Verwendung der unten beschriebenen GetSample-Methode berücksichtigt werden.

Es empfiehlt sich stets, die Formel vor der Zuweisung an einen Pool auszuwerten, und es ist wichtig, den Status der ausgelösten automatischen Skalierung zu kontrollieren.

> [AZURE.NOTE]Jedes Azure Batch-Konto ist auf eine bestimmte Anzahl von Rechenknoten beschränkt, die für die Verarbeitung verwendet werden können. Das System erstellt die Knoten bis zu diesem Limit und erreicht möglicherweise nicht die von der Formel berechneten Zielwerte.

## Definieren der Formel

Die von Ihnen definierte Formel bestimmt die Anzahl der im nächsten Verarbeitungsintervall verfügbaren Rechenknoten im Pool. Die Formel ist eine Zeichenfolge, die eine Größe von 8 KB nicht überschreiten darf, und kann bis zu 100 durch Semikolons getrennte Anweisungen enthalten.

Die Anweisungen in einer Formel sind frei gebildete Ausdrücke. Sie können vom System definierte Variablen, benutzerdefinierte Variablen, konstante Werte und unterstützte Operationen auf diese Variablen oder Konstanten enthalten:

	VAR = Function(System defined variables, user-defined variables);

Auf diese Weise können Sie Variablen kombinieren, um komplexe Formeln zu erstellen:

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### Variablen

Vom System definierte Variablen und benutzerdefinierte Variablen können in einer Formel verwendet werden. Sie können den Wert dieser vom System definierten Variablen einstellen, um die Anzahl der Rechenknoten im Pool zu kontrollieren.

<table>
  <tr>
    <th>Variable</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Die vorgegebene Anzahl dedizierter Rechenknoten für den Pool. Der Wert kann anhand der tatsächlichen Anforderungen laufender Aufgaben geändert werden.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>Dieser Vorgang wird ausgeführt, wenn Rechenknoten aus einem Pool entfernt werden. Mögliche Werte:
      <br/>
      <ul>
        <li><p><b>requeue</b>: Aufgaben sofort beenden und wieder in die Auftragswarteschlange einfügen, damit sie neu geplant werden.</p></li>
        <li><p><b>terminate</b>: Aufgaben sofort beenden und aus der Auftragswarteschlange entfernen.</p></li>
        <li><p><b>taskcompletion</b>: auf derzeit ausgeführte Aufgaben warten und den Knoten dann aus dem Pool entfernen.</p></li>
        <li><p><b>retaineddata</b>: warten, bis alle lokal vorgehaltenen Aufgabendaten des Pools gelöscht wurden, bevor der Knoten aus dem Pool gelöscht wird.</p></li>
      </ul></td>
   </tr>
</table>

Sie können die Werte dieser vom System definierten Variablen nur lesen, um anhand der Metriken aus den Sample-Werten der Rechenknoten Anpassungen vorzunehmen.

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
    <td>Die Anzahl der Rechenknoten</td>
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
    <td>Die aktuelle Anzahl der zugewiesenen Rechenknoten</td>
  </tr>
</table>

### Typen

Folgende Typen werden in Formeln unterstützt:

- double
- doubleVec
- string
- timestamp
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

Folgende Vorgänge sind für die oben aufgeführten Typen zulässig.

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

Folgende vordefinierte Funktionen stehen zum Definieren einer Formel für das automatische Skalieren zur Verfügung.

<table>
  <tr>
    <th>Funktion</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>Der Durchschnittswert aller Werte in der doubleVecList.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>Die Länge des Vektors, der aus der doubleVecList erstellt wurde.</td>
  <tr>
    <td>double lg(double)</td>
    <td>Logarithmus zur Basis 2.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Komponentenweiser Logarithmus zur Basis 2. Ein vec(double) muss explizit für einzelne double-Parameter übergeben werden, sonst wird implizit die Version double lg(double) verwendet.</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>Natürlicher Logarithmus.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Komponentenweiser Logarithmus zur Basis 2. Ein vec(double) muss explizit für einzelne double-Parameter übergeben werden, sonst wird implizit die Version double lg(double) verwendet.</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>Logarithmus zur Basis 10.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Komponentenweiser Logarithmus zur Basis 10. Ein vec(double) muss explizit für einzelne double-Parameter übergeben werden, sonst wird die Version double log(double) Version verwendet.</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>Der maximale Wert in der doubleVecList.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>Der minimale Wert in der doubleVecList.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>Die Zweiernorm des Vektors, der aus der doubleVecList erstellt wurde.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>Das Perzentil-Element des Vektors v.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>Ein Zufallswert zwischen 0,0 und 1,0.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>Der Unterschied zwischen dem Minimal- und Maximalwert in doubleVecList.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>Die Stichproben-Standardabweichung der Werte in der doubleVecList.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>Beendet die Auswertung des Ausdrucks für das automatische Skalieren.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>Die Summe aller Komponenten von DoubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>Der Zeitstempel der aktuellen Zeit, wenn keine Parameter übergeben werden, oder andernfalls der Zeitstempel der DateTime-Zeichenfolge. Unterstützte dateTime-Formate sind W3CDTF und RFC1123.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>Der Wert des Elements an Position i im Vektor v mit einem Anfangsindex von 0.</td>
  </tr>
</table>

Einige der beschriebenen Funktionen akzeptieren eine Liste als Argument. Die durch Komma getrennte Liste ist eine beliebige Kombination aus double und doubleVec. Beispiel:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

Der doubleVecList-Wert wird vor der Auswertung in einen einzelnen doubleVec konvertiert. Falls beispielsweise v = [1, 2, 3] ist, dann entspricht der Aufruf avg(v) dem Aufruf avg(1,2,3), und avg (v, 7) entspricht dem Aufruf avg(1,2,3,7).

### Beispieldaten

Die vom System definierten Variablen sind Objekte, die Methoden für den Zugriff auf die zugehörigen Daten bereitstellen. Der folgende Ausdruck zeigt z. B. eine Anforderung zum Abrufen der letzten fünf Minuten der CPU-Auslastung:

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
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: legt einen Zeitrahmen für die Datenerfassung mit einer Startzeit und Endzeit fest.</p></li></ul></td>
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
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>: Da die GetSample-Methode fehlschlägt, wenn der Prozentsatz der Samplewerte geringer ist als der angegebene samplePercent-Wert, können Sie mithilfe der GetSamplePercent-Methode die Anzahl zunächst prüfen und dann eventuell eine andere Aktion ausführen, wenn nicht genug Samplewerte vorhanden sind, ohne die Auswertung für das automatische Skalieren anhalten.</p></td>
  </tr>
</table>

### Metriken

Folgende Metriken lassen sich über eine Formel definieren.

<table>
  <tr>
    <th>Metrik</th>
    <th>Beschreibung</th>
  </tr>
  <tr>
    <td>Ressource</td>
    <td><p>Basierend auf der CPU-Nutzung, Bandbreitennutzung, Speicherauslastung und Anzahl der Rechenknoten. Die oben beschriebenen Systemvariablen werden in Formeln verwendet, um die Rechenknoten in einem Pool verwalten:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Folgende Systemvariablen werden für Anpassungen anhand von Knotenmetriken verwendet:</p>
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
    <p>Das folgende Beispiel zeigt eine Formel, mit der die Anzahl der Rechenknoten im Pool auf 110&#160;% der aktuellen Zielanzahl von Knoten festgelegt wird, wenn in den vergangenen 10&#160;Minuten die minimale durchschnittliche CPU-Auslastung über 70&#160;% lag:</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>Das folgende Beispiel zeigt eine Formel, die verwendet wird, um die Anzahl der Rechenknoten im Pool auf 90&#160;% der aktuellen Knotenanzahl festzulegen, wenn die durchschnittliche CPU-Nutzung der letzten 60&#160;Minuten unter 20&#160;% liegt:</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>In diesem Beispiel wird die vorgegebene Anzahl dedizierter Rechenknoten auf maximal 400 eingestellt:</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>Aufgabe</td>
    <td><p>Anhand des Aufgabenstatus, beispielsweise "Aktiv", "Ausstehend" oder "Abgeschlossen".</p>
    <p>Folgende Systemvariablen werden für Anpassungen anhand der Aufgabenmetriken verwendet:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>Folgendes Beispiel zeigt eine Formel, die erkennt, ob 70&#160;% der Beispiele in den letzten 15&#160;Minuten erfasst wurden. Falls dies nicht der Fall ist, wird das letzte Beispiel verwendet. Es wird versucht, die Anzahl der Rechenknoten auf die Anzahl der aktiven Aufgaben, jedoch maximal auf 3, zu erhöhen. Die Knotenanzahl wird auf ein Viertel der Anzahl der aktiven Aufgaben eingestellt, da die MaxTasksPerVM-Eigenschaft des Pools auf 4 festgelegt wurde. Dabei wird auch die Deallokationsoption als "Taskcompletion" festgelegt, um den Computer beizubehalten, bis die Aufgabe beendet ist.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $NodeDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## Auswerten der Formel für das automatische Skalieren

Es ist stets empfehlenswert, eine Formel auszuwerten, bevor Sie sie in Ihrer Anwendung verwenden. Die Formel wird ausgewertet, indem Sie einen Testlauf für einen vorhandenen Pool ausführen. Verwenden Sie hierzu eine der folgenden Methoden:

- [IPoolManager.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/dn931617.aspx) oder [IPoolManager.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/dn931545.aspx): Diese .NET-Methoden erfordern den Namen eines vorhandenen Pools sowie die Zeichenkette mit der Formel für das automatische Skalieren. Die Ergebnisse des Aufrufs befinden sich in einer Instanz der[AutoScaleEvaluation-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Auswerten von automatischer Skalierung](https://msdn.microsoft.com/library/azure/dn820183.aspx): Bei dieser REST-Anfrage wird der Pool-Name im URI übergeben und die Formel für das automatische Skalieren im autoScaleFormula-Element des Anforderungstexts. Die bei der Anfrage generierte Antwort enthält Fehlerinformationen, die in Zusammenhang mit der Formel stehen können.

## Erstellen eines Pools mit aktiviertem automatischen Skalieren

Einen solchen Pool erstellen Sie mithilfe einer der folgenden Methoden:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): Dieses Cmdlet legt die Formel für das automatische Skalieren mit dem AutoScaleFormula-Parameter fest.
- [IPoolManager.CreatePool Method](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx): Nachdem diese .NET-Methode zum Erstellen eines Pools verwendet wurde, werden die Eigenschaften [ICloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) und [ICloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx) für den Pool eingestellt, um das automatische Skalieren zu aktivieren.
- [Hinzufügen eines Pools](https://msdn.microsoft.com/library/azure/dn820174.aspx): Die Elemente enableAutoScale und autoScaleFormula werden in dieser REST-API verwendet, um bei der Erstellung das automatische Skalieren für den Pool einzurichten.

> [AZURE.NOTE]Wenn Sie bei der Erstellung eines Pools mithilfe der oben genannten Ressourcen das automatische Skalieren einrichten, wird der targetDedicated-Parameter für den Pool nicht verwendet.

## Aktivieren des automatischen Skalierens nach der Erstellung eines Pools

Wenn Sie bereits anhand des targetDedicated-Parameters einen Pool mit einer festgelegten Anzahl von Rechenknoten eingerichtet haben, können Sie den vorhandenen Pool zu einem späteren Zeitpunkt so aktualisieren, dass automatisch skaliert wird. Hierzu gehen Sie auf eine der beiden folgenden Arten vor:

- [IPoolManager.EnableAutoScale-Methode](https://msdn.microsoft.com/library/azure/dn931709.aspx): Diese .NET-Methode erfordert den Namen des vorhandenen Pools und die Formel für das automatische Skalieren.
- [Aktivieren/Deaktivieren der automatischen Skalierung](https://msdn.microsoft.com/library/azure/dn820173.aspx): Diese REST-API erfordert den Namen des vorhandenen Pools im URI und die Formel für das automatische Skalieren im Request-Body.

> [AZURE.NOTE]Der beim Erstellen des Pools für den targetDedicated-Parameter festgelegte Wert wird beim Auswerten der Formel für das automatische Skalieren ignoriert.

## Abrufen von Informationen über Durchläufe des automatischen Skalierens

Sie sollten in regelmäßigen Abständen die Ergebnisse der Durchläufe des automatischen Skalierens überprüfen. Gehen Sie dazu auf eine der beiden folgenden Arten vor:

- [ICloudPool.AutoScaleRun-Eigenschaft](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx): Beim Verwenden der .NET-Bibliothek bietet diese Pool-Eigenschaft eine Instanz der [AutoScaleRun-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), die die Eigenschaften [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx), [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) und [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx) zur Verfügung stellt.
- [Pool abrufen](https://msdn.microsoft.com/library/dn820165.aspx): Diese REST-API gibt Informationen zu dem Pool zurück, einschließlich des neuesten Durchlaufs des automatischen Skalierens.

## Nächste Schritte

1.	Möglicherweise müssen Sie auf den Rechenknoten zugreifen, um die Effizienz der Anwendung vollständig bewerten zu können. Um den Remotezugriff nutzen zu können, muss dem Rechenknoten, auf den zugegriffen werden soll, ein Benutzerkonto hinzugefügt werden und eine RDP-Datei von dem Knoten abgerufen werden. Fügen Sie das Benutzerkonto auf eine der folgenden Arten hinzu:

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): Dieses Cmdlet erfordert den Poolnamen, den Namen des Rechenknotens, den Kontonamen und das Kennwort als Parameter.
	- [IVM.CreateUser](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx): Diese .NET-Methode erstellt eine Instanz der [IUser-Schnittstelle](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx), für die der Kontoname und das Kennwort für den Rechenknoten eingestellt werden können.
	- [Hinzufügen von TVM-Benutzern](https://msdn.microsoft.com/library/dn820137.aspx): Der Name des Pools und des Rechenknotens werden im URI festgelegt und der Kontoname und das Kennwort im Anforderungstext dieser REST-API übertragen.

		Abrufen der RDP-Datei:

	- [IVM.GetRDPFile](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx): Diese .NET-Methode erfordert den Namen der zu erstellenden RDP-Datei.
	- [Abrufen der TVM RDP-Datei](https://msdn.microsoft.com/library/dn820120.aspx): Diese REST-API erfordert den Namen des Pools und den Namen des Rechenknotens. Die Antwort enthält den Inhalt der RDP-Datei.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): Dieses Cmdlet ruft die RDP-Datei aus dem angegebenen Rechenknoten ab und speichert sie am festgelegten Speicherort oder in einen Stream.
2.	Einige Anwendungen erzeugen große Datenmengen, die nur schwer zu verarbeiten sind. Eine Möglichkeit, dies zu lösen, besteht in [effizienten Listenabfragen](batch-efficient-list-queries.md).

<!---HONumber=August15_HO7-->
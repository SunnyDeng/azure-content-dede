<properties 
	pageTitle="Verwenden der Engagement-API auf der universellen Windows-Plattform" 
	description="Verwenden der Engagement-API auf der universellen Windows-Plattform"			
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Verwenden der Engagement-API auf der universellen Windows-Plattform

Dieses Dokument ist ein Zusatz zu dem Dokument [Integration von Engagement in die universelle Windows-Plattform](../mobile-engagement-windows-store-integrate-engagement/): Es bietet tiefergehende Details zur Verwendung der Engagement-API zur Ausgabe von Anwendungsstatistiken.

Beachten Sie: Wenn Engagement nur die Sitzungsaktivitäten der Anwendung, Abstürze und technische Informationen melden soll, ist es am einfachsten, wenn alle `Page`-Unterklassen von der `EngagementPage`-Klasse erben.

Wenn Sie darüber hinaus noch mehr Meldungen wünschen, z. B. wenn Sie anwendungsspezifische Ereignisse, Fehler und Aufträge melden möchten, oder wenn die Aktivitäten Ihrer Anwendung anders als in den `EngagementPage`-Klassen implementiert gemeldet werden sollen, dann müssen Sie die Engagement-API verwenden.

Die Engagement-API wird von der `EngagementAgent`-Klasse zur Verfügung gestellt. Sie können über `EngagementAgent.Instance` auf diese Methoden zugreifen.

Auch wenn das Agent-Modul nicht initialisiert wurde, wird jeder Aufruf zur API verzögert und erneut ausgeführt, sobald der Agent verfügbar ist.

##Engagement-Konzepte

In den folgenden Abschnitten werden die [Mobile Engagement-Konzepte](../mobile-engagement-concepts/) für die universelle Windows-Plattform genauer dargestellt.

### `Session` und `Activity`

Eine *Aktivität* ist normalerweise einer Seite der Anwendung zugeordnet, d. h. die *Aktivität* beginnt, wenn die Seite angezeigt wird, und wird beendet, wenn die Seite geschlossen wird: Dies ist der Fall, wenn das Engagement-SDK unter Verwendung der `EngagementPage`-Klasse integriert worden ist.

*Aktivitäten* können aber auch manuell mithilfe der Engagement-API gesteuert werden. Auf diese Weise kann eine bestimmte Seite in mehrere Teile unterteilt werden, um weitere Informationen über die Verwendung dieser Seite zu erhalten (z. B. um zu erfahren, wie oft und wie lange Dialoge innerhalb dieser Seite verwendet werden).

##Berichterstellung für Aktivitäten

### Benutzer startet eine neue Aktivität

#### Verweis

			void StartActivity(string name, Dictionary<object, object> extras = null)

Sie müssen jedes Mal `StartActivity()` aufrufen, wenn sich die Benutzeraktivität ändert. Der erste Aufruf dieser Funktion startet eine neue Benutzersitzung.

> [AZURE.IMPORTANT]Das SDK ruft automatisch die EndActivity-Methode auf, wenn die Anwendung geschlossen wird. Daher wird DRINGEND empfohlen, die StartActivity-Methode aufzurufen, sobald sich die Aktivität des Benutzers ändert, und die EndActivity-Methode NIE aufzurufen, weil dadurch ein Beenden der aktuellen Sitzung erzwungen wird.

#### Beispiel

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### Der Benutzer beendet seine aktuelle Aktivität

#### Referenz-

			void EndActivity()

Damit werden die Aktivität und die Sitzung beendet. Sie sollten diese Methode nur dann aufrufen, wenn Sie sich wirklich sicher sind.

#### Beispiel

			EngagementAgent.Instance.EndActivity();

##Berichterstellung für Aufträge

### Starten eines Auftrags

#### Referenz-

			void StartJob(string name, Dictionary<object, object> extras = null)

Sie können den Auftrag verwenden, um bestimmte Aufgaben eine Zeit lang nachzuverfolgen.

#### Beispiel

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Beenden eines Auftrags

#### Verweis

			void EndJob(string name)

Sobald eine Aufgabe, die von einem Auftrag nachverfolgt wird, beendet ist, sollten Sie die EndJob-Methode für diesen Auftrag aufrufen, indem Sie den Auftragsnamen angeben.

#### Beispiel

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Berichterstellung für Ereignisse

Es gibt drei Arten von Ereignissen:

-   Eigenständige Ereignisse
-   Sitzungsereignisse
-   Auftragsereignisse

### Eigenständige Ereignisse

#### Verweis

			void SendEvent(string name, Dictionary<object, object> extras = null)

Eigenständige Ereignisse können außerhalb des Kontexts einer Sitzung auftreten.

#### Beispiel

			EngagementAgent.Instance.SendEvent("event", extra);

### Sitzungsereignisse

#### Verweis

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Sitzungsereignisse werden normalerweise verwendet, um die Aktionen eines Benutzers während seiner Sitzung zu melden.

#### Beispiel

**Ohne Daten:**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Mit Daten:**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Auftragsereignisse

#### Referenz-

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Auftragsereignisse werden normalerweise verwendet, um die Aktionen eines Benutzers während eines Auftrags zu melden.

#### Beispiel

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Melden von Fehlern

Es gibt drei Arten von Fehlern:

-   Eigenständige Fehler
-   Sitzungsfehler
-   Auftragsfehler

### Eigenständige Fehler

#### Referenz-

			void SendError(string name, Dictionary<object, object> extras = null)

Im Gegensatz zu Sitzungsfehlern können eigenständige Fehler außerhalb des Kontexts einer Sitzung auftreten.

#### Beispiel

			EngagementAgent.Instance.SendError("errorName", extras);

### Sitzungsfehler

#### Verweis

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Sitzungsfehler werden normalerweise zum Melden der Fehler verwendet, die Auswirkungen auf den Benutzer während seiner Sitzung haben.

#### Beispiel

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Auftragsfehler

#### Referenz-

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Fehler können mit einem ausgeführten Auftrag in Zusammenhang stehen anstatt mit der aktuellen Benutzersitzung.

#### Beispiel

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Berichterstellung von Abstürzen

Der Agent stellt zwei Methoden für den Umgang mit Abstürzen zur Verfügung.

### Senden einer Ausnahme

#### Verweis

			void SendCrash(Exception e, bool terminateSession = false)

#### Beispiel

Sie können eine Ausnahme jederzeit durch folgenden Aufruf senden:

			EngagementAgent.Instance.SendCrash(aCatchedException);

Sie können auch einen optionalen Parameter zum Beenden der Engagement-Sitzung zur gleichen Zeit wie bei der Übermittlung des Absturzes verwenden. Hierzu rufen Sie Folgendes auf:

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Wenn Sie dies tun, werden die Sitzung und die Aufträge direkt nach dem Senden des Absturzes geschlossen.

### Senden Sie eine nicht behandelte Ausnahme

#### Verweis

			void SendCrash(Exception e)

Engagement bietet auch die Möglichkeit, unbehandelte Ausnahmen zu senden, wenn der automatische **Absturz**-Bericht von Engagement **DEAKTIVIERT** ist. Dies ist besonders bei Verwendung in der Anwendung UnhandledException-Ereignishandler nützlich.

Durch diese Methode werden **IMMER** die Engagement-Sitzungen und -Aufträge nach dem Aufrufen beendet.

#### Beispiel

Sie können sie verwenden, um Ihren eigenen UnhandledExceptionEventArgs-Handler zu implementieren. Fügen Sie z. B. die `Current_UnhandledException`-Methode der `App.xaml.cs`-Datei hinzu:

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
			{
			   EngagementAgent.Instance.SendCrash(e.Exception,false);
			}

Fügen Sie in "App.xaml.cs" in "Public App(){}" Folgendes hinzu:

			Application.Current.UnhandledException += Current_UnhandledException;

##Geräte-ID

			String EngagementAgent.Instance.GetDeviceId()

Sie können die Engagement-Geräte-ID durch Aufrufen dieser Methode abrufen.

##Extras-Parameter

Einem Ereignis, einem Fehler, einer Aktivität oder einem Auftrag können beliebige Daten zugeordnet werden. Diese Daten können unter Verwendung eines Wörterbuchs strukturiert werden. Schlüssel und Werte können einen beliebigen Typ aufweisen.

Extras-Daten werden serialisiert; wenn Sie also einen eigenen Typ in Extras einfügen möchten, müssen Sie einen Datenvertrag für diesen Typ hinzufügen.

### Beispiel

Erstellen Sie die neue Klasse "Person".

			using System.Runtime.Serialization;
			
			namespace Microsoft.Azure.Engagement
			{
			  [DataContract]
			  public class Person
			  {
			    public Person(string name, int age)
			    {
			      Age = age;
			      Name = name;
			    }
			
			    // Properties
			
			    [DataMember]
			    public int Age
			    {
			      get;
			      set;
			    }
			
			    [DataMember]
			    public string Name
			    {
			      get;
			      set; 
			    }
			  }
			}

Fügen Sie anschließend eine `Person`-Instanz zu einem Extra hinzu.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING]Wenn Sie andere Objekttypen einfügen, stellen Sie sicher, dass deren ToString()-Methode implementiert wird, sodass eine visuell lesbare Zeichenfolge zurückgegeben wird.

### Grenzen

#### Schlüssel

Jeder Schlüssel in dem Objekt muss mit dem folgenden regulären Ausdruck übereinstimmen:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben, gefolgt von Buchstaben, Ziffern oder Unterstrichen (_) beginnen müssen.

#### Größe

Extras sind beschränkt auf **1024** Zeichen pro Aufruf.

##Informationen zur Berichterstellung für Anwendungen

### Referenz-

			void SendAppInfo(Dictionary<object, object> appInfos)

Sie können Nachverfolgungsinformationen (oder beliebige andere anwendungsspezifische Informationen) manuell mithilfe der SendAppInfo()-Funktion melden.

Beachten Sie, dass diese Informationen inkrementell gesendet werden können: Nur der letzte Wert für einen bestimmten Schlüssel wird für ein bestimmtes Gerät gespeichert. Verwenden Sie wie bei Ereignisextras ein Wörterbuch <Objekt, Objekt>, um Informationen anzufügen.

### Beispiel

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			  {
			    {"birthdate", "1983-12-07"},
			    {"gender", "female"}
			  };
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Grenzen

#### Schlüssel

Jeder Schlüssel in dem Objekt muss mit dem folgenden regulären Ausdruck übereinstimmen:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Das bedeutet, dass Schlüssel mit mindestens einem Buchstaben, gefolgt von Buchstaben, Ziffern oder Unterstrichen (_) beginnen müssen.

#### Größe

Anwendungsinformationen sind beschränkt auf **1024** Zeichen pro Aufruf.

Im vorherigen Beispiel enthält das an den Server gesendete JSON 44 Zeichen:

			{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=July15_HO2-->
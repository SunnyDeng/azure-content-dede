<properties
	pageTitle="Verwenden der Mobile Apps-Clientbibliothek von Android"
	description="Verwenden des Android-Client-SDK für Azure Mobile Apps."
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="ricksal"/>


# Verwenden der Android-Clientbibliothek für Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

In diesem Leitfaden erfahren Sie, wie Sie das Android-Client-SDK für Mobile Apps zum Implementieren gängiger Szenarien wie dem Abfragen von Daten (Einfügen, Aktualisieren und Löschen), Authentifizieren von Benutzern, zur Fehlerbehandlung und zum Anpassen des Clients verwenden. Er bietet auch eine fundierte Einführung in gängigen Clientcode, der in den meisten mobilen Apps verwendet wird.

Dieser Leitfaden konzentriert sich auf die Clientseite des Android-SDK. Weitere Informationen zu den serverseitigen SDKs für mobile Apps finden Sie unter [Arbeiten mit dem .NET-Back-End-SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) oder [Verwenden des Node.js-Back-End-SDKs](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Referenzdokumentation

Sie finden die Javadocs-API-Referenz für die Android-Clientbibliothek [auf GitHub](http://azure.github.io/azure-mobile-apps-android-client/).

## Einrichtung und Voraussetzungen

Das Mobile Services-SDK unterstützt Android Version 2.2 oder höher. Wir empfehlen jedoch die Verwendung von Android 4.2 oder höher.

Absolvieren Sie das Tutorial [Schnellstart für Mobile Apps](app-service-mobile-android-get-started.md), um zu gewährleisten, dass Sie Android Studio installiert haben. Dies hilft Ihnen, Ihr Konto zu konfigurieren und Ihr erstes Mobile App-Back-End zu erstellen. In diesem Falle können Sie den Rest dieses Abschnitts überspringen.

Wenn Sie entscheiden, das Schnellstarttutorial nicht zu absolvieren, und eine Android-App mit einem Mobile App-Back-End verbinden möchten, gehen Sie folgendermaßen vor:

- [Erstellen Sie ein Mobile App-Back-End](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) zur Verwendung mit Ihrer Android-App (sofern Ihre App nicht bereits darüber verfügt),
- [aktualisieren Sie die Gradle-Builddateien](#gradle-build) in Android Studio, und
- [aktivieren Sie die Internetberechtigung](#enable-internet)

Danach müssen Sie die Schritte ausführen, die in der fundierten Einführung beschrieben werden.

###<a name="gradle-build"></a>Aktualisieren der Gradle-Builddatei

Ändern Sie beide **build.gradle**-Dateien:

1. Fügen Sie diesen Code in die *Project*-Ebene der **build.gradle**-Datei im *buildscript*-Tag ein:

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. Fügen Sie diesen Code in die *Module app*-Ebene der **build.gradle**-Datei im *dependencies*-Tag ein:

		compile 'com.microsoft.azure:azure-mobile-android:3.1'

	Derzeit ist 3.1 die neueste Version. Die unterstützten Versionen sind [hier](http://go.microsoft.com/fwlink/p/?LinkID=717034) aufgeführt.

###<a name="enable-internet"></a>Aktivieren der Internetberechtigung
Für den Zugriff auf Azure muss für Ihre App die INTERNET-Berechtigung aktiviert sein. Wenn sie nicht bereits aktiviert ist, fügen Sie Ihrer Datei **AndroidManifest.xml** die folgende Codezeile hinzu:

	<uses-permission android:name="android.permission.INTERNET" />

## Die fundierte Einführung

In diesem Abschnitt wird ein Teil des Codes der Schnellstart-App erörtert. Wenn Sie das Schnellstarttutorial nicht absolviert haben, müssen Sie diesen Code Ihrer App hinzufügen.

> [AZURE.NOTE] Die Zeichenfolge „MobileServices“ kommt im Code häufig vor: Tatsächlich verweist der Code auf das Mobile Apps-SDK ist, es ist nur eine temporäre Übernahme aus der Vergangenheit.


###<a name="data-object"></a>Definieren von Clientdatenklassen

Für den Zugriff auf Daten aus SQL Azure-Tabellen definieren Sie Clientdatenklassen, die den Tabellen im Mobile App-Back-End entsprechen. Für die Beispiele in diesem Thema wird eine Tabelle mit dem Namen *ToDoItem* verwendet, die folgende Spalten hat:

- id
- text
- complete

Das entsprechend typisierte clientseitige Objekt sieht wie folgt aus:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Der Code ist in einer Datei namens **ToDoItem.java** gespeichert.

Wenn Ihre SQL Azure-Tabelle weitere Spalten enthält, würden Sie die entsprechenden Felder dieser Klasse hinzufügen.

Enthielte sie z. B. eine Integerspalte „Priority“, dann könnten Sie dieses Feld zusammen mit seinen Getter- und Setter-Methoden hinzufügen:

		private Integer priority;

	    /**
	     * Returns the item priority
	     */
	    public Integer getPriority() {
	        return mPriority;
	    }

	    /**
	     * Sets the item priority
	     *
	     * @param priority
	     *            priority to set
	     */
	    public final void setPriority(Integer priority) {
	        mPriority = priority;
	    }

Informationen zum Erstellen zusätzlicher Tabellen in Ihrem Mobile Apps-Back-End finden Sie unter [Gewusst wie: Definieren eines Tabellencontrollers](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (.NET-Back-End) oder [Definieren von Tabellen mit einem dynamischen Schema](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (Node.js-Back-End). Für ein Node.js-Back-End können Sie auch die Einstellung **Einfache Tabellen** im [Azure-Portal] verwenden.

###<a name="create-client"></a>Gewusst wie: Erstellen des Clientkontexts

Der folgende Code erstellt das **MobileServiceClient**-Objekt, das für den Zugriff auf Ihr Mobile App-Back-End verwendet wird. Der Code gehört in die `onCreate`-Methode der **Activity**-Klasse, die in *AndroidManifest.xml* als **MAIN**-Aktion und **LAUNCHER**-Kategorie angegeben ist. Im Schnellstartcode gehört er in die Datei **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileAppUrl", // Replace with the above Site URL
				this)

Ersetzen Sie in diesem Code `MobileAppUrl` durch die URL des Mobile App-Back-Ends. Sie finden die URL im [Azure-Portal](https://portal.azure.com/) auf dem Blatt für das Mobile App-Back-End. Zum Kompilieren dieser Codezeile müssen Sie auch folgende **import**-Anweisung hinzufügen:

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Erstellen von Tabellenverweisen

Der einfachste Weg, Daten im Back-End abzufragen oder zu ändern, ist das *typisierte Programmiermodell*, da Java eine stark typisierte Sprache ist (später besprechen wir das *untypisierte* Modell). Dieses Modell ermöglicht eine nahtlose JSON-Serialisierung und -Deserialisierung mithilfe der [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801)-Bibliothek, wenn Daten zwischen Clientobjekten und Tabellen im Back-End-Azure SQL gesendet werden: Der Entwickler muss nicht eingreifen, da das Framework alle Aufgaben übernimmt.

Um auf eine Tabelle zuzugreifen, erstellen Sie zuerst ein [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835)-Objekt durch Aufrufen der **getTable**-Methode im [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Diese Methode verfügt über zwei Überladungen:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Im folgenden Code ist *mClient* ein Verweis auf Ihr MobileServiceClient-Objekt.

Die [erste Überladung](http://go.microsoft.com/fwlink/p/?LinkId=296839) wird verwendet, wenn Klassenname und Tabellenname gleich sind, und dies trifft im Schnellstart zu:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


Die [zweite Überladung](http://go.microsoft.com/fwlink/p/?LinkId=296840) wird verwendet, wenn sich Tabellenname und Klassenname unterscheiden: Der erste Parameter ist der Tabellenname.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Gewusst wie: Datenbindung in der Benutzeroberfläche

Datenbindung besteht aus drei Komponenten:

- Die Datenquelle
- Das Bildschirmlayout
- Der Adapter, der diese beiden Komponenten verbindet.

In unserem Beispielcode geben wir die Daten aus der Mobile Apps-SQL Azure-Tabelle *ToDoItem* in ein Array zurück. Dies ist ein häufiges Muster für Datenanwendungen: Datenbankabfragen geben oft eine Sammlung von Zeilen in Form von Listen oder Arrays an den Client zurück. Das Array ist in diesem Fall die Datenquelle.

Der Code definiert ein Bildschirmlayout für die Anzeige der Daten, die auf dem Gerät erscheinen sollen.

Diese beiden Komponenten sind über einen Adapter verbunden, der in diesem Code eine Erweiterung der *ArrayAdapter&lt;ToDoItem&gt;*-Klasse ist.

#### <a name="layout"></a>Definieren des Layouts

Das Layout wird durch mehrere XML-Codeabschnitte definiert. Anhand eines existierenden Layouts nehmen wir an, dass der folgende Code die **ListView** darstellt, die wir mit unseren Serverdaten füllen möchten.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


Das *listitem*-Attribut im obigen Code definiert die id des Layouts für eine bestimmte Zeile in der Liste. Der folgende Code definiert ein Kontrollkästchen und dessen zugehörigen Text. Dieses Element wird einmal pro Listenelement instanziiert. In diesem Layout wird das Feld **id** nicht angezeigt, und bei einem komplexeren Layout würden zusätzliche Felder angezeigt. Dieser Code befindet sich in der Datei **row\_list\_to\_do.xml**.

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="horizontal">
	    <CheckBox
	        android:id="@+id/checkToDoItem"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="@string/checkbox_text" />
	</LinearLayout>


#### <a name="adapter"></a>Definieren des Adapters

Da die Datenquelle in unserer Ansicht ein *ToDoItem* ist, leiten wir unseren Adapter von der *ArrayAdapter&lt;ToDoItem&gt;*-Klasse ab. Diese Unterklasse produziert eine Ansicht für jedes *ToDoItem* und verwendet dabei das *row\_list\_to\_do*-Layout.

In unserem Code definieren wir die folgende Klasse als Erweiterung der *ArrayAdapter&lt;E&gt;*-Klasse:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Sie müssen die *getView*-Methode der Klasse überschreiben. Dieser Beispielcode ist ein Beispiel dafür: Die Einzelheiten sind von der jeweiligen Anwendung abhängig.

    @Override
	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

Wir erstellen eine Instanz dieser Klasse in unserer Aktivität wie folgt:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Beachten Sie, dass der zweite Parameter des ToDoItemAdapter-Konstruktors ein Verweis auf das Layout ist. Im Anschluss an den Aufruf des Konstruktors steht der folgende Code, der zunächst einen Verweis auf **ListView** abruft und anschließend *setAdapter* aufruft, um sich selbst für den soeben erstellten Adapter zu konfigurieren:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>Die API-Struktur

Mobile Apps-Tabellenvorgänge und benutzerdefinierte API-Aufrufe sind asynchron, sodass Sie die Objekte [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) und [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) in allen asynchronen Methoden mit Abfragen und Einfügungen, Aktualisierungen und Löschungen verwenden. Dies erleichtert die Ausführungen mehrerer Vorgänge in einem Hintergrundthread, ohne sich um mehrere geschachtelte Rückrufe kümmern zu müssen.

Um zu erfahren, wie diese asynchronen APIs in Ihrer Android-App verwendet und wie Daten auf der Benutzeroberfläche angezeigt werden, können Sie sich die Datei **ToDoActivity.java** im Android-Schnellstartprojekt über das [Azure-Portal] ansehen.


#### <a name="use-adapter"></a>Verwenden des Adapters

Sie sind nun in der Lage, die Datenbindung zu verwenden. Der folgende Code zeigt, wie Sie die Elemente aus der Tabelle im mobilen Dienst abrufen, den Adapter leeren und anschließend dessen *add*-Methode aufrufen, um ihn mit den zurückgegebenen Elementen zu füllen.


**TBD**: Testen Sie diesen Code!

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

Sie müssen den Adapter jedes mal aufrufen, wenn Sie die *ToDoItem*-Tabelle ändern und das Ergebnis der Änderungen anzeigen möchten. Da Veränderungen auf einzelnen Einträgen erfolgen, erhalten Sie in diesem Fall eine einzelne Zeile anstatt einer Sammlung. Beim Einfügen von Elementen rufen Sie die *add*-Methode des Adapters auf und beim Löschen von Elementen dessen *remove*-Methode.

##<a name="querying"></a>Gewusst wie: Abfragen von Daten aus Ihrem Mobile App-Back-End

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihr Mobile App-Back-End stellen können. Dies umfasst folgende Aufgaben:

- [Zurückgeben aller Elemente]
- [Zurückgegebene Daten filtern]
- [Zurückgegebene Daten sortieren]
- [Daten seitenweise zurückgeben]
- [Bestimmte Spalten auswählen]
- [Verketten von Abfragemethoden](#chaining)

### <a name="showAll"></a>Zurückgeben aller Elemente einer Tabelle

Die folgende Abfrage gibt alle Elemente aus der *ToDoItem*-Tabelle zurück.

	List<ToDoItem> results = mToDoTable.execute().get();

Die Variable *results* gibt das Resultset der Abfrage als Liste zurück.

### <a name="filtering"></a>Gewusst wie: Filtern zurückgegebener Daten

Bei der Ausführung der folgenden Abfrage werden alle Elemente aus der *ToDoItem*-Tabelle zurückgegeben, wobei *complete* gleich *FALSE* ist. Dies ist der Code, der bereits im Schnellstart vorhanden ist.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben.

Sie definieren einen Filter mit einem **where**-Methodenaufruf für den Tabellenverweis. Diesem Code folgt ein **field**-Methodenaufruf, der das logische Prädikat angibt. Mögliche Prädikatmethoden sind **eq** (equals, ist gleich), **ne** (not equal, ist ungleich), **gt** (greater than, größer als), **ge** (greater than or equal to, größer als oder gleich), **lt** (less than, kleiner als), **le** (less than or equal to, kleiner als oder gleich) usw. Mit diesen Methoden können Sie die numerischen und Zeichenfolgenfelder mit bestimmten Werten vergleichen.

Sie können nach Daten filtern. Mit den folgenden Methoden können Sie das gesamte Datumsfeld oder Teile des Datums vergleichen: **year**, **month**, **day**, **hour**, **minute** und **second**. Im folgenden Beispiel wird ein Filter für Elemente hinzugefügt, deren *Fälligkeitsdatum* 2013 entspricht.

	mToDoTable.where().year("due").eq(2013).execute().get();

Die folgenden Methoden unterstützen komplexe Filter für Zeichenfolgenfelder: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** und **length**. Das folgende Beispiel filtert nach Tabellenzeilen, deren *text*-Spalte mit „PRI0“ beginnt.

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

Die folgenden Operatormethoden werden von Zahlenfeldern unterstützt: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** und **round**. Das folgende Beispiel filtert nach Tabellenzeilen, in denen *duration* eine gerade Zahl ist.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Sie können Prädikate mit diesen logischen Methoden verknüpfen: **and**, **or** und **not**. Im folgenden Beispiel werden zwei der obigen Beispiele kombiniert.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

Außerdem können Sie logische Operatoren gruppieren und schachteln, wie etwa hier:

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Eine ausführlichere Besprechung und weitere Beispiele für Filter finden Sie unter [Exploring the richness of the Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (Untersuchen der umfassenden Möglichkeiten des Android-Clientabfragemodells).

### <a name="sorting"></a>Gewusst wie: Sortieren zurückgegebener Daten

Der folgende Code gibt alle Elemente aus einer *ToDoItems*-Tabelle in aufsteigender Reihenfolge sortiert nach dem *text*-Feld zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Back-End-Tabelle:

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Der erste Parameter der **orderBy**-Methode ist eine Zeichenfolge mit dem Namen des Felds, nach dem sortiert werden soll.

Für den zweiten Parameter wird die Aufzählung **QueryOrder** verwendet, um anzugeben, ob auf- oder absteigend sortiert werden soll.

Beachten Sie, dass beim Filtern mit der ***where***-Methode die ***where***-Methode vor dem Aufruf der ***orderBy***-Methode aufgerufen werden muss.

### <a name="paging"></a>Seitenweises Zurückgeben von Daten

Das erste Beispiel zeigt, wie Sie die ersten fünf Elemente einer Tabelle abrufen können. Die Abfrage gibt die Elemente aus einer *ToDoItems*-Tabelle zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Back-End-Tabelle:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Hier ist eine Abfrage, welche die ersten fünf Elemente überspringt und die nächsten fünf zurückgibt:

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Gewusst wie: Auswählen bestimmter Spalten

Der folgende Code veranschaulicht, wie alle Elemente aus einer *ToDoItems*-Tabelle zurückgegeben werden, zeigt aber nur das *complete*-Feld und das *text*-Feld an. *mToDoTable* ist der Verweis auf die zuvor erstellte Back-End-Tabelle.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


Die Parameter der select-Funktion sind in diesem Fall die Namen der Tabellenspalten, die Sie zurückgeben möchten.

Die **select**-Methode folgt im Anschluss an Methoden wie **where** und **orderBy**, falls diese vorhanden sind. Auf sie können Pagingmethoden wie **top** folgen.

### <a name="chaining"></a>Verketten von Abfragemethoden

Wie Sie sehen, können die Methoden zum Abfragen von Back-End-Tabellen verkettet werden. Auf diese Weise können Sie z. B. spezielle Spalten gefilterter Zeilen mit Sortierung und Seitenverwaltung abfragen. Sie können komplexe logische Filter erstellen.

Dies funktioniert dank der von den Abfragemethoden zurückgegebenen **MobileServiceQuery&lt;T&gt;**-Objekte, in denen wiederum zusätzliche Methoden aufgerufen werden können. Um die Methodenverkettung zu beenden und die Abfrage auszuführen, rufen Sie die **execute**-Methode auf.

Im folgenden Codebeispiel wird *mToDoTable* als Verweis auf die Tabelle *ToDoItem* verwendet.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

Beachten Sie, dass beim Verketten von Methoden die *where*-Methode und Prädikate immer an erster Stelle stehen müssen. Anschließend können Sie Folgemethoden in der Reihenfolge aufrufen, in der Ihre Anwendung diese benötigt.


##<a name="inserting"></a>Gewusst wie: Einfügen von Daten in das Back-End

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können.

Zunächst instanziieren Sie ein Objekt der *ToDoItem*-Klasse und setzen dessen Eigenschaften.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

Als Nächstes führen Sie den folgenden Code aus:

	ToDoItem entity = mToDoTable.insert(item).get();

Die zurückgegebene Entität entspricht den in die Back-End-Tabelle eingefügten Daten, die ID und alle anderen für das Back-End festgelegten Werte inbegriffen.

Mobile Apps setzt voraus, dass jede Tabelle eine Spalte namens **id** aufweist, die zum Indizieren der Tabelle verwendet wird. Standardmäßig ist diese Spalte ein Zeichenfolgen-Datentyp, der zur Unterstützung der Offlinesynchronisierung benötigt wird. Der Standardwert der ID-Spalte ist eine GUID, aber Sie können andere eindeutige Werte wie E-Mail-Adressen oder Benutzernamen bereitstellen. Wenn kein Zeichenfolgen-ID-Wert für einen eingefügten Datensatz angegeben wird, generiert das Back-End einen neuen GUID-Wert.

Zeichenfolgen-ID-Werte bieten folgende Vorteile:

+ IDs können ohne Roundtrip zur Datenbank generiert werden.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ ID-Werte lassen sich leichter in die Anwendungslogik integrieren.

##<a name="updating"></a>Gewusst wie: Aktualisieren von Daten in einer mobilen App

Der folgende Code zeigt, wie Sie Daten in einer Tabelle aktualisieren können.

    mToDoTable.update(item).get();

In diesem Beispiel ist *item* ein Verweis auf eine Zeile in der *ToDoItem*-Tabelle, an der einige Änderungen vorgenommen wurden.

##<a name="deleting"></a>Gewusst wie: Löschen von Daten in einer mobilen App

Der folgende Code zeigt, wie Sie Daten durch Angabe des Datenobjekts aus einer Tabelle löschen.

	mToDoTable.delete(item);

Sie können ein Element auch durch Angabe des **id**-Felds der zu löschenden Zeile löschen.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>Gewusst wie: Abfragen bestimmter Elemente

Dieser Code zeigt, wie Sie nach einem Element mit einer bestimmten *id* suchen können.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Gewusst wie: Arbeiten mit nicht typisierten Daten

Mit dem untypisierten Programmiermodell erhalten Sie vollständige Kontrolle über die JSON-Serialisierung. Dies ist hilfreich in bestimmten Szenarien, z. B. wenn Ihre Back-End-Tabelle eine große Anzahl an Spalten enthält, und Sie nur einige wenige Spalten benötigen. Im typisierten Modell müssen Sie alle Spalten der Tabelle in Ihrer mobilen App in Ihrer Datenklasse definieren. Im untypisierten Modell definieren Sie dagegen nur die Spalten, die Sie verwenden möchten.

Die meisten API-Aufrufe für den Datenzugriff sind gleich wie beim typisierten Programmiermodell. Im untypisierten Modell rufen Sie Methoden des **MobileServiceJsonTable**-Objekts anstelle des **MobileServiceTable**-Objekts auf.


### <a name="json_instance"></a>Erstellen einer Instanz einer nicht typisierten Tabelle

Ähnlich wie beim typisierten Modell benötigen Sie zunächst einen Tabellenverweis. In diesem Fall handelt es sich jedoch um ein **MobileServicesJsonTable**-Objekt. Sie erhalten den Verweis durch einen Aufruf der **getTable**-Methode für eine Instanz des Clients wie folgt:

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Sobald Sie eine Instanz von **MobileServiceJsonTable** erstellt haben, können Sie beinahe alle Methoden aufrufen, die Sie auch beim typisierten Programmiermodell zur Verfügung haben. In manchen Fällen nehmen die Methoden jedoch untypisierte Parameter entgegen, wie Sie in den folgenden Beispielen sehen werden.

### <a name="json_insert"></a>Einfügen in nicht typisierte Tabellen

Der folgende Code zeigt, wie Sie Elemente einfügen können. Sie müssen zunächst ein [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html) erstellen, das Teil der [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801)-Bibliothek ist.

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

Anschließend fügen Sie das Objekt ein.

    mJsonToDoTable.insert(jsonItem).get();


Verwenden Sie den folgenden Methodenaufruf, falls Sie die ID des eingefügten Objekts abrufen müssen:

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Löschen aus einer nicht typisierten Tabelle

Der folgende Code zeigt, wie Sie eine Instanz löschen, in diesem Fall die Instanz des **JsonObject**, die Sie im vorigen *insert*-Beispiel eingefügt haben. Beachten Sie, dass der Code derselbe wie beim typisierten Fall ist, aber die Methode weist eine andere Signatur auf, da sie auf ein **JsonObject** verweist.


         mToDoTable.delete(jsonItem);


Sie können eine Instanz auch direkt anhand ihrer ID löschen:

		 mToDoTable.delete(ID);


### <a name="json_get"></a>Zurückgeben aller Zeilen aus einer nicht typisierten Tabelle

Der folgende Code ruft eine gesamte Tabelle ab. Da Sie eine JSON-Tabelle verwenden, können Sie nur einige der Spalten der Tabelle selektiv abrufen.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Für Filterung, Sortierung und Seitenverwaltung können Sie Methoden mit den gleichen Namen wie im typisierten Programmiermodell verketten.



##<a name="custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Rufen Sie von einem Android-Client aus die **invokeApi**-Methode auf, um den benutzerdefinierten API-Endpunkt aufzurufen. Das folgende Beispiel zeigt, wie ein API-Endpunkt mit dem Namen *completeAll* aufgerufen wird, der eine Sammelklasse mit dem Namen MarkAllResult zurückgibt.

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

Die **invokeApi**-Methode wird beim Client aufgerufen, der eine POST-Anfrage an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler. Mit anderen Versionen von **invokeApi** können Sie optional ein Objekt im Anforderungshauptteil senden, die HTTP-Methode angeben und Abfrageparameter mit der Anforderung senden. Nicht typisierte Versionen von **invokeApi** werden ebenfalls bereitgestellt.

##<a name="authentication"></a>Gewusst wie: Hinzufügen von Authentifizierung zur App

Es stehen bereits Tutorials zur Verfügung, in denen das Hinzufügen dieser Features im Detail beschrieben wird.

App Service unterstützt das [Authentifizieren von App-Benutzern](app-service-mobile-android-get-started-users.md) mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft-Konto, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln auf Ihrem Back-End zu implementieren.

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein *Serverfluss* und ein *Clientfluss*. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden, und Programmierung Ihrerseits erforderlich ist.

Für die Authentifizierung in Ihrer App sind drei Schritte erforderlich:

- Registrieren Sie Ihre App für die Authentifizierung mit einem Anbieter, und konfigurieren Sie Ihr Mobile App-Back-End.
- Schränken Sie die Tabellenberechtigungen ausschließlich auf authentifizierte Benutzer ein.
- Fügen Sie Ihrer App Authentifizierungscode hinzu.

Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die SID eines authentifizierten Benutzers verwenden, um Anfragen zu verändern.

Diese beiden ersten Aufgaben können Sie im [Azure-Portal](https://portal.azure.com/) erledigen. Weitere Informationen finden Sie unter [Get started with authentication] (Erste Schritte zur Authentifizierung, in englischer Sprache).

### <a name="caching"></a>Hinzufügen des Authentifizierungscodes zur App

Der folgende Code startet den Serverfluss-Anmeldeprozess mit dem Google-Anbieter:

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Sie können die ID des angemeldeten Benutzers von einem **MobileServiceUser** mit der **getUserId**-Methode abrufen. Ein Beispiel zur Verwendung von „Futures“ zum Abrufen der asynchronen APIs für die Anmeldung finden Sie unter [Erste Schritte mit Authentifizierungen].


### <a name="caching"></a>Zwischenspeichern von Authentifizierungstokens

Beim Zwischenspeichern von Authentifizierungstoken müssen Benutzer-ID und Authentifizierungstoken lokal auf dem Gerät gespeichert werden. Beim nächsten Start der App prüfen Sie den Cache und können den Anmeldevorgang überspringen und den Client mit diesen Daten aktivieren, sofern sie vorhanden sind. Diese Daten sind jedoch vertraulich und sollten aus Sicherheitsgründen verschlüsselt gespeichert werden, falls das Telefon gestohlen wird.

Ein vollständiges Beispiel zum Zwischenspeichern von Authentifizierungstoken finden Sie im Abschnitt [Zwischenspeichern von Authentifizierungstoken](app-service-mobile-android-get-started-users.md#cache-tokens).

Wenn Sie versuchen, ein abgelaufenes Token zu verwenden, erhalten Sie die Antwort *401 nicht autorisiert*. Der Benutzer muss sich erneut anmelden, um ein neues Token zu erhalten. Um den Code zur Behandlung dieses Falls nicht an jeder Stelle in Ihrer App schreiben zu müssen, an der der mobile Dienst aufgerufen wird, können Sie Filter verwenden, mit denen Sie Aufrufe an und Antworten von Mobile Services abfangen können. Der Filtercode prüft Antworten auf 401, löst bei Bedarf den Anmeldeprozess aus und setzt anschließend die Anfrage fort, die den 401-Fehler ausgelöst hatte. Sie können auch das Token auf Ablauf überprüfen.


## <a name="adal"></a>Gewusst wie: Authentifizieren von Benutzern mit der Active Directory-Authentifizierungsbibliothek

Nutzen Sie die Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL), um Benutzer mithilfe von Azure Active Directory bei Ihrer Anwendung anzumelden. Dies wird aufgrund der besseren Bedienbarkeit und der Möglichkeit, zusätzliche Anpassungen vorzunehmen, den `loginAsync()`-Methoden häufig vorgezogen.

1. Konfigurieren Sie Ihr mobiles App-Back-End für die AAD-Anmeldung, indem Sie die im Tutorial [Gewusst wie: Konfigurieren von App Service für die Active Directory-Anmeldung](app-service-mobile-how-to-configure-active-directory-authentication.md) beschriebenen Schritte ausführen. Schließen Sie auch den optionalen Schritt zur Registrierung einer nativen Clientanwendung ab.

2. Installieren Sie ADAL, indem Sie die Datei „build.gradle“ so ändern, dass sie Folgendes enthält:

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. Fügen Sie Ihrer Anwendung den folgenden Code hinzu, und nehmen Sie die folgenden Ersetzungen vor:

* Ersetzen Sie **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, unter dem Sie Ihre Anwendung bereitgestellt haben. Das Format sollte https://login.windows.net/contoso.onmicrosoft.com entsprechen. Sie können diesen Wert auf der Registerkarte „Domäne“ in Ihrer Azure Active Directory-Instanz im [klassischen Azure-Portal] kopieren.

* Ersetzen Sie **INSERT-RESOURCE-ID-HERE** durch die Client-ID für Ihr Mobile App-Back-End. Sie finden diese im Portal auf der Registerkarte **Erweitert** unter **Azure Active Directory-Einstellungen**.

* Ersetzen Sie **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der nativen Clientanwendung kopiert haben.

* Ersetzen Sie **INSERT-REDIRECT-URI-HERE** durch den Endpunkt _/.auth/login/done_ Ihrer Website mithilfe des HTTPS-Schemas. Dieser Wert sollte etwa so aussehen: \__https://contoso.azurewebsites.net/.auth/login/done_.

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## Hinzufügen von Pushbenachrichtigungen zur App

In der [Übersicht](notification-hubs-overview.md/#integration-with-app-service-mobile-apps) erfahren Sie, wie Microsoft Azure Notification Hubs eine Vielzahl von Pushbenachrichtigungen unterstützt.

In [diesem Tutorial](app-service-mobile-android-get-started-push.md) wird bei jedem Einfügen eines Datensatzes eine Pushbenachrichtigung gesendet.

## Hinzufügen von Offlinesynchronisierung zu Ihrer App
Das Schnellstarttutorial enthält Code, der Offlinesynchronisierung implementiert. Suchen Sie nach Code, dem Kommentare wie der folgende vorangestellt sind:

	// Offline Sync

Indem Sie die Auskommentierung der folgenden Codezeilen aufheben, können Sie Offlinesynchronisierung implementieren, und Sie können mit ähnlichem Code den Code anderer Mobile Apps ergänzen.

##<a name="customizing"></a>Gewusst wie: Anpassen des Clients

Sie haben mehrere Möglichkeiten, das Standardverhalten des Clients anzupassen.

### <a name="headers"></a>Gewusst wie: Anpassen der Anforderungsheader

Sie können benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen. Konfigurieren Sie dazu einen **ServiceFilter**, wie im folgenden Beispiel:

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Gewusst wie: Anpassen der Serialisierung

Der Client geht davon aus, dass alle Tabellennamen, Spaltennamen und Datentypen im Back-End genau den im Client definierten Datenobjekten entsprechen. Diese Namen können sich jedoch aus verschiedensten Gründen auf Server und Client unterschieden. In Ihrem Szenario möchten Sie vielleicht eine der folgenden Arten von Anpassungen vornehmen:

- Die Spaltennamen der Tabelle im mobilen Dienst entsprechen nicht den Namen, die Ihr Client verwendet.
- Verwenden Sie eine Tabelle in einem mobilen Dienst mit einem anderen Namen als die Klasse, die der Tabelle im Client zugeordnet ist.
- Aktivieren Sie die automatische Großschreibung von Eigenschaften.
- Fügen Sie Objekten komplexe Eigenschaften hinzu.

### <a name="columns"></a>Zuordnen unterschiedlicher Namen zwischen Client und Server

Angenommen, Ihr Java-Clientcode verwendet Namen nach Java-Standardschema für die *ToDoItem*-Objekteigenschaften.

- mId
- mText
- mComplete
- mDuration


Sie müssen die clientseitigen Namen zu JSON-Namen serialisieren, die den Spaltennamen in der *ToDoItem*-Tabelle auf dem Server entsprechen. Der folgende Code verwendet die [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801)-Bibliothek und tut eben dies.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Gewusst wie: Zuordnen unterschiedlicher Tabellennamen zwischen Client und Back-End

Die Zuordnung von Client-Tabellennamen zu anderen Tabellennamen im mobilen Dienst ist einfach. Verwenden Sie dazu eine der Überschreibungen der <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>-Funktion, wie im folgenden Code gezeigt.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Automatisieren von Tabellennamenzuordnungen

Die Zuordnung von Spaltennamen für kleine Tabellen mit nur wenigen Spalten ist kein großer Aufwand, wie zuvor gezeigt. Angenommen, die Tabelle hat jedoch viele Spalten, z. B. 20 oder 30. In diesem Fall können wir die <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>-API aufrufen und eine Umwandlungsstrategie für alle Spalten angeben, um nicht jeden Spaltennamen einzeln zuordnen zu müssen.

Dazu verwenden wir die <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>-Bibliothek, mit der die Android-Clientbibliothek im Hintergrund Java-Objekte nach JSON-Daten serialisiert, die anschließend an Azure Mobile Services übertragen werden.

Der folgende Code verwendet die *setFieldNamingStrategy()*-Methode, in der wir eine *FieldNamingStrategy()*-Methode definieren. Diese Methode legt für alle Feldnamen fest, dass das erste Zeichen (ein "m") gelöscht und das folgende Zeichen zu einem Kleinbuchstaben umgewandelt werden soll. Dieser Code aktiviert außerdem die lesbare Ausgabe (Pretty Printing) des Ausgangs-JSON.

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());



Dieser Code muss vor jeglichen Methodenaufrufen auf das Mobile Services-Clientobjekt ausgeführt werden.

### <a name="complex"></a>Speichern von Objekt- oder Array-Eigenschaften in einer Tabelle

Unsere bisherigen Serialisierungsbeispiele verwenden einfache Typen wie Ganzzahlen und Zeichenfolgen, die problemlos nach JSON serialisiert und in die Tabelle im mobilen Dienst eingefügt werden können. Nehmen wir an, wir möchten ein komplexes Objekt in unserem Clienttyp hinzufügen, das nicht automatisch nach JSON serialisiert und in die Tabelle eingefügt werden kann. Wir könnten beispielsweise ein Array von Zeichenfolgen in das Client-Objekt einfügen. In diesem Fall müssen wir selbst angeben, wie das Objekt serialisiert und das Array in die Tabelle im mobilen Dienst einzufügen ist.

Ein Beispiel hierzu finden Sie im Blogeintrag <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Customizing serialization using the <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> library in the Mobile Services Android client</a> (Benutzerdefinierte Serialisierung mit der gson-Bibliothek im Android-Client für mobile Dienste, in englischer Sprache).

Sie können diese allgemeine Methode immer dann verwenden, wenn Sie mit komplexen Objekten arbeiten, die nicht automatisch nach JSON serialisiert und in Tabellen in mobilen Diensten eingefügt werden können.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Zurückgeben aller Elemente]: #showAll
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure-Portal]: https://portal.azure.com
[Get started with authentication]: app-service-mobile-android-get-started-users.md
[Erste Schritte mit Authentifizierungen]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0309_2016-->
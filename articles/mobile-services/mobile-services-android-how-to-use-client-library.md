<properties
	pageTitle="Arbeiten mit der Mobile Services-Android-Clientbibliothek"
	description="Erfahren Sie mehr über die Verwendung eines Android-Clients für Azure Mobile Services."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>


# Verwenden der Android-Clientbibliothek für Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des Android-Clients für Azure Mobile Services. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern, Fehlerbehandlung und Anpassungen des Clients.

Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst das Schnellstart-Lernprogramm [Erste Schritte mit Mobile Services] absolvieren. Durch das erfolgreiche Abschließen dieses Lernprogramms wird sichergestellt, dass Sie Android Studio installiert haben. Es unterstützt Sie beim Konfigurieren Ihres Kontos und Erstellen Ihres ersten mobilen Diensts sowie beim Installieren des Mobile Services SDKs, das Android Version 2.2 oder höher unterstützt, wobei wir jedoch die Erstellung für Android Version 4.2 oder höher empfehlen.

Sie finden die Javadocs-API-Referenz für die Android-Clientbibliothek [hier](http://go.microsoft.com/fwlink/p/?LinkId=298735).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Einrichtung und Voraussetzungen

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://go.microsoft.com/fwlink/p/?LinkId=298592). Der Code in diesem Artikel verwendet eine Tabelle mit dem Namen *TodoItem* und den folgenden Spalten:

- id
- text
- complete

Das entsprechende typisierte clientseitige Objekt sieht wie folgt aus:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Wenn das dynamische Schema aktiviert ist, generiert Azure Mobile Services automatisch neue Spalten anhand des Objekts in der Einfüge- oder Updateanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://go.microsoft.com/fwlink/p/?LinkId=296271).

##<a name="create-client"></a>Gewusst wie: Erstellen des Mobile Services-Clients
Der folgende Code erstellt das [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html)-Objekt, das für den Zugriff auf Ihren mobilen Dienst verwendet wird. Der Code gehört in die `onCreate`-Methode der Activity-Klasse, die in *AndroidManifest.xml* als **MAIN**-Aktion und **LAUNCHER**-Kategorie angegeben ist.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key
				this)

Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel des mobilen Diensts in dieser Reihenfolge. Beide Werte finden Sie im klassischen Azure-Portal, indem Sie Ihren mobilen Dienst auswählen und auf *Dashboard* klicken.

##<a name="instantiating"></a>Erstellen von Tabellenverweisen

Der einfachste Weg zum Abfragen oder Ändern von Daten in mobilen Diensten ist das *typisierte Programmiermodell*, da Java eine stark typisierte Sprache ist (später besprechen wir das *untypisierte* Modell). Dieses Modell bietet eine nahtlose Serialisierung und Deserialisierung in JSON mithilfe der [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801)-Bibliothek, wenn Daten zwischen dem Client und dem mobilen Dienst gesendet werden: Der Entwickler muss nicht eingreifen, da das Framework alle Aufgaben übernimmt.

Der erste Schritt zum Abfragen und Ändern von Daten ist die Erstellung eines [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835)-Objekts durch Aufrufen der **getTable**-Methode im [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Wir betrachten zwei Überladungen dieser Methode:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Im folgenden Code ist *mClient* ein Verweis auf Ihren Client für den mobilen Dienst.

Die [erste Überladung](http://go.microsoft.com/fwlink/p/?LinkId=296839) wird verwendet, wenn Klassenname und Tabellenname gleich sind:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


Die [zweite Überladung](http://go.microsoft.com/fwlink/p/?LinkId=296840) wird verwendet, wenn sich Tabellenname und Typname unterscheiden.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>Die API-Struktur

Seit Version 2.0 der Clientbibliothek verwenden Tabellevorgänge mobiler Dienste die Objekte [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) und [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) in allen asynchronen Vorgängen, z. B. in Methoden, die Abfragen und Vorgänge wie Einfügungen, Aktualisierungen und Löschungen einbeziehen. Dies erleichtert die Ausführungen mehrerer Vorgänge (in einem Hintergrundthread), ohne sich um mehrere geschachtelte Rückrufe kümmern zu müssen.


##<a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihren mobilen Dienst stellen können. Die Unterabschnitte beschreiben Aspekte wie z. B. Sortierung, Filterung und Seitenverwaltung. Zuletzt besprechen wir die Möglichkeit der Verkettung dieser Operationen.

### <a name="showAll"></a>Zurückgeben aller Elemente einer Tabelle

Der folgende Code gibt alle Elemente aus der *ToDoItem*-Tabelle zurück. Die Elemente werden auf der Benutzeroberfläche durch Hinzufügen der Elemente zu einem Adapter angezeigt. Dieser Code ist vergleichbar mit dem im Schnellstart-Lernprogramm [Erste Schritte mit Mobile Services] enthaltenen Code.

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


Abfragen dieser Art verwenden das [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html)-Objekt.

Die *result*-Variable gibt das Resultset der Abfrage zurück und der Code nach der `mToDoTable.execute().get()`-Anweisung veranschaulicht, wie die einzelnen Zeilen angezeigt werden.


### <a name="filtering"></a>Filtern zurückgegebener Daten

Der folgende Code gibt alle Elemente der *ToDoItem*-Tabelle zurück, deren *complete*-Feld gleich *false* ist. *mToDoTable* ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result =
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



Sie initialisieren den Filter mit einem [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867)-Methodenaufruf auf den Tabellenverweis. Dieser Code wird gefolgt von einem [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869)-Methodenaufruf, der das logische Prädikat angibt. Mögliche Prädikatmethoden sind [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) usw.

Dies ist ausreichend für Vergleiche von numerischen Feldern und Zeichenfolgen mit bestimmten Werten. Sie können jedoch noch viel mehr tun.

Sie können beispielsweise nach Daten filtern. Sie können entweder das gesamte Datumsfeld oder Teile des Datums mit Methoden wie [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) und [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472) vergleichen. Die folgende Codezeile verwendet einen Filter mit *due date* gleich 2013.

		mToDoTable.where().year("due").eq(2013).execute().get();

Sie können eine Reihe komplexer Filter für Zeichenfolgenfelder verwenden, wie z. B. [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) und [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). Die folgende Codezeile filtert nach Tabellenzeilen, deren *text*-Spalte mit "PRI0" beginnt.

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

Für numerische Felder existiert ebenfalls eine Reihe komplexer Filter mit Methoden wie z. B. [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) und [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). Die folgende Codezeile filtert nach Tabellenzeilen, deren *duration* eine gerade Zahl ist.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


Sie können Prädikate mit Methoden wie [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) und [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515) verknüpfen. Die folgende Codezeile kombiniert die beiden obigen Beispiele.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

Außerdem können Sie logische Operatoren gruppieren und schachteln, wie der folgende Code zeigt:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

Eine detailliertere Besprechung und weitere Beispiele für Filter finden Sie unter [Exploring the richness of the Mobile Services Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (Besprechung des Abfragemodells für Android-Clients und mobile Dienste, in englischer Sprache).

### <a name="sorting"></a>Sortieren zurückgegebener Daten

Der folgende Code gibt alle Elemente aus einer *ToDoItems*-Tabelle in aufsteigender Reihenfolge nach dem *text*-Feld sortiert zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Tabelle des mobilen Diensts.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Der erste Parameter der [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519)-Methode ist eine Zeichenfolge mit dem Namen des Felds, nach dem sortiert werden soll.

Der zweite Parameter verwendet die Aufzählung [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521), um anzugeben, ob auf- oder absteigend sortiert werden soll.

Beachten Sie, dass beim Filtern mit der ***where***-Methode die ***where***-Methode vor dem Aufruf der ***orderBy***-Methode aufgerufen werden muss.

### <a name="paging"></a>Seitenweises Zurückgeben von Daten

Das erste Beispiel zeigt, wie Sie die ersten fünf Elemente einer Tabelle abrufen können. Die Abfrage gibt die Elemente aus einer *ToDoItems*-Tabelle zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Tabelle des mobilen Diensts.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


Anschließend definieren wir eine Abfrage, welche die ersten fünf Elemente überspringt und die nächsten fünf zurückgibt.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Auswählen bestimmter Spalten

Der folgende Code veranschaulicht, wie alle Elemente aus einer *ToDoItems*-Tabelle zurückgegeben werden, zeigt jedoch nur das *complete*-Feld und das *text*-Feld an. *mToDoTable* ist der Verweis auf die zuvor erstellte Tabelle des mobilen Diensts.

		mToDoTable.select("complete", "text").execute().get();


Die Parameter der select-Funktion sind in diesem Fall die Namen der Tabellenspalten, die Sie zurückgeben möchten.

Die [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689)-Methode folgt im Anschluss an Methoden wie [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) und [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), falls diese existieren. Nach Select können Methoden wie z. B. [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731) folgen.

### <a name="chaining"></a>Verketten von Abfragemethoden

Die Methoden zum Abfragen von Tabellen in mobilen Diensten können verkettet werden. Auf diese Weise können Sie z. B. spezielle Spalten gefilterter Zeilen mit Sortierung und Seitenverwaltung abfragen. Sie können komplexe logische Filter erstellen.

Dies funktioniert dank der von den Abfragemethoden zurückgegebenen [**MobileServiceQuery<T>**](http://go.microsoft.com/fwlink/p/?LinkId=298551)-Objekten, in denen wiederum zusätzliche Methoden aufgerufen werden können. Um die Methodenverkettung zu beenden und die Abfrage auszuführen, rufen Sie die [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554)-Methode auf.

Das folgende Codebeispiel verwendet *mToDoTable* als Verweis auf die Tabelle *ToDoItem* im mobilen Dienst.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)
					.execute().get();

Beachten Sie, dass beim Verketten von Methoden die *where*-Methode und Prädikate immer an erster Stelle stehen müssen. Anschließend können Sie Folgemethoden in der Reihenfolge aufrufen, in der Ihre Anwendung diese benötigt.


##<a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können.

Zunächst instanziieren Sie ein Objekt der *ToDoItem*-Klasse und setzen dessen Eigenschaften.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

 Als Nächstes führen Sie den folgenden Code aus:

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


Dieser Code fügt ein neues Element ein und fügt es dem Adapter hinzu, damit es in der Benutzeroberfläche angezeigt wird.

Mobile Services unterstützen eindeutige benutzerdefinierte Zeichenfolgenwerte für die Tabellen-ID. Daher können Anwendungen benutzerdefinierte Werte, wie E-Mail-Adressen oder Benutzernamen, für die ID-Spalte einer Mobile Services-Tabelle verwenden. Wenn die Zeile beispielsweise durch eine E-Mail-Adresse identifiziert werden soll, können Sie das folgende JSON-Objekt verwenden.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

Falls beim Einfügen eines Datensatzes in eine Tabelle kein Id-Wert angegeben wird, generiert der mobile Dienst einen eindeutigen Wert für die Id.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

+ Ids können ohne Roundtrip zur Datenbank generiert werden.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und weist diese der ID eines neuen Datensatzes zu. Dieser Wert ähnelt dem ID-Wert, den Mobile Services generieren würde, wenn Sie für die ID eines Datensatzes keinen Wert übergeben.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Wenn eine Anwendung einen Id-Wert übergibt, speichert der mobile Dienst diesen Wert unverändert. Dies beinhaltet auch vor- und nachstehende Leerzeichen. Leerzeichen werden aus dem Wert gekürzt.

Der `id`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

+ Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1].
+  Druckbare Zeichen: **"**(0 x 0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter "CLI zum Verwalten von Mobile Services-Tabellen".


##<a name="updating"></a>Gewusst wie: Aktualisieren von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie Daten in einer Tabelle aktualisieren können. In diesem Beispiel ist *item* ein Verweis auf eine Zeile in der *ToDoItem*-Tabelle, an der einige Änderungen vorgenommen wurden. Die folgende Methode aktualisiert die Tabelle und den UI-Adapter.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }

	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>Gewusst wie:Löschen von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie Daten in einer Tabelle löschen können. Er löscht ein vorhandenes Element aus der ToDoItem-Tabelle, für die das Kontrollkästchen **Abgeschlossen** in der Benutzeroberfläche aktiviert ist.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


Der folgende Code zeigt eine weitere Möglichkeit, dies zu erreichen. Der Code löscht ein existierendes Element in der ToDoItem-Tabelle, indem er den Wert des id-Felds der zu löschenden Zeile angibt (in diesem Fall "2FA404AB-E458-44CD-BC1B-3BC847EF0902"). In einer realen App würden Sie die ID auf irgendeine Art aufnehmen und als Variable übergeben. Um hier das Testen zu vereinfachen, können Sie im klassischen Azure-Portal zu Ihrem Dienst wechseln, auf **Daten** klicken und eine ID kopieren, die Sie für den Test verwenden möchten.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>Gewusst wie: Abfragen bestimmter Elemente
Manchmal müssen Sie ein bestimmtes Objekt anhand dessen *id* abfragen im Gegensatz zu Abfragen, bei denen Sie eine Reihe von Objekten erhalten, die bestimmte Kriterien erfüllen. Der folgende Code zeigt, wie Sie dies für einen *ID*-Wert von `0380BAFB-BCFF-443C-B7D5-30199F730335` durchführen können. In einer realen App würden Sie die ID auf irgendeine Art aufnehmen und als Variable übergeben. Um hier das Testen zu vereinfachen, können Sie im klassischen Azure-Portal zu Ihrem Dienst wechseln, auf die Registerkarte **Daten** klicken und eine ID kopieren, die Sie für den Test verwenden möchten.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>Arbeiten mit nicht typisierten Daten

Mit dem untypisierten Programmiermodell erhalten Sie vollständige Kontrolle über die JSON-Serialisierung. Dies ist hilfreich in bestimmten Szenarien, z. B. wenn eine Tabelle in Ihrem mobilen Dienst eine große Anzahl an Spalten enthält, und Sie nur einige wenige Spalten benötigen. Im typisierten Modell müssen Sie alle Spalten der Tabelle in Ihrem mobilen Dienst in Ihrer Datenklasse definieren. Im untypisierten Modell definieren Sie dagegen nur die Spalten, die Sie verwenden möchten.

Die meisten API-Aufrufe für den Datenzugriff sind gleich wie beim typisierten Programmiermodell. Im untypisierten Modell rufen Sie Methoden des **MobileServiceJsonTable**-Objekts anstelle des **MobileServiceTable**-Objekts auf.


### <a name="json_instance"></a>Erstellen einer Instanz einer nicht typisierten Tabelle

Ähnlich wie beim typisierten Modell benötigen Sie zunächst einen Tabellenverweis. In diesem Fall handelt es sich jedoch um ein [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733)-Objekt. Sie erhalten den Verweis durch einen Aufruf der [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734)-Methode auf einer Instanz des Mobile Services-Clients.

Zunächst definieren Sie die Variablen:

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



Sobald Sie eine Instanz des Mobile Services-Clients in der **onCreate**-Methode erstellt haben (in diesem Fall die *mClient*-Variable), können Sie mit dem folgenden Code eine Instanz von **MobileServiceJsonTable** erstellen.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

Sobald Sie eine Instanz von **MobileServiceJsonTable** erstellt haben, können Sie beinahe alle Methoden aufrufen, die Sie auch beim typisierten Programmiermodell zur Verfügung haben. In manchen Fällen nehmen die Methoden jedoch untypisierte Parameter entgegen, wie Sie in den folgenden Beispielen sehen werden.

### <a name="json_insert"></a>Einfügen in nicht typisierte Tabellen

Der folgende Code zeigt, wie Sie Elemente einfügen können. Sie müssen zunächst ein [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html) erstellen, das Teil der <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>-Bibliothek ist.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

Anschließend fügen Sie das Objekt ein. Die an die [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535)-Methode übergebene Rückruffunktion ist eine Instanz der [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532)-Klasse. Beachten Sie, dass der Parameter der *insert*-Methode ein JsonObject ist.

        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


Verwenden Sie den folgenden Methodenaufruf, falls Sie die ID des eingefügten Objekts abrufen müssen:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Löschen aus einer nicht typisierten Tabelle

Der folgende Code zeigt, wie Sie eine Instanz löschen, in diesem Fall die Instanz des **JsonObject**, die Sie im vorigen *insert*-Beispiel eingefügt haben. Beachten Sie, dass der Code derselbe wie beim typisierten Fall ist, aber die Methode weist eine andere Signatur auf, da sie auf ein **JsonObject** verweist.


         mToDoTable.delete(item);


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


##<a name="binding"></a>Gewusst wie: Datenbindung in der Benutzeroberfläche

Datenbindung besteht aus drei Komponenten:

- die Datenquelle
- das Bildschirmlayout
- und der Adapter, der diese beiden Komponenten verbindet.

In unserem Beispielcode geben wir die Daten aus der Tabelle *ToDoItem* im mobilen Dienst in ein Array zurück. Dies ist ein häufiges Muster für Datenanwendungen: Datenbankabfragen geben normalerweise eine Sammlung von Zeilen in Form von Listen oder Arrays an den Client zurück. Das Array ist in diesem Fall die Datenquelle.

Der Code definiert ein Bildschirmlayout für die Anzeige der Daten, die auf dem Gerät erscheinen sollen.

Diese beiden Komponenten sind über einen Adapter verbunden, der in diesem Code eine Erweiterung der *ArrayAdapter&lt;ToDoItem&gt;*-Klasse ist.

### <a name="layout"></a>Definieren des Layouts

Das Layout wird durch mehrere XML-Codeabschnitte definiert. Anhand eines existierenden Layouts nehmen wir an, dass der folgende Code die **ListView** darstellt, die wir mit unseren Serverdaten füllen möchten.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


Das *listitem*-Attribut im obigen Code definiert die id des Layouts für eine bestimmte Zeile in der Liste. Der folgende Code definiert ein Kontrollkästchen und dessen zugehörigen Text. Dieses Element wird einmal pro Listenelement instanziiert. Ein komplexeres Layout könnte zusätzliche Felder in der Anzeige definieren. Dieser Code befindet sich in der Datei *row\_list\_to\_do.xml*.

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


### <a name="adapter"></a>Definieren des Adapters

Da die Datenquelle in unserer Ansicht ein *ToDoItem* ist, leiten wir unseren Adapter von der *ArrayAdapter&lt;ToDoItem&gt;*-Klasse ab. Diese Unterklasse produziert eine Ansicht für jedes *ToDoItem* und verwendet dabei das *row\_list\_to\_do*-Layout.

In unserem Code definieren wir die folgende Klasse als Erweiterung der *ArrayAdapter&lt;E&gt;*-Klasse:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Sie müssen die *getView*-Methode der Klasse überschreiben. Dieser Beispielcode ist ein Beispiel dafür: Die Einzelheiten sind von der jeweiligen Anwendung abhängig.

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

		return row;
	}

Wir erstellen eine Instanz dieser Klasse in unserer Aktivität wie folgt:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Beachten Sie, dass der zweite Parameter des ToDoItemAdapter-Konstruktors ein Verweis auf das Layout ist. Im Anschluss an den Aufruf des Konstruktors steht der folgende Code, der zunächst einen Verweis auf **ListView** abruft und anschließend *setAdapter* aufruft, um sich selbst für den soeben erstellten Adapter zu konfigurieren:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Verwenden des Adapters

Sie sind nun in der Lage, die Datenbindung zu verwenden. Der folgende Code zeigt, wie Sie die Elemente aus der Tabelle im mobilen Dienst abrufen, den Adapter leeren und anschließend dessen *add*-Methode aufrufen, um ihn mit den abgerufenen Elementen zu füllen.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
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

Sie müssen den Adapter jedes mal aufrufen, wenn Sie die *ToDoItem*-Tabelle ändern und das Ergebnis der Änderungen anzeigen möchten. Da Veränderungen auf einzelnen Einträgen erfolgen, erhalten Sie in diesem Fall eine einzelne Zeile anstatt einer Sammlung. Beim Einfügen von Elementen rufen Sie die *add*-Methode des Adapters auf und beim Löschen von Elementen dessen *remove*-Methode.

##<a name="custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats. Ein Beispiel für das Erstellen einer benutzerdefinierten API in Ihrem mobilen Dienst finden Sie unter [Gewusst wie: Definieren eines benutzerdefinierten API-Endpunkts](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


##<a name="authentication"></a>Authentifizieren von Benutzern

Mobile Services unterstützt Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln auf Ihrem Back-End zu implementieren. Weitere Informationen finden Sie unter [Get started with authentication](http://go.microsoft.com/fwlink/p/?LinkId=296316) (Erste Schritte zur Authentifizierung, in englischer Sprache).

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein *Serverfluss* und ein *Clientfluss*. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

Für die Authentifizierung in Ihrer App sind drei Schritte erforderlich:

- Registrieren Ihrer App für die Authentifizierung mit einem Anbieter und Konfigurieren von Mobile Services
- Einschränken von Tabellenberechtigungen für authentifizierte Benutzer
- Hinzufügen des Authentifizierungscodes zu Ihrer App


Mobile Services unterstützt die folgenden existierenden Identitätsanbieter für die Authentifizierung Ihrer Benutzer:

- Microsoft-Konto
- Facebook
- Twitter
- Google
- Azure Active Directory

Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die IDs authentifizierter Benutzer verwenden, um Anfragen zu verändern.

Diese beiden ersten Aufgaben können Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) erledigen. Weitere Informationen finden Sie unter [Get started with authentication](http://go.microsoft.com/fwlink/p/?LinkId=296316) (Erste Schritte zur Authentifizierung, in englischer Sprache).

### <a name="caching"></a>Hinzufügen des Authentifizierungscodes zur App

1.  Fügen Sie die folgenden import-Anweisungen zur Aktivitätsdatei Ihrer App hinzu.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Fügen Sie in der **onCreate**-Methode der Aktivitätsklasse die folgende Codezeile hinter dem Code ein, mit dem das `MobileServiceClient`-Objekt erstellt wird: Dabei wird davon ausgegangen, dass *mClient* der Verweis auf das `MobileServiceClient`-Objekt ist.

	    // Login using the Google provider.

		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();
    		}
    	});

    Dieser Code authentifiziert einen Benutzer mit einer Google-Anmeldung. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    > [AZURE.NOTE]Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die **login**-Methode übergebenen Wert in einen der folgenden Werte: _MicrosoftAccount_, _Facebook_, _Twitter_ oder _WindowsAzureActiveDirectory_.


3. Beim Ausführen der App können Sie sich mit dem Identitätsanbieter Ihrer Wahl anmelden.


### <a name="caching"></a>Zwischenspeichern von Authentifizierungstokens

Dieser Abschnitt beschreibt die Zwischenspeicherung von Authentifizierungstokens. Durch dieses Verfahren müssen sich Benutzer nicht erneut authentifizieren, wenn die App in den Ruhezustand versetzt wird und das Token noch gültig ist.

Beim Zwischenspeichern von Authentifizierungstoken müssen Benutzer-ID und Authentifizierungstoken lokal auf dem Gerät gespeichert werden. Beim nächsten Start der App prüfen Sie den Cache und können den Anmeldevorgang überspringen und den Client mit diesen Daten aktivieren, sofern sie vorhanden sind. Diese Daten sind jedoch vertraulich und sollten aus Sicherheitsgründen verschlüsselt gespeichert werden, falls das Telefon gestohlen wird.

Der folgende Codeausschnitt veranschaulicht, wie ein Token für die Anmeldung über ein Microsoft-Konto abgerufen wird. Das Token wird zwischengespeichert und erneut geladen, wenn der Cache gefunden wird.

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();
		    		}
		    	});		}
	}


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined");
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined");
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


Was geschieht also, wenn Ihr Token abläuft? In diesem Fall erhalten Sie beim Verbindungsversuch die Antwort *401 unauthorized*. Der Benutzer muss sich erneut anmelden, um ein neues Token zu erhalten. Um den Code zur Behandlung dieses Falls nicht an jeder Stelle in Ihrer App schreiben zu müssen, an der der mobile Dienst aufgerufen wird, können Sie Filter verwenden, mit denen Sie Aufrufe an und Antworten von Mobile Services abfangen können. Der Filtercode prüft Antworten auf 401, löst bei Bedarf den Anmeldeprozess aus und setzt anschließend die Anfrage fort, die den 401-Fehler ausgelöst hatte.


##<a name="customizing"></a>Gewusst wie: Anpassen des Clients

Es gibt mehrere Möglichkeiten, das Standardverhalten des Mobile Services-Clients anzupassen.

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

Mobile Services nimmt standardmäßig an, dass Tabellen- und Spaltennamen sowie Datentypen auf Client und Server exakt gleich sind. Diese Namen können sich jedoch aus verschiedensten Gründen auf Server und Client unterschieden. Möglicherweise möchten Sie einen vorhandenen Client abändern, sodass dieser Mobile Services anstelle eines Konkurrenzprodukts verwendet.

Oder Sie möchten eine der folgenden Arten von Anpassungen vornehmen:

- Die Spaltennamen der Tabelle im mobilen Dienst entsprechen nicht den Namen, die Ihr Client verwendet
- Verwenden Sie eine Tabelle in einem mobilen Dienst mit einem anderen Namen als die Klasse, die der Tabelle im Client zugeordnet ist
- Aktivieren der automatischen Großschreibung von Eigenschaften
- Hinzufügen von komplexen Eigenschaften zu Objekten

### <a name="columns"></a>Zuordnen unterschiedlicher Namen zwischen Client und Server

Angenommen, Ihr Java-Clientcode verwendet Namen nach Java-Standardschema für die *ToDoItem*-Objekteigenschaften.

- mId
- mText
- mComplete
- mDuration


Sie müssen die clientseitigen Namen zu JSON-Namen serialisieren, die den Spaltennamen in der *ToDoItem*-Tabelle auf dem Server entsprechen. Der folgende Code, der die <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>-Bibliothek verwendet, tut genau dies.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Zuordnen unterschiedlicher Tabellennamen zwischen Client und mobilem Dienst

Die Zuordnung von Client-Tabellennamen zu anderen Tabellennamen im mobilen Dienst ist einfach. Verwenden Sie dazu eine der Überschreibungen der <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>-Funktion, wie im folgenden Code gezeigt.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Automatisieren von Tabellennamenzuordnungen

Die Zuordnung von Spaltennamen für kleine Tabellen mit nur wenigen Spalten ist kein großer Aufwand, wie zuvor gezeigt. Angenommen, die Tabelle hat jedoch viele Spalten, z. B. 20 oder 30. In diesem Fall können wir die <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>-API aufrufen und eine Umwandlungsstrategie für alle Spalten angeben, um nicht jeden Spaltennamen einzeln zuordnen zu müssen.

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
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
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
[Erste Schritte mit Mobile Services]: mobile-services-android-get-started.md
[ASCII-Steuerzeichen C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set

<!---HONumber=AcomDC_0121_2016-->
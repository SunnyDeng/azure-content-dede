<properties linkid="develop-mobile-how-to-guides/work-with-android-client-library" urlDisplayName="Android Client Library" pageTitle="Working with the Mobile Services Android Client Library" metaKeywords="" description="Learn how to use an Android client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the Android client library for Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Verwenden der Android-Clientbibliothek für Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/de-de/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/de-de/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/de-de/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des Android-Clients für Azure Mobile Services. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern, Fehlerbehandlung und Anpassungen des Clients. Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst den [Schnellstart für mobile Dienste][Schnellstart für mobile Dienste] absolvieren. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

Die Beispiele sind in Java geschrieben, und Sie benötigen das [Mobile Services-SDK][Mobile Services-SDK]. Für dieses Lernprogramm benötigen Sie außerdem das [Android SDK][Android SDK], das aus der integrierten Entwicklungsumgebung (IDE) Eclipse und dem Android Developer Tools (ADT)-Plugin besteht. Das Mobile Services-SDK unterstützt Android Version 2.2 oder später. Wir empfehlen jedoch die Verwendung von Android 4.2 oder später.

## Inhaltsverzeichnis

-   [Windows Azure Mobile Services][Windows Azure Mobile Services]
-   [Konzepte][Konzepte]
-   [Einrichtung und Voraussetzungen][Einrichtung und Voraussetzungen]
-   [Gewusst wie: Erstellen des Mobile Services-Clients][Gewusst wie: Erstellen des Mobile Services-Clients]
-   [Gewusst wie: Erstellen eines Tabellenverweises][Gewusst wie: Erstellen eines Tabellenverweises]

    -   [Die API-Struktur][Die API-Struktur]
-   [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst][Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]

    -   [Zurückgegebene Daten filtern][Zurückgegebene Daten filtern]
    -   [Zurückgegebene Daten sortieren][Zurückgegebene Daten sortieren]
    -   [Daten seitenweise zurückgeben][Daten seitenweise zurückgeben]
    -   [Bestimmte Spalten auswählen][Bestimmte Spalten auswählen]
    -   [Gewusst wie: Verketten von Abfragemethoden][Gewusst wie: Verketten von Abfragemethoden]
-   [Gewusst wie: Einfügen von Daten in einen mobilen Dienst][Gewusst wie: Einfügen von Daten in einen mobilen Dienst]
-   [Gewusst wie: Aktualisieren von Daten in einem mobilen Dienst][Gewusst wie: Aktualisieren von Daten in einem mobilen Dienst]
-   [Gewusst wie: Löschen von Daten in einem mobilen Dienst][Gewusst wie: Löschen von Daten in einem mobilen Dienst]
-   [Gewusst wie: Abfragen bestimmter Elemente][Gewusst wie: Abfragen bestimmter Elemente]
-   [Gewusst wie: Arbeiten mit nicht typisierten Daten][Gewusst wie: Arbeiten mit nicht typisierten Daten]
-   [Gewusst wie: Datenbindung in der Benutzeroberfläche][Gewusst wie: Datenbindung in der Benutzeroberfläche]

    -   [Gewusst wie: Definieren des Layouts][Gewusst wie: Definieren des Layouts]
    -   [Gewusst wie: Definieren des Adapters][Gewusst wie: Definieren des Adapters]
    -   [Gewusst wie: Verwenden des Adapters][Gewusst wie: Verwenden des Adapters]
-   [Gewusst wie: Authentifizieren von Benutzern][Gewusst wie: Authentifizieren von Benutzern]

    -   [Zwischenspeichern von Authentifizierungstokens][Zwischenspeichern von Authentifizierungstokens]
-   [Gewusst wie: Fehlerbehandlung][Gewusst wie: Fehlerbehandlung]
-   [Gewusst wie: Anpassen des Clients][Gewusst wie: Anpassen des Clients]

    -   [Anpassen der Anforderungsheader][Anpassen der Anforderungsheader]
    -   [Anpassen der Serialisierung][Anpassen der Serialisierung]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="setup"></a><span class="short-header">Einrichtung</span>Einrichtung und Voraussetzungen

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle][Erstellen einer Tabelle]. Der Code in diesem Artikel verwendet eine Tabelle mit dem Namen *TodoItem* und den folgenden Spalten:

-   id
-   text
-   complete
-   due
-   duration

Das entsprechende typisierte clientseitige Objekt sieht wie folgt aus:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
        private Date due
        private Integer duration;
    }

Wenn das dynamische Schema aktiviert ist, generiert Azure Mobile Services automatisch neue Spalten anhand des Objekts in der Einfüge- oder Updateanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema][Dynamisches Schema].

## <a name="create-client"></a><span class="short-header">Erstellen des mobile Dienste-Clients</span>Vorgehensweise: Erstellen des mobile Dienste-Clients

Der folgende Code erstellt das [MobileServiceClient][MobileServiceClient]-Objekt, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.

            MobileServiceClient mClient = new MobileServiceClient(
                    "MobileServiceUrl", // Replace with the above Site URL
                    "AppKey",           // replace with the Application Key 
                    this)

Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel des mobilen Diensts in dieser Reihenfolge. Beide Werte finden Sie im Azure-Verwaltungsportal, indem Sie Ihren mobilen Dienst auswählen und auf *Dashboard* klicken.

## <a name="instantiating"></a><span class="short-header">Erstellen eines Tabellenverweises</span>Vorgehensweise: Erstellen eines Tabellenverweises

Der einfachste Weg zum Abfragen oder Ändern von Daten in mobilen Diensten ist das *typisierte Programmiermodell*, da Java eine stark typisierte Sprache ist (später besprechen wir das *untypisierte* Modell). Dieses Modell bietet nahtlose Serialisierung und Deserialisierung nach JSON unter Verwendung der [gson][gson]-Bibliothek bei der Übertragung von Daten zwischen Client und mobilem Dienst: Das Framework nimmt dem Entwickler die gesamte Arbeit ab.

Der erste Schritt zum Abfragen und Ändern von Daten ist die Erstellung eines [MobileServiceTable][MobileServiceTable]-Objekts durch Aufrufen der **getTable**-Methode im [**MobileServiceClient**][MobileServiceClient]. Wir betrachten zwei Überladungen dieser Methode:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

Im folgenden Code ist *mClient* ein Verweis auf Ihren Client für den mobilen Dienst.

Die [erste Überladung][erste Überladung] wird verwendet, wenn Klassenname und Tabellenname gleich sind:

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

Die [zweite Überladung][zweite Überladung] wird verwendet, wenn sich Tabellenname und Typname unterscheiden.

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="api"></a>Die API-Struktur

Tabellenoperationen in mobilen Diensten verwenden das asynchrone Rückrufmodell. Alle Methoden mit Abfragen und Operationen wie Einfügen, Ändern und Löschen haben ein Rückruf-Objekt als Parameter. Dieses Objekt enthält immer eine **OnCompleted**-Methode. Die **onCompleted**-Methode enthält ein **Exception**-Objekt als Parameter, mit dem Sie ermitteln können, ob der Methodenaufruf erfolgreich war. Wenn das **Exception**-Objekt null ist, bedeutet dies einen erfolgreichen Aufruf, ansonsten enthält das **Exception**-Objekt den Fehlergrund.

Insgesamt existieren mehrere verschiedene Rückrufobjekte, deren Verwendung davon abhängt, ob Sie Daten abfragen, ändern oder löschen. Die Parameter der *onCompleted*-Methode unterscheiden sich je nach verwendetem Rückrufobjekt.

## <a name="querying"></a><span class="short-header">Abfragen von Daten</span>Vorgehensweise: Abfragen von Daten aus einem mobilen Dienst

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihren mobilen Dienst stellen können. Die Unterabschnitte beschreiben Aspekte wie z. B. Sortierung, Filterung und Seitenverwaltung. Zuletzt besprechen wir die Möglichkeit der Verkettung dieser Operationen.

Der folgende Code gibt alle Elemente aus der *ToDoItem*-Tabelle zurück.

        mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
                public void onCompleted(List<ToDoItem> result, int count,
                    Exception exception, ServiceFilterResponse response) {
                    if (exception == null) {
                        for (ToDoItem item : result) {
                            Log.i(TAG, "Read object with ID " + item.id);  
                        }
                    }
                }
            });

Abfragen dieser Art verwenden das [**TableQueryCallback\<E\>**][**TableQueryCallback\<E\>**]-Rückrufobjekt.

Der Parameter *result* gibt das Resultset der Abfrage zurück, und der Code in der Ergolgsabfrage des *exception*-Tests durchläuft die zurückgegebenen Zeilen.

### <a name="filtering"></a>Gewusst wie: Zurückgegebene Daten filtern

Der folgende Code gibt alle Elemente der *ToDoItem*-Tabelle zurück, deren *complete*-Feld gleich *false* ist. *mToDoTable* ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben.

        mToDoTable.where().field("complete").eq(false)
                  .execute(new TableQueryCallback<ToDoItem>() {
                        public void onCompleted(List<ToDoItem> result, 
                                                int count, 
                                                Exception exception,
                                                ServiceFilterResponse response) {
                if (exception == null) {
                    for (ToDoItem item : result) {
                        Log.i(TAG, "Read object with ID " + item.id);  
                    }
                } 
            }
        });

Sie initialisieren den Filter mit einem [**where**][**where**]-Methodenaufruf auf den Tabellenverweis. Dieser Code wird gefolgt von einem [**field**][**field**]-Methodenaufruf, der das logische Prädikat angibt. Mögliche Prädikatmethoden sind [**eq**][**eq**], [**ne**][**ne**], [**gt**][**gt**], [**ge**][**ge**], [**lt**][**lt**], [**le**][**le**] usw.

Dies ist ausreichend für Vergleiche von numerischen Feldern und Zeichenfolgen mit bestimmten Werten. Sie können jedoch noch viel mehr tun.

Sie können beispielsweise nach Daten filtern. Sie können entweder das gesamte Datumsfeld oder Teile des Datums mit Methoden wie [**year**][**year**], [**month**][**month**], [**day**][**day**], [**hour**][**hour**], [**minute**][**minute**] und [**second**][**second**] vergleichen. Die folgende Codezeile verwendet einen Filter mit *due date* gleich 2013.

        mToDoTable.where().year("due").eq(2013)

Sie können eine Reihe komplexer Filter für Zeichenfolgenfelder verwenden, wie z. B. [**startsWith**][**startsWith**], [**endsWith**][**endsWith**], [**concat**][**concat**], [**subString**][**subString**], [**indexOf**][**indexOf**], [**replace**][**replace**], [**toLower**][**toLower**], [**toUpper**][**toUpper**], [**trim**][**trim**] und [**length**][**length**]. Die folgende Codezeile filtert nach Tabellenzeilen, deren *text*-Spalte mit "PRI0" beginnt.

        mToDoTable.where().startsWith("text", "PRI0")

Für numerische Felder existiert ebenfalls eine Reihe komplexer Filter mit Methoden wie z. B. [**add**][**add**], [**sub**][**sub**], [**mul**][**mul**], [**div**][**div**], [**mod**][**mod**], [**floor**][**floor**], [**ceiling**][**ceiling**] und [**round**][**round**]. Die folgende Codezeile filtert nach Tabellenzeilen, deren *duration* eine gerade Zahl ist.

        mToDoTable.where().field("duration").mod(2).eq(0)

Sie können Prädikate mit Methoden wie [**and**][**and**], [**or**][**or**] und [**not**][**not**] verknüpfen. Die folgende Codezeile kombiniert die beiden obigen Beispiele.

        mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

Außerdem können Sie logische Operatoren gruppieren und schachteln, wie der folgende Code zeigt:

        mToDoTable.where()
                    .year("due").eq(2013)
                        .and
                    (startsWith("text", "PRI0").or().field("duration").gt(10))

Eine detailliertere Besprechung und weitere Beispiele für Filter finden Sie unter [Exploring the richness of the Mobile Services Android client query model][Exploring the richness of the Mobile Services Android client query model] (Besprechung des Abfragemodells für Android-Clients und mobile Dienste, in englischer Sprache).

### <a name="sorting"></a>Gewusst wie: Zurückgegebene Daten sortieren

Der folgende Code gibt alle Elemente aus einer *ToDoItems*-Tabelle in aufsteigender Reihenfolge nach dem *text*-Feld sortiert zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Tabelle des mobilen Diensts.

        mToDoTable.orderBy("text", QueryOrder.Ascending)
            .execute(new TableQueryCallback<ToDoItem>() { 
                /* same implementation as above */ 
            }); 

Der erste Parameter der [**orderBy**][**orderBy**]-Methode ist eine Zeichenfolge mit dem Namen des Felds, nach dem sortiert werden soll.

Der zweite Parameter verwendet die Aufzählung [**QueryOrder**][**QueryOrder**], um anzugeben, ob auf- oder absteigend sortiert werden soll.

Beachten Sie, dass beim Filtern mit der ***where***-Methode die ***where***-Methode vor dem Aufruf der ***orderBy***-Methode aufgerufen werden muss.

### <a name="paging"></a>Gewusst wie: Daten seitenweise zurückgeben

Das erste Beispiel zeigt, wie Sie die ersten fünf Elemente einer Tabelle abrufen können. Die Abfrage gibt die Elemente aus einer *ToDoItems*-Tabelle zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Tabelle des mobilen Diensts.

        mToDoTable.top(5)
                .execute(new TableQueryCallback<ToDoItem>() {   
                public void onCompleted(List<ToDoItem> result, 
                                        int count,
                                        Exception exception, 
                                        ServiceFilterResponse response) {
                    if (exception == null) {
                        for (ToDoItem item : result) {
                            Log.i(TAG, "Read object with ID " + item.id);  
                        }
                    } 
                }
            });

Anschließend definieren wir eine Abfrage, welche die ersten fünf Elemente überspringt und die nächsten fünf zurückgibt.

        mToDoTable.skip(5).top(5)
                .execute(new TableQueryCallback<ToDoItem>() {   
                // implement onCompleted logic here
            });

### <a name="selecting"></a>Gewusst wie: Bestimmte Spalten auswählen

Der folgende Code veranschaulicht, wie alle Elemente aus einer *ToDoItems*-Tabelle zurückgegeben werden, zeigt jedoch nur das *complete*-Feld und das *text*-Feld an. *mToDoTable* ist der Verweis auf die zuvor erstellte Tabelle des mobilen Diensts.

        mToDoTable.select("complete", "text")
                .execute(new TableQueryCallback<ToDoItem>() { 
                    /* same implementation as above */ 
            }); 

Die Parameter der select-Funktion sind in diesem Fall die Namen der Tabellenspalten, die Sie zurückgeben möchten.

Die [**select**][**select**]-Methode folgt im Anschluss an Methoden wie [**where**][1] und [**orderBy**][2], falls diese existieren. Nach Select können Methoden wie z. B. [**top**][**top**] folgen.

### <a name="chaining"></a>Gewusst wie: Verketten von Abfragemethoden

Die Methoden zum Abfragen von Tabellen in mobilen Diensten können verkettet werden. Auf diese Weise können Sie z. B. spezielle Spalten gefilterter Zeilen mit Sortierung und Seitenverwaltung abfragen. Sie können komplexe logische Filter erstellen.

Dies funktioniert dank der von den Abfragemethoden zurückgegebenen [**MobileServiceQuery\<T\>**][**MobileServiceQuery\<T\>**]-Objekten, die ihrerseits zusätzliche Methoden zum Aufruf enthalten können. Um die Methodenverkettung zu beenden und die Abfrage auszuführen, rufen Sie die [**execute**][**execute**]-Methode auf.

Das folgende Codebeispiel verwendet *mToDoTable* als Verweis auf die Tabelle *ToDoItem* im mobilen Dienst.

        mToDoTable.where().year("due").eq(2013)
                        .and().startsWith("text", "PRI0")
                        .or().field("duration").gt(10)
                    .select("id", "complete", "text", "duration")
                    .orderBy(duration, QueryOrder.Ascending).top(20)                
                    .execute(new TableQueryCallback<ToDoItem>() { 
                        /* code to execute */ 
                });

Beachten Sie, dass beim Verketten von Methoden die *where*-Methode und Prädikate immer an erster Stelle stehen müssen. Anschließend können Sie Folgemethoden in der Reihenfolge aufrufen, in der Ihre Anwendung diese benötigt.

## <a name="inserting"></a><span class="short-header">Einfügen von Daten</span>Vorgehensweise: Einfügen von Daten in einen mobilen Dienst

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können.

Zunächst instanziieren Sie ein Objekt der *ToDoItem*-Klasse und setzen dessen Eigenschaften.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 
        

Anschließend rufen Sie die [**insert**][**insert**]-Methode auf.

        mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                Exception exception, 
                                ServiceFilterResponse response) {   
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Für **insert**-Operationen ist das Rückrufobjekt vom Typ [**TableOperationCallback\<ToDoItem\>**][**TableOperationCallback\<ToDoItem\>**].

Der entity-Parameter der **onCompleted**-Methode enthält das neu eingefügte Objekt. Der Code für den Erfolgsfall zeigt, wie Sie auf die *id* der eingefügten Zeile zugreifen können.

Mobile Services unterstützen eindeutige benutzerdefinierte Zeichenfolgenwerte als Tabellen-ID. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der ID-Spalte einer Mobile Services-Tabelle verwenden. Wenn die Zeile beispielsweise durch eine E-Mail-Adresse identifiziert werden soll, können Sie das folgende JSON-Objekt verwenden.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.id = "myemail@mydomain.com";
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 

Falls beim Einfügen neuer Datensätze in eine Tabelle kein Wert für die Zeichenfolgen-ID angegeben wird, wird ein eindeutiger ID-Wert von Mobile Services generiert.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

-   Ids können ohne Roundtrip zur Datenbank generiert werden.
-   Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
-   Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und verwendet diese als Id für einen neuen Eintrag. Dieser Wert ähnelt dem Id-Wert, den der mobile Dienst generieren würde, wenn in der Abfrage kein Id-Wert übergeben wird.

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

-   Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1][ASCII-Steuerzeichen C0 und C1].
-   Druckbare Zeichen: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI to manage Mobile Services tables][CLI to manage Mobile Services tables] (CLI für Tabellen in mobilen Diensten, in englischer Sprache).

## <a name="updating"></a><span class="short-header">Aktualisieren von Daten</span>Gewusst wie: Aktualisieren von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie Daten in einer Tabelle aktualisieren können. In diesem Beispiel ist *mToDoItem* ein Verweis auf ein Element in der *ToDoItem*-Tabelle, und wir ändern dessen *duration*-Eigenschaft.

        mToDoItem.duration = 5;
        mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                    Exception exception, 
                                    ServiceFilterResponse response) {
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Beachten Sie, dass die Parameter des Rückrufobjekts und der *onCompleted*-Methode gleich sind wie beim Einfügen von Objekten.

## <a name="deleting"></a><span class="short-header">Löschen von Daten</span>Vorgehensweise: Löschen von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie Daten in einer Tabelle löschen können. Der Code löscht ein existierendes Element aus der ToDoItem-Tabelle anhand eines Verweises auf das Element, in diesem Fall *mToDoItem*.

        mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
            public void onCompleted(Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

Beachten Sie, dass das Rückrufobjekt im Fall von *delete* vom Typ [**TableDeleteCallback**][**TableDeleteCallback**] ist. Außerdem gibt die **onCompleted**-Methode keine Tabellenzeile zurück.

Der folgende Code zeigt eine weitere Möglichkeit, dies zu erreichen. Der Code löscht ein existierendes Element in der ToDoItem-Tabelle, indem er den Wert des id-Felds der zu löschenden Zeile angibt (in diesem Fall "37BBF396-11F0-4B39-85C8-B319C729AF6D").

        mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

## <a name="lookup"></a><span class="short-header">Abfragen von Daten</span>Gewusst wie: Abfragen bestimmter Elemente

Manchmal müssen Sie ein bestimmtes Objekt anhand dessen *id* abfragen im Gegensatz zu Abfragen, bei denen Sie eine Reihe von Objekten erhalten, die bestimmte Kriterien erfüllen. Der folgende Code ruft ein Objekt mit *id* = "37BBF396-11F0-4B39-85C8-B319C729AF6D" ab.

        mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
            public void onCompleted(item entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Read object with ID " + entity.id);    
                }
            }
        });

## <a name="untyped"></a><span class="short-header">Arbeiten mit nicht typisierten Daten</span>Vorgehensweise: Arbeiten mit nicht typisierten Daten

Mit dem untypisierten Programmiermodell erhalten Sie vollständige Kontrolle über die JSON-Serialisierung. Dies ist hilfreich in bestimmten Szenarien, z. B. wenn eine Tabelle in Ihrem mobilen Dienst eine große Anzahl an Spalten enthält, und Sie nur einige wenige Spalten benötigen. Im typisierten Modell müssen Sie alle Spalten der Tabelle in Ihrem mobilen Dienst in Ihrer Datenklasse definieren. Im untypisierten Modell definieren Sie dagegen nur die Spalten, die Sie verwenden möchten.

Ähnlich wie beim typisierten Modell benötigen Sie zunächst einen Tabellenverweis. In diesem Fall handelt es sich jedoch um ein [MobileServicesJsonTable][MobileServicesJsonTable]-Objekt. Sie erhalten den Verweis durch einen Aufruf der [getTable()][getTable()]-Methode auf einer Instanz des Mobile Services-Clients.

Sie verwenden die folgende Überladung dieser Methode, die speziell für untypisierte JSON-basierte Programmiermodelle existiert:

        public class MobileServiceClient {
            public MobileServiceJsonTable getTable(String name);
        }

Die meisten API-Aufrufe für den Datenzugriff sind gleich wie beim typisierten Programmiermodell. Im untypisierten Modell rufen Sie Methoden des **MobileServiceJsonTable**-Objekts anstelle des **MobileServiceTable**-Objekts auf. Die Verwendung des Rückrufobjekts und der **onCompleted**-Methode ist sehr ähnlich wie beim typisierten Modell.

### <a name="json_instance"></a>Gewusst wie: Erstellen einer Instanz einer untypisierten Tabelle

Sobald Sie eine Instanz des Mobile Services-Clients erstellt haben (in diesem Fall die *mClient*-Variable), können Sie mit dem folgenden Code eine Instanz von **MobileServiceJsonTable** erstellen.

        MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

Sobald Sie eine Instanz von **MobileServiceJsonTable** erstellt haben, können Sie beinahe alle Methoden aufrufen, die Sie auch beim typisierten Programmiermodell zur Verfügung haben. In manchen Fällen nehmen die Methoden jedoch untypisierte Parameter entgegen, wie Sie in den folgenden Beispielen sehen werden.

### <a name="json_insert"></a>Gewusst wie: Einfügen in untypisierte Tabellen

Der folgende Code zeigt, wie Sie Elemente einfügen können. Sie müssen zunächst ein [**JsonObject**][**JsonObject**] erstellen, das Teil der [gson][gson]-Bibliothek ist.

        JsonObject task = new JsonObject();
        task.addProperty("text", "Wake up");
        task.addProperty("complete", false);
        task.addProperty("duration", 5);

Anschließend fügen Sie das Objekt ein. Die an die [**insert**][3]-Methode übergebene Rückruffunktion ist eine Instanz der [**TableJsonOperationCallback**][**TableJsonOperationCallback**]-Klasse. Beachten Sie, dass der erste Parameter der *onCompleted*-Methode ein JsonObject ist.

        mTable.insert(task, new TableJsonOperationCallback() {
            public void onCompleted(JsonObject jsonObject, 
                                    Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object inserted with ID " + 
                jsonObject.getAsJsonPrimitive("id").getAsString());
                }
            }
        });

Sie erhalten die ID des eingefügten Objekts mit dem folgenden Methodenaufruf:

                jsonObject.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Gewusst wie: Löschen aus einer untypisierten Tabelle

Der folgende Code zeigt, wie Sie eine Instanz löschen, in diesem Fall die Instanz des **JsonObject**, die Sie im vorigen *insert*-Beispiel eingefügt haben. Das Rückrufobjekt **TableDeleteCallback** ist dasselbe Objekt wie im typisierten Programmiermodell, und dessen **onCompleted**-Methode hat eine andere Signatur als im **insert**-Beispiel.

        mTable.delete(task, new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

Sie können eine Instanz auch direkt anhand ihrer ID löschen:

        mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)

### <a name="json_get"></a>Gewusst wie: Zurückgeben aller Zeilen aus einer untypisierten Tabelle

Der folgende Code ruft eine gesamte Tabelle ab. Beachten Sie, dass das untypisierte Programmiermodell ein anderes Rückrufobjekt verwendet: [**TableJsonQueryCallback**][**TableJsonQueryCallback**].

        mTable.execute(new TableJsonQueryCallback() {
            public void onCompleted(JsonElement result, 
                                    int count, 
                                    Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    JsonArray results = result.getAsJsonArray();
                    for(JsonElement item : results){
                        Log.i(TAG, "Read object with ID " + 
                    item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
                    }
                }
            }
        });

Für Filterung, Sortierung und Seitenverwaltung können Sie Methoden mit den gleichen Namen wie im typisierten Programmiermodell verketten.

## <a name="binding"></a><span class="short-header">Datenbindung</span>Gewusst wie: Datenbindung in der Benutzeroberfläche

Datenbindung besteht aus drei Komponenten:

-   die Datenquelle
-   das Bildschirmlayout
-   und der Adapter, der diese beiden Komponenten verbindet.

In unserem Beispielcode geben wir die Daten aus der Tabelle *ToDoItem* im mobilen Dienst in ein Array zurück. Dies ist ein typisches Muster für Datenanwendungen: Datenbankabfragen geben normalerweise eine Sammlung von Zeilen in Form von Listen oder Arrays an den Client zurück. Das Array ist in diesem Fall die Datenquelle.

Der Code definiert ein Bildschirmlayout für die Anzeige der Daten, die auf dem Gerät erscheinen sollen.

Diese beiden Komponenten sind über einen Adapter verbunden, in diesem Fall eine Erweiterung der *ArrayAdapter\<ToDoItem\>*-Klasse.

### <a name="layout"></a>Gewusst wie: Definieren des Layouts

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
        

### <a name="adapter"></a>Gewusst wie: Definieren des Adapters

Da die Datenquelle in unserer Ansicht ein *ToDoItem* ist, leiten wir unseren Adapter von der Klasse *ArrayAdapter\<ToDoItem\>* ab. Diese Unterklasse produziert eine Ansicht für jedes *ToDoItem* und verwendet dabei das *row\_list\_to\_do*-Layout.

In unserem Code definieren wir die folgende Klasse als Erweiterung der *ArrayAdapter\<E\>*-Klasse:

        public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Sie müssen die *getView*-Methode der Klasse überschreiben. Dieser Code zeigt ein Beispiel hierfür. Die Details unterscheiden sich je nach Anwendungsfall.

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

### <a name="use-adapter"></a>Gewusst wie: Verwenden des Adapters

Sie sind nun in der Lage, die Datenbindung zu verwenden. Der folgende Code zeigt, wie Sie die Elemente aus der Tabelle im mobilen Dienst abrufen, den Adapter leeren und anschließend dessen *add*-Methode aufrufen, um ihn mit den abgerufenen Elementen zu füllen.

        mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
            public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
                if (exception == null) {
                    mAdapter.clear();
                    for (ToDoItem item : result) {
                        mAdapter.add(item);
                    }
                } 
            }
        });

Sie müssen den Adapter jedes mal aufrufen, wenn Sie die *ToDoItem*-Tabelle ändern und das Ergebnis der Änderungen anzeigen möchten. Da Veränderungen auf einzelnen Einträgen erfolgen, erhalten Sie in diesem Fall eine einzelne Zeile anstatt einer Sammlung. Beim Einfügen von Elementen rufen Sie die *add*-Methode des Adapters auf und beim Löschen von Elementen dessen *remove*-Methode.

## <a name="authentication"></a><span class="short-header">Authentifizieren</span>Vorgehensweise: Authentifizieren von Benutzern

Mobile Dienste unterstützen Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie unter [Get started with authentication][Get started with authentication] (Erste Schritte zur Authentifizierung, in englischer Sprache).

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein *Serverfluss* und ein *Clientfluss*. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

Für die Authentifizierung in Ihrer App sind drei Schritte erforderlich:

1.  Registrieren Ihrer App für die Authentifizierung mit einem Anbieter und Konfigurieren von Mobile Services
2.  Einschränken von Tabellenberechtigungen für authentifizierte Benutzer
3.  Hinzufügen des Authentifizierungscodes zu Ihrer App

Mobile Services unterstützt die folgenden existierenden Identitätsanbieter für die Authentifizierung Ihrer Benutzer:

-   Microsoft Account
-   Facebook
-   Twitter
-   Google
-   Azure Active Directory

Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die IDs authentifizierter Benutzer verwenden, um Anfragen zu verändern.

Diese beiden ersten Aufgaben können Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] erledigen. Weitere Informationen finden Sie unter [Get started with authentication][Get started with authentication] (Erste Schritte zur Authentifizierung, in englischer Sprache).

### <a name="caching"></a>Gewusst wie: Hinzufügen des Authentifizierungscodes zu Ihrer App

1.  Fügen Sie die folgenden import-Anweisungen zur Aktivitätsdatei Ihrer App hinzu.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Fügen Sie die folgende Codezeile in der **onCreate**-Methode der Aktivitätsklasse im Anschluss an den Code hinzu, durch den das `MobileServiceClient`-Objekt erstellt wird: Wir gehen davon aus, dass der Verweis auf das `MobileServiceClient`-Objekt *mClient* entspricht.

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {
                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {  
                            if (exception == null) {
                                /* User now logged in, you can get their identity via user.getUserId() */ 
                            } else {
                                /* Login error */
                            }
                        }
                    });

    Dieser Code authentifiziert einen Benutzer mit einer Google-Anmeldung. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    <div class="dev-callout"><b>Hinweis</b>
<p>Falls Sie einen anderen Identit&auml;tsanbieter als Google verwenden, &auml;ndern Sie den an die <strong>login</strong>-Methode &uuml;bergebenen Wert auf einen der folgenden Werte: <i>MicrosoftAccount</i>, <i>Facebook</i>, <i>Twitter</i> oder <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Beim Ausführen der App können Sie sich mit dem Identitätsanbieter Ihrer Wahl anmelden.

### <a name="caching"></a>Gewusst wie: Zwischenspeichern von Authentifizierungstokens

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
            // Login using the provider.
            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                    new UserAuthenticationCallback() {
                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                createTable();
                                cacheUser(mClient.getCurrentUser());
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }
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

Was geschieht also, wenn Ihr Token abläuft? In diesem Fall erhalten Sie beim Verbindungsversuch die Antwort *401 unauthorized*. Der Benutzer muss sich erneut anmelden, um ein neues Token zu erhalten. Um den Code zur Behandlung dieses Falls nicht an jeder Stelle in Ihrer App schreiben zu müssen, an der Mobile Services aufgerufen wird, können Sie Filter verwenden, mit denen Sie Aufrufe an und Antworten von Mobile Services abfangen können. Der Filtercode prüft Antworten auf 401, löst bei Bedarf den Anmeldeprozess aus und setzt anschließend die Anfrage fort, die den 401-Fehler ausgelöst hatte.

## <a name="errors"></a><span class="short-header">Fehlerbehandlung</span>Vorgehensweise: Fehlerbehandlung

Sie finden ein Beispiel für Validierung und Fehlerbehandlung [hier][hier]. Dieses Beispiel implementiert die Validierung mithilfe von Serverskripts, die im Fehlerfall Ausnahmen zurückgeben und Clientcode, der diese Ausnahmen behandelt.

Ein weiterer möglicher Ansatz ist ein *globaler* Fehler-Handler. Der gezeigte Code für den Zugriff auf Tabellen in mobilen Diensten verwendet drei verschiedene Rückrufobjekte:

-   **TableQueryCallback**/**TableQueryJsonCallback**
-   **TableOperationCallback**/**TableJsonOperationCallback**
-   **TableDeleteCallback**

All diese Objekte haben eine **OnCompleted**-Methode, die als zweiten Parameter ein **java.lang.Exception**-Objekt erwarten. Sie können diese Rückrufobjekte erweitern und Ihre eigenen **onCompleted**-Methoden schreiben, die prüfen, ob der exception-Parameter null ist. Wenn dies der Fall ist, liegt kein Fehler vor, und Sie können **super.OnCompleted()** aufrufen.

Falls das **Exception**-Objekt nicht null ist, können Sie Ihre eigene Fehlerbehandlung durchführen und detaillierte Informationen über den Fehler anzeigen. Die folgende Codezeile zeigt weitere Details einer Ausnahme an.

        String msg = exception.getCause().getMessage();

Sie können nun Ihre erweiterten Rückrufe verwenden und brauchen die Ausnahme nicht mehr prüfen, da diese für alle Instanzen des Rückrufs an einem zentralen Ort (\#2) behandelt wird.

## <a name="customizing"></a><span class="short-header">Anpassen des Clients</span>Vorgehensweise: Anpassen des Clients

### <a name="headers"></a>Gewusst wie: Anpassen der Anforderungsheader

Sie können benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen. Konfigurieren Sie dazu einen ServiceFilter, wie im folgenden Beispiel:

        client = client.withFilter(new ServiceFilter() {
        
            @Override
            public void handleRequest(ServiceFilterRequest request,
                    NextServiceFilterCallback nextServiceFilterCallback,
                    ServiceFilterResponseCallback responseCallback) {
                request.addHeader("My-Header", "Value");      
                nextServiceFilterCallback.onNext(request, responseCallback);
            }
        });

### <a name="serialization"></a>Gewusst wie: Anpassen der Serialisierung

Mobile Services nimmt standardmäßig an, dass Tabellen- und Spaltennamen sowie Datentypen auf Client und Server exakt gleich sind. Diese Namen können sich jedoch aus verschiedensten Gründen auf Server und Client unterschieden. Möglicherweise möchten Sie einen existierenden Client abändern, sodass dieser Azure Mobile Services anstelle eines Konkurrenzprodukts verwendet.

Oder Sie möchten eine der folgenden Anpassungen vornehmen:

-   
    Die Spaltennamen der Tabelle im mobilen Dienst entsprechen nicht den im Client verwendeten Namen
-   Verwenden Sie eine Tabelle in einem mobilen Dienst mit einem anderen Namen als die Klasse, die der Tabelle im Client zugeordnet ist
-   Aktivieren der automatischen Großschreibung von Eigenschaften
-   Hinzufügen von komplexen Eigenschaften zu Objekten

### <a name="columns"></a>Gewusst wie: Zuordnen unterschiedlicher Namen zwischen Client und Server

Angenommen, Ihr Java-Clientcode verwendet mit den folgenden vergleichbare Namen nach Java-Standardschema für die *ToDoItem*-Objekteigenschaften.

-   mId
-   mText
-   mComplete
-   mDuration

Sie müssen die clientseitigen Namen zu JSON-Namen serialisieren, die den Spaltennamen in der *ToDoItem*-Tabelle auf dem Server entsprechen. Der folgende Code verwendet die [gson][gson]-Bibliothek und tut eben dies.

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Gewusst wie: Zuordnen unterschiedlicher Tabellennamen zwischen Client und mobilem Dienst

Die Zuordnung von Client-Tabellennamen zu anderen Tabellennamen im mobilen Dienst ist einfach. Verwenden Sie dazu eine der Überschreibungen der
[getTable()][zweite Überladung]-Funktion, wie im folgenden Code gezeigt.

        mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Gewusst wie: Automatisieren von Tabellennamenzuordnungen

Die Zuordnung von Spaltennamen für kleine Tabellen mit nur wenigen Spalten ist kein großer Aufwand, wie zuvor gezeigt. Nehmen wir jedoch an, unserer Tabelle hat viele Spalten, z. B. 20 oder 30. In diesem Fall können wir die [gson][gson]-API aufrufen und eine Umwandlungsstrategie für alle Spalten angeben, um nicht jeden einzelnen Spaltennamen einzeln zuordnen zu müssen.

Dazu verwenden wir die [gson][gson]-Bibliothek, mit der die Android-Clientbibliothek im Hintergrund Java-Objekte nach JSON-Daten serialisiert, die anschließend an Azure Mobile Services übertragen werden.

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

### <a name="complex"></a>Gewusst wie: Speichern von Objekten oder Array-Eigenschaften in einer Tabelle

Unsere bisherigen Serialisierungsbeispiele verwenden einfache Typen wie Ganzzahlen und Zeichenfolgen, die problemlos nach JSON serialisiert und in die Tabelle im mobilen Dienst eingefügt werden können. Nehmen wir an, wir möchten ein komplexes Objekt in unserem Clienttyp hinzufügen, das nicht automatisch nach JSON serialisiert und in die Tabelle eingefügt werden kann. Wir könnten beispielsweise ein Array von Zeichenfolgen in das Client-Objekt einfügen. In diesem Fall müssen wir selbst angeben, wie das Objekt serialisiert und das Array in die Tabelle im mobilen Dienst einzufügen ist.

Ein Beispiel hierzu finden Sie im Blogeintrag [Customizing serialization using the][Customizing serialization using the][gson][gson] library in the Mobile Services Android client</a> (Benutzerdefinierte Serialisierung mit der gson-Bibliothek im Android-Client für mobile Dienste, in englischer Sprache).

Sie können diese allgemeine Methode immer dann verwenden, wenn Sie mit komplexen Objekten arbeiten, die nicht automatisch nach JSON serialisiert und in Tabellen in mobilen Diensten eingefügt werden können.

## <a name="next-steps"></a>Nächste Schritte

Sie finden die Javadocs-Referenz für die Android Client-API [][]<http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html></a>



  [Schnellstart für mobile Dienste]: /de-de/develop/mobile/tutorials/get-started-android/
  [Mobile Services-SDK]: http://go.microsoft.com/fwlink/p/?linkid=280126
  [Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409
  [Windows Azure Mobile Services]: #what-is
  [Konzepte]: #concepts
  [Einrichtung und Voraussetzungen]: #setup
  [Gewusst wie: Erstellen des Mobile Services-Clients]: #create-client
  [Gewusst wie: Erstellen eines Tabellenverweises]: #instantiating
  [Die API-Struktur]: #api
  [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
  [Zurückgegebene Daten filtern]: #filtering
  [Zurückgegebene Daten sortieren]: #sorting
  [Daten seitenweise zurückgeben]: #paging
  [Bestimmte Spalten auswählen]: #selecting
  [Gewusst wie: Verketten von Abfragemethoden]: #chaining
  [Gewusst wie: Einfügen von Daten in einen mobilen Dienst]: #inserting
  [Gewusst wie: Aktualisieren von Daten in einem mobilen Dienst]: #updating
  [Gewusst wie: Löschen von Daten in einem mobilen Dienst]: #deleting
  [Gewusst wie: Abfragen bestimmter Elemente]: #lookup
  [Gewusst wie: Arbeiten mit nicht typisierten Daten]: #untyped
  [Gewusst wie: Datenbindung in der Benutzeroberfläche]: #binding
  [Gewusst wie: Definieren des Layouts]: #layout
  [Gewusst wie: Definieren des Adapters]: #adapter
  [Gewusst wie: Verwenden des Adapters]: #use-adapter
  [Gewusst wie: Authentifizieren von Benutzern]: #authentication
  [Zwischenspeichern von Authentifizierungstokens]: #caching
  [Gewusst wie: Fehlerbehandlung]: #errors
  [Gewusst wie: Anpassen des Clients]: #customizing
  [Anpassen der Anforderungsheader]: #headers
  [Anpassen der Serialisierung]: #serialization
  [Nächste Schritte]: #next-steps
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Erstellen einer Tabelle]: http://go.microsoft.com/fwlink/p/?LinkId=298592
  [Dynamisches Schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [MobileServiceClient]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
  [gson]: %20http://go.microsoft.com/fwlink/p/?LinkId=290801
  [MobileServiceTable]: http://go.microsoft.com/fwlink/p/?LinkId=296835
  [erste Überladung]: http://go.microsoft.com/fwlink/p/?LinkId=296839
  [zweite Überladung]: http://go.microsoft.com/fwlink/p/?LinkId=296840
  [**eq**]: http://go.microsoft.com/fwlink/p/?LinkId=298461
  [**ne**]: http://go.microsoft.com/fwlink/p/?LinkId=298462
  [**gt**]: http://go.microsoft.com/fwlink/p/?LinkId=298463
  [**ge**]: http://go.microsoft.com/fwlink/p/?LinkId=298464
  [**lt**]: http://go.microsoft.com/fwlink/p/?LinkId=298465
  [**le**]: http://go.microsoft.com/fwlink/p/?LinkId=298466
  [Exploring the richness of the Mobile Services Android client query model]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=296296
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=296313
  [ASCII-Steuerzeichen C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [CLI to manage Mobile Services tables]: http://www.windowsazure.com/de-de/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [MobileServicesJsonTable]: http://go.microsoft.com/fwlink/p/?LinkId=298733
  [3]: http://go.microsoft.com/fwlink/p/?LinkId=298535
  [Get started with authentication]: http://go.microsoft.com/fwlink/p/?LinkId=296316
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [hier]: https://www.windowsazure.com/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [Customizing serialization using the]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
  []: http://go.microsoft.com/fwlink/p/?LinkId=298735 "hier"

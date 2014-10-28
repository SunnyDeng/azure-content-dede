<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Aufrufen von gespeicherten SQL-Prozeduren mit einem JavaScript-Back-End

Gründe, warum Sie gespeicherte SQL-Server-Prozeduren aufrufen würden:

-   Ihr mobiler Dienst verwendet vorhandene gespeicherte Prozeduren, die Sie aufrufen möchten
-   Ihre Anwendungsarchitektur erfordert die Verwendung gespeicherter Prozeduren für den Datenbankzugriff
-   persönlichen Vorlieben

In diesem Thema lernen Sie Folgendes:

-   [Aufrufen einer einfachen gespeicherten Prozedur][Aufrufen einer einfachen gespeicherten Prozedur]
-   [Aufrufen einer gespeicherten Prozedur mit Parametern][Aufrufen einer gespeicherten Prozedur mit Parametern]
-   [Weiterführende Informationen][Weiterführende Informationen]

In diesem Artikel werden gespeicherte Prozeduren in einem mobilen Dienst mit einem JavaScript-Back-End erläutert.

Sie können stattdessen mobile Dienste auch mit einem .Net-Back-End erstellen, was eine komplett andere Architektur basierend auf dem Entity Framework erfordert. Weitere Informationen dazu finden Sie unter [Funktionsweise des Azure Mobile Services .Net-Back-Ends][Funktionsweise des Azure Mobile Services .Net-Back-Ends] (How the Azure Mobile Services .Net Backend works, in englischer Sprache).

## Aufrufen einer einfachen gespeicherten Prozedur

**„Meine Datenbank verwendet gespeicherte Prozeduren: Wie rufe ich diese von einem mobilen Dienst aus auf?“**

Verwenden Sie dazu das [mssql-Objekt][mssql-Objekt], mit dem Sie *Transact-SQL* („T-SQL“)-Anweisungen ausführen können, welche die Prozedur aufrufen.

### Schreiben und Testen des Transact SQL-Codes

Angenommen, die gespeicherte Prozedur trägt den Namen *GetAll*, und Ihr mobiler Dienst heißt **todolist**.

1.  Wählen Sie das Symbol für **Datenbanken** in der Seitenleiste des Azure-Portals aus und dann die Datenbank, deren Name dem Namen des mobilen Diensts mit dem Suffix „\*\*\_db\*\*“ entspricht.

2.  Klicken Sie auf der Schnellstartseite für die Datenbank auf den Link **Verwalten** unten auf der Seite.

3.  Melden Sie sich bei der Datenbank an.

4.  Klicken Sie in der oberen Leiste auf **Neue Abfrage**, und fügen Sie folgenden Code hinzu:

            EXEC todolist.GetAll

5.  Klicken Sie auf **Ausführen**, und überprüfen Sie die Ergebnisse.

Sie sehen, dass der Prozedurname den Namen des Datenbankschemas als Präfix erhält (standardmäßig der mobile Dienstname).

### Wo starte ich den Aufruf für das mssql-Objekt?

Wenn Sie die gespeicherte Prozedur direkt aufrufen möchten, besteht die flexibelste Möglichkeit darin, eine [benutzerdefinierte API][benutzerdefinierte API] zu schreiben und aufzurufen.

1.  Klicken Sie im Dashboard des mobilen Diensts auf **API**, dann auf **CREATE**, nennen Sie Ihr Skript ***getall***, und fügen Sie diesen Code hinzu, um die gespeicherte Prozedur aufzurufen:

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  Testen Sie dies im Browser, indem Sie folgende URL in die Adresszeile einfügen:

        https://todolist.azure-mobile.net/api/getall

Sie können stattdessen ähnlichen Code in ein Standard-Serverskript für *read*, *insert*, *update* oder *delete* einer Tabelle einfügen, sodass der Code das Standardverhalten umgeht und die gespeicherte Prozedur aufruft. Ein ähnlicher Code ist auch für ein *Scheduler*-Skript geeignet. Das Erstellen einer benutzerdefinierten API ist jedoch die flexibelste Möglichkeit.

### Aufrufen des Codes vom Client

Passen Sie den Clientcode zum Aufruf der **invokeApi**-Methode für das **MobileServiceClient**-Objekt an. Die genaue Codesyntax ist je nach Clientgerät unterschiedlich und wird in den folgenden Themen erläutert:

-   [Windows Store C#][Windows Store C#]
-   [Windows Store JavaScript][Windows Store JavaScript]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>Aufrufen einer gespeicherten Prozedur mit Parametern

Angenommen, die gespeicherte Prozedur trägt den Namen *ItemsByStatus*, verfügt über einen Parameter namens *Status* und soll über eine benutzerdefinierte API aufgerufen werden.

1.  Befolgen Sie die Schritte im vorhergehenden Abschnitt, um den T-SQL-Code mithilfe des Azure-Datenbankportals zu schreiben und zu testen, verwenden Sie aber stattdessen diesen T-SQL-Code:

        EXEc todolist.ItemsByStatus @Status = 1

2.  Klicken Sie auf das Symbol **Ausführen**, und überprüfen Sie die Ergebnisse.

3.  Erstellen Sie wie im vorhergehenden Schritt eine neue benutzerdefinierte API namens **completeall** mit dem folgenden Code, der die gespeicherte Prozedur aufruft. Beachten Sie, dass Sie im unten angegebenen Code den eigentlichen Wert des Parameters [\*\*@Status\*\*][\*\*@Status\*\*], den Sie beim Testen verwendet haben, durch **"?"** ersetzen, der zur Laufzeit mit dem bereitgestellten Parameter ausgefüllt wird.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  Testen Sie die API mit folgender URL in einem Browser:

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  Rufen Sie die API vom Client wie im vorhergehenden Abschnitt beschrieben auf.

### Weitere komplexe Parametersignaturen

Die gespeicherte Prozedur im vorhergehenden Abschnitt enthielt nur einen einzigen Parameter. Dies ist die Syntax für eine längere Signatur:

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

Beachten Sie, dass in der Anweisung **EXEC** die Parameter nach Namen statt nach Position aufgerufen werden. Dies ist die empfohlene Syntax und isoliert Ihren Code von kleineren Änderungen in der gespeicherten Prozedur, wie beispielsweise das Ändern der Parameterreihenfolge oder das Hinzufügen optionaler Parameter.

## <a name="more"></a>Weiterführende Informationen

Dieses Thema beschreibt nur einen Bruchteil der Möglichkeiten.

Ausführliche Beschreibungen des verwendeten Codes finden Sie in folgenden Themen:

-   -   

Dies sind weitere mögliche Szenarien:

-   Unter [Wie führe ich mehr als 1 Lesevorgang durch Skripting aus][Wie führe ich mehr als 1 Lesevorgang durch Skripting aus] wird beschrieben, wie Sie bedingungsabhängig eine Tabelle auslesen oder alternativ eine gespeicherte Prozedur aufrufen. Der Blogger, Carlos Figueira, veröffentlicht häufig Hinweise zu Azure Mobile Services und Datenbanken.

-   [Zugriff auf eine gespeicherte Prozedur von einem anderen Schema][Zugriff auf eine gespeicherte Prozedur von einem anderen Schema] beschreibt Lösungen zu den Problemen, die auftreten können, wenn Sie gespeicherte Prozeduren aufrufen, die sich in einem abweichenden Schema derselben Mobile Services-Datenbank befinden.

Sie können auch das Azure-Portal zum Verwalten und Erstellen von neuen gespeicherten Prozeduren verwenden.

<!-- Anchors. --> <!-- URLs. -->

  [Aufrufen einer einfachen gespeicherten Prozedur]: #simple
  [Aufrufen einer gespeicherten Prozedur mit Parametern]: #parameters
  [Weiterführende Informationen]: #more
  [Funktionsweise des Azure Mobile Services .Net-Back-Ends]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [mssql-Objekt]: http://msdn.microsoft.com/de-de/library/windowsazure/jj554212.aspx
  [benutzerdefinierte API]: http://msdn.microsoft.com/de-de/library/windowsazure/dn280974.aspx
  [Windows Store C#]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Windows Store JavaScript]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-html-call-custom-api/
  [\*\*@Status\*\*]: mailto:**@Status*
  [Wie führe ich mehr als 1 Lesevorgang durch Skripting aus]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [Zugriff auf eine gespeicherte Prozedur von einem anderen Schema]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx

<properties
	pageTitle="Tutorial: Web-App mit mehrinstanzfähiger Datenbank und Verwendung von Entity Framework und Sicherheit auf Zeilenebene"
	description="Erfahren Sie, wie Sie eine ASP.NET MVC 5-Web-App mit einem mehrinstanzenfähigen SQL-Datenbank-Back-End und Verwendung von Entity Framework und Sicherheit auf Zeilenebene entwickeln."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
	services="app-service\web"
	documentationCenter=".net"
	manager="jeffreyg"
  authors="tmullaney"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="thmullan"/>

# Tutorial: Web-App mit mehrinstanzfähiger Datenbank und Verwendung von Entity Framework und Sicherheit auf Zeilenebene

In diesem Tutorial wird veranschaulicht, wie Sie eine mehrinstanzenfähige Web-App mit einem Mandantenmodell vom Typ „[Freigegebene Datenbank, Freigegebenes Schema](https://msdn.microsoft.com/library/aa479086.aspx)“ und Verwendung von Entity Framework und [Sicherheit auf Zeilenebene](https://msdn.microsoft.com/library/dn765131.aspx) erstellen. In diesem Modell enthält eine Einzeldatenbank Daten für viele Mandanten, und jeder Zeile einer Tabelle ist eine „Mandanten-ID“ zugeordnet. Die Sicherheit auf Zeilenebene (Row-Level Security, RLS), ein neues Features von Azure SQL-Datenbank, wird verwendet, um für Mandaten den gegenseitigen Zugriff auf die Daten zu verhindern. Dies erfordert nur eine einzelne, kleine Änderung an der Anwendung. Indem die Mandantenzugriffslogik direkt in der Datenbank zentralisiert wird, vereinfacht RLS den Anwendungscode und verringert das Risiko versehentlicher Datenlecks zwischen Mandanten.

Wir beginnen mit der einfachen Contact Manager-Anwendung aus [Erstellen einer ASP.NET-MVP-App mit „auth“ und SQL DB und Bereitstellung für Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Bisher lässt die Anwendung für alle Benutzer (Mandanten) das Anzeigen aller Kontakte zu:

![Contact Manager-Anwendung vor Aktivierung von RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Mit wenigen Änderungen fügen wir die Unterstützung für die Mehrmandantenfähigkeit hinzu, damit Benutzer nur die Kontakte sehen können, die zu ihnen gehören.

## Schritt 1: Hinzufügen einer Interceptor-Klasse in der Anwendung zum Festlegen von SESSION\_CONTEXT

Wir müssen eine Anwendungsänderung vornehmen. Da alle Anwendungsbenutzer die Verbindung mit der Datenbank mit der gleichen Verbindungszeichenfolge herstellen (also der gleichen SQL-Anmeldung), kann eine RLS-Richtlinie derzeit nicht ermitteln, nach welchem Benutzer gefiltert werden soll. Dieser Ansatz wird häufig in Webanwendungen verwendet, weil er ein effizientes Verbindungspooling ermöglicht. Aber dies bedeutet, dass wir eine andere Möglichkeit benötigen, um in der Datenbank den aktuellen Anwendungsbenutzer zu identifizieren. Die Lösung besteht darin, dass von der Anwendung ein Schlüssel-Wert-Paar für die aktuelle UserId in [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806) festgelegt wird, bevor eine Abfrage ausgeführt wird. SESSION\_CONTEXT ist ein Schlüsselwertspeicher für den Sitzungsbereich, und unsere RLS-Richtlinie verwendet die darin gespeicherte UserId, um den aktuellen Benutzer zu identifizieren. *Hinweis: SESSION\_CONTEXT ist derzeit ein Vorschaufeature von Azure SQL-Datenbank.*

Wir fügen einen [Interceptor](https://msdn.microsoft.com/data/dn469464.aspx) hinzu. Dies ist ein neues Feature in Entity Framework (EF) 6 zum automatischen Festlegen der aktuellen UserId in SESSION\_CONTEXT, indem jeweils eine T-SQL-Anweisung vorangestellt wird, bevor EF eine Abfrage ausführt.

1.	Öffnen Sie das ContactManager-Projekt in Visual Studio.
2.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner „Models“, und wählen Sie „Hinzufügen“ > „Klasse“.
3.	Geben Sie der neuen Klasse den Namen „SessionContextInterceptor.cs“, und klicken Sie auf „Hinzufügen“.
4.	Ersetzen Sie den Inhalt von „SessionContextInterceptor.cs“ durch den folgenden Code:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.SqlClient;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbCommandInterceptor
    {
        private void SetSessionContext(DbCommand command)
        {
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    // Set SESSION_CONTEXT to current UserId before executing queries
                    var sql = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId;";

                    command.CommandText = sql + command.CommandText;
                    command.Parameters.Insert(0, new SqlParameter("@UserId", userId));
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        public void NonQueryExecuting(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void NonQueryExecuted(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {

        }
        public void ReaderExecuting(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ReaderExecuted(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {

        }
        public void ScalarExecuting(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ScalarExecuted(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {

        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Dies ist die einzige Anwendungsänderung, die erforderlich ist. Nun können Sie die Anwendung erstellen und veröffentlichen.

## Schritt 2: Hinzufügen einer UserId-Spalte zum Datenbankschema

Als Nächstes müssen wir der Tabelle „Contacts“ die Spalte „UserId“ hinzufügen, um jeder Zeile einen Benutzer (Mandanten) zuzuordnen. Wir ändern das Schema direkt in der Datenbank, damit wir dieses Feld nicht in unser EF-Datenmodell einbinden müssen.

Stellen Sie die Verbindung mit der Datenbank direkt her, indem Sie entweder SQL Server Management Studio oder Visual Studio verwenden, und führen Sie dann den folgenden T-SQL-Code aus:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Der Tabelle „Contacts“ wird die Spalte „UserId“ hinzugefügt. Wir verwenden den Datentyp „nvarchar(128)“, um den Abgleich mit den in der Tabelle „AspNetUsers“ gespeicherten UserIds durchzuführen. Wir erstellen auch eine DEFAULT-Einschränkung, mit der die UserId für neu eingefügte Zeilen auf die UserId festgelegt wird, die derzeit in SESSION\_CONTEXT gespeichert ist.

Die Tabelle sieht nun folgendermaßen aus:

![SSMS-Contacts-Tabelle](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Wenn neue Kontakte erstellt werden, werden sie automatische der richtigen UserId zugewiesen. Zu Demonstrationszwecken weisen wir einige dieser vorhandenen Kontakte einem vorhandenen Benutzer zu.

Wenn Sie in der Anwendung bereits einige Benutzer erstellt haben (z. B. über lokale, Google- oder Facebook-Konten), werden diese in der Tabelle „AspNetUsers“ aufgeführt. Im folgenden Screenshot ist bisher nur ein Benutzer zu sehen.

![SSMS-AspNetUsers-Tabelle](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Kopieren Sie die „Id“ für user1@contoso.com, und fügen Sie sie unten in die T-SQL-Anweisung ein. Führen Sie diese Anweisung aus, um für drei Kontakte diese UserId zuzuordnen.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## Schritt 3: Erstellen einer Richtlinie für die Sicherheit auf Zeilenebene in der Datenbank

Der letzte Schritt ist das Erstellen einer Sicherheitsrichtlinie, für die die UserId in SESSION\_CONTEXT genutzt wird, um die von den Abfragen zurückgegebenen Ergebnisse automatisch zu filtern.

Führen Sie den folgenden T-SQL-Code aus, während die Verbindung mit der Datenbank besteht:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
	RETURNS TABLE
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS accessResult
	WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
	ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
	ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Dieser Code bewirkt drei Dinge. Erstens wird ein neues Schema als bewährte Methode zum Zentralisieren und Beschränken des Zugriffs auf die RLS-Objekte erstellt. Zweitens wird eine Prädikatfunktion erstellt, die „1“ zurückgibt, wenn die UserId einer Zeile mit der UserId in SESSION\_CONTEXT übereinstimmt. Drittens wird eine Sicherheitsrichtlinie erstellt, mit der diese Funktion sowohl als Filter als auch als Blockprädikat der Tabelle „Contacts“ hinzugefügt wird. Mit dem Filterprädikat wird bewirkt, dass Abfragen nur Zeilen zurückgeben, die zum aktuellen Benutzer gehören. Das Blockprädikat dient als Schutz, um zu verhindern, dass die Anwendung versehentlich eine Zeile für den falschen Benutzer einfügt. *Hinweis: Blockprädikate sind derzeit ein Vorschaufeature von Azure SQL-Datenbank.*

Führen Sie nun die Anwendung aus, und melden Sie sich als user1@contoso.com an. Diesem Benutzer werden jetzt nur die Kontakte angezeigt, die wir dieser UserId zugewiesen haben:

![Contact Manager-Anwendung vor Aktivierung von RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Versuchen Sie, einen neuen Benutzer zu registrieren, um dies zu überprüfen. Für den Benutzer werden keine Kontakte angezeigt, weil ihnen keine Benutzer zugewiesen wurden. Wenn diese Benutzer einen neuen Kontakt erstellen, ist er nur ihnen zugewiesen und nur für sie allein sichtbar.

## Nächste Schritte

Das ist alles! Die einfache Contact Manager-Web-App wurde in eine mehrinstanzenfähige Web-App umgewandelt, in der jeder Benutzer über eine eigene Kontaktliste verfügt. Durch den Einsatz der Sicherheit auf Zeilenebene haben wir die komplexe Aufgabe vermieden, die Mandantenzugriffslogik in unserem Anwendungscode durchzusetzen. Dank dieser Transparenz ist für die Anwendung die Konzentration auf das eigentliche Geschäftsproblem möglich, und außerdem wird das Risiko von versehentlichen Datenlecks verringert, wenn die Codebasis der Anwendung anwächst.

In diesem Tutorial haben wir in Bezug auf die Möglichkeiten von RLS nur an der Oberfläche gekratzt. Beispielsweise ist es möglich, eine anspruchsvollere oder feiner abgestimmte Zugriffslogik zu verwenden und mehr als nur die aktuelle UserId in SESSION\_CONTEXT zu speichern. Sie können [RLS auch in Clientbibliotheken mit Tools für elastische Datenbanken integrieren](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md), um mehrinstanzenfähige Shards auf einer Datenebene mit horizontaler Hochskalierung zu unterstützen.

Außerdem arbeiten wir daran, RLS auch über diese Möglichkeiten hinaus noch besser zu machen. Bitte teilen Sie uns im Kommentarbereich mit, wenn Sie Fragen oder Ideen haben oder Funktionen vorschlagen möchten. Wir schätzen Ihr Feedback!

<!---HONumber=Nov15_HO2-->
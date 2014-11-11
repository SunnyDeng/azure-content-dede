<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Lernprogramm zu Azure-Cloud-Diensten: ASP.NET MVC-Webrolle, Workerrolle und Azure-Speichertabellen, Warteschlangen und Blobs – 1 von 5

Dieses Lernprogramm behandelt die Erstellung und Bereitstellung von ASP.NET MVC-Webanwendungen mit mehreren Ebenen, die in einem Azure-Cloud-Dienst ausgeführt werden und Azure-Speichertabellen, Warteschlangen und Blobs verwenden. Sie können [die fertige Anwendung][die fertige Anwendung] aus der MSDN Code Gallery herunterladen. Ein [E-Book][E-Book] einer früheren Version finden Sie in der TechNet E-Book-Galerie.

Das folgende Diagramm zeigt die Interaktion zwischen den verschiedenen Anwendungskomponenten:

![Verarbeitung der E-Mails][Verarbeitung der E-Mails]

Dies ist eine Lernprogrammserie mit fünf Teilen. Eine schnellere, einfachere Einführung in Cloud-Dienste, Warteschlangen und Blobs finden Sie unter [Erste Schritte mit Azure-Cloud-Diensten und ASP.NET][Erste Schritte mit Azure-Cloud-Diensten und ASP.NET]. Alternativ können Sie Anwendungen mit mehreren Ebenen in Websites und WebJobs ausführen. Weitere Informationen dazu finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK][Erste Schritte mit dem Azure WebJobs SDK].

In dieser Lernprogrammserie lernen Sie Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
-   Erstellen eines Cloudprojekts in Visual Studio mit einer ASP.NET MVC-Webrolle und zwei Workerrollen.
-   Veröffentlichen des Cloudprojekts in einem Azure Cloud-Dienst.
-   Verwenden des Dienstes für Warteschlangenspeicherung in Azure für die Kommunikation zwischen Ebenen oder zwischen Workerrollen.
-   Verwenden des Tabellenspeicherdienstes in Azure als hochskalierbarer Datenspeicher für strukturierte, nichtrelationale Daten.
-   Verwenden des Blob-Dienstes in Azure zum Speichern von Dateien in der Cloud.
-   Anzeigen und bearbeiten von Tabellen, Warteschlangen und Blobs in Azure mit dem Visual Studio-Server-Explorer.
-   Versenden von E-Mails mit SendGrid.
-   Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten.
-   Skalieren einer Anwendung durch Einsatz zusätzlicher Workerrolleninstanzen.

## <a name="toc"></a>Lernprogramme in dieser Serie

Diese Serie setzt sich aus fünf Lernprogrammen zusammen:

1.  **Einführung in die Azure-E-Mail-Dienstanwendung** (dieses Lernprogramm). Detaillierte Informationen zur Anwendung und deren Architektur. Sie können diesen Teil überspringen, wenn Sie nur erfahren möchten, wie die Bereitstellung funktioniert, oder nur den Code sehen möchten. Später können Sie dann hierher zurückkehren, um mehr über die Architektur zu erfahren.
2.  [Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung][Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung]. Herunterladen der Beispielanwendung mit anschließender Konfiguration, lokalen Tests sowie Bereitstellung und Test in der Cloud.
3.  [Erstellen der Webrolle für die Azure-E-Mail-Dienstanwendung][Erstellen der Webrolle für die Azure-E-Mail-Dienstanwendung]. Erstellen der MVC-Komponenten für die Anwendung und lokale Tests der Komponenten.
4.  [Erstellen der Workerrolle A (E-Mail-Planer) für die Azure-E-Mail-Dienstanwendung][Erstellen der Workerrolle A (E-Mail-Planer) für die Azure-E-Mail-Dienstanwendung]. Erstellen der Back-End-Komponente, die Warteschlangen-Arbeitsaufgaben für den E-Mail-Versand generiert und lokale Tests der Komponente.
5.  [Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung][Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung]. Erstellen der Back-End-Komponente, die Warteschlangen-Arbeitsaufgaben für den E-Mail-Versand verarbeitet und lokale Tests der Komponente.

## Abschnitte dieses Lernprogramms

-   [Voraussetzungen][Voraussetzungen]
-   [Übersicht Front-End][Übersicht Front-End]
-   [Übersicht Back-End][Übersicht Back-End]
-   [Azure-Tabellen][Azure-Tabellen]
-   [Azure-Warteschlangen][Azure-Warteschlangen]
-   [Datendiagramm][Datendiagramm]
-   [Azure-Blobs][Azure-Blobs]
-   [Azure-Cloud-Dienst und Azure-Website im Vergleich][Azure-Cloud-Dienst und Azure-Website im Vergleich]
-   [Kosten][Kosten]
-   [Authentifizierung und Autorisierung][Authentifizierung und Autorisierung]
-   [Nächste Schritte][Nächste Schritte]

## Voraussetzungen

Die Anweisungen im Lernprogramm funktionieren mit den folgenden Produkten:

-   Visual Studio 2013 mit Update 2
-   Visual Studio 2013 Express für das Web mit Update 2

Sie benötigen auch ein Azure-Abonnement. Sie können ein [kostenloses Testkonto][kostenloses Testkonto] erstellen oder [Ihre MSDN-Abonnementenvorteile aktivieren][Ihre MSDN-Abonnementenvorteile aktivieren].

## <a name="frontend"></a>Übersicht Front-End

Bei der Anwendung handelt es sich um einen E-Mail-Listendienst. Das Front-End beinhaltet Webseiten, mit denen Administratoren des Dienstes E-Mail-Listen verwalten können.

(Die Screenshots zeigen den Vorlagenstil aus Visual Studio 2012. Der Inhalt ist in Visual Studio 2013 derselbe, aber es wird ein anderer Stil verwendet.)

![Indexseite Adressenliste][Indexseite Adressenliste]

![Indexseite Empfänger][Indexseite Empfänger]

Auf weiteren Seiten können Administratoren Nachrichten für den Versand an E-Mail-Listen erstellen.

![Indexseite Nachrichten][Indexseite Nachrichten]

![Seite Nachricht erstellen][Seite Nachricht erstellen]

Dieser Dienst richtet sich an Unternehmen, deren Kunden sich auf der Website in eine Adressenliste eintragen können. Ein Administrator kann z. B. eine Liste für die Ankündigungen der Fakultät für Geschichte an der Contoso University einrichten. Wenn sich ein Student für die Ankündigungen der Fakultät für Geschichte interessiert und auf einen Link auf der Website der Contoso University klickt, dann ruft die Contoso University einen Webdienst des Azure-E-Mail-Dienstes auf. Die Dienstmethode verschickt eine E-Mail an den Kunden. Diese E-Mail enthält einen Link zu einer Seite, die den Kunden in der Empfängerliste der Ankündigungen der Fakultät für Geschichte willkommen heißt.

![Bestätigungs-E-Mail][Bestätigungs-E-Mail]

![Seite "Willkommen in der Liste"][Seite "Willkommen in der Liste"]

Jede verschickte E-Mail (mit Ausnahme der Abonnementbestätigung) enthält einen Link zur Kündigung des Abonnements. Wenn der Empfänger auf diesen Link klickt, öffnet sich eine Seite, auf der er die Kündigung bestätigen kann.

![Seite "Abonnement kündigen"][Seite "Abonnement kündigen"]

Wenn der Empfänger auf die Schaltfläche **Bestätigen** klickt, öffnet sich eine Seite, auf der die Kündigung des Abonnements bestätigt wird.

![Abbestellung bestätigt-Seite][Abbestellung bestätigt-Seite]

## <a name="backend"></a>Übersicht Back-End

Das Front-End speichert E-Mail-Listen und zu verschickende Nachrichten in Azure-Tabellen. Wenn ein Administrator den Versand einer Nachricht plant, wird eine Tabellenzeile mit dem geplanten Datum und anderen Daten wie z. B. der Betreffzeile in die Tabelle `message` eingetragen. Eine Workerrolle prüft in regelmäßigen Abständen die Tabelle `message` auf Nachrichten, die noch nicht verschickt wurden (Workerrolle A).

Wenn Workerrolle A eine noch nicht verschickte Nachricht findet, führt sie die folgenden Aufgaben aus:

-   Abrufen aller E-Mail-Adressen in der Empfängerliste für die E-Mail.
-   Einfügen der Informationen für den Versand der einzelnen E-Mails in die Tabelle `message`.
-   Erstellung einer Warteschlangen-Arbeitsaufgabe für jede zu verschickende E-Mail.

Eine zweite Workerrolle (Workerrolle B) prüft die Warteschlange auf Arbeitsaufgaben. Wenn Workerrolle B eine Arbeitsaufgabe findet, wird diese Aufgabe verarbeitet, die E-Mail verschickt und die Arbeitsaufgabe anschließend aus der Warteschlange gelöscht. Das folgende Diagramm zeigt diese Beziehungen.

![Verarbeitung der E-Mails][Verarbeitung der E-Mails]

Wenn Workerrolle B ausfällt und ersetzt werden muss, gehen keine E-Mails verloren, weil die Warteschlangen-Arbeitsaufgabe erst nach dem Versand der E-Mail gelöscht wird. Die Anwendung verhindert auch den mehrfachen Versand von E-Mails, falls Workerrolle A ausfällt und neu gestartet werden muss.

![Vermeidung doppelt verschickter E-Mails][Vermeidung doppelt verschickter E-Mails]

Workerrolle B prüft eine Abonnement-Warteschlange auf neue Arbeitsaufgaben von der Web-API-Dienstmethode für neue Abonnements. Wenn die Workerrolle eine Arbeitsaufgabe findet, verschickt sie die Bestätigungs-E-Mail.

![Verarbeitung der Abonnement-Warteschlange][Verarbeitung der Abonnement-Warteschlange]

## <a name="tables"></a>Azure-Tabellen

Die Azure-E-Mail-Dienstanwendung speichert Daten in Azure-Speichertabellen. Azure-Tabellen sind eine Form von NoSQL-Datenspeicher im Gegensatz zu relationalen Datenbanken wie der [Azure SQL-Datenbank][Azure SQL-Datenbank]. Azure-Tabellen sind eine gute Wahl, wenn Effizienz und Skalierbarkeit wichtiger sind als Datennormalisierung und relationale Integrität. In dieser Anwendung erstellt eine Workerrolle eine Zeile pro erstellter Arbeitsaufgabe, und eine andere Workerrolle liest und aktualisiert eine Zeile für jede verschickte E-Mail. Dies könnte in einer relationalen Datenbank zu einem Leistungsengpass führen. Außerdem sind Azure-Tabellen günstiger als Azure-SQL. Weitere Informationen zu Azure-Tabellen finden Sie im [letzten Lernprogramm in dieser Serie][Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung].

Der folgende Abschnitt beschreibt den Inhalt der Azure-Tabellen, die von der Azure-E-Mail-Dienstanwendung verwendet werden. Das [Datendiagramm der Azure-E-Mail-Dienstanwendung][Datendiagramm] im weiteren Verlauf dieser Seite bildet die Tabellen und ihre Beziehungen ab.

### Tabelle mailinglist

Die Tabelle `mailinglist` enthält Informationen über Mailinglisten und deren Abonnenten. (Eine Namenskonvention für Azure-Tabellen empfiehlt den Gebrauch von Kleinbuchstaben.)

Unterschiedliche Zeilen in Azure-Tabellen können unterschiedliche Schemas haben. Diese Flexibilität wird normalerweise genutzt, um Daten in einer Tabelle zu speichern, für die in einer relationalen Datenbank mehrere Tabellen notwendig wären. Zum Speichern der Mailinglisten-Daten könnten Sie z. B. in einer SQL-Datenbank drei Tabellen verwenden: eine Tabelle `mailinglist` mit Informationen zu der Liste, eine Tabelle `subscriber` mit Informationen zu Abonnenten und eine Tabelle `mailinglistsubscriber`, die Mailinglisten mit Abonnenten verknüpft und umgekehrt. In der NoSQL-Tabelle der aktuellen Anwendung sind all diese Funktionen in einer Tabelle namens `mailinglist` enthalten.

Jede Zeile in einer Azure-Tabelle hat einen *Partitionsschlüssel* und einen *Zeilenschlüssel*, mit denen sich die Zeile eindeutig identifizieren lässt. Der Partitionsschlüssel unterteilt die Tabelle in logische Partitionen. Innerhalb einer Partition identifiziert der Zeilenschlüssel jede Zeile eindeutig. Da diese Art von Tabellen keine sekundären Indizes enthält, müssen Sie die Skalierbarkeit der Anwendung sicherstellen. Achten Sie beim Design Ihrer Tabellen darauf, dass Sie in Ihren Abfragen immer den Partitionsschlüssel und den Zeilenschlüssel angeben können.

Der Partitionsschlüssel für die Tabelle `mailinglist` ist der Name der Mailingliste.

Für den Zeilenschlüssel der Tabelle `mailinglist` gibt es zwei Möglichkeiten: Entweder die Konstante "mailinglist" oder die E-Mail-Adresse des Abonnenten. Zeilen mit dem Zeilenschlüssel "mailinglist" enthalten Informationen über die Mailingliste. Zeilen mit einer E-Mail-Adresse als Zeilenschlüssel enthalten Informationen über die Abonnenten der Liste.

Mit anderen Worten: Zeilen mit dem Zeilenschlüssel "mailinglist" entsprechen einer Tabelle `mailinglist` in einer relationalen Datenbank. Zeilen mit Zeilenschlüssel = E-Mail-Adresse entsprechen einer Tabelle `subscriber` und einer Zuordnungstabelle `mailinglistsubscriber` in einer relationalen Datenbank.

Die Nutzung einer einzigen Tabelle für verschiedene Zwecke auf diese Art verbessert die Leistung. In einer relationalen Datenbank müssten drei Tabellen gelesen und drei Sätze von Zeilen sortiert und miteinander verglichen werden, was zeitaufwändig sein kann. In diesem Fall wird nur eine Tabelle gelesen, und die Zeilen werden automatisch nach Partitionsschlüssel und Zeilenschlüssel sortiert zurückgegeben.

Die folgende Tabelle zeigt die Zeileneigenschaften für diejenigen Zeilen, die Informationen zur Mailingliste enthalten (Zeilenschlüssel = "mailinglist").

| Eigenschaft         | Datentyp | Beschreibung                                                                                                                                                                                                                                                              |
|---------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partitionsschlüssel | String   | ListName: Ein eindeutiger Bezeichner für die Mailingliste, z. B. contoso1. Ein häufiger Anwendungszweck für die Tabelle ist das Abrufen aller Informationen für eine bestimmte Mailingliste. Daher ist der Listenname ein guter Schlüssel zum Partitionieren der Tabelle. |
| Zeilenschlüssel     | String   | Die Konstante "mailinglist".                                                                                                                                                                                                                                              |
| Beschreibung        | String   | Beschreibung der Mailingliste, z. B.: "Ankündigungen der Fakultät für Geschichte an der Contoso University".                                                                                                                                                              |
| FromEmailAddress    | String   | Die Absenderadresse für E-Mails an diese Liste, z. B. donotreply@contoso.edu.                                                                                                                                                                                             |

Die folgende Tabelle zeigt die Zeileneigenschaften für diejenigen Zeilen, die Informationen zu Abonnenten der Mailingliste enthalten (Zeilenschlüssel = E-Mail-Adresse).

| Eigenschaft         | Datentyp | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|---------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partitionsschlüssel | String   | ListName: Der Name (eindeutiger Bezeichner) der Mailingliste, z. B. contoso1.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Zeilenschlüssel     | String   | EmailAddress: Die E-Mail-Adresse des Abonnenten, z. B. student1@contoso.edu.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| SubscriberGUID      | String   | Wird beim Hinzufügen der E-Mail-Adresse zur Liste generiert. Dieser Wert wird in Links zum Abonnieren/Abbestellen verwendet. Auf diese Weise soll verhindert werden, dass ein Benutzer ein Abonnement eines anderen Benutzers kündigen kann.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
                                   Manche Abfragen für die Webseiten zum Abonnieren/Abbestellen verwenden nur den Partitionsschlüssel und diese Eigenschaft. Abfragen über eine Partition ohne Zeilenschlüssel schränken die Skalierbarkeit der Anwendung ein, da die Abfragen mit zunehmender Größe der Mailinglisten langsamer werden. Zur Verbesserung der Skalierbarkeit könnten Nachschlagezeilen eingefügt werden, deren Zeilenschlüssel die SubscriberGUID enthält. Pro E-Mail-Adresse könnte z. B. eine Zeile mit dem Zeilenschlüssel "email:student1@domain.com" und eine weitere Zeile für denselben Abonnenten mit dem Zeilenschlüssel "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a" angelegt werden. Dies lässt sich leicht implementieren, da atomische Batch-Transaktionen auf Zeilen innerhalb einer Partition einfach zu programmieren sind. Weitere Informationen finden Sie unter [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (Design einer skalierbaren Partitionierungsstrategie für Azure-Tabellenspeicher, in englischer Sprache)][Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (Design einer skalierbaren Partitionierungsstrategie für Azure-Tabellenspeicher, in englischer Sprache)]  |
| Verified            | Boolean  | Dieser Wert ist "Falsch", wenn eine neue Zeile für einen neuen Abonnenten angelegt wird. Er wird erst auf "Wahr" gesetzt, nachdem der neue Abonnent auf den Bestätigungslink in der Willkommens-E-Mail klickt oder ein Administrator den Wert manuell ändert. Beim Versand von Nachrichten an Listen erhalten die Abonnenten, bei denen dieser Wert "Falsch" ist, keine E-Mail.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

Die folgende Liste zeigt mögliche Beispieldaten für diese Tabelle.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Beschreibung

</th>
<td>
Ankündigungen der Fakultät für Geschichte an der Contoso University

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
student1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
6f32b03b-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
wahr

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
student2@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
01234567-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
falsch

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Beschreibung

</th>
<td>
Technische Stellenausschreibungen von Fabrikam

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@fabrikam.com

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
applicant1@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verified

</th>
<td>
wahr

</td>
</tr>
</table>
### Tabelle message

Die Tabelle `message` enthält Informationen über Nachrichten, deren Versand an eine Mailingliste geplant wurde. Administratoren erstellen und bearbeiten Zeilen in dieser Tabelle über Webseiten, und die Workerrollen übergeben anhand dieser Tabelle die Informationen über einzelne E-Mails von Workerrolle A zu Workerrolle B.

Der Partitionsschlüssel für die Tabelle message ist das geplante Versanddatum der E-Mail im Format jjjj-mm-tt. Auf diese Weise wird die Tabelle für die am häufigsten ausgeführte Abfrage optimiert, nämlich die Auswahl von Zeilen mit einem Wert von heute oder früher im Feld `ScheduledDate`. Dabei entsteht jedoch auch ein möglicher Leistungsengpass, da Azure-Speichertabelle einen maximalen Durchsatz von 500 Entitäten pro Sekunde für eine Partition bieten. Die Anwendung schreibt für jede zu verschickende E-Mail eine Zeile in die Tabelle `message`, liest eine Zeile und löscht eine Zeile. Die kürzestmögliche Zeit für die Verarbeitung von 1.000.000 E-Mails an einem einzigen Tag beträgt daher knapp zwei Stunden, unabhängig von der Anzahl der Workerrollen zur Bewältigung der zusätzlichen Last.

Für den Zeilenschlüssel der Tabelle `message` gibt es zwei Möglichkeiten: die Konstante "message" plus ein eindeutiger Schlüssel für die Nachricht namens `MessageRef` oder der `MessageRef`-Wert plus die E-Mail-Adresse des Abonnenten. Zeilen, deren Zeilenschlüssel mit "message" beginnt, enthalten Informationen über die Nachricht, wie z. B. die zugehörige Mailingliste und das geplante Versanddatum. Zeilen mit `MessageRef` und einer E-Mail-Adresse als Zeilenschlüssel enthalten alle benötigten Informationen für den Versand einer E-Mail an diese Adresse.

Im relationalen Kontext entsprechen Zeilen, deren Zeilenschlüssel mit "message" beginnt, einer `message`-Tabelle. Zeilen mit Zeilenschlüssel = `MessageRef` plus E-Mail-Adresse entsprechen einer join-Abfrage mit Informationen aus den Feldern `mailinglist`, `message` und `subscriber`.

Die folgende Tabelle zeigt die Zeileneigenschaften für diejenigen Zeilen der Tabelle `message`, die Informationen zur eigentlichen Nachricht enthalten.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Eigenschaft</th>
<th align="left">Datentyp</th>
<th align="left">Beschreibung</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Partitionsschlüssel</td>
<td align="left">String</td>
<td align="left">Das geplante Versanddatum der Nachricht im Format jjjj-mm-tt.</td>
</tr>
<tr class="even">
<td align="left">Zeilenschlüssel</td>
<td align="left">String</td>
<td align="left">Die Konstante &quot;message&quot; verkettet mit dem <code data-inline="1">MessageRef</code>-Wert. Der <code data-inline="1">MessageRef</code>-Wert ist ein eindeutiger Wert, der beim Erstellen der Zeile aus dem <code data-inline="1">Ticks</code>-Wert von <code data-inline="1">DateTime.Now</code> generiert wird.<br /><br />Hinweis: Anwendungen mit hohem Volumen, mehreren Threads und mehreren Instanzen müssen mit Ausnahmen für doppelt vorhandene Zeilenschlüssel umgehen können, wenn Ticks verwendet werden. Ticks sind nicht garantiert eindeutig.</td>
</tr>
<tr class="odd">
<td align="left">ScheduledDate</td>
<td align="left">Date</td>
<td align="left">Das geplante Versanddatum der Nachricht. (Gleicher Wert wie im <code data-inline="1">PartitionKey</code>, jedoch im Date-Format.)</td>
</tr>
<tr class="even">
<td align="left">SubjectLine</td>
<td align="left">String</td>
<td align="left">Die Betreffzeile der E-Mail.</td>
</tr>
<tr class="odd">
<td align="left">ListName</td>
<td align="left">String</td>
<td align="left">Die Liste, an die diese Nachricht verschickt wird.</td>
</tr>
<tr class="even">
<td align="left">Status</td>
<td align="left">String</td>
<td align="left"><ul>
<li>&quot;Ausstehend&quot; -- Workerrolle A hat noch nicht mit der Erstellung von Warteschlangen-Nachrichten für die Planung der E-Mails begonnen.</li>
<li>&quot;Queuing&quot; -- Workerrolle A hat bereits mit der Erstellung von Warteschlangen-Nachrichten für die Planung der E-Mails begonnen.</li>
<li>&quot;Verarbeitung&quot; -- Workerrolle A hat Warteschlangen-Nachrichten für sämtliche E-Mails in der Liste erstellt, die E-Mails wurden allerdings noch nicht vollständig verschickt.</li>
<li>&quot;Abgeschlossen&quot; -- Workerrolle B hat die Verarbeitung aller Warteschlangen-Arbeitsaufgabe abgeschlossen (alle E-Mails wurden verschickt). Abgeschlossene Zeilen werden in der Tabelle <code data-inline="1">messagearchive</code> archiviert, wie später in diesem Lernprogramm erklärt. Für die nächste Version wird diese Eigenschaft vermutlich in <code data-inline="1">enum</code> umgewandelt.</li>
</ul></td>
</tr>
</tbody>
</table>

Wenn Workerrolle A eine Warteschlangen-Nachricht für eine an eine Liste zu verschickende E-Mail erstellt, schreibt sie eine E-Mail-Zeile in die Tabelle `message`. Wenn Workerrolle B die E-Mail verschickt, verschiebt sie die E-Mail-Zeile in die Tabelle `messagearchive` und ändert die Eigenschaft `EmailSent` in `true`. Wenn alle E-Mail-Zeilen mit dem Status "Verarbeitung" für eine Nachricht archiviert wurden, setzt die Workerrolle A den Status auf "Abgeschlossen" und verschiebt die `message`-Zeile in die Tabelle `messagearchive`.

Die folgende Tabelle zeigt die Zeileneigenschaften für die E-Mail-Zeilen in der Tabelle `message`.

| Eigenschaft         | Datentyp | Beschreibung                                                                                                                       |
|---------------------|----------|------------------------------------------------------------------------------------------------------------------------------------|
| Partitionsschlüssel | String   | Das geplante Versanddatum der Nachricht im Format jjjj-mm-tt.                                                                      |
| Zeilenschlüssel     | String   | Der `MessageRef`-Wert und die Empfängeradresse aus der `subscriber`-Zeile der Tabelle `mailinglist`.                               |
| MessageRef          | Long     | Entspricht der `MessageRef`-Komponente im `RowKey`.                                                                                |
| ScheduledDate       | Date     | Das geplante Datum aus der `message`-Zeile in der Tabelle `message`. (Gleicher Wert wie im `PartitionKey`, jedoch im Date-Format.) |
| SubjectLine         | String   | Die Betreffzeile der E-Mail aus der `message`-Zeile in der Tabelle `message`.                                                      |
| ListName            | String   | Der Name der Mailingliste aus der Tabelle `mailinglist`.                                                                           |
| From EmailAddress   | String   | Der Absender der E-Mail aus der `mailinglist`-Zeile in der Tabelle `mailinglist`.                                                  |
| EmailAddress        | String   | Die E-Mail-Adresse aus der `subscriber`-Zeile in der Tabelle `mailinglist`.                                                        |
| SubscriberGUID      | String   | Die Abonnenten-GUID aus der `subscriber`-Zeile in der Tabelle `mailinglist`.                                                       |
| EmailSent           | Boolean  | "Falsch" bedeutet, dass die E-Mail noch nicht verschickt wurde; "wahr" bedeutet, dass die E-Mail verschickt wurde.                 |

Diese Zeilen enthalten redundante Daten, die man in relationalen Datenbanken normalerweise vermeiden würde. In diesem Fall tauschen Sie jedoch einige der Nachteile redundanter Daten gegen die Vorteile der besseren Verarbeitungseffizienz und Skalierbarkeit ein. Da sämtliche für den Versand einer E-Mail benötigten Daten in einer Zeile enthalten sind, braucht die Workerrolle B beim Abrufen von Arbeitsaufgaben aus der Warteschlange auch nur eine Zeile auslesen.

Vielleicht haben Sie sich gefragt, woher der Text der E-Mail kommen soll. Diese Zeilen haben keine Blob-Referenzen für die Dateien, die den E-Mail-Text enthalten, da dieser Wert vom `MessageRef`-Wert abgeleitet wird. Wenn der `MessageRef`-Wert z. B. 634852858215726983 ist, dann heißen die Blobs 634852858215726983.htm und 634852858215726983.txt.

Die folgende Liste zeigt mögliche Beispieldaten für diese Tabelle.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
message634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Neue Vorlesungsreihe

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Verarbeitung

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
634852858215726983student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Neue Vorlesungsreihe

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
wahr

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
634852858215726983student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Neue Vorlesungsreihe

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
12345678-90ed-41a9-b8ac-c1310c679876

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
wahr

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Partitionsschlüssel

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Zeilenschlüssel

</th>
<td>
message124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Neue Jobangebote

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
fabrikam

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Ausstehend

</td>
</tr>
</table>

### Tabelle messagearchive

Wenn Sie sicherstellen möchten, dass Abfragen insbesondere über Felder mit Ausnahme von `PartitionKey` und `RowKey` effizient ausgeführt werden, können Sie z. B. die Tabellengröße einschränken. Die Abfrage in Workerrolle A zur Prüfung, ob alle E-Mails für eine Nachricht verschickt wurden, sucht nach E-Mail-Zeilen in der Tabelle `message` mit `EmailSent` = falsch. Der `EmailSent`-Wert ist nicht Teil von Partitions- oder Zeilenschlüssel. Daher ist dies keine effiziente Abfrage für Nachrichten mit vielen E-Mail-Zeilen. Die Anwendung verschiebt deshalb E-Mail-Zeilen nach dem Versand der E-Mails in die Tabelle `messagearchive`. Folglich braucht die Abfrage zur Prüfung, ob alle E-Mails für eine Nachricht verschickt wurden, nur `PartitionKey` und `RowKey` der Tabelle "message" abzufragen. Wenn diese Abfrage irgendwelche E-Mail-Zeilen für eine Nachricht findet, bedeutet dies, dass noch nicht alle E-Mails verschickt wurden und die Nachricht noch nicht als `Complete` markiert werden kann.

Das Schema der Zeilen in der Tabelle `messagearchive` ist identisch mit dem Schema der Tabelle `message`. Je nachdem, was Sie mit diesen Archivdaten vorhaben, können Sie Größe und Ausgaben begrenzen, indem Sie die Anzahl der pro Zeile gespeicherten Eigenschaften reduzieren oder indem Sie Zeilen nach Ablauf einer bestimmten Zeit löschen.

## <a name="queues"></a>Azure-Warteschlangen

Azure-Warteschlangen erleichtern die Kommunikation zwischen den unterschiedlichen Anwendungsebenen und zwischen Workerrollen innerhalb der Back-End-Ebene.
Workerrolle A und Workerrolle B kommunizieren mithilfe von Warteschlangen, um die Anwendung skalierbar zu machen. Workerrolle A könnte z. B. eine Zeile pro E-Mail in der Tabelle message anlegen, und Workerrolle B könnte die Tabelle auf Zeilen mit noch nicht verschickten E-Mails prüfen. In diesem Fall wäre es jedoch nicht möglich, die Arbeit auf weitere Instanzen von Workerrolle B aufzuteilen. Bei der Koordination der Arbeit zwischen Workerrolle A und Workerrolle B über Tabellenzeilen lässt sich nicht garantieren, dass jede Tabellenzeile nur von einer Workerrolleninstanz verarbeitet wird. Warteschlangen bieten eben diese Garantie. Wenn eine Workerrolleninstanz eine Arbeitsaufgabe aus der Warteschlange entnimmt, garantiert der Warteschlangendienst, dass keine andere Workerrolleninstanz dieselbe Arbeitsaufgabe entnehmen kann. Diese Exklusivitätsfunktion von Azure-Warteschlangen erleichtert das Aufteilen einer Arbeitslast auf mehrere Instanzen einer Workerrolle.

Azure bietet außerdem noch den Service Bus-Warteschlangendienst. Weitere Informationen zu Azure Storage-Warteschlangen und Service Bus-Warteschlangen finden Sie im [letzten Lernprogramm in dieser Serie][Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung].

Die Azure-E-Mail-Dienstanwendung verwendet zwei Warteschlangen mit den Namen `AzureMailQueue` und `AzureMailSubscribeQueue`.

### AzureMailQueue

Die Warteschlange `AzureMailQueue` koordiniert den Versand von E-Mails an Mailinglisten. Workerrolle A legt für jede zu verschickende E-Mail eine Arbeitsaufgabe in die Warteschlange, und Workerrolle B nimmt eine Arbeitsaufgabe aus der Warteschlange und verschickt die E-Mail.

Eine Warteschlangen-Arbeitsaufgabe besteht aus einer durch Kommas getrennten Zeichenfolge und enthält das geplante Versanddatum der Nachricht (Partitionsschlüssel der Tabelle `message`) und die Werte `MessageRef` und `EmailAddress` (Zeilenschlüssel der Tabelle `message`), plus ein Kennzeichen, das angibt, ob das Element erstellt wurde, nachdem die Workerrolle ausgefallen ist und neu gestartet wurde, z. B.:

      2012-10-15,634852858215726983,student1@contoso.edu,0

Workerrolle B verwendet diese Werte, um eine Zeile aus der Tabelle `message` abzufragen, die alle für den Versand der E-Mail benötigten Informationen enthält. Wenn das Neustart-Kennzeichen einen Neustart anzeigt, dann muss Workerrolle B vor dem Versand sicherstellen, dass die E-Mail noch nicht verschickt wurde.

Für Lastspitzen kann der Cloud-Dienst so konfiguriert werden, dass mehrere Instanzen der Workerrolle B instanziiert werden, die unabhängig voneinander Arbeitsaufgaben aus der Warteschlange entnehmen.

### AzureMailSubscribeQueue

Die Warteschlange `AzureMailSubscribeQueue` koordiniert den Versand von Bestätigungs-E-Mails für Abonnements. Wenn eine bestimmte Dienstmethode aufgerufen wird, legt diese Dienstmethode eine Arbeitsaufgabe in der Warteschlange ab. Workerrolle B entnimmt die Arbeitsaufgabe aus der Warteschlange und verschickt die Bestätigungs-E-Mail.

Jede Warteschlangen-Arbeitsaufgabe enthält die Abonnenten-GUID. Dieser Wert identifiziert die E-Mail-Adresse und die zu abonnierende Liste eindeutig und liefert Workerrolle B somit alle für den Versand der Bestätigungs-E-Mail benötigten Informationen. Wie zuvor erklärt, ist dafür eine Abfrage über ein Feld erforderlich, das nicht Teil von `PartitionKey` oder `RowKey` ist, was im Allgemeinen ineffizient ist. Um die Anwendung skalierbarer zu machen, müsste die Tabelle `mailinglist` umgestaltet werden, sodass die Abonnenten-GUID im `RowKey` enthalten ist.

## <a name="datadiagram"></a><span class="short-header">Datendiagramm</span>Datendiagramm Azure-E-Mail-Dienst

Das folgende Diagramm enthält Tabellen, Warteschlangen und deren Beziehungen.

![Datendiagramm für die Azure-E-Mail-Dienstanwendung][Datendiagramm für die Azure-E-Mail-Dienstanwendung]

## <a name="blobs"></a>Azure-Blobs

Blobs sind "binary large objects" (große binäre Objekte). Der Azure-Blobdienst bietet Werkzeuge zum Hochladen und Speichern von Dateien in der Cloud. Weitere Informationen zu Azure-Blobs finden Sie im [letzten Lernprogramm in dieser Serie][Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung].

Administratoren des Azure-E-Mail-Dienstes können den Text von E-Mails im HTML-Format in *.htm*-Dateien und im Nur-Text-Format als *.txt*-Dateien ablegen. Bei der Planung von Nachrichten können sie diese Dateien auf der Webseite **Create Message** hochladen, und der ASP.NET MVC-Controller der Seite speichert die hochgeladene Datei in einem Azure-Blob.

Blobs werden in Blob-Containern gespeichert. Diese verhalten sich ähnlich wie Ordner für Dateien. Die Azure-E-Mail-Dienstanwendung verwendet einen einzigen Blob-Container mit den Namen **azuremailblobcontainer**. Der Name der Blobs im Container setzt sich zusammen aus dem MessageRef-Wert und der Dateiendung, z. B.: 634852858215726983.htm und 634852858215726983.txt.

Da sowohl HTML- als auch Nur-Text-Nachrichten aus Zeichenfolgen bestehen, könnte die Anwendung den Nachrichtentext auch in String-Eigenschaften in der Tabelle `Message` anstelle von Blobs speichern. Die Größe von Eigenschaften in Tabellenzeilen ist jedoch auf 64K beschränkt, und Blobs haben keine solche Einschränkung für den E-Mail-Text. (64K ist die maximale Gesamtgröße der Eigenschaft. Nach Abzug überschüssiger Codierungszeichen beträgt die maximale Größe von Zeichenfolgen in Eigenschaften ca. 48k.)

## <a name="wawsvswacs"></a>Azure-Cloud-Dienst und Azure-Website im Vergleich

Der Azure-E-Mail-Dienst ist beim Download so konfiguriert, dass Front-End und Back-End in einem einzigen Azure-Cloud-Dienst untergebracht sind.

![Übersicht der Anwendungsarchitektur][Übersicht der Anwendungsarchitektur]

Alternativ kann das Front-End in einer Azure-Website ausgeführt werden.

![Alternative Anwendungsarchitektur][Alternative Anwendungsarchitektur]

Eine weitere Möglichkeit ist, das Front-End in einer Azure-Website auszuführen und das WebJobs-Feature zu verwenden, um das Back-End auf denselben Servern auszuführen, auf denen auch das Front-End ausgeführt wird. Weitere Informationen finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK][Erste Schritte mit dem Azure WebJobs SDK].

## <a name="cost"></a>Kosten

Dieser Abschnitt enthält eine kurze Übersicht über die Kosten für die Ausführung der Beispielanwendung in Azure unter Verwendung der ursprünglichen Tarife von Dezember 2012. Vor kostenbasierten Geschäftsentscheidungen sollten Sie immer die aktuellen Tarife auf den folgenden Webseiten prüfen:

-   [Azure-Preisrechner][Azure-Preisrechner]
-   [SendGrid Azure][SendGrid Azure]

Die Kosten hängen von der Anzahl der Web- und Workerrolleninstanzen ab, die Sie betreiben. Um sich für die [99,95%-Servicelevel-Vereinbarung (SLA) des Azure-Cloud-Dienstes][99,95%-Servicelevel-Vereinbarung (SLA) des Azure-Cloud-Dienstes] zu qualifizieren, müssen Sie mindestens zwei Instanzen pro Rolle bereitstellen. Dies liegt unter anderem daran, dass die virtuellen Computer, auf denen Ihre Anwendung läuft, ca. zwei mal pro Monat für Betriebssystem-Upgrades neu gestartet werden müssen. (Weitere Informationen zu OS-Updates finden Sie unter [Role Instance Restarts Due to OS Upgrades][Role Instance Restarts Due to OS Upgrades] (Neustarts von Rolleninstanzen für OS-Upgrades, in englischer Sprache).)

Die Arbeit der Workerrollen in diesem Beispiel ist nicht zeitkritisch und benötigt daher nicht zwingend die 99,5%-SLA. Sie können daher eine einzige Instanz pro Workerrolle betreiben, solange diese Instanz die Arbeitslast bewältigen kann. Die Webrolleninstanz ist dagegen zeitkritisch. Benutzer erwarten eine Website ohne Ausfälle, und daher sollte eine Produktionsanwendung aus mindestens zwei Instanzen der Webrolle bestehen.

Die folgende Tabelle zeigt die Kosten für die Standardarchitektur der Azure-E-Mail-Dienst-Beispielanwendung unter Annahme einer minimalen Arbeitslast. Für die Kostenrechnung wird von virtuellen Computern der Größe sehr klein (freigegeben) ausgegangen. Die Standardgröße für virtuelle Computer bei der Erstellung von Visual Studio-Cloudprojekten ist klein und der Preis für diese Computer ist ca. sechs mal höher als für die Größe sehr klein.

<table border="1">
<tr bgcolor="lightgray">
<th>
Komponente bzw. Dienst

</th>
<th>
Tarif

</th>
<th>
Kosten pro Monat

</th>
</tr>
<tr>
<td>
Webrolle

</td>
<td>
2 Instanzen zu 0,02 $ pro Stunde für sehr kleine Instanzen

</td>
<td>
$29.00

</td>
</tr>
<tr>
<td>
Workerrolle A (plant den Versand von E-Mails)

</td>
<td>
1 Instanz zu 0,02 $ pro Stunde für sehr kleine Instanzen

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Workerrolle B (verschickt E-Mails)

</td>
<td>
1 Instanz zu 0,02 $ pro Stunde für sehr kleine Instanzen

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Azure-Speichertransaktionen

</td>
<td>
1 Million Transaktionen pro Monat zu 0,10 $ pro Million (Jede Abfrage zählt als eine Transaktion; Workerrolle A prüft fortlaufend Tabellen auf zu verschickende Nachrichten. Außerdem schreibt die Anwendung Diagnosedaten in den Azure-Speicher, und jeder dieser Schreibvorgänge zählt als Transaktion.)

</td>
<td>
$0.10

</td>
</tr>
<tr>
<td>
Lokal redundanter Azure-Speicher

</td>
<td>
2,33 $ für 25 GB (Inklusive Speicher für Anwendungstabellen und Diagnosedaten.)

</td>
<td>
$2.33

</td>
</tr>
<tr>
<td>
Bandbreite

</td>
<td>
5 GB ausgehend sind kostenlos

</td>
<td>
Kostenlos

</td>
</tr>
<tr>
<td>
SendGrid

</td>
<td>
Azure-Kunden können 25.000 E-Mails pro Monat kostenlos verschicken

</td>
<td>
Kostenlos

</td>
</tr>
<tr>
<td colspan="2">
Gesamt

</td>
<td>
$60.43

</td>
</tr>
</table>
Sie sehen also, dass Rolleninstanzen einen Großteil der Gesamtkosten ausmachen. Auch angehaltene Rolleninstanzen verursachen Kosten. Sie müssen die Rolleninstanz löschen, um weitere Kosten zu vermeiden. Um Kosten zu sparen, können Sie z. B. sämtlichen Code aus Workerrolle A und Workerrolle B in einer Workerrolle zusammenfassen. Diese Lernprogramme verwenden bewusst zwei Workerinstanzen, um die horizontale Skalierung zu vereinfachen. Die Arbeit von Workerrolle B wird vom Azure-Warteschlangendienst koordiniert, und Sie können Workerrolle B horizontal skalieren, indem Sie die Anzahl der Rolleninstanzen erhöhen. (Workerrolle B ist der begrenzende Faktor für hohe Lastbedingungen.) Die Arbeit von Workerrolle A wird nicht durch Warteschlangen koordiniert, daher können Sie keine zusätzlichen Instanzen von Workerrolle A einsetzen. Wenn die beiden Workerrollen kombiniert wären und Sie horizontal skalieren müssten, bräuchten Sie einen Mechanismus, um sicherzustellen, dass die Aufgaben von Workerrolle A nur in einer Instanz laufen. (Ein solcher Mechanismus wird von [CloudFx][CloudFx] angeboten. Siehe dazu das [WorkerRole.cs-Beispiel][WorkerRole.cs-Beispiel].)

Es ist auch möglich, sämtlichen Code der beiden Workerrollen in eine Webrolle zu verschieben und alles in einer Webrolle auszuführen. Hintergrundaufgaben werden in ASP.NET jedoch nicht unterstützt und gelten nicht als robust, und eine solche Architektur würde die Skalierbarkeit beeinträchtigen. Weitere Informationen finden Sie unter [The Dangers of Implementing Recurring Background Tasks In ASP.NET][The Dangers of Implementing Recurring Background Tasks In ASP.NET] (Die Gefahren wiederkehrender Hintergrundaufgaben in ASP.NET, in englischer Sprache). Siehe auch [How to Combine a Worker and Web Role in Azure][How to Combine a Worker and Web Role in Azure] (Kombinieren von Worker- und Webrolle in Azure, in englischer Sprache) und [Combining Multiple Azure Worker Roles into an Azure Web Role][Combining Multiple Azure Worker Roles into an Azure Web Role] (Kombinieren von Azure-Workerrollen in einer Azure-Webrolle, in englischer Sprache). Wenn Sie in diese Richtung gehen möchten, ist die [Ausführung in einer Azure-Website und Verwendung des WebJobs-Features für Back-End-Aufgaben][Ausführung in einer Azure-Website und Verwendung des WebJobs-Features für Back-End-Aufgaben] eine bessere Lösung.

Eine weitere Möglichkeit zur Kostensenkung ist der [Anwendungsblock für automatische Skalierung][Anwendungsblock für automatische Skalierung], um Workerrollen nur während bestimmter Zeitintervalle einzusetzen und nach Abschluss der Arbeit zu löschen. Weitere Informationen zur automatischen Skalierung finden Sie im [letzten Lernprogramm in dieser Serie][Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung].

Azure bietet in Zukunft möglicherweise einen Benachrichtigungsmechanismus für geplante Neustarts an, sodass Sie eine zusätzliche Webrolle für das Neustart-Zeitfenster starten können. Sie würden sich damit zwar nicht für die 99,95%-SLA qualifizieren, aber könnten Ihre Kosten um beinahe die Hälfte senken und sicherstellen, dass Ihre Webanwendung während des Neustarts verfügbar ist.

## <a name="auth"></a>Authentifizierung und Autorisierung

In Produktionsanwendungen würden Sie einen Authentifizierungs- und Autorisierungsmechanismus wie [ASP.NET Identity][ASP.NET Identity] für das ASP.NET MVC-Web-Front-End entwickeln, inklusive der ASP.NET-Web-API-Dienstmethode. Für die Sicherung der Web-API-Dienstmethode existieren noch weitere Optionen, wie z. B. ein gemeinsamer geheimer Schlüssel. Authentifizierungs- und Autorisierungsfunktionen wurden in der Beispielanwendung ausgelassen, um deren Einrichtung und Bereitstellung zu vereinfachen.

## <a name="nextsteps"></a>Nächste Schritte

Im [nächsten Lernprogramm][Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung] werden Sie das Beispielprojekt herunterladen, Ihre Entwicklungsumgebung konfigurieren, das Projekt für Ihre Umgebung konfigurieren und anschließend lokal und in der Cloud testen. In den Lernprogrammen 3 bis 5 lernen Sie, das Projekt von Grund auf zu erstellen.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie im [letzten Lernprogramms in dieser Serie][letzten Lernprogramms in dieser Serie].

<div><a href="/de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">Lernprogramm 2</a></div>

  [die fertige Anwendung]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [E-Book]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
  [Verarbeitung der E-Mails]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
  [Erste Schritte mit Azure-Cloud-Diensten und ASP.NET]: /de-de/documentation/articles/cloud-services-dotnet-get-started/
  [Erste Schritte mit dem Azure WebJobs SDK]: /de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung]: /de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Erstellen der Webrolle für die Azure-E-Mail-Dienstanwendung]: /de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [Voraussetzungen]: #prerequisites
  [Übersicht Front-End]: #frontend
  [Übersicht Back-End]: #backend
  [Azure-Tabellen]: #tables
  [Azure-Warteschlangen]: #queues
  [Datendiagramm]: #datadiagram
  [Azure-Blobs]: #blobs
  [Azure-Cloud-Dienst und Azure-Website im Vergleich]: #wawsvswacs
  [Kosten]: #cost
  [Authentifizierung und Autorisierung]: #auth
  [Nächste Schritte]: #nextsteps
  [kostenloses Testkonto]: /de-de/pricing/free-trial/
  [Ihre MSDN-Abonnementenvorteile aktivieren]: /de-de/pricing/member-offers/msdn-benefits/
  [Indexseite Adressenliste]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
  [Indexseite Empfänger]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
  [Indexseite Nachrichten]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
  [Seite Nachricht erstellen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
  [Bestätigungs-E-Mail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
  [Seite "Willkommen in der Liste"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
  [Seite "Abonnement kündigen"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
  [Abbestellung bestätigt-Seite]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
  [Vermeidung doppelt verschickter E-Mails]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
  [Verarbeitung der Abonnement-Warteschlange]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
  [Azure SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336279.aspx
  [Datendiagramm für die Azure-E-Mail-Dienstanwendung]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png
  [Übersicht der Anwendungsarchitektur]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
  [Alternative Anwendungsarchitektur]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
  [Azure-Preisrechner]: http://www.windowsazure.com/de-de/pricing/calculator/
  [SendGrid Azure]: http://sendgrid.com/windowsazure.html
  [Role Instance Restarts Due to OS Upgrades]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [CloudFx]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
  [WorkerRole.cs-Beispiel]: http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169
  [The Dangers of Implementing Recurring Background Tasks In ASP.NET]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
  [How to Combine a Worker and Web Role in Azure]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
  [Combining Multiple Azure Worker Roles into an Azure Web Role]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
  [Ausführung in einer Azure-Website und Verwendung des WebJobs-Features für Back-End-Aufgaben]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Anwendungsblock für automatische Skalierung]: /de-de/develop/net/how-to-guides/autoscaling/
  [ASP.NET Identity]: http://asp.net/identity
  [letzten Lernprogramms in dieser Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps

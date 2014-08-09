<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="Step 1: Overview" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 1: Overview" metaKeywords="Azure tutorial, email list service app, email service architecture, Azure tutorial overview, Azure multi-tier, Azure storage, Azure blobs, Azure tables, Azure queues" description="Learn about the five-part multi-tier Azure web application tutorial." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Multi-tier ASP.NET MVC Web Site Tutorial - Step 1: Overview" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

ASP.NET-Webanwendungen mit mehreren Ebenen mithilfe von Azure-Speichertabellen, Warteschlangen und Blobs - 1 von 5
==================================================================================================================

Dieses Lernprogramm behandelt die Erstellung von ASP.NET-MVC-Webanwendungen mit mehreren Ebenen mithilfe von Azure-Speichertabellen, Warteschlangen und Blobs sowie die Bereitstellung solcher Anwendungen in einem Azure-Cloud-Dienst. Dieses Lernprogramm geht davon aus, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieser Serie sind Sie in der Lage, eine robuste und skalierbare datengesteuerte Webanwendung zu erstellen und diese in der Cloud bereitzustellen.

Dieses Dokument ist als kostenloses E-Book in der [TechNet E-Book-Galerie](http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs) verfügbar.

LernzieleLernziele
------------------

Diese Serie von Lernprogrammen vermittelt die folgenden Kenntnisse:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK.
-   Erstellen eines Cloudprojekts in Visual Studio mit einer ASP.NET MVC-Webrolle und zwei Workerrollen.
-   Veröffentlichen des Cloudprojekts in einem Azure Cloud-Dienst.
-   Veröffentlichen des MVC-Projekts auf einer Azure-Website, während die Workerrollen weiterhin in einem Cloud-Dienst arbeiten.
-   Verwenden des Dienstes für Warteschlangenspeicherung in Azure für die Kommunikation zwischen Ebenen oder zwischen Workerrollen.
-   Verwenden des Tabellenspeicherdienstes in Azure als hochskalierbarer Datenspeicher für strukturierte, nichtrelationale Daten.
-   Verwenden des Blob-Dienstes in Azure zum Speichern von Dateien in der Cloud.
-   Anzeigen und bearbeiten von Tabellen, Warteschlangen und Blobs in Azure mithilfe von Visual Studio oder des Azure-Speicher-Explorers.
-   Versenden von E-Mails mit SendGrid.
-   Konfigurieren von Ablaufverfolgung und Anzeigen der Ablaufverfolgungsdaten.
-   Skalieren einer Anwendung durch Einsatz zusätzlicher Workerrolleninstanzen.

Übersicht Front-EndÜbersicht Front-End
--------------------------------------

Sie werden eine Anwendung zum Verwalten von E-Mail-Listen. Das Front-End der Anwendung mit mehreren Ebenen beinhaltet Webseiten, mit denen Administratoren des Dienstes E-Mail-Listen verwalten können.

![Indexseite Adressenliste](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png)

![Indexseite Empfänger](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png)

Auf weiteren Seiten können Administratoren Nachrichten für den Versand an E-Mail-Listen erstellen.

![Indexseite Nachrichten](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png)

![Seite Nachricht erstellen](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png)

Dieser Dienst richtet sich an Unternehmen, deren Kunden sich auf der Webseite in eine Adressenliste eintragen können. Ein Administrator kann z. B. eine Liste für die Ankündigungen der Fakultät für Geschichte an der Contoso University einrichten. Wenn sich ein Student für die Ankündigungen der Fakultät für Geschichte interessiert und auf einen Link auf der Website der Contoso University klickt, dann ruft die Contoso University einen Webdienst des Azure-E-Mail-Dienstes auf. Die Dienstmethode verschickt eine E-Mail an den Kunden. Diese E-Mail enthält einen Link zu einer Seite, die den Kunden in der Empfängerliste der Ankündigungen der Fakultät für Geschichte willkommen heißt.

![Bestätigungs-E-Mail](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png)

![Seite "Willkommen in der Liste"](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png)

Jede vom Dienst verschickte E-Mail (mit Ausnahme der Abonnementbestätigung) enthält einen Link zur Kündigung des Abonnements. Wenn der Empfänger auf diesen Link klickt, öffnet sich eine Seite, auf der er die Kündigung bestätigen kann.

![Seite "Abonnement kündigen"](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png)

Wenn der Empfänger auf die Schaltfläche **Bestätigen** klickt, öffnet sich eine Seite, auf der die Kündigung des Abonnements bestätigt wird.

![Seite "Kündigung bestätigt"](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png)

LernprogrammeLernprogramme in dieser Serie
------------------------------------------

Es folgt eine Liste der Lernprogramme mit einer kurzen Zusammenfassung der Inhalte:

1.  **Einführung in die Azure-E-Mail-Dienstanwendung** (dieses Lernprogramm). Übersicht über die Anwendung und deren Architektur.
2.  [Konfigurieren und Bereitstellen der Azure-E-Mail-Dienstanwendung](/de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). Herunterladen der Beispielanwendung mit anschließender Konfiguration, lokalen Tests sowie Bereitstellung und Test in der Cloud.
3.  [Erstellen der Webrolle für die Azure-E-Mail-Dienstanwendung](/de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/). Erstellen der MVC 4-Komponenten für die Anwendung und lokale Tests der Komponenten.
4.  [Erstellen der Workerrolle A (E-Mail-Planer) für die Azure-E-Mail-Dienstanwendung](/de-de/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/). Erstellen der Back-End-Komponente, die Warteschlangen-Arbeitsaufgaben für den E-Mail-Versand generiert und lokale Tests der Komponente.
5.  [Erstellen der Workerrolle B (E-Mail-Versand) für die Azure-E-Mail-Dienstanwendung](/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/). Erstellen der Back-End-Komponente, die Warteschlangen-Arbeitsaufgaben für den E-Mail-Versand verarbeitet und lokale Tests der Komponente.

Falls Sie die Anwendung direkt herunterladen und ausprobieren möchten, benötigen Sie nur die ersten zwei Lernprogramme. Falls Sie alle Schritte für die Erstellung einer solchen Anwendung von Grund auf kennenlernen möchten, können Sie die letzten drei Lernprogramme im Anschluss an die ersten zwei durcharbeiten.

Warum diese AnwendungWarum eine Anwendung für E-Mail-Listen
-----------------------------------------------------------

Wir haben für dieses Beispiel eine Anwendung für E-Mail-Listen ausgewählt, weil diese Art von Anwendung besonders robust und skalierbar sein muss, zwei der wichtigsten Gründe für Azure.

### Robust

Wenn ein Server beim Versand von E-Mails an eine große Liste ausfällt, möchten Sie möglichst schnell und einfach einen neuen Server einsetzen, und die Anwendung soll an der richtigen Stelle fortfahren, ohne E-Mails auszulassen oder doppelt zu senden. Web- und Workerrolleninstanzen (virtuelle Computer) werden in Azure Cloud-Diensten bei einem Ausfall automatisch ersetzt. Mit den Speichertabellen und Warteschlangen in Azure können Sie serverübergreifende Kommunikation implementieren, deren bisher geleistete Arbeit bei einem Ausfall erhalten bleibt.

### Skalierbar

E-Mail-Dienste müssen außerdem mit Lastspitzen umgehen können, da der Versand manchmal an sehr kleine und manchmal an sehr große Listen erfolgt. In vielen Hostumgebungen müssen Sie ausreichende Hardware für den Umgang mit Lastspitzen kaufen und instandhalten, und Sie bezahlen 100% der Zeit für eine Kapazität, die Sie möglicherweise nur während 5% der Zeit wirklich brauchen. Mit Azure bezahlen Sie nur für die tatsächlich benötigte Rechenleistung und nur so lange, wie Sie diese auch nutzen. Für die Skalierung vor einem großen Mailversand können Sie die Anzahl der zur Verfügung stehenden Server per Konfigurationseinstellung erhöhen. Dies ist auch programmgesteuert möglich. Sie können z. B. Ihre Anwendung so einstellen, dass Azure ab einer bestimmten Anzahl Arbeitsaufgaben in der Warteschlange automatisch zusätzliche Instanzen der Workerrolle startet, die diese Arbeitsaufgaben verarbeitet.

Übersicht Back-EndÜbersicht Back-End
------------------------------------

Das Front-End speichert E-Mail-Listen und zu verschickende Nachrichten in Azure-Tabellen. Wenn ein Administrator den Versand einer Nachricht plant, wird eine Tabellenzeile mit dem geplanten Datum und anderen Daten wie z. B. der Betreffzeile in die Tabelle `message` eingetragen. Eine Workerrolle prüft in regelmäßigen Abständen die Tabelle `message` auf Nachrichten, die noch nicht verschickt wurden (Workerrolle A).

Wenn Workerrolle A eine noch nicht verschickte Nachricht findet, führt sie die folgenden Aufgaben aus:

-   Abrufen aller E-Mail-Adressen in der Empfängerliste für die E-Mail.
-   Einfügen der Informationen für den Versand der einzelnen E-Mails in die Tabelle `message`.
-   Erstellung einer Warteschlangen-Arbeitsaufgabe für jede zu verschickende E-Mail.

Eine zweite Workerrolle (Workerrolle B) prüft die Warteschlange auf Arbeitsaufgaben. Wenn Workerrolle B eine Arbeitsaufgabe findet, wird diese Aufgabe verarbeitet, die E-Mail verschickt und die Arbeitsaufgabe anschließend aus der Warteschlange gelöscht. Das folgende Diagramm zeigt diese Beziehungen.

![Verarbeitung der E-Mails](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png)

Wenn Workerrolle B ausfällt und ersetzt werden muss, gehen keine E-Mails verloren, weil die Warteschlangen-Arbeitsaufgabe erst nach dem Versand der E-Mail gelöscht wird. Das Back-End implementiert außerdem Tabellenregeln, um den mehrfachen Versand von E-Mails zu verhindern, falls Workerrolle A ausfällt und neu gestartet werden muss. In diesem Fall kann es passieren, dass für manche Empfängeradressen mehrere Warteschlangen-Arbeitsaufgaben generiert werden. Allerdings wird für jede Empfängeradresse anhand einer Zeile in der Tabelle `message` geprüft, ob die E-Mail verschickt wurde. Je nach Timing des Neustarts und der E-Mail-Verarbeitung stellt Workerrolle A mit dieser Zeile sicher, dass keine zweite Arbeitsaufgabe erstellt wird, und Workerrolle B stellt sicher, dass keine zweite E-Mail verschickt wird.

![Vermeidung doppelt verschickter E-Mails](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png)

Workerrolle B prüft außerdem eine Abonnement-Warteschlange auf neue Arbeitsaufgaben von der Web-API-Dienstmethode für neue Abonnements. Wenn die Workerrolle eine Arbeitsaufgabe findet, verschickt sie die Bestätigungs-E-Mail.

![Verarbeitung der Abonnement-Warteschlange](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png)

TabellenAzure-Tabellen
----------------------

Die Azure-E-Mail-Dienstanwendung speichert Daten in Azure-Speichertabellen. Azure-Tabellen sind eine Form von NoSQL-Datenspeicher im Gegensatz zu relationalen Datenbanken wie der [Azure SQL-Datenbank](http://msdn.microsoft.com/de-de/library/windowsazure/ee336279.aspx). Sie sind daher eine gute Wahl, wenn Effizienz und Skalierbarkeit wichtiger sind als Datennormalisierung und relationale Integrität. In dieser Anwendung erstellt eine Workerrolle eine Zeile pro erstellter Arbeitsaufgabe, und eine andere Workerrolle liest und aktualisiert eine Zeile für jede verschickte E-Mail. Dies könnte in einer relationalen Datenbank zu einem Leistungsengpass führen. Außerdem sind Azure-Tabellen günstiger als Azure-SQL. Weitere Informationen zu Azure-Tabellen finden Sie in der Ressourcenliste am Ende des [letzten Lernprogramms in dieser Serie](/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Der folgende Abschnitt beschreibt den Inhalt der Azure-Tabellen, die von der Azure-E-Mail-Dienstanwendung verwendet werden. Das [Datendiagramm der Azure-E-Mail-Dienstanwendung](#datadiagram) im weiteren Verlauf dieser Seite bildet die Tabellen und ihre Beziehungen ab.

### Tabelle mailinglist

Die Tabelle `mailinglist` enthält Informationen über Mailinglisten und deren Abonnenten. (Die Namenskonvention für Azure-Tabellen empfiehlt den Gebrauch von Kleinbuchstaben.) Administratoren können auf den Webseiten Mailinglisten erstellen und bearbeiten, und Kunden und Abonnenten können auf anderen Webseiten eine Dienstmethode zum Abonnieren und Abbestellen aufrufen.

Unterschiedliche Zeilen in NoSQL-Tabellen können unterschiedliche Schemata haben. Diese Flexibilität wird normalerweise genutzt, um Daten in einer Tabelle zu speichern, für die in einer relationalen Datenbank mehrere Tabellen notwendig wären. Zum Speichern der Mailinglisten-Daten könnten Sie in einer SQL-Datenbank drei Tabellen verwenden: Eine Tabelle `mailinglist` mit Informationen über die Liste, eine Tabelle `subscriber` mit Informationen über Abonnenten und eine Tabelle `mailinglistsubscriber`, die Mailinglisten mit Abonnenten verknüpft und umgekehrt. In der NoSQL-Tabelle der aktuellen Anwendung sind all diese Funktionen in einer Tabelle namens `mailinglist` enthalten.

Jede Zeile in einer Azure-Tabelle hat einen *Partitionsschlüssel* und einen *Zeilenschlüssel*, mit denen sich die Zeile eindeutig identifizieren lässt. Der Partitionsschlüssel unterteilt die Tabelle in logische Partitionen. Innerhalb einer Partition identifiziert der Zeilenschlüssel jede Zeile eindeutig. Da diese Art von Tabellen keine sekundären Indizes enthält, müssen Sie die Skalierbarkeit der Anwendung sicherstellen. Achten Sie beim Design Ihrer Tabellen darauf, dass Sie in der WHERE-Klausel Ihrer Abfragen immer den Partitionsschlüssel und den Zeilenschlüssel angeben können.

Der Partitionsschlüssel für die Tabelle `mailinglist` ist der Name der Mailingliste.

Für den Zeilenschlüssel der Tabelle `mailinglist` gibt es zwei Möglichkeiten: Entweder die Konstante "mailinglist" oder die E-Mail-Adresse des Abonnenten. Zeilen mit dem Zeilenschlüssel "mailinglist" enthalten Informationen über die Mailingliste. Zeilen mit einer E-Mail-Adresse als Zeilenschlüssel enthalten Informationen über die Abonnenten der Liste.

Mit anderen Worten: Zeilen mit dem Zeilenschlüssel "mailinglist" entsprechen einer Tabelle `mailinglist` in einer relationalen Datenbank. Zeilen mit Zeilenschlüssel = E-Mail-Adresse entsprechen einer Tabelle `subscriber` und einer Zuordnungstabelle `mailinglistsubscriber` in einer relationalen Datenbank.

Die Nutzung einer einzigen Tabelle für verschiedene Zwecke auf diese Art verbessert die Leistung. In einer relationalen Datenbank müssten drei Tabellen gelesen und drei Sätze von Zeilen sortiert und miteinander verglichen werden, was zeitaufwändig sein kann. In diesem Fall wird nur eine Tabelle gelesen, und die Zeilen werden automatisch nach Partitionsschlüssel und Zeilenschlüssel sortiert zurückgegeben.

Die folgende Tabelle zeigt die Zeileneigenschaften für diejenigen Zeilen, die Informationen zur Mailingliste enthalten (Zeilenschlüssel = "MailingList").

<table  border="1">

<tr  bgcolor="lightgray">
<th>Eigenschaft</th>

<th>Datentyp</th>

<th>Beschreibung</th>

</tr>


<tr>
<td>Partitionsschlüssel</td>

<td>String</td>

<td>ListName:  Ein eindeutiger Bezeichner für die Mailingliste, z. B. contoso1. Ein häufiger Anwendungszweck für die Tabelle ist das Abrufen aller Informationen für eine bestimmte Mailingliste. Daher ist der Listenname ein guter Schlüssel zum Partitionieren der Tabelle.</td>

</tr>


<tr>
<td>Zeilenschlüssel</td>

<td>String</td>

<td>Die Konstante "mailinglist".</td>

</tr>


<tr>
<td>Beschreibung</td>

<td>String</td>

<td>Beschreibung der Mailingliste, z. B.: "Ankündigungen der Fakultät für Geschichte an der Contoso University".</td>

</tr>


<tr>
<td>FromEmailAddress</td>

<td>String</td>

<td>Die Absenderadresse für E-Mails an diese Liste, z. B.: donotreply@contoso.edu.</td>

</tr>


</table>

Die folgende Tabelle zeigt die Zeileneigenschaften für diejenigen Zeilen, die Informationen zu Abonnenten der Mailingliste enthalten (Zeilenschlüssel = E-Mail-Adresse).

<table  border="1">

<tr  bgcolor="lightgray">
<th>Eigenschaft</th>

<th>Datentyp</th>

<th>Beschreibung</th>

</tr>


<tr>
<td>Partitionsschlüssel</td>

<td>String</td>

<td>ListName:  Der Name (eindeutiger Bezeichner) der Mailingliste, z. B. contoso1.</td>

</tr>


<tr>
<td>Zeilenschlüssel</td>

<td>String</td>

<td>EmailAddress:  Die E-Mail-Adresse des Abonnenten, z. B.: student1@contoso.edu.</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>Wird beim Hinzufügen der E-Mail-Adresse zur Liste generiert. Dieser Wert wird in Links zum Abonnieren/Abbestellen verwendet. Auf diese Weise soll verhindert werden, dass ein Benutzer ein Abonnement eines anderen Benutzers kündigen kann. <br  />
<br  />
Manche Abfragen für die Webseiten zum Abonnieren/Abbestellen verwenden nur den Partitionsschlüssel und diese Eigenschaft. Abfragen über eine Partition ohne Zeilenschlüssel schränken die Skalierbarkeit der Anwendung ein, da die Abfragen mit zunehmender Größe der Mailinglisten langsamer werden. Zur Verbesserung der Skalierbarkeit könnten Nachschlagezeilen eingefügt werden, deren Zeilenschlüssel die SubscriberGUID enthält. Pro E-Mail-Adresse könnte z. B. eine Zeile mit dem Zeilenschlüssel "email:student1@domain.com" und eine weitere Zeile für denselben Abonnenten mit dem Zeilenschlüssel "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a" angelegt werden. Dies lässt sich leicht implementieren, da atomische Batch-Transaktionen auf Zeilen innerhalb einer Partition einfach zu programmieren sind. Wir hoffen, dass wir diese Funktion bis zur nächsten Version der Beispielanwendung implementieren können. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/hh508997.aspx">Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (Design einer skalierbaren Partitionierungsstrategie für Azure-Tabellenspeicher, in englischer Sprache)</a>

</td>

</tr>


<tr>
<td>Verified</td>

<td>Boolean</td>

<td>Dieser Wert ist "Falsch", wenn eine neue Zeile für einen neuen Abonnenten angelegt wird. Er wird erst auf "Wahr" gesetzt, nachdem der neue Abonnent auf den Bestätigungslink in der Willkommens-E-Mail klickt oder ein Administrator den Wert manuell ändert. Beim Versand von Nachrichten an Listen erhalten die Abonnenten, bei denen dieser Wert "Falsch" ist, keine E-Mail.</td>

</tr>


</table>

Die folgende Liste zeigt mögliche Beispieldaten für diese Tabelle.

<table  border="1">
<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Beschreibung</th>

<td>Ankündigungen der Fakultät für Geschichte an der Contoso University</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>student1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>6f32b03b-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>wahr</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>student2@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>01234567-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>falsch</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Beschreibung</th>

<td>Technische Stellenausschreibungen von Fabrikam</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@fabrikam.com</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>applicant1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>wahr</td>

</tr>


</table>

### Tabelle message

Die Tabelle `message` enthält Informationen über Nachrichten, deren Versand an eine Mailingliste geplant wurde. Administratoren erstellen und bearbeiten Zeilen in dieser Tabelle über Webseiten, und die Workerrollen übergeben anhand dieser Tabelle die Informationen über einzelne E-Mails von Workerrolle A zu Workerrolle B.

Der Partitionsschlüssel für die Tabelle message ist das geplante Versanddatum der E-Mail im Format jjjj-mm-tt. Auf diese Weise wird die Tabelle für die am häufigsten ausgeführte Abfrage optimiert, nämlich die Auswahl von Zeilen mit einem Wert von heute oder früher im Feld `ScheduledDate`. Dabei entsteht jedoch auch ein möglicher Leistungsengpass, da Azure-Speichertabelle einen maximalen Durchsatz von 500 Entitäten pro Sekunde für eine Partition bieten. Die Anwendung schreibt für jede zu verschickende E-Mail eine Zeile in die Tabelle `message`, liest eine Zeile und löscht eine Zeile. Die kürzestmögliche Zeit für die Verarbeitung von 1.000.000 E-Mails an einem einzigen Tag beträgt daher knapp zwei Stunden, unabhängig von der Anzahl der Workerrollen zur Bewältigung der zusätzlichen Last.

Für den Zeilenschlüssel der Tabelle `message` gibt es zwei Möglichkeiten: Die Konstante "message" plus ein eindeutiger Schlüssel für die Nachricht namens `MessageRef`, oder der `MessageRef`-Wert plus die E-Mail-Adresse des Abonnenten. Zeilen, deren Zeilenschlüssel mit "message" beginnt, enthalten Informationen über die Nachricht, wie z. B. die zugehörige Mailingliste und das geplante Versanddatum. Zeilen mit `MessageRef` und einer E-Mail-Adresse als Zeilenschlüssel enthalten alle benötigten Informationen für den Versand einer E-Mail an diese Adresse.

Im relationalen Kontext entsprechen Zeilen, deren Zeilenschlüssel mit "message" beginnt, einer `message`-Tabelle. Zeilen mit Zeilenschlüssel = `MessageRef` plus E-Mail-Adresse entsprechen einer join-Abfrage mit Informationen aus den Feldern `mailinglist`, `message` und `subscriber`.

Die folgende Tabelle zeigt die Zeileneigenschaften für diejenigen Zeilen der Tabelle `message`, die Informationen zur eigentlichen Nachricht enthalten.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Eigenschaft</th>

<th>Datentyp</th>

<th>Beschreibung</th>

</tr>


<tr>
<td>Partitionsschlüssel</td>

<td>String</td>

<td>Das geplante Versanddatum der Nachricht im Format jjjj-mm-tt.</td>

</tr>


<tr>
<td>Zeilenschlüssel</td>

<td>String</td>

<td>Die Konstante "message" verkettet mit dem <code>MessageRef</code>
-Wert. <code>MessageRef</code>
 ist ein eindeutiger Wert, der aus dem <code>Ticks</code>
-Wert von <code>DateTime.Now</code>
 zum Erstellungszeitpunkt der Zeile generiert wird. <br  />
<br  />
Hinweis: Anwendungen mit hohem Volumen, mehreren Threads und mehreren Instanzen müssen mit Ausnahmen für doppelt vorhandene Zeilenschlüssel umgehen können, wenn Ticks verwendet werden. Ticks sind nicht garantiert eindeutig.</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>Das geplante Versanddatum der Nachricht. (Gleicher Wert wie im <code>PartitionKey</code>
, jedoch im Date-Format.)</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>Die Betreffzeile der E-Mail.</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>Die Liste, an die diese Nachricht verschickt wird.</td>

</tr>


<tr>
<td>Status</td>

<td>String</td>

<td><ul>
<li>"Ausstehend" -- Workerrolle A hat noch nicht mit der Erstellung von Warteschlangen-Nachrichten für die Planung der E-Mails begonnen.</li>

<li>"Queuing" -- Workerrolle A hat bereits mit der Erstellung von Warteschlangen-Nachrichten für die Planung der E-Mails begonnen.</li>

<li>"Verarbeitung" -- Workerrolle A hat Warteschlangen-Nachrichten für sämtliche E-Mails in der Liste erstellt, die E-Mails wurden allerdings noch nicht vollständig verschickt.</li>

<li>"Abgeschlossen" -- Workerrolle B hat die Verarbeitung aller Warteschlangen-Arbeitsaufgabe abgeschlossen (alle E-Mails wurden verschickt). Abgeschlossene Zeilen werden in der Tabelle <code>messagearchive</code>
 archiviert, wie später in diesem Lernprogramm erklärt. Für die nächste Version wird diese Eigenschaft vermutlich in <code>enum</code>
 umgewandelt.</li>
</ul>
</td>

</tr>


</table>

Wenn Workerrolle A eine Warteschlangen-Nachricht für eine zu verschickende E-Mail erstellt, schreibt sie eine E-Mail-Zeile in die Tabelle `message`. Wenn Workerrolle B die E-Mail verschickt, verschiebt sie die E-Mail-Zeile in die Tabelle `messagearchive` und ändert die Eigenschaft `EmailSent` zu `wahr`. Wenn alle E-Mail-Zeilen mit dem Status "Verarbeitung" für eine Nachricht archiviert wurden, setzt die Workerrolle A den Status auf "Abgeschlossen" und verschiebt die
`message`-Zeile in die Tabelle `messagearchive`.

Die folgende Tabelle zeigt die Zeileneigenschaften für die E-Mail-Zeilen in der Tabelle `message`.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Eigenschaft</th>

<th>Datentyp</th>

<th>Beschreibung</th>

</tr>


<tr>
<td>Partitionsschlüssel</td>

<td>String</td>

<td>Das geplante Versanddatum der Nachricht im Format jjjj-mm-tt.</td>

</tr>


<tr>
<td>Zeilenschlüssel</td>

<td>String</td>

<td>Der <code>MessageRef</code>
-Wert und die Empfängeradresse aus der <code>subscriber</code>
-Zeile der Tabelle <code>mailinglist</code>
.
</td>

</tr>


<tr>
<td>MessageRef</td>

<td>Long</td>

<td>Entspricht der <code>MessageRef</code>
-Komponente im <code>Zeilenschlüssel</code>
.</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>Das geplante Datum aus der <code>message</code>
-Zeile in der Tabelle <code>message</code>
. (Gleicher Wert wie im <code>PartitionKey</code>
, jedoch im Date-Format.)</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>Die Betreffzeile der E-Mail aus der <code>message</code>
-Zeile in der Tabelle <code>message</code>
.</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>Der Name der Mailingliste aus der Tabelle <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>From EmailAddress</td>

<td>String</td>

<td>Der Absender der E-Mail aus der <code>mailinglist</code>
-Zeile in der Tabelle <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>EmailAddress</td>

<td>String</td>

<td>Die E-Mail-Adresse aus der <code>subscriber</code>
-Zeile in der Tabelle <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>Die Abonnenten-GUID aus der <code>subscriber</code>
-Zeile in der Tabelle <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>EmailSent</td>

<td>Boolean</td>

<td>"Falsch" bedeutet, dass die E-Mail noch nicht verschickt wurde; "wahr" bedeutet, dass die E-Mail verschickt wurde.</td>

</tr>


</table>

Diese Zeilen enthalten redundante Daten, die man in relationalen Datenbanken normalerweise vermeiden würde. In diesem Fall tauschen Sie jedoch einige der Nachteile redundanter Daten gegen die Vorteile der besseren Verarbeitungseffizienz und Skalierbarkeit ein. Da sämtliche für den Versand einer E-Mail benötigten Daten in einer Zeile enthalten sind, braucht die Workerrolle B beim Abrufen von Arbeitsaufgaben aus der Warteschlange auch nur eine Zeile auslesen.

Vielleicht haben Sie sich gefragt, woher der Text der E-Mail kommen soll. Diese Zeilen haben keine Blob-Referenzen für die Dateien, die den E-Mail-Text enthalten, da dieser Wert vom `MessageRef`-Wert abgeleitet wird. Wenn der `MessageRef`-Wert z. B. 634852858215726983 ist, dann heißen die Blobs 634852858215726983.htm und 634852858215726983.txt.

Die folgende Liste zeigt mögliche Beispieldaten für diese Tabelle.

<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>message634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Neue Vorlesungsreihe</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Verarbeitung</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>634852858215726983student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Neue Vorlesungsreihe</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>wahr</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>634852858215726983student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Neue Vorlesungsreihe</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>12345678-90ed-41a9-b8ac-c1310c679876</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>wahr</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Partitionsschlüssel</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Zeilenschlüssel</th>

<td>message124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>Neue Jobangebote</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>fabrikam</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Ausstehend</td>

</tr>


</table>

### Tabelle messagearchive

Wenn Sie sicherstellen möchten, dass Abfragen insbesondere über Felder mit Ausnahme von `Partitionsschlüssel` und `Zeilenschlüssel` effizient ausgeführt werden, können Sie z. B. die Tabellengröße einschränken. Die Abfrage in Workerrolle A zur Prüfung, ob alle E-Mails für eine Nachricht verschickt wurden, sucht nach E-Mail-Zeilen in der Tabelle `message` mit `EmailSent` = falsch. Der `EmailSent`-Wert ist nicht Teil von Partitions- oder Zeilenschlüssel. Daher ist dies keine effiziente Abfrage für Nachrichten mit vielen E-Mail-Zeilen. Daher verschiebt die Anwendung E-Mail-Zeilen nach dem Versand der E-Mails in die Tabelle `messagearchive`. Daher braucht die Abfrage zur Prüfung, ob alle E-Mails für eine Nachricht verschickt wurden, nur `Partitionsschlüssel` und `Zeilenschlüssel` der Tabelle message abfragen. Wenn diese Abfrage irgendwelche E-Mail-Zeilen für eine Nachricht findet, bedeutet dies, dass noch nicht alle E-Mails verschickt wurden und die Nachricht noch nicht als `Abgeschlossen` markiert werden kann.

Das Schema der Zeilen in der Tabelle `messagearchive` ist identisch zum Schema der Tabelle `message`. Je nachdem, was Sie mit diesen Archivdaten vorhaben, können Sie Größe und Ausgaben begrenzen, indem Sie die Anzahl der pro Zeile gespeicherten Eigenschaften reduzieren oder indem Sie Zeilen nach Ablauf einer bestimmten Zeit löschen.

WarteschlangenAzure-Warteschlangen
----------------------------------

Azure-Warteschlangen erleichtern die Kommunikation zwischen den unterschiedlichen Anwendungsebenen und zwischen Workerrollen innerhalb der Back-End-Ebene. Workerrolle A und Workerrolle B kommunizieren mithilfe von Warteschlangen, um die Anwendung skalierbar zu machen. Workerrolle A könnte z. B. eine Zeile pro E-Mail in der Tabelle message anlegen, und Workerrolle B könnte die Tabelle auf Zeilen mit noch nicht verschickten E-Mails prüfen. In diesem Fall wäre es jedoch nicht möglich, die Arbeit auf weitere Instanzen von Workerrolle B aufzuteilen. Bei der Koordination der Arbeit zwischen Workerrolle A und Workerrolle B über Tabellenzeilen lässt sich nicht garantieren, dass jede Tabellenzeile nur von einer Workerrolleninstanz verarbeitet wird. Warteschlangen bieten eben diese Garantie. Wenn eine Workerrolleninstanz eine Arbeitsaufgabe aus der Warteschlange entnimmt, garantiert der Warteschlangendienst, dass keine andere Workerrolleninstanz dieselbe Arbeitsaufgabe entnehmen kann. Diese Exklusivitätsfunktion von Azure-Warteschlangen erleichtert das Aufteilen einer Arbeitslast auf mehrere Instanzen einer Workerrolle.

Azure bietet außerdem noch den Servicebus-Warteschlangendienst. Weitere Informationen zu Azure-Speichertabellen und Servicebus-Warteschlangen finden Sie in der Ressourcenliste am Ende des [letzten Lernprogramms in dieser Serie](/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Die Azure-E-Mail-Dienstanwendung verwendet zwei Warteschlangen mit den Namen `AzureMailQueue` und `AzureMailSubscribeQueue`.

### AzureMailQueue

Die Warteschlange `AzureMailQueue` koordiniert den Versand von E-Mails an Mailinglisten. Workerrolle A legt für jede zu verschickende E-Mail eine Arbeitsaufgabe in die Warteschlange, und Workerrolle B nimmt eine Arbeitsaufgabe aus der Warteschlange und verschickt die E-Mail.

Eine Warteschlangen-Arbeitsaufgabe besteht aus einer durch Kommas getrennten Zeichenfolge und enthält das geplante Versanddatum der Nachricht (Partitionsschlüssel der Tabelle `message`) und die Werte `MessageRef` und `EmailAddress` (Zeilenschlüssel der Tabelle `message`), plus ein Kennzeichen, das angibt, ob das Element erstellt wurde, nachdem die Workerrolle ausgefallen ist und neu gestartet wurde, z. B.:

      2012-10-15,634852858215726983,student1@contoso.edu,0

Workerrolle B verwendet diese Werte, um eine Zeile aus der Tabelle `message` abzufragen, die alle für den Versand der E-Mail benötigten Informationen enthält. Wenn das Neustart-Kennzeichen einen Neustart anzeigt, dann muss Workerrolle B vor dem Versand sicherstellen, dass die E-Mail noch nicht verschickt wurde.

Für Lastspitzen kann der Cloud-Dienst so konfiguriert werden, dass mehrere Instanzen der Workerrolle B instanziiert werden, die unabhängig voneinander Arbeitsaufgaben aus der Warteschlange entnehmen.

### AzureMailSubscribeQueue

Die Warteschlange `AzureMailSubscribeQueue` koordiniert den Versand von Bestätigungs-E-Mails für Abonnements. Wenn eine bestimmte Dienstmethode aufgerufen wird, legt diese Dienstmethode eine Arbeitsaufgabe in der Warteschlange ab. Workerrolle B entnimmt die Arbeitsaufgabe aus der Warteschlange und verschickt die Bestätigungs-E-Mail.

Jede Warteschlangen-Arbeitsaufgabe enthält die Abonnenten-GUID. Dieser Wert identifiziert die E-Mail-Adresse und die zu abonnierende Liste eindeutig und liefert Workerrolle B somit alle für den Versand der Bestätigungs-E-Mail benötigten Informationen. Wie zuvor erklärt ist dafür eine Abfrage über Felder erforderlich, die nicht Teil von `Partitionsschlüssel` oder `Zeilenschlüssel` sind, was im Allgemeinen ineffizient ist. Um die Anwendung skalierbarer zu machen, müsste die Tabelle `mailinglist` umgestaltet werden, sodass die Abonnenten-GUID im `Zeilenschlüssel` enthalten ist.

DatendiagrammDatendiagramm Azure-E-Mail-Dienst
----------------------------------------------

Das folgende Diagramm enthält Tabellen, Warteschlangen und deren Beziehungen.

![Datendiagramm für die Azure-E-Mail-Dienstanwendung](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png)

BlobsAzure-Blobs
----------------

Blobs sind "binary large objects" (große binäre Objekte). Der Azure-Blobdienst bietet Werkzeuge zum Hochladen und Speichern von Dateien in der Cloud. Weitere Informationen zu Azure-Blobs finden Sie in der Ressourcenliste am Ende des [letzten Lernprogramms in dieser Serie](/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Administratoren des Azure-E-Mail-Dienstes können den Text von E-Mails im HTML-Format in *.htm*-Dateien und im Nur-Text-Format als *.txt*-Dateien ablegen. Bei der Planung von Nachrichten können sie diese Dateien auf der Webseite **Create Message** hochladen, und der ASP.NET MVC-Controller der Seite speichert die hochgeladene Datei in einem Azure-Blob.

Blobs werden in Blob-Containern gespeichert. Diese verhalten sich ähnlich wie Ordner für Dateien. Die Azure-E-Mail-Dienstanwendung verwendet einen einzigen Blob-Container mit den Namen **azuremailblobcontainer**. Der Name der Blobs im Container setzt sich zusammen aus dem MessageRef-Wert und der Dateiendung, z. B.: 634852858215726983.htm und 634852858215726983.txt.

Da sowohl HTML- als auch Nur-Text-Nachrichten aus Zeichenfolgen bestehen, könnte die Anwendung den Nachrichtentext auch in String-Eigenschaften in der Tabelle `message` anstelle von Blobs speichern. Die Größe von Eigenschaften in Tabellenzeilen ist jedoch auf 64K beschränkt, und Blobs haben keine solche Einschränkung für den E-Mail-Text. (64K ist die maximale Gesamtgröße der Eigenschaft. Nach Abzug überschüssiger Codierungszeichen beträgt die maximale Größe von Zeichenfolgen in Eigenschaften ca. 48k.)

Cloud-Dienst vs. WebsiteAzure-Cloud-Dienst vs. Azure-Website
------------------------------------------------------------

Der Azure-E-Mail-Dienst ist beim Download so konfiguriert, dass Front-End und Back-End in einem einzigen Azure-Cloud-Dienst untergebracht sind.

![Übersicht der Anwendungsarchitektur](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png)

Alternativ kann das Front-End in einer Azure-Website ausgeführt werden.

![Alternative Anwendungsarchitektur](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png)

Konfiguration und Bereitstellung werden vereinfacht, wenn alle Komponenten in einem einzigen Cloud-Dienst untergebracht sind. Wenn Sie die Anwendung mit ASP.NET MVC-Front-End in einer Azure-Website erstellen, müssen Sie die Komponenten separat bereitstellen (Azure-Website und Azure-Cloud-Dienst). Außerdem bieten die Webrollen in Azure-Cloud-Diensten die folgenden Features, die in Azure-Websites nicht verfügbar sind:

-   Unterstützung für benutzerdefinierte Zertifikate und Zertifikate mit Platzhalter.
-   Vollständige Kontrolle über die IIS-Konfiguration. Viele IIS-Features können in Azure-Websites nicht aktiviert werden. Mit Azure-Webrollen können Sie einen Startbefehl definieren, der das [AppCmd](http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd")-Programm ausführt, um IIS-Einstellungen zu ändern, die nicht in Ihrer *Web.config*-Datei konfiguriert werden können. Weitere Informationen finden Sie unter [Konfigurieren von IIS-Komponenten in Windows Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433059.aspx) und [Blockieren des Zugriffs auf eine Webrolle für bestimmte IP-Adressen](http://msdn.microsoft.com/de-de/library/windowsazure/jj154098.aspx).
-   Support für die automatische Skalierung Ihrer Webanwendung mit dem [Anwendungsblock für automatische Skalierung](/de-de/develop/net/how-to-guides/autoscaling/).
-   Möglichkeit zur Ausführung erweiterter Startskripts zum Installieren von Anwendungen, Ändern von Registrierungseinstellungen, Installieren von Leistungsindikatoren usw.
-   Netzwerkisolation für den Einsatz mit [Azure Connect](http://msdn.microsoft.com/de-de/library/windowsazure/gg433122.aspx) und [virtuelle Azure-Netzwerke](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx).
-   Remotedesktopzugriff für Debugging und erweiterte Diagnose.
-   Parallele Aktualisierungen mit [VIP-Austausch](http://msdn.microsoft.com/de-de/library/windowsazure/ee517253.aspx "VIP-Swap"). Mit diesem Feature können Sie den Inhalt Ihrer Staging- und Produktionsbereitstellungen tauschen.

Die alternative Architektur bietet möglicherweise Kosteneinsparungen, da eine Azure-Website für dieselbe Kapazität günstiger ist als eine Webrolle in einem Cloud-Dienst. Die Implementierungsunterschiede zwischen den beiden Architekturen werden in späteren Lernprogrammen dieser Serie behandelt.

Weitere Informationen für die Wahl zwischen Azure-Websites und Azure-Cloud-Diensten finden Sie unter [Azure-Ausführungsmodelle](http://www.windowsazure.com/de-de/manage/windows/fundamentals/compute/).

KostenKosten
------------

Dieser Abschnitt enthält eine kurze Übersicht über die Kosten für die Ausführung der Beispielanwendung in Azure unter Verwendung der Tarife von Dezember 2012. Vor kostenbasierten Geschäftsentscheidungen sollten Sie immer die aktuellen Tarife auf den folgenden Webseiten prüfen:

-   [Azure-Preisrechner](http://www.windowsazure.com/de-de/pricing/calculator/)
-   [SendGrid Azure](http://sendgrid.com/windowsazure.html)

Die Kosten hängen von der Anzahl der Web- und Workerrolleninstanzen ab, die Sie betreiben. Um sich für die [99,95%-Servicelevel-Vereinbarung (SLA) des Azure-Cloud-Dienstes](https://www.windowsazure.com/en-us/support/legal/sla/ "SLA") zu qualifizieren, müssen Sie mindestens zwei Instanzen pro Rolle bereitstellen. Dies liegt unter anderem daran, dass die virtuellen Computer, auf denen Ihre Anwendung läuft, ca. zwei mal pro Monat für Betriebssystem-Upgrades neu gestartet werden müssen. (Weitere Informationen zu OS-Updates finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Neustarts von Rolleninstanzen für OS-Upgrades, in englischer Sprache).)

Die Arbeit der Workerrollen in diesem Beispiel ist nicht zeitkritisch und benötigt daher nicht zwingend die 99,5%-SLA. Sie können daher eine einzige Instanz pro Workerrolle betreiben, solange diese Instanz die Arbeitslast bewältigen kann. Die Webrolleninstanz ist dagegen zeitkritisch. Benutzer erwarten eine Website ohne Ausfälle, und daher sollte eine Produktionsanwendung aus mindestens zwei Instanzen der Webrolle bestehen.

Die folgende Tabelle zeigt die Kosten für die Standardarchitektur der Azure-E-Mail-Dienst-Beispielanwendung unter Annahme einer minimalen Arbeitslast. Für die Kostenrechnung wird von virtuellen Computern der Größe sehr klein (freigegeben) ausgegangen. Die Standardgröße für virtuelle Computer bei der Erstellung von Visual Studio-Cloudprojekten ist klein und der Preis für diese Computer ist ca. sechs mal höher als für die Größe sehr klein.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Komponente bzw. Dienst</th>

<th>Tarif</th>

<th>Kosten pro Monat</th>

</tr>


<tr>
<td>Webrolle</td>

<td>2 Instanzen zu 0,02 $ pro Stunde für sehr kleine Instanzen</td>

<td>$29.00</td>

</tr>


<tr>
<td>Workerrolle A (plant den Versand von E-Mails)</td>

<td>1 Instanz zu 0,02 $ pro Stunde für sehr kleine Instanzen</td>

<td>$14.50
</td>

</tr>


<tr>
<td>Workerrolle B (verschickt E-Mails)</td>

<td>1 Instanz zu 0,02 $ pro Stunde für sehr kleine Instanzen</td>

<td>$14.50</td>

</tr>


<tr>
<td>Azure-Speichertransaktionen</td>

<td>1 Million Transaktionen pro Monat zu 0,10 $ pro Million (Jede Abfrage zählt als eine Transaktion; Workerrolle A prüft fortlaufend Tabellen auf zu verschickende Nachrichten. Außerdem schreibt die Anwendung Diagnosedaten in den Azure-Speicher, und jeder dieser Schreibvorgänge zählt als Transaktion.)</td>

<td>$0.10</td>

</tr>


<tr>
<td>Lokal redundanter Azure-Speicher</td>

<td>2,33 $ für 25 GB (Inklusive Speicher für Anwendungstabellen und Diagnosedaten.)</td>

<td>$2.33</td>

</tr>


<tr>
<td>Bandbreite</td>

<td>5 GB ausgehend sind kostenlos</td>

<td>Kostenlos</td>

</tr>


<tr>
<td>SendGrid</td>

<td>Azure-Kunden können 25.000 E-Mails pro Monat kostenlos verschicken</td>

<td>Kostenlos</td>

</tr>


<tr>
<td  colspan="2">Gesamt</td>

<td>$60.43</td>

</tr>


</table>

Sie sehen also, dass Rolleninstanzen einen Großteil der Gesamtkosten ausmachen. Auch angehaltene Rolleninstanzen verursachen Kosten. Sie müssen die Rolleninstanz löschen, um weitere Kosten zu vermeiden. Um Kosten zu sparen, können Sie z. B. sämtlichen Code aus Workerrolle A und Workerrolle B in einer Workerrolle zusammenfassen. Diese Lernprogramme verwenden bewusst zwei Workerinstanzen, um die horizontale Skalierung zu vereinfachen. Die Arbeit von Workerrolle B wird vom Azure-Warteschlangendienst koordiniert, und Sie können Workerrolle B horizontal skalieren, indem Sie die Anzahl der Rolleninstanzen erhöhen. (Workerrolle B ist der begrenzende Faktor für hohe Lastbedingungen.) Die Arbeit von Workerrolle A wird nicht durch Warteschlangen koordiniert, daher können Sie keine zusätzlichen Instanzen von Workerrolle A einsetzen. Wenn die beiden Workerrollen kombiniert wären und Sie horizontal skalieren müssten, bräuchten Sie einen Mechanismus, um sicherzustellen, dass die Aufgaben von Workerrolle A nur in einer Instanz laufen. (Ein solcher Mechanismus wird von [CloudFx](http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX") angeboten. Siehe dazu das [WorkerRole.cs-Beispiel](http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169).)

Es ist auch möglich, sämtlichen Code der beiden Workerrollen in eine Webrolle zu verschieben und alles in einer Webrolle auszuführen. Hintergrundaufgaben werden in ASP.NET jedoch nicht unterstützt und gelten nicht als robust, und eine solche Architektur würde die Skalierbarkeit beeinträchtigen. Weitere Informationen finden Sie unter [The Dangers of Implementing Recurring Background Tasks In ASP.NET](http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx) (Die Gefahren wiederkehrender Hintergrundaufgaben in ASP.NET, in englischer Sprache). Siehe auch [How to Combine a Worker and Web Role in Azure](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html) (Kombinieren von Worker- und Webrolle in Azure, in englischer Sprache) und [Combining Multiple Azure Worker Roles into an Azure Web Role](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html) (Kombinieren von Azure-Workerrollen in einer Azure-Webrolle, in englischer Sprache).

Eine weitere Möglichkeit zur Kostensenkung ist der [Anwendungsblock für automatische Skalierung](/de-de/develop/net/how-to-guides/autoscaling/), um Workerrollen nur während bestimmter Zeitintervalle einzusetzen und nach Abschluss der Arbeit zu löschen. Weitere Informationen zur automatischen Skalierung finden Sie in den Links am Ende des [letzten Lernprogramms in dieser Serie](/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Azure bietet in Zukunft möglicherweise einen Benachrichtigungsmechanismus für geplante Neustarts an, sodass Sie eine zusätzliche Webrolle für das Neustart-Zeitfenster starten können. Sie würden sich damit zwar nicht für die 99,95%-SLA qualifizieren, aber könnten Ihre Kosten um beinahe die Hälfte senken und sicherstellen, dass Ihre Webanwendung während des Neustarts verfügbar ist.

Authentifizierung und AutorisierungAuthentifizierung und Autorisierung
----------------------------------------------------------------------

In Produktionsanwendungen würden Sie einen Authentifizierungs- und Autorisierungsmechanismus ähnlich des ASP.NET-Mitgliedschaftssystems für das ASP.NET MVC-Web-Front-End entwickeln, inklusive der ASP.NET-Web-API-Dienstmethode. Für die Sicherung der Web-API-Dienstmethode existieren noch weitere Optionen, wie z. B. ein gemeinsamer geheimer Schlüssel. Authentifizierungs- und Autorisierungsfunktionen wurden in der Beispielanwendung ausgelassen, um deren Einrichtung und Bereitstellung zu vereinfachen. (Das zweite Lernprogramm in dieser Serie beschreibt die Implementierung von IP-Einschränkungen, um unbefugten Zugriff bei der Bereitstellung in der Cloud zu verhindern.)

Weitere Informationen zur Implementierung von Authentifizierung und Autorisierung in ASP.NET MVC-Webprojekten finden Sie in den folgenden Ressourcen:

-   [Authentifizierung und Autorisierung in der ASP.NET-Web-API](http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api)
-   [Music Store Teil 7: Mitgliedschaft und Autorisierung](http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7)

**Hinweis**: Ursprünglich war ein Mechanismus zur Sicherung der Web-API-Dienstmethode mit einem gemeinsamen geheimen Schlüssel geplant, allerdings wurde diese Funktion nicht rechtzeitig zur ersten Version fertig. Aus diesem Grund enthält das dritte Lernprogramm keine Anleitung zur Erstellung des Web-API-Controllers für den Abonnementprozess. Wir hoffen, dass die nächste Version dieses Lernprogramms eine Anleitung zur Implementierung eines sicheren Abonnementprozesses enthält. Bis dahin können Sie die Anwendung testen, indem Sie Listenabonnements für E-Mail-Adressen über die Administrator-Webseiten erstellen.

Nächste SchritteNächste Schritte
--------------------------------

Im [nächsten Lernprogramm](/de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/) werden Sie das Beispielprojekt herunterladen, Ihre Entwicklungsumgebung konfigurieren, das Projekt für Ihre Umgebung konfigurieren und anschließend lokal und in der Cloud testen. In den weiteren Lernprogrammen lernen Sie, das Projekt von Grund auf zu erstellen.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie am Ende des [letzten Lernprogramms in dieser Serie](/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

[Lernprogramm 2](/de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)


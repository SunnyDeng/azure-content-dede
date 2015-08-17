<properties
   pageTitle="Erste Schritte mit Azure Data Catalog"
   description="Umfassendes Lernprogramm zu den Szenarien und Funktionen von Azure Data Catalog"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>

<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>


# Erste Schritte mit Azure Data Catalog

Dieser Artikel enthält ein umfassendes Lernprogramm zu den Szenarien und Funktionen der öffentlichen Preview-Version von Azure Data Catalog. Nachdem Sie sich für die Preview-Version registriert haben, können Sie anhand dieser Schritte ein Data Catalog-Element erstellen und Datenquellen registrieren, mit Anmerkungen versehen und ermitteln.

## Voraussetzungen für das Lernprogramm

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-	**Azure-Abonnement:** Wenn Sie kein Abonnement besitzen, können Sie in wenigen Minuten ein kostenloses Testkonto einrichten. Im Artikel [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) finden Sie Details.
-	**Azure Active Directory:** In Azure Data Catalog wird [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) für die Identitäts- und Zugriffsverwaltung verwendet.
-	**Datenquellen:** Azure Data Catalog enthält Funktionen zur Ermittlung von Datenquellen, und für das Lernprogramm benötigen Sie Zugriff auf eine oder mehrere Datenquellen. Das Lernprogramm wurde unter Verwendung der Adventure Works-Beispieldatenbanken geschrieben. Sie können aber auch beliebige andere unterstützte Datenquellen verwenden, wenn Sie lieber mit Daten arbeiten möchten, die Ihnen vertraut sind und die für Ihre Position relevant sind.

## Übung 1: Installieren der Adventure Works-Beispieldatenbank

In dieser Übung installieren Sie die Adventure Works-Beispieldaten für das SQL Server-Datenbankmodul und SQL Server Analysis Services (Mehrdimensional). Diese Beispiele werden in den folgenden Übungen verwendet.

> [AZURE.NOTE]Diese Übung ist optional. Die restlichen Übungen im Lernprogramm sind so geschrieben, dass auf die Adventure Works-Beispieldatenbanken verwiesen wird. Sie können die Übung aber auch überspringen und stattdessen Ihre eigenen Datenquellen verwenden. Unten sind die Schritte zum Installieren von Adventure Works angegeben.

### Installieren der Adventure Works 2014 OLTP- und Data Warehouse-Datenbanken

Die Adventure Works OLTP-Datenbank unterstützt standardmäßige Onlinetransaktionsverarbeitung-Szenarios für einen fiktiven Fahrradhersteller (Adventure Works Cycles), z. B. für die Bereiche Fertigung, Vertrieb und Einkauf. Anhand der Adventure Works DW-Datenbank wird veranschaulicht, wie Sie ein Data Warehouse erstellen.

Die Datenbanken befinden sich unter http://msftdbprodsamples.codeplex.com/ und können installiert werden, indem die Schritte unter [So wird‘s gemacht: Installieren der Adventure Works 2014-Beispieldatenbanken] ausgeführt werden.

In dieser Übung haben Sie die Adventure Works-Beispieldatenbanken installiert, die in den restlichen Übungen verwendet werden. Wenn Sie diese Übung übersprungen haben und Datenquellen Ihres eigenen Unternehmens verwenden, sollten Sie darauf vorbereitet sein, sich Namen, Tags und andere Metadaten zu merken.

## Übung 2: Registrieren von Datenquellen

In dieser Übung verwenden Sie das Azure Data Catalog-Registrierungstool, um Ihre Datenquellen für den Katalog zu registrieren. Die Registrierung ist der Prozess zum Extrahieren wichtiger struktureller Metadaten – z. B. Namen, Typen und Orte – aus der Datenquelle und der darin enthaltenen Ressource sowie zum Kopieren dieser Metadaten in den Katalog. Die Datenquellen und ihre Daten bleiben an ihrem Ort, aber die Metadaten werden vom Katalog verwendet, um sie leichter ermittelbar und verständlich zu machen.

### So registrieren Sie eine Datenquelle

1.	Melden Sie sich am Azure Data Catalog-Portal an.

    ![register1][1]

2.	Führen Sie einen Bildlauf nach unten aus, und klicken Sie auf **Daten veröffentlichen**.

    ![register2][2]
3.	Klicken Sie auf **Anwendung starten**.
4.	Klicken Sie auf der Seite **Willkommen** auf **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein.
5.	Klicken Sie auf der Seite **Microsoft Azure Data Catalog** auf **SQL Server**.

    ![register3][3]
6.	Geben Sie Ihren **Servernamen** ein, und klicken Sie auf **VERBINDEN**.
7.	Auf der nächsten Seite registrieren Sie die Metadaten der Datenquelle. In diesem Beispiel registrieren Sie **Product**-Objekte aus dem Production-Namespace von AdventureWorks. Gehen Sie wie folgt vor:
    
    a. Klicken Sie in der Hierarchiestruktur auf **Production**.

    b. Führen Sie STRG+Klicken auf „Product“, „ProductCategory“, „ProductDescription“ und „ProductPhoto“ aus.

    ![register4][4]

    c. Klicken Sie auf den Pfeil zum Verschieben der ausgewählten Elemente (**>**). Alle Product-Objekte werden in die Liste **Für Registrierung** verschoben.

    ![register5][5]

    d. **Optional:** Sie können die Funktionen **Vorschau einfügen** und **Datenquellenexperten hinzufügen** verwenden.

    e. Geben Sie unter **Tags hinzufügen** eine Beschreibung und ein Foto ein. Hiermit werden Suchtags für diese Datenressourcen hinzugefügt. Tags sind eine hervorragende Möglichkeit, Benutzer beim Suchen nach einer registrierten Datenquelle zu unterstützen.
 
    f. Klicken Sie auf **REGISTRIEREN**. Azure Data Catalog registriert Ihre ausgewählten Objekte. In dieser Übung werden die ausgewählten Objekte von Adventure Works registriert.

    ![register6][6]

    g. Klicken Sie zum Anzeigen der registrierten Datenquellenobjekte auf **Portal anzeigen**. Im Azure Data Catalog-Portal können Sie Datenquellenobjekte auf **Kacheln** oder in einer **Liste** anzeigen.

    ![register7][7]

In dieser Übung haben Sie Objekte aus der Adventure Works-Beispieldatenbank registriert, damit sie von Benutzern in Ihrem Unternehmen leicht ermittelt werden können. In der nächsten Übung erfahren Sie, wie Sie registrierte Datenressourcen ermitteln.

## Übung 3: Ermitteln von registrierten Datenressourcen

In dieser Übung verwenden Sie das Azure Data Catalog-Portal, um registrierte Datenressourcen zu ermitteln und die dazugehörigen Metadaten anzuzeigen. Azure Data Catalog enthält mehrere Tools zum Ermitteln von Datenressourcen, z. B. eine einfache Schlüsselwortsuche, interaktive Filter und eine erweiterte Suchsyntax für Poweruser.

### So ermitteln Sie registrierte Datenressourcen

**Azure Data Catalog** verfügt über eine einfache, leistungsstarke Suchsyntax, mit deren Hilfe Sie leicht Abfragen erstellen können, die zum Zurückgeben der erforderlichen Daten für Benutzer dienen. Ausführliche Informationen zu **Azure Data Catalog** finden Sie unter „Search-Syntaxreferenz“.

**Azure Data Catalog** enthält die folgenden Suchoptionen:

- Schlüsselwortsuche
- Filter
- Erweiterte Suche

Sie können auch genauer einstellen, welche Datenressourcen angezeigt werden sollen. **Azure Data Catalog** verfügt über die folgenden Anzeigeoptionen:

- Eigenschaften anzeigen
- Spalten anzeigen
- Vorschau anzeigen

In diesem Beispiel verwenden Sie eine Schlüsselwortsuche. Die **Azure Data Catalog**-Suche enthält verschiedene Abfrageverfahren. In diesem Beispiel wird eine Suchabfrage vom Typ **Gruppierung** verwendet.

**Abfrageverfahren** <table><tr><td><b>Verfahren</b></td><td><b>Zweck</b></td><td><b>Beispiel</b></td></tr><tr><td>Bereichsdefinition für Eigenschaften</td><td>Es werden nur Datenquellen zurückgegeben, bei denen der Suchbegriff in der angegebenen Eigenschaft übereinstimmt.</td><td>name:product</td></tr><tr><td>Logische Operatoren</td><td>Eine Suche wird mit booleschen Operatoren erweitert oder eingegrenzt, wie im Abschnitt „Boolesche Operatoren“ auf dieser Seite beschrieben.</td><td>finance NOT corporate</td></tr><tr><td>Gruppierung mit Klammern</td><td>Klammern werden zum Gruppieren von Teilen der Abfrage verwendet, um eine logische Isolierung zu erzielen, besonders in Verbindung mit booleschen Operatoren.</td><td>name:product AND (tags:illustration OR tags:photo)</td></tr><tr><td>Vergleichsoperatoren</td><td>Vergleiche (mit Ausnahme von „Equality“) werden für Eigenschaften verwendet, die über numerische und Datumsdatentypen verfügen.</td><td>creationTime:&gt;11/05/14</td></tr></table>

In diesem Beispiel führen Sie eine Suche vom Typ **Gruppierung** für Datenressourcen durch, bei denen der Name mit dem Produkt übereinstimmt und Tags mit der Abbildung oder dem Foto übereinstimmen.

1.	Melden Sie sich am **Azure Data Catalog**-Portal an.
2.	Klicken Sie auf **Ermitteln**.
3.	Geben Sie im Feld **Suche** eine Abfrage vom Typ **Gruppierung** ein: (tags:description OR tags:photo). 
4.	Klicken Sie auf das Suchsymbol, oder drücken Sie die EINGABETASTE. In **Azure Data Catalog** werden Datenressourcen für diese Suchabfrage angezeigt.
    
    ![Suchen][8]

In dieser Übung haben Sie das **Azure Data Catalog**-Portal verwendet, um Datenressourcen zu ermitteln und anzuzeigen, die für den Katalog registriert wurden.

## Übung 4: Versehen von registrierten Datenquellen mit Anmerkungen

In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um Datenressourcen mit Anmerkungen zu versehen, die im Katalog bereits registriert wurden. Die von Ihnen angegebenen Anmerkungen dienen als Ergänzung und Erweiterung der strukturellen Metadaten, die während der Registrierung aus der Datenquelle extrahiert wurden, und führen dazu, dass die Datenressourcen noch besser ermittelbar und verständlich sind. Da jeder **Data Catalog**-Benutzer eigene Anmerkungen angeben kann, ist es für alle beteiligten Benutzer leicht, die Daten zu teilen.

### So versehen Sie Datenressourcen mit Anmerkungen

1.	Melden Sie sich am **Azure Data Catalog**-Portal an.
2.	Klicken Sie auf **Ermitteln**.
3.	Wählen Sie eine oder mehrere **Datenressourcen** aus. Wählen Sie in diesem Beispiel **ProductPhoto** aus, und geben Sie „Produktfotos für Marketingmaterial“ ein.
4.	Geben Sie eine **Beschreibung** ein, damit andere Benutzer die Daten besser ermitteln können und verstehen, warum und wie die ausgewählte Datenressource verwendet werden kann. Geben Sie beispielsweise „Produktbilder“ ein. Sie können auch mehrere Tags hinzufügen und Spalten anzeigen.
5.	Jetzt können Sie das Suchen und Filtern ausprobieren, um Datenressourcen anhand der beschreibenden Metadaten zu ermitteln, die Sie dem Katalog hinzugefügt haben.

    ![Mit Anmerkungen versehen][9]

In dieser Übung haben Sie registrierten Datenressourcen beschreibende Informationen hinzugefügt, damit Katalogbenutzer Datenquellen anhand von verständlichen Begriffen ermitteln können.

## Übung 5: Crowdsourcing für Metadaten

In dieser Übung arbeiten Sie mit einem anderen Benutzer zusammen, um den Datenressourcen im Katalog Metadaten hinzuzufügen. Beim Crowdsourcing-Ansatz von Azure Data Catalog für Anmerkungen können Benutzer Tags, Beschreibungen und andere Metadaten hinzufügen, damit alle an einer Datenressource und ihrer Nutzung beteiligten Benutzer ihre Sichtweise erfassen und für andere Personen zur Verfügung stellen können.

> [AZURE.NOTE]Es ist kein Problem, wenn bei Ihnen gerade kein anderer Benutzer verfügbar ist, der mit Ihnen in diesem Lernprogramm zusammenarbeiten kann. Jeder Benutzer, der auf den Datenkatalog zugreift, kann eigene Angaben hinzufügen, wenn er dies möchte. Mit diesem Crowdsourcing-Ansatz für Metadaten können der Inhalt des Katalogs und der Umfang der Katalogmetadaten im Laufe der Zeit ausgebaut werden.

### So können Sie Metadaten für Datenressourcen per Crowdsourcing beschaffen

Bitten Sie einen Kollegen, die obige Übung **Versehen von registrierten Datenquellen mit Anmerkungen** durchzuführen. Nachdem Ihr Kollege einer Datenressource eine Beschreibung hinzugefügt hat, z. B. ProductPhoto, werden mehrere Anmerkungen angezeigt.

In dieser Übung haben Sie die Funktionen von Azure Data Catalog zum Crowdsourcing von Metadaten untersucht, wobei jeder Katalogbenutzer die selbst ermittelten Datenressourcen mit Anmerkungen versehen kann. Übung: Herstellen einer Verbindung mit Datenquellen In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um mit Microsoft Excel eine Verbindung mit Datenquellen herzustellen.

> [AZURE.NOTE]Es ist wichtig zu beachten, dass Benutzer mit **Azure Data Catalog** keinen Zugriff auf die eigentliche Datenquelle erhalten – es wird Benutzern lediglich erleichtert, sie zu ermitteln und zu verstehen. Wenn Benutzer eine Verbindung mit einer Datenquelle herstellen, werden von der gewählten Clientanwendung deren Windows-Anmeldeinformationen verwendet, oder Benutzer werden bei Bedarf zur Eingabe ihrer Anmeldeinformationen aufgefordert. Falls dem Benutzer bisher noch kein Zugriff auf die Datenquelle gewährt wurde, muss dies erfolgen, bevor er die Verbindung herstellen kann.

### So stellen Sie über Excel eine Verbindung mit einer Datenquelle her

1.	Melden Sie sich am **Azure Data Catalog**-Portal an.
2.	Klicken Sie auf **Ermitteln**.
3.	Wählen Sie eine Datenressource aus. Wählen Sie in diesem Beispiel „ProductCategory“ aus.
4.	Wählen Sie **Öffnen in** > **Excel**.

    ![connect1][10]
5.	Klicken Sie im Fenster **Sicherheitshinweis für Microsoft Excel** auf **Aktivieren**.
6.	Öffnen Sie die Datei **ProductCategory.odc**.
7.	Die Datenquelle wird in Excel geöffnet.

    ![connect2][11]

In dieser Übung haben Sie Verbindungen mit Datenquellen hergestellt, die per Azure Data Catalog ermittelt wurden. Mit dem **Azure Data Catalog**-Portal können Benutzer Verbindungen direkt mit Clientanwendungen herstellen, die in das Menü **Öffnen in…** integriert sind. Außerdem können Benutzer eine Verbindung mit einer beliebigen Anwendung herstellen, indem sie die Speicherortinformationen der Verbindung in den Ressourcenmetadaten verwenden.

## Übung 6: Entfernen von Datenquellenmetadaten

In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um Vorschaudaten aus registrierten Datenressourcen zu entfernen und Datenressourcen aus dem Katalog zu löschen.

> [AZURE.NOTE]Das Standardverhalten des Katalogs besteht darin, das Registrieren beliebiger Datenquellen für alle Benutzer zuzulassen und Benutzern auch das Löschen aller registrierten Datenressourcen zu ermöglichen. Die Verwaltungsfunktionen der **Standard Edition von Azure Data Catalog** umfassen zusätzliche Optionen zum Festlegen des Besitzes von Ressourcen, Einschränken der Ermittlung von Ressourcen und Einschränken des Löschens von Ressourcen.

In **Azure Data Catalog** können Sie die Vorschau für das Löschen einzelner oder mehrerer Ressourcen entfernen.

### So löschen Sie mehrere Datenressourcen

1.	Melden Sie sich am **Azure Data Catalog**-Portal an.
2.	Klicken Sie auf **Ermitteln**.
3.	Wählen Sie eine oder mehrere Datenressourcen aus.
4.	Klicken Sie auf **Löschen**.

In dieser Übung haben Sie die registrierten Datenressourcen aus dem Katalog entfernt.

## Übung 7: Verwalten von registrierten Datenquellen

In dieser Übung verwenden Sie die Verwaltungsfunktionen von **Azure Data Catalog**, um Datenressourcen in Besitz zu nehmen und zu steuern, was Benutzer ermitteln können und wie sie die Ressourcen verwalten können.

Hinweis: Die in dieser Übung beschriebenen Verwaltungsfunktionen sind nur in der Standard Edition von Azure Data Catalog verfügbar, nicht in der Free Edition. In **Azure Data Catalog** können Sie Datenressourcen in Besitz nehmen, Datenressourcen Mitbesitzer hinzufügen und die Sichtbarkeit von Datenressourcen festlegen.

### So nehmen Sie Datenressourcen in Besitz und schränken die Sichtbarkeit ein

1.	Melden Sie sich am **Azure Data Catalog**-Portal an.
2.	Klicken Sie auf **Ermitteln**.
3.	Wählen Sie eine oder mehrere Datenressourcen aus.
4.	Klicken Sie im Bereich **Eigenschaften** unter **Verwaltung** auf **In Besitz nehmen**.
5.	Klicken Sie zum Einschränken der Sichtbarkeit auf **Besitzer und diese Benutzer**.

    ![Besitz][12]

In dieser Übung haben Sie die Verwaltungsfunktionen des Katalogs kennengelernt und die Sichtbarkeit ausgewählter Datenressourcen eingeschränkt.

## Zusammenfassung

In diesem Lernprogramm haben Sie wichtige Funktionen der Preview-Version von **Azure Data Catalog** kennengelernt, z. B. das Registrieren, Ermitteln und Verwalten von Datenquellen sowie das Versehen mit Anmerkungen. Nachdem Sie das Lernprogramm abgeschlossen haben, ist es an der Zeit, mit der Verwendung zu beginnen. Sie können noch heute starten, indem Sie die Datenquellen registrieren, die Sie und Ihr Team verwenden, und indem Sie Kollegen zur Nutzung des Katalogs einladen.


<!--Image references-->
[1]: ./media/data-catalog-get-started/register1.png
[2]: ./media/data-catalog-get-started/register2.png
[3]: ./media/data-catalog-get-started/register3.png
[4]: ./media/data-catalog-get-started/register4.png
[5]: ./media/data-catalog-get-started/register5.png
[6]: ./media/data-catalog-get-started/register6.png
[7]: ./media/data-catalog-get-started/register7.png
[8]: ./media/data-catalog-get-started/search.png
[9]: ./media/data-catalog-get-started/annotate.png
[10]: ./media/data-catalog-get-started/connect1.png
[11]: ./media/data-catalog-get-started/connect2.png
[12]: ./media/data-catalog-get-started/ownership.png

<!---HONumber=August15_HO6-->
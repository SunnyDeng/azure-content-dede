<properties
   pageTitle="Erste Schritte mit Azure Data Catalog"
   description="Umfassendes Lernprogramm zu den Szenarios und Funktionen von Azure Data Catalog"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/03/2016"
   ms.author="derrickv"/>

# Erste Schritte mit Azure Data Catalog

Dieser Artikel enthält ein umfassendes Tutorial zu den Szenarien und Funktionen der öffentlichen Preview-Version von **Azure Data Catalog**. Nachdem Sie sich für die Preview-Version registriert haben, können Sie anhand dieser Schritte ein Data Catalog-Element erstellen und Datenquellen registrieren, mit Anmerkungen versehen und ermitteln.

## Voraussetzungen für das Lernprogramm

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement:** Wenn Sie kein Abonnement besitzen, können Sie in wenigen Minuten ein kostenloses Testkonto einrichten. Details hierzu finden Sie unter [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Azure Active Directory:** In Azure Data Catalog wird [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für die Identitäts- und Zugriffsverwaltung verwendet.
- **Datenquellen:** Azure Data Catalog verfügt über Funktionen zum Ermitteln von Datenquellen. Für das Tutorial wird die Adventure Works-Beispieldatenbank verwendet. Sie können aber auch beliebige andere unterstützte Datenquellen verwenden, wenn Sie lieber mit Daten arbeiten möchten, die Ihnen vertraut und die für Ihre Rolle relevant sind. Eine Liste mit den unterstützten Datenquellen finden Sie unter [Unterstützte Datenquellen](data-catalog-dsr.md).

Wir beginnen, indem wir die Adventure Works-Beispieldatenbank installieren.

## Übung 1: Installieren der Adventure Works-Beispieldatenbank

In dieser Übung installieren Sie die Adventure Works-Beispieldatenbank für das SQL Server-Datenbankmodul, das in den folgenden Übungen verwendet wird.

### Installieren der Adventure Works 2014 OLTP-Datenbank

Die Adventure Works-Datenbank unterstützt standardmäßige Onlinetransaktionsverarbeitung-Szenarien für einen fiktiven Fahrradhersteller (Adventure Works Cycles), z. B. für die Bereiche Produkte, Vertrieb und Einkauf. In diesem Tutorial registrieren Sie Informationen zu Produkten in **Azure Data Catalog**.

Hier wird nun beschrieben, wie Sie die Adventure Works-Beispieldatenbank installieren.

Zum Installieren der Adventure Works-Beispieldatenbank können Sie eine AdventureWorks2014-Sicherung wiederherstellen, die sich auf CodePlex unter [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) befindet. Eine Möglichkeit zum Wiederherstellen der Datenbank ist das Ausführen eines T-SQL-Skripts in SQL Server Management Studio.

**Installieren der Adventure Works-Beispieldatenbank mit einem T-SQL-Skript**

1.	Erstellen Sie einen Ordner mit dem Namen „C:\\DataCatalog\_GetStarted“. Wenn Sie einen anderen Ordnernamen verwenden, sollten Sie sicherstellen, dass Sie unten im T-SQL-Skript den Pfad ändern.
2.	Laden Sie [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) herunter.
3.	Extrahieren Sie die Datei „Adventure Works 2014 Full Database Backup.zip“ in den Ordner „C:\\DataCatalog\_GetStarted“. Im Skript unten wird vorausgesetzt, dass sich die Sicherungsdatei unter „C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak“ befindet.
4.	Klicken Sie in **SQL Server Management Studio** in der Symbolleiste **Standard** auf **Neue Abfrage**.
5.	Führen Sie den folgenden T-SQL-Code im Abfragefenster aus.

**Führen Sie dieses Skript zum Installieren der Adventure Works 2014-Datenbank aus.**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

Als Alternative zum Ausführen eines T-SQL-Skripts können Sie die Datenbank auch mit SQL Server Management Studio wiederherstellen. Weitere Informationen finden Sie unter [Wiederherstellen einer Datenbanksicherung (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx).

In dieser Übung haben Sie die Adventure Works-Beispieldatenbank installiert, die in den restlichen Übungen verwendet wird. In der nächsten Übung erfahren Sie, wie Sie **Azure Data Catalog**-Datenquellen aus Tabellen in der Adventure Works-Beispieldatenbank registrieren.

## Übung 2: Registrieren von Datenquellen

In dieser Übung verwenden Sie das **Azure Data Catalog**-Registrierungstool, um Adventure Works-Datenquellen für den Katalog zu registrieren. Die Registrierung ist der Prozess zum Extrahieren wichtiger struktureller Metadaten – z. B. Namen, Typen und Orte – aus der Datenquelle und des darin enthaltenen Assets sowie zum Kopieren dieser Metadaten in den Katalog. Die Datenquellen und ihre Daten bleiben an ihrem Ort, aber die Metadaten werden vom Katalog verwendet, um sie leichter ermittelbar und verständlich zu machen.

### So registrieren Sie eine Datenquelle

1.	Navigieren Sie zu https://azure.microsoft.com/services/data-catalog, und klicken Sie auf **Erste Schritte**.
2.	Melden Sie sich am **Azure Data Catalog**-Portal an, und klicken Sie auf **Daten veröffentlichen**.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Klicken Sie auf **Anwendung starten**.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Klicken Sie auf der Seite **Willkommen** auf **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein.
5. Doppelklicken Sie auf der Seite **Microsoft Azure Data Catalog** auf **SQL Server**, oder klicken Sie auf **SQL Server** und dann auf **Weiter**.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Geben Sie die SQL Server-Verbindungseigenschaften für AdventureWorks2014 ein (siehe Beispiel unten), und klicken Sie auf **VERBINDEN**.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	Auf der nächsten Seite registrieren Sie die Metadaten der Datenquelle. In diesem Beispiel registrieren Sie **Production/Product**-Objekte aus dem Production-Namespace von AdventureWorks. Gehen Sie wie folgt vor:

    a. Klicken Sie in der Struktur **Serverhierarchie** auf **Production**.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Führen Sie STRG+Klicken auf „Product“, „ProductCategory“, „ProductDescription“ und „ProductPhoto“ aus.

    c. Klicken Sie auf den Pfeil zum Verschieben der ausgewählten Elemente (**>**). Alle ausgewählten Product-Objekte werden in die Liste **Zu registrierende Objekte** verschoben.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. Geben Sie unter **Tags hinzufügen** eine Beschreibung und ein Foto ein. Hiermit werden Suchtags für diese Datenassets hinzugefügt. Tags sind eine hervorragende Möglichkeit, Benutzer beim Suchen nach einer registrierten Datenquelle zu unterstützen.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e. **Optional:** Sie können die Funktionen **Vorschau einfügen** und **Datenquellenexperten hinzufügen** verwenden.

    f. Klicken Sie auf **REGISTRIEREN**. **Azure Data Catalog** registriert Ihre ausgewählten Objekte. In dieser Übung werden die ausgewählten Objekte von Adventure Works registriert.

    g. Klicken Sie zum Anzeigen der registrierten Datenquellenobjekte auf **Portal anzeigen**. Im **Azure Data Catalog**-Portal können Sie Datenquellenobjekte auf **Kacheln** oder in einer **Liste** anzeigen.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

In dieser Übung haben Sie Objekte aus der Adventure Works-Beispieldatenbank registriert, damit sie von Benutzern in Ihrem Unternehmen leicht ermittelt werden können. In der nächsten Übung erfahren Sie, wie Sie registrierte Datenassets ermitteln.

## Übung 3: Ermitteln von registrierten Datenassets

In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um registrierte Datenassets zu ermitteln und die dazugehörigen Metadaten anzuzeigen. **Azure Data Catalog** enthält mehrere Tools zum Ermitteln von Datenassets, z. B. eine einfache Schlüsselwortsuche, interaktive Filter und eine erweiterte Suchsyntax für Poweruser.

### So ermitteln Sie registrierte Datenassets

**Azure Data Catalog** verfügt über eine einfache, leistungsstarke Suchsyntax, mit deren Hilfe Sie leicht Abfragen erstellen können, die zum Zurückgeben der erforderlichen Daten für Benutzer dienen. Ausführliche Informationen zur **Azure Data Catalog**-Suche finden Sie unter [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (Referenz zur Data Catalog-Suchsyntax).

**Azure Data Catalog** enthält die folgenden Suchoptionen:

- Schlüsselwortsuche
- Filter
- Erweiterte Suche

Sie können auch genauer einstellen, welche Datenassets angezeigt werden sollen. **Azure Data Catalog** verfügt über die folgenden Anzeigeoptionen:

- Eigenschaften anzeigen
- Spalten anzeigen
- Vorschau anzeigen

In diesem Beispiel verwenden Sie eine Schlüsselwortsuche. Die **Azure Data Catalog**-Suche enthält verschiedene Abfrageverfahren. In diesem Beispiel wird eine Suchabfrage vom Typ **Gruppierung** verwendet.

**Abfragetechniken**

|Technik|Verwenden|Beispiel
|---|---|---
|Eigenschaftsbasierte Suche|Es werden nur Datenquellen zurückgegeben, bei denen sich für den Suchbegriff in der angegebenen Eigenschaft eine Übereinstimmung ergibt.|name:product
|Logische Operatoren|Eine Suche kann mit booleschen Operatoren erweitert oder eingegrenzt werden, wie im Abschnitt „Boolesche Operatoren“ auf dieser Seite beschrieben.|Finanzen NOT Unternehmen
|Gruppierung mit Klammern|Teile der Abfrage werden mithilfe von Klammern gruppiert, um eine logische Isolierung zu erzielen (insbesondere in Verbindung mit booleschen Operatoren).|name:product AND (tags:illustration OR tags:photo)
|Vergleichsoperatoren|Für Eigenschaften mit numerischen Datentypen und Datumsdatentypen können andere Vergleiche als Gleichheit verwendet werden.|creationTime:>11/05/14

In diesem Beispiel führen Sie eine Suche vom Typ **Gruppierung** für Datenassets durch, bei denen der Name mit dem Produkt übereinstimmt und Tags mit der Abbildung oder dem Foto übereinstimmen.

1. Navigieren Sie zu https://azure.microsoft.com/services/data-catalog, klicken Sie auf **Erste Schritte**, und melden Sie sich am **Azure Data Catalog**-Portal an.
2. Geben Sie im Feld **Data Catalog durchsuchen** eine **Gruppierung** wie folgt ein: query: (tags:description OR tags:photo).
3. Klicken Sie auf das Suchsymbol, oder drücken Sie die EINGABETASTE. In **Azure Data Catalog** werden Datenassets für diese Suchabfrage angezeigt.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

In dieser Übung haben Sie das **Azure Data Catalog**-Portal verwendet, um Adventure Works-Datenassets, die für den Katalog registriert wurden, zu ermitteln und anzuzeigen.

<a name="annotating"/>
## Übung 4: Versehen von registrierten Datenquellen mit Anmerkungen

In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um Datenassets mit Anmerkungen zu versehen, die im Katalog bereits registriert wurden. Die von Ihnen angegebenen Anmerkungen dienen als Ergänzung und Erweiterung der strukturellen Metadaten, die während der Registrierung aus der Datenquelle extrahiert wurden, und führen dazu, dass die Datenassets viel besser ermittelbar und verständlich sind. Da jeder **Data Catalog**-Benutzer eigene Anmerkungen angeben kann, ist es für alle beteiligten Benutzer leicht, die Daten zu teilen.

### So versehen Sie Datenassets mit Anmerkungen

1. Navigieren Sie zu https://azure.microsoft.com/services/data-catalog, klicken Sie auf **Erste Schritte**, und melden Sie sich am **Azure Data Catalog**-Portal an.
2. Klicken Sie auf **Ermitteln**.
3. Wählen Sie mindestens ein **Datenasset** aus. Wählen Sie in diesem Beispiel **ProductPhoto** aus, und geben Sie „Produktfotos für Marketingmaterial“ ein.
4. Geben Sie eine **Beschreibung** ein, damit andere Benutzer die Daten besser ermitteln können und verstehen, warum und wie das ausgewählte Datenasset verwendet werden kann. Geben Sie beispielsweise „Produktbilder“ ein. Sie können auch mehrere Tags hinzufügen und Spalten anzeigen.
5. Jetzt können Sie das Suchen und Filtern ausprobieren, um Datenassets anhand der beschreibenden Metadaten zu ermitteln, die Sie dem Katalog hinzugefügt haben.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

In dieser Übung haben Sie registrierten Datenassets beschreibende Informationen hinzugefügt, damit Katalogbenutzer Datenquellen anhand von verständlichen Begriffen ermitteln können.

## Übung 5: Crowdsourcing für Metadaten

In dieser Übung arbeiten Sie mit einem anderen Benutzer zusammen, um den Datenassets im Katalog Metadaten hinzuzufügen. Beim Crowdsourcing-Ansatz von **Azure Data Catalog** für Anmerkungen können Benutzer Tags, Beschreibungen und andere Metadaten hinzufügen, damit alle an einem Datenasset und seiner Nutzung beteiligten Benutzer ihre Sichtweise erfassen und für andere Personen zur Verfügung stellen können.

> [AZURE.NOTE] Es ist kein Problem, wenn bei Ihnen gerade kein anderer Benutzer verfügbar ist, der mit Ihnen in diesem Lernprogramm zusammenarbeiten kann. Jeder Benutzer, der auf den Datenkatalog zugreift, kann eigene Angaben hinzufügen, wenn er dies möchte. Mit diesem Crowdsourcing-Ansatz für Metadaten können der Inhalt des Katalogs und der Umfang der Katalogmetadaten im Laufe der Zeit ausgebaut werden.

### So können Sie Metadaten für Datenassets per Crowdsourcing beschaffen

Bitten Sie einen Kollegen, die obige Übung [Versehen von registrierten Datenquellen mit Anmerkungen](#annotating) durchzuführen. Nachdem Ihre Kollegen einem Datenasset Beschreibungen hinzugefügt haben, z. B. ProductPhoto, werden mehrere Anmerkungen angezeigt.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

In dieser Übung haben Sie die Funktionen von **Azure Data Catalog** zum Crowdsourcing von Metadaten untersucht, wobei jeder Katalogbenutzer die selbst ermittelten Datenassets mit Anmerkungen versehen kann.

## Übung 6: Herstellen einer Verbindung mit Datenquellen

In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um über Microsoft Excel eine Verbindung mit einer Datenquelle herzustellen.

> [AZURE.NOTE] Es ist wichtig zu beachten, dass **Azure Data Catalog** Benutzern keinen Zugriff auf die eigentliche Datenquelle ermöglicht – es erleichtert Benutzern lediglich, sie zu ermitteln und zu verstehen. Wenn Benutzer eine Verbindung mit einer Datenquelle herstellen, werden von der gewählten Clientanwendung deren Windows-Anmeldeinformationen verwendet, oder Benutzer werden bei Bedarf zur Eingabe ihrer Anmeldeinformationen aufgefordert. Falls dem Benutzer bisher noch kein Zugriff auf die Datenquelle gewährt wurde, muss dies erfolgen, bevor er die Verbindung herstellen kann.

### So stellen Sie über Excel eine Verbindung mit einer Datenquelle her

1. Navigieren Sie zu https://azure.microsoft.com/services/data-catalog, klicken Sie auf **Erste Schritte**, und melden Sie sich am **Azure Data Catalog**-Portal an.
2. Klicken Sie auf **Ermitteln**.
3. Wählen Sie ein Datenasset aus. Wählen Sie in diesem Beispiel „ProductCategory“ aus.
4. Wählen Sie **Öffnen in** > **Excel** aus.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. Klicken Sie im Fenster **Microsoft Excel-Sicherheitshinweis** auf **Aktivieren**.
6. Öffnen Sie die Datei **ProductCategory.odc**.
7. Die Datenquelle wird in Excel geöffnet.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

In dieser Übung haben Sie Verbindungen mit Datenquellen hergestellt, die per **Azure Data Catalog** ermittelt wurden. Mit dem **Azure Data Catalog**-Portal können Benutzer Verbindungen direkt mit Clientanwendungen herstellen, die in das Menü **Öffnen in…** integriert sind. Außerdem können Benutzer eine Verbindung mit einer beliebigen Anwendung herstellen, indem sie die Speicherortinformationen der Verbindung in den Assetmetadaten verwenden.

## Übung 7: Entfernen von Datenquellenmetadaten

In dieser Übung verwenden Sie das **Azure Data Catalog**-Portal, um Vorschaudaten aus registrierten Datenassets zu entfernen und Datenassets aus dem Katalog zu löschen.

> [AZURE.NOTE] Das Standardverhalten des Katalogs besteht darin, das Registrieren beliebiger Datenquellen für alle Benutzer zuzulassen und Benutzern auch das Löschen aller registrierten Datenassets zu ermöglichen. Die Verwaltungsfunktionen in der **Standard Edition von Azure Data Catalog** umfassen zusätzliche Optionen zum Festlegen des Besitzes von Assets, Einschränken der Ermittlung von Assets und Einschränken des Löschens von Assets.

In **Azure Data Catalog** können Sie ein einzelnes Asset oder mehrere Assets löschen.

### So löschen Sie mehrere Datenassets

1. Navigieren Sie zu https://azure.microsoft.com/services/data-catalog, klicken Sie auf **Erste Schritte**, und melden Sie sich am **Azure Data Catalog**-Portal an.
2. Klicken Sie auf **Ermitteln**.
3. Wählen Sie mindestens ein Datenasset aus.
4. Klicken Sie auf **Löschen**.

In dieser Übung haben Sie die registrierten Datenassets aus dem Katalog entfernt.

## Übung 8: Verwalten von registrierten Datenquellen

In dieser Übung verwenden Sie die Verwaltungsfunktionen von **Azure Data Catalog**, um Datenassets in Besitz zu nehmen und zu steuern, was Benutzer ermitteln und wie sie diese Ressourcen verwalten können.

> [AZURE.NOTE] Die in dieser Übung beschriebenen Verwaltungsfunktionen sind nur in der **Standard Edition von Azure Data Catalog** verfügbar, nicht in der **Free Edition**. In **Azure Data Catalog** können Sie Datenassets in Besitz nehmen, Datenassets Mitbesitzer hinzufügen und die Sichtbarkeit von Datenassets festlegen.

### So nehmen Sie Datenassets in Besitz und schränken die Sichtbarkeit ein

1. Navigieren Sie zu https://azure.microsoft.com/services/data-catalog, klicken Sie auf **Erste Schritte**, und melden Sie sich am **Azure Data Catalog**-Portal an.
2. Klicken Sie auf **Ermitteln**.
3. Wählen Sie mindestens ein Datenasset aus.
4. Klicken Sie im Bereich **Eigenschaften** unter **Verwaltung** auf **Besitz übernehmen**.
5. Klicken Sie zum Einschränken der Sichtbarkeit auf **Besitzer und diese Benutzer**.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

In dieser Übung haben Sie die Verwaltungsfunktionen von **Azure Data Catalog** kennengelernt und die Sichtbarkeit ausgewählter Datenassets eingeschränkt.

## Zusammenfassung

In diesem Tutorial haben Sie wichtige Funktionen der Preview-Version von **Azure Data Catalog** kennengelernt, z. B. das Registrieren, Ermitteln und Verwalten von Datenquellen sowie das Versehen mit Anmerkungen. Nachdem Sie das Lernprogramm abgeschlossen haben, ist es an der Zeit, mit der Verwendung zu beginnen. Sie können noch heute starten, indem Sie die Datenquellen registrieren, die Sie und Ihr Team verwenden, und indem Sie Kollegen zur Nutzung des Katalogs einladen.

<!---HONumber=AcomDC_0309_2016-->
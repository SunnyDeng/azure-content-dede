<properties
   pageTitle="Häufig gestellte Fragen zu Azure Data Catalog"
   description="Azure Data Catalog: Häufig gestellte Fragen"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/> <tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Häufig gestellte Fragen zu Azure Data Catalog

Dieser Artikel enthält Antworten auf häufig gestellte Fragen im Zusammenhang mit dem Microsoft Azure Data Catalog-Dienst.

## F: Was ist Azure Data Catalog?

Microsoft Azure Data Catalog ist ein vollständig verwalteter Dienst, der in der Microsoft Azure-Cloud gehostet wird. Er dient als Registrierungs- und Ermittlungssystem für die Datenquellen von Unternehmen. Azure Data Catalog enthält Funktionen, mit denen alle Benutzer – von Analysten über Datenwissenschaftler bis zu Entwicklern – Datenquellen registrieren, ermitteln, verstehen und nutzen können.

## F: Welche Kundenprobleme werden mit Azure Data Catalog gelöst?

Mit Azure Data Catalog werden Probleme bei der Ermittlung von Datenquellen und „obskuren Daten“ gelöst, indem Benutzer Datenquellen von Unternehmen ermitteln und verstehen können.

## F: Was sind die Zielgruppen von Azure Data Catalog?

Azure Data Catalog enthält Funktionen für technisch versierte und andere Benutzer, z. B.:

- Datenentwickler, BI- und Analyseexperten: Personen, die für die Produktion von Daten- und Analyseinhalten zur Verwendung durch andere Benutzer verantwortlich sind
-	Data Stewards: Personen, die über die Kenntnisse zu den Daten verfügen, ihre Bedeutung kennen und über die beabsichtigte Nutzung und den Zweck Bescheid wissen
- Datennutzer: Personen, die Daten leicht ermitteln und verstehen und Verbindungen damit herstellen müssen, um ihre Arbeit mit dem Tool ihrer Wahl zu erledigen
- Zentrale IT-Abteilung: Personen, die Hunderte von Datenquellen für die Ermittlung durch geschäftliche Benutzer bereitstellen und die überwachen müssen, wie und von wem Daten verwendet werden

## F: In welchen Regionen ist Azure Data Catalog verfügbar?

Während der Preview-Phase sind Azure Data Catalog-Dienste nur in den folgenden Rechenzentren verfügbar:

- USA \(Westen\)
- USA \(Osten\)
- Westeuropa
- Australien \(Osten\)

## F: Welche Grenzwerte gelten für die Anzahl der Datenressourcen in Azure Data Catalog?

Die Free Edition von Azure Data Catalog ist auf 5.000 registrierte Datenressourcen begrenzt.

Die Standard Edition von Azure Data Catalog unterstützt bis zu 100.000 registrierte Datenressourcen.

## F: Welche Datenquellen und Ressourcentypen werden unterstützt?

Während der Preview-Phase unterstützt Azure Data Catalog derzeit relationale SQL Server-Datenbanken \(z. B. Azure SQL DB\) und SQL Server Analysis Services-Datenbanken \(mehrdimensional und tabellarisch\) sowie SQL Server Reporting Services \(nur systemeigener Modus\) und Oracle Database.

Während der Preview-Phase unterstützt Azure Data Catalog die folgenden Ressourcentypen:

- SQL Server-Tabelle
- SQL Server-Ansicht
- SQL Server Analysis Services-Dimension
- SQL Server Analysis Services-Measure
- SQL Server Analysis Services-KPI 
- SQL Server Analysis Services-Tabelle
- SQL Server Reporting Services-Bericht  
- Oracle Database-Tabelle
- Oracle Database-Ansicht

## F: Wie fordere ich die Unterstützung für eine andere Datenquelle an?

Für Anfragen zu Features und anderes Feedback können Sie das [Azure Data Catalog-Forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) verwenden.
 
## F: Wie beginne ich mit der Nutzung von Azure Data Catalog?

Befolgen Sie am besten die Anleitung unter „Erste Schritte mit Data Catalog“. Dieser Artikel enthält eine umfassende Vorstellung der Funktionen, die in der öffentlichen Preview-Version enthalten sind.

## F: Wie registriere ich meine Daten?

Starten Sie zum Registrieren Ihrer Daten in Azure Data Catalog das Azure Data Catalog-Registrierungstool, indem Sie im Azure Data Catalog-Portal den Bereich „Veröffentlichen“ verwenden. Melden Sie sich in der Azure Data Catalog-Veröffentlichungsanwendung mit denselben Anmeldeinformationen wie für das Azure Data Catalog-Portal an, und wählen Sie die Datenquelle und die speziellen Ressourcen aus, die Sie registrieren möchten.

## F: Welche Eigenschaften werden für Datenressourcen extrahiert, die registriert werden?

Die spezifischen Eigenschaften variieren je nach Datenquelle, aber im Allgemeinen werden mit dem Azure Data Catalog-Veröffentlichungsdienst die folgenden Informationen extrahiert:

- Ressourcenname
- Ressourcentyp
- Ressourcenbeschreibung
- Namen von Attributen/Spalten 
- Datentypen von Attributen/Spalten
- Beschreibung von Attributen/Spalten

> [AZURE.IMPORTANT]Von Azure Data Catalog werden Ihre Daten nicht in die Cloud \(cloudAzure\) verschoben oder kopiert. Beim Registrieren von Ressourcen einer Datenquelle werden die Metadaten der Ressource nach Azure kopiert, aber die eigentlichen Daten verbleiben am vorhandenen Speicherort der Datenquelle. Die einzige Ausnahme von dieser Regel ist, wenn ein Benutzer sich beim Registrieren von Ressourcen zum Hochladen von Vorschaudatensätzen entscheidet. In diesem Fall werden bis zu 20 Datensätze aus jeder Ressource kopiert und als Momentaufnahme in **Azure Data Catalog** gespeichert.

<br/>

> [AZURE.NOTE]Für Datenquellen wie SQL Server Analysis Services, die über eine sehr gute **Description**-Eigenschaft verfügen, wird dieser Eigenschaftswert von der **Azure Data Catalog**-Veröffentlichungsanwendung extrahiert. Für relationale SQL Server-Datenbanken ohne sehr gute **Description**-Eigenschaft extrahiert die **Azure Data Catalog**-Veröffentlichungsanwendung den Wert für Objekte und Spalten aus der erweiterten ms\_description-Eigenschaft. Weitere Informationen finden Sie bei TechNet unter [Verwenden von erweiterten Eigenschaften für Datenbankobjekte](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## F: Wie lange sollte es dauern, bis neu registrierte Ressourcen in Azure Data Catalog angezeigt werden?

Nach dem Registrieren von Ressourcen mit **Azure Data Catalog** kann es 5 bis 10 Sekunden dauern, bevor sie im **Azure Data Catalog**-Portal angezeigt werden.

## F: Wie kann ich die Metadaten für meine registrierten Datenressourcen mit Anmerkungen versehen und erweitern?

Die einfachste Möglichkeit zum Angeben von Metadaten für registrierte Ressourcen ist das Auswählen der Ressource im **Azure Data Catalog**-Portal und das anschließende Eingeben der Metadatenwerte im Eigenschaftenbereich oder Schemabereich für das ausgewählte Objekt.

Sie können auch während des Registrierungsvorgangs einige Metadaten angeben, z. B. Experten und Tags. Die im **Azure Data Catalog**-Veröffentlichungsdienst angegebenen Werte gelten für alle Ressourcen, die zum jeweiligen Zeitpunkt registriert werden. Um die zuletzt registrierten Objekte im Portal zum Hinzufügen zusätzlicher Anmerkungen anzuzeigen, wählen Sie auf dem letzten Bildschirm der **Azure Data Catalog**-Veröffentlichungsanwendung die Schaltfläche **Portal anzeigen**.

## F: Wie lösche ich meine registrierten Datenobjekte?

Sie können ein Objekt aus **Azure Data Catalog** löschen, indem Sie das Objekt im Portal auswählen und dann auf die Schaltfläche **Löschen** klicken. Die Metadaten des Objekts werden aus **Azure Data Catalog** entfernt, aber dies wirkt sich nicht auf die zugrunde liegende Datenquelle aus.

## F: Was ist ein Experte?

Ein Experte ist eine Person, die über fundiertes Wissen zu einem Datenobjekt verfügt. Ein Objekt kann mehrere Experten haben. Ein Experte muss nicht unbedingt der Besitzer eines Objekts sein. Es handelt sich lediglich um eine Person, die weiß, wie die Daten verwendet werden können und sollten.

## F: Wie lautet die Vereinbarung zum Servicelevel \(SLA\) für die Preview-Version?

Während der Preview-Phase für **Azure Data Catalog** gilt keine besondere Vereinbarung zum Servicelevel.

## F: Wie stelle ich Informationen für das Azure Data Catalog-Team bereit, wenn Probleme auftreten?

Nutzen Sie das **Azure Data Catalog**-Forum, um Probleme zu melden, Informationen bereitzustellen und Fragen zu stellen. Sie finden das Forum unter http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409.

##F: Funktioniert Azure Data Catalog auch mit anderen Datenquellen, an denen ich interessiert bin? 
Wir arbeiten aktiv daran, **Azure Data Catalog** weitere Datenquellen hinzuzufügen. Wenn Sie sich für eine bestimmte Datenquelle Unterstützung wünschen, können Sie dies im [Azure Data Catalog-Forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) vorschlagen \(oder eine ggf. bereits vorhandene Anfrage unterstützen\).

## F: Welche Verbindung besteht zwischen Azure Data Catalog und Data Catalog in Power BI für Office 365?

Sie können sich **Azure Data Catalog** als Weiterentwicklung von Data Catalog vorstellen. **Azure Data Catalog** verfügt über ähnliche Funktionen für die Veröffentlichung und Ermittlung von Datenquellen, ist aber auf weiter gefasste Szenarios ausgerichtet und nicht von Office 365 abhängig. Kurz nach der allgemeinen Verfügbarkeit von Azure Data Catalog werden die beiden Kataloge zu einem einzelnen Dienst zusammengefasst.

## F: Welche Berechtigungen benötigt ein Benutzer, um Ressourcen unter Azure Data Catalog registrieren zu können?

Der Benutzer, der das **Azure Data Catalog**-Registrierungstool ausführt, benötigt Berechtigungen für die Datenquelle, mit denen er die Metadaten aus der Quelle auslesen kann. Wenn der Benutzer auch die Einbindung einer Vorschau auswählt, muss er auch über Berechtigungen verfügen, mit denen er die Daten aus den registrierten Objekten einlesen kann.

## F: Wird Azure Data Catalog auch für die lokale Bereitstellung verfügbar gemacht?

**Azure Data Catalog** ist ein Clouddienst, der sowohl Clouddatenquellen als auch lokale Datenquellen verarbeiten kann. Es handelt sich also um eine Hybridlösung zur Ermittlung von Datenquellen. Es ist derzeit keine Version des **Azure Data Catalog**-Diensts geplant, der lokal ausgeführt wird.

##F: Können aus den registrierten Datenquellen auch mehr oder umfangreichere Metadaten extrahiert werden?

Wir arbeiten derzeit daran, die Funktionen von **Azure Data Catalog** zu erweitern. Wenn zusätzliche Metadaten vorhanden sind, die während der Registrierung aus der Datenquelle extrahiert werden sollen, können Sie dies im [Azure Data Catalog-Forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) vorschlagen \(oder einen bereits vorhandenen Vorschlag unterstützen\). In Zukunft werden auch Drittanbieter berechtigt sein, neue Datenquellentypen per Erweiterbarkeits-API hinzuzufügen.

## F: Wie kann ich die Sichtbarkeit der registrierten Datenressourcen so einschränken, dass nur bestimmte Personen sie ermitteln können?

Wählen Sie die Datenressourcen in Azure Data Catalog aus, und klicken Sie auf die Schaltfläche „In Besitz nehmen“. Besitzer von Datenressourcen in Azure Data Catalog können die Einstellungen zur Sichtbarkeit ändern, um entweder für alle Katalogbenutzer die Ermittlung der im Besitz befindlichen Ressourcen zuzulassen oder die Sichtbarkeit auf bestimmte Benutzer zu beschränken.

## F: Wie aktualisiere ich die Registrierung für eine Datenressource so, dass Änderungen in der Datenquelle im Katalog widergespiegelt werden?

Zum Aktualisieren der Metadaten für Datenressourcen, die im Katalog bereits registriert sind, registrieren Sie die Datenquelle mit den Ressourcen einfach erneut. Alle Änderungen in der Datenquelle, z. B. hinzugefügte oder entfernte Spalten in Tabellen oder Ansichten, werden im Katalog aktualisiert, aber alle von Benutzern angegebenen Anmerkungen werden beibehalten.

## F: Wie stelle ich bei der Arbeit mit Azure Data Catalog Fragen oder nehme die Hilfe in Anspruch?

Wenn Probleme auftreten oder falls Sie Hilfe mit der Preview-Version von Azure Data Catalog benötigen, können Sie einen Beitrag im [Azure Data Catalog-Forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) erstellen.

## F: Meine Frage wird hier nicht beantwortet. Was kann ich tun?

Wechseln Sie in das [Azure Data Catalog-Forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Fragen, die dort gestellt werden, werden in diesen Artikel aufgenommen.

<!---HONumber=July15_HO5-->
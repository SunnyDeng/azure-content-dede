<properties title="Erstellen eines DocumentDB-Kontos" pageTitle="Erstellen eines DocumentDB-Kontos | Azure" description="Dieses Thema beschreibt die Erstellung eines DocumentDB-Kontos und die Auswahl von Kontoeinstellungen im Azure-Vorschauportal."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Erstellen eines DocumentDB-Kontos

Zur Verwendung von DocumentDB müssen Sie ein DocumentDB-Konto in einer verfügbaren geografischen Region erstellen. Dieses Thema beschreibt die Erstellung eines DocumentDB-Kontos im Azure-Vorschauportal.

## Inhaltsverzeichnis

-   [Gewusst wie: Erstellen eines DocumentDB-Kontos][Gewusst wie: Erstellen eines DocumentDB-Kontos]
-   [Nächste Schritte][Nächste Schritte]

## <span id="HowTo"></span></a>Gewusst wie: Erstellen eines DocumentDB-Kontos

1.  Melden Sie sich beim [Azure-Vorschau-Verwaltungsportal][Azure-Vorschau-Verwaltungsportal] an.
2.  Klicken Sie auf „Neu -\> DocumentDB Account“.
    ![][0]

    Alternativ können Sie in der Azure-Galerie die Kategorie „Daten, Speicher und Sicherung“ suchen, **DocumentDB** auswählen und dann auf **Erstellen** klicken.

    ![][1]

    Geben Sie im Fenster **New DocumentDB (Preview)** die gewünschte Konfiguration für das DocumentDB-Konto an.

    ![][2]

    Geben Sie im Feld „Name“ einen Unterdomänennamen ein, der im URI für das DocumentDB-Konto verwendet werden soll. Dieser Wert wird der Hostname im URI, der zum Adressieren des DocumentDB-Kontos verwendet wird. Der Namenseintrag darf nur Kleinbuchstaben, Ziffern und den Bindestrich „-“ enthalten und muss zwischen 3 und 50 Zeichen lang sein.

    *Beachten Sie, dass documents.azure.com an den gewählten Endpunktnamen angehängt wird, um den DocumentDB-Konto-Endpunkt zu erstellen.*

    Der Bereich **Preisebene** ist gesperrt, da die DocumentDB-Vorschau nur eine Standardpreisebene unterstützt.

    *Weitere Informationen finden Sie in den [DocumentDB Preisdetails][DocumentDB Preisdetails].*

    Der Bereich **Optionale Konfiguration** dient zur Angabe der Anfangskapazität, die Ihrem DocumentDB-Konto zugewiesen wird. DocumentDB verwendet Kapazitätseinheiten, um Ihnen die Skalierung Ihres DocumentDB-Kontos zu ermöglichen, wobei jede Kapazitätseinheit reservierten Datenbankspeicher und Durchsatz umfasst. Standardmäßig wird 1 Kapazitätseinheit bereitgestellt. Sie können die Anzahl der Kapazitätseinheiten, die Ihrem DocumentDB-Konto zur Verfügung steht, jederzeit im [Azure-Vorschau-Verwaltungsportal][Azure-Vorschau-Verwaltungsportal] anpassen.

    *Ausführliche Informationen zur DocumentDB-Kontokapazität und zum Durchsatz finden Sie im Artikel [Verwalten der Kapazität und Leistung von DocumentDB][Verwalten der Kapazität und Leistung von DocumentDB].*

    Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihr DocumentDB-Konto auswählen oder erstellen. Standardmäßig wird eine neue Ressourcengruppe erstellt. Sie können jedoch eine vorhandene Ressourcengruppe auswählen, zu der Sie Ihr DocumentDB-Konto hinzufügen möchten.

    *Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen].*

    Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Sie mit dem DocumentDB-Konto verwenden möchten.

    *Falls Ihr Konto nur über ein Abonnement verfügt, wird dies automatisch ausgewählt.*

    Unter **Standort** können Sie einen geografischen Standort angeben, an dem Ihr DocumentDB-Konto gehostet werden soll.

3.  Klicken Sie nach der Konfiguration der Optionen für das neue DocumentDB-Konto auf **Erstellen**. Die Erstellung des DocumentDB-Kontos kann einige Minuten dauern. Sie können den Fortschritt im Startboard überwachen, um den Status zu überprüfen.
    ![][3]

    Alternativ verwenden Sie den Benachrichtigungshub.

    ![][4]

    ![][5]

4.  Nachdem das DocumentDB-Konto erstellt wurde, kann es mit den Standardeinstellungen verwendet werden.

    *Die Standardkonsistenz des DocumentDB-Kontos wird auf „Session“ eingestellt. Sie können die Standardkonsistenzeinstellung im [Azure-Vorschau-Verwaltungsportal][Azure-Vorschau-Verwaltungsportal] ändern.*
    ![][6]

5.  Außerdem können Sie im Fensterbereich **Durchsuchen** auf Ihre vorhandenen DocumentDB-Konten zugreifen.
    ![][7]

## <span id="NextSteps"></span></a>Nächste Schritte

-   Mehr über DocumentDB erfahren Sie in der Azure DocumentDB-Dokumentation auf [azure.com][azure.com]

<!--Image references-->

  [Gewusst wie: Erstellen eines DocumentDB-Kontos]: #Howto
  [Nächste Schritte]: #NextSteps
  [Azure-Vorschau-Verwaltungsportal]: https://portal.azure.com/
  [0]: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [DocumentDB Preisdetails]: http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409
  [Verwalten der Kapazität und Leistung von DocumentDB]: ../documentdb-manage/
  [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/de-de/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319

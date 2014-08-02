<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Premium SQL Database" pageTitle="Sign up for Azure Premium for SQL Database" metaKeywords="" description="Describes how to sign up for the Premium for SQL Database preview, request your Premium database quota, and then upgrade a database to Premium in Azure SQL Database." metaCanonical="" services="cloud-services" documentationCenter="" title="Sign up for the preview of Premium for Azure SQL Database" authors="karaman" solutions="" manager="" editor="tysonn" />

Registrieren für die Premium-Vorschau der Azure SQL-Datenbank
=============================================================

Dieses Lernprogramm beschreibt die notwendigen Schritte für die Teilnahme an der Premium-Vorschau für die SQL-Datenbank.

Die Azure SQL-Datenbank wurde als limitierte Vorschau eines neuen Dienstes gestartet - Premium Edition für SQL-Datenbank. Premium-Datenbanken stellen reservierte Ressourcen für eine besser vorhersagbare Leistung in Cloud-Anwendungen bereit.

[Die in diesem Thema beschriebene Funktion ist nur in der Vorschau verfügbar. Dieses Thema ist Teil der Vorabdokumentation und kann in zukünftigen Versionen geändert werden.]

Inhaltsverzeichnis
------------------

-   [Schritt 1: Registrieren für die Premium-Vorschau der SQL-Datenbank](#SignUp)
-   [Schritt 2: Anfordern eines Premium-Datenbankkontingents](#Quota)
-   [Schritt 3: Erstellen einer Premium-Datenbank](#Upgrade)

Schritt 1: Registrieren für die Premium-Vorschau der SQL-Datenbank
------------------------------------------------------------------

Um dieses neue Feature nutzen zu können, müssen Sie Ihr Abonnement für die Premium-Vorschau der SQL-Datenbank registrieren.

1.  Melden Sie sich mit Ihrem Microsoft-Konto auf der Seite für [Azure-Vorschaufunktionen](http://account.windowsazure.com/PreviewFeatures) an.

    **Hinweis** - Nur Azure-Kontoadministratoren haben Zugriff auf das Kontoportal. Falls Sie nicht Kontoadministrator Ihres Abonnements sind, muss die entsprechende Person den Anmeldeprozess für Ihr Abonnement ausführen. Weitere Informationen zu Azure-Konten und Abonnements finden Sie unter [Kaufoptionen](http://account.windowsazure.com/PreviewFeatures).

    ![Image1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2.  Suchen Sie den Eintrag **Premium for SQL Database** in der Liste der Vorschaufeatures und klicken Sie auf die entsprechende **try it now**-Schaltfläche.

    ![Image2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)

3.  Wählen Sie das Abonnement aus, mit dem Sie sich für die Vorschau registrieren möchten.

    ![Image3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

    Der Zugriff auf die Vorschau ist aktiven, zahlungspflichtigen Azure-Abonnements vorbehalten. Sie haben die Möglichkeit, mehrere Abonnements zu registrieren, pro Abonnement ist jedoch nur eine Registrierung zulässig.

    Wenn Sie ein Abonnement für die Vorschau registrieren, entstehen Ihnen keine zusätzlichen Kosten. Nachdem die Vorschau jedoch aktiviert und das Premium-Kontingent eingerichtet wurde, unterliegt das Erstellen einer Premium-Datenbank den auf der Seite [Preisdetails zur SQL-Datenbank](http://www.windowsazure.com/en-us/pricing/details/sql-database/) dargelegten Preisinformationen.

    Der aktuelle Status der Anmeldeanfrage wird in der Liste der Vorschaufeatures angezeigt.

    ![Image4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4.  Anfragen werden auf Basis der aktuellen Kapazität und der Nachfrage genehmigt. Die Aktivierung Ihres Abonnements kann bis zu 2 Tage in Anspruch nehmen. Bei großer Nachfrage oder Erfüllung der Kapazitätsgrenze für die öffentliche Vorschau können längere Wartezeiten entstehen.

5.  Sie erhalten eine Benachrichtigung per E-Mail, sobald die Vorschau für Ihr Abonnement aktiviert wurde.

Schritt 2: Anfordern eines Premium-Datenbankkontingents
-------------------------------------------------------

Sobald die Vorschau für Ihr Abonnement aktiviert wurde, müssen Sie das Premium-Datenbankkontingent für jeden Server beantragen, auf dem Sie eine Premium-Datenbank erstellen möchten. Aufgrund der begrenzen Kapazität möchten wir Sie bitten, das Kontingent nur für diejenigen Server zu beantragen, auf denen Sie Premium-Datenbanken erstellen möchten, und alle anderen ausstehenden Anfragen zu stornieren.

1.  Melden Sie sich mit Ihrem Microsoft-Konto beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.

    **Hinweis** - Kontoadministratoren, Dienstadministratoren und Co-Administratoren des Abonnements können Kontingente beantragen, sobald die Vorschau für das Abonnement aktiviert wurde.

2.  Navigieren Sie zur Liste der **Server** in der **SQL Databases**-Erweiterung.
3.  Wählen Sie den Server aus, für den Sie das Premium-Datenbankkontingent beantragen möchten.
4.  Navigieren Sie zum Bereich **Quick Start** für den entsprechenden Server, indem Sie auf das Blitz-Symbol in der oberen Navigationsleiste klicken.
5.  Wählen Sie **Request Premium Database Quota** im Bereich **Premium Database** aus.

    ![Image6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)

    Nach der Übermittlung Ihrer Anfrage kann es bis zu 5 Tage dauern, bis das Kontingent bereitgestellt wird. Bei großer Nachfrage oder Erfüllung der Kapazitätsgrenze können längere Wartezeiten entstehen.

    Weitere Hinweise zu Anfragen für Premium-Datenbankkontingente:

    -   Das Kontingent ist nicht für Kunden mit kostenlosen Testversionen verfügbar.
    -   Das Kontingent ist zu Anfang limitiert, und Anfragen werden anhand der aktuellen Nachfrage und verfügbarer Kapazitäten genehmigt.
    -   Microsoft kann nicht genutzte Kontingente nach 15 Tagen wieder freigeben.
    -   Für jeden logischen Server in einem Abonnement kann nur eine Kontingentanfrage gestellt werden.
    -   Das Kontingent ist zunächst auf eine Datenbank pro logischem Server beschränkt.
    -   Die Kontingentanfragen sind kostenlos. Bei der Erstellung von Premium-Datenbanken oder Upgrades existierender Web- oder Business-Edition-Datenbanken auf Premium fallen jedoch zusätzliche Kosten für die Datenbank an.

6.  Der Status Ihrer Kontingentanfrage wird auf der **Quick Start**-Seite angezeigt.

    ![Image7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)

7.  Sie erhalten eine E-Mail-Benachrichtigung, wenn Ihre Kontingentanfrage genehmigt wurde und Sie Ihr Kontingent verwenden können.
8.  Nach der Genehmigung finden Sie das verbleibende Kontingent für Premium-Datenbanken in den Registerkarten **Quick Start** oder **Dashboard** Ihres Servers.

    ![Image8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

Schritt 3: Erstellen einer Premium-Datenbank
--------------------------------------------

Nach der Genehmigung Ihres Kontingents können Sie neue Premium-Datenbanken erstellen oder existierende Web- oder Business-Edition-Datenbanken auf Premium erweitern, um die reservierten Kapazitäten und die besser vorhersagbare Leistung zu nutzen.

![Image9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)

![Image10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)

Weitere Informationen finden Sie unter [Verwalten von Premium-Datenbanken](http://go.microsoft.com/fwlink/p/?LinkID=311927).

**Hinweis:** Sie können den Premium-Status und die Reservierungsgröße Ihrer Datenbank nur einmal alle 24 Stunden ändern.

Nächste Schritte
----------------

Weitere Informationen zu Premium-Datenbanken finden Sie unter:

-   [Verwalten einer Premium-Datenbank](http://go.microsoft.com/fwlink/p/?LinkID=311927)
-   [Leitfaden für Premium-SQL-Datenbanken](http://go.microsoft.com/fwlink/p/?LinkId=313650)


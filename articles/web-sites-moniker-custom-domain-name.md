<properties title="Learn how to configure an Azure web site to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure web site to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Moniker)
======================================================================================

[Custom Domain](/en-us/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-moniker-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/en-us/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel erläutert die Verwendung eines benutzerdefinierten, von [Moniker.com](https://moniker.com) gekauften Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für Basis-, Freigabe- und Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

Interpretation von DNS-Datensätzen
----------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Konfigurieren Ihrer Websites für Basis-, Freigabe- und Standardmodus
--------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Moniker bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools für Moniker.com zu suchen:

1.  Melden Sie sich auf Moniker.com an und wählen Sie **My Domains** (Meine Domänen). Klicken Sie anschließend auf **Manage Templates** (Vorlagen verwalten).

    ![Seite "My Domains" (Meine Domänen) auf Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  Wählen Sie auf der Seite **Zone Template Management** (Zonen-Vorlagenverwaltung) **Create New Template** (Neue Vorlage erstellen).

    ![Moniker Zonen-Vorlagenverwaltung](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  Geben Sie einen **Vorlagennamen** ein.

4.  Erstellen Sie anschließend einen DNS-Datensatz, indem Sie zuerst die **Art des Datensatzes** auswählen. Geben Sie anschließend den **Hostnamen** und die **Adresse** ein.

    ![Moniker Zonen-Vorlage erstellen](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    -   Wenn Sie einen CNAME-Datensatz erstellen, müssen Sie im Feld **Hostname** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Im Feld **Adresse** müssen Sie den Domänennamen **.azurewebsites.net** Ihrer Azure-Website eingeben. Beispiel: **contoso.azurwebsites.net**.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie für den **Hostnamen** entweder **@** eingeben (dies steht für den Namen der Root-Domäne, z. B. **contoso.com**) oder die Unterdomäne, die Sie verwenden möchten (z. B. **www.**). In das Feld **Adresse** müssen Sie die IP-Adresse Ihrer Azure-Website eingeben.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
        >
        > -   Einen Wert **Hostname** mit **www** und einen Wert **Adresse** mit **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Einen Wert **Hostname** mit **awverify.www** und einen Wert **Adresse** mit **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

5.  Klicken Sie auf die Schaltfläche **Add** (Hinzufügen), um den Eintrag hinzuzufügen.

6.  Klicken Sie, nachdem alle Einträge hinzugefügt wurden, auf **Save** (Speichern).

7.  Wählen Sie **Domain Manager** (Domänenmanager), um zur Liste der Domänen zurückzukehren.

8.  Aktivieren Sie das Kästchen neben Ihrer Zieldomäne und klicken Sie erneut auf **Manage Templates** (Vorlagen verwalten).

9.  Wählen Sie die neue Vorlage aus, die Sie zuvor erstellt haben. Klicken Sie anschließend auf den Link **place selected domains (1) into this Template** (Ausgewählte Domänen (1) in diese Vorlage kopieren).

    ![Moniker Zonen-Vorlage erstellen](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

Aktivieren des Domänennamens auf Ihrer Website
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


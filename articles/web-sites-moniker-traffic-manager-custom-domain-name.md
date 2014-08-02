<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Moniker, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfiguration eines benutzerdefinierten Domänennamens für eine Azure-Website mithilfe des Traffic Manager (Moniker)
===================================================================================================================

[Custom Domain](/en-us/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-moniker-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/en-us/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel informiert Sie über die Verwendung eines von [Moniker](https://moniker.com) erworbenen benutzerdefinierten Domänennamens auf einer Azure-Website.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren des Traffic Manager für Ihre Website](#enabledomain)

Interpretation von DNS-Datensätzen
----------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Konfigurieren Ihrer Websites für den Standardmodus
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Moniker bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools für Moniker.com zu suchen:

1.  Melden Sie sich auf Moniker.com an und wählen Sie **My Domains** (Meine Domänen). Klicken Sie anschließend auf **Manage Templates** (Vorlagen verwalten).

    ![Seite "My Domains" (Meine Domänen) auf Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  Wählen Sie auf der Seite **Zone Template Management** (Zonen-Vorlagenverwaltung) **Create New Template** (Neue Vorlage erstellen).

    ![Moniker Zonen-Vorlagenverwaltung](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  Geben Sie einen **Vorlagennamen** ein.

4.  Erstellen Sie anschließend einen DNS-Datensatz, indem Sie zuerst die **Art des Datensatzes** auswählen. Geben Sie anschließend den **Hostnamen** und die **Adresse** ein.

    ![Moniker Zonen-Vorlage erstellen](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    -   Wenn Sie einen CNAME-Datensatz erstellen, müssen Sie im Feld **Hostname** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Im **Adressfeld** müssen Sie den Domänennamen **.trafficmanager.net** des Traffic Manager-Profils festlegen, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen zu einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.

5.  Klicken Sie auf die Schaltfläche **Add** (Hinzufügen), um den Eintrag hinzuzufügen.

6.  Klicken Sie, nachdem alle Einträge hinzugefügt wurden, auf **Save** (Speichern).

7.  Wählen Sie **Domain Manager** (Domänenmanager), um zur Liste der Domänen zurückzukehren.

8.  Aktivieren Sie das Kästchen neben Ihrer Zieldomäne und klicken Sie erneut auf **Manage Templates** (Vorlagen verwalten).

9.  Wählen Sie die neue Vorlage aus, die Sie zuvor erstellt haben. Klicken Sie anschließend auf den Link **place selected domains (1) into this Template** (Ausgewählte Domänen (1) in diese Vorlage kopieren).

    ![Moniker Zonen-Vorlage erstellen](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

Aktivieren einer Traffic Manager-Website
----------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


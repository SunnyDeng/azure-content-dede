<properties
   pageTitle="Verwalten von Traffic Manager-Profilen"
   description="In diesem Artikel wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, deaktivieren, aktivieren, löschen und seinen Verlauf anzeigen."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma" />

# Verwalten von Traffic Manager-Profilen

Sie verwenden ein Traffic Manager-Profil um anzugeben, welche Endpunkte für Cloud-Dienste oder Websites von Traffic Manager überwacht werden und welche Lastenausgleichsmethode Sie für das Verteilen der Verbindungen mit diesen Endpunkten verwenden möchten.

## Erstellen eines Traffic Manager-Profils mithilfe von "Schnellerfassung"

Sie können sehr schnell ein Traffic Manager-Profil erstellen, indem Sie im Verwaltungsportal die **Schnellerfassung** verwenden. Mit der Schnellerfassung können Sie Profile mit grundlegenden Konfigurationseinstellungen erstellen. Allerdings können Sie die Schnellerfassung nicht für Einstellungen wie die Gruppe von Endpunkten (Cloud-Dienste und Websites), die Failoverreihenfolge für die Failover-Lastenausgleichsmethode oder die Überwachungseinstellungen verwenden. Nach dem Erstellen Ihres Profils können Sie diese Einstellungen im Verwaltungsportal konfigurieren. Traffic Manager unterstützt bis zu 200 Endpunkte pro Profil. Die meisten Verwendungsszenarien erfordern jedoch nur eine kleine Anzahl von Endpunkten. Eine Liste der Konfigurationsschritte finden Sie unter [Traffic Manager-Konfigurationsaufgaben](https://msdn.microsoft.com/library/azure/hh744830.aspx).

### So erstellen Sie ein neues Traffic Manager-Profil

1. **Stellen Sie Ihre Clouddienste und Websites in der Produktionsumgebung bereit.** Weitere Informationen über Clouddienste finden Sie unter [Clouddienste](http://go.microsoft.com/fwlink/p/?LinkId=314074). Informationen über Clouddienste finden Sie unter [Bewährte Methoden](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Weitere Informationen zu Websites finden Sie unter [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Melden Sie sich beim Verwaltungsportal an.** Um ein neues Traffic Manager-Profil zu erstellen, klicken Sie unten links im Portal auf **Neu**. Klicken Sie auf **Netzwerkdienste**, auf **Traffic Manager** und dann auf **Schnellerfassung**, um mit der Konfiguration des Profils zu beginnen.
3. **Konfigurieren Sie das DNS-Präfix.** Geben Sie Ihre Traffic Manager-Profilen einen eindeutigen DNS-Präfixnamen. Sie können nur das Präfix für einen Traffic Manager-Domänennamen angeben.
4. **Wählen Sie das Abonnement.** Wählen Sie das entsprechende Azure-Abonnement. Jedes Profil ist einem einzelnen Abonnement zugeordnet. Wenn Sie nur über ein Abonnement verfügen, wird diese Option nicht angezeigt.
5. **Wählen Sie die Lastenausgleichsmethode.** Wählen Sie die Lastenausgleichsmethode in **Lastenausgleichsrichtlinie**. Weitere Informationen zu den Lastenausgleichsmethoden finden Sie unter [Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md).
6. **Klicken Sie auf "Erstellen", um ein neues Profil zu erstellen**. Nach Abschluss der Konfiguration des Profils finden Sie Ihr Profil im Verwaltungsportal im Bereich "Traffic Manager".
7. **Konfigurieren Sie Endpunkte, Überwachung und weitere Einstellungen im Verwaltungsportal.** Da Sie mithilfe der Schnellerfassung nur grundlegende Einstellungen konfigurieren können, ist es notwendig, zusätzliche Einstellungen, z. B. die Liste der Endpunkte und deren Failoverreihenfolge zu konfigurieren, um die gewünschte Konfiguration abzuschließen. Eine Liste der Aufgaben zum Vervollständigen der Konfiguration finden Sie unter [Traffic Manager-Konfigurationsaufgaben](https://msdn.microsoft.com/library/azure/hh744830.aspx).

## Deaktivieren, Aktivieren oder Löschen von Profilen

Sie können ein vorhandenes Traffic Manager-Profil so deaktivieren, dass keine Benutzeranforderungen mehr an die konfigurierten Endpunkte verwiesen werden. Wenn Sie ein Traffic Manager-Profil deaktivieren, bleiben das Profil selbst und die Informationen im Profil erhalten und können auf der Traffic Manager-Benutzeroberfläche bearbeitet werden. Wenn Sie das Profil erneut aktivieren möchten, können Sie dies problemlos im Verwaltungsportal tun, woraufhin die Weiterleitung wird aufgenommen wird. Wenn Sie ein Traffic Manager-Profil im Verwaltungsportal erstellen, wird es automatisch aktiviert. Wenn ein Profil nicht mehr erforderlich ist, können Sie es löschen.

### So deaktivieren Sie ein Profil

1. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass der entsprechende Eintragstyp und Zeiger auf einen anderen Namen oder die IP-Adresse eines bestimmten Speicherorts im Internet verwendet werden. Ändern Sie also den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass er nicht mehr einen CNAME-Ressourceneintrag verwendet, der auf den Domänennamen Ihres Traffic Manager-Profils verweist.
2. Der Datenverkehr wird nicht mehr über die Traffic Manager-Profileinstellungen an die Endpunkte weitergeleitet.
3. Wählen Sie das Profil aus, das Sie deaktivieren möchten. Um das Profil auszuwählen, markieren Sie das Profil auf der Seite "Traffic Manager" durch Klicken auf die Spalte neben dem Profilnamen. Klicken Sie nicht auf den Namen des Profils oder den Pfeil neben dem Namen, da Sie dadurch zur Einstellungsseite für das Profil gelangen.
4. Klicken Sie nach Auswahl des Profils am unteren Rand der Seite auf **Deaktivieren**.

### So aktivieren Sie ein Profil

1. Wählen Sie das Profil aus, das Sie aktivieren möchten. Um das Profil auszuwählen, markieren Sie das Profil auf der Seite "Traffic Manager" durch Klicken auf die Spalte neben dem Profilnamen. Klicken Sie nicht auf den Namen des Profils oder den Pfeil neben dem Namen, da Sie dadurch zur Einstellungsseite für das Profil gelangen.
2. Klicken Sie nach Auswahl des Profils am unteren Rand der Seite auf **Aktivieren**.
3. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass der mit CNAME-Eintragstyp verwendet wird, der den Domänennamen Ihres Unternehmens dem Domänennamen Ihres Traffic Manager-Profils zuordnet. Weitere Informationen dazu finden Sie unter [Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](traffic-manager-point-internet-domain.md).
4. Der Datenverkehr wird wieder an die Endpunkte weitergeleitet.

### So löschen Sie ein Profil

1. Ändern Sie den DNS-Ressourceneintrag auf dem Internet-DNS-Server so, dass er nicht mehr einen CNAME-Ressourceneintrag verwendet, der auf den Domänennamen Ihres Traffic Manager-Profils verweist.
2. Wählen Sie das Profil aus, das Sie löschen möchten. Um das Profil auf der Traffic Manager-Seite auszuwählen, markieren Sie es durch Klicken auf die Spalte neben dem Profil. Klicken Sie nicht auf den Namen des Profils oder den Pfeil neben dem Namen, da Sie dadurch zur Einstellungsseite für das Profil gelangen.
4. Klicken Sie nach Auswahl des Profils am unteren Rand der Seite auf **Löschen**.

## Anzeigen des Änderungsverlaufs für das Traffic Manager-Profil

Sie können die Änderungsverlauf für Ihr Traffic Manager-Profil im Verwaltungsportal unter "Verwaltungsdienste" anzeigen.

### So zeigen Sie den Traffic Manager-Änderungsverlauf an

1. Klicken Sie im linken Bereich des Verwaltungsportals auf **Verwaltungsdienste**.
2. Klicken Sie auf der Seite **Verwaltungsdienste** auf Vorgangsprotokolle.
3. Auf der Seite "Vorgangsprotokolle" können Sie Filter verwenden, um den Änderungsverlauf für Ihr Traffic Manager-Profil anzuzeigen. Nachdem Sie die Filteroptionen ausgewählt haben, klicken Sie auf das Häkchen, um die Ergebnisse anzuzeigen.
   - Wenn Sie Profiländerungen für all Ihre Profile anzeigen möchten, wählen Sie Ihr Abonnement und den gewünschten Zeitraum aus, und wählen Sie dann aus dem Dropdownmenü **Typ** die Option **Traffic Manager** aus.
   - Wenn Sie nach Profilnamen filtern möchten, geben Sie den Namen des Profils in das Feld **Dienstname** ein, oder wählen Sie ihn aus der Dropdownliste aus.
   - Wenn Sie Details für jede einzelne Änderung anzeigen möchten, wählen Sie die Zeile mit der Änderung aus, die Sie anzeigen möchten, und klicken Sie dann am unteren Rand der Seite auf **Details**. Im Fenster **Vorgangsdetails** können Sie die XML-Darstellung des API-Objekts anzeigen, das als Teil des Vorgangs erstellt oder aktualisiert wurde, und den XML-Code in die Zwischenablage kopieren.


## Siehe auch

[Traffic Manager – Übersicht](traffic-manager-overview.md)

[Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md)

[Traffic Manager-Überwachung](traffic-manager-monitoring.md)

[Traffic Manager-Konfigurationsaufgaben](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)
 

<!---HONumber=July15_HO4-->
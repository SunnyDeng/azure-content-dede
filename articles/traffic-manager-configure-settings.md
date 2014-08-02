<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Configure Traffic Manager Settings" authors="" solutions="" manager="" editor="" />

Konfigurieren der Traffic Manager-Einstellungen
===============================================

Azure Traffic Manager ermöglicht Ihnen, die Verteilung von Benutzerdatenverkehr in gehosteten Azure-Diensten zu steuern.

Traffic Manager funktioniert durch das Anwenden eines intelligenten Richtlinienmodus auf die DNS-Abfragen im Hauptdomänennamen des Unternehmens. Aktualisieren Sie die DNS-Ressourceneinträge, die zu Ihrem Unternehmen gehören, sodass sie auf die Traffic Manager-Domänen verweisen. Traffic Manager-Richtlinien, die mit diesen Domänen verbunden sind, lösen dann die DNS-Abfragen im Hauptdomänennamen des Unternehmens in die IP-Adressen bestimmter in Azure gehosteter Dienste auf, die in den Traffic Manager-Richtlinien aufgeführt sind. Weitere Informationen finden Sie unter [Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx).

Inhaltsverzeichnis
------------------

-   [Gewusst wie: Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](#howto_point_company)
-   [Gewusst wie: Testen von Richtlinien](#howto_test)
-   [Gewusst wie: Zeitweiliges Deaktivieren von Richtlinien und gehosteten Diensten](#howto_temp_disable)
-   [Gewusst wie: Bearbeiten von Richtlinien](#howto_edit_policy)
-   [Gewusst wie: Gleichmäßiger Lastenausgleich von Datenverkehr über mehrere gehostete Dienste hinweg](#howto_load_balance)
-   [Gewusst wie: Erstellen von Failover-Richtlinien](#howto_create_failover)
-   [Gewusst wie: Weiterleiten von eingehendem Verkehr an gehostete Dienste basierend auf Netzwerkleistung](#howto_direct)

Gewusst wie: Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne
----------------------------------------------------------------------------------------

Um den Domänennamen des Unternehmens auf eine Traffic Manager-Domäne zu verweisen, bearbeiten Sie den DNS-Ressourceneintrag auf dem DNS-Server mit CNAME.

Um beispielsweise die Hauptdomäne **www.contoso.com** des Unternehmens auf die Traffic Manager-Domäne namens **contoso.trafficmanager.net** zu verweisen, aktualisieren Sie den DNS-Ressourceneintrag wie folgt: `www.contoso.com IN CNAME contoso.trafficmanager.net`

Nun wird der gesamte Verkehr von *www.contoso.com* an *contoso.trafficmanager.net* umgeleitet. Stellen Sie sicher, dass Sie eine Domäne verwenden, von welcher der gesamte Datenverkehr an Traffic Manager umgeleitet werden soll.

Gewusst wie: Testen von Richtlinien
-----------------------------------

Die beste Möglichkeit, Ihre Richtlinie zu testen, ist das Einrichten einer Reihe von Clients und das anschließende Reduzieren der Dienste in der Richtlinie um jeweils einen Dienst. Die folgenden Tipps unterstützen Sie beim Testen Ihrer Traffic Manager-Richtlinie:

-   **Stellen Sie die DNS-Gültigkeitsdauer (TTL) sehr niedrig ein**, sodass die Änderungen schnell propagiert werden, beispielsweise auf 30 Sekunden.

-   **Sie sollten die IP-Adressen der in Azure gehosteten Dienste** für die getestete Richtlinie kennen. Diese Information erhalten Sie im Azure-Verwaltungsportal. Klicken Sie auf die Produktionsbereitstellung Ihres Dienstes. Das Eigenschaftenfenster auf der rechten Seite enthält als letzten Eintrag die VIP, die virtuelle IP-Adresse des gehosteten Dienstes.

    ![IP-Adresse des gehosteten Dienstes](./media/traffic-manager-configure-settings/hosted_service_IP_location.png)

    **Abbildung 1**: IP-Adresse des gehosteten Dienstes

-   **Verwenden Sie Tools, mit denen Sie einen DNS-Namen in eine IP-Adresse auflösen können**, und zeigen Sie diese Adresse an. Damit überprüfen Sie, ob der Domänenname des Unternehmens in die IP-Adressen der gehosteten Dienste in Ihrer Richtlinie aufgelöst wird. Sie sollten in Übereinstimmung mit der Lastenausgleichsmethode der Traffic Manager-Richtlinie aufgelöst werden. Falls Sie mit einem Windows-System arbeiten, können Sie nslookup von einem CMD-Fenster aus verwenden (Erläuterung siehe unten). Weitere öffentlich verfügbare Tools, mit denen Sie eine IP-Adresse herausfinden können, sind ebenfalls im Internet verfügbar.

-   **Überprüfen Sie die Traffic Manager-Richtlinie** mithilfe von *nslookup*.

> **So überprüfen Sie eine Traffic Manager-Richtlinie mithilfe von nslookup**

> 1.  Starten Sie ein CMD-Fenster, indem Sie auf **Start - Ausführen** klicken und dann **CMD** eingeben.

> 1.  Um den DNS-Auflösungscache zu leeren, geben Sie `ipconfig /flushdns` ein.

> 1.  Geben Sie den Befehl `nslookup <your traffic manager domain>` ein. Mit dem folgenden Befehl wird beispielsweise eine Domäne mit dem Präfix *myapp.contoso* überprüft: `nslookup myapp.contoso.trafficmanager.net`

> > Das Ergebnis sieht in der Regel wie folgt aus:

> > -   Der DNS-Name und die IP-Adresse des DNS-Servers, auf den zugegriffen wird, um diese Traffic Manager-Domäne aufzulösen.

> > -   Der Traffic Manager-Domänenname, den Sie in der Befehlszeile nach "nslookup" eingegeben haben, und die IP-Adresse, in welche die Traffic Manager-Domäne aufgelöst wird. Die zweite IP-Adresse ist für die Überprüfung wichtig. Sie sollte mit einer VIP für einen der gehosteten Dienste in der getesteten Traffic Manager-Richtlinie übereinstimmen.

> > ![Beispiel für Befehl nslookup](./media/traffic-manager-configure-settings/nslookup_command_example.png)

> > **Abbildung 2**: Beispiel für Befehl nslookup

-   **Verwenden Sie eine der unten aufgelisteten zusätzlichen Testmethoden.** Wählen Sie die geeignete Methode für die Art des von Ihnen getesteten Lastenausgleichs.

### Leistungsrichtlinien

Sie benötigen Clients in verschiedenen Regionen der Welt, um Ihre Domäne effektiv zu testen. Sie können Clients in Azure erstellen, die versuchen, Ihre Dienste über den Domänennamen des Unternehmens aufzurufen. Alternativ können Sie sich remote bei Clients in anderen Ländern anmelden, falls Sie in einem globalen Unternehmen arbeiten, und von diesen Clients aus testen.

Es gibt kostenlose webbasierte nslookup- und dig-Dienste. Mit einigen davon können Sie die DNS-Namensauflösung von verschiedenen Standorten aus überprüfen. Führen Sie beispielsweise eine nslookup-Suche durch. Eine weitere Option wäre eine Drittanbieterlösung wie Gomez oder Keynote, um zu bestätigen, dass Ihre Richtlinien den Datenverkehr wie erwartet verteilen.

### Failover-Richtlinien

1.  Lassen Sie alle Dienste aktiviert.

2.  Verwenden Sie einen einzelnen Client.

3.  Fordern Sie die DNS-Auflösung für Ihre Unternehmensdomäne mit Ping oder einem ähnlichen Dienstprogramm an.

4.  Stellen Sie sicher, dass die erhaltene IP-Adresse für den primären gehosteten Dienst gilt.

5.  Fahren Sie den primären Dienst herunter bzw. entfernen Sie die Überwachungsdatei, sodass Traffic Manager davon ausgeht, dass der Dienst ausgefallen ist.

6.  Warten Sie mindestens 2 Minuten.

7.  Fordern Sie die DNS-Auflösung an.

8.  Stellen Sie sicher, dass die erhaltene IP-Adresse für den sekundären gehosteten Dienst gilt, wie aus der Reihenfolge der Dienste im Dialogfeld "Traffic Manager-Richtlinie bearbeiten" ersichtlich ist.

9.  Wiederholen Sie diesen Vorgang, indem Sie den zweiten Dienst herunterfahren, dann den dritten usw. Stellen Sie jeweils sicher, dass die DNS-Auflösung die IP-Adresse des nächsten Dienstes in der Liste zurückgibt. Nachdem alle Dienste heruntergefahren sind, sollten Sie erneut die IP-Adresse der ersten gehosteten Dienstes erhalten.

### Roundrobin-Richtlinien

1.  Lassen Sie alle Dienste aktiviert.

2.  Verwenden Sie einen einzelnen Client.

3.  Fordern Sie die DNS-Auflösung für die oberste Domäne an.

4.  Stellen Sie sicher, dass die erhaltene IP-Adresse eine Adresse aus Ihrer Liste ist.

5.  Wiederholen Sie den Prozess, indem Sie die DNS-TTL ablaufen lassen, sodass Sie eine neue IP-Adresse erhalten. Sie sollten für jeden der gehosteten Dienste eine IP-Adresse sehen. Dann wird der Prozess wiederholt.

Gewusst wie: Zeitweiliges Deaktivieren von Richtlinien und gehosteten Diensten
------------------------------------------------------------------------------

Sie können zuvor erstellte Traffic Manager-Richtlinien deaktivieren, damit sie keinen Datenverkehr weiterleiten. Wenn Sie eine Traffic Manager-Richtlinie deaktivieren, bleiben die Informationen der Richtlinie erhalten und können in der Traffic Manager-Benutzeroberfläche bearbeitet werden. Sie können die Richtlinie ganz einfach erneut aktivieren, um die Weiterleitung wieder aufzunehmen.

Außerdem können Sie einzelne gehostete Dienste deaktivieren, die Teil der Traffic Manager-Richtlinie sind. Diese Aktion belässt den gehosteten Dienst effektiv als Teil der Richtlinie, aber die Richtlinie agiert, als ob der gehostete Dienst nicht darin enthalten ist. Diese Aktion ist äußerst nützlich zum zeitweiligen Entfernen eines gehosteten Dienstes, der sich im Wartungsmodus befindet oder erneut bereitgestellt werden soll und daher instabil ist. Nachdem der gehostete Dienst wieder betriebsbereit ist, kann er erneut aktiviert werden.

**Hinweis**
 Das Deaktivieren eines gehosteten Dienstes hat nichts mit dem Bereitstellungsstatus in Azure zu tun. Ein fehlerfrei funktionierender Dienst bleibt in Betrieb und kann Datenverkehr empfangen, selbst wenn er in Traffic Manager deaktiviert ist. Außerdem wirkt sich das Deaktivieren eines gehosteten Dienstes in einer Richtlinie nicht auf den Status in einer anderen Richtlinie aus.

### So deaktivieren Sie eine Richtlinie

1.  Wählen Sie eine aktivierte Richtlinie in der Traffic Manager-Benutzeroberflächenstruktur aus.

2.  Klicken Sie in der oberen Symbolleiste auf **Richtlinie deaktivieren**. Die Schaltfläche ist abgeblendet, wenn Sie eine Richtlinie auswählen, die bereits deaktiviert ist.

### So aktivieren Sie eine Richtlinie

1.  Wählen Sie eine deaktivierte Richtlinie in der Traffic Manager-Benutzeroberflächenstruktur aus.

2.  Klicken Sie in der oberen Symbolleiste auf **Richtlinie aktivieren**. Die Schaltfläche ist abgeblendet, wenn Sie eine Richtlinie auswählen, die bereits deaktiviert ist.

### So deaktivieren Sie einen gehosteten Dienst

1.  Wählen Sie einen aktivierten gehosteten Dienst in der Traffic Manager-Benutzeroberfläche aus. Sie müssen eine Richtlinie erweitern, um zu sehen, ob gehostete Dienste darin enthalten sind.

2.  Klicken Sie in der oberen Symbolleiste auf **Disable Service Policy**. Die Schaltfläche ist abgeblendet, wenn Sie einen gehosteten Dienst auswählen, der bereits deaktiviert ist.

3.  Der Datenverkehr wird nun nicht mehr an den gehosteten Dienst geleitet, basierend auf der für die Traffic Manager-Domäne eingestellten DNS-TTL-Zeitdauer, die Teil der Richtlinie ist. Weitere Informationen finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zum Überwachen von gehosteten Diensten in Azure Traffic Manager.

### So aktivieren Sie einen gehosteten Dienst

1.  Wählen Sie einen deaktivierten gehosteten Dienst in der Traffic Manager-Benutzeroberfläche aus. Sie müssen eine Richtlinie erweitern, um zu sehen, ob gehostete Dienste darin enthalten sind.

2.  Klicken Sie in der oberen Symbolleiste auf **Enable Service Policy**. Die Schaltfläche ist abgeblendet, wenn Sie einen gehosteten Dienst auswählen, der bereits aktiviert ist.

3.  Der Datenverkehr wird wieder entsprechend der Richtlinie an den gehosteten Dienst geleitet.

Gewusst wie: Bearbeiten von Richtlinien
---------------------------------------

Wenn Sie eine Richtlinie oder einen bestimmten gehosteten Dienst in der Richtlinie zeitweilig ausschalten möchten, können Sie sie temporär deaktivieren, ohne die Richtlinie zu ändern.

Gehen Sie wie folgt vor, um eine Richtlinie in einen anderen Typ zu ändern:

1.  **Melden Sie sich beim Traffic Manager-Bereich im Verwaltungsportal** unter <http://manage.windowsazure.com> an. Klicken Sie links unten auf der Portalseite auf **Virtuelles Netzwerk**, und wählen Sie im linken Fensterbereich die Option **Traffic Manager** aus.

2.  **Wählen Sie die zu ändernde Richtlinie** auf dem Traffic Manager-Bildschirm im Verwaltungsportal aus.

3.  Klicken Sie in der oberen Menüleiste auf **Konfigurieren**.

4.  **Nehmen Sie die gewünschten Änderungen an der Richtlinie vor.** Wenn Sie die Lastenausgleichsmethode ändern, ist je nach ausgewählter Option die Reihenfolge der gehosteten Dienste in der **Liste der ausgewählten gehosteten Dienste** wichtig oder auch nicht.

5.  Klicken Sie auf **Speichern**.

Gewusst wie: Gleichmäßiger Lastenausgleich von Datenverkehr über mehrere gehostete Dienste hinweg
-------------------------------------------------------------------------------------------------

Ein häufiges Lastenausgleichsmuster ist das Bereitstellen eines Satzes identischer gehosteter Dienste und das Senden von Datenverkehr an alle mit der Roundrobin-Methode. In diesem Artikel werden die Schritte zum Einrichten einer Traffic Manager-Domäne und der Richtlinie zum Durchführen dieser Art von Lastenausgleich erläutert.

Weitere Informationen zu den verschiedenen Lastenausgleichsmethoden von Traffic Manager finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx) im Abschnitt zu den Lastenausgleichsmethoden in Azure Traffic Manager.

1.  **Stellen Sie Ihre gehosteten Dienste** in der Produktionsumgebung bereit. Informationen zur Entwicklung und Bereitstellung finden Sie unter [In Azure gehostete Dienste](http://msdn.microsoft.com/library/gg432967.aspx).

2.  **Melden Sie sich beim Traffic Manager-Bereich im Verwaltungsportal** unter <http://manage.windowsazure.com> an. Klicken Sie links unten auf der Portalseite auf **Virtuelles Netzwerk**, und wählen Sie im linken Fensterbereich die Option **Traffic Manager** aus.

3.  **Wählen Sie die Richtlinien aus, und klicken Sie auf "Erstellen".** Wählen Sie den Ordner **Policies** in der Navigationsstruktur auf der linken Seite aus, um **Erstellen** in der oberen Symbolleiste zu aktivieren. Wählen Sie **Erstellen**. Das Dialogfeld **Create Traffic Manager policy** wird angezeigt.

    ![Schaltfläche "Erstellen" für Richtlinien](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **Abbildung 1**: Schaltfläche "Erstellen" für Richtlinien

4.  **Wählen Sie ein Abonnement aus.** Richtlinien und Domänen sind mit einem einzigen Abonnement verknüpft.

5.  **Wählen Sie die Roundrobin-Lastenausgleichsmethode.** Weitere Informationen zu den verschiedenen Lastenausgleichsmethoden von Traffic Manager finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx) im Abschnitt zu den Lastenausgleichsmethoden in Azure Traffic Manager.

6.  **Suchen Sie die gehosteten Dienste, und fügen Sie sie zur Richtlinie hinzu.** Verwenden Sie das Filterfeld, um gehostete Dienste zu suchen, welche die von Ihnen eingegebene Zeichenfolge enthalten. Leeren Sie das Feld, um alle gehosteten Dienste in der Produktion für das in Schritt 4 ausgewählte Abonnement anzuzeigen. Fügen Sie sie mithilfe der Pfeilschaltflächen zur Richtlinie hinzu. Die Reihenfolge im Feld **Selected DNS names** hat keinen Einfluss auf diese Lastenausgleichsmethode.

7.  **Richten Sie die Überwachung ein.** Durch die Überwachung wird sichergestellt, dass kein Datenverkehr an gehostete Dienste, die offline sind, gesendet wird. Sie müssen einen bestimmten Pfad und einen Dateinamen einrichten. Weitere Informationen finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zum Überwachen von gehosteten Diensten in Azure Traffic Manager.

8.  **Benennen Sie die Traffic Manager-Domäne.** Geben Sie Ihrer Domäne einen eindeutigen Namen. Sie können nur den Präfix für Ihre Domäne angeben. Lassen Sie den Wert für die DNS-Gültigkeitsdauer (TTL) auf dem Standardwert. Weitere Informationen zu den Auswirkungen dieser Einstellung finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zu den empfohlenen Methoden für gehostete Dienste und Richtlinien bei der Verwendung von Azure Traffic Manager.

    Das Dialogfeld **Create Traffic Manager policy** sollte in etwa wie im folgenden Beispiel aussehen.

    ![Dialogfeld für Roundrobin-Lastenausgleichsmethode](./media/traffic-manager-configure-settings/Dialog_box_for_Round_Robin_load_balancing_method.png)

    **Abbildung 2**: Dialogfeld für Roundrobin-Lastenausgleichsmethode

9.  **Testen Sie die Traffic Manager-Domäne und die Richtlinie.** Eine Anleitung finden Sie unter [Gewusst wie: Testen einer Azure Traffic Manager-Richtlinie](#howto_test).

10. **Lassen Sie den DNS-Server auf die Traffic Manager-Domäne verweisen.** Sobald Ihre Traffic Manager-Domäne eingerichtet ist und funktioniert, bearbeiten Sie den DNS-Eintrag des autorisierenden DNS-Servers, sodass Ihre Unternehmensdomäne auf die Traffic Manager-Domäne verweist. Mit dem folgenden Befehl wird beispielsweise der gesamte Datenverkehr von **www.contoso.com** an die Traffic Manager-Domäne **contoso.trafficmanager.net** geleitet: `www.contoso.com IN CNAME contoso.trafficmanager.net` Weitere Informationen finden Sie unter [Gewusst wie: Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](#howto_point_company).

Gewusst wie: Erstellen von Failover-Richtlinien
-----------------------------------------------

Häufig möchte ein Unternehmen die Zuverlässigkeit seiner Dienste gewährleisten. Dazu werden Sicherungsdienste bereitgestellt, falls der primäre Dienst ausfällt. Ein häufiges Muster für Dienst-Failover ist das Bereitstellen eines Satzes identischer gehosteter Dienste und das Senden von Datenverkehr an den primären Dienst (mit einer Liste von einer oder mehreren Sicherungen). In diesem Artikel werden die Schritte zum Einrichten einer Traffic Manager-Richtlinie zum Durchführen dieser Art von Failover-Sicherung erläutert.

Weitere Informationen zu den verschiedenen Lastenausgleichsmethoden von Traffic Manager finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx) im Abschnitt zu den Lastenausgleichsmethoden in Azure Traffic Manager.

1.  **Stellen Sie Ihre gehosteten Dienste** in der Produktionsumgebung bereit. Informationen zur Entwicklung und Bereitstellung finden Sie unter [In Azure gehostete Dienste](http://msdn.microsoft.com/library/gg432967.aspx).

2.  **Melden Sie sich beim Traffic Manager-Bereich im Verwaltungsportal** unter <http://manage.windowsazure.com> an. Klicken Sie links unten auf der Portalseite auf **Virtuelles Netzwerk**, und wählen Sie im linken Fensterbereich die Option **Traffic Manager** aus.

3.  **Wählen Sie die Richtlinien aus, und klicken Sie auf "Erstellen".** Wählen Sie den Ordner **Policies** in der Navigationsstruktur auf der linken Seite aus, um **Erstellen** in der oberen Symbolleiste zu aktivieren. Wählen Sie **Erstellen**. Das Dialogfeld **Create Traffic Manager policy** wird angezeigt.

    ![Schaltfläche "Erstellen" für Richtlinien](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **Abbildung 1**: Schaltfläche "Erstellen" für Richtlinien

4.  **Wählen Sie ein Abonnement aus.** Richtlinien und Domänen sind mit einem einzigen Abonnement verknüpft.

5.  **Wählen Sie die Failover-Richtlinie als Lastenausgleichsmethode.** Weitere Informationen zu den verschiedenen Lastenausgleichsmethoden von Traffic Manager finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx) im Abschnitt zu den Lastenausgleichsmethoden in Azure Traffic Manager.

6.  **Suchen Sie die gehosteten Dienste, und fügen Sie sie zur Richtlinie hinzu.** Verwenden Sie das Filterfeld, um gehostete Dienste zu suchen, welche die von Ihnen eingegebene Zeichenfolge enthalten. Leeren Sie das Feld, um alle gehosteten Dienste in der Produktion für das in Schritt 4 ausgewählte Abonnement anzuzeigen. Fügen Sie sie mithilfe der Pfeilschaltflächen zur Richtlinie hinzu. Wenn Sie die Lastenausgleichsmethode **Failover** auswählen, ist die Reihenfolge der ausgewählten Dienste wichtig. Der primäre gehostete Dienst befindet sich ganz oben. Verwenden Sie die Aufwärts- und Abwärtspfeile, um die Reihenfolge nach Bedarf anzupassen.

7.  Durch die Überwachung wird sichergestellt, dass kein Datenverkehr an gehostete Dienste, die offline sind, gesendet wird. Das Verwenden einer Failover-Richtlinie ohne Einrichten einer Überwachung ist sinnlos, da der Verkehr an den primären gehosteten Dienst gesendet wird, selbst wenn dieser gehostete Dienst als offline angezeigt wird. Um die gehosteten Dienste zu überwachen, müssen Sie einen bestimmten Pfad und einen Dateinamen angeben. Weitere Informationen finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zum Überwachen von gehosteten Diensten in Azure Traffic Manager.

8.  **Benennen Sie die Traffic Manager-Domäne.** Geben Sie Ihrer Domäne einen eindeutigen Namen. Sie können nur den Präfix für Ihre Domäne angeben. Lassen Sie den Wert für die **DNS-Gültigkeitsdauer (TTL)** auf dem Standardwert. Weitere Informationen zu den Auswirkungen dieser Einstellung finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zu den empfohlenen Methoden für gehostete Dienste und Richtlinien bei der Verwendung von Azure Traffic Manager.

    Das Dialogfeld **Create Traffic Manager policy** sollte in etwa wie im folgenden Beispiel aussehen.

    ![Dialogfeld für Failover-Lastenausgleichsmethode](./media/traffic-manager-configure-settings/Dialog_box_for_Failover_load_balancing_method.png)

    **Abbildung 2**: Dialogfeld für Failover-Lastenausgleichsmethode

9.  **Testen Sie die Traffic Manager-Domäne und die Richtlinie.** Weitere Informationen finden Sie unter [Gewusst wie: Testen einer Azure Traffic Manager-Richtlinie](#howto_test).

10. **Lassen Sie den DNS-Server auf die Traffic Manager-Domäne verweisen.** Sobald Ihre Traffic Manager-Domäne eingerichtet ist und funktioniert, bearbeiten Sie den DNS-Eintrag des autorisierenden DNS-Servers, sodass Ihre Unternehmensdomäne auf die Traffic Manager-Domäne verweist. Weitere Informationen dazu finden Sie unter [Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](#howto_point_company). Mit dem folgenden Befehl wird beispielsweise der gesamte Datenverkehr von **www.contoso.com** an die Traffic Manager-Domäne **contoso.trafficmanager.net** geleitet: `www.contoso.com IN CNAME contoso.trafficmanager.net`

Gewusst wie: Weiterleiten von eingehendem Verkehr an gehostete Dienste basierend auf Netzwerkleistung
-----------------------------------------------------------------------------------------------------

Um Lastenausgleich für gehostete Dienste, die sich in verschiedenen Datencentern rund um den Globus befinden, einzurichten, können Sie eingehenden Verkehr an den nächstgelegenen gehosteten Dienst leiten. Obwohl sich "nächstgelegen" direkt auf die geografische Entfernung beziehen kann, bezieht es sich möglicherweise auch auf den Standort mit der niedrigsten Latenz beim Bearbeiten der Anfrage. Die leistungsbezogene Lastenausgleichsmethode ermöglicht Ihnen eine Verteilung basierend auf Standort und Latenz, kann jedoch nicht die Echtzeitänderungen in der Netzwerkkonfiguration bzw. Last berücksichtigen. Weitere Informationen zu den verschiedenen Lastenausgleichsmethoden von Traffic Manager finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx) im Abschnitt zu den Lastenausgleichsmethoden in Azure Traffic Manager.

Die folgenden Schritte führen Sie durch diesen Prozess:

1.  **Stellen Sie Ihre gehosteten Dienste** in der Produktionsumgebung bereit. Weitere Informationen erhalten Sie unter [Cloud-Dienste](http://msdn.microsoft.com/en-us/library/windowsazure/gg432967.aspx). Lesen Sie außerdem den Abschnitt zu den empfohlenen Methoden für gehostete Dienste und Richtlinien in der [Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

2.  **Melden Sie sich beim Traffic Manager-Bereich im Verwaltungsportal** unter <http://manage.windowsazure.com> an. Klicken Sie links unten auf der Portalseite auf **Virtuelles Netzwerk**, und wählen Sie im linken Fensterbereich die Option **Traffic Manager** aus.

3.  **Wählen Sie die Richtlinien aus, und klicken Sie auf "Erstellen".** Wählen Sie den Ordner **Policies** in der Navigationsstruktur auf der linken Seite aus, um **Erstellen** in der oberen Symbolleiste zu aktivieren. Wählen Sie **Erstellen**. Das Dialogfeld **Create Traffic Manager policy** wird angezeigt.

    ![Schaltfläche "Erstellen" für Richtlinien](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **Abbildung 1**: Schaltfläche "Erstellen" für Richtlinien

4.  **Wählen Sie ein Abonnement aus.** Richtlinien und Domänen sind mit einem einzigen Abonnement verknüpft.

5.  **Wählen Sie die Lastenausgleichsmethode "Leistung" aus.** Weitere Informationen zu den verschiedenen Lastenausgleichsmethoden von Traffic Manager finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx) im Abschnitt zu den Lastenausgleichsmethoden in Azure Traffic Manager.

6.  **Suchen Sie die gehosteten Dienste, und fügen Sie sie zur Richtlinie hinzu.** Verwenden Sie das Filterfeld, um gehostete Dienste zu suchen, welche die von Ihnen eingegebene Zeichenfolge enthalten. Leeren Sie das Feld, um alle gehosteten Dienste in der Produktion für das in Schritt 4 ausgewählte Abonnement anzuzeigen. Fügen Sie sie mithilfe der Pfeilschaltflächen zur Richtlinie hinzu. Die Reihenfolge im Feld **Selected DNS names** hat keinen Einfluss auf diese Lastenausgleichsmethode.

7.  **Richten Sie die Überwachung ein.** Durch die Überwachung wird sichergestellt, dass kein Datenverkehr an gehostete Dienste, die offline sind, gesendet wird. Sie müssen einen bestimmten Pfad und einen Dateinamen einrichten. Weitere Informationen finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zum Überwachen von gehosteten Diensten in Azure Traffic Manager.

8.  **Benennen Sie die Traffic Manager-Domäne.** Geben Sie Ihrer Domäne einen eindeutigen Namen. Sie können nur den Präfix für Ihre Domäne angeben. Lassen Sie den Wert für die **DNS-Gültigkeitsdauer (TTL)** auf dem Standardwert. Weitere Informationen zu den Auswirkungen dieser Einstellung finden Sie unter [Azure Traffic Manager-Übersicht](http://msdn.microsoft.com/en-us/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) im Abschnitt zu den empfohlenen Methoden für gehostete Dienste und Richtlinien bei der Verwendung von Azure Traffic Manager.

    Das Dialogfeld **Create Traffic Manager policy** sollte in etwa wie im folgenden Beispiel aussehen.

    ![Dialogfeld für Leistungs-Lastenausgleichsmethode](./media/traffic-manager-configure-settings/Dialog_box_for_Performance_load_balancing_method.png)

    **Abbildung 2**: Dialogfeld für Leistungs-Lastenausgleichsmethode

9.  **Testen Sie die Traffic Manager-Domäne und die Richtlinie.** Weitere Informationen zum Testen finden Sie unter [Gewusst wie: Testen einer Azure Traffic Manager-Richtlinie](#howto_test).

10. **Lassen Sie den DNS-Server auf die Traffic Manager-Domäne verweisen.** Sobald Ihre Traffic Manager-Domäne eingerichtet ist und funktioniert, bearbeiten Sie den DNS-Eintrag des autorisierenden DNS-Servers, sodass Ihre Unternehmensdomäne auf die Traffic Manager-Domäne verweist. Mit dem folgenden Befehl wird beispielsweise der gesamte Datenverkehr von **www.contoso.com** an die Traffic Manager-Domäne **contoso.trafficmanager.net** geleitet: `www.contoso.com IN CNAME contoso.trafficmanager.net` Weitere Informationen finden Sie unter [Gewusst wie: Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](#howto_point_company).



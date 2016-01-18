<properties
	pageTitle="Azure AD Connect-Synchronisierung: Konfigurieren der Filterung | Microsoft Azure"
	description="Erläutert das Konfigurieren der Filterung bei der Azure AD Connect-Synchronisierung."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/30/2015"
	ms.author="andkjell;markusvi"/>


# Azure AD Connect-Synchronisierung: Konfigurieren der Filterung
Per Filterung können Sie für Ihr lokales Verzeichnis steuern, welche Objekte in Azure AD angezeigt werden. Die Standardkonfiguration deckt alle Objekte in allen Domänen der konfigurierten Gesamtstrukturen ab. Dies ist die für den Normalfall empfohlene Konfiguration. Beispielsweise profitieren Endbenutzer, die Office 365-Workloads verwenden, z. B. Exchange Online und Skype for Business, von einer vollständigen globalen Adressliste, die zum Senden von E-Mails und Anrufen anderer Personen genutzt werden kann. Bei der Standardkonfiguration erhalten diese Benutzer die gleiche Funktionalität wie bei einer lokalen Implementierung von Exchange oder Lync.

In einigen Fällen ist es erforderlich, Änderungen an der Standardkonfiguration vorzunehmen. Hier einige Beispiele:

- Sie planen, die [Azure AD-Multi-Verzeichnistopologie](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory) zu verwenden. Sie müssen einen Filter anwenden, um zu steuern, welches Objekt mit einem bestimmten Azure AD-Verzeichnis synchronisiert werden soll.
- Sie führen ein Pilotprojekt für Azure oder Office 365 aus und benötigen nur eine Teilmenge der Benutzer in Azure AD. In dem kleinen Pilotprojekt müssen Sie nicht unbedingt über eine vollständige globale Adressliste verfügen, um die Funktionen zu demonstrieren.
- Sie haben sehr viele Dienstkonten und andere nicht persönliche Konten, die nicht in Azure AD enthalten sein sollen.
- Aus Compliance-Gründen löschen Sie lokal keine Benutzerkonten, sondern deaktivieren sie nur. In Azure AD sollen aber nur aktive Konten vorhanden sein.

In diesem Artikel wird beschrieben, wie Sie die verschiedenen Filtermethoden konfigurieren.

> [AZURE.IMPORTANT]Microsoft unterstützt die Änderung oder den Einsatz der Azure AD Connect-Synchronisierung außerhalb dieser formal dokumentierten Aktionen nicht. All diese Aktionen können zu einem inkonsistenten oder nicht unterstützten Zustand der Azure AD Connect-Synchronisierung führen. Folglich kann Microsoft keinen technischen Support für derartige Bereitstellungen leisten.

## Grundlagen und wichtige Hinweise
In der Azure AD Connect-Synchronisierung können Sie die Filterung jederzeit aktivieren. Wenn Sie mit einer Standardkonfiguration der Verzeichnissynchronisierung beginnen und dann die Filterung konfigurieren, werden die Objekte, die herausgefiltert werden, nicht mehr mit Azure AD synchronisiert. Dadurch werden alle Objekte in Azure AD, die zuvor synchronisiert, aber dann gefiltert wurden, in Azure AD gelöscht.

Stellen Sie vor dem Vornehmen von Änderungen an der Filterung sicher, dass Sie die [geplante Aufgabe deaktivieren](#disable-scheduled-task). So werden nicht versehentlich Änderungen exportiert, deren Richtigkeit Sie nicht überprüft haben.

Da bei der Filterung sehr viele Objekte gleichzeitig entfernt werden können, sollten Sie darauf achten, dass Ihre neuen Filter korrekt sind, bevor Sie mit dem Exportieren von Änderungen nach Azure AD beginnen. Es wird dringend empfohlen, nach dem Durchführen der Konfigurationsschritte die [Überprüfungsschritte](#apply-and-verify-changes) auszuführen, bevor Sie exportieren und Änderungen an Azure AD vornehmen.

Um das versehentliche Löschen von vielen Objekten zu verhindern, ist das Feature zum[Verhindern versehentlicher Löschungen](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) standardmäßig aktiviert. Wenn Sie aufgrund einer Filterung viele Objekte löschen (standardmäßig 500), müssen Sie die Schritte in diesem Artikel ausführen, damit die Löschvorgänge auch für Azure AD gelten.

Wenn Sie einen älteren Build als November 2015 nutzen ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)), eine Änderung an der Filterkonfiguration vornehmen und die Synchronisierung von Kennwörtern verwenden, müssen Sie eine vollständige Synchronisierung aller Kennwörter auslösen, nachdem Sie die Konfiguration abgeschlossen haben. Informationen zu Schritten zum Auslösen einer vollständigen Kennwortsynchronisierung finden Sie unter [Auslösen einer vollständigen Synchronisierung aller Kennwörter](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Falls Sie Version 1.0.9125 oder höher verwenden, wird mit der normalen Aktion **Vollständige Synchronisierung** auch berechnet, ob Kennwörter synchronisiert werden sollen. Dieser zusätzliche Schritt ist nicht mehr erforderlich.

Wenn **Benutzer**objekte in Azure AD aufgrund eines Filterungsfehlers versehentlich gelöscht wurden, können Sie die Benutzerobjekte in Azure AD neu erstellen, indem Sie Ihre Filterkonfigurationen entfernen und dann Ihre Verzeichnisse erneut synchronisieren. Die Benutzer werden dann aus dem Papierkorb in Azure AD wiederhergestellt. Das Löschen anderer Objekttypen kann aber nicht rückgängig gemacht werden. Wenn Sie beispielsweise eine Sicherheitsgruppe versehentlich löschen, die als Zugriffssteuerungsliste (ACL) für eine Ressource verwendet wurde, können die Gruppe und die zugehörigen ACLs nicht wiederhergestellt werden.

Von Azure AD Connect werden nur Objekte gelöscht, die einmal als zum Bereich gehörend betrachtet wurden. Wenn in Azure AD Objekte enthalten sind, die von einem anderen Synchronisierungsmodul erstellt wurden und nicht Teil des Bereichs sind, werden sie durch das Hinzufügen der Filterung nicht entfernt. Wenn Sie beispielsweise mit einem DirSync-Server beginnen, mit dem eine vollständige Kopie Ihres gesamten Verzeichnisses in Azure AD erstellt wurde, und einen neuen Azure AD Connect-Synchronisierungsserver parallel mit der von Beginn an aktivierten Filterung installieren, werden die von DirSync erstellten zusätzlichen Objekte nicht entfernt.

Die Filterkonfigurationen werden beibehalten, wenn Sie eine neuere Version von Azure AD Connect installieren oder ein Upgrade darauf durchführen. Es ist immer eine bewährte Methode, vor dem Ausführen des ersten Synchronisierungszyklus zu verifizieren, dass die Konfiguration nach einem Upgrade auf eine neuere Version nicht versehentlich geändert wurde.

Wenn Sie über mehrere Gesamtstrukturen verfügen, müssen die in diesem Thema beschriebenen Filterkonfigurationen auf jede Gesamtstruktur angewendet werden (vorausgesetzt, für alle soll die gleiche Konfiguration gelten).

### Deaktivieren von geplanten Aufgaben
Führen Sie die folgenden Schritte aus, um die geplante Aufgabe zu deaktivieren, mit der jeweils im Abstand von drei Stunden ein Synchronisierungszyklus ausgelöst wird:

- Starten Sie im Menü „Start“ den **Scheduler**.
- Suchen Sie direkt unterhalb der **Aufgabenplanungsbibliothek** nach der Aufgabe mit dem Namen **Azure AD Sync Scheduler**, klicken Sie mit der rechten Maustaste, und wählen Sie **Deaktivieren**. ![Aufgabenplanung](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)
- Sie können jetzt Konfigurationsänderungen vornehmen und das Synchronisierungsmodul manuell über die Konsole **Synchronization Service Manager** ausführen.

Nachdem Sie alle Änderungen an der Filterung durchgeführt haben, sollten Sie nicht vergessen, die Aufgabe wieder zu **aktivieren**.

## Filteroptionen
Die folgenden Filterkonfigurationstypen können auf das Tool für die Verzeichnissynchronisierung angewendet werden:

- [**Gruppenbasiert**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): Die auf einer einzelnen Gruppe basierende Filterung kann nur bei der Erstinstallation mit dem Installations-Assistenten konfiguriert werden. Sie wird in diesem Thema nicht weiter behandelt.

- [**Domänenbasiert**](#domain-based-filtering): Bei dieser Option können Sie auswählen, welche Domänen mit Azure AD synchronisiert werden. Außerdem können Sie Domänen aus der Konfiguration des Synchronisierungsmoduls hinzufügen und entfernen, wenn Sie Änderungen an Ihrer lokalen Infrastruktur vornehmen, nachdem Sie die Azure AD Connect-Synchronisierung installiert haben.

- [**Basierend auf Organisationseinheiten**](#organizational-unitbased-filtering): Bei dieser Filteroption können Sie auswählen, welche Organisationseinheiten mit Azure AD synchronisiert werden. Diese Option ist für alle Objekttypen in den ausgewählten Organisationseinheiten vorhanden.

- [**Attributbasiert**](#attribute-based-filtering): Bei dieser Option können Sie Objekte basierend auf den Attributwerten der Objekte filtern. Sie können auch unterschiedliche Filter für unterschiedliche Objekttypen verwenden.

Sie können gleichzeitig mehrere Filteroptionen verwenden. Beispielsweise können Sie die auf Organisationseinheiten basierende Filterung verwenden, um nur die Objekte einer Organisationseinheit einzubeziehen, und gleichzeitig die attributbasierte Filterung, um die Objekte weiter zu filtern. Wenn Sie mehrere Filtermethoden verwenden, wird für die Filter zwischen den Filtern ein logisches „Und“ genutzt.

## Domänenbasierte Filterung
Dieser Abschnitt enthält die Schritte, die Sie ausführen müssen, um den Filter für Ihre Domäne zu konfigurieren. Wenn Sie nach der Installation von Azure AD Connect in der Gesamtstruktur Domänen hinzugefügt oder entfernt haben, müssen Sie auch die Konfiguration der Filterung aktualisieren.

Die Konfiguration der domänenbasierten Filterung umfasst folgende Schritte:

- [Wählen Sie die Domänen aus](#select-domains-to-be-synchronized), die in die Synchronisierung einbezogen werden sollen.
- Passen Sie für jede hinzugefügte und entfernte Domäne die [Ausführungsprofile](#update-run-profiles) an.
- [Wenden Sie die Änderungen an, und überprüfen Sie sie](#apply-and-verify-changes).

### Auswählen der zu synchronisierenden Domänen
**Um den Filter für die Domäne festzulegen, führen Sie die folgenden Schritte aus:**

- Melden Sie sich an dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
- Starten Sie den **Synchronisierungsdienst** über das Menü „Start“.
- Wählen Sie **Connectors** aus, und wählen Sie in der Liste **Connectors** den Connector mit dem Typ **Active Directory-Domänendienste** aus. Wählen Sie unter **Aktionen** die Option **Eigenschaften**. ![Connectoreigenschaften](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
-  Klicken Sie auf **Verzeichnispartitionen konfigurieren**.
- Aktivieren bzw. deaktivieren Sie die Domänen in der Liste **Verzeichnispartitionen auswählen** je nach Bedarf. Achten Sie darauf, dass nur die Partitionen ausgewählt sind, die Sie synchronisieren möchten. ![Partitionen](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) Wenn Sie Ihre lokale AD-Infrastruktur geändert und der Gesamtstruktur Domänen hinzugefügt oder daraus entfernt haben, können Sie auf die Schaltfläche **Aktualisieren** klicken, um eine aktualisierte Liste zu erhalten. Beim Aktualisieren werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Geben Sie hierbei Anmeldeinformationen an, die Ihnen Lesezugriff auf Ihr lokales Active Directory-Verzeichnis ermöglichen. Es muss sich nicht um den Benutzer handeln, der im Dialogfeld bereits angegeben ist. ![Aktualisierung erforderlich](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)
- Schließen Sie nach Abschluss des Vorgangs das Dialogfeld **Eigenschaften**, indem Sie auf **OK** klicken. Wenn Sie Domänen aus der Gesamtstruktur entfernt haben, wird per Popupmeldung darauf hingewiesen, dass eine Domäne entfernt wurde und diese Konfiguration bereinigt wird.
- Fahren Sie mit dem Anpassen der [Ausführungsprofile](#update-run-profiles) fort.

### Aktualisieren von Ausführungsprofilen
Wenn Sie den Domänenfilter aktualisiert haben, müssen Sie auch die Ausführungsprofile aktualisieren:

- Stellen Sie in der Liste **Connectors** sicher, dass der Connector ausgewählt ist, den Sie im vorherigen Schritt geändert haben. Wählen Sie unter **Aktionen** die Option **Ausführungsprofile konfigurieren**. ![Connector-Ausführungsprofile](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)

Sie müssen die folgenden Profile anpassen:

- Vollständiger Import
- Vollständige Synchronisierung
- Deltaimport
- Deltasynchronisierung
- Export

Führen Sie für jedes der fünf Profile für eine **hinzugefügte** Domäne jeweils die folgenden Schritte aus:

- Wählen Sie das Ausführungsprofil aus, und klicken Sie auf **Neuer Schritt**.
- Wählen Sie auf der Seite **Schritt konfigurieren** in der Dropdownliste **Typ** den Schritttyp mit dem gleichen Namen wie für das Profil aus, das Sie konfigurieren. Klicken Sie anschließend auf **Weiter**. ![Connector-Ausführungsprofile](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)
- Wählen Sie auf der Seite **Connectorkonfiguration** in der Dropdownliste **Partition** den Namen der Domäne aus, die Sie dem Domänenfilter hinzugefügt haben. ![Connector-Ausführungsprofile](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)
- Um das Dialogfeld **Ausführungsprofile konfigurieren** zu schließen, klicken Sie auf **Fertig stellen**.

Führen Sie für jedes der fünf Profile für eine **entfernte** Domäne jeweils die folgenden Schritte aus:

- Wählen Sie das Ausführungsprofil aus.
- Wenn der **Wert** des Attributs **Partition** eine GUID ist, wählen Sie den Ausführungsschritt aus und klicken auf **Schritt löschen**. ![Connector-Ausführungsprofile](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)

Das Endergebnis sollte sein, dass jede Domäne, die Sie synchronisieren möchten, als Schritt in jedem Ausführungsprofil aufgelistet wird.

Um das Dialogfeld **Ausführungsprofile konfigurieren** zu schließen, klicken Sie auf **OK**.

- Zum Abschließen der Konfiguration [wenden Sie die Änderungen an und überprüfen sie](#apply-and-verify-changes).

## Filterung basierend auf Organisationseinheiten
**Führen Sie zum Konfigurieren der auf Organisationseinheiten basierenden Filterung die folgenden Schritte aus:**

- Melden Sie sich an dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
- Starten Sie den **Synchronisierungsdienst** über das Menü „Start“.
- Wählen Sie **Connectors** aus, und wählen Sie in der Liste **Connectors** den Connector mit dem Typ **Active Directory-Domänendienste** aus. Wählen Sie unter **Aktionen** die Option **Eigenschaften**. ![Connectoreigenschaften](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)
-  Klicken Sie auf **Verzeichnispartitionen konfigurieren**, wählen Sie die Domäne, die Sie konfigurieren möchten, und klicken Sie dann auf **Container**.
- Geben Sie bei Aufforderung Anmeldeinformationen an, die Ihnen den Lesezugriff auf Ihr lokales Active Directory-Verzeichnis ermöglichen. Es muss sich nicht um den Benutzer handeln, der im Dialogfeld bereits angegeben ist.
- Deaktivieren Sie im Dialogfeld **Container auswählen** die Organisationseinheiten, die Sie nicht mit dem Cloudverzeichnis synchronisieren möchten, und klicken Sie auf **OK**. ![Organisationseinheit](./media/active-directory-aadconnectsync-configure-filtering/ou.png)
	- Der Container **Computer** sollte ausgewählt sein, damit die Synchronisierung Ihrer Windows 10-Computer mit Azure AD erfolgreich ist. Falls sich die einer Domäne angehörenden Computer in anderen Organisationseinheiten befinden, sollten Sie sicherstellen, dass sie ausgewählt sind.
	- Der Container **ForeignSecurityPrincipals** sollte ausgewählt sein, wenn Sie über mehrere Gesamtstrukturen mit Vertrauensstellungen verfügen. Dadurch kann die gesamtstrukturübergreifende Sicherheitsgruppenmitgliedschaft aufgelöst werden.
	- Die Organisationseinheit **RegisteredDevices** sollte ausgewählt sein, wenn Sie das Feature für das Geräterückschreiben aktiviert haben. Falls Sie ein anderes Feature für das Geräterückschreiben verwenden, z. B. das Gruppenrückschreiben, sollten Sie sicherstellen, dass diese Speicherorte ausgewählt sind.
	- Wählen Sie eine beliebige andere Organisationseinheit aus, in der Benutzer, iNetOrgPersons, Gruppen, Kontakte und Computer enthalten sind. In der obigen Abbildung sind diese Elemente alle in der Organisationseinheit „ManagedObjects“ enthalten.
- Schließen Sie nach Abschluss des Vorgangs das Dialogfeld **Eigenschaften**, indem Sie auf **OK** klicken.
- Zum Abschließen der Konfiguration [wenden Sie die Änderungen an und überprüfen sie](#apply-and-verify-changes).

## Attributbasierte Filterung
Stellen Sie sicher, dass Sie den Build vom November 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) oder höher verwenden, damit diese Schritte funktionieren.

Die attributbasierte Filterung ist die flexibelste Möglichkeit zum Filtern von Objekten. Sie können die hohe Leistungsfähigkeit der [deklarativen Bereitstellung](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) nutzen, um nahezu jeden Aspekt der Synchronisierung von Objekten mit Azure AD zu steuern.

Die Filterung kann sowohl in [eingehender](#inbound-filtering) Richtung von Active Directory zum Metaverse als auch in [ausgehender](#outbound-filtering) Richtung vom Metaverse zu Azure AD angewendet werden. Es wird empfohlen, die Filterung in eingehender Richtung anzuwenden, da dies am einfachsten zu verwalten ist. Die ausgehende Filterung sollte nur verwendet werden, wenn Objekte aus mehreren Gesamtstrukturen verknüpft werden müssen, bevor die Auswertung stattfinden kann.

### Eingehende Filterung
Bei der eingehenden Filterung wird die Standardkonfiguration genutzt, bei der für an AAD gesendete Objekte das Metaverse-Attribut „cloudFiltered“ nicht auf einen Wert festgelegt sein darf, damit die Synchronisierung erfolgen kann. Wenn der Wert dieses Attributs auf **True** festgelegt ist, wird das Objekt nicht synchronisiert. Es sollte nicht standardmäßig auf **False** festgelegt sein. Um sicherzustellen, dass über andere Regeln ein Wert beigetragen werden kann, sollte dieses Attribut nur über die Werte **True** oder **NULL** (nicht vorhanden) verfügen.

Bei der eingehenden Filterung nutzen wir die Leistungsfähigkeit des **Bereichs**, um zu ermitteln, welche Objekte synchronisiert werden sollen. Hierbei nehmen Sie die Anpassungen vor, um die Anforderungen Ihres Unternehmens zu erfüllen. Das Bereichsmodul verfügt über die Elemente **group** (Gruppe) und **clause** (Klausel), um zu ermitteln, ob eine Synchronisierungsregel Teil des Bereichs sein soll. Eine **Gruppe** enthält eine oder mehrere **Klauseln**. Ein logisches „Und“ wird zwischen mehreren Klauseln und ein logisches „Oder“ zwischen mehreren Gruppen verwendet.

Beispiel: ![Bereich](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Lesen Sie dieses Beispiel wie folgt: **(department = IT) OR (department = Sales AND c = US)**.

In den unten angegebenen Beispielen und Schritten verwenden wir das Benutzerobjekt als Beispiel, aber Sie können es für alle Objekttypen nutzen.

In den folgenden Beispielen beginnen die verwendeten Rangfolgenwerte bei 500. So wird sichergestellt, dass sie nach den standardmäßigen Regeln (niedrigere Rangfolge, höherer numerischer Wert) ausgewertet werden.

#### Negative Filterung („do not sync these“)
Im folgenden Beispiel werden alle Benutzer herausgefiltert (nicht synchronisiert), bei denen **extensionAttribute15** den Wert **NoSync** hat.

- Melden Sie sich an dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
- Öffnen Sie im Menü „Start“ den **Synchronisierungsregel-Editor**.
- Stellen Sie sicher, dass **Eingehend** ausgewählt ist, und klicken Sie auf **Neue Regel hinzufügen**.
- Geben Sie der Regel einen aussagekräftigen Namen, z. B. „*In from AD – User DoNotSyncFilter*“. Wählen Sie die richtige Gesamtstruktur, **User** als **CS-Objekttyp** und **Person** als **MV-Objekttyp** aus. Wählen Sie **Join** als **Verknüpfungsart** aus, und geben Sie als Rangfolge einen Wert ein, der zurzeit nicht von einer anderen Synchronisierungsregel verwendet wird (z. B. 500). Klicken Sie dann auf **Weiter**. ![Eingehend 1 Beschreibung](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)
- Klicken Sie unter **Bereichsfilter** auf **Gruppe hinzufügen**, klicken Sie auf **Klausel hinzufügen**, und wählen Sie dann unter "Attribut" die Option **ExtensionAttribute15** aus. Stellen Sie sicher, dass der Operator auf **EQUAL** festgelegt ist, und geben Sie dann den Wert **NoSync** in das Feld „Wert“ ein. Klicken Sie auf **Weiter**. ![Eingehend 2 Bereich](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)
- Lassen Sie die **Joinregeln** leer, und klicken Sie dann auf **Weiter**.
- Klicken Sie auf **Transformation hinzufügen**, legen Sie **FlowType** auf **Konstante** fest, wählen Sie **cloudFiltered** als Zielattribut aus, und geben Sie dann im Feld „Quelltext“ den Wert **True** ein. Klicken Sie auf **Hinzufügen**, um die Regel zu speichern. ![Eingehend 3 Transformation](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
- Zum Abschließen der Konfiguration [wenden Sie die Änderungen an und überprüfen sie](#apply-and-verify-changes).

#### Positive Filterung („only sync these“)
Das Ausdrücken der positiven Filterung kann mit mehr Aufwand verbunden sein. Sie müssen hierbei nämlich auch Objekte berücksichtigen, bei denen die Synchronisierung nicht offensichtlich ist, z. B. Konferenzräume.

Die positive Filterung erfordert zwei Synchronisierungsregeln. Eine Regel (oder mehrere) mit dem richtigen Bereich der zu synchronisierenden Objekte und eine zweite Synchronisierungsregel, die alles abdeckt. Mit der zweiten Regel werden alle Objekte herausgefiltert, die noch nicht als Objekt identifiziert wurden, das synchronisiert werden soll.

Im folgenden Beispiel werden nur Benutzerobjekte synchronisiert, bei denen das department-Attribut den Wert **Sales** hat.

- Melden Sie sich an dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
- Öffnen Sie im Menü „Start“ den **Synchronisierungsregel-Editor**.
- Stellen Sie sicher, dass **Eingehend** ausgewählt ist, und klicken Sie auf **Neue Regel hinzufügen**.
- Geben Sie der Regel einen aussagekräftigen Namen, z. B. „*In from AD – User Sales sync*“. Wählen Sie die richtige Gesamtstruktur, **User** als **CS-Objekttyp** und **Person** als **MV-Objekttyp** aus. Wählen Sie **Join** als **Verknüpfungsart** aus, und geben Sie als Rangfolge einen Wert ein, der zurzeit nicht von einer anderen Synchronisierungsregel verwendet wird (z. B. 501). Klicken Sie dann auf **Weiter**. ![Eingehend 4 Beschreibung](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)
- Klicken Sie unter **Bereichsfilter** auf **Gruppe hinzufügen**, klicken Sie auf **Klausel hinzufügen**, und wählen Sie dann unter „Attribut“ die Option **department** aus. Stellen Sie sicher, dass der Operator auf **EQUAL** festgelegt ist, und geben Sie dann den Wert **Sales** in das Feld „Wert“ ein. Klicken Sie auf **Weiter**. ![Eingehend 5 Bereich](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)
- Lassen Sie die **Joinregeln** leer, und klicken Sie dann auf **Weiter**.
- Klicken Sie auf **Transformation hinzufügen**, legen Sie **FlowType** auf **Konstante** fest, wählen Sie **cloudFiltered** als Zielattribut aus, und geben Sie dann im Feld „Quelltext“ den Wert **False** ein. Klicken Sie auf **Hinzufügen**, um die Regel zu speichern. ![Eingehend 6 Transformation](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) Dies ist ein Sonderfall, in dem „cloudFiltered“ explizit auf „False“ festgelegt wird.

Wir müssen jetzt die Synchronisierungsregel „catch-all“ erstellen, die alles abdeckt.

- Geben Sie der Regel einen aussagekräftigen Namen, z. B. „*In from AD – User Catch-all filter*“. Wählen Sie die richtige Gesamtstruktur, **User** als **CS-Objekttyp** und **Person** als **MV-Objekttyp** aus. Wählen Sie **Join** als **Verknüpfungsart** aus, und geben Sie als Rangfolge einen Wert ein, der zurzeit nicht von einer anderen Synchronisierungsregel verwendet wird (z. B. 600). Wir haben einen höheren Vorrangigkeitswert (geringere Vorrangigkeit) als in der vorherigen Synchronisierungsregel ausgewählt, aber gleichzeitig Platz gelassen. So können wir später mehr Synchronisierungsregeln für die Filterung hinzufügen, wenn wir mit der Synchronisierung weiterer Abteilungen beginnen möchten. Klicken Sie auf **Weiter**. ![Eingehend 7 Beschreibung](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
- Lassen Sie **Bereichsfilter** leer, und klicken Sie auf **Weiter**. Ein leerer Filter gibt an, dass die Regel auf alle Objekte angewendet werden soll.
- Lassen Sie die **Joinregeln** leer, und klicken Sie dann auf **Weiter**.
- Klicken Sie auf **Transformation hinzufügen**, legen Sie **FlowType** auf **Konstante** fest, wählen Sie **cloudFiltered** als Zielattribut aus, und geben Sie dann im Feld „Quelltext“ den Wert **True** ein. Klicken Sie auf **Hinzufügen**, um die Regel zu speichern. ![Eingehend 3 Transformation](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
- Zum Abschließen der Konfiguration [wenden Sie die Änderungen an und überprüfen sie](#apply-and-verify-changes).

Bei Bedarf können wir weitere Regeln des ersten Typs erstellen, bei denen wir immer mehr Objekte in die Synchronisierung einbeziehen.

### Ausgehende Filterung
In einigen Fällen ist es erforderlich, die Filterung erst auszuführen, nachdem die Objekte dem Metaverse hinzugefügt wurden. Es kann z. B. erforderlich sein, das E-Mail-Attribut aus der Ressourcengesamtstruktur und das Attribut „userPrincipalName“ aus der Kontogesamtstruktur zu untersuchen, um zu ermitteln, ob ein Objekt synchronisiert werden soll. In diesen Fällen wird die Filterung für die ausgehende Regel erstellt.

In diesem Beispiel wird die Filterung so geändert, dass nur Benutzer synchronisiert werden, deren Attribute "mail" und "userPrincipalName" auf "@contoso.com" enden:

- Melden Sie sich an dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
- Öffnen Sie im Menü „Start“ den **Synchronisierungsregel-Editor**.
- Klicken Sie unter „Regeltyp“ dann auf **Ausgehend**.
- Suchen Sie nach der Regel mit dem Namen **Out to AAD – User Join SOAInAD**. Klicken Sie auf **Bearbeiten**.
- Wählen Sie im Popupfenster die Antwort **Ja**, um eine Kopie der Regel zu erstellen.
- Ändern Sie auf der Seite **Beschreibung** die Vorrangigkeit in einen nicht verwendeten Wert, z. B. 50.
- Klicken Sie im linken Navigationsbereich auf **Bereichsfilter**. Klicken Sie auf **Klausel hinzufügen**, und wählen Sie **mail** als Attribut aus. Wählen Sie **ENDSWITH** als Operator und **@contoso.com** als Werttyp aus. Klicken Sie auf **Klausel hinzufügen**, und wählen Sie **userPrincipalName** als Attribut aus. Wählen Sie **ENDSWITH** als Operator und **@contoso.com** als Werttyp aus.
- Klicken Sie auf **Speichern**.
- Zum Abschließen der Konfiguration [wenden Sie die Änderungen an und überprüfen sie](#apply-and-verify-changes).

## Anwenden und Überprüfen von Änderungen
Nachdem Sie Änderungen an der Konfiguration vorgenommen haben, müssen diese Änderungen auf die Objekte angewendet werden, die bereits im System vorhanden sind. Es kann auch sein, dass derzeit nicht im Synchronisierungsmodul enthaltene Objekte verarbeitet werden sollen und das Auslesen aus dem Quellsystem erneut durchgeführt werden muss, um den Inhalt zu verifizieren.

Wenn Sie die Konfiguration per Filterung nach **Domäne** oder **Organisationseinheit** geändert haben, müssen Sie einen **vollständigen Import** gefolgt von einer **Deltasynchronisierung** durchführen.

Falls Sie die Konfiguration per Filterung nach dem **Attribut** geändert haben, müssen Sie die **vollständige Synchronisierung** durchführen.

Führen Sie die folgenden Schritte aus:

- Starten Sie den **Synchronisierungsdienst** über das Menü „Start“.
- Wählen Sie **Connectors** und in der Liste **Connectors** den Connector aus, für den Sie vorher eine Konfigurationsänderung vorgenommen haben. Wählen Sie unter **Aktionen** die Option **Ausführen**. ![Connectorausführung](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)
- Wählen Sie unter **Ausführungsprofile** den im vorherigen Abschnitt erwähnten Vorgang aus. Falls Sie zwei Aktionen ausführen müssen, führen Sie die zweite Aktion aus, nachdem die erste abgeschlossen ist (die Spalte **State** ist für den ausgewählten Connector auf **Idle** festgelegt).

Nach der Synchronisierung werden alle Änderungen für den Export bereitgestellt. Bevor wir die Änderungen in Azure AD tatsächlich vornehmen, möchten wir sicherstellen, dass alle Änderungen richtig sind.

- Starten Sie eine Eingabeaufforderung, und wechseln Sie zu: `%Program Files%\Microsoft Azure AD Sync\bin`
- Führen Sie diesen Befehl aus: `csexport "Name of Connector" %temp%\export.xml /f:x` Sie finden den Namen des Connectors im Synchronisierungsdienst. Für Azure AD lautet dieser ähnlich wie "contoso.com – AAD".
- Führen Sie diesen Befehl aus: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
- Sie verfügen jetzt im Ordner "%temp%" über eine Datei namens "export.csv", die in Microsoft Excel untersucht werden kann. Diese Datei enthält alle Änderungen, die exportiert werden sollen.
- Nehmen Sie erforderliche Änderungen an den Daten oder der Konfiguration vor, und führen Sie die oben genannten Schritte erneut aus (Importieren, Synchronisieren und Überprüfen), bis Sie die Änderungen erhalten, die Sie exportieren möchten.

Wenn Sie zufrieden sind, können Sie die Änderungen nach Azure AD exportieren.

- Wählen Sie **Connectors** und in der Liste **Connectors** den Azure AD-Connector aus. Wählen Sie unter **Aktionen** die Option **Ausführen**.
- Wählen Sie unter **Ausführungsprofile** die Option **Exportieren**.
- Falls im Rahmen Ihrer Konfigurationsänderungen viele Objekte geändert werden, wird für den Export ein Fehler angezeigt, sofern die Zahl die konfigurierte Schwelle (standardmäßig 500) übersteigt. In diesem Fall müssen Sie das Feature [Verhindern von versehentlichen Löschungen](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) vorübergehend deaktivieren.

Jetzt ist es an der Zeit, den Scheduler wieder zu aktivieren.

- Starten Sie im Menü „Start“ den **Scheduler**.
- Suchen Sie direkt unterhalb der **Aufgabenplanungsbibliothek** nach der Aufgabe mit dem Namen **Azure AD Sync Scheduler**, klicken Sie mit der rechten Maustaste, und wählen Sie **Aktivieren**.

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0107_2016-->
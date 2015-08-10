<properties
	pageTitle="Azure AD Connect-Synchronisierung: Konfigurieren der Filterung"
	description="Erläutert das Konfigurieren der Filterung bei der Azure AD Connect-Synchronisierung"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect-Synchronisierung: Konfigurieren der Filterung

In der Azure AD Connect-Synchronisierung können Sie die Filterung jederzeit aktivieren. Wenn Sie bereits die Standardkonfigurationen der Directory-Synchronisierung ausgeführt und dann die Filterung konfiguriert haben, werden die Objekte, die herausgefiltert werden, nicht mehr mit Azure AD synchronisiert. Dadurch werden alle Objekte in Azure AD, die zuvor synchronisiert, aber dann gefiltert wurden, in Azure AD gelöscht. Wenn Objekte aufgrund eines Filterungsfehlers versehentlich gelöscht wurden, können Sie die Objekte erneut in Azure AD erstellen, indem Sie Ihre Filterkonfigurationen entfernen und dann Ihre Verzeichnisse erneut synchronisieren.

> [AZURE.IMPORTANT]Microsoft unterstützt die Änderung oder den Einsatz der Azure AD Connect-Synchronisierung außerhalb dieser formal dokumentierten Aktionen nicht. All diese Aktionen können zu einem inkonsistenten oder nicht unterstützten Zustand der Azure AD Connect-Synchronisierung führen. Folglich kann Microsoft keinen technischen Support für derartige Bereitstellungen leisten.
 
Mit Ausnahme der ausgehenden attributbasierten Filterung werden die Konfigurationen beibehalten, wenn Sie eine neuere Version von Azure AD Connect installieren oder ein Upgrade durchführen. Es ist immer eine bewährte Methode, vor dem Ausführen des ersten Synchronisierungszyklus zu verifizieren, dass die Konfiguration nach einem Upgrade auf eine neuere Version nicht versehentlich geändert wurde.


## Filteroptionen

> [AZURE.NOTE]In diesem Thema wird "SourceAD" als Name für Ihren Active Directory-Domänendienstconnector verwendet. Wenn Sie mehrere Gesamtstrukturen besitzen, benötigen Sie einen Connector pro Gesamtstruktur, und die Konfiguration muss für jede Gesamtstruktur wiederholt werden.
 
Die folgenden drei Filterkonfigurationstypen können auf das AD-Synchronisierungstool angewendet werden:

- **Domänenbasiert**: Verwenden Sie diesen Filtertyp, um die Eigenschaften des SourceAD-Connectors in der Azure AD Connect-Synchronisierung zu verwalten. Mit diesem Typ können Sie auswählen, welche Domänen zum Synchronisieren mit Azure AD zulässig sind.

- **Konfigurieren der auf Organisationseinheiten basierenden Filterung**: Verwenden Sie diesen Filtertyp, um die Eigenschaften des SourceAD-Connectors in der Azure AD Connect-Synchronisierung zu verwalten. Mit diesem Typ können Sie auswählen, welche Organisationseinheiten zum Synchronisieren mit Azure AD zulässig sind.

- **Attributbasiert**: Verwenden Sie diese Filtermethode, um attributbasierte Filter anzugeben. So können Sie steuern, welche Objekte mit der Cloud synchronisiert werden sollen.

## Konfigurieren der domänenbasierten Filterung

Dieser Abschnitt enthält die Schritte, die Sie ausführen müssen, um den Filter für Ihre Domäne zu konfigurieren.


> [AZURE.NOTE]Wenn Sie Ihren Domänenfilter geändert haben, müssen Sie auch Ihre Ausführungsprofile aktualisieren.
 

**Um den Filter für die Domäne festzulegen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.

2. Tippen oder klicken Sie in **Start** auf **Synchronisierungsdienst**, um den **Synchronisierungsdienst-Manager** aufzurufen. <br>

3. Klicken Sie zum Öffnen der Ansicht "Connectors" im Menü **Tools** auf **Connectors**.

4. Wählen Sie in der Liste **Connectors** den Connector mit **Active Directory-Domänendienst** als **Typ**.

5. Um das Dialogfeld **Eigenschaften** zu öffnen, klicken Sie im Menü **Aktionen** auf **Eigenschaften**.

6. Klicken Sie auf **Verzeichnispartitionen konfigurieren**.

7. Stellen Sie in der Liste **Verzeichnispartitionen auswählen** sicher, dass nur die Partitionen ausgewählt sind, die Sie synchronisieren möchten. <br> \>[AZURE.Note]Um eine Domäne aus dem Synchronisierungsprozess zu entfernen, deaktivieren Sie deren Kontrollkästchen.

8. Klicken Sie auf **OK**, um das Dialogfeld **Eigenschaften** zu schließen.


Wenn Sie den Domänenfilter aktualisiert haben, müssen Sie auch folgende Ausführungsprofile aktualisieren:

- Vollständiger Import
- Vollständige Synchronisierung
- Deltaimport
- Deltasynchronisierung
- Export


Wenn Sie eine Partition aus der Liste der Verzeichnispartitionen entfernt haben, müssen Sie sicherstellen, dass alle Ausführungsprofilschritte, die diese Partition referenzieren, ebenfalls entfernt werden.

**Um einen Schritt aus einem Ausführungsprofil zu entfernen, führen Sie die folgenden Schritte aus:**

1. Wählen Sie in der Liste **Connectors** den Connector mit **Active Directory-Domänendienst** als **Typ**.

2. Um das Dialogfeld **Ausführungsprofile konfigurieren für** zu öffnen, klicken Sie im Menü **Aktionen** auf **Ausführungsprofile konfigurieren**.

3. Wählen Sie in der Liste **Connectorausführungsprofile** das zu konfigurierende Ausführungsprofil.

4. Führen Sie für jeden Schritt in der Schrittdetailliste die folgenden Schritte aus:

     4\.1. Falls erforderlich, klicken Sie auf den Schritt, um die Schrittdetails zu erweitern.

     4\.2. Wenn der **Wert** des Attributs **Partition** eine GUID ist, klicken Sie auf "Schritt löschen".

5. Um das Dialogfeld **Ausführungsprofile konfigurieren für** zu schließen, klicken Sie auf **OK**.

Wenn Sie der Liste der Verzeichnispartitionen eine Partition hinzugefügt haben, müssen Sie sicherstellen, dass für diese Partition in jedem der Ausführungsprofile in der obigen Liste ein Ausführungsprofilschritt verfügbar ist.

**Um einem Ausführungsprofil einen Schritt hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Wählen Sie in der Liste **Connectors** den Connector mit **Active Directory-Domänendienst** als **Typ**.

2. Um das Dialogfeld **Ausführungsprofile konfigurieren für** zu öffnen, klicken Sie im Menü **Aktionen** auf **Ausführungsprofile konfigurieren**.

3. Wählen Sie in der Liste **Connectorausführungsprofile** das zu konfigurierende Ausführungsprofil.

4. Um das Dialogfeld **Ausführungsprofil konfigurieren** zu öffnen, klicken Sie auf **Neuer Schritt**.

5. Wählen Sie auf der Seite **Schritt konfigurieren** in der Schritttypliste den Schritttyp aus, und klicken Sie dann auf **Weiter**.

6. Wählen Sie auf der Seite **Connectorkonfiguration** in der Liste **Partition** den Namen der Partition, die Sie dem Domänenfilter hinzugefügt haben.

7. Um das Dialogfeld **Ausführungsprofile konfigurieren** zu schließen, klicken Sie auf **Fertig stellen**.

8. Um das Dialogfeld **Ausführungsprofile konfigurieren für** zu schließen, klicken Sie auf **OK**.

 

## Konfigurieren der auf Organisationseinheiten basierenden Filterung

**Führen Sie zum Konfigurieren der auf Organisationseinheiten basierenden Filterung die folgenden Schritte aus:**

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.

2. Tippen oder klicken Sie in **Start** auf **Synchronisierungsdienst**, um den **Synchronisierungsdienst-Manager** aufzurufen.<br>

3. Klicken Sie im **Synchronisierungsdienst-Manager** auf **Connectors**, und doppelklicken Sie dann auf **SourceAD**.

4. Klicken Sie auf **Verzeichnispartitionen konfigurieren**, wählen Sie die Domäne, die Sie konfigurieren möchten, und klicken Sie dann auf **Container**.

5. Geben Sie bei Aufforderung die Domänenanmeldeinformationen für die lokale Active Directory-Gesamtstruktur ein.<br> \>[AZURE.NOTE]Wenn das Dialogfeld zur Eingabe der Anmeldeinformationen angezeigt wird, wird das zum Importieren aus und Exportieren nach AD DS verwendete Konto angezeigt. Wenn Sie das Kennwort für das Konto nicht kennen, können Sie ein anderes Konto eingeben. Das Konto, das Sie verwenden, muss über Leseberechtigungen für die Domäne verfügen, die gerade konfiguriert wird.

6. Deaktivieren Sie im Dialogfeld **Container auswählen** die Organisationseinheiten, die Sie nicht mit dem Cloudverzeichnis synchronisieren möchten, und klicken Sie auf **OK**.

7. Klicken Sie auf der Seite **Eigenschaften von SourceAD** auf **OK**.

8. Führen Sie einen vollständigen Import und eine Deltasynchronisierung mithilfe der folgenden Schritte aus:

     8\.1. Wählen Sie in der Liste der Connectors **SourceAD** aus.

     8\.2. Wählen Sie zum Öffnen des Dialogfelds **Connector ausführen** im Menü **Aktionen** die Option **Ausführen** aus.

     8\.3. Wählen Sie in der Liste **Ausführungsprofile** den Eintrag **Vollständiger Import** aus, und warten Sie, bis das Ausführungsprofil abgeschlossen ist.

     8\.4. Wählen Sie zum Öffnen des Dialogfelds **Connector ausführen** im Menü **Aktionen** die Option **Ausführen** aus.

     8\.5. Wählen Sie in der Liste **Ausführungsprofile** den Eintrag **Deltasynchronisierung** aus, und warten Sie, bis das Ausführungsprofil abgeschlossen ist.




## Konfigurieren der attributbasierten Filterung

Es gibt mehrere Methoden zur Konfiguration von Filtern auf Grundlage von Attributen. Die Konfiguration der eingehenden Filterung aus AD wird empfohlen, weil diese Konfigurationseinstellungen auch nach einem Upgrade auf eine neuere Version beibehalten werden. Die Konfiguration der ausgehenden Filterung zu Azure AD wird unterstützt. Diese Einstellungen werden jedoch nach einem Upgrade auf eine neuere Version nicht beibehalten. Diese Konfiguration sollte nur verwendet werden, wenn erforderlich ist, das kombinierte Objekt im Metaverse zu untersuchen, um die Filterung zu ermitteln.

### Eingehende Filterung

Die eingehende Filterung nutzt die Standardkonfiguration, in der für Objekte, die an Azure AD gesendet werden, das Metaverseattribut "cloudFiltered" nicht auf einen Wert festgelegt werden darf und das Metaverseattribut "sourceObjectType" auf „User“ oder „Contact“ festgelegt ist.

Das Attribut "cloudFiltered" sollte auf True festgelegt werden, wenn das Objekt nicht mit Azure AD synchronisiert werden und in anderen Fällen leer bleiben soll. Diese Methode wird verwendet, wenn für ein Objekt keine Synchronisierung erfolgen soll \(negative Filterung\).

Im folgenden Beispiel werden alle Benutzer ausgefiltert, bei denen "extensionAttribute15" den Wert "NoSync" aufweist:

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe "ADSyncAdmins" ist.
2. Öffnen Sie den **Synchronisierungsregeln-Editor** im **Startmenü**.
3. Stellen Sie sicher, dass **Eingehend** ausgewählt ist, und klicken Sie auf **Neue Regel hinzufügen**.
4. Geben Sie der Regel einen beschreibenden Namen, z. B. \*Eingehend aus AD – DoNotSyncFilter für Benutzer\*, wählen Sie die richtige Gesamtstruktur aus, und wählen Sie dann **Benutzer** als **CS-Objekttyp** und **Person** als **MV-Objekttyp** aus. Wählen Sie **Join** als **Verknüpfungsart** aus, und geben Sie als Rangfolge einen Wert ein, der zurzeit nicht von einer anderen Synchronisierungsregel verwendet wird \(z. B. 50\). Klicken Sie dann auf **Weiter**.
5. Klicken Sie unter **Bereichsfilter** auf **Gruppe hinzufügen**, klicken Sie auf **Klausel hinzufügen**, und wählen Sie dann unter "Attribut" die Option **ExtensionAttribute15** aus. Stellen Sie sicher, dass der Operator auf **EQUAL** festgelegt ist, und geben Sie dann den Wert "NoSync" in das Feld **Wert** ein. Klicken Sie auf **Weiter**.
6. Lassen Sie die **Joinregeln** leer, und klicken Sie dann auf **Weiter**.
7. Klicken Sie auf **Transformation hinzufügen**, legen Sie **FlowType** auf **Konstante** fest, wählen Sie "cloudFiltered" als Zielattribut aus, und geben Sie dann im Feld "Quelltext" den Wert "True" ein. Klicken Sie auf "Hinzufügen", um die Regel zu speichern.
8. Führen Sie eine vollständige Synchronisierung aus: Klicken Sie auf der Registerkarte **Connectors** mit der rechten Maustaste auf **SourceAD**. Klicken Sie auf **Ausführen**, auf **Vollständige Synchronisierung** und dann auf **OK**. 


Das Attribut **sourceObjectType** stellt einen **Benutzer** oder **Kontakt** für Azure AD bereit, wenn dieses Attribut den Wert **User** bzw. **Contact** besitzt. Durch Erstellen einer Synchronisierungsregel mit einer höheren Rangfolge als die integrierten Regeln kann das Standardverhalten außer Kraft gesetzt werden. Diese Methode bietet außerdem die Möglichkeit, positive und negative Regeln auszudrücken.

In diesem Beispiel werden nur Benutzer synchronisiert, deren Abteilungsattribut \*Sales\* oder leer ist:

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe "ADSyncAdmins" ist.
2. Öffnen Sie den **Synchronisierungsregeln-Editor** im **Startmenü**.
3. Stellen Sie sicher, dass **Eingehend** ausgewählt ist, und klicken Sie auf **Neue Regel hinzufügen**.
4. Geben Sie der Regel einen beschreibenden Namen \(z. B. \*Eingehend aus AD – DoNotSyncFilter für Benutzer\*\), wählen Sie die richtige Gesamtstruktur aus, und wählen Sie dann **Benutzer** als **CS-Objekttyp** und **Person** als **MV-Objekttyp** aus. Wählen Sie **Join** als **Verknüpfungsart** aus, und geben Sie als **Rangfolge** einen Wert ein, der zurzeit nicht von einer anderen Synchronisierungsregel verwendet wird \(z. B. 60\). Klicken Sie auf **Weiter**.
5. Lassen Sie den **Bereichsfilter** und die **Joinregeln** leer, und klicken Sie zweimal auf **Weiter**.
6. Klicken Sie auf **Transformation hinzufügen**, legen Sie **FlowType** auf **Ausdruck** fest, und wählen Sie dann das **Zielattribut** als **sourceObjectType** aus. Geben Sie in der **Quelle** den folgenden Ausdruck ein:<br>`IIF(IsNullOrEmpty([department]),NULL,IIF([department]<>”Sales”,”DoNotSync”,NULL))`
7. Klicken Sie auf "Hinzufügen", um die Regel zu speichern.
8. Führen Sie eine vollständige Synchronisierung aus: Klicken Sie auf der Registerkarte **Connectors** mit der rechten Maustaste auf **SourceAD**. Klicken Sie auf **Ausführen**, auf **Vollständige Synchronisierung** und dann auf **OK**. Das Ergebnis würde folgendermaßen aussehen:<br>

> [AZURE.NOTE]Beachten Sie, dass eine Mischung aus "cloudFiltered" und "sourceObjectType" verwendet wird, um zu bestimmen, welche Objekte mit Azure AD synchronisiert werden sollen.
 
Die Verwendung von Ausdrücken ermöglicht leistungsfähige Filteroptionen. Beachten Sie im Ausdruck oben, dass der Literalwert NULL angegeben ist, wenn die Abteilung nicht vorhanden ist und wenn die Abteilung "Sales" war. Dies zeigt an, dass dieses Attribut keinen Wert beigetragen hat und daher die integrierten Regeln ausgewertet werden. Dies ist gewünscht, damit ermittelt werden kann, ob ein **Benutzer** oder ein **Kontakt** in Azure AD erstellt werden soll.


## Ausgehende Filterung

In einigen Fällen ist es erforderlich, die Filterung erst auszuführen, nachdem die Objekte dem Metaverse hinzugefügt wurden. Es kann z. B. erforderlich sein, das E-Mail-Attribut aus der Ressourcengesamtstruktur und das Attribut "userPrincipalName" aus der Kontogesamtstruktur zu untersuchen, um zu ermitteln, ob ein Objekt synchronisiert werden soll. In diesen Fällen wird die Filterung für die ausgehende Regel erstellt.

> [AZURE.NOTE]Für diese Methode ist eine Änderung der integrierten Synchronisierungsregeln erforderlich. Das Ändern des Bereichs einer Synchronisierungsregel wird unterstützt. Die Änderung bleibt jedoch ggf. nach einem Upgrade auf eine neuere Version von Azure AD Connect nicht erhalten. Wenn Sie die ausgehende Filterung verwenden, notieren Sie sich die Änderungen, die vorgenommen werden müssen. Stellen Sie dann nach einem Upgrade sicher, dass die Filterung noch vorhanden ist, und wenden Sie sie bei Bedarf erneut an.
 

In diesem Beispiel wird die Filterung so geändert, dass nur Benutzer synchronisiert werden, deren Attribute "mail" und "userPrincipalName" auf "@contoso.com" enden:

1. Melden Sie sich bei dem Computer, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mithilfe eines Kontos an, das Mitglied der Sicherheitsgruppe "ADSyncAdmins" ist.
2. Öffnen Sie den Synchronisierungsregeln-Editor im Startmenü.
3. Klicken Sie unter "Regeltypen" auf "Ausgehend".
4. Suchen Sie nach der Regel namens "Out to AAD – User Join". Klicken Sie auf "Bearbeiten".
5. Klicken Sie im linken Navigationsbereich auf "Bereichsfilter". Klicken Sie auf "Klausel hinzufügen", und wählen Sie "mail" als Attribut aus. Wählen Sie "ENDSWITH" als Operator und "@contoso.com" als Werttyp aus. Klicken Sie auf "Klausel hinzufügen", und wählen Sie "userPrincipalName" als Attribut aus. Wählen Sie "ENDSWITH" als Operator und "@contoso.com" als Werttyp aus.
6. Klicken Sie auf Speichern.
7. Führen Sie eine vollständige Synchronisierung aus: Klicken Sie auf der Registerkarte "Connectors" mit der rechten Maustaste auf "SourceAD". Klicken Sie auf "Ausführen", auf "Vollständige Synchronisierung" und dann auf "OK".



## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

 

<!---HONumber=July15_HO5-->
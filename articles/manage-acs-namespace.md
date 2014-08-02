<properties linkid="manage-services-manage-acs" urlDisplayName="Manage ACS" pageTitle="Access Control Service - Azure service management" metaKeywords="" description="Learn how to manage your Azure Access Control Service (ACS) using certificates and keys." metaCanonical="" services="active-directory" documentationCenter="" title="Managing Your ACS Namespace" authors="" solutions="" manager="" editor="" />

Verwalten Ihres ACS-Namespace
=============================

In diesem Thema werden Verwaltungsaufgaben beschrieben, deren regelmäßige Ausführung empfohlen wird, damit Ihre Anwendungen, die Azure Access Control Service (ACS) verwenden, auch weiterhin korrekt und unterbrechungsfrei funktionieren. Es handelt sich dabei um folgende Verwaltungsaufgaben:

1.  Wichtig: Ablaufverfolgung und Ausführung von Rollovern für Zertifikate, vom ACS-Namespace verwendete Schlüssel und Kennwörter, Anwendungen der vertrauenden Seite, Dienstidentitäten, Identitätsanbieter und das ACS-Verwaltungsdienstkonto. Weitere Informationen finden Sie unten unter "Verwaltungsrichtlinien für Zertifikate und Schlüssel".

2.  Überprüfen Sie Ihre Identitätsanbieter, Dienstidentitäten, Regeln und Portaladministratoren, und entfernen Sie sie, sofern sie veraltet sind.

Weitere Informationen zu ACS finden Sie unter [Access Control Service 2.0](http://msdn.microsoft.com/en-us/library/gg429786.aspx).

Verwaltungsrichtlinien für Zertifikate und Schlüssel
----------------------------------------------------

Aus Sicherheitsgründen laufen die in ACS verwendeten Zertifikate und Schlüssel zu einem bestimmten Zeitpunkt ab. Es ist wichtig, die Ablaufdaten im Auge zu behalten, um diese Zertifikate und Schlüssel rechtzeitig erneuern zu können.

Dies sind die allgemeinen Schritte zum Rollover einer Token-Signatur (symmetrischer Schlüssel oder X.509-Zertifikats) oder eines Token-Verschlüsselungszertifikats:

1.  Konfigurieren Sie das neue Zertifikat oder den neuen Schlüssel in ACS als "sekundäres" Zertifikat bzw. "sekundären" Schlüssel neben dem vorhandenen ablaufenden Zertifikat bzw. Schlüssel.
2.  Benachrichtigen Sie die Partner, die den Dienst nutzen, darüber, dass sie ihre entsprechenden Schlüssel bis zu einem bestimmten Termin aktualisieren müssen.
3.  Die Partner sollten das entsprechende Zertifikat bzw. den entsprechenden Schlüssel für ihre vertrauenden Seiten oder Identitätsanbieter aktualisieren. Importieren Sie z. B. die aktualisierten WS-Federation-Metadaten für den ACS-Namespace, die das neue Zertifikat zur Validierung der Token-Signatur enthalten, oder konfigurieren Sie den symmetrischen Schlüssel in der Config-Datei der Anwendung.
4.  Wenn alle Anwendungen aktualisiert sind (oder nachdem ein Stichtag abgelaufen ist), markieren Sie das neue Zertifikat oder den neuen Schlüssel in der ACS-Konfiguration als primär.
5.  Entfernen Sie nach einer angemessenen Frist das alte Zertifikat bzw. den alten Schlüssel aus der ACS-Konfiguration.

Dies sind die allgemeinen Schritte zur Durchführung eines Rollovers für Token-Verschlüsselungszertifikate:

1.  Aktualisieren Sie das entsprechende Zertifikat oder den entsprechenden Schlüssel zur Token-Entschlüsselung in den Anwendungen der vertrauenden Seite, oder lassen Sie Ihre Partner diese Aktualisierung durchführen.
2.  Konfigurieren Sie das neue Verschlüsselungszertifikat in ACS neben dem vorhandenen ablaufenden Zertifikat.
3.  Entfernen Sie das alte Verschlüsselungszertifikat.

Dies sind die allgemeinen Schritte zur Durchführung eines Rollovers für Dienstidentitäts- oder Verwaltungsdienstschlüssel:

1.  Konfigurieren Sie das neue Zertifikat oder den neuen Schlüssel in ACS neben dem vorhandenen ablaufenden Zertifikat bzw. Schlüssel.
2.  Aktualisieren Sie das entsprechende Zertifikat oder den entsprechenden Schlüssel zur Token-Abfrage in den Client-Anwendungen, oder lassen Sie Ihre Partner diese Aktualisierung durchführen.
3.  Entfernen Sie das alte Zertifikat oder den alten Schlüssel, wenn alle Clients aktualisiert sind (oder nach einer angemessenen Frist).

Wenn ein Zertifikat oder ein Schlüssel abläuft, schlägt die Ausgabe von Tokens durch ACS fehl. Dadurch funktioniert die vertrauende Seite nicht mehr ordnungsgemäß. Abgelaufene Zertifikate und Schlüssel werden von ACS ignoriert. Dies führt zu Ausnahmefehlern, als wenn von vornherein kein Zertifikat oder Schlüssel konfiguriert worden wäre. In den folgenden Abschnitten finden Sie Informationen zu allen von ACS verwalteten Zertifikaten und Schlüsseln, zu ihrer Erneuerung sowie dazu, wie Sie erkennen, wann sie abgelaufen sind und erneuert werden müssen.

-   Der Abschnitt "Zertifikate und Schlüssel" im ACS-Verwaltungsportal hilft Ihnen, Zertifikate und Schlüssel für Dienst-Namespaces und Anwendungen der vertrauenden Seite zu verwalten. Weitere Informationen über diese Arten von Anmeldeinformationen erhalten Sie unter [Zertifikate und Schlüssel](http://msdn.microsoft.com/en-us/library/gg185932.aspx).
-   Der Abschnitt "Dienstidentitäten" im ACS-Verwaltungsportal hilft Ihnen bei der Verwaltung von Anmeldeinformationen (Zertifikate, Schlüssel oder Kennwörter) für Dienstidentitäten. Weitere Informationen über Dienstidentitäten erhalten Sie unter [Dienstidentitäten](http://msdn.microsoft.com/en-us/library/gg185945.aspx).
-   Der Abschnitt "Verwaltungsdienst" im ACS-Verwaltungsportal hilft Ihnen bei der Verwaltung von Anmeldeinformationen (Zertifikate, Schlüssel oder Kennwörter) für den ACS-Verwaltungsdienst. Weitere Informationen über den ACS-Verwaltungsdienst erhalten sie unter [ACS-Verwaltungsdienst](http://msdn.microsoft.com/en-us/library/gg185972.aspx).

Es gibt einige Typen von Zertifikaten und Schlüsseln, die im ACS-Verwaltungsportal nicht sichtbar sind. Speziell im Falle von WS-Federation-Identitätsanbietern wie AD FS müssen Sie die Gültigkeit der von den Identitätsanbietern verwendeten Zertifikate proaktiv prüfen. Aktuell sind die über die Metadaten der WS-Federation-Identitätsanbieter verfügbaren Zertifikate nicht im ACS-Verwaltungsportal sichtbar. Um die Gültigkeit der Zertifikate zu prüfen, ermitteln Sie mithilfe des Verwaltungsdienstes das Gültigkeits- und das Ablaufdatum, also die Eigenschaften StartDate und EndDate des [IdentityProviderKey](http://msdn.microsoft.com/en-us/library/hh124084.aspx). Wenn das Zertifikat oder der Schlüssel abläuft und deshalb ungültig wird, beginnt ACS, für das Zertifikat bzw. den Schlüssel spezifische Ausnahmefehler [ACS-Fehlercodes](http://msdn.microsoft.com/en-us/library/gg185949.aspx) auszulösen. Die speziellen Fehlercodes finden Sie in den Abschnitten unten.

Sie können die Zertifikate und Schlüssel programmiert aktualisieren, indem Sie den [ACS-Verwaltungsdienst](http://msdn.microsoft.com/en-us/library/gg185972.aspx) verwenden. Sie können auch das Codebeispiel "KeyManagement" prüfen, das als Teil von [Codebeispiel: Verwaltungsdienst](http://msdn.microsoft.com/en-us/library/gg185970.aspx) zum Herunterladen erhältlich ist.

Verfügbare Zertifikate und Schlüssel
------------------------------------

Die folgenden Liste enthält die verfügbaren Zertifikate und Schlüssel, die in ACS verwendet werden und deren Ablaufdatum überprüft werden muss:

-   Token-Signaturzertifikate
-   Token-Signaturschlüssel
-   Token-Verschlüsselungszertifikate
-   Token-Entschlüsselungszertifikate
-   Anmeldeinformationen für die Dienstidentität
-   Anmeldeinformationen für ACS-Verwaltungsdienstkonten
-   Signatur- und Verschlüsselungszertifikate für den Identitätsanbieter

Weiter unten in diesem Thema werden jedes Zertifikat und jeder Schlüssel detailliert beschrieben.

Token-Signaturzertifikate
-------------------------

ACS signiert alle ausgegebenen Sicherheits-Tokens. Beim Erstellen einer Anwendung, die von ACS ausgestellte SAML-Tokens nutzt, werden X.509-Zertifikate für die Signatur verwendet.

Sie können Token-Signaturzertifikate mithilfe des Abschnitts "Zertifikate und Schlüssel" im ACS-Verwaltungsportal verwalten.

**Verwalten von Token-Signaturzertifikaten**

1.  Öffnen Sie einen Internet-Browser, und besuchen Sie das Azure-Verwaltungsportal (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Melden Sie sich mit einer Windows Live-ID auf der Website an. Wenn Sie keine Windows Live-ID haben, klicken Sie auf "Registrieren", um eine solche zu erstellen.

3.  Nach der Anmeldung mit Ihrer Windows Live-ID werden Sie auf die Seite "Verwaltungsportal" weitergeleitet. Klicken Sie links unten auf dieser Seite auf **Dienstbus und Zugriffssteuerung**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Starten Sie das ACS-Verwaltungsportal, indem Sie in der Struktur auf der linken Seite auf **Zugangssteuerung** klicken, wählen Sie den ACS-Dienst-Namespace, den Sie konfigurieren möchten, und klicken Sie anschließend in der Symbolleiste oben auf der Seite auf die Schaltfläche **Zugriffssteuerungsdienst**.

    ![](./media/manage-acs-namespace/ACS2.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Klicken Sie in der Struktur auf der linken Seite unter dem Abschnitt "Diensteinstellungen" auf **Zertifikate und Schlüssel**.

    ![](./media/manage-acs-namespace/ACS4.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS5.png)

6.  Klicken Sie unter dem Abschnitt "Token-Signatur" auf die Schaltfläche "Hinzufügen", um das neue Zertifikat in ACS als "sekundäres" Zertifikat neben dem vorhandenen ablaufenden Zertifikat zu konfigurieren.

7.  Benachrichtigen Sie die Partner, die den Dienst nutzen, darüber, dass sie ihre entsprechenden Schlüssel bis zu einem bestimmten Termin aktualisieren müssen.

8.  Die Partner sollten das entsprechende Zertifikat für ihre vertrauenden Seiten oder Identitätsanbieter aktualisieren. Importieren Sie z. B. die aktualisierten WS-Federation-Metadaten für den ACS-Namespace, die das neue Zertifikat zur Validierung der Token-Signatur enthalten, oder konfigurieren Sie den symmetrischen Schlüssel in der Config-Datei der Anwendung.

9.  Wenn alle Anwendungen aktualisiert sind (oder nachdem ein Stichtag abgelaufen ist), markieren Sie das neue Zertifikat in der ACS-Konfiguration als primär.

10. Klicken Sie nach einer angemessenen Frist unter dem Abschnitt "Token-Signatur" der Seite "Zertifikate und Schlüssel" auf die Schaltfläche "Löschen", um das alte Zertifikat aus der ACS-Konfiguration zu entfernen.

Weitere Informationen finden Sie unter [Zertifikate und Schlüssel](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Nach dem Ablauf von Signaturzertifikaten erhalten Sie die folgenden Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern:

<table><tr><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td>
<td><b>Zur Behebung des Fehlers erforderliche Aktion</b></td>
</tr>
<tr>
<td>ACS50004</td>
<td>Es ist kein primäres X.509-Signaturzertifikat konfiguriert. Für SAML ist jedoch ein Signaturzertifikat erforderlich.</td>
<td>Wenn die ausgewählte vertrauende Seite SAML als Token-Typ verwendet, stellen Sie sicher, dass für die vertrauende Seite oder den Dienst-Namespace ein gültiges X.509-Zertifikat konfiguriert ist. Das Zertifikat muss als primäres Zertifikat festgelegt sein und sich innerhalb seines Gültigkeitszeitraums befinden.</td></tr>
</table>

Token-Signaturschlüssel
-----------------------

ACS signiert alle ausgegebenen Sicherheits-Tokens. Beim Erstellen einer Anwendung, die von ACS ausgestellte SWT-Tokens nutzt, werden symmetrische 256-Bit-Signaturschlüssel verwendet.

Sie können Token-Signaturschlüssel mithilfe des Abschnitts "Zertifikate und Schlüssel" im ACS-Verwaltungsportal verwalten.

**Verwalten von Token-Signaturschlüsseln**

1.  Öffnen Sie einen Internet-Browser, und besuchen Sie das Azure-Verwaltungsportal (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Melden Sie sich mit einer Windows Live-ID auf der Website an. Wenn Sie keine Windows Live-ID haben, klicken Sie auf "Registrieren", um eine solche zu erstellen.

3.  Nach der Anmeldung mit Ihrer Windows Live-ID werden Sie auf die Seite "Verwaltungsportal" weitergeleitet. Klicken Sie links unten auf dieser Seite auf **Dienstbus und Zugriffssteuerung**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Starten Sie das ACS-Verwaltungsportal, indem Sie in der Struktur auf der linken Seite auf **Zugangssteuerung** klicken, wählen Sie den ACS-Dienst-Namespace, den Sie konfigurieren möchten, und klicken Sie anschließend in der Symbolleiste oben auf der Seite auf die Schaltfläche **Zugriffssteuerungsdienst**.

    ![](./media/manage-acs-namespace/ACS2.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Klicken Sie in der Struktur auf der linken Seite unter dem Abschnitt "Diensteinstellungen" auf **Zertifikate und Schlüssel**.

    ![](./media/manage-acs-namespace/ACS4.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS5.png)

6.  Klicken Sie unter dem Abschnitt "Token-Signatur" auf die Schaltfläche "Hinzufügen", um den neuen Schlüssel in ACS als "sekundären" Schlüssel neben dem vorhandenen ablaufenden Schlüssel zu konfigurieren.

7.  Benachrichtigen Sie die Partner, die den Dienst nutzen, darüber, dass sie ihre entsprechenden Schlüssel bis zu einem bestimmten Termin aktualisieren müssen.

8.  Die Partner sollten den entsprechenden Schlüssel für ihre vertrauenden Seiten oder Identitätsanbieter aktualisieren. Importieren Sie z. B. die aktualisierten WS-Federation-Metadaten für den ACS-Namespace, die das neue Zertifikat zur Validierung der Token-Signatur enthalten, oder konfigurieren Sie den symmetrischen Schlüssel in der Config-Datei der Anwendung.

9.  Wenn alle Anwendungen aktualisiert sind (oder nachdem ein Stichtag abgelaufen ist), markieren Sie den neuen Schlüssel in der ACS-Konfiguration als primär.

10. Klicken Sie nach einer angemessenen Frist unter dem Abschnitt "Token-Signatur" der Seite "Zertifikate und Schlüssel" auf die Schaltfläche "Löschen", um den alten Schlüssel aus der ACS-Konfiguration zu entfernen.

Weitere Informationen finden Sie unter [Zertifikate und Schlüssel](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Nach dem Ablauf von Signaturschlüsseln erhalten Sie die folgenden Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern:

<table><tr><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td>
<td><b>Zur Behebung des Fehlers erforderliche Aktion</b></td>
</tr>
<tr>
<td>ACS50003</td>
<td>Es ist kein primärer symmetrischer Signaturschlüssel konfiguriert. Für SWT ist jedoch ein symmetrischer Signaturschlüssel erforderlich.</td>
<td>Wenn die ausgewählte vertrauende Seite SWT als Token-Typ verwendet, stellen Sie sicher, dass für die vertrauende Seite oder den Dienst-Namespace ein symmetrischer Schlüssel konfiguriert ist, als primärer Schlüssel festgelegt ist und sich innerhalb seines Gültigkeitszeitraums befindet.</td></tr>
</table>

Token-Verschlüsselungszertifikate
---------------------------------

Eine Token-Verschlüsselung ist erforderlich, wenn es sich bei der Anwendung einer vertrauenden Seite um einen Webdienst handelt, der Proof-of-Possession-Token über das WS-Trust-Protokoll verwendet. In anderen Fällen ist eine Verschlüsselung optional.

Sie können Token-Verschlüsselungszertifikate mithilfe des Abschnitts "Zertifikate und Schlüssel" im ACS-Verwaltungsportal verwalten.

**Verwalten von Token-Verschlüsselungszertifikaten**

1.  Öffnen Sie einen Internet-Browser, und besuchen Sie das Azure-Verwaltungsportal (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Melden Sie sich mit einer Windows Live-ID auf der Website an. Wenn Sie keine Windows Live-ID haben, klicken Sie auf "Registrieren", um eine solche zu erstellen.

3.  Nach der Anmeldung mit Ihrer Windows Live-ID werden Sie auf die Seite "Verwaltungsportal" weitergeleitet. Klicken Sie links unten auf dieser Seite auf **Dienstbus und Zugriffssteuerung**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Starten Sie das ACS-Verwaltungsportal, indem Sie in der Struktur auf der linken Seite auf **Zugangssteuerung** klicken, wählen Sie den ACS-Dienst-Namespace, den Sie konfigurieren möchten, und klicken Sie anschließend in der Symbolleiste oben auf der Seite auf die Schaltfläche **Zugriffssteuerungsdienst**.

    ![](./media/manage-acs-namespace/ACS2.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Klicken Sie in der Struktur auf der linken Seite unter dem Abschnitt "Diensteinstellungen" auf **Zertifikate und Schlüssel**.

    ![](./media/manage-acs-namespace/ACS4.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS7.png)

6.  Aktualisieren Sie das entsprechende Zertifikat oder den entsprechenden Schlüssel zur Token-Verschlüsselung in den Anwendungen der vertrauenden Seite, oder lassen Sie Ihre Partner diese Aktualisierung durchführen.
7.  Konfigurieren Sie das neue Verschlüsselungszertifikat in ACS neben dem vorhandenen ablaufenden Zertifikat, indem Sie auf die Schaltfläche "Hinzufügen" klicken.
8.  Löschen Sie das alte Verschlüsselungszertifikat, indem Sie auf die Schaltfläche "Löschen" klicken.

Weitere Informationen finden Sie unter [Zertifikate und Schlüssel](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Nach dem Ablauf von Verschlüsselungszertifikaten erhalten Sie die folgenden Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern:

<table><tr><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td>
<td><b>Zur Behebung des Fehlers erforderliche Aktion</b></td>
</tr>
<tr>
<td>ACS50005</td>
<td>Token-Verschlüsselung ist erforderlich, aber für die vertrauende Seite ist kein Verschlüsselungszertifikat konfiguriert.</td>
<td>Deaktivieren Sie entweder die Token-Verschlüsselung für die ausgewählte vertrauende Seite, oder laden Sie ein X.509-Zertifikat hoch, um es für die Token-Verschlüsselung zu verwenden.</td></tr>
</table>

Token-Entschlüsselungszertifikate
---------------------------------

ACS kann verschlüsselte Token von WS-Federation-Identitätsanbietern annehmen (z. B. AD FS 2.0). Für die Entschlüsselung wird ein in ACS gehostetes X.509-Zertifikat verwendet.

Sie können Token-Entschlüsselungszertifikate mithilfe des Abschnitts "Zertifikate und Schlüssel" im ACS-Verwaltungsportal verwalten.

**Verwalten von Token-Entschlüsselungszertifikaten**

1.  Öffnen Sie einen Internet-Browser, und besuchen Sie das Azure-Verwaltungsportal (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Melden Sie sich mit einer Windows Live-ID auf der Website an. Wenn Sie keine Windows Live-ID haben, klicken Sie auf "Registrieren", um eine solche zu erstellen.

3.  Nach der Anmeldung mit Ihrer Windows Live-ID werden Sie auf die Seite "Verwaltungsportal" weitergeleitet. Klicken Sie links unten auf dieser Seite auf **Dienstbus und Zugriffssteuerung**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Starten Sie das ACS-Verwaltungsportal, indem Sie in der Struktur auf der linken Seite auf **Zugangssteuerung** klicken, wählen Sie den ACS-Dienst-Namespace, den Sie konfigurieren möchten, und klicken Sie anschließend in der Symbolleiste oben auf der Seite auf die Schaltfläche **Zugriffssteuerungsdienst**.

    ![](./media/manage-acs-namespace/ACS2.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Klicken Sie in der Struktur auf der linken Seite unter dem Abschnitt "Diensteinstellungen" auf **Zertifikate und Schlüssel**.

    ![](./media/manage-acs-namespace/ACS4.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS9.png)

6.  Klicken Sie unter dem Abschnitt "Token-Entschlüsselung" auf die Schaltfläche "Hinzufügen", um das neue Zertifikat in ACS als "sekundäres" Zertifikat neben dem vorhandenen ablaufenden Zertifikat zu konfigurieren.

7.  Benachrichtigen Sie die Partner, die den Dienst nutzen, darüber, dass sie ihre entsprechenden Schlüssel bis zu einem bestimmten Termin aktualisieren müssen.

8.  Die Partner sollten das entsprechende Zertifikat für ihre vertrauenden Seiten oder Identitätsanbieter aktualisieren. Importieren Sie z. B. die aktualisierten WS-Federation-Metadaten für den ACS-Namespace, die das neue Zertifikat zur Validierung der Token-Signatur enthalten, oder konfigurieren Sie den symmetrischen Schlüssel in der Config-Datei der Anwendung.

9.  Wenn alle Anwendungen aktualisiert sind (oder nachdem ein Stichtag abgelaufen ist), markieren Sie das neue Zertifikat in der ACS-Konfiguration als primär.

10. Klicken Sie nach einer angemessenen Frist unter dem Abschnitt "Token-Signatur" der Seite "Zertifikate und Schlüssel" auf die Schaltfläche "Löschen", um das alte Zertifikat aus der ACS-Konfiguration zu entfernen.

Weitere Informationen finden Sie unter [Zertifikate und Schlüssel](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Nach dem Ablauf von Entschlüsselungszertifikaten erhalten Sie die folgenden Fehlermeldungen, wenn Sie versuchen, ein Token anzufordern:

<table><tr><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Fehler beim Verarbeiten des SOAP-Headers.</td>
</tr>
<tr><td>ACS20001</td>
<td>Fehler beim Verarbeiten einer WS-Federation-Anmeldeantwort.</td></tr>
</table>

Anmeldeinformationen für die Dienstidentität
--------------------------------------------

Dienstidentitäten sind Anmeldeinformationen, die global für den ACS-Namespace konfiguriert werden und Anwendungen oder Clients erlauben, sich direkt in ACS zu authentifizieren und ein Token zu erhalten. Es gibt drei Arten von Anmeldeinformationen, mit der eine ACS-Dienstidentität verknüpft werden kann: Symmetrische Schlüssel, Kennwörter und X.509-Zertifikate.

Sie können Anmeldeinformationen für Dienstidentitäten direkt über die Seite "Dienstidentitäten" des ACS-Verwaltungsportals verwalten.

**Verwalten von Anmeldeinformationen für Dienstidentitäten**

1.  Öffnen Sie einen Internet-Browser, und besuchen Sie das Azure-Verwaltungsportal (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Melden Sie sich mit einer Windows Live-ID auf der Website an. Wenn Sie keine Windows Live-ID haben, klicken Sie auf "Registrieren", um eine solche zu erstellen.

3.  Nach der Anmeldung mit Ihrer Windows Live-ID werden Sie auf die Seite "Verwaltungsportal" weitergeleitet. Klicken Sie links unten auf dieser Seite auf **Dienstbus und Zugriffssteuerung**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Starten Sie das ACS-Verwaltungsportal, indem Sie in der Struktur auf der linken Seite auf **Zugangssteuerung** klicken, wählen Sie den ACS-Dienst-Namespace, den Sie konfigurieren möchten, und klicken Sie anschließend in der Symbolleiste oben auf der Seite auf die Schaltfläche **Zugriffssteuerungsdienst**.

    ![](./media/manage-acs-namespace/ACS2.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Klicken Sie in der Struktur auf der linken Seite unter dem Abschnitt "Diensteinstellungen" auf **Dienstidentitäten**.

    ![](./media/manage-acs-namespace/ACS11.png)

6.  Klicken Sie auf die Dienstidentität, die Sie bearbeiten möchten.

    ![](./media/manage-acs-namespace/ACS112.png)

7.  Klicken Sie im Abschnitt "Anmeldeinformationen" auf die Schaltfläche "Hinzufügen", um das neue Zertifikat oder den neuen Schlüssel neben dem vorhandenen auslaufenden Zertifikat bzw. Schlüssel in ACS zu konfigurieren.

8.  Aktualisieren Sie das entsprechende Zertifikat oder den entsprechenden Schlüssel zur Token-Abfrage in den Client-Anwendungen, oder lassen Sie Ihre Partner diese Aktualisierung durchführen.

9.  Klicken Sie auf die Schaltfläche "Löschen", um das alte Zertifikat oder den alten Schlüssel zu entfernen, wenn alle Clients aktualisiert sind (oder nach einer angemessenen Frist).

Weitere Informationen finden Sie unter [Dienstidentitäten](http://msdn.microsoft.com/en-us/library/gg185945.aspx).

Im Folgenden finden Sie die Ausnahmefehler, die ACS auslöst, wenn die Anmeldeinformationen abgelaufen sind.

<table><tr><td><b>Anmeldeinformation</b></td><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td><td><b>Zur Behebung des Fehlers erforderliche Aktion</b></td>
</tr>
<tr>
<td>Symmetrischer Schlüssel, Kennwort</td>
<td>ACS50006</td>
<td>Signaturverifizierung fehlgeschlagen. (In der Nachricht sind eventuell noch mehr Details enthalten.)</td>
<td></td>
</tr>
<tr><td>X.509-Zertifikat</td>
<td>ACS50016</td>
<td>Das X.509-Zertifikat mit dem Betreff '<Name des Zertifikatbetreffs>' und dem Fingerabdruck '<Fingerabdruck des Zertifikats>' stimmt mit keinem konfigurierten Zertifikat überein.</td>
<td>Stellen Sie sicher, dass das angeforderte Zertifikat in ACS hochgeladen wurde.</td>
</tr>
</table>

Folgen Sie zur Überprüfung und Aktualisierung der Ablaufdaten von symmetrischen Schlüsseln oder Kennwörtern oder zum Hochladen neuer Zertifikate als Anmeldeinformationen für Dienstidentitäten den unter [Gewusst wie: Hinzufügen von Dienstidentitäten mit einem X.509-Zertifikat, Kennwort oder symmetrischen Schlüssel](http://msdn.microsoft.com/en-us/library/gg185924.aspx) genannten Anweisungen. Eine Liste von Anmeldeinformationen für Dienstidentitäten ist auf der Seite "Bearbeiten der Dienstidentität" verfügbar.

Anmeldeinformationen für den Verwaltungsdienst
----------------------------------------------

Der ACS-Verwaltungsdienst ist ein wichtiger Bestandteil von ACS, mit dem Sie die Einstellungen in einem ACS-Namespace programmiert verwalten und konfigurieren können. Das ACS-Verwaltungsdienstkonto kann mit drei Arten von Anmeldeinformationen verknüpft werden. Dabei handelt es sich um einen symmetrischen Schlüssel, ein Kennwort und ein X.509-Zertifikat.

Sie können die Anmeldeinformationen für den Verwaltungsdienst auf der Seite "Verwaltungsdienst" des ACS-Verwaltungsportals verwalten.

**Verwalten der Anmeldeinformationen für den ACS-Verwaltungsdienst**

1.  Öffnen Sie einen Internet-Browser, und besuchen Sie das Azure-Verwaltungsportal (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Melden Sie sich mit einer Windows Live-ID auf der Website an. Wenn Sie keine Windows Live-ID haben, klicken Sie auf "Registrieren", um eine solche zu erstellen.

3.  Nach der Anmeldung mit Ihrer Windows Live-ID werden Sie auf die Seite "Verwaltungsportal" weitergeleitet. Klicken Sie links unten auf dieser Seite auf **Dienstbus und Zugriffssteuerung**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Starten Sie das ACS-Verwaltungsportal, indem Sie in der Struktur auf der linken Seite auf **Zugangssteuerung** klicken, wählen Sie den ACS-Dienst-Namespace, den Sie konfigurieren möchten, und klicken Sie anschließend in der Symbolleiste oben auf der Seite auf die Schaltfläche **Zugriffssteuerungsdienst**.

    ![](./media/manage-acs-namespace/ACS2.png)

    An diesem Punkt sollte Ihr Bildschirm folgendermaßen aussehen:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Klicken Sie in der Struktur auf der linken Seite unter dem Abschnitt "Verwaltung" auf **Verwaltungsdienst**.

    ![](./media/manage-acs-namespace/ACS14.png)

6.  Klicken Sie auf das Verwaltungsdienstkonto.

    ![](./media/manage-acs-namespace/ACS15.png)

7.  Klicken Sie im Abschnitt "Anmeldeinformationen" auf die Schaltfläche "Hinzufügen", um das neue Zertifikat oder den neuen Schlüssel neben dem vorhandenen auslaufenden Zertifikat bzw. Schlüssel in ACS zu konfigurieren.

8.  Aktualisieren Sie das entsprechende Zertifikat oder den entsprechenden Schlüssel zur Token-Abfrage in den Client-Anwendungen, oder lassen Sie Ihre Partner diese Aktualisierung durchführen.

9.  Klicken Sie auf die Schaltfläche "Löschen", um das alte Zertifikat oder den alten Schlüssel zu entfernen, wenn alle Clients aktualisiert sind (oder nach einer angemessenen Frist).

Weitere Informationen finden Sie unter [ACS-Verwaltungsdienst](http://msdn.microsoft.com/en-us/library/gg185972.aspx).

ACS löst folgende Ausnahmefehler aus, wenn diese Anmeldeinformationen abgelaufen sind:

<table><tr><td><b>Anmeldeinformation</b></td><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td><td><b>Zur Behebung des Fehlers erforderliche Aktion</b></td>
</tr>
<tr>
<td>Symmetrischer Schlüssel, Kennwort</td>
<td>ACS50006</td>
<td>Signaturverifizierung fehlgeschlagen. (In der Nachricht sind eventuell noch mehr Details enthalten.)</td>
<td></td>
</tr>
<tr><td>X.509-Zertifikat</td>
<td>ACS50016</td>
<td>Das X.509-Zertifikat mit dem Betreff '<Name des Zertifikatbetreffs>' und dem Fingerabdruck '<Fingerabdruck des Zertifikats>' stimmt mit keinem konfigurierten Zertifikat überein.</td>
<td>Stellen Sie sicher, dass das angeforderte Zertifikat in ACS hochgeladen wurde.</td>
</tr>
</table>

Eine Liste von Anmeldeinformationen für das ACS-Verwaltungsdienstkonto ist auf der Seite "Bearbeiten des Verwaltungsdienstkontos" im ACS-Verwaltungsportal verfügbar.

Zertifikat des WS-Federation-Identitätsanbieters
------------------------------------------------

Das Zertifikat des WS-Federation-Identitätsanbieters steht über seine Metadaten zur Verfügung. Beim Konfigurieren eines WS-Federation-Identitätsanbieters, z. B. AD FS, wird das WS-Federation-Signaturzertifikat über per URL oder als Datei erhältliche WS-Federation-Metadaten konfiguriert. Weitere Informationen dazu erhalten Sie unter [WS-Federation-Identitätsanbieter](http://msdn.microsoft.com/en-us/library/gg185933.aspx) und [Gewusst wie: Konfigurieren von AD FS 2.0 als Identitätsanbieter](http://msdn.microsoft.com/en-us/library/gg185961.aspx). Sobald Sie den WS-Federation-Identitätsanbieter konfiguriert haben, fragen Sie die Gültigkeit seiner Zertifikate mithilfe des ACS-Verwaltungsdiensts ab. Bitte beachten Sie, dass die Schlüssel bei jedem weiteren Hochladen der Metadaten über das ACS-Verwaltungsportal oder den ACS-Verwaltungsdienst ausgetauscht werden.

Im Folgenden finden Sie die Ausnahmefehler, die ACS auslöst, wenn das Zertifikat abgelaufen ist:

<table><tr><td><b>Fehlercode</b></td>
<td><b>Nachricht</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Fehler beim Verarbeiten des SOAP-Headers.</td>
</tr>
<tr><td>ACS20001</td>
<td>Fehler beim Verarbeiten einer WS-Federation-Anmeldeantwort.</td></tr>
<tr><td>ACS50006</td><td>Signaturverifizierung fehlgeschlagen. (In der Nachricht sind eventuell noch mehr Details enthalten.)</td></tr>
</table>



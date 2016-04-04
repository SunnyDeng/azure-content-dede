<properties
   pageTitle="Ausführen einer Sicherheitsüberprüfung | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management privilegierten Identitäten Rollen hinzufügen."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: Ausführen einer Sicherheitsüberprüfung


Nachdem eine [Sicherheitsüberprüfung gestartet wurde](active-directory-privileged-identity-management-how-to-start-security-review.md), lässt sich privilegierter Zugriff sehr einfach überprüfen.

## Für Prüfer: Genehmigen oder Verweigern des Zugriffs

### Überprüfen
1. Klicken Sie im PIM-Hauptmenü auf **Administratorzugriff überprüfen**. Eine Liste mit Sicherheitsüberprüfungen wird angezeigt.
2. Wählen Sie in der Liste den/die **Benutzer** aus, dessen/deren Zugriff Sie ändern möchten. HINWEIS: Der Zugriff wird tatsächlich geändert. Dieser Vorgang besteht lediglich aus dem Erstellen einer Prüfliste für Personen, die den Zugriff für die Rolle möglicherweise ändern. Nachdem mindestens ein Benutzer ausgewählt wurde, werden die Schaltflächen **Zugriff genehmigen** und **Zugriff verweigern** aktiviert.
3. Klicken Sie für die von Ihnen ausgewählten Benutzer auf **Zugriff genehmigen** oder **Zugriff verweigern**. Im Hauptmenü des Azure-Portals wird eine Benachrichtigung angezeigt, und die Überprüfungsliste wird ausgeblendet. Schließen Sie das Blatt **Azure AD-Rollen überprüfen**.

### Selbstüberprüfung
1. Der Benutzer empfängt eine E-Mail mit der Aufforderung, seinen Zugriff zu überprüfen. Die E-Mail enthält einen Link zum Anmelden beim Azure-Portal.
2. Im Portal kann der Benutzer den eigenen Zugriff genehmigen oder verweigern, indem er auf die Schaltfläche **Zugriff genehmigen** bzw. **Zugriff verweigern** klickt. Sein Name wird aus der Liste entfernt.

## Für Überprüfungs-Manager: Verwalten von Sicherheitsüberprüfungen

## Abschließen oder Beenden einer Überprüfung
1. Wechseln Sie zum PIM-Dashboard.
2. Klicken Sie in der Liste **Sicherheitsüberprüfungen** auf die Sicherheitsüberprüfung, die Sie abschließen möchten. Das Detailblatt der Sicherheitsüberprüfung wird angezeigt.
3. Klicken Sie auf **Überprüfung beenden**, um die Überprüfung abzuschließen oder zu beenden. Dadurch wird die Überprüfung archiviert und das Blatt nicht mehr angezeigt.

## Exportieren einer Überprüfung
Sie können eine Überprüfung exportieren, um sie in Excel oder in einem anderen Programm zu verwenden, das mit CSV-Dateien kompatibel ist.

1. Wechseln Sie zum PIM-Dashboard.
2. Klicken Sie auf den Abschnitt **Sicherheitsüberprüfungen** des Dashboards. Das Blatt **Sicherheitsüberprüfungen** wird angezeigt.
3. Klicken Sie auf die Sicherheitsüberprüfung, die Sie exportieren möchten. Das Detailblatt der Sicherheitsüberprüfung wird angezeigt.
4. Klicken Sie auf die Schaltfläche **Exportieren**. Der Download einer CSV-Datei wird gestartet.

## Löschen einer Überprüfung

> [AZURE.WARNING] Vor dem Löschvorgang wird keine Warnung angezeigt. Vergewissern Sie sich daher, dass Sie die Überprüfung *tatsächlich löschen möchten*.

1. Kehren Sie zum PIM-Dashboard zurück.
2. Klicken Sie auf den Abschnitt **Sicherheitsüberprüfungen** des Dashboards. Das Blatt **Sicherheitsüberprüfungen** wird angezeigt.
3. Klicken Sie auf die Sicherheitsüberprüfung, die Sie löschen möchten. Das Detailblatt der Sicherheitsüberprüfung wird angezeigt.
4. Klicken Sie auf die Schaltfläche **Löschen**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->
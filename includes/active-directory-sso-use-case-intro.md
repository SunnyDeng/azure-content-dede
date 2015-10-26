Unternehmen verwenden aus Produktivitätsgründen zunehmend SaaS-Anwendungen (Software-as-a-Service), da die Cloudtechnologie und entsprechende Tools immer mehr zur Verfügung stehen. Mit der steigenden Anzahl von SaaS-Apps wird es zu einer Herausforderung für die Administratoren, Konten und Zugriffsrechte zu verwalten, und für die Benutzer, die verschiedenen Kennwörter nicht zu vergessen. Die individuelle Verwaltung der einzelnen Programme verursacht zusätzliche Arbeit und ist weniger sicher.

- Mitarbeiter, die den Überblick über viele Kennwörter behalten müssen, verwenden tendenziell weniger sichere Methoden, um sie sich zu merken: Sie schreiben sie auf oder verwenden dieselben Kennwörter über viele Konten hinweg.

- Wenn ein neuer Mitarbeiter eintrifft oder ein Mitarbeiter das Unternehmen verlässt, müssen alle Konten einzeln bereitgestellt oder deaktiviert sein.

- Darüber hinaus können Mitarbeiter anfangen, SaaS-Apps für ihre Arbeit zu verwenden, ohne den Umweg über die IT-Abteilung zu nehmen, d. h., sie erstellen ihre eigenen Konten auf Systemen, die die IT-Administratoren nicht genehmigt haben und die nicht überwacht werden.

Eine Lösung für all diese Herausforderungen stellt das einmalige Anmelden (SSO) bereit. Es ist die einfachste Methode, um mehrere Apps zu verwalten und Benutzern eine konsistente Anmeldung zu bieten. Azure Active Directory (Azure AD) bietet eine robuste SSO-Lösung und umfasst viele verfügbare vorintegrierte Clientanwendungen sowie Tutorials für Administratoren, damit diese schnell eine neue App einrichten und die Bereitstellung von Benutzern starten können.


## Integration von Apps mit Azure Active Directory  

Mit Azure AD können Sie Ihre Apps und die bereitgestellten Konten integrieren. Dies kann durch eine von zwei Methoden erfolgen.

- Wenn die App vorab im App-Katalog integriert ist, können Sie über dieses Portal Apps einrichten und die Einstellungen konfigurieren, um SSO zu ermöglichen. Für jede Katalog-App können Sie mithilfe der einfachen Schrittanleitungen im App-Katalog und im Azure-Portal die einmalige Anmeldung aktivieren.

- Wenn die App nicht im Katalog enthalten ist, können Sie die meisten Apps weiterhin in Azure AD als benutzerdefinierte App einrichten. Dies erfordert etwas mehr technisches Fachwissen bei der Konfiguration. Sie können jede Anwendung hinzufügen, die SAML 2.0 als Verbund-App unterstützt, oder jede Anwendung für die kennwortbasierte einmalige Anmeldung, die über eine HTML-basierte Anmeldeseite verfügt.

Für den Fall, dass Benutzer ihre eigenen Konten für SaaS-Apps erstellt haben, die von der IT nicht verwaltetet werden, bietet das Tool [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) eine Lösung. Dieses Tool überwacht den Internetdatenverkehr, um festzustellen, welche Apps in der gesamten Organisation von wie vielen Personen verwendet werden. Anhand dieser Informationen kann die IT feststellen, welche Apps von den Benutzern bevorzugt werden, und entscheiden, welche für SSO in Azure AD integriert werden sollen.

Wenn Sie eine Anwendung in Azure AD integrieren, können Sie die eingerichteten Anwendungsidentitäten der Benutzer den jeweiligen Azure AD-Identitäten zuordnen.

<!---HONumber=Oct15_HO3-->
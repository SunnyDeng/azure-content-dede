<properties title="Error During Authentication Detection" pageTitle="Error During Authentication Detection" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

#### Fehler während der Authentifizierungserkennung
Beim Erkennen des vorherigen Authentifizierungscodes hat der Assistent einen nicht kompatiblen Authentifizierungstyp erkannt.

###### Was wird überprüft?

Der Assistent versucht, Versionen von Authentifizierungscode zu erkennen, die mit früheren Versionen von Visual Studio konfiguriert wurden. Wenn Sie diesen Fehler erhalten, ist ein nicht kompatibler Authentifizierungstyp vorhanden. Der Assistent erkennt die folgenden Authentifizierungstypen aus früheren Versionen von Visual Studio:

* Windows-Authentifizierung
* Einzelne Benutzerkonten
* Organisationskonten

Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD][Authentifizierungsszenarien für Azure AD].

  [Authentifizierungsszenarien für Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx

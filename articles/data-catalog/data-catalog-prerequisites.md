<properties
   pageTitle="Azure Data Catalog-Voraussetzungen"
   description="Wie steige ich in Azure Data Catalog ein?"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/11/2015"
   ms.author="maroche"/>

# Wie steige ich in Azure Data Catalog ein?

## Azure Data Catalog-Voraussetzungen

Es gibt einige Dinge, die Sie erledigen müssen, bevor Sie **Azure Data Catalog** einrichten können. Keine Sorge, das wird nicht lange dauern!

## Azure Active Directory

Azure Active Directory (Azure AD) bietet Ihrem Unternehmen eine einfache Möglichkeit für die Identitäts- und Zugriffsverwaltung – in der Cloud und lokal. Benutzer können ein einzelnes Arbeits- oder Schulkonto für das einmalige Anmelden bei Webanwendungen in der Cloud oder lokalen Webanwendung verwenden. Azure Data Catalog verwendet Azure AD für die Authentifizierung bei der Anmeldung. Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD](active-directory-get-started.md).

## Active Directory-Richtlinienkonfiguration

Es gibt Situationen, in denen Benutzer sich beim Azure Data Catalog-Portal anmelden können, jedoch bei dem Versuch, sich beim Tool zum Registrieren von Datenquellen anzumelden, eine Fehlermeldung erhalten, die die Anmeldung verhindert. Dieses Problem tritt möglicherweise nur dann auf, wenn der Benutzer über das Unternehmensnetzwerk arbeitet oder wenn der Benutzer von außerhalb des Unternehmensnetzwerks eine Verbindung herstellt.

Das Tool zum Registrieren von Datenquellen verwendet die Formularauthentifizierung, um Benutzeranmeldungen für Active Directory zu überprüfen. Für eine erfolgreiche Anmeldung muss die Formularauthentifizierung durch einen Active Directory-Administrator in der globalen Authentifizierungsrichtlinie aktiviert sein.

Mit der globalen Authentifizierungsrichtlinie können separate Authentifizierungsmethoden für Intranet- und Extranetverbindungen aktiviert werden, wie nachstehend veranschaulicht. Anmeldefehler können auftreten, wenn die Formularauthentifizierung nicht für das Netzwerk aktiviert ist, aus dem der Benutzer eine Verbindung herstellt.

 ![Globale Authentifizierungsrichtlinie für Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Weitere Informationen finden Sie unter [Konfigurieren von Authentifizierungsrichtlinien](https://technet.microsoft.com/de-DE/library/dn486781.aspx).


## Azure-Abonnement
Mit Azure-Abonnements können Sie den Zugriff auf die Clouddienstressourcen wie Azure Data Catalog organisieren. Mit diesen Abonnements können Sie darüber hinaus steuern, wie die Ressourcennutzung gemeldet, abgerechnet und bezahlt wird. Jedes Abonnement kann in Bezug auf Abrechnung und Zahlung unterschiedlich eingerichtet sein, daher können Sie je nach Abteilung, Projekt, Niederlassung usw. verschiedene Abonnements und verschiedene Pläne haben. Jeder Clouddienst gehört zu einem Abonnement, und Sie benötigen ein Abonnement vor dem Einrichten von Azure Data Catalog. Weitere Informationen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](https://msdn.microsoft.com/library/azure/hh531793.aspx).

<!---HONumber=Oct15_HO3-->
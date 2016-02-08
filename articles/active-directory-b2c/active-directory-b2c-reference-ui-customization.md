<properties
	pageTitle="Azure Active Directory B2C-Vorschau: Anpassung der Benutzeroberfläche (UI) | Microsoft Azure"
	description="Ein Thema über das Anpassungsfeatures für die Benutzeroberfläche (UI) in Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C-Vorschau: Anpassen der Azure AD B2C-Benutzeroberfläche (UI)

Benutzerfreundlichkeit ist in einer kundenorientierten Anwendung entscheidend. Dies ist der Unterschied zwischen einer guten Anwendung und einer hervorragenden – und zwischen lediglich aktiven Consumern und wirklich engagierten. Azure Active Directory (AD) B2C ermöglicht es Ihnen, die Seiten für die Registrierung und Anmeldung von Consumern (*siehe Hinweis unten*) und für die Profilbearbeitung mit einer präzisen Kontrolle anzupassen.

> [AZURE.NOTE]
Gegenwärtig sind Anmeldeseiten für lokale Konten, Überprüfungs-E-Mails und Seiten zum eigenständigen Zurücksetzen des Kennworts nur mithilfe des [Features für Unternehmensbranding](./active-directory/active-directory-add-company-branding.md) anpassbar, nicht durch die Mechanismen, die in diesem Artikel beschrieben sind.

Dieser Artikel enthält Folgendes:

- Übersicht über das Anpassungsfeature für die Seitenbenutzeroberfläche (UI).
- Ein Hilfsprogramm, mit dem Sie das Anpassungsfeature für die Seiten-UI mit unserem Beispielinhalt testen können.
- Die wichtigsten UI-Elemente in jedem Seitentyp.
- Bewährte Methoden beim Üben des Umgangs mit diesem Feature.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Anpassungsfeature für die Seitenbenutzeroberfläche (UI)

Mit dem Anpassungsfeature für die Seiten-UI können Sie das Aussehen und Verhalten der Seiten für die Registrierung und Anmeldung von Consumern und für die Profilbearbeitung anpassen (durch Konfigurieren von [Richtlinien](active-directory-b2c-reference-policies.md)). Beim Navigieren zwischen der Anwendung und den Seiten, die vom Azure AD B2C-Dienst verarbeitet werden, wird den Consumern eine einheitliche Benutzeroberfläche angeboten.

Im Gegensatz zu anderen Diensten, in denen Optionen beschränkt oder nur über APIs verfügbar sind, verwendet Azure AD B2C eine moderne (und einfachere) Vorgehensweise bei der Anpassung der Seiten-UI. So funktioniert es: Azure AD B2C führt Code im Browser des Consumers aus und verwendet einen modernen Ansatz namens [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/) zum Laden des Inhalts von einer URL, die Sie in einer Richtlinie angeben. Sie können verschiedene URLs für unterschiedliche Seiten angeben. Der Code führt den Azure AD B2C-Inhalt (UI-Elemente genannt) und den Inhalt, der von der URL geladen wird, zusammen und zeigt die Seite für den Consumer an. Sie müssen lediglich wohlgeformten HTML5-Inhalt mit einem `<div id="api"></div>`-Element irgendwo in `<body>` erstellen, wo der Azure AD B2C-Inhalt zusammengeführt wird. Hosten Sie diese Inhalte auf einem HTTPS-Endpunkt (auf dem CORS zulässig ist). Sie können zudem Azure AD B2C-UI-Elemente vollständig formatieren.

## Ausprobieren des Anpassungsfeatures für die Benutzeroberfläche

Wenn Sie das Anpassungsfeature für die Benutzeroberfläche mithilfe unseres auf dem Azure-Blobspeicher gehosteten HTML- und CSS-Beispielinhalts ausprobieren möchten, haben wir [ein einfaches Hilfsprogramm](active-directory-b2c-reference-ui-customization-helper-tool.md) zum Hochladen und Konfigurieren des statischen Inhalts bereitgestellt.

## Die wichtigsten UI-Elemente in jedem Seitentyp

Im folgenden Abschnitt finden Sie Beispiele für HTML5-Fragmente (für jeden Seitentyp), die Azure AD B2C im <div id="api"></div>-Element in Ihrem Inhalt zusammenführt. Sie können diese UI-Elemente mit Ihren eigenen Stylesheets anpassen. Diese Stylesheets müssen die standardmäßigen Stylesheets überschreiben, die wir in diesen Seiten in den <head>-Fragmenten hinzufügen.

> [AZURE.IMPORTANT]
	Gehen Sie in der Vorschau davon aus, dass sich die genauen UI-Elemente ändern, da sie an Ihr Feedback angepasst werden. Überprüfen Sie immer den Quellcode der Standardseiten auf die neuesten Aktualisierungen. Tatsächlich ist die erste Änderung, die berücksichtigt wird, das Entfernen unserer Standardstylesheets. Dies bedeutet, dass Sie immer Ihre eigenen Stylesheets für diese UI-Elemente in Ihrem Inhalt bereitstellen müssen.

## Auswahlseite für Identitätsanbieter

Diese Seite enthält eine Liste der Identitätsanbieter, aus denen der Benutzer während der Registrierung und Anmeldung auswählen kann. Dies sind entweder Identitätsanbieter in Form von sozialen Netzwerken wie Facebook und Google+ oder lokale Konten (basierend auf E-Mail-Adressen oder Benutzernamen).

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>

	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

## Registrierungsseite für lokales Konto

Diese Seite enthält ein Registrierungsformular, das der Benutzer ausfüllen muss, wenn die Registrierung mit einer auf einem lokalen Konto basierenden E-Mail-Adresse oder einem entsprechenden Benutzernamen erfolgt. Das Formular kann verschiedene Eingabesteuerelemente enthalten, z. B. Texteingabefelder, Kennworteingabefelder, Optionsfelder, Dropdownlisten mit einer Auswahlmöglichkeit und Kontrollkästchen mit mehreren Optionen.

```HTML

<div id="api" data-name="SelfAsserted">
	<div class="intro">
		<p>Create your account by providing the following details</p>
	</div>

	<div id="attributeVerification">
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList">
			<ul>
				<li>
					<div class="attrEntry validate">
						<div>
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email">
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
						</div>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul>
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

## Registrierungsseite für Konten sozialer Netzwerke

Diese Seite enthält ein Registrierungsformular, das der Consumer ausfüllen muss, wenn die Registrierung mit einem vorhandenen Konto bei einem sozialen Netzwerk wie Facebook oder Google+ als Identitätsanbieter erfolgt. Diese Seite ähnelt der Registrierungsseite für lokale Konten (siehe Abschnitt oben), eine Ausnahme bilden die Kennworteingabefelder.

## Seite für Multi-Factor Authentication

Diese Seite ermöglicht es Benutzern, während der Registrierung oder Anmeldung ihre Telefonnummern (mit Text oder Stimme) zu überprüfen.

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

## Fehlerseite


```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## Wichtige Aspekte beim Erstellen der eigenen Inhalte

Wenn Sie das Anpassungsfeature für die Seiten-UI verwenden möchten, beachten Sie die folgenden bewährten Methoden:

- Kopieren Sie nicht die Azure AD B2C-Standardvorlage, um zu versuchen, sie zu ändern. Es wird empfohlen, eigene HTML5-Inhalte von Grund auf neu zu erstellen und die Standardvorlage als Referenz zu verwenden.
- Aus Gründen der Sicherheit ist es nicht zulässig, JavaScript in Ihren Inhalt aufzunehmen. Das meiste, was Sie benötigen, sollte ohne Bearbeitungsaufwand verfügbar sein. Falls nicht, fordern Sie über [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/) neue Funktionen an.
- Unterstützte Browserversionen:
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (eingeschränkt)
	- Internet Explorer 8 (eingeschränkt)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=AcomDC_0128_2016-->
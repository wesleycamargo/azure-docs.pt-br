<properties
	pageTitle="Visualização do Active Directory B2C do Azure: personalização de IU (interface do usuário) | Microsoft Azure"
	description="Um tópico sobre os recursos de personalização de IU (Interface do usuário) no Active Directory B2C do Azure"
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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: Como Personalizar a IU (Interface do Usuário) do AD B2C do Azure

A experiência do usuário é fundamental em um aplicativo voltado ao consumidor. É a diferença entre um bom aplicativo e um aplicativo ótimo e também a diferença entre consumidores simplesmente ativos e aqueles realmente comprometidos. O AD (Active Directory) B2C do Azure permite que você personalize a inscrição e entrada do consumidor (*consulte a observação abaixo*) e as páginas de edição de perfil com controle perfeito de pixels.

> [AZURE.NOTE]Atualmente, as páginas de entrada de conta local, emails de verificação e páginas de redefinição de senhas de autoatendimento só podem ser personalizados usando o [recurso de marca da empresa](./active-directory/active-directory-add-company-branding.md) e não pelos mecanismos descritos neste artigo.

Neste artigo, você lerá sobre:

- Visão geral do recurso de personalização da IU (interface do usuário) da página.
- Uma ferramenta auxiliar que ajudará você a testar o recurso de personalização da interface do usuário da página usando nosso conteúdo de exemplo.
- Os elementos principais da interface do usuário em cada tipo de página.
- Práticas recomendadas ao exercitar esse recurso.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Recurso de Personalização da IU (Interface do Usuário) da Página

O recurso de personalização da interface do usuário de página permite que você personalize a aparência das páginas de inscrição, entrada edição de perfil do consumidor (pela configuração de [políticas](active-directory-b2c-references.policies.md)). Seus consumidores terão experiências consistentes ao navegar entre seu aplicativo e as páginas atendidas pelo serviço do AD B2C do Azure.

Ao contrário de outros serviços no qual as opções são limitadas ou só estão disponíveis por meio de APIs, o AD B2C do Azure usa uma abordagem moderna (e mais simples) para a personalização da interface do usuário da página. Eis aqui como ele funciona: o AD B2C do Azure executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (Compartilhamento de Recursos Entre Origens)](http://www.w3.org/TR/cors/) para carregar conteúdo proveniente de uma URL que você especifica em uma política. Você pode especificar URLs diferentes para diferentes páginas. O código mescla o conteúdo do AD B2C do Azure (chamado de elementos de interface do usuário) e o conteúdo carregado por meio de sua URL e exibe a página para o consumidor. Tudo que você precisa fazer é criar um conteúdo HTML5 bem-formado com um elemento `<div id="api"></div>` localizado em algum lugar no `<body>` - é para este local que o conteúdo do AD B2C do Azure é mesclado. E hospede esse conteúdo em um ponto de extremidade HTTPS (com CORS permitido). Você também pode formatar totalmente o estilo dos elementos da interface do usuário do AD B2C do Azure.

## Uma Ferramenta Auxiliar que ajudará você a Testar o Recurso de Personalização da interface do usuário da Página com nosso Conteúdo de Exemplo

Use nossa ferramenta auxiliar (Em breve) para experimentar o recurso de personalização da interface do usuário da página usando nosso conteúdo de exemplo.

## Os Elementos principais da interface do usuário em cada Tipo de Página.

Nas seções abaixo, você encontrará exemplos dos fragmentos de HTML5 (para cada tipo de página) que o AD B2C do Azure mescla no elemento <div id="api"></div> localizado em seu conteúdo. Você pode usar suas próprias folhas de estilo para personalizar esses elementos de interface do usuário. Essas folhas de estilo precisarão substituir as folhas de estilo padrão que adicionarmos a essas páginas nos fragmentos de <head>.

> [AZURE.IMPORTANT]Durante a visualização, espere que os elementos exatos da interface do usuário sofram modificações conforme aprendemos e nos adaptamos com base em seus comentários. Sempre Inspecione o código-fonte das páginas padrão para as atualizações mais recentes. Na verdade, a primeira alteração a ser considerada é a remoção de nossas folhas de estilo padrão; isso significa que você sempre terá de fornecer suas próprias folhas de estilo para esses elementos de interface do usuário em seu conteúdo.

## Página de Seleção do Provedor de Identidade

Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição ou entrada. Esses são os provedores de identidade social, como Facebook e Google+, ou então contas locais (baseadas em endereço de email ou nome de usuário).

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

## Página de Inscrição de Conta Local

Esta página contém um formulário de inscrição que o usuário deve preencher ao entrar, usando uma conta local baseada em nome de usuário ou endereço de email. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, lista suspensa de seleção única e caixas de multisseleção.

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

## Página de Inscrição de Conta Social

Esta página contém um formulário de inscrição que o consumidor precisa preencher ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou Google+. Esta página é semelhante à página de inscrição de conta local (mostrada na seção anterior) com exceção dos campos de entrada de senha.

## Página Multi-Factor Authentication

Esta página permite que os usuários verifiquem seus números de telefone (usando texto ou voz) durante a inscrição ou entrada.

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

## Página de Erro


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

## Coisas para lembrar ao criar seu próprio Conteúdo

Se você estiver planejando usar o recurso de personalização de interface do usuário da página, examine as seguintes práticas recomendadas:

- Não copie substituindo o modelo de padrão do AD B2C do Azure nem tente modificá-lo. É melhor criar o conteúdo HTML5 do zero e usar o modelo padrão como referência.
- Por motivos de segurança, não permitimos incluir nenhum JavaScript em seu conteúdo. A maior parte do que você precisa deve estar disponível imediatamente. Caso contrário, use a [Voz do Usuário](http://feedback.azure.com/forums/169401-azure-active-directory) para solicitar novas funcionalidades.
- Versões do navegador para as quais há suporte:
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (limitado)
	- Internet Explorer 8 (limitado)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=Sept15_HO4-->
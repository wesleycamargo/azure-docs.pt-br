---
title: Códigos de erro de autenticação e autorização do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os códigos de erro do AADSTS que são retornados do serviço de token de segurança (STS) do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: 0643ae39d24f8ef0c1c1c18dcf73cf840c95e277
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956313"
---
# <a name="authentication-and-authorization-error-codes"></a>Códigos de erro de autenticação e autorização

Procurando informações sobre os códigos de erro AADSTS que são retornados do serviço de token de segurança (STS) do Azure Active Directory (Azure AD)? Leia este documento para encontrar descrições de erros, correções e algumas sugestões sugeridas do AADSTS.

> [!NOTE]
> Esta informação é preliminar e está sujeita a alterações. Tem alguma dúvida ou não consegue encontrar o que está procurando? Crie um problema no GitHub ou consulte [Suporte e opções de ajuda para desenvolvedores](active-directory-develop-help-support.md) para aprender sobre outras maneiras de obter ajuda e suporte.

## <a name="aadsts-error-codes"></a>Códigos de erro AADSTS

| Erro | DESCRIÇÃO |
|---|---|
| AADSTS16000 | SelectUserAccount - Esta é uma interrupção lançada pelo Azure AD, que resulta na interface do usuário que permite ao usuário selecionar entre várias sessões válidas do SSO. Esse erro é bastante comum e pode ser retornado ao aplicativo se `prompt=none` for especificado. |
| AADSTS16001 | UserAccountSelectionInvalid - Você verá esse erro se o usuário clicar em um bloco que a lógica de seleção da sessão rejeitou. Quando acionado, esse erro permite que o usuário recupere escolhendo de uma lista atualizada de blocos / sessões ou escolhendo outra conta. Este erro pode ocorrer devido a um defeito no código ou condição de corrida. |
| AADSTS16002 | AppSessionSelectionInvalid - O requisito de SID especificado pelo aplicativo não foi atendido.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant |
| AADSTS17003 | CredentialKeyProvisioningFailed |
| AADSTS20001 | WsFedSignInResponseError - Há um problema com seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS20012 | WsFedMessageInvalid - Há um problema com seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS20033 | FedMetadataInvalidTenantName - Há um problema com seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError - há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Existe um problema com o seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS50000 | TokenIssuanceError - há um problema com o serviço de logon. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver esse problema. |
| AADSTS50001 | InvalidResource - O recurso está desabilitado ou não existe. Verifique o código do seu aplicativo para garantir que você especificou o URL exato do recurso que você está tentando acessar.  |
| AADSTS50002 | NotAllowedTenant - O login falhou devido ao acesso ao proxy restrito no locatário. Se for sua própria política de inquilino, você poderá alterar suas configurações de inquilino restrito para corrigir esse problema. |
| AADSTS50003 | MissingSigningKey - O login falhou devido à falta de chave de assinatura ou certificado. Isso pode ter ocorrido porque não havia uma chave de assinatura configurada no aplicativo. Confira as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se você ainda encontrar problemas, entre em contato com o proprietário do aplicativo ou com um administrador do aplicativo. |
| AADSTS50005 | DevicePolicyError - O usuário tentou efetuar login em um dispositivo a partir de uma plataforma que atualmente não é suportada pela política de acesso condicional. |
| AADSTS50006 | InvalidSignature - verificação de assinatura falhou devido a uma assinatura inválida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - O certificado de criptografia de parceiro não foi encontrado para este aplicativo. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para obter esse fixo. |
| AADSTS50008 | InvalidSamlToken - A asserção SAML está ausente ou está mal configurada no token. Entre em contato com seu provedor de federação. |
| AADSTS50010 | AudienceUriValidationFailed - A validação do URI de público para o aplicativo falhou, pois nenhum público-alvo de token foi configurado. |
| AADSTS50011 | InvalidReplyTo - O endereço de resposta está ausente, está mal configurado ou não corresponde aos endereços de resposta configurados para o aplicativo. Teste a resolução listada em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se você ainda encontrar problemas, entre em contato com o proprietário do aplicativo ou o administrador do aplicativo. |
| AADSTS50012 | AuthenticationFailed - A autenticação falhou por um dos seguintes motivos:<ul><li>O nome da entidade do certificado de assinatura não está autorizado</li><li>Uma política de autoridade confiável correspondente não foi encontrada para o nome do assunto autorizado</li><li>A cadeia de certificados não é válida</li><li>O certificado de assinatura não é válido</li><li>A política não está configurada no inquilino</li><li>A impressão digital do certificado de assinatura não é autorizada</li><li>A declaração do cliente contém uma assinatura inválida</li></ul> |
| AADSTS50013 | InvalidAssertion - A declaração é inválida devido a várias razões - O emissor do token não corresponde à versão da API dentro de seu intervalo de tempo válido - expirado - mal formado - O token de atualização na asserção não é um token de atualização principal. |
| AADSTS50014 | GuestUserInPendingState |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState |
| AADSTS50017 | Falha na validação do certificado - Falha na validação do certificado, pelos seguintes motivos:<ul><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Não é possível localizar o CrlSegment esperado</li><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Ponto de distribuição de CRL delta é configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível recuperar segmentos CRL válidos devido a um problema de tempo limite</li><li>Não é possível baixar o CRL</li></ul>Entre em contato com o administrador de locatário. |
| AADSTS50020 | UserUnauthorized - Os usuários não são autorizados a chamar este endpoint. |
| AADSTS50027 | InvalidJwtToken - token JWT inválido devido aos seguintes motivos:<ul><li>não contém declaração nonce, subdeclaração</li><li>incompatibilidade do identificador do assunto</li><li>declaração duplicada nas declarações de idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro de seu intervalo de tempo válido </li><li>o formato do token não é apropriado</li><li>falha na verificação de assinatura do token de ID externa do emissor.</li></ul> |
| AADSTS50029 | URI inválida – o nome de domínio contém caracteres inválidos. Entre em contato com o administrador de locatário. |
| AADSTS50032 | WeakRsaKey - Indica a tentativa incorreta do usuário de usar uma chave RSA fraca. |
| AADSTS50033 | RetryableError - Indica um erro temporário não relacionado às operações do banco de dados. |
| AADSTS50034 | UserAccountNotFound - Para entrar neste aplicativo, a conta deve ser adicionada ao diretório. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - O sal necessário para gerar um identificador de pares está ausente no principal. Entre em contato com o administrador de locatário. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - incompatibilidades de assunto emissor de declaração na asserção de cliente. Entre em contato com o administrador de locatário. |
| AADSTS50049 | NoSuchInstanceForDiscovery - instância desconhecida ou inválida. |
| AADSTS50050 | MalformedDiscoveryRequest - a solicitação está malformada. |
| AADSTS50053 | IdsLocked - A conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta. |
| AADSTS50055 | InvalidPasswordExpiredPassword - A senha expirou. |
| AADSTS50056 | Senha inválida ou nula - A senha não existe na loja para esse usuário. |
| AADSTS50057 | UserDisabled - A conta de usuário está desativada. A conta foi desabilitada por um administrador. |
| AADSTS50058 | UserInformationNotProvided - Isso significa que um usuário não está conectado. Esse é um erro muito comum que é esperado quando um usuário não é autenticado e ainda não fez login.</br>Se esse erro for incentivado em um contexto de SSO em que o usuário acabou de se conectar, significa que a sessão de SSO não foi encontrada ou é inválida.</br>Esse erro pode ser retornado para o aplicativo se prompt = none for especificado. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - Informações de identificação de inquilino não foram encontradas na solicitação ou implícitas por nenhuma credencial fornecida. O usuário pode entrar em contato com o administrador do locatário para ajudar a resolver o problema. |
| AADSTS50061 | SignoutInvalidRequest - solicitação de saída é inválida. |
| AADSTS50064 | CredentialAuthenticationError |
| AADSTS50068 | SignoutInitiatorNotParticipant |
| AADSTS50070 | SignoutUnknownSessionIdentifier |
| AADSTS50071 | SignoutMessageExpired |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - O usuário precisa se inscrever para autenticação de segundo fator (interativo). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - É necessária uma autenticação forte e o usuário não passou no desafio do MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - O usuário deve usar a autenticação de vários fatores para acessar esse recurso. Tente novamente com uma nova solicitação de autorização para o recurso. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - Devido a uma alteração de configuração feita pelo administrador ou porque o usuário foi movido para um novo local, o usuário é solicitado a usar a autenticação multifator. |
| AADSTS50085 | O token de atualização precisa de logon do IDP social. Faça com que o usuário tente entrar novamente com o nome de usuário-senha |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError |
| AADSTS50089 | O token de fluxo expirou – Falha na autenticação. Faça com que o usuário tente entrar novamente com o nome de usuário-senha |
| AADSTS50097 | DeviceAuthenticationRequired - A autenticação do dispositivo é necessária. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - A assinatura do JWT é inválida. |
| AADSTS50105 | EntitlementGrantsNotFound - O usuário conectado não está atribuído a uma função para o aplicativo conectado. Assign the user  to the app. Para obter mais informações:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri - O objeto de região de federação solicitado não existe. Entre em contato com o administrador de locatário. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - problema com cabeçalho JWT. Entre em contato com o administrador de locatário. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - Claims Transformation contém um parâmetro de entrada inválido. Entre em contato com o administrador de locatário para atualizar a política. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - O login foi interrompido devido a uma redefinição de senha ou entrada de registro de senha. |
| AADSTS50126 | InvalidUserNameOrPassword - Erro ao validar credenciais devido a nome de usuário ou senha inválidos. |
| AADSTS50127 | BrokerAppNotInstalled - O usuário precisa instalar um aplicativo do broker para obter acesso a esse conteúdo. |
| AADSTS50128 | Nome de domínio inválido – Nenhuma informação de identificação de locatário foi encontrada na solicitação nem está implícita em quaisquer credenciais fornecidas|
| AADSTS50129 | DeviceIsNotWorkplaceJoined - A junção no local de trabalho é necessária para registrar o dispositivo. |
| AADSTS50131 | ConditionalAccessFailed - Indica vários erros de acesso condicional, como estado de dispositivo ruim do Windows, solicitação bloqueada devido a atividades suspeitas, política de acesso ou decisões de política de segurança. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - A sessão não é válida devido à expiração da senha ou alteração recente de senha. |
| AADSTS50133 | SsoArtifactRevoked - A sessão não é válida devido à expiração da senha ou alteração recente de senha. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter |
| AADSTS50135 | PasswordChangeCompromisedPassword - A alteração da senha é necessária devido ao risco da conta. |
| AADSTS50136 | RedirectMsaSessionToApp - sessão única do MSA detectada. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken |
| AADSTS50140 | KmsiInterrupt - Este erro ocorreu devido à interrupção "Mantenha-me conectado" quando o usuário estava fazendo login. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes. |
| AADSTS50143 | Incompatibilidade de sessão – A sessão é inválida porque o locatário do usuário não corresponde a dica de domínio devido a diferentes recursos. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - A senha do Active Directory do usuário expirou. Gere uma nova senha para o usuário ou peça para o usuário usar a ferramenta de redefinição de autoatendimento para redefinir sua senha. |
| AADSTS50146 | MissingCustomSigningKey - esse aplicativo precisa ser configurado com uma chave de assinatura específica do aplicativo. Ele não está configurado com uma, ou a chave expirou ou ainda não é válida. |
| AADSTS50147 | MissingCodeChallenge |
| AADSTS50155 | DeviceAuthenticationFailed - Falha na autenticação do dispositivo para este usuário. |
| AADSTS50158 | ExternalSecurityChallenge - O desafio de segurança externa não foi satisfeito. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - As reclamações enviadas por um provedor externo não são suficientes ou a Declaração em falta é solicitada ao provedor externo. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Falha ao enviar solicitação para o provedor de declarações. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - O cliente é capaz de obter um token SSO por meio da extensão de contas do Windows 10, mas o token não foi encontrado na solicitação ou o token fornecido expirou. |
| AADSTS50169 | InvalidRequestBadRealm - O território não é um território configurado do namespace de serviço atual. |
| AADSTS50170 | MissingExternalClaimsProviderMapping |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - O desafio externo não é suportado para usuários passantes. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - O controle de sessão não é suportado para usuários de passagem. |
| AADSTS50180 | WindowsIntegratedAuthMissing - É necessária uma autenticação integrada do Windows. Habilite o locatário para um SSO contínuo. |
| AADSTS50187 | DeviceInformationNotProvided |
| AADSTS51000 | RequiredFeatureNotEnabled |
| AADSTS51001 | DomainHintMustbePresent - A dica de domínio deve estar presente com o identificador de segurança local ou o UPN local. |
| AADSTS51004 | UserAccountNotInDirectory - a conta de usuário não existe no diretório. |
| AADSTS51005 | TemporaryRedirect - Equivalente ao status HTTP 307, que indica que as informações solicitadas estão localizadas no URI especificado no cabeçalho do local. Quando você receber esse status, siga o cabeçalho de local associado à resposta. Quando o método de solicitação original era POST, a solicitação redirecionada também usa o método POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - a autenticação integrada do Windows é necessária. Usuário que efetuou login usando um token de sessão que não possui a declaração de autenticação integrada do Windows. Solicite ao usuário que efetue login novamente. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - O usuário não forneceu consentimento para acessar recursos do LinkedIn. |
| AADSTS53000 | DeviceNotCompliant - A política de acesso condicional requer um dispositivo compatível e o dispositivo não é compatível. O usuário deve inscrever seu dispositivo em um provedor de MDM aprovado, como o Intune. |
| AADSTS53001 | DeviceNotDomainJoined - A política de acesso condicional requer um dispositivo associado ao domínio e o dispositivo não é associado ao domínio. O usuário use um domínio tenha ingressado no dispositivo. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - O aplicativo usado não é um aplicativo aprovado para acesso condicional. O usuário precisa usar um dos aplicativos da lista de aplicativos aprovados para usar a fim de obter acesso. |
| AADSTS53003 | BlockedByConditionalAccess - O acesso foi bloqueado por políticas de acesso condicional. A política de acesso não permite a emissão de token. |
| AADSTS53004 | ProofUpBlockedDueToRisk - O usuário precisa concluir o processo de registro de autenticação multifator antes de acessar este conteúdo. O usuário deve se registrar para a autenticação multifator. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - O usuário ou administrador não consentiu em usar o aplicativo com o ID X. Envie uma solicitação de autorização interativa para esse usuário e recurso. |
| AADSTS65004 | UserDeclinedConsent - O usuário recusou o consentimento para acessar o aplicativo. Peça que o usuário tente entrar novamente e dê consentimento ao aplicativo|
| AADSTS65005 | Configuração incorreta - A lista de acesso de recursos necessária ao aplicativo não contém aplicativos detectáveis pelo recurso ou O aplicativo cliente solicitou acesso ao recurso, que não foi especificado em sua lista de acesso a recursos necessária ou Serviço de gráfico retornou solicitação incorreta ou recurso não encontrado. Se o aplicativo dá suporte a SAML, você pode ter configurado o aplicativo com o identificador incorreto (entidade). Experimente a resolução listada para SAML usando o link abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - Falha na autenticação. O token de atualização não é válido. O erro pode ser devido aos seguintes motivos:<ul><li>O cabeçalho de vinculação de token está vazio</li><li>Token binding hash não corresponde</li></ul> |
| AADSTS70001 | UnauthorizedClient - o aplicativo está desabilitado. |
| AADSTS70002 | InvalidClient - Erro ao validar as credenciais. O client_secret especificado não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente novamente. Para mais informações, consulte [Use o código de autorização para solicitar um token de acesso](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType - O aplicativo retornou um tipo de concessão não suportado. |
| AADSTS70004 | InvalidRedirectUri - o aplicativo retornou um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum endereço configurado ou a quaisquer endereços na lista de aprovação OIDC. |
| AADSTS70005 | UnsupportedResponseType - O aplicativo retornou um tipo de resposta não suportado devido aos seguintes motivos:<ul><li>tipo de resposta 'token' não está habilitado para o aplicativo</li><li>o tipo de resposta “id_token” requer escopo “OpenID” – contém um valor de parâmetro OAuth sem suporte no wctx codificado</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - O aplicativo retornou um valor não suportado de `response_mode` ao solicitar um token.  |
| AADSTS70008 | ExpiredOrRevokedGrant - O token de atualização expirou devido à inatividade. O token foi emitido no XXX e ficou inativo por um determinado período. |
| AADSTS70011 | InvalidScope - O escopo solicitado pelo aplicativo é inválido. |
| AADSTS70012 | MsaServerError - Ocorreu um erro no servidor durante a autenticação de um usuário MSA (consumidor). Tente novamente. Se continuar a falhar, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - Erro no fluxo do dispositivo OAuth 2.0. A autorização está pendente. O dispositivo tentará novamente a consulta. |
| AADSTS70018 | BadVerificationCode - Código de verificação inválido devido ao usuário digitar o código do usuário errado para o fluxo do código do dispositivo. Autorização não é aprovada. |
| AADSTS70019 | CodeExpired - O código de verificação expirou. Solicite ao usuário de tentar novamente na entrada. |
| AADSTS75001 | BindingSerializationError - Ocorreu um erro durante a ligação da mensagem SAML. |
| AADSTS75003 | UnsupportedBindingError - O aplicativo retornou um erro relacionado à ligação não suportada (a resposta do protocolo SAML não pode ser enviada por meio de ligações que não sejam HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid - o Azure AD não suporta a solicitação SAML enviada pelo aplicativo para SSO. |
| AADSTS75008 | RequestDeniedError - a solicitação do aplicativo foi negada, pois a solicitação SAML tinha um destino inesperado. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - O método de autenticação pelo qual o usuário autenticado com o serviço não corresponde ao método de autenticação solicitado. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - A Solicitação de Autenticação SAML2 possui NameIdPolicy inválido. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - O Agente de Autenticação não consegue se conectar ao Active Directory. Certifique-se de que os servidores de agentes sejam membros da mesma floresta do AD que os usuários cujas senhas precisam ser validadas e possam se conectar ao Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - A solicitação de validação de senha expirou. Certifique-se de que o Active Directory esteja disponível e responda às solicitações dos agentes. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - Ocorreu um erro desconhecido ao processar a resposta do Agente de Autenticação. Tente novamente a solicitação. Se continuar a falhar, [abra um ticket de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - O Agente de Autenticação não pode validar a senha do usuário. Verifique os logs do agente para obter mais informações e verifique se o Active Directory está operando conforme o esperado. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - O Agente de Autenticação não pode descriptografar a senha. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - Os usuários tentaram efetuar logon fora dos horários permitidos (isso é especificado no AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - A tentativa de autenticação não pôde ser concluída devido à distorção de tempo entre a máquina que está executando o agente de autenticação e o AD. Corrigir problemas de sincronização de horário. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Falha na tentativa de autenticação do Kerberos. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - O pacote de autenticação não é suportado. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Nenhum cabeçalho de autorização foi encontrado. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - O locatário não está habilitado para SSO Contínuo. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Não é possível validar o ticket Kerberos do usuário. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Falha no SSO Contínuo porque o ticket Kerberos do usuário expirou ou é inválido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Não é possível localizar o objeto do usuário com base nas informações do ticket Kerberos do usuário. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - O usuário que está tentando entrar no Azure AD é diferente do usuário conectado ao dispositivo. |
| AADSTS90002 | InvalidTenantName - O nome do inquilino não foi encontrado no armazenamento de dados. Check to make sure you have the correct tenant ID. |
| AADSTS90004 | InvalidRequestFormat |
| AADSTS90005 | InvalidRequestWithMultipleRequirements |
| AADSTS90006 | ExternalServerRetryableError |
| AADSTS90007 | InvalidSessionId |
| AADSTS90008 | TokenForItselfRequiresGraphPermission |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier |
| AADSTS90010 | NotSupported |
| AADSTS90012 | RequestTimeout |
| AADSTS90013 | InvalidUserInput |
| AADSTS90014 | MissingRequiredField - Esse código de erro pode aparecer em vários casos quando um campo esperado não está presente na credencial. |
| AADSTS90015 | QueryStringTooLong |
| AADSTS90016 | MissingRequiredClaim |
| AADSTS90019 | MissingTenantRealm - O Azure AD não pôde determinar o identificador de inquilino da solicitação. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - O formato do nome principal não é válido ou não atende ao formato `name[/host][@realm]` esperado. O nome principal é obrigatório, o host e o domínio são opcionais e podem ser definidos como nulos. |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError |
| AADSTS90033 | MsodsServiceUnavailable |
| AADSTS90036 | MsodsServiceUnretryableFailure |
| AADSTS90038 | NationalCloudTenantRedirection - O inquilino especificado 'Y' pertence ao National Cloud 'X'. A instância atual da nuvem 'Z' não se associa com o X. Um erro de redirecionamento da nuvem é retornado. |
| AADSTS90043 | NationalCloudAuthCodeRedirection |
| AADSTS90051 | InvalidNationalCloudId |
| AADSTS90055 | TenantThrottlingError - Há muitas solicitações recebidas. Esta exceção é lançada para inquilinos bloqueados. |
| AADSTS90056 | BadResourceRequest - Para resgatar o código de um token de acesso, o aplicativo deve enviar uma solicitação POST para o endpoint `/token`. Além disso, antes disso, você deve fornecer um código de autorização e enviá-lo na solicitação POST para o nó de extremidade `/token`. Consulte este artigo para obter uma visão geral do fluxo do código de autorização do OAuth 2.0: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Você deve primeiro direcionar o usuário para o nó de extremidade `/authorize`, que retornará um código de autorização. Ao postar uma solicitação para o endpoint `/token`, o usuário obtém o token de acesso. Faça logon no portal do Azure e verifique **Registros do aplicativo> Endpoints** para confirmar se os dois pontos de extremidade foram configurados corretamente. |
| AADSTS90072 | PassThroughUserMfaError |
| AADSTS90081 | OrgIdWsFederationMessageInvalid |
| AADSTS90082 | OrgIdWsFederationNotSupported |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed |
| AADSTS90090 | GraphRetryableError |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Gráfico retornado com um código de erro proibido para a solicitação. |
| AADSTS90094 | AdminConsentRequired |
| AADSTS90100 | InvalidRequestParameter |
| AADSTS90101 | InvalidEmailAddress |
| AADSTS90102 | InvalidUriParameter |
| AADSTS90107 | InvalidXml |
| AADSTS90114 | InvalidExpiryDate |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode |
| AADSTS90120 | InvalidDeviceFlowRequest |
| AADSTS90121 | InvalidEmptyRequest |
| AADSTS90123 | IdentityProviderAccessDenied |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported |
| AADSTS90125 | DebugModeEnrollTenantNotFound |
| AADSTS90126 | DebugModeEnrollTenantNotInferred |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated |
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound |
| AADSTS130005 | NgcInvalidSignature |
| AADSTS130006 | NgcTransportKeyNotFound |
| AADSTS130007 | NgcDeviceIsDisabled |
| AADSTS130008 | NgcDeviceIsNotFound |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce |
| AADSTS140001 | InvalidSessionKey |
| AADSTS165000 | InvalidRequestCanary |
| AADSTS165004 | InvalidApiCanary |
| AADSTS165900 | InvalidApiRequest |
| AADSTS220450 | UnsupportedAndroidWebViewVersion |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - O recurso não está configurado para aceitar tokens somente de dispositivo. |
| AADSTS240001 | BulkAADJTokenUnauthorized |
| AADSTS240002 | RequiredClaimIsMissing |
| AADSTS700020 | InteractionRequired |
| AADSTS700022 | InvalidMultipleResroucesScope |
| AADSTS700023 | InvalidResourcelessScope |
| AADSTS900000 | WebWatsonEnvironmentError |
| AADSTS1000000 | UserNotBoundError |
| AADSTS1000002 | BindCompleteInterruptError |

## <a name="next-steps"></a>Próximas etapas

* Tem alguma dúvida ou não consegue encontrar o que está procurando? Crie um problema no GitHub ou consulte [Suporte e opções de ajuda para desenvolvedores](active-directory-develop-help-support.md) para aprender sobre outras maneiras de obter ajuda e suporte.
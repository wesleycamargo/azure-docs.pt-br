<properties 
	pageTitle="Visão geral do Microsoft Passport e detalhes sobre essa nova autenticação com base em certificação | Microsoft Azure" 
	description="Um tópico que explica como os usuários podem configurar a Junção do Azure AD durante sua experiência de primeira execução." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Autenticando identidades sem senhas com o Microsoft Passport

Somente os atuais métodos de autenticação com senha não são suficientes para manter os usuários protegidos. Os usuários reutilizar e esquecem senhas. As senhas são propensas a sofrer violações, phishing e falhas e são fáceis de adivinhar. Elas também podem ser difíceis de lembrar e propensas a ataques como o "Pass the hash". Pass the hash é uma técnica de hackers que permite que um invasor seja autenticado em um servidor/serviço remoto usando o hash subjacente NTLM e/ou LanMan da senha do usuário, em vez de exigir a senha de texto sem formatação associada, como normalmente é o caso. Para obter mais informações sobre o Pass the hash, consulte [Pass the hash](https://technet.microsoft.com/pt-BR/dn785092.aspx).

## O que é o Microsoft Passport
O Microsoft Passport é uma nova abordagem à autenticação com chave privada/pública ou baseada em certificados para empresas e consumidores que vai além de senhas. Essa forma de autenticação se baseia nas credenciais de par de chaves que podem substituir senhas e ser resistentes a violações, roubos e phishing. O Microsoft Passport permite que os usuários se autentiquem em uma conta da Microsoft, uma conta do Active Directory, uma conta do Active Directory (AD) do Microsoft Azure ou um serviço que não seja da Microsoft mas que dê suporte à autenticação de Fast ID Online (FIDO). Após uma verificação inicial em duas etapas durante o registro no Microsoft Passport, um Microsoft Passport é configurado no dispositivo do usuário e o usuário define um gesto, que pode ser o Windows Hello ou um PIN. O usuário fornece o gesto para verificar a identidade. O Windows, em seguida, usa o Microsoft Passport para autenticar usuários e ajudá-los a acessar recursos e serviços protegidos.

A chave privada é disponibilizada somente por meio de um "gesto do usuário" - como um PIN, dados biométricos ou dispositivo remoto, como um cartão inteligente - que o usuário usou para entrar no dispositivo e essa informação é vinculada a um certificado ou um par de chaves assimétricas. Essa chave privada é atestada no hardware se o dispositivo tiver um chip do TPM (Trusted Platform Module). A chave privada nunca deixará o dispositivo.

A chave pública é registrada com o Active Directory do Azure e o Windows Server Active Directory (para local). Os provedores de identidade (IDPs) validam o usuário mapeando a chave pública do usuário para a chave privada e fornece informações de entrada por meio do OTP (One Time Password), Phonefactor ou um mecanismo de notificação diferente.
## Por que as empresas deveriam adotar o Microsoft Passport
Habilitando o Microsoft Passport, as empresas podem tornar seus recursos ainda mais seguros ao:

* Configurar o Microsoft Passport com uma opção preferencial de hardware, o que significa que chaves serão geradas no TPM 1.2 ou 2.0, quando disponível, e pelo software quando o TPM não estiver disponível. 

* Definir a complexidade e o comprimento do PIN, e se o uso do Hello está habilitado na sua organização

* Configurar o Microsoft Passport para dar suporte a cenários semelhantes ao uso de cartão inteligente, usando a confiança baseada em certificado.

## Como funciona
1. As chaves são geradas no hardware. Muitos computadores têm um chip do TPM (Trusted Platform Module) interno que protege o hardware integrando chaves criptográficas a dispositivos. O TPM 1.2 ou 2.0 é usado para gerar chaves ou certificados que terão as chaves geradas.

2. Essas chaves associadas ao hardware são atestadas pelo TPM.

3. Um gesto de desbloqueio único desbloqueia o dispositivo, e esse gesto poderá obter acesso a vários recursos se o dispositivo for unido ao domínio ou ao Azure AD.

## Ciclo de vida do Microsoft Passport
Ciclo de vida de autenticação do Microsoft Passport ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png) O diagrama acima ilustra o par de chaves pública-privada e a validação pelo provedor de identidade. Cada uma dessas etapas é explicada em detalhes a seguir:

1. O usuário comprova sua identidade por meio de vários métodos internos de verificação (gestos, cartões inteligentes físicos, autenticação multifator) e envia essas informações para o IDP (provedor de identidade), como o Active Directory do Azure ou o Active Directory.

2.  O dispositivo cria as chaves, atesta a chave, leva a parte pública dessa chave, a anexa a instruções de estação, entra e a envia ao IDP para registrar essa chave.

3. Assim que a parte pública da chave é registrada no IDP, ela desafia o dispositivo a entrar com a parte privada da chave. O IDP, em seguida, valida e emite o token de autenticação que permite ao usuário acessar recursos protegidos.

4. Assim que a parte pública da chave é registrada no IDP, ela desafia o dispositivo a entrar com a parte privada da chave.

5. O IDP, em seguida, valida e emite o token de autenticação que permite ao usuário e ao dispositivo acessar recursos protegidos. Os IDPs podem gravar em aplicativos multiplataforma ou usar o suporte ao navegador por meio de APIs JS/Webcrypto para criar e usar as credenciais do Microsoft Passport para seus usuários.

## Requisitos de implantação
No nível corporativo
---------------------------
* Assinatura do Azure

No nível do usuário
-------------------------------------------------------------
* O computador deve executar o Windows 10 Professional ou o SKU do Enterprise

## Informações adicionais

* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-overview.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)
* [Gerenciar a verificação de identidade usando o Microsoft Passport] (https://technet.microsoft.com/library/mt219735(v=vs.85).aspx)

<!---HONumber=Oct15_HO3-->
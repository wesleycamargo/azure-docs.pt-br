<properties 
	pageTitle="Azure Multi-Factor Authentication - o que é isso? | Microsoft Azure" 
	description="O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ela fornece uma camada adicional de segurança para logons de usuário e transações." 
	keywords="Introdução à MFA, visão geral de mfa"
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="billmath"/>

# O que é o Azure Multi-Factor Authentication?
O MFA (Multi-Factor Authentication) é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir:

- Algo que você sabe (normalmente, uma senha)
- Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
- Algo seu (biometria)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>



O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ele fornece uma segunda camada de segurança para logons de usuário e transações.

O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação — chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou verificação de código e tokens OATH de terceiros.

Para obter uma visão geral de como funciona o Azure Multi-Factor Authentication, consulte o vídeo a seguir.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##Por que usar o Azure Multi-Factor Authentication?

Hoje, mais do que nunca, as pessoas estão cada vez mais conectadas. Com os Smartphones, tablets, laptops e PCs, as pessoas têm várias opções diferentes para se conectar e se manterem conectadas. As pessoas podem acessar suas contas e aplicativos de qualquer lugar e isso significa que eles podem fazer mais trabalho e atender melhor a seus clientes.

O Azure Multi-Factor Authentication é uma solução fácil de usar, escalonável e confiável que fornece um segundo método de autenticação para que os usuários sempre estejam protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalonável](./media/multi-factor-authentication/scalable.png)| ![Sempre protegidos](./media/multi-factor-authentication/protected.png)|![Confiável](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalonável**|**Sempre protegidos**|**Confiável**

- **Fácil de usar** - a Autenticação Multifator do Azure é simples de configurar e usar. A proteção adicional que acompanha o Azure Multi-Factor Authentication permite que os usuários usem e gerenciem os seus próprios dispositivos e, em muitos casos, ele pode ser configurado com apenas alguns cliques.
- **Escalonável** - a Autenticação Multifator do Azure utiliza a força da nuvem e integra-se ao seu AD local e aos aplicativos personalizados. Essa proteção ainda é estendida aos seus cenários essenciais de missão de alto volume.
- **Sempre protegidos** - o Azure Multi-Factor Authentication fornece autenticação forte usando os mais altos padrões do setor.
- **Confiável** - Garantimos 99,9% de disponibilidade do Azure Multi-Factor Authentication. O serviço é considerado indisponível quando não é possível receber ou processar solicitações de autenticação parao Multi-Factor Authentication.  

Para obter informações adicionais sobre por que usar o Azure Multi-Factor Authentication, consulte o vídeo a seguir.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>


## Como funciona o Azure Multi-Factor Authentication

A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. Se o usuário perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que também saiba a senha do usuário.

![Prova](./media/multi-factor-authentication-how-it-works/howitworks.png)



O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil:

- chamada telefônica 
- mensagem de texto
- notificação de aplicativos móveis, permitindo que os usuários escolham o método que preferirem
- código de verificação de aplicativo móvel
- Tokens OATH de terceiros

Para obter informações adicionais sobre como ele funciona, veja o vídeo a seguir.

[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Métodos disponíveis para autenticação multifator
Quando um usuário entra, uma verificação adicional é enviada ao usuário. Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

Método de verificação | Descrição 
------------- | ------------- |
Chamada telefônica | É feita uma chamada para o telefone inteligente do usuário solicitando que verifique se está se conectando pressionando o sinal #. Isso concluirá o processo de verificação. Essa opção é configurável e pode ser alterada para um código que você especificar.
Mensagem de texto | Uma mensagem de texto será enviada para o smartphone do usuário com um código de 6 dígitos. Digite esse código para concluir o processo de verificação.
Notificação de aplicativo móvel | Uma solicitação de verificação será enviada ao smartphone do usuário solicitando que conclua a verificação, selecionando Verificar no aplicativo móvel. Isso ocorrerá se você selecionou a notificação do aplicativo como seu método de verificação principal. Se o usuário receber isso quando não estiver se conectando, é possível relatar como fraude.
Código de verificação co Aplicativos Móveis | Um código de verificação será enviado ao aplicativo móvel que está sendo executado no smartphone do usuário. Isso ocorrerá se você selecionou o código de verificação como seu método de verificação principal.


## Versões disponíveis do Azure Multi-Factor Authentication
O Azure Multi-Factor Authentication está disponível em três versões diferentes. A tabela a seguir descreve cada uma deças em mais detalhes.

Versão | Descrição 
------------- | ------------- |
Autenticação Multifator para Office 365 | Esta versão funciona exclusivamente com os aplicativos do Office 365 e é gerenciada a partir do portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a autenticação multifator. Esta versão é fornecida com uma assinatura do Office 365.
Autenticação Multifator para administradores do Azure | O mesmo subconjunto de recursos da Autenticação Multifator para Office 365 estará disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional, habilitando essa funcionalidade essencial de autenticação multifator. Portanto, um administrador que queira acessar o portal do Azure para criar uma VM, um site, gerenciar armazenamento, serviços móveis ou qualquer outro serviço do Azure poderá adicionar a autenticação multifator à sua conta de administrador.
Autenticação Multifator do Azure | O Azure Multi-Factor Authentication oferece o conjunto mais avançado de recursos. Ele fornece opções de configuração adicionais por meio do Portal de Gerenciamento do Azure, relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. O Azure Multi-Factor Authentication vem como parte do Azure Active Directory Premium.

## Comparação de recursos dasversões
A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Azure Multi-Factor Authentication.


Recurso | Multi-Factor Authentication para Office 365 (incluído nas SKUs do Office 365)|A Autenticação Multifator para administradores do Azure (incluída com a assinatura do Azure) | O Multi-Factor Authentication (incluído no Azure AD Premium e Enterprise Mobility Suite) 
------------- | :-------------: |:-------------: |:-------------: |
Os administradores podem proteger contas com a MFA| * | * (Disponível apenas para contas de Administrador do Azure)|*
Aplicativos móveis como um fator secundário|* | * | *
Chamada telefônica como um fator secundário|* | * | *
SMS como um fator secundário|* | * | *
Senhas de aplicativos para clientes que não oferecem suporte a MFA|* | * | *
Controle do administrador sobre métodos de autenticação| | | *
Modo PIN| | | *
Alerta de fraude| | | *
Relatórios de MFA| | | *
Desvio único| | | *
Saudações personalizadas para chamadas telefônicas| | | *
Personalização da ID do chamador para chamadas telefônicas| | | *
Confirmação de evento| | | *
IPs Confiáveis| | | *
Suspender MFA para dispositivos lembrados (visualização pública)| | | *
SDK de MFA| | | *
MFA para aplicativos locais usando o servidor MFA| | | *


## Como obter o Azure Multi-Factor Authentication

O Azure Multi-Factor Authentication vem como parte do Azure Active Directory Premium e o Enterprise Mobility Suite. Se você já os tiver, você também tem o Azure Multi-Factor Authentication.

Se você for usuário do Office 365 ou um assinante do Azure e quiser aproveitar os recursos adicionais fornecidos pelo Azure Multi-Factor Authentication, continue lendo.

Se você não tiver nenhum deles,para começar a usar a Azure Multi-Factor Authentication, você primeiro precisa de uma assinatura do Azure ou uma [Assinatura de avaliação do Azure](http://azure.microsoft.com/pricing/free-trial/).

Quando usar o Azure multi-Factor Authentication, há dua opções de cobrança disponíveis:

- **Por usuário**. Geralmente para empresas que desejam habilitar a autenticação multifator para um número fixo de funcionários que precisam regularmente de autenticação.
- **Por Autenticação**. Em geral, para empresas que querem habilitar a autenticação multifator para um grupo grande de usuários externos que raramente precisam de autenticação.

Para obter detalhes sobre preços, consulte [Preços do Azure MFA.](http://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Escolha o modelo que funciona melhor para sua organização. Depois, para iniciar, consulte a [Introdução](multi-factor-authentication-get-started.md)

## Escolha a solução de segurança multifator para você

Como há vários tipos de Azure Multi-Factor Authentication, precisamos determinar algumas coisas para descobrir qual versão é a correta para usar. Esses elementos são:

-	[O que estou tentando proteger](#what-am-i-trying-to-secure)
-	[Onde os usuários estão localizados](#where-are-the-users-located)

As seções a seguir fornecem orientações sobre como determinar a cada um deles.

### O que estou tentando proteger?

Para determinar a solução de autenticação multifator correta, precisamos responder primeiro à pergunta de o que você está tentando proteger com um segundo método de autenticação. É um aplicativo no Azure? Ou é um sistema de acesso remoto, por exemplo. Ao determinar o que estamos tentando proteger, passaremos a responder à pergunta de onde a autenticação multifator precisa ser habilitada.



O que você está tentando proteger| Autenticação multifator na nuvem|Servidor Multi-Factor Authentication 
------------- | :-------------: | :-------------: |
Aplicativos primários da Microsoft|* |* |
Aplicativos SaaS da Galeria de Aplicativos|* |* |
Aplicativos IIS publicados por meio da Proxy de aplicativo do Azure AD|* |* |
Aplicativos IIS não publicados por meio da Proxy de aplicativo do Azure AD | |* |
Acesso remoto, como VPN, RDG| |* |



### Onde os usuários estão localizados

Em seguida, dependendo de onde os usuários estão localizados, podemos determinar a solução correta para usar, seja a autenticação multifator na nuvem ou no local usando o Servidor MFA.



Local do usuário| Solução
------------- | :------------- | 
Azure Active Directory| Autenticação multifator na nuvem|
Azure AD e AD local usando federação com AD FS| O MFA na nuvem e o Servidor MFA são opções disponíveis 
Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - sem sincronização de senha|O MFA na nuvem e o Servidor MFA são opções disponíveis 
Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - com sincronização de senha|Autenticação multifator na nuvem
Active Directory local|Servidor Multi-Factor Authentication

A tabela a seguir é uma comparação dos recursos que estão com uma autenticação multifator na nuvem e com o Servidor Multi-Factor Authentication.

 | Autenticação multifator na nuvem | Servidor Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notificação de aplicativo móvel como um segundo fator | ● | ● |
Código de verificação de aplicativo móvel como um segundo fator | ● | ●
Chamada telefônica como um segundo fator | ● | ● 
SMS unidirecional como segundo fator | ● | ●
SMS bidirecional como segundo fator | | ● 
Tokens de hardware como segundo fator | | ● 
Senhas de aplicativos para clientes que não oferecem suporte a MFA | ● |  
Controle do administrador sobre métodos de autenticação | | ● 
Modo PIN | | ●
Alerta de fraude | ● | ●
Relatórios de MFA | ● | ● 
Desvio único | ● | ● 
Saudações personalizadas para chamadas telefônicas | ● | ● 
ID do chamador personalizável para chamadas telefônicas | ● | ● 
IPs confiáveis | ● | ● 
Suspender MFA para dispositivos lembrados (visualização pública) | ● |  
Acesso condicional | ● | ● 
Cache | ● | ● 

Agora que determinamos se usar a autenticação multifator na nuvem ou o Servidor MFA local, podemos pode começar a configurar e usar o Azure Multi-Factor Authentication. **Selecione o ícone que representa seu cenário.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_1125_2015-->
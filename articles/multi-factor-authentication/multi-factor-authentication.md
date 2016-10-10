<properties
	pageTitle="Azure Multi-Factor Authentication - o que é isso? | Microsoft Azure"
	description="Este tópico explica o que é o MFA (Multi-Factor Authentication), por que alguém o usaria, mais informações sobre o cliente do Multi-Factor Authentication e os diferentes métodos e as versões disponíveis. O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ele fornece uma camada extra de segurança às entradas e transações dos usuários."
	keywords="introdução ao MFA, visão do geral do mfa, o que é mfa"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2016"
	ms.author="kgremban"/>

# O que é o Azure Multi-Factor Authentication?
A autenticação multifator (MFA) é um método de autenticação que exige mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir:

- Algo que você sabe (normalmente, uma senha)
- Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
- Algo seu (biometria)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>


O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação — chamada telefônica, mensagem de texto ou verificação de aplicativo móvel.

>[AZURE.VIDEO multi-factor-authentication-overview]

## Por que usar o Azure Multi-Factor Authentication?

Hoje, mais do que nunca, as pessoas estão cada vez mais conectadas. Com os Smartphones, tablets, laptops e PCs, as pessoas têm várias opções diferentes para se conectar e se manterem conectadas. As pessoas podem acessar suas contas e aplicativos de qualquer lugar, o que significa que elas podem concluir mais tarefas de trabalho e atender melhor os seus clientes.

O Azure Multi-Factor Authentication é uma solução fácil de usar, escalonável e confiável que fornece um segundo método de autenticação para que os usuários sempre estejam protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalonável](./media/multi-factor-authentication/scalable.png)| ![Sempre protegidos](./media/multi-factor-authentication/protected.png)|![Confiável](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalonável**|**Sempre protegidos**|**Confiável**

- **Fácil de usar** – a Autenticação Multifator do Azure é simples de configurar e usar. A proteção adicional que acompanha a Autenticação Multifator do Azure permite que os usuários gerenciem seus próprios dispositivos. O melhor de tudo, em muitos casos ela pode ser configurada com apenas alguns cliques.
- **Escalonável** – a Autenticação Multifator do Azure utiliza os recursos da nuvem e integra-se ao seu AD e a aplicativos personalizados locais. Essa proteção ainda é estendida aos seus cenários essenciais de missão de alto volume.
- **Sempre protegidos** - o Azure Multi-Factor Authentication fornece autenticação forte usando os mais altos padrões do setor.
- **Confiável** - Garantimos 99,9% de disponibilidade do Azure Multi-Factor Authentication. O serviço é considerado indisponível quando não é possível receber ou processar solicitações de autenticação parao Multi-Factor Authentication.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## Como funciona o Azure Multi-Factor Authentication

A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a sua senha, isso será inútil se ele também não tiver posse do dispositivo confiável. Se você perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que também saiba a sua senha.

![Prova](./media/multi-factor-authentication-how-it-works/howitworks.png)



O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil:

- chamada telefônica
- mensagem de texto
- notificação de aplicativos móveis, permitindo que os usuários escolham o método que preferirem
- código de verificação de aplicativo móvel
- tokens OATH de terceiros

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Métodos disponíveis para autenticação multifator
Quando um usuário entra, uma verificação adicional é enviada ao usuário. Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

Método de verificação | Descrição
------------- | ------------- |
Chamada telefônica | É feita uma chamada para o telefone do usuário solicitando que o mesmo verifique se está se conectando. Pressione a tecla # para concluir o processo de verificação. Essa opção é configurável e pode ser alterada para um código que você especificar.
Mensagem de texto | Uma mensagem de texto é enviada para o smartphone do usuário com um código de 6 dígitos. Digite esse código para concluir o processo de verificação.
Notificação de aplicativo móvel | Uma solicitação de verificação é enviada ao smartphone do usuário solicitando que conclua a verificação, selecionando **Verificar** no aplicativo móvel. Isso ocorre se a notificação do aplicativo é o método de verificação principal. Se o usuário receber essa notificação quando não estiver se conectando, o mesmo pode relatar esse fato como fraude.</li><br><p> O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).
Código de verificação co Aplicativos Móveis | Um código de verificação é enviado ao aplicativo móvel que está sendo executado no smartphone do usuário. Isso ocorre se você selecionou o código de verificação como seu método de verificação principal.</li><br><p> O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).


## Versões disponíveis do Azure Multi-Factor Authentication
O Azure Multi-Factor Authentication está disponível em três versões diferentes. A tabela a seguir descreve cada uma deças em mais detalhes.

Versão | Descrição
------------- | ------------- |
Autenticação Multifator para Office 365 | Esta versão funciona exclusivamente com os aplicativos do Office 365 e é gerenciada a partir do portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a autenticação multifator. Esta versão é fornecida com uma assinatura do Office 365.
Autenticação Multifator para administradores do Azure | O mesmo subconjunto de recursos da Autenticação Multifator para Office 365 está disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional, habilitando essa funcionalidade essencial de autenticação multifator. Um administrador que queira acessar o portal do Azure para criar uma VM ou um site, gerenciar armazenamento ou usar qualquer outro serviço do Azure poderá adicionar a MFA à sua conta de administrador.
Autenticação Multifator do Azure | O Azure Multi-Factor Authentication oferece o conjunto mais avançado de recursos. Ela fornece opções de configuração adicionais por meio do [portal clássico do Azure](http://manage.windowsazure.com), relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. O Azure Multi-Factor Authentication é fornecido como parte do Azure Active Directory Premium e do Enterprise Mobility Suite.

## Comparação de recursos dasversões
A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure.


Recurso | Multi-Factor Authentication para Office 365 (incluído nas SKUs do Office 365)|A Autenticação Multifator para administradores do Azure (incluída com a assinatura do Azure) | O Multi-Factor Authentication (incluído no Azure AD Premium e Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Os administradores podem proteger contas com a MFA| ● | ● (Disponível apenas para contas de Administrador do Azure)|●
Aplicativos móveis como um fator secundário|● | ● | ●
Chamada telefônica como um fator secundário|● | ● | ●
SMS como um fator secundário|● | ● | ●
Senhas de aplicativos para clientes que não oferecem suporte a MFA|● | ● | ●
Controle do administrador sobre métodos de autenticação| ● | ● | ●
Modo PIN| | | ●
Alerta de fraude| | | ●
Relatórios de MFA| | | ●
Desvio único| | | ●
Saudações personalizadas para chamadas telefônicas| | | ●
Personalização da ID do chamador para chamadas telefônicas| | | ●
Confirmação de evento| | | ●
IPs confiáveis| | | ●
Lembrar MFA para dispositivos confiáveis |● | ● | ●
SDK de MFA | | | ● exige o provedor de Autenticação Multifator e assinatura completa do Azure
MFA para aplicativos locais usando o servidor MFA| | | ●

## Como obter o Azure Multi-Factor Authentication

Se desejar todas as funcionalidades oferecidas pela Autenticação Multifator do Azure além dos recursos para usuários do Office 365 e administradores do Azure, há várias opções:

1.	Comprar licenças do Azure Multi-Factor Authentication e atribuí-las a seus usuários.
2.	Comprar licenças que incluem a Autenticação Multifator do Azure agrupado dentro delas, como o Azure Active Directory Premium, Enterprise Mobility Suite ou Enterprise Cloud Suite e atribuí-las a seus usuários.
3.	Crie um Provedor do Azure Multi-Factor Authentication em uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma assinatura de avaliação do Azure. Assinaturas de avaliação precisam ser convertidas em assinaturas regulares antes da expiração da avaliação.

Ao usar um Provedor de Autenticação Multifator do Azure, há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:


- **Por usuário**. Para empresas que desejam habilitar a autenticação multifator para um número fixo de funcionários que precisam regularmente de autenticação.
- **Por autenticação**. Para empresas que querem habilitar a autenticação multifator para um grupo grande de usuários externos que raramente precisam de autenticação.

O Azure Multi-Factor Authentication fornece métodos de verificação selecionável para a nuvem e o servidor. Isso significa que é possível escolher quais métodos estarão disponíveis para os usuários usar com a autenticação multifator. Atualmente, esse recurso está em preview pública para a versão de nuvem da autenticação multifator. Para saber mais, consulte os [métodos de verificação selecionáveis](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Para obter detalhes sobre preços, consulte [Preços do Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Escolha o modelo baseado em consumo que funciona melhor para sua organização. Depois, para iniciar, veja [Introdução.](multi-factor-authentication-get-started.md)

## Próximas etapas

Para começar a usar a Autenticação Multifator do Azure, a primeira etapa é [escolher entre MFA na nuvem ou local](multi-factor-authentication-get-started.md)

<!---HONumber=AcomDC_0928_2016-->
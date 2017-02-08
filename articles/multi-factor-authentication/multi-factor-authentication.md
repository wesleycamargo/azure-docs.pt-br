---
title: "Visão geral do Azure MFA | Microsoft Docs"
description: "O que é a Autenticação Multifator do Azure (MFA, Multi-factor Authentication), por que usar MFA, mais informações sobre o cliente MFA e os diferentes métodos e versões disponíveis. "
keywords: "introdução ao MFA, visão do geral do mfa, o que é mfa"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9ad8df803853a7f57fba5737506110d4b0cc68e7


---
# <a name="what-is-azure-multi-factor-authentication"></a>O que é o Azure Multi-Factor Authentication?
A verificação em duas etapas é um método de autenticação que exige mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir:

* Algo que você sabe (normalmente, uma senha)
* Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
* Algo seu (biometria)

<center>![Nome de usuário e senha](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Certificados](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smartphone](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Cartão inteligente](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Cartão inteligente virtual](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Nome de usuário e senha](./media/multi-factor-authentication/cert.png)</center>

O Azure MFA é uma solução de verificação em duas etapas da Microsoft. O Azure MFA ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de métodos de verificação, incluindo chamada telefônica, mensagem de texto ou verificação de aplicativo móvel.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/WA-MFA-Overview/player]
> 
> 

## <a name="why-use-azure-multi-factor-authentication"></a>Por que usar o Azure Multi-Factor Authentication?
Hoje, mais do que nunca, as pessoas estão cada vez mais conectadas. Com os Smartphones, tablets, laptops e PCs, as pessoas têm várias opções diferentes para se conectar e se manterem conectadas. As pessoas podem acessar suas contas e aplicativos de qualquer lugar, o que significa que elas podem concluir mais tarefas de trabalho e atender melhor os seus clientes.

O Azure Multi-Factor Authentication é uma solução fácil de usar, escalonável e confiável que fornece um segundo método de autenticação para que os usuários sempre estejam protegidos.

| ![Fácil de usar](./media/multi-factor-authentication/simple.png) | ![Escalonável](./media/multi-factor-authentication/scalable.png) | ![Sempre protegidos](./media/multi-factor-authentication/protected.png) | ![Confiável](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Fácil de usar** |**Escalonável** |**Sempre protegidos** |**Confiável** |

* **Fácil de usar** – a Autenticação Multifator do Azure é simples de configurar e usar. A proteção extra que acompanha a Autenticação Multifator do Azure permite que os usuários gerenciem seus próprios dispositivos. O melhor de tudo, em muitos casos ela pode ser configurada com apenas alguns cliques.
* **Escalonável** – a Autenticação Multifator do Azure utiliza os recursos da nuvem e integra-se ao seu AD e a aplicativos personalizados locais. Essa proteção ainda é estendida aos seus cenários essenciais de missão de alto volume.
* **Sempre protegidos** - o Azure Multi-Factor Authentication fornece autenticação forte usando os mais altos padrões do setor.
* **Confiável** - Garantimos 99,9% de disponibilidade do Azure Multi-Factor Authentication. O serviço é considerado indisponível quando não é possível receber ou processar solicitações de verificação para verificação em duas etapas.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Windows-Azure-Multi-Factor-Authentication/player]
> 
> 

## <a name="how-azure-multi-factor-authentication-works"></a>Como funciona o Azure Multi-Factor Authentication
A segurança da verificação em duas etapas baseia-se na sua abordagem em camadas. O comprometimento de vários métodos de verificação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a sua senha, isso será inútil se ele também não tiver posse do dispositivo confiável. Se você perder o dispositivo, quem encontrar o dispositivo não vai conseguir usá-lo, a menos que também saiba a sua senha.

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]
> 
> 

## <a name="methods-available-for-multi-factor-authentication"></a>Métodos disponíveis para a Autenticação Multifator
Quando um usuário entra, uma solicitação de verificação adicional é enviada ao usuário. Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

| Método de verificação | Descrição |
| --- | --- |
| chamada telefônica |É feita uma chamada para o telefone do usuário solicitando que o mesmo verifique se está se conectando. Pressione a tecla # para concluir o processo de verificação. Essa opção é configurável e pode ser alterada para um código que você especificar. |
| mensagem de texto |Uma mensagem de texto é enviada para o smartphone do usuário com um código de 6 dígitos. Digite esse código para concluir o processo de verificação. |
| Notificação de aplicativo móvel |Uma solicitação de verificação é enviada ao smartphone do usuário solicitando que conclua a verificação, selecionando **Verificar** no aplicativo móvel. Isso ocorre se a notificação do aplicativo é o método de verificação principal. Se o usuário receber essa notificação quando não estiver se conectando, o mesmo pode relatar esse fato como fraude. |
| Código de verificação com aplicativos móveis |O aplicativo móvel em um dispositivo do usuário gera um código de verificação. Isso ocorre se você selecionou o código de verificação como seu método de verificação principal. |

Para os métodos de verificação de aplicativo móvel, a autenticação multifator do Azure funciona com aplicativos de autenticação de terceiros para Smartphones. Entretanto, recomendamos o aplicativo Microsoft Authenticator, que está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis do Azure Multi-Factor Authentication
O Azure Multi-Factor Authentication está disponível em três versões diferentes. 

| Versão | Descrição |
| --- | --- |
| Autenticação Multifator para Office 365 |Esta versão funciona exclusivamente com os aplicativos do Office 365 e é gerenciada a partir do portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a verificação em duas etapas. Esta versão é fornecida com uma assinatura do Office 365. |
| Autenticação Multifator para administradores do Azure |O mesmo subconjunto de recursos da verificação em duas etapas para Office 365 está disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure pode habilitar esta funcionalidade para proteção adicional. Um administrador que queira acessar o portal do Azure para criar uma VM ou um site, gerenciar armazenamento ou usar qualquer outro serviço do Azure poderá adicionar a MFA à sua conta de administrador. |
| Autenticação Multifator do Azure |O Azure Multi-Factor Authentication oferece o conjunto mais avançado de recursos. Ela fornece opções de configuração adicionais por meio do [portal clássico do Azure](https://manage.windowsazure.com), relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. A autenticação multifator do Azure é fornecida como parte do Azure Active Directory Premium e do Enterprise Mobility Suite e pode ser implantada na nuvem ou no local. Veja abaixo [outras maneiras de obter a Autenticação Multifator do Azure](multi-factor-authentication.md#how-to-get-azure-multi-factor-authentication). |

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões
A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Autenticação Multifator do Azure.

> [!NOTE]
> Esta tabela de comparação aborda os recursos que fazem parte de cada assinatura. Se você tiver o AD do Azure Premium ou o Enterprise Mobility Suite, alguns recursos podem não estar disponíveis caso você use [MFA na nuvem ou MFA no local](multi-factor-authentication-get-started.md).
> 
> 

| Recurso | Autenticação Multifator para Office 365 | Autenticação Multifator para administradores do Azure | Autenticação Multifator do Azure |
| --- |:---:|:---:|:---:|
| Os administradores podem proteger contas com a MFA |● |● (Disponível apenas para contas de Administrador do Azure) |● |
| Aplicativos móveis como um fator secundário |● |● |● |
| Chamada telefônica como um fator secundário |● |● |● |
| SMS como um fator secundário |● |● |● |
| Senhas de aplicativos para clientes que não oferecem suporte a MFA |● |● |● |
| Controle do administrador sobre métodos de autenticação |● |● |● |
| Modo PIN | | |● |
| Alerta de fraude | | |● |
| Relatórios de MFA | | |● |
| Desvio único | | |● |
| Saudações personalizadas para chamadas telefônicas | | |● |
| Personalização da ID do chamador para chamadas telefônicas | | |● |
| Confirmação de evento | | |● |
| IPs confiáveis | | |● |
| Lembrar MFA para dispositivos confiáveis |● |● |● |
| SDK de MFA | | |● exige o provedor de Autenticação Multifator e assinatura completa do Azure |
| MFA para aplicativos locais usando o servidor MFA | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Como obter o Azure Multi-Factor Authentication
Se você quiser a funcionalidade completa oferecida pela autenticação multifator do Azure, há várias opções:

1. Comprar licenças do Azure Multi-Factor Authentication e atribuí-las a seus usuários.
2. Comprar licenças que incluem a Autenticação Multifator do Azure agrupado dentro delas, como o Azure Active Directory Premium, Enterprise Mobility Suite ou Enterprise Cloud Suite e atribuí-las a seus usuários.
3. Crie um Provedor do Azure Multi-Factor Authentication em uma assinatura do Azure. Ao usar um Provedor de Autenticação Multifator do Azure, há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:  
   * **Por usuário**. Para empresas que desejam habilitar a verificação em duas etapas para um número fixo de funcionários que precisam regularmente de autenticação.  
   * **Por autenticação**. Para empresas que querem habilitar a verificação em duas etapas para um grupo grande de usuários externos que raramente precisam de autenticação.  

O Azure Multi-Factor Authentication fornece métodos de verificação selecionável para a nuvem e o servidor. Isso significa que é possível escolher quais métodos estarão disponíveis para os usuários. Atualmente, esse recurso está em preview pública para a versão de nuvem da autenticação multifator. Para obter mais informações, consulte os [métodos de verificação selecionáveis](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Para obter detalhes sobre preços, consulte [Preços do Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="next-steps"></a>Próximas etapas
Para começar a usar a Autenticação Multifator do Azure, a primeira etapa é [escolher entre MFA na nuvem ou local](multi-factor-authentication-get-started.md)




<!--HONumber=Dec16_HO2-->



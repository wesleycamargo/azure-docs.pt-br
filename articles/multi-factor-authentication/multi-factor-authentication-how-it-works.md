---
title: Azure Multi-Factor Authentication - Como funciona
description: "O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80bd6265c991eb7bf91f92fbe69c902c1e1d0a0b


---
# <a name="how-azure-multi-factor-authentication-works"></a>Como funciona o Azure Multi-Factor Authentication
A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. Se o usuário perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que também saiba a senha do usuário.

![Prova](./media/multi-factor-authentication-how-it-works/howitworks.png)

O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples.  Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil:

* chamada telefônica
* mensagem de texto
* notificação de aplicativos móveis, permitindo que os usuários escolham o método que preferirem
* código de verificação de aplicativo móvel
* Tokens OATH de terceiros

Para obter informações adicionais sobre como ele funciona, veja o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]
> 
> 

## <a name="methods-available-for-multi-factor-authentication"></a>Métodos disponíveis para autenticação multifator
Quando um usuário entra, uma verificação adicional é enviada ao usuário.  Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

| Método de verificação | Descrição |
| --- | --- |
| chamada telefônica |É feita uma chamada para o telefone inteligente do usuário solicitando que verifique se está se conectando pressionando o sinal #.  Isso concluirá o processo de verificação.  Essa opção é configurável e pode ser alterada para um código que você especificar. |
| mensagem de texto |Uma mensagem de texto será enviada para o smartphone do usuário com um código de 6 dígitos.  Digite esse código para concluir o processo de verificação. |
| Notificação de aplicativo móvel |Uma solicitação de verificação será enviada ao smartphone do usuário solicitando que conclua a verificação, selecionando Verificar no aplicativo móvel. Isso ocorrerá se você selecionou a notificação do aplicativo como seu método de verificação principal.  Se o usuário receber isso quando não estiver se conectando, é possível relatar como fraude. |
| Código de verificação co Aplicativos Móveis |Um código de verificação será enviado ao aplicativo móvel que está sendo executado no smartphone do usuário.  Isso ocorrerá se você selecionou o código de verificação como seu método de verificação principal. |

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis do Azure Multi-Factor Authentication
O Azure Multi-Factor Authentication está disponível em três versões diferentes.  A tabela a seguir descreve cada uma deças em mais detalhes.

| Versão | Descrição |
| --- | --- |
| Autenticação Multifator para Office 365 |Esta versão funciona exclusivamente com os aplicativos do Office 365 e é gerenciada a partir do portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a autenticação multifator.  Esta versão é fornecida com uma assinatura do Office 365. |
| Autenticação Multifator para administradores do Azure |O mesmo subconjunto de recursos da Autenticação Multifator para Office 365 estará disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional, habilitando essa funcionalidade essencial de autenticação multifator. Portanto, um administrador que queira acessar o portal do Azure para criar uma VM, um site, gerenciar armazenamento, serviços móveis ou qualquer outro serviço do Azure poderá adicionar a autenticação multifator à sua conta de administrador. |
| Autenticação Multifator do Azure |O Azure Multi-Factor Authentication oferece o conjunto mais avançado de recursos. <br><br>Ele fornece opções de configuração adicionais por meio do portal de Gerenciamento do Azure, relatórios avançados e suporte para uma variedade de aplicativos locais e na nuvem. O Azure Multi-Factor Authentication pode ser adquirido como uma licença independente e está vinculado ao Azure Active Directory Premium e ao Enterprise Mobility Suite. <br><br>Ele também pode ser comprado com base no consumo, com a criação de um Provedor do Azure Multi-Factor Authentication em uma assinatura do Azure. |

## <a name="feature-comparison-of-versions"></a>Comparação de recursos dasversões
A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Azure Multi-Factor Authentication.

| Recurso | Multi-Factor Authentication para Office 365 (incluído nas SKUs do Office 365) | A Autenticação Multifator para administradores do Azure (incluída com a assinatura do Azure) | O Multi-Factor Authentication (incluído no Azure AD Premium e Enterprise Mobility Suite) |
| --- |:---:|:---:|:---:|
| Os administradores podem proteger contas com a MFA |* |* (Disponível apenas para contas de Administrador do Azure) |* |
| Aplicativos móveis como um fator secundário |* |* |* |
| Chamada telefônica como um fator secundário |* |* |* |
| SMS como um fator secundário |* |* |* |
| Senhas de aplicativos para clientes que não oferecem suporte a MFA |* |* |* |
| Controle do administrador sobre métodos de autenticação |* |* |* |
| Modo PIN | | |* |
| Alerta de fraude | | |* |
| Relatórios de MFA | | |* |
| Desvio único | | |* |
| Saudações personalizadas para chamadas telefônicas | | |* |
| Personalização da ID do chamador para chamadas telefônicas | | |* |
| Confirmação de evento | | |* |
| IPs confiáveis | | |* |
| Suspender MFA para dispositivos lembrados (visualização pública) | | |* |
| SDK de MFA | | |* |
| MFA para aplicativos locais usando o servidor MFA | | |* |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Como obter o Azure Multi-Factor Authentication
Se desejar todas as funcionalidades oferecidas pelo Azure Multi-Factor Authentication em vez de apenas aquelas fornecidas para usuários do Office 365 e administradores do Azure, há várias opções para obtê-las:

1. Comprar licenças do Azure Multi-Factor Authentication e atribuí-las a seus usuários.
2. Comprar licenças que incluem o Azure Multi-Factor Authentication agrupado dentro delas, como o Azure Active Directory Premium ou o Enterprise Mobility Suite e atribuí-las a seus usuários.
3. Crie um Provedor do Azure Multi-Factor Authentication em uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma assinatura de avaliação do Azure. Assinaturas de avaliação precisarão ser convertidas em assinaturas regulares antes da expiração da avaliação.

Ao usar um Provedor do Azure Multi-Factor Authentication, há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:

* **Por usuário**. Geralmente para empresas que desejam habilitar a autenticação multifator para um número fixo de funcionários que precisam regularmente de autenticação.
* **Por autenticação**. Em geral, para empresas que querem habilitar a autenticação multifator para um grupo grande de usuários externos que raramente precisam de autenticação.

Para obter detalhes sobre preços, consulte [Preços do Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Escolha o modelo baseado em consumo que funciona melhor para sua organização.   Depois, para iniciar, consulte a [Introdução](multi-factor-authentication-get-started.md)




<!--HONumber=Nov16_HO3-->



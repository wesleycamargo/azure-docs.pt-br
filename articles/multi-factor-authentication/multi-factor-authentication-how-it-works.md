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
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Como funciona o Azure Multi-Factor Authentication
A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. Se o usuário perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que também saiba a senha do usuário.

![Prova](./media/multi-factor-authentication-how-it-works/howitworks.png)

O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples.  Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil.

Para obter informações detalhadas sobre como isso funciona, veja o vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>Métodos disponíveis para verificação em duas etapas
Quando um usuário entra, uma verificação adicional é enviada ao usuário.  Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

| Método de verificação | Descrição |
| --- | --- |
| chamada telefônica |É feita uma chamada para o telefone registrado do usuário solicitando que confirme se está se conectando pressionando o sinal # ou inserindo um PIN. |
| mensagem de texto |Uma mensagem de texto será enviada para o celular de um usuário com um código de seis dígitos.  Digite esse código para concluir o processo de verificação. |
| Notificação de aplicativo móvel |Uma solicitação de verificação é enviada ao smartphone do usuário solicitando que conclua a verificação, selecionando Verificar no aplicativo móvel. Isso ocorrerá se você selecionou a notificação do aplicativo como seu método de verificação principal.  Se o usuário receber isso quando não estiver se conectando, é possível relatar como fraude. |
| Código de verificação do aplicativo móvel |O aplicativo móvel, executado no smartphone do usuário, exibe um código de verificação de seis dígitos que muda a cada 30 segundos. O usuário encontra o código mais recente e o insere na página de entrada para concluir o processo de verificação. Isso ocorrerá se você selecionou o código de verificação como seu método de verificação principal. |
| Tokens OATH de terceiros | A Autenticação Multifator do Azure pode ser configurada a fim de aceitar métodos de verificação de terceiros. |

O Azure Multi-Factor Authentication fornece métodos de verificação selecionável para a nuvem e o servidor. Isso significa que é possível escolher quais métodos estarão disponíveis para os usuários: chamada telefônica, texto, notificação no aplicativo ou códigos de aplicativo. Para obter mais informações, consulte os [métodos de verificação selecionáveis](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os diferentes [métodos de consumo e versões para a Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md)

- Escolha se deseja implantar o Azure MFA [na nuvem ou no local](multi-factor-authentication-get-started.md)

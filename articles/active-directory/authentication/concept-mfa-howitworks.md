---
title: Autenticação Multifator do Azure - Como funciona
description: A Autenticação Multifator do Azure ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 709fab070533984f94a72ff2136a8bc32fbe6ec6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="how-azure-multi-factor-authentication-works"></a>Como funciona a Autenticação Multifator do Azure
A segurança da verificação em duas etapas baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. 

![Prova](./media/concept-mfa-howitworks/howitworks.png)

A Autenticação Multifator do Azure ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples.  Ele fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de opções de verificação fácil.


## <a name="methods-available-for-two-step-verification"></a>Métodos disponíveis para verificação em duas etapas
Quando um usuário entra, uma verificação adicional é enviada ao usuário.  Veja a seguir uma lista de métodos que podem ser usados para essa segunda verificação.

| Método de verificação | DESCRIÇÃO |
| --- | --- |
| chamada telefônica |É feita uma chamada para o telefone registrado de um usuário. O usuário insere um PIN, se necessário e, em seguida, pressiona a tecla #. |
| mensagem de texto |Uma mensagem de texto é enviada para o celular de um usuário com um código de seis dígitos. O usuário insere esse código na página de entrada. |
| Notificação de aplicativo móvel |Uma solicitação de verificação é enviada para o smartphone de um usuário. O usuário insere um PIN, se necessário e, em seguida, seleciona **Confirmar** no aplicativo móvel. |
| Código de verificação do aplicativo móvel |O aplicativo móvel, executado no smartphone de um usuário, exibe um código de verificação que muda a cada 30 segundos. O usuário localiza o código mais recente e o insere na página de entrada. |
| Tokens OATH de terceiros | O Servidor de Autenticação Multifator do Azure pode ser configurado para aceitar métodos de verificação de terceiros. |

A Autenticação Multifator do Azure fornece métodos de verificação selecionável para a nuvem e o servidor. É possível escolher quais métodos estarão disponíveis para os usuários: chamada telefônica, texto, notificação no aplicativo ou códigos do aplicativo. Para obter mais informações, consulte os [métodos de verificação selecionáveis](howto-mfa-mfasettings.md#selectable-verification-methods).

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os diferentes [métodos de consumo e versões para a Autenticação Multifator do Azure](concept-mfa-licensing.md)

- Escolha se deseja implantar o Azure MFA [na nuvem ou no local](concept-mfa-whichversion.md)

- Leia as respostas para as [Perguntas frequentes](multi-factor-authentication-faq.md)
---
title: "Referência para relatórios de autenticação multifator no portal do Azure | Microsoft Docs"
description: "Informações de referência para relatórios de autenticação multifator no portal do Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3c676b0c9c8090fb9680bc71051a9247ed0a4574
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Referência para relatórios de autenticação multifator no portal do Azure

Com os [relatórios do Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) no [portal do Azure](https://portal.azure.com), você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

Os [relatórios de atividade de entrada](active-directory-reporting-activity-sign-ins.md) fornecem informações sobre o uso de aplicativos gerenciados e de atividades de entrada de usuário, que incluem informações sobre o uso da autenticação multifator (MFA). 

Os dados de MFA oferecem ideias sobre como a MFA está funcionando em sua organização. Isso permite que você responda perguntas como: 

- A entrada foi desafiada com a MFA? 

- Como o usuário concluiu o MFA? 

- Por que o usuário não conseguiu concluir a MFA?  

Ao agregar todos os dados de entrada, você poderá ter uma melhor compreensão da experiência da MFA dentro de sua organização. Os dados ajudam a responder perguntas como: 

- Quantos usuários são desafiados para MFA?  

- Quantos usuários não conseguiram concluir o desafio da MFA? 

- Quais são os problemas comuns de MFA que os usuários finais estão encontrando? 


Esses dados estão disponíveis por meio do portal do Azure e da [API de relatórios](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Estrutura de dados


Os relatórios de atividade de entrada para MFA fornecem acesso às seguintes informações:

**MFA necessária:** se a MFA será necessário para a entrada ou não. A MFA pode ser exigida devido ao acesso MFA condicional por usuário, ou por outros motivos. Os valores possíveis são `Yes` ou `No`.

**Método de autenticação de MFA:** o método de autenticação de usuário usado para concluir a MFA. Os valores possíveis são: 

- mensagem de texto 

- Notificação de aplicativo móvel 

- Chamada telefônica (telefone de autenticação) 

- Código de verificação do aplicativo móvel 

- Chamada telefônica (telefone comercial) 

- Chamada telefônica (telefone de autenticação alternativo) 

**Detalhe de autenticação MFA:** versão limpa do número de telefone, por exemplo: + X XXXXXXXX64. 

**Resultado da MFA:** para saber mais se a MFA foi atendida ou negada:

- Se a MFA tiver sido atendida, esta coluna fornecerá mais informações sobre como a MFA foi atendida. 

- Se a MFA tiver sido negada, esta coluna fornecerá o motivo da negação. Os valores possíveis são `Satisfied` ou `Denied`. 

A seção a seguir lista os valores de cadeia de caracteres possíveis para o campo de resultado da MFA.

## <a name="status-strings"></a>Cadeias de caracteres de status

Esta seção lista os possíveis valores de cadeia de caracteres de status de resultado da MFA.

### <a name="satisfied-status-strings"></a>Cadeias de caracteres de status satisfeito


- Autenticação Multifator do Azure

    - concluído na nuvem 

    - expirou devido às políticas configuradas no locatário 

    - registro solicitado 

    - satisfeito por declaração no token 

    - satisfeito por declaração no token 

    - satisfeito por declaração no token 

    - satisfeito por declaração no token 

    - satisfeita por declaração fornecida pelo provedor externo 

    - satisfeita por autenticação forte 

    - ignorado porque o fluxo exercitado era o fluxo de logon de agente do Windows 

    - ignorado porque o fluxo exercitado era o fluxo de logon de agente do Windows 

    - ignorado devido a senha de aplicativo 

    - ignorado devido ao local 

    - ignorado devido a dispositivo registrado 
    
    - ignorado devido a dispositivo lembrado 

    - concluído com êxito 

- Redirecionado para um provedor externo para autenticação multifator 

 
### <a name="denied-status-strings"></a>Cadeias de caracteres de status negado

Autenticação Multifator do Azure negada; 

- autenticação em andamento 

- tentativa de autenticação duplicada 

- foi inserido código incorreto muitas vezes 

- autenticação inválida 

- Código de verificação do aplicativo móvel inválido 

- configuração incorreta 

- chamada telefônica caiu na caixa postal 

- o número de telefone tem um formato inválido 

- erro de serviço 

- não é possível acessar o telefone do usuário 

- não é possível enviar a notificação de aplicativo móvel no dispositivo 

- não é possível enviar a notificação de aplicativo móvel 

- usuário recusou a autenticação 

- o usuário não respondeu à notificação do aplicativo móvel 

- o usuário não tem nenhum método de verificação registrado 

- código incorreto inserido por usuário 

- PIN incorreto inserido pelo usuário 

- o usuário desligou a chamada telefônica sem obter êxito na autenticação 

- usuário está bloqueado 

- o usuário nunca inseriu o código de verificação 

- usuário não encontrado 
 
- código de verificação já usado uma vez 



## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).





























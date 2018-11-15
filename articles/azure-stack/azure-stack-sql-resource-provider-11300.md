---
title: Azure notas de versão do provedor 1.1.30.0 recursos de SQL de pilha | Microsoft Docs
description: Saiba mais sobre o que a atualização mais recente do Azure Stack SQL recurso provedor, incluindo problemas conhecidos e onde baixá-la.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: d2dda25c63a6e4a73205b9e4a830a211d025b3ed
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688199"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Notas de versão de 1.1.30.0 do provedor de recursos do SQL

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e problemas conhecidos na versão 1.1.30.0 do provedor de recursos do SQL.

## <a name="build-reference"></a>Referência de build
Baixe o provedor de recursos do SQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O provedor de recursos tem um mínimo correspondente do Azure Stack compilar. A versão de lançamento do Azure Stack mínima necessária para instalar esta versão do provedor de recursos do SQL é listada abaixo:

> |Versão mínima do Azure Stack|Versão do provedor de recursos SQL|
> |-----|-----|
> |Versão 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Aplicar a atualização de pilha do Azure mínima com suporte para o seu sistema integrado do Azure Stack ou implantar o mais recente do Azure Stack desenvolvimento ASDK (Kit) antes de implantar a versão mais recente do provedor de recursos do SQL.

## <a name="new-features-and-fixes"></a>Novos recursos e correções
Esta versão do provedor de recursos do SQL do Azure Stack inclui os seguintes aprimoramentos e correções:

- **Telemetria habilitada para implantações de provedor de recursos do SQL**. Coleta de telemetria foi habilitada para implantações de provedor de recursos do SQL. Telemetria coletada inclui a implantação de provedor de recursos, iniciar e parar tempos, status, mensagens de saída e detalhes do erro de saída (se aplicável).

- **Atualização de criptografia TLS 1.2**. TLS 1.2 somente suporte habilitado para a comunicação de provedor de recursos com os componentes internos do Azure Stack. 

### <a name="fixes"></a>Correções

- **Compatibilidade de PowerShell do Azure Stack de provedor de recursos SQL**. O provedor de recursos do SQL foi atualizado para funcionar com o perfil do PowerShell do Azure Stack 2018-03-01-hybrid e para fornecer compatibilidade com AzureRM 1.3.0 e posterior.

- **Folha de alteração de senha de logon SQL**. Corrigido um problema em que a senha não pode ser alterada na folha de alteração de senha. As notificações de alteração de links removidos da senha.

- **Atualização de folha de configurações do servidor de hospedagem SQL**. Corrigido um problema em que a folha de configurações incorretamente foi intitulada como "Senha".

## <a name="known-issues"></a>Problemas conhecidos 

- **SKUs do SQL pode levar até uma hora para ser visíveis no portal do**. Pode demorar até uma hora para recentemente criados SKUs fique visível para uso durante a criação de novos bancos de dados SQL. 

    **Solução alternativa**: nenhum.

- **Reutilizado logons do SQL Server**. Tentativa de criar um novo SQL logon com o mesmo nome de usuário como um logon existente na mesma assinatura resultará em reutilizando o mesmo logon e a senha existente. 

    **Solução alternativa**: usar nomes de usuário diferente ao criar novos logons sob a mesma assinatura ou crie logons com o mesmo nome de usuário em assinaturas diferentes.

- **Logons do SQL Server compartilhados causam inconsistência de dados**. Se um logon do SQL é compartilhado para vários bancos de dados do SQL na mesma assinatura, alterando a senha de login irá causar inconsistência de dados.

    **Solução alternativa**: sempre usam logons diferentes para diferentes bancos de dados na mesma assinatura.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para administradores de nuvem operacional do Azure Stack
Consulte a documentação na [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provedor de recursos do SQL](azure-stack-sql-resource-provider.md).

[Preparar para implantar o provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Atualizar o provedor de recursos do SQL de uma versão anterior](azure-stack-sql-resource-provider-update.md). 
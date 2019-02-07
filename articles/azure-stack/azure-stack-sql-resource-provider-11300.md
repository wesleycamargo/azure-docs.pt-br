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
ms.date: 1/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 1/09/2019
ms.openlocfilehash: aaa4dc48f237a4647cfde330e40eda91eeb1388d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768725"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Notas de versão de 1.1.30.0 do provedor de recursos do SQL

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

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

    **Solução alternativa**: Nenhuma.

- **Reutilizado logons do SQL Server**. Tentativa de criar um novo SQL logon com o mesmo nome de usuário como um logon existente na mesma assinatura resultará em reutilizando o mesmo logon e a senha existente. 

    **Solução alternativa**: Usar nomes de usuário diferente ao criar novos logons sob a mesma assinatura ou crie logons com o mesmo nome de usuário em assinaturas diferentes.

- **Logons do SQL Server compartilhados causam inconsistência de dados**. Se um logon do SQL é compartilhado para vários bancos de dados do SQL na mesma assinatura, alterando a senha de login irá causar inconsistência de dados.

    **Solução alternativa**: Sempre usam logons diferentes para diferentes bancos de dados na mesma assinatura.

- **Requisito de suporte do TLS 1.2**. Se você tentar implantar ou atualizar o provedor de recursos do SQL de um computador onde o TLS 1.2 não está habilitada, a operação poderá falhar. Execute o seguinte comando do PowerShell no computador que está sendo usado para implantar ou atualizar o provedor de recursos para verificar que o TLS 1.2 é retornado como o suporte para:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Se **Tls12** não está incluído na saída do comando, TLS 1.2 não está habilitado no computador.

    **Solução alternativa**: Execute o seguinte comando do PowerShell para habilitar o TLS 1.2 e, em seguida, iniciar a implantação de provedor de recursos ou atualizar o script na mesma sessão do PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **Falha do provedor de recursos do SQL adicionar o ouvinte do SQL Server Always On**. Ao usar o endereço IP do ouvinte do SQL Server sempre no ouvinte, o provedor de recursos do SQL VM não é possível resolver o nome do ouvinte host.

    **Solução alternativa**: Certifique-se de que o DNS funciona corretamente para resolver o IP do ouvinte para o nome de host do ouvinte.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para administradores de nuvem operacional do Azure Stack
Consulte a documentação na [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provedor de recursos do SQL](azure-stack-sql-resource-provider.md).

[Preparar para implantar o provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Atualizar o provedor de recursos do SQL de uma versão anterior](azure-stack-sql-resource-provider-update.md). 
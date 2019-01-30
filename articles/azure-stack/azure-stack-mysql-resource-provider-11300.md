---
title: Notas de versão do Azure MySQL pilha recursos provedor 1.1.30.0 | Microsoft Docs
description: Saiba mais sobre o que a atualização mais recente do Azure Stack MySQL recurso provedor, incluindo problemas conhecidos e onde baixá-la.
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
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 1bfdaef4523a714aed0f1b7bbdb5a600f7775ffb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244803"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>Notas de versão do provedor 1.1.30.0 de recursos do MySQL

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e problemas conhecidos no MySQL versão 1.1.30.0 do provedor de recursos.

## <a name="build-reference"></a>Referência de build
Baixe o provedor de recursos MySQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O provedor de recursos tem um mínimo correspondente do Azure Stack compilar. A versão de lançamento do Azure Stack mínima necessária para instalar esta versão do provedor de recursos MySQL esteja listada abaixo:

> |Versão mínima do Azure Stack|Versão do provedor de recursos MySQL|
> |-----|-----|
> |Atualização de pilha 1808 (1.1808.0.97) do Azure|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Aplicar a atualização de pilha do Azure mínima com suporte para o seu sistema integrado do Azure Stack ou implantar o mais recente do Azure Stack desenvolvimento ASDK (Kit) antes de implantar a versão mais recente do provedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Novos recursos e correções
Esta versão do provedor de recursos MySQL do Azure Stack inclui os seguintes aprimoramentos e correções:

- **Telemetria habilitada para implantações do provedor de recursos MySQL**. Coleta de telemetria foi habilitada para implantações de provedor de recursos do MySQL. Telemetria coletada inclui a implantação de provedor de recursos, iniciar e parar tempos, status, mensagens de saída e detalhes do erro de saída (se aplicável).

- **Atualização de criptografia TLS 1.2**. TLS 1.2 somente suporte habilitado para a comunicação de provedor de recursos com os componentes internos do Azure Stack. 

### <a name="fixes"></a>Correções

- **Compatibilidade de PowerShell do Azure Stack de provedor de recursos MySQL**. O provedor de recursos MySQL foi atualizado para funcionar com o perfil do PowerShell do Azure Stack 2018-03-01-hybrid e para fornecer compatibilidade com AzureRM 1.3.0 e posterior.

- **Folha de alteração de senha de logon de MySQL**. Corrigido um problema em que a senha não pode ser alterada na folha de alteração de senha. As notificações de alteração de links removidos da senha.

## <a name="known-issues"></a>Problemas conhecidos 

- **SKUs MySQL pode levar até uma hora para ser visíveis no portal do**. Pode demorar até uma hora para recentemente criados SKUs fique visível para uso durante a criação de novos bancos de dados MySQL. 

    **Solução alternativa**: Nenhuma.

- **Reutilizado MySQL logons**. Tentativa de criar um novo MySQL logon com o mesmo nome de usuário como um logon existente na mesma assinatura resultará em reutilizando o mesmo logon e a senha existente. 

    **Solução alternativa**: Usar nomes de usuário diferente ao criar novos logons sob a mesma assinatura ou crie logons com o mesmo nome de usuário em assinaturas diferentes.

- **Requisito de suporte do TLS 1.2**. Se você tentar implantar ou atualizar o provedor de recursos do MySQL de um computador onde o TLS 1.2 não está habilitada, a operação poderá falhar. Execute o seguinte comando do PowerShell no computador que está sendo usado para implantar ou atualizar o provedor de recursos para verificar que o TLS 1.2 é retornado como o suporte para:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Se **Tls12** não está incluído na saída do comando, TLS 1.2 não está habilitado no computador.

    **Solução alternativa**: Execute o seguinte comando do PowerShell para habilitar o TLS 1.2 e, em seguida, iniciar a implantação de provedor de recursos ou atualizar o script na mesma sessão do PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para administradores de nuvem operacional do Azure Stack
Consulte a documentação na [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provedor de recursos MySQL](azure-stack-mysql-resource-provider.md).

[Preparar para implantar o provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Atualizar o provedor de recursos do MySQL de uma versão anterior](azure-stack-mysql-resource-provider-update.md). 
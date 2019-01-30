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
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 4b9b91c17a9199c7890d17a2b721ce0803e6bd6c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243327"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Notas de versão de 1.1.33.0 do provedor de recursos do SQL

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e problemas conhecidos na versão 1.1.33.0 do provedor de recursos do SQL.

## <a name="build-reference"></a>Referência de build
Baixe o provedor de recursos do SQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O provedor de recursos tem um mínimo correspondente do Azure Stack compilar. A versão de lançamento do Azure Stack mínima necessária para instalar esta versão do provedor de recursos do SQL é listada abaixo:

> |Versão mínima do Azure Stack|Versão do provedor de recursos SQL|
> |-----|-----|
> |Versão 1808 (1.1808.0.97)|[SQL RP versão 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Aplicar a atualização de pilha do Azure mínima com suporte para o seu sistema integrado do Azure Stack ou implantar o mais recente do Azure Stack desenvolvimento ASDK (Kit) antes de implantar a versão mais recente do provedor de recursos do SQL.

## <a name="new-features-and-fixes"></a>Novos recursos e correções
Esta versão do provedor de recursos do SQL do Azure Stack inclui os seguintes aprimoramentos e correções:

### <a name="fixes"></a>Correções
- **Extensão do portal de provedor de recursos do SQL pode escolher a assinatura errada**. O provedor de recursos do SQL usa chamadas de Azure Resource Manager para determinar a primeira assinatura de administrador de serviço a ser usado, que podem não ser o *assinatura do provedor padrão*. Se isso acontecer, o provedor de recursos do SQL não funcionará normalmente. 

- **Servidor de hospedagem SQL não lista os bancos de dados hospedados.** Bancos de dados criados pelo usuário talvez não estejam listados ao exibir os recursos de locatário para servidores de hospedagem SQL.

- **Implantação de provedor (1.1.30.0) do recurso SQL anterior pode falhar se o TLS 1.2 não estiver habilitado**. Atualizado o provedor de recursos do SQL 1.1.33.0 para habilitar o TLS 1.2 ao implantar o provedor de recursos, atualizando o provedor de recursos ou girando segredos. 

- **Falha de rotação do segredo de provedor de recursos do SQL**. Corrigido o problema resultante no código de erro a seguir para girar segredos: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problemas conhecidos 

- **SKUs do SQL pode levar até uma hora para ser visíveis no portal do**. Pode demorar até uma hora para recentemente criados SKUs fique visível para uso durante a criação de novos bancos de dados SQL. 

    **Solução alternativa**: Nenhuma.

- **Reutilizado logons do SQL Server**. Tentativa de criar um novo SQL logon com o mesmo nome de usuário como um logon existente na mesma assinatura resultará em reutilizando o mesmo logon e a senha existente. 

    **Solução alternativa**: Usar nomes de usuário diferente ao criar novos logons sob a mesma assinatura ou crie logons com o mesmo nome de usuário em assinaturas diferentes.

- **Logons do SQL Server compartilhados causam inconsistência de dados**. Se um logon do SQL é compartilhado para vários bancos de dados do SQL na mesma assinatura, alterando a senha de login irá causar inconsistência de dados.

    **Solução alternativa**: Sempre usam logons diferentes para diferentes bancos de dados na mesma assinatura.

- **Falha do provedor de recursos do SQL adicionar o ouvinte do SQL Server Always On**. Ao usar o endereço IP do ouvinte do SQL Server sempre no ouvinte, o provedor de recursos do SQL VM não é possível resolver o nome do ouvinte host.

    **Solução alternativa**: Certifique-se de que o DNS funciona corretamente para resolver o IP do ouvinte para o nome de host do ouvinte.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para administradores de nuvem operacional do Azure Stack
Consulte a documentação na [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provedor de recursos do SQL](azure-stack-sql-resource-provider.md).

[Preparar para implantar o provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Atualizar o provedor de recursos do SQL de uma versão anterior](azure-stack-sql-resource-provider-update.md). 
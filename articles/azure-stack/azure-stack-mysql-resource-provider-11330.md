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
ms.date: 01/08/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: a173a21ca3690db4cf5074d067d0e9bbe929e077
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54122368"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Notas de versão do provedor 1.1.33.0 de recursos do MySQL

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e problemas conhecidos no MySQL versão 1.1.33.0 do provedor de recursos.

## <a name="build-reference"></a>Referência de build
Baixe o provedor de recursos MySQL binário e, em seguida, execute o Self-extractor para extrair o conteúdo para um diretório temporário. O provedor de recursos tem um mínimo correspondente do Azure Stack compilar. A versão de lançamento do Azure Stack mínima necessária para instalar esta versão do provedor de recursos MySQL esteja listada abaixo:

> |Versão mínima do Azure Stack|Versão do provedor de recursos MySQL|
> |-----|-----|
> |Versão 1811 (1.1811.0.101)|[RP MySQL versão 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Aplicar a atualização de pilha do Azure mínima com suporte para o seu sistema integrado do Azure Stack ou implantar o mais recente do Azure Stack desenvolvimento ASDK (Kit) antes de implantar a versão mais recente do provedor de recursos MySQL.

## <a name="new-features-and-fixes"></a>Novos recursos e correções
Esta versão do provedor de recursos MySQL do Azure Stack inclui os seguintes aprimoramentos e correções:

### <a name="fixes"></a>Correções
- **Extensão do portal de provedor de recursos do MySQL pode escolher a assinatura errada**. O provedor de recursos do MySQL usa chamadas de Azure Resource Manager para determinar a primeira assinatura de administrador de serviço a ser usado, que podem não ser o *assinatura do provedor padrão*. Se isso acontecer, o provedor de recursos MySQL não funcionará normalmente. 

- **MySQL server de hospedagem não lista hospedado bancos de dados.** Bancos de dados criados pelo usuário talvez não estejam listados ao exibir os recursos de locatário para servidores de hospedagem MySQL.

- **Implantação de provedor (1.1.30.0) de recursos MySQL anterior poderia falhar se o TLS 1.2 não estiver habilitado**. Atualizado o provedor de recursos MySQL 1.1.33.0 para habilitar o TLS 1.2 ao implantar o provedor de recursos, atualizando o provedor de recursos ou girando segredos. 

- **Falha de rotação do segredo de provedor de recursos do MySQL**. Corrigido o problema resultante no código de erro a seguir para girar segredos: ` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problemas conhecidos 

- **SKUs MySQL pode levar até uma hora para ser visíveis no portal do**. Pode demorar até uma hora para recentemente criados SKUs fique visível para uso durante a criação de novos bancos de dados MySQL. 

    **Solução alternativa**: Nenhuma.

- **Reutilizado MySQL logons**. Tentativa de criar um novo MySQL logon com o mesmo nome de usuário como um logon existente na mesma assinatura resultará em reutilizando o mesmo logon e a senha existente. 

    **Solução alternativa**: Usar nomes de usuário diferente ao criar novos logons sob a mesma assinatura ou crie logons com o mesmo nome de usuário em assinaturas diferentes.

- **Logons de MySQL compartilhados causam inconsistência de dados**. Se um logon do MySQL é compartilhado para vários bancos de dados MySQL na mesma assinatura, alterando a senha de login irá causar inconsistência de dados.

    **Solução alternativa**: Sempre usam logons diferentes para diferentes bancos de dados na mesma assinatura.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conhecidos para administradores de nuvem operacional do Azure Stack
Consulte a documentação na [notas de versão do Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o provedor de recursos MySQL](azure-stack-mysql-resource-provider.md).

[Preparar para implantar o provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Atualizar o provedor de recursos do MySQL de uma versão anterior](azure-stack-mysql-resource-provider-update.md). 
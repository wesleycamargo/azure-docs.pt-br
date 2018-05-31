---
title: Recursos de dados de cliente do Azure Key Vault | Microsoft Docs
description: Saiba mais sobre os dados do cliente no Cofre de Chaves
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/15/2018
ms.author: barclayn
ms.openlocfilehash: 12a176a6eba3b6dd83ef7fb97f89ecfe99083059
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213315"
---
# <a name="azure-key-vault-customer-data-features"></a>Recursos de dados de cliente do Azure Key Vault

O Azure Key Vault recebe os dados do cliente durante a criação ou atualização de cofres, chaves, segredos, certificados e contas de armazenamento gerenciadas. Esses dados de cliente estarão diretamente visíveis no portal do Azure e por meio da API REST. Os dados do cliente podem ser editados ou excluídos por atualizar ou excluir o objeto que contém os dados.

Os logs de acesso do sistema são gerados quando um usuário ou aplicativo acessa o Cofre de Chaves. Os logs detalhados de acesso detalhados estão disponíveis para clientes que usam o Azure Insights.

## <a name="identifying-customer-data"></a>Identificando os dados do cliente

As informações a seguir identificam os dados do cliente dentro do Azure Key Vault:

- As políticas de acesso para o Azure Key Vault contêm IDs de objeto que representam os usuários, grupos ou aplicativos
- Os assuntos do certificado podem incluir endereços de email ou outros identificadores do usuário ou organizacionais
- Contatos de certificado podem conter endereços de email do usuário, nomes ou números de telefone
- Emissores de certificado podem conter endereços de email, nomes, números de telefone, credenciais de conta e detalhes organizacionais
- Marcas arbitrárias podem ser aplicadas a objetos no Azure Key Vault. Esses objetos incluem cofres, chaves, segredos, certificados e contas de armazenamento. As marcas usadas podem conter dados pessoais
- Os logs de acesso do Azure Key Vault contêm IDs de objeto, [UPNs](../active-directory/connect/active-directory-aadconnect-userprincipalname.md)e endereços IP para cada chamada à API REST
- Os logs de diagnóstico do Azure Key Vault podem conter IDs de objeto e endereços de IP para as chamadas de API REST

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente

As mesmas APIs REST, experiência do Portal e SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento gerenciado, também são capazes de atualizar e excluir esses objetos.

A exclusão reversível permite recuperar os dados excluídos por 90 dias após a exclusão. Ao usar a exclusão reversível, os dados poderão ser excluídos permanentemente antes de 90 dias, o período de retenção expira, executando uma operação de limpeza. Se o cofre ou a assinatura tiver sido configurado para operações de limpeza de bloco, não é possível excluir permanentemente os dados até que o período de retenção agendado tenha passado.

## <a name="exporting-customer-data"></a>Exportando os dados do cliente

As mesmas APIs REST, experiência do Portal e SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento gerenciado também permitem que você visualize e exporte esses objetos.

O logging de acesso do Azure Key Vault é um recurso opcional que pode ser ativado para gerar os logs para cada chamada API REST. Esses logs serão transferidos para uma conta de armazenamento em sua assinatura em que você aplica a política de retenção que atenda aos requisitos da sua organização.

Os logs de diagnóstico do Azure Key Vault contem dados pessoas que podem ser recuperados fazendo uma solicitação de exportação no portal Privacidade do Usuário. Essa solicitação deve ser feita pelo administrador do locatário.

## <a name="next-steps"></a>Próximas etapas

- [Logs do Cofre da Chave do Azure](key-vault-logging.md)

- [Visão geral de exclusão reversível do Azure Key Vault](key-vault-soft-delete-cli.md)

- [Vaults](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Operações de chave do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Operações de segredo do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certificados e políticas do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Emissores de certificado](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Chaves de conta de Armazenamento do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)

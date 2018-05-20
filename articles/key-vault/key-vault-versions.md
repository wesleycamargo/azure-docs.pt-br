---
title: Versões do Key Vault
description: As diversas versões do Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-versions"></a>Versões do Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - Chaves da Conta de Armazenamento Gerenciadas

Verão de 2017 - o recurso de Chaves de Conta de Armazenamento facilitou a integração com o Armazenamento do Azure. Consulte o tópico de visão geral para saber mais, [Visão geral das Chaves de Conta de Armazenamento Gerenciado](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - Exclusão reversível

Verão de 2017 - recurso de exclusão reversível recurso adicionado para melhorar a proteção dos de seus cofres de chave e objetos no cofre chave. Para saber mais, confira o tópico de visão geral, [Visão geral da exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - Gerenciamento de certificados

Gerenciamento de certificado foi adicionado como um recurso para a versão de GA 2015-06-01 em 26 de setembro de 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - Disponibilidade geral

Versão de Disponibilidade geral 2015-06-01, anunciada em 24 de junho de 2015.

As seguintes alterações foram feitas nessa versão:

- Excluir uma chave - campo "usar" removido.
- Obtenha informações sobre uma chave - campo "usar" removido.
- Importar uma chave em um cofre - campo "usar" removido.
- Restaurar uma chave - campo "usar" removido.
- "RSA_OAEP" alterado para "RSA-OAEP" para Algoritmos RSA. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-versão prévia 

Segunda versão prévia 2015-02-01-versão prévia, lançada em 20 de abril de 2015. Para saber mais, consulte a postagem no blog [Atualização da API REST](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx).

As seguintes tarefas foram atualizadas:

- Listar as chaves em um cofre - adição de suporte a paginação para a operação.
- Listar as versões de uma chave - adição de operação para listar as versões de uma chave.
- Listar os segredos em um cofre - adição de suporte a paginação.
- Listar as versões de um segredo - adição de operação para listar as versões de um segredo.
- Todas as operações - adição de carimbos de data/hora de criação/atualização dos atributos.
- Criar um segredo - adição de Content-Type aos segredos.
- Criar uma chave - adição de marcas como informações opcionais.
- Criar um segredo - adição de marcas como informações opcionais.
- Atualizar uma chave - adição de marcas como informações opcionais.
- Atualizar um segredo - adição de marcas como informações opcionais.
- Alteração do tamanho máximo para segredos de 10 K para 25 K Bytes. Confira [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-versão prévia

Primeira versão prévia 2014-12-08-versão prévia, lançada em 8 de janeiro de 2015.

## <a name="see-also"></a>Consulte também
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)

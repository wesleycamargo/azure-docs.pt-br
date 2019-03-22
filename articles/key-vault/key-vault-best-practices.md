---
title: Práticas recomendadas para usar o Key Vault - Azure Key Vault | Microsoft Docs
description: Este documento explica algumas das práticas recomendadas para usar o Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: 85bd1858bc3f2d505e1e4d0a88e8c77a46ae4447
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57887961"
---
# <a name="best-practices-to-use-key-vault"></a>Práticas recomendadas para usar o Cofre de chaves

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu cofre

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Isso [artigo](key-vault-secure-your-key-vault.md) fornece uma visão geral do modelo de acesso do Cofre de chaves. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

Sugestões enquanto controla o acesso ao seu cofre são da seguinte maneira:
1. Bloquear o acesso a sua assinatura, o grupo de recursos e a chave de cofres (RBAC)
2. Criar políticas de acesso para cada cofre
3. Usar a entidade de segurança de acesso com privilégios mínimos para conceder acesso
4. Ativar o Firewall e [pontos de extremidade de serviço de rede virtual](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Usar o Cofre de chaves separado

Nossa recomendação é usar um cofre por aplicativo por ambiente (desenvolvimento, pré-produção e produção). Isso ajuda você a compartilhar não segredos em ambientes e também reduz a ameaça em caso de violação.

## <a name="backup-your-vault"></a>Seu Cofre de backup

Verifique se você regulares voltar cumulativos de atualizações para seu [cofre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) em update/delete/criação de objetos dentro de um cofre.

## <a name="turn-on-logging"></a>Ativar o registro em log

[Ativar o log](key-vault-logging.md) para o cofre. Também Configure alertas.

## <a name="turn-on-recovery-options"></a>Ativar as opções de recuperação

1. Ative [exclusão reversível](key-vault-ovw-soft-delete.md).
2. Ative a proteção de limpeza se você quiser se proteger contra a exclusão de força do segredo / cofre mesmo após a exclusão reversível está ativada.

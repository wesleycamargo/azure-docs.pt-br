---
title: Autorizar acesso ao Armazenamento do Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as diferentes maneiras de autorizar acesso ao Armazenamento do Microsoft Azure, incluindo o Azure Active Directory, a autenticação de Chave Compartilhada ou assinaturas de acesso compartilhado.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 03/21/2019
ms.date: 04/08/2019
ms.author: v-jay
ms.subservice: common
ms.openlocfilehash: ba4d83d620a597c9a59f1a3c7f2f9d6722ba42d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483699"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizar acesso ao Armazenamento do Microsoft Azure

Sempre que você acessar os dados na conta de armazenamento, seu cliente fará uma solicitação sobre HTTP/HTTPS para Armazenamento do Microsoft Azure. Toda solicitação para um recurso seguro deve ser autorizada para que o serviço garanta que o cliente tenha as permissões necessárias para acessar os dados. O Armazenamento do Microsoft Azure oferece essas opções para autorizar o acesso a recursos seguros:

- **Integração do Active Directory (Azure AD)** para blobs e filas. O Azure AD fornece RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente a recursos em uma conta de armazenamento. Para obter mais informações, consulte [autenticar solicitações para o armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização de chave compartilhada** para blobs, arquivos, filas e tabelas. Um cliente usando a Chave Compartilhada passa um cabeçalho com cada solicitação assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Assinaturas de acesso compartilhado** para blobs, arquivos, filas e tabelas. SAS (assinaturas de acesso compartilhado) fornecem acesso delegado limitado a recursos em uma conta de armazenamento. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões concedidas fornecerá flexibilidade no gerenciamento de acesso. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-dotnet-shared-access-signature-part-1.md) para saber mais.
- **Acesso de leitura pública anônimo** para contêineres e blobs. A autorização não é necessária. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md).  

Por padrão, todos os recursos no Armazenamento do Microsoft Azure são protegidos e estão disponíveis apenas para o proprietário da conta. Embora seja possível usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes o acesso a recursos na conta de armazenamento, a Microsoft recomenda o uso do Azure AD quando possível para obter máxima segurança e facilidade de uso. 




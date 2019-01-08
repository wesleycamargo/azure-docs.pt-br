---
title: Introdução ao Cofre de chaves de pilha do Azure | Microsoft Docs
description: Saiba como a pilha do Azure Key Vault gerencia chaves e segredos
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: 40a748895038dbadea7d12d79268f89796a4b428
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54061494"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introdução ao Cofre de chaves no Azure Stack

## <a name="prerequisites"></a>Pré-requisitos

* Você deve assinar uma oferta que inclui o serviço do Azure Key Vault.  
* [PowerShell está configurado para uso com o Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Noções básicas sobre o Cofre de chaves

Cofre de chaves no Azure Stack ajuda a proteger chaves criptográficas e segredos que aplicativos e serviços de nuvem usam. Ao usar o Cofre de chaves, você pode criptografar chaves e segredos, como:

* Chaves de autenticação
* Chaves da conta de armazenamento
* Chaves de criptografia de dados
* arquivos. pfx
* Senhas

O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-las diretamente para chaves de produção. Os administradores de segurança podem conceder (e revogar) permissões para chaves, conforme necessário.

Qualquer pessoa com uma assinatura do Azure Stack pode criar e usar cofres de chaves. Embora o Cofre de chaves beneficie os desenvolvedores e administradores de segurança, o operador que gerencia outros serviços do Azure Stack para uma organização pode implementar e gerenciá-lo. Por exemplo, o Azure Stack operador pode entrar com uma assinatura do Azure Stack, crie um cofre para a organização na qual armazenar chaves e, em seguida, ser responsável por essas tarefas operacionais:

* Criar ou importar uma chave ou segredo.
* Revogar ou excluir uma chave ou segredo.
* Autorize usuários ou aplicativos para acessar o Cofre de chaves para que eles possam gerenciar ou usar suas chaves e segredos.
* Configurar o uso de chave (por exemplo, assinar ou criptografar).

O operador pode, em seguida, fornecer aos desenvolvedores com o recurso uniformes (URIs) para chamar a partir de seus aplicativos. Operadores também podem fornecer aos administradores de segurança com informações de uso de chave de registro em log.

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para obter mais informações, consulte o guia do desenvolvedor do Cofre de chaves.

## <a name="scenarios"></a>Cenários

Os cenários a seguir descrevem como o Key Vault pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

### <a name="developer-for-an-azure-stack-application"></a>Desenvolvedor de um aplicativo do Azure Stack

**Problema:** Eu quero escrever um aplicativo para o Azure Stack que usa chaves de assinatura e criptografia. Quero que as chaves sejam externas meu aplicativo, para que a solução seja adequada para um aplicativo distribuído geograficamente.

**Instrução:** As chaves são armazenadas em um cofre e invocadas por um URI, quando necessário.

### <a name="developer-for-software-as-a-service-saas"></a>Desenvolvedor de SaaS (software como serviço)

**Problema:** Eu não quero a responsabilidade ou responsabilidade em potencial para meu cliente chaves e segredos. Quero que os clientes possuam e gerenciem suas chaves, para que eu possa me concentrar em fazer o que faço melhor, que é fornecer os principais recursos de software.

**Instrução:** Os clientes podem importar suas próprias chaves para o Azure Stack e, em seguida, gerenciá-los.

### <a name="chief-security-officer-cso"></a>Diretor de segurança (CSO)

**Problema:** Quero garantir que minha organização controle o ciclo de vida da chave e possa monitorar seu uso.

**Instrução:** O Key Vault foi projetado para que a Microsoft não veja nem extraia suas chaves. Quando um aplicativo precisa executar operações criptográficas usando chaves do cliente, o Cofre de chaves usa as chaves em nome do aplicativo. O aplicativo não vê as chaves do cliente. Embora usemos vários serviços do Azure Stack e recursos, você pode gerenciar as chaves de um único local no Azure Stack. O cofre fornece uma única interface, independentemente de quantos cofres você tenha no Azure Stack, quais regiões eles suporte e quais aplicativos usá-los.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar cofre de chaves no Azure Stack, o portal](azure-stack-kv-manage-portal.md)  
* [Gerenciar o Key Vault no Azure Stack usando o PowerShell](azure-stack-kv-manage-powershell.md)


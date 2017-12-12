---
title: "Introdução de pilha Cofre de chaves do Azure | Microsoft Docs"
description: Saiba como o Cofre de chaves do Azure pilha gerencia chaves e segredos
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: mabrigg
ms.openlocfilehash: a50a03e70ccf014a8a9d33e0f177febed560853f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introdução ao Cofre de chaves na pilha do Azure

## <a name="prerequisites"></a>Pré-requisitos 

* Você deve assinar uma oferta que inclui o serviço de Cofre de chaves do Azure.  
* [PowerShell está configurado para uso com o Azure pilha](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Noções básicas sobre o Cofre de chaves
Cofre de chaves na pilha do Azure ajuda a proteger as chaves de criptografia e usam segredos que aplicativos e serviços de nuvem. Usando o Cofre de chaves, você pode criptografar chaves e segredos, como:
   * Chaves de autenticação 
   * Chaves da conta de armazenamento
   * Chaves de criptografia de dados
   * arquivos. pfx
   * Senhas

O Cofre da Chave simplifica o processo de gerenciamento de chaves e permite que você tenha controle das chaves que acessam e criptografam seus dados. Desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-las diretamente para chaves de produção. Administradores de segurança podem conceder (e revogar) permissão a chaves conforme for necessário.

Qualquer pessoa com uma assinatura do Azure pilha pode criar e usar o Cofre de chave. Embora o Cofre de chaves beneficia os desenvolvedores e administradores de segurança, o operador que gerencia outros serviços da pilha do Azure para uma organização pode implementar e gerenciar. Por exemplo, a pilha do Azure operador pode entrar com uma assinatura de pilha do Azure, crie um cofre para a organização na qual armazenar chaves e, em seguida, ser responsável por essas tarefas operacionais:

* Criar ou importar uma chave ou segredo.
* Revogar ou excluir uma chave ou segredo.
* Autorize usuários ou aplicativos para acessar o Cofre de chaves, para que eles podem gerenciar ou usar suas chaves e segredos.
* Configurar o uso de chave (por exemplo, assinar ou criptografar).

O operador, em seguida, pode fornecer aos desenvolvedores Uniform Resource Identifiers URIs () para chamar a partir de seus aplicativos. Operadores também podem fornecer aos administradores de segurança com as informações de log de uso de chave.

Os desenvolvedores também podem gerenciar as chaves diretamente, por meio de APIs. Para obter mais informações, consulte o guia do desenvolvedor do Cofre de chaves.

## <a name="scenarios"></a>Cenários
Os cenários a seguir descrevem como o Cofre de chaves pode ajudar a atender às necessidades de desenvolvedores e administradores de segurança.

### <a name="developer-for-an-azure-stack-application"></a>Desenvolvedor de um aplicativo de pilha do Azure
**Problema:** quero escrever um aplicativo para a pilha do Azure que usa chaves de assinatura e criptografia. Desejo essas chaves como externo de meu aplicativo para que a solução é adequada para um aplicativo que é distribuído geograficamente.

**Instrução:** chaves são armazenadas em um cofre e invocadas por um URI, quando necessário.

### <a name="developer-for-software-as-a-service-saas"></a>Desenvolvedor de software como serviço (SaaS)
**Problema:** não quero a responsabilidade de responsabilidade ou potencial para meu cliente chaves e segredos. Desejo que os clientes de implantar e gerenciar suas chaves, para que possam concentrar-se sobre o que fazer melhor, que fornece os principais recursos de software.

**Instrução:** os clientes podem importar suas próprias chaves para a pilha do Azure e gerenciá-los. 

### <a name="chief-security-officer-cso"></a>Diretor de segurança (CSO)
**Problema:** desejo Certifique-se de que a minha organização tem controle sobre o ciclo de vida de chave e pode monitorar o uso da chave.

**Instrução:** Cofre de chaves foi projetado para que a Microsoft não consulte ou extrair as chaves. Quando um aplicativo precisa para executar operações de criptografia usando chaves de clientes, o Cofre de chaves usa as chaves em nome do aplicativo. O aplicativo não vê as chaves dos clientes. Embora usamos vários recursos e serviços de pilha do Azure, você pode gerenciar as chaves de um único local na pilha do Azure. O cofre fornece uma única interface, independentemente de quantas cofres na pilha do Azure, quais regiões suporte e quais aplicativos usá-los.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar o Cofre de chaves na pilha do Azure usando o portal](azure-stack-kv-manage-portal.md)  
* [Gerenciar o Cofre de chaves na pilha do Azure usando o PowerShell](azure-stack-kv-manage-powershell.md)


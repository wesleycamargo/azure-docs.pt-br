---
title: Serviço de Blockchain do Azure Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027909"
---
# <a name="azure-blockchain-service-consortium"></a>Serviço de Blockchain do Azure Consortium

Usando o serviço de Blockchain do Azure, você pode criar consortium privada blockchain redes em que cada rede de blockchain pode ser limitado aos participantes específicos na rede. Somente os participantes na rede privada consortium blockchain podem exibir e interagir com o blockchain. Redes Consortium no serviço de Blockchain do Azure podem conter dois tipos de funções de membro participante:

* **Administrador** -privilegiado participantes que podem executar ações de gerenciamento consortium e podem participar de transações de blockchain.

* **Usuário** -os participantes que não podem executar qualquer ação de gerenciamento consortium, mas podem participar de transações de blockchain.

Redes Consortium podem ser uma combinação de funções de participante e podem ter um número arbitrário de cada tipo de função. Deve haver pelo menos um administrador.

O diagrama a seguir mostra uma rede de consórcio com vários participantes:

![Diagrama de rede privada consortium](./media/consortium/network-diagram.png)

Com o gerenciamento de consortium no serviço de Blockchain do Azure, você pode gerenciar os participantes na rede de consórcio. Gerenciamento do consórcio é baseado no modelo de consenso da rede. Na versão de visualização atual, o serviço de Blockchain do Azure fornece um modelo de consenso centralizado para gerenciamento consortium. Qualquer participante com privilégios com uma função de administrador pode executar ações de gerenciamento de consortium, como adicionar ou remover participantes de uma rede.

## <a name="roles"></a>Funções

Os participantes de um consórcio podem ser indivíduos ou organizações e podem ser atribuídos uma função de usuário ou uma função de administrador. A tabela a seguir lista as diferenças entre as duas funções de alto nível:

| Ação | Função de usuário | Função de administrador
|--------|:----:|:------------:|
| Criar novo membro | Sim | Sim |
| Convidar novos membros | Não  | Sim |
| Definir ou alterar a função de membro participante | Não  | Sim |
| Alterar nome de exibição de membro | Somente para o próprio membro | Somente para o próprio membro |
| Remover membros | Somente para o próprio membro | Sim |
| Participar de transações de blockchain | Sim | Sim |

### <a name="user-role"></a>Função de usuário

Os usuários são participantes consortium sem capacidades de administrador. Eles não podem participar no gerenciamento de membros relacionados ao consortium. Os usuários podem alterar seu nome de exibição de membro e podem remover de um consórcio.

### <a name="administrator"></a>Administrador

Um administrador pode gerenciar os membros dentro do consortium. Um administrador pode convidar membros, remover membros ou atualizar funções de membros dentro do consórcio.
Sempre deve haver pelo menos um administrador em um consórcio. O último administrador deve especificar outro participante como uma função de administrador antes de deixar um consórcio.

## <a name="managing-members"></a>Gerenciamento de membros

Somente os administradores podem convidar outros participantes do consortium. Os administradores de convidar participantes usando a ID de assinatura do Azure.

Depois que o convidado, os participantes poderão ingressar o blockchain consortium Implantando um novo membro no serviço de Blockchain do Azure. Para exibir e Junte-se o convidado consortium, você deve especificar a mesma ID de assinatura do Azure usada no convite pelo administrador de rede.

Os administradores podem remover todos os participantes do consórcio, incluindo outros administradores. Os membros só podem remover em si de um consórcio.

## <a name="consortium-management-smart-contract"></a>Contrato de inteligentes de gerenciamento Consortium

Gerenciamento de Consortium no serviço de Blockchain do Azure é feito por meio de contratos inteligentes de gerenciamento consortium. Os contratos inteligentes são implantados automaticamente para os nós quando você implanta um novo membro de blockchain.

O endereço do contrato raiz consortium gerenciamento inteligente pode ser exibido no portal do Azure. O **RootContract endereço** está na seção de visão geral de blockchain do membro.

![Endereço RootContract](./media/consortium/rootcontract-address.png)

Você pode interagir com o contrato de inteligentes de gerenciamento do consortium usando o gerenciamento de consortium [módulo do PowerShell](manage-consortium-powershell.md), o Azure portal, ou diretamente por meio do contrato inteligente usando o serviço de Blockchain do Azure gerado Ethereum conta.

## <a name="ethereum-account"></a>Conta Ethereum

Quando um membro é criado, uma chave de conta Ethereum é criada. Azure Blockchain Service usa a chave para criar as transações relacionadas ao gerenciamento de consortium. A chave da conta Ethereum é gerenciada automaticamente pelo serviço de Blockchain do Azure.

A conta de membro pode ser exibida no portal do Azure. A conta do membro é na seção de visão geral de blockchain do membro.

![Conta do membro](./media/consortium/member-account.png)

Você pode redefinir a sua conta de Ethereum clicando em sua conta do membro e inserindo uma nova senha. O endereço da conta Ethereum e a senha serão redefinidas.  

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar os membros no serviço de Blockchain do Azure usando o PowerShell](manage-consortium-powershell.md)

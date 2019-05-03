---
title: Visão geral de Blockchain Service do Azure
description: Visão geral dos serviços de Blockchain do Azure
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 4416c30f57b469ee125400c696e8b34311a94926
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028179"
---
# <a name="what-is-azure-blockchain-service"></a>O que é o serviço do Azure Blockchain?

Azure Blockchain Service é um serviço de razão totalmente gerenciado que permite aos usuários a capacidade de crescimento e operar redes de blockchain em grande escala no Azure. Ao fornecer controle unificada para gerenciamento de infraestrutura, bem como controle de rede de blockchain, Blockchain o serviço do Azure fornece:

* Implantação de rede simples e operações
* Gerenciamento de consortium internos
* Desenvolver contratos inteligentes com ferramentas de desenvolvimento familiar

Azure Blockchain Service foi projetado para dar suporte a vários protocolos de razão. Atualmente, ele oferece suporte para o Ethereum [Quorum](https://www.jpmorgan.com/Quorum) razão usando o [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) mecanismo de consenso.

Esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Você pode se concentrar no desenvolvimento de aplicativos e lógica de negócios em vez de alocar tempo e recursos para gerenciamento de infraestrutura e máquinas virtuais. Além disso, você pode continuar a desenvolver seu aplicativo com a plataforma de sua escolha para entregar suas soluções sem precisar aprender novas habilidades e ferramentas de código-fonte aberto.

## <a name="network-deployment-and-operations"></a>Operações e a implantação de rede

Implantar o serviço de Blockchain do Azure pode ser feito por meio do portal do Azure, CLI do Azure, bem como por meio de código do Visual Studio usando a extensão de Blockchain do Azure.  Implantação é simplificada, incluindo a transação e o validador de nós, redes virtuais do Azure para isolamento de segurança, bem como armazenamento gerenciado pelo serviço de provisionamento.  Além disso, ao implantar um novo membro de blockchain, os usuários também criar ou ingressar um consórcio.  Consórcio permitem que várias partes em diferentes assinaturas do Azure ser capaz de se comunicar com segurança uma da outra em um blockchain compartilhado.  Essa implantação simplificada reduz a implantação de rede de blockchain de dias para minutos.

### <a name="performance-and-service-tiers"></a>Camadas de serviço e desempenho

O serviço de Blockchain do Azure oferece duas camadas de serviço: *Básico* e *Standard*. Cada camada oferece diferentes de desempenho e recursos para dar suporte ao desenvolvimento leve e testar cargas de trabalho até altamente dimensionados implantações de blockchain de produção. Ambas as camadas incluem o nó de pelo menos uma transação e um nó de validador (Basic) ou dois nós de validador (padrão).

![Tipos de preço](./media/overview/pricing-tiers.png)

Além de oferecer dois nós de validador, o *Standard* camada fornece 2 *vCores* para cada nó de transação e validador, enquanto a camada básica oferece uma configuração de 1 vCore.  Oferecendo 2 núcleos virtuais para nós de transação e validador, 1 vCore pode ser dedicado a razão de Quorum, enquanto o 1 vCore restante pode ser usado para outros serviços relacionados à infra-estrutura, garantindo um desempenho ideal para a produção cargas de trabalho do blockchain. Para obter mais informações sobre os detalhes de preços, consulte [preços do serviço do Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Segurança e manutenção

Depois de provisionar seu primeiro membro de blockchain, você tem a capacidade de adicionar nós de transações adicionais para o membro.  Por padrão, nós de transação são protegidos por meio de regras de firewall e precisarão ser configurado para acesso.  Além disso, todos os nós de transação criptografam dados em movimento via TLS.  Existem várias opções para proteger o acesso do nó de transação, incluindo as regras de firewall, autenticação básica, as chaves de acesso, bem como integração do Active Directory do Azure. Para obter mais informações, consulte [configurar nós de transação](configure-transaction-nodes.md) e [configurar o acesso do Azure Active Directory](configure-aad.md).

Como um serviço gerenciado, o serviço de Blockchain do Azure garante que nós do membro seu blockchain são corrigidas com o host mais recente atualizações de pilha de software de sistema e a razão de operando, configurado para alta disponibilidade (somente na camada Standard), eliminando grande parte das operações de desenvolvimento necessário para nós de blockchain IaaS tradicionais.  Para obter mais informações sobre a aplicação de patches e atualizações, consulte [suporte para versões de razão do serviço do Azure Blockchain](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitoramento e registro em log

Além disso, o serviço de Blockchain do Azure fornece métricas avançadas por meio do serviço do Azure Monitor fornecendo percepções de nós de CPU, memória e utilização de armazenamento, bem como informações úteis sobre atividade de rede de blockchain como transações e os blocos podem ser extraídos, profundidade da fila de transações, bem como as conexões ativas.  As métricas podem ser personalizadas para fornecer modos de exibição para as informações que são importantes para seu aplicativo de blockchain.  Além disso, os limites podem ser definidos por meio de alertas, permitindo que os usuários disparar ações como enviar uma mensagem de email ou texto, executando um aplicativo lógico, a função do Azure ou o envio para um webhook personalizado.

![Métricas](./media/overview/metrics.png)

Por meio do Azure Log Analytics, os usuários podem exibir logs relacionados a razão de Quorum ou outras conexões de informações importantes, como tentativas para os nós de transação.

## <a name="built-in-consortium-management"></a>Gerenciamento de consortium internos

Ao implantar seu primeiro membro de blockchain, você ingressar ou cria um novo consortium.  Um consórcio é um grupo lógico usado para gerenciar a governança e a conectividade entre os membros de blockchain que transact em um processo de várias parte.  Azure Blockchain Service fornece controles de governança internas por meio de predefinidos contratos inteligentes, que determinam quais membros de ações em consórcio podem executar.  Esses controles de governança podem ser personalizados conforme necessário pelo administrador do consórcio. Quando você cria um novo consortium, o membro de blockchain é o administrador padrão do consórcio, permitindo a capacidade de convidar outros participantes para ingressar seu consortium.  Você pode associar um consórcio somente se você foi convidado anteriormente.  Ao unir um consórcio, seu membro de blockchain é sujeitos a controles de governança colocados em vigor pelo administrador do consórcio.

![Gerenciamento de Consortium](./media/overview/consortium.png)

Ações de gerenciamento, como adição e remoção de membros de um consórcio de Consortium podem ser acessadas por meio do PowerShell e uma API REST. Você pode gerenciar programaticamente um consórcio através de interfaces comuns em vez de modificar e enviando contratos inteligentes com base em solidez. Para obter mais informações, consulte [gerenciamento consortium](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Desenvolver usando ferramentas de desenvolvimento familiar

Com base na razão de Quorum Ethereum de software livre, você pode desenvolver aplicativos para o serviço de Blockchain do Azure da mesma maneira como faria para os aplicativos existentes do Ethereum. Trabalhando com parceiros líderes do setor, a extensão do Azure Blockchain Development Kit Visual Studio Code permite aos desenvolvedores aproveitar ferramentas familiares como o Suite Truffle criar contratos inteligentes. Usando a extensão do Kit de desenvolvimento de Blockchain do Azure, os desenvolvedores podem criar ou se conectar a e consortium existente para que você pode criar e implantar seu inteligente contratos todos partir de um IDE. Usando a extensão do Azure Blockchain Visual Studio Code, você pode criar ou conectar-se a um consórcio existente para que você pode criar e implantar contratos inteligentes tudo a partir de um IDE. Para obter mais informações, consulte [Kit de desenvolvimento de Blockchain do Azure no marketplace do VS Code](http://aka.ms/vscodebcextension) e o [guia de usuário do Kit de desenvolvimento do Azure Blockchain](http://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Suporte e comentários

Precisa de Ajuda ou tiver comentários?

* Visite o [blog do Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), e [Fórum do Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Próximas etapas

Para começar, experimente um início rápido ou descobrir mais detalhes desses recursos.
* [Criar um membro de blockchain usando o portal do Azure](create-member.md) ou [criar um membro de blockchain usando a CLI do Azure]()
* Para comparações de custo e calculadoras, consulte o [página de preços](https://azure.microsoft.com/pricing/details/blockchain-service).
* Criar seu primeiro aplicativo usando o [Kit de desenvolvimento de Blockchain do Azure](https://github.com/Azure-Samples/blockchain-devkit)
* Extensão do Azure Blockchain VSCode [guia do usuário](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)

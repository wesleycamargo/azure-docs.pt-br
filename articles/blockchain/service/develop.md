---
title: Visão geral de desenvolvimento do Azure Blockchain Service
description: Introdução sobre o desenvolvimento de soluções no serviço de Blockchain do Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027894"
---
# <a name="azure-blockchain-service-development-overview"></a>Visão geral de desenvolvimento do Azure Blockchain Service

Com o serviço de Blockchain do Azure, você pode criar redes de blockchain para habilitar cenários de empresa como o acompanhamento de ativos, token digital, fidelidade e recompensa, cadeia de suprimentos financeiros e provenance consortium. Este artigo é uma introdução à visão geral de desenvolvimento do serviço de Blockchain do Azure e tópicos principais para implementar o blockchain para empresa.

## <a name="client-connection-to-azure-blockchain-service"></a>Conexão de cliente para o serviço do Azure Blockchain

Há diferentes tipos de clientes para redes de blockchain, incluindo nós completos, nós de luz e os clientes remotos. Service de Blockchain do Azure cria uma rede de blockchain que inclui os nós. Você pode usar diferentes clientes como o seu gateway para o serviço de Blockchain do Azure para desenvolvimento de blockchain. Azure Blockchain Service oferece autenticação básica ou a chave de acesso como um ponto de extremidade de desenvolvimento. Estes são os clientes populares, você pode usar o connect.

### <a name="metamask"></a>MetaMask

MetaMask é uma carteira baseados em navegador (cliente remoto), o cliente RPC e o explorer contrato básico. Ao contrário de outras carteiras de navegador, MetaMask injeta uma instância de web3 o contexto de JavaScript do navegador, que atua como um cliente RPC que se conecta a uma variedade de Ethereum blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, nó do RPC local, etc.). Você pode configurar RPC personalizado facilmente para se conectar ao serviço de Blockchain do Azure e começar o desenvolvimento de blockchain usando Remix.

### <a name="geth"></a>Geth

Geth é a interface de linha de comando para a execução de um nó Ethereum completo implementado em Ir. Você não precisa executar completo do nó, mas pode iniciar o console interativo que fornece um ambiente de tempo de execução do JavaScript expor uma API de JavaScript para interagir com o serviço de Blockchain do Azure.

## <a name="development-framework-configuration"></a>Configuração da estrutura de desenvolvimento

Para desenvolver soluções de blockchain empresariais sofisticados, uma estrutura de desenvolvimento é necessário para se conectar a redes diferentes de blockchain, gerenciar o ciclo de vida do contrato inteligente, automatize os testes, implantar contrato inteligente com scripts e equipar um console interativo.

Truffle é uma estrutura de desenvolvimento de blockchain populares para escrever, compilar, implantar e testar aplicativos descentralizados em blockchains Ethereum. Você também pode pensar Truffle como uma estrutura que tenta integrar perfeitamente o desenvolvimento de smart contrato e desenvolvimento tradicional da web.

Até mesmo o menor projeto interage com pelo menos dois nós de blockchain: Um no computador do desenvolvedor e o outro que representa a rede em que o desenvolvedor implanta seu aplicativo. Por exemplo, o principal rede Ethereum pública ou um serviço de Blockchain do Azure. Truffle fornece um sistema para gerenciar os artefatos de compilação e implantação para cada rede e faz isso de forma que simplifica a implantação do aplicativo final. Para saber mais, confira [Início Rápido: Use Truffle para se conectar a uma rede um serviço do Azure Blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transação de Ethereum Quorum privada

Quorum é um protocolo de razão distribuído com base em Ethereum com transação além de contrato de privacidade e novos mecanismos de consenso. Principais aprimoramentos sobre o Go Ethereum incluem:

* Privacidade – Quorum dá suporte a transações privadas e contratos privados por meio da separação do estado públicas e privadas e utiliza as trocas de mensagens criptografadas de peer-to-peer para direcionado transferência de dados privados para os participantes da rede.
* Mecanismos alternativos de consenso - sem necessidade de consenso de prova de trabalho ou de prova de jogo em uma rede com permissão. Quorum oferece vários mecanismos de consenso são projetados para cadeias de consortium como REPOSICIONAMENTO e IBFT.  Serviços de Blockchain do Azure usa o mecanismo de consenso IBFT.

* Emparelhar permissão - permissão de nó e ponto a ponto usando contratos inteligentes, garantir que apenas as pessoas conhecidas pode se associar à rede
* Maior desempenho - Quorum oferece um desempenho maior que Geth pública

Consulte [Tutorial: Enviar uma transação usando o serviço do Azure Blockchain](send-transaction.md) para obter um exemplo de transação privada.

## <a name="block-explorers"></a>Gerenciadores de bloco

Gerenciadores de bloco são os navegadores de blockchain online que exibem conteúdo de bloco individual, os dados de endereço de transação e o histórico. Informações do bloco básico está disponível por meio do Azure Monitor no serviço de Blockchain do Azure, no entanto, se você precisar obter informações mais detalhadas durante o desenvolvimento, gerenciadores de bloco podem ser útil.  Há gerenciadores de bloco de código-fonte aberto populares, que você pode usar. A seguir está uma lista de gerenciadores de bloco que funcionam com o serviço de Blockchain do Azure:

* [Gerenciador de serviços do Azure Blockchain](https://web3labs.com/azure-offer) de laboratórios de Web3
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Medida TPS

Como o blockchain é usado em mais cenários empresariais, transações por velocidade do segundo (TPS) é importante para evitar afunilamentos e ineficiências de sistema. Altas taxas de transação podem ser difícil de manter dentro de um blockchain descentralizado. Uma medida de TPS precisa pode ser afetada por diversos fatores, como o thread do servidor, tamanho da fila de transações, latência de rede e segurança. Se você precisa medir a velocidade TPS durante o desenvolvimento, uma ferramenta de código-fonte aberto popular é [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Use Truffle para se conectar a uma rede um serviço do Azure Blockchain](connect-truffle.md)

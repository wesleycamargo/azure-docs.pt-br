---
title: Visão geral do Azure Blockchain Workbench
description: Visão geral do Azure Blockchain Workbench e seus recursos.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/21/2018
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 9cd8ef3977d12364759838b46632ba32e0de6e70
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="what-is-azure-blockchain-workbench"></a>O que é o Azure Blockchain Workbench?

O Azure Blockchain Workbench é uma coleção de serviços e recursos do Azure desenvolvidos para ajudar você a criar e implantar Aplicações Blockchain para compartilhar processos comerciais e dados com outras organizações. O Azure Blockchain Workbench oferece os recursos de infraestrutura para desenvolver aplicativos de blockchain, permitindo que os desenvolvedores foquem na criação de lógica de negócios e contratos inteligentes. Ele também facilita a criação de aplicações blockchain integrando vários recursos e serviços do Azure para ajudar a automatizar tarefas comuns de desenvolvimento.

## <a name="create-blockchain-applications"></a>Criar aplicações blockchain

Com o Blockchain Workbench, você pode definir aplicações blockchain usando a configuração e escrever o código de contrato inteligente. Você pode iniciar rapidamente o desenvolvimento de aplicações blockchain e focar em definir o seu contrato e escrever a lógica de negócios em vez de criar a infraestrutura e configurar serviços de suporte.

## <a name="manage-applications-and-users"></a>Gerenciar aplicativos e usuários

O Azure Blockchain Workbench fornece uma aplicação Web e APIs REST para o gerenciamento de usuários e aplicações blockchain. Os administradores do Blockchain Workbench podem gerenciar o acesso a aplicação e atribuir funções de aplicação aos seus usuários. Os usuários do Azure AD são mapeados automaticamente para os membros na aplicação.

## <a name="integrate-blockchain-with-applications"></a>Integrar o blockchain com os aplicações

Você pode usar as APIs REST do Blockchain Workbench e message-based APIs para integrar com sistemas existentes. As APIs oferecem uma interface que permite a substituição ou o uso de várias tecnologias de Ledger distribuído, armazenamento e banco de dados ofertados.

O Blockchain Workbench pode transformar as mensagens enviadas para a sua message-based API para criar transações em um formato esperado pela API nativa do que blockchain.  O Workbench pode entrar e rotear transações para o blockchain apropriado. 

O Workbench oferece automaticamente eventos para que o Barramento de Serviço e a Grade de Eventos enviem mensagens para clientes downstream. Os desenvolvedores podem se integrar a esses sistemas de mensagens para gerar transações e ver os resultados.

## <a name="deploy-a-blockchain-network"></a>Implantar uma rede de blockchain

O Azure Blockchain Workbench simplifica a configuração do Consortium Blockchain Network como solução pré-configurada com um modelo de solução do Azure Resource Manager. O modelo fornece uma implantação simplificada que implanta todos os componentes necessários para executar um consórcio. Atualmente, o Blockchain Workbench oferece suporte para Ethereum.

## <a name="use-active-directory-login"></a>Usar o logon do Active Directory

Com os protocolos de blockchain existentes, as identidades de blockchain são representadas como um endereço na rede. O Azure Blockchain Workbench abstrai a identidade do blockchain, associando-a a uma identidade do Active Directory, simplificando a criação de aplicativos empresariais com as identidades do Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Sincronizar dados on-chain com armazenamento off-chain

O Azure Blockchain Workbench facilita a análise de eventos e dados de blockchain, sincronizando automaticamente os dados no blockchain para o armazenamento off-chain. Em vez de extrair dados diretamente do blockchain, você pode consultar sistemas de banco de dados off-chain, como o SQL Server. Não é necessário conhecimento específico de Blockchain para usuários finais que estão executando tarefas de análise de dados.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Arquitetura do Blockchain Workbench](blockchain-workbench-architecture.md)

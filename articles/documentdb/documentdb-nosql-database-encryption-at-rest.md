---
title: "Criptografia de banco de dados em repouso – Azure Cosmos DB | Microsoft Docs"
description: "Saiba como o Azure Cosmos DB fornece criptografia padrão de todos os dados."
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Criptografia de banco de dados em repouso do Azure Cosmos DB

A criptografia em repouso é uma frase que normalmente se refere à criptografia de dados em dispositivos de armazenamento não volátil, como SSDs (Unidades de Estado Sólido) e HDDs (Discos Rígidos).  O Azure Cosmos DB armazena seus bancos de dados primários em SSD e seus anexos de mídia e backups nos Blobs do Azure, que geralmente têm suporte do HDD.  Com o lançamento da criptografia em repouso no Azure Cosmos DB, todos os bancos de dados, anexos de mídia e backups agora são criptografados.  Isso significa que seus dados agora são criptografados em trânsito (pela rede) e em repouso (armazenamento não volátil), fornecendo criptografia de ponta a ponta.

Como um serviço PaaS, trabalhamos intensamente para facilitar grande parte do uso do Azure Cosmos DB.  Levando isso em consideração, todos os dados do usuário armazenados no Azure Cosmos DB são criptografados em repouso e em transporte, sem que o usuário precise executar nenhuma ação.  Em outras palavras, a Criptografia em Repouso é "ativada" por padrão.  Não existem controles para ativá-lo ou desativá-la. Fornecemos esse recurso sem deixar de cumprir os nossos [SLAs de disponibilidade e desempenho](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>Como a criptografia em repouso funciona?

A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas seguros de armazenamento de chaves, redes criptografadas e APIs criptográficas.  O diagrama abaixo mostra como o armazenamento de dados criptografados e o gerenciamento de chaves são separados.  Os sistemas que descriptografam e processam dados precisam se comunicar com sistemas que gerenciam chaves.

![Diagrama de design](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

O fluxo básico de uma solicitação de usuário é este:
- A conta do banco de dados do usuário é preparada e as chaves de armazenamento são recuperadas por meio de uma solicitação ao RP (Provedor de Recursos) do Serviço de Gerenciamento.
- Um usuário cria uma conexão com o Azure Cosmos DB por meio de transporte seguro/HTTPS (os SDKs simplificam os detalhes).
- O usuário envia um Documento JSON para ser armazenado pela conexão segura criada anteriormente.
- O Documento JSON é indexado, a menos que o usuário tenha desativado a indexação.
- O Documento JSON e os Dados de Índice são gravados no armazenamento seguro.
- Periodicamente, os dados são lidos no armazenamento seguro e o backup é feito Repositório de Blobs Criptografados do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>P: Há algum custo adicional no Armazenamento do Azure se o SSE estiver habilitado?
R: Não há qualquer custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: Quem gerencia as chaves de criptografia?
R: As chaves são gerenciadas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: Com que frequência as chaves de criptografia são trocadas?
R: A Microsoft tem um conjunto de diretrizes internas, seguida pelo DocumentDB.  Embora as diretrizes específicas não sejam publicadas, a Microsoft publica o Security Development Lifecycle (também conhecido como SDL) [https://www.microsoft.com/sdl/default.aspx], que é visto como um subconjunto de diretrizes internas e que apresenta as melhores práticas úteis para desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso usar minhas próprias chaves de criptografia?
R: O Azure Cosmos DB é um serviço PaaS e trabalhamos intensamente para manter a facilidade de uso do serviço.  Observamos que essa pergunta geralmente é feita como uma pergunta substituta para o cumprimento de uma conformidade, como as normas PCI-DSS.  Como parte da criação desse recurso, trabalhamos com auditores de conformidade a fim de garantir que os clientes que usam o Azure Cosmos DB atendem a seus respectivos requisitos sem que eles mesmos precisem gerenciar chaves.
Por isso, no momento, oferecemos aos usuários a opção de responsabilizarem-se pelo gerenciamento de chaves.


### <a name="q-what-regions-have-encryption-turned-on"></a>P: Em quais regiões a criptografia está ativada?
R: A criptografia está ativada para todos os dados do usuário em todas as regiões do Azure Cosmos DB.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: A criptografia afeta os SLAs de latência de desempenho e produtividade?
R: Não há nenhum impacto ou alteração nos SLAs de desempenho agora que a criptografia em repouso está habilitada em todas as contas novas e existentes.  Leia mais na [página SLAs de desempenho](https://azure.microsoft.com/support/legal/sla/documentdb) para ver as últimas garantias.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: O emulador local dá suporte à criptografia em repouso?
R: O emulador é uma ferramenta de desenvolvimento/teste independente e não usa os serviços de gerenciamento de chaves utilizados pelo serviço gerenciado do DocumentDB. Nossa recomendação é habilitar o BitLocker em unidades em que você está armazenando dados confidenciais de teste do emulador. O (emulador dá suporte à alteração do diretório de dados padrão) [https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator], bem como ao uso de uma localização conhecida.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral da segurança do Azure Cosmos DB e as últimas melhorias, consulte [Segurança de banco de dados do Azure Cosmos DB](documentdb-nosql-database-security.md).

Para obter mais detalhes sobre as certificações da Microsoft, confira [Central de Confiabilidade do Azure](https://azure.microsoft.com/en-us/support/trust-center/).


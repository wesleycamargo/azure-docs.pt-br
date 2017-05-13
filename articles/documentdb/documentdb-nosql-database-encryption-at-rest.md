---
title: "Criptografia de banco de dados NoSQL em repouso – Azure DocumentDB | Microsoft Docs"
description: "Saiba como o Azure DocumentDB fornece criptografia padrão de todos os dados NoSQL."
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>Criptografia de banco de dados NoSQL em repouso do DocumentDB

A criptografia em repouso é uma frase que normalmente se refere à criptografia de dados em dispositivos de armazenamento não volátil, como SSDs (Unidades de Estado Sólido) e HDDs (Discos Rígidos).  O DocumentDB armazena seus bancos de dados primários em SSD e seus anexos de mídia e backups nos Blobs do Azure, que geralmente têm apoio do HDD.  Com o lançamento da criptografia em repouso para DocumentDB, todos os seus bancos de dados, anexos de mídia e backups agora são criptografados.  Isso significa que seus dados agora são criptografados em trânsito (pela rede) e em repouso (armazenamento não volátil), fornecendo criptografia de ponta a ponta.

Como um serviço PaaS, trabalhamos duro para facilitar bastante o uso do DocumentDB.  Levando isso em consideração, todos os dados do usuário armazenados no DocumentDB são criptografados em repouso e em transporte, sem que o usuário precise executar qualquer ação.  Em outras palavras, a Criptografia em Repouso é "ativada" por padrão.  Não existem controles para ativá-lo ou desativá-la. Fornecemos esse recurso sem deixar de cumprir os nossos [SLAs de disponibilidade e desempenho](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>Como a criptografia em repouso funciona?

A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas seguros de armazenamento de chaves, redes criptografadas e APIs criptográficas.  O diagrama abaixo mostra como o armazenamento de dados criptografados e o gerenciamento de chaves são separados.  Os sistemas que descriptografam e processam dados precisam se comunicar com sistemas que gerenciam chaves.

![Diagrama de design](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

O fluxo básico de uma solicitação de usuário é este:
- A conta do banco de dados do usuário é preparada e as chaves de armazenamento são recuperadas por meio de uma solicitação ao RP (Provedor de Recursos) do Serviço de Gerenciamento.
- Um usuário cria uma conexão com o DocumentDB por meio de transporte seguro/HTTPS (os SDKs simplificam os detalhes).
- O usuário envia um Documento JSON para ser armazenado pela conexão segura criada anteriormente.
- O Documento JSON é indexado, a menos que o usuário tenha desativado a indexação.
- O Documento JSON e os Dados de Índice são gravados no armazenamento seguro.
- Periodicamente, os dados são lidos no armazenamento seguro e o backup é feito Repositório de Blobs Criptografados do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>P: Há algum custo adicional no Armazenamento do Azure se o SSE estiver habilitado?
R: Não há qualquer custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: Quem gerencia as chaves de criptografia?
R: As chaves são gerenciadas pela Microsoft.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso usar minhas próprias chaves de criptografia?
R: O DocumentDB é um serviço PaaS e trabalhamos arduamente para manter a facilidade de uso do serviço.  Observamos que essa pergunta geralmente é feita como uma pergunta substituta para o cumprimento de uma conformidade, como as normas PCI-DSS.  Como parte da criação desse recurso, trabalhamos com auditores de conformidade a fim de garantir que os clientes que usam o DocumentDB atendam aos respectivos requisitos sem que eles mesmos precisem gerenciar chaves.
Por isso, no momento, oferecemos aos usuários a opção de responsabilizarem-se pelo gerenciamento de chaves.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: Em quais regiões a criptografia está ativada?
R: A criptografia está ativada para todos os dados do usuário em todas as regiões do DocumentDB.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral da segurança do DocumentDB e ver os aprimoramentos mais recentes, confira [Segurança de banco de dados NoSQL do DocumentDB](documentdb-nosql-database-security.md).

Para obter mais detalhes sobre as certificações da Microsoft, confira [Central de Confiabilidade do Azure](https://azure.microsoft.com/en-us/support/trust-center/).


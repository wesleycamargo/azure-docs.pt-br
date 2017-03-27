---
title: "O que é a API para MongoDB do DocumentDB? | Microsoft Docs"
description: "Saiba mais sobre a API para MongoDB do DocumentDB e como você pode executar facilmente aplicativos existentes do MongoDB na nuvem do Azure"
keywords: "o que é o MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f173fa709f2a7a21042752ba4b5ac936d01fe300
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>O que é a API para MongoDB do DocumentDB?

Os bancos de dados do Banco de Dados de Documentos agora podem ser usados como o repositório de dados para aplicativos escritos para o MongoDB. Isso significa que ao usar [drivers](https://docs.mongodb.org/ecosystem/drivers/) existentes para bancos de dados do MongoDB, seu aplicativo escrito para MongoDB agora pode se comunicar com o DocumentDB e usar os bancos de dados do DocumentDB em vez dos bancos de dados do MongoDB. Em muitos casos, é possível alternar o uso do MongoDB para DocumentDB, bastando alterar uma cadeia de conexão. Usando essa funcionalidade, os clientes podem criar e executar aplicativos de banco de dados do MongoDB facilmente na nuvem do Azure (aproveitando os bancos de dados NoSQL totalmente gerenciados e escalonáveis do DocumentDB) enquanto continuam usando habilidades e ferramentas conhecidas para MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>Qual é o benefício de usar a API para MongoDB do DocumentDB?
**Sem Gerenciamento de Servidor**: o DocumentDB é um serviço totalmente gerenciado, o que significa que você não precisa gerenciar infraestruturas ou Máquinas Virtuais. O DocumentDB está disponível em mais de 30 [Regiões do Azure](https://azure.microsoft.com/regions/services/).

**Escala ilimitada**: você pode escalar a produtividade e o armazenamento, de modo independente e elástica. É possível adicionar capacidade para atender a milhões de solicitações por segundo com facilidade.

**Nível empresarial**: o DocumentDB permite várias réplicas locais para proporcionar 99,99% de disponibilidade e proteção de dados em caso de falhas locais e regionais. O DocumentDB apresenta recursos de segurança e [certificações de conformidade](https://www.microsoft.com/trustcenter) de nível empresarial. 

**Compatibilidade do MongoDB** – a API para MongoDB do DocumentDB foi projetada para compatibilidade com o MongoDB. Você pode usar o código, os aplicativos, os drivers e as ferramentas existentes para trabalhar com o DocumentDB. 

Saiba mais nesse vídeo do Azure Friday com Scott Hanselman e o Gerenciador Principal de Engenharia do Document DB, Kirill Gavrylyuk.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>Como começar?
Crie uma conta da API para MongoDB do DocumentDB no [Portal do Azure](https://portal.azure.com) e troque a conexão para sua nova conta. 

*É só isso!*

Para obter instruções detalhadas, siga [criar conta](documentdb-create-mongodb-account.md) e [conectar-se à sua conta](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Próximas etapas

Informações sobre o DocumentDB: a API para MongoDB está integrada à documentação geral do DocumentDB, mas veja aqui algumas orientações para começar:
* Siga o tutorial [Conectar-se a uma conta do MongoDB](documentdb-connect-mongodb-account.md) para saber como obter informações sobre a cadeia de conexão da sua conta.
* Siga o tutorial [Usar o MongoChef com o DocumentDB](documentdb-mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados do DocumentDB e o aplicativo MongoDB no MongoChef.
* Siga o tutorial [Migrar dados para o DocumentDB com suporte de protocolo para o MongoDB](documentdb-mongodb-migrate.md) para importar seus dados em um banco de dados da API para MongoDB.
* Criar seu primeiro aplicativo da API para MongoDB usando [Node.js](documentdb-mongodb-samples.md).
* Criar seu primeiro aplicativo Web da API para MongoDB usando [.NET](documentdb-mongodb-application.md).
* Conectar-se a uma conta da API para MongoDB usando o [Robomongo](documentdb-mongodb-robomongo.md).
* Saiba quantos RUs suas operações estão usando com o [comando GetLastRequestStatistics e as métricas do Portal do Azure](documentdb-request-units.md#GetLastRequestStatistics).
* Saiba como [configurar preferências de leitura para aplicativos distribuídos globalmente](documentdb-distribute-data-globally.md#ReadPreferencesAPIforMongoDB).



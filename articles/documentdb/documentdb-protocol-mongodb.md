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
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 6c143a18883c99a24264d3174df7ec214d696fb5
ms.lasthandoff: 03/08/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>O que é a API para MongoDB do DocumentDB?

Os bancos de dados do Banco de Dados de Documentos agora podem ser usados como o repositório de dados para aplicativos escritos para o MongoDB. Isso significa que ao usar [drivers](https://docs.mongodb.org/ecosystem/drivers/) existentes para bancos de dados do MongoDB, seu aplicativo escrito para MongoDB agora pode se comunicar com o DocumentDB e usar os bancos de dados do DocumentDB em vez dos bancos de dados do MongoDB. Em muitos casos, é possível alternar o uso do MongoDB para DocumentDB, bastando alterar uma cadeia de conexão. Usando essa funcionalidade, os clientes podem criar e executar aplicativos de banco de dados do MongoDB facilmente na nuvem do Azure (aproveitando os bancos de dados NoSQL totalmente gerenciados e escalonáveis do DocumentDB) enquanto continuam usando habilidades e ferramentas conhecidas para MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>Qual é o benefício de usar a API para MongoDB do DocumentDB?
**Sem Gerenciamento de Servidor**: o DocumentDB é um serviço totalmente gerenciado, o que significa que você não precisa gerenciar infraestruturas ou Máquinas Virtuais. O DocumentDB está disponível em mais de 20 [Regiões do Azure](https://azure.microsoft.com/regions/services/).

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
* Siga o tutorial [Conectar-se a uma conta do MongoDB](documentdb-connect-mongodb-account.md) para saber como obter informações sobre a cadeia de conexão da sua conta.
* Siga o tutorial [Usar o MongoChef com o DocumentDB](documentdb-mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados do DocumentDB e o aplicativo MongoDB no MongoChef.
* Explore [amostras](documentdb-mongodb-samples.md) da API para MongoDB do DocumentDB.



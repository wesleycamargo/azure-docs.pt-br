---
title: "O que é o suporte de protocolo do Banco de Dados de Documentos para MongoDB? | Microsoft Docs"
description: "O que é o suporte de protocolo do Banco de Dados de Documentos para MongoDB? Ele permite usar o Azure DocumentDB, um serviço gerenciado baseado em nuvem, como o repositório de dados para aplicativos escritos para MongoDB."
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
ms.date: 01/16/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2de367287a4bee05694070dbeeb4d4a458d47f11
ms.openlocfilehash: 38d811a7a08a6f8a6dd07a29e9a49bb34810ee91


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>O que é o suporte de protocolo do Banco de Dados de Documentos para MongoDB?

Os bancos de dados do Banco de Dados de Documentos agora podem ser usados como o repositório de dados para aplicativos escritos para o MongoDB. Isso significa que ao usar [drivers](https://docs.mongodb.org/ecosystem/drivers/) existentes para bancos de dados do MongoDB, seu aplicativo escrito para MongoDB agora pode se comunicar com o DocumentDB e usar os bancos de dados do DocumentDB em vez dos bancos de dados do MongoDB. Em muitos casos, é possível alternar o uso do MongoDB para DocumentDB, bastando alterar uma cadeia de conexão. Usando essa funcionalidade, os clientes podem criar e executar aplicativos de banco de dados do MongoDB facilmente na nuvem do Azure (aproveitando os bancos de dados NoSQL totalmente gerenciados e escalonáveis do DocumentDB) enquanto continuam usando habilidades e ferramentas conhecidas para MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>Qual é o benefício de usar o suporte ao protocolo do DocumentDB para MongoDB?
**Sem Gerenciamento de Servidor**: o DocumentDB é um serviço totalmente gerenciado, o que significa que você não precisa gerenciar infraestruturas ou Máquinas Virtuais. O DocumentDB está disponível em mais de 20 [Regiões do Azure](https://azure.microsoft.com/regions/services/).

**Escala ilimitada**: você pode escalar a produtividade e o armazenamento, de modo independente e elástica. É possível adicionar capacidade para atender a milhões de solicitações por segundo com facilidade.

**Nível empresarial**: o DocumentDB permite várias réplicas locais para proporcionar 99,99% de disponibilidade e proteção de dados em caso de falhas locais e regionais. O DocumentDB apresenta recursos de segurança e [certificações de conformidade](https://www.microsoft.com/trustcenter) de nível empresarial. 

**Compatibilidade do MongoDB**: o suporte ao protocolo do DocumentDB para MongoDB foi projetado para compatibilidade com o MongoDB. Você pode usar o código, os aplicativos, os drivers e as ferramentas existentes para trabalhar com o DocumentDB. 

Saiba mais nesse vídeo do Azure Friday com Scott Hanselman e o Gerenciador Principal de Engenharia do Document DB, Kirill Gavrylyuk.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>Como começar?
Criar uma conta do DocumentDB com o suporte ao protocolo para MongoDB no [Portal do Azure](https://portal.azure.com) e troque a conexão para sua nova conta. 

*É só isso!*

Para obter instruções detalhadas, siga [criar conta](documentdb-create-mongodb-account.md) e [conectar-se à sua conta](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Próximas etapas
* Siga o tutorial [Criar uma conta do DocumentDB com suporte ao protocolo para MongoDB](documentdb-create-mongodb-account.md) para criar uma conta do DocumentDB.
* Siga o tutorial [Conectar-se a uma conta do DocumentDB com suporte ao protocolo para MongoDB](documentdb-connect-mongodb-account.md) para saber como obter informações sobre a cadeia de conexão da sua conta.
* Siga o tutorial [Usar o MongoChef com uma conta do DocumentDB com suporte ao protocolo para MongoDB](documentdb-mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados do DocumentDB e o aplicativo MongoDB no MongoChef.
* Explore [amostras](documentdb-mongodb-samples.md)do Banco de Dados de Documentos com suporte de protocolo para MongoDB.




<!--HONumber=Jan17_HO3-->



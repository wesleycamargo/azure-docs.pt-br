<properties 
	pageTitle="Suporte do protocolo do Banco de Dados de Documentos para o MongoDB | Microsoft Azure" 
	description="Saiba mais sobre o protocolo de suporte do Banco de Dados de Documentos para MongoDB, agora disponível na versão de visualização pública." 
	keywords="mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# Suporte do protocolo do Banco de Dados de Documentos para o MongoDB

## O que é o Banco de Dados de Documentos do Azure?
O Banco de Dados de Documentos do Azure é um serviço de banco de dados NoSQL totalmente gerenciado criado para fornecer desempenho rápido e previsível, alta disponibilidade, dimensionamento automático e facilidade de desenvolvimento. Seu modelo de dados flexível, suas latências baixas consistentes e seus recursos avançados de consulta o tornam uma ótima opção para aplicativos Web, móveis, de jogos e da IoT e muitos outros que precisam de escala contínua. Leia mais em [Introdução ao Banco de Dados de Documentos](documentdb-introduction.md).

## O que é o suporte de protocolo do Banco de Dados de Documentos para MongoDB?
Os bancos de dados do Banco de Dados de Documentos agora podem ser usados como o repositório de dados para aplicativos escritos para o MongoDB. Usando os [drivers](https://docs.mongodb.org/ecosystem/drivers/) existentes para MongoDB, os aplicativos podem se comunicar de modo fácil e transparente com o Banco de Dados de Documentos, em muitos casos simplesmente alterando uma cadeia de conexão. Usando essa funcionalidade de visualização, os clientes podem criar e executar aplicativos facilmente na nuvem do Azure (aproveitando os bancos de dados NoSQL totalmente gerenciados e escalonáveis) enquanto continuam usando habilidades e ferramentas conhecidas para MongoDB.

O protocolo de suporte do Banco de Dados de Documentos para MongoDB habilita as principais funções da API do MongoDB para operações CRUD (Criar, Ler, Atualizar e Excluir) dados, bem como para consultar o banco de dados. Os recursos atualmente implementados foram priorizados com base nas necessidades de plataformas, estruturas e ferramentas comuns, bem como pelos clientes em larga escala do MongoDB na avaliação do Azure para a respectiva plataforma de nuvem.
  

## Próximas etapas


- Saiba como [criar](documentdb-create-mongodb-account.md) uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
- Saiba como se [conectar](documentdb-connect-mongodb-account.md) a uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
- Saiba como [usar o MongoChef](documentdb-mongodb-mongochef.md) com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
- Explore [amostras](documentdb-mongodb-samples.md) do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

 

<!---HONumber=AcomDC_0824_2016-->
<properties 
	pageTitle="Introdução à camada Premium do Cache Redis do Azure" 
	description="Saiba como criar e gerenciar a Persistência do Redis, clustering do Redis e o suporte de VNET para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="sdanie"/>

# Introdução à camada Premium do Cache Redis do Azure
O Cache Redis do Azure é um cache distribuído e gerenciado que ajuda você a criar aplicativos altamente escalonáveis e responsivos, fornecendo acesso extremamente rápido aos seus dados.

A nova camada Premium é uma camada pronta para Empresas, que inclui todos os recursos da camada Standard e muito mais, como um melhor desempenho, cargas de trabalho maiores, recuperação de desastres e segurança avançada. Continue lendo para saber mais sobre os recursos adicionais da camada de cache Premium.

## Melhor desempenho em relação às camadas Standard ou Basic.
**Melhor desempenho em relação às camadas Standard ou Básica.** Os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às Camadas Standard ou Básica. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas.

**A taxa de transferência para o Cache do mesmo tamanho é maior na camada Premium quando comparada à camada Standard.** Por exemplo: para um Cache de 53 GB, a taxa de transferência de P4 (Premium) é de 250 mil solicitações por segundo quando comparada a 150 mil do C6 (Standard).

Consulte [Perguntas frequentes sobre o Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, taxa de transferência e largura de banda com caches premium.

## Persistência de dados do Redis
A camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Em caso de problemas de infraestrutura subjacente, pode haver uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache Redis do Azure oferece opções de RDB e AOF (em breve) na [persistência do Redis](http://redis.io/topics/persistence).

Para obter instruções sobre como configurar a persistência, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).

##Cluster Redis
Se desejar criar caches maiores que 53 GB ou fragmentar dados entre vários nós do Redis, você pode usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica gerenciado pelo Azure para alta disponibilidade.

**O clustering do Redis fornece máxima escala e produtividade.** A taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo: se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será de 250 mil *10 = 2,5 milhões de solicitações por segundo. Consulte [Perguntas frequentes sobre o Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, taxa de transferência e largura de banda com caches premium.

Para começar com o clustering, veja [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).

##Isolamento e segurança avançados
Os caches criados na camada Básica ou Standard podem ser acessados na Internet pública. O acesso ao Cache é restrito com base na tecla de acesso. Com a camada Premium, é possível garantir ainda mais que apenas os clientes em uma rede especificada possam acessar o Cache. É possível implantar o Cache Redis em uma [VNet (Rede Virtual) do Azure](https://azure.microsoft.com/services/virtual-network/). Você pode usar todos os recursos da VNet como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Redis.

Para saber mais, consulte [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

## Próximas etapas

Crie um cache e explore os novos recursos da camada premium.

-	[Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
-	[Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md)
-	[Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)
  

<!---HONumber=AcomDC_0504_2016-->
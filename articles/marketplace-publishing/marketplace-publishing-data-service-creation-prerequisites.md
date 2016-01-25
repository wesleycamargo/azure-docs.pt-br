<properties
   pageTitle="Pré-requisitos técnicos para criar um Serviço de Dados para o Marketplace | Microsoft Azure"
   description="Entender os requisitos para criar um Serviço de Dados para implantar e vender no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="hascipio; avikova" />

# Pré-requisitos técnicos para criar uma oferta do Serviço de Dados para o Azure Marketplace
Leia o processo completamente antes de começar e compreender onde e por que cada etapa é executada. O quanto for possível, você deve preparar informações da sua empresa e outros dados, baixar as ferramentas necessárias e criar componentes técnicos antes de iniciar o processo de criação de oferta.

Você deve ter os seguintes itens prontos antes de iniciar o processo:

## Tomar uma decisão sobre qual tecnologia será usada para publicar sua oferta do Serviço de Dados

Um Editor pode decidir entre várias tecnologias ao publicar o Serviço de Dados no Azure Marketplace. As principais tecnologias com suporte descritas abaixo. Sem levar em conta a tecnologia usada para publicar o Serviço de Dados, o usuário final consome dados por meio do **feed OData** expostos pelo Serviço do Azure Marketplace. Encontre informações completas sobre o serviço do OData em [http://www.odata.org/](http://www.odata.org/)

## Banco de Dados SQL do Azure

Ter o conjunto de dados pronto no SQL Azure é responsabilidade do Editor. Você precisará se inscrever no Azure, provisionar o tamanho apropriado do Banco de Dados e carregar seus Dados no Banco de Dados SQL do Azure. O Editor também é responsável por manter seus dados sempre atualizados. Encontre mais informações sobre como assinar os Serviços do Azure em [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Ao mover os dados no SQL Azure, o Azure Marketplace pode expor tabelas e exibições. O Editor pode especificar quais tabelas/exibições e colunas são expostas ao usuário final. Além disso, o provedor de conteúdo também pode especificar quais colunas podem ser consultadas pelo usuário final e quais só são retornadas na carga. Isso fornece um alto nível de flexibilidade sobre quais dados no banco de dados devem ser expostos. As colunas que podem ser consultadas precisam ser incluídas no backup por um ou mais índices de banco de dados.

## Serviço Web baseado em REST

Protocolo com suporte: **somente HTTPS**

Os serviços existentes baseados em REST podem ser expostos por meio do Azure Marketplace. Como o conjunto de dados sempre é exposto ao usuário final como um feed OData, o serviço do Azure Marketplace precisa ter a capacidade de mapear o serviço para um serviço baseado no OData. Para fazer isso, os pontos de extremidade baseados em REST precisam expor todos os parâmetros como parâmetros HTTP.

A carga precisa estar em um formato que pode ser mapeado para uma resposta ATOM. Portanto, a resposta dos serviços precisa estar no formato XML e só pode conter somente um elemento repetição que contém os valores de carga (como um conjunto de registros). O serviço do Azure Marketplace mapeará o nó de repetição para o nó de entrada no ATOM e os valores de carga nos nós de propriedade no nó de entrada.

As informações de autorização (como a chave de API, token de autenticação, etc.) devem ser fornecidas como um parâmetro de HTTP ou no cabeçalho HTTP (par de valor de chave) – também há suporte para a autenticação básica. Uma chave válida deve ser fornecida e todas as solicitações feitas por meio do Azure Marketplace estão sendo feitas por meio dessa chave. O monitoramento de usuário e a cobrança ocorrem na camada do Azure Marketplace.

Os erros retornados pelo serviço precisam ser mapeados para os códigos de status HTTP. Caso o serviço retorne um XML que contém o erro, isso será mapeado pelo serviço do Azure Marketplace para os códigos de status HTTP.

## Serviços Web baseados em SOAP

Protocolo: **somente HTTPS**

Os requisitos são os mesmos que os descritos na seção do serviço baseado em REST. A única diferença é que os parâmetros também podem ser fornecidos em um corpo XML que está sendo lançado para o serviço do Editor a cada solicitação feita por meio do Azure Marketplace. Isso significa que os parâmetros HTTP fornecidos pelo usuário no front-end estão sendo convertidos em elementos XML de um documento XML que está sendo postado com a solicitação ao serviço Web do provedor de conteúdo.

## Serviços Web baseados em OData

Protocolo: **somente HTTPS**

Os dados podem ser expostos como um serviço do OData no Azure Marketplace. O sistema transmitirá o serviço e substituirá a raiz do serviço pela raiz do serviço do Azure Marketplace – para garantir que todas as chamadas subsequentes passem pelo Azure Marketplace.

Os serviços do OData não precisam passar apenas em um banco de dados no back-end. O OData dá suporte a qualquer tipo de armazenamento ou lógica de negócios para orientar o serviço.


## Próximas etapas
Agora que você examinou os pré-requisitos e concluiu as tarefas necessárias, continue com a criação de sua oferta do Serviço de Dados, como detalhado no [Guia de publicação do serviço de dados](marketplace-publishing-data-service-creation.md).

Ou então, se quiser examinar o processo geral e os respectivos artigos de cada uma das fases de publicação, veja o artigo [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=AcomDC_0114_2016-->
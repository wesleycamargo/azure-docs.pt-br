<properties 
	pageTitle="Como usar o CDN ‒ guia de recursos do Azure" 
	description="Saiba como usar a CDN (Rede de Distribuição de Conteúdo) do Azure para fornecer conteúdo com alta largura de banda armazenando em cache blobs e conteúdo estático." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="mazha"/>


# Usando a CDN para Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure é o bloco de construção fundamental para dimensionar qualquer aplicativo HTTP no Azure. Ele oferece aos clientes do Azure uma solução global armazenando em cache e entregando conteúdo próximo aos usuários finais. Como resultado, em vez de acessar a origem toda vez, as solicitações de usuário são inteligentemente roteadas para os POPs (pontos de presença) de borda da CDN com melhor desempenho. Isso aumenta significativamente o desempenho e a experiência do usuário. Para obter uma lista atual dos locais de nós da CDN, consulte [Locais de nó da CDN do Azure].

Os benefícios do uso da CDN para armazenar dados do Azure em cache incluem:

-   Melhor desempenho e experiência de usuário para usuários finais que estão longe de uma fonte de conteúdo e estão usando aplicativos onde muitas 'viagens de internet' são necessárias para carregar conteúdo
-   Grande escala distribuída para lidar melhor com alta carga instantânea, digamos, no início de um evento, como o lançamento de um produto
-   Ao distribuir solicitações de usuário e entregar conteúdo por meio de POPs de borda globais, menos tráfego é enviado para a origem, aliviando a carga sobre ela.

Clientes atuais da CDN agora podem usar a CDN do Azure no [Portal de Gerenciamento do Azure]. A CDN é um recurso suplementar à sua assinatura e tem um [plano de faturamento] separado.

##Etapa 1: criar uma origem de CDN no Azure

A origem da CDN é o local por meio do qual a CDN busca conteúdo e armazena-o em cache nos POPs de borda. A origem integrada do Azure inclui Azure Apps, Serviços de Nuvem, Armazenamento e Serviços de Mídia.

## Etapa 2: criar um ponto de extremidade CDN apontando para sua origem do Azure

Depois que a origem é configurada, ela estará disponível na lista de origens quando você [criar um novo ponto de extremidade da CDN](cdn-create-new-endpoint.md).

> [AZURE.NOTE]A configuração criada para o ponto de extremidade não estará imediatamente disponível. Pode levar até 60 minutos para o registro se propagar pela rede CDN. Os usuários que tentarem usar imediatamente o nome de domínio CDN poderão receber o código de status 400 (Solicitação Incorreta) até que o conteúdo esteja disponível por meio da CDN.

## Etapa 3: definir sua configuração da CDN 

Você pode habilitar uma série de recursos para o ponto de extremidade da CDN, como política de armazenamento em cache, caching de cadeia de caracteres de consulta, etc.

## Etapa 4: acessar conteúdo da CDN

Para acessar o conteúdo armazenado em cache na CDN, utilize a URL da CDN fornecida no portal. Por exemplo, o endereço de um blob armazenado em cache será semelhante ao seguinte: http://<*NamespaceDaCDN*>.vo.msecnd.net/<*MeuContêinerPúblico*>/<*NomeDoBlob*>



## Consulte também

[Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure](cdn-overview.md)
 

<!---HONumber=62-->
<properties 
	pageTitle="Operações comuns na API de recomendações de aprendizado de máquina | Azure" 
	description="Aplicativo de exemplo de recomendação do Azure ML" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="jaymathe"/> 


# Operações comuns na API de recomendações de aprendizado de máquina

##Finalidade

Este documento mostra o uso de algumas das APIs de recomendação de AM do Azure por meio de um aplicativo de exemplo.

Este aplicativo não deve incluir a funcionalidade completa ou usar todas as APIs, mas apenas demonstrar algumas das operações comuns a executar quando você quiser experimentar inicialmente as possibilidades do serviço de recomendação de AM do Azure. 

##Introdução à Recomendação

Recomendação por meio do serviço de recomendação de AM do Azure é habilitada quando você compila um modelo de recomendação com base nos seguintes dados:

* Um repositório do item que você deseja recomendar, também conhecido como um catálogo
* Dados que representam o uso de itens por usuário/sessão (eles podem ser adquiridos ao longo do tempo por meio da aquisição de dados, não fazem parte do aplicativo de amostra)

Depois que um modelo de recomendação for compilado, é possível usá-lo para prever itens para um usuário de acordo com um conjunto de itens (pode ser um único) que ele selecionar.

Para possibilitar o cenário acima, realize a seguinte operação no serviço de recomendação de AM do Azure:

* Criar um modelo - é um contêiner lógico que conterá os dados (catálogo e uso) e os modelos de previsão. Cada contêiner de modelo é identificado por um id exclusivo alocado no momento da compilação. Esse id, chamado de id do modelo, é usado pela maioria das APIs 
* Catálogo de carregamento - depois de compilar um contêiner do modelo, é possível associar um catálogo a ele

Observação: As etapas acima ('Compilar modelo' e 'Carregar catálogo') normalmente são executadas uma vez durante o ciclo de vida do modelo.

* Carregar uso - para adicionar dados de uso ao contêiner do modelo.
* Compilar modelo de recomendação - depois de ter dados suficientes, você disparará a compilação do modelo de recomendação. Essa operação usará os melhores algoritmos de aprendizado de máquina para compilar um modelo de recomendação. Cada compilação está associada um id único, e você precisará manter esse id, já que ele é necessário para a funcionalidade de algumas APIs.
* Processo de compilação de monitor - uma criação de modelo de recomendação é uma operação assíncrona e pode levar de alguns minutos a várias horas, dependendo da quantidade de dados (catálogo e uso) e dos parâmetros de compilação. Portanto, você precisará monitorar a compilação. Um modelo de recomendação é compilado apenas se a criação associada terminar com sucesso.
* (Opcional) Escolher uma compilação de modelo de recomendação ativa. Essa etapa é necessária apenas se você tiver mais de um modelo de recomendação integrado ao seu contêiner do modelo. Qualquer solicitação para obter a recomendação sem indicar a criação ativa (modelo de recomendação ativo) será redirecionada automaticamente pelo sistema para a compilação ativa padrão. 

Observação: Uma compilação ativa (modelo de recomendação) está pronta para produção e é criada para a carga de trabalho de produção em oposição a um modelo de recomendação não ativo, que permanece em um ambiente semelhante ao de teste (às vezes chamado de preparo)

* Obter a recomendação - quando você tiver um modelo de recomendação, é possível disparar a recomendação para um único item ou uma lista de itens escolhidos. 

Você normalmente invocará Obter Recomendação por um determinado período de tempo, enquanto isso, pode redirecionar dados de uso para o sistema de recomendação de AM do Azure, que adicionará esse uso ao contêiner do modelo especificado. Depois de ter dados de uso suficientes, é possível disparar uma nova compilação de modelo de recomendação para usar os dados mais reais. 

##Pré-requisitos

* Visual Studio 2013
* Acesso à Internet 

##Solução de aplicativo de exemplo de AM do Azure

A solução contém o código-fonte, arquivo de catálogo e de uso de exemplo e diretivas para baixar os pacotes nuget necessários para compilação.

##A API usada

O aplicativo usa apenas um pequeno subconjunto da funcionalidade de recomendação de AM do Azure por meio de um subconjunto da API disponível. As seguintes APIs são demonstradas no aplicativo:

* Compilar modelo - crie o contêiner lógico para conter dados e modelos de recomendação. Um modelo é identificado por um nome, um usuário não pode criar um modelo de duas vezes com o mesmo nome.
* Carregar arquivo de catálogo - para carregar dados do catálogo
* Carregar arquivo de uso - para carregar dados de uso
* Disparar compilação - para criar um modelo de recomendação
* Monitorar a execução de compilação - para monitorar o status de uma compilação de modelo de recomendação
* Escolher um modelo criado para a recomendação - para indicar qual modelo de recomendação usar por padrão para um determinado contêiner do modelo. Essa etapa é necessária apenas se você tiver mais de um modelo de recomendação e desejar uma compilação inativa como compilação ativa.
* Obter recomendação - para recuperar o item recomendado de acordo com um determinado item único ou um conjunto de itens. 

Para uma descrição completa da API, consulte a documentação do Microsoft Azure Marketplace. 

Observação: Um modelo pode ter várias compilações ao longo do tempo (não simultaneamente), sendo que cada compilação é criada com o mesmo catálogo ou o catálogo atualizado e dados de uso adicionais.

## Armadilhas comuns

* Você precisa fornecer seu nome de usuário e sua chave de conta principal do Microsoft Azure Marketplace como uma linha de comando para executar a amostra
* Executar o aplicativo de amostra consecutivamente falhará - o identificador de fluxo do aplicativo processa a criação, o carregamento e a compilação de monitorar e obter recomendação de um modelo de nome predefinido, portanto, falhará em execução consecutiva se você não alterar o nome do modelo entre as chamadas
* Recomendação pode retornar sem dados - o aplicativo de amostra usa um arquivo de uso e catálogo muito pequeno, portanto, o modelo de recomendação criado não é significativo e, como resultado, algum item do catálogo não terá itens recomendados.

## Isenção de responsabilidade
O aplicativo de amostra não se destina a ser executado para produção, os dados fornecidos no catálogo e uso são muito pequenos e não fornecem um modelo de recomendação significativo, sendo fornecidos apenas para demonstração. 

## Legal
Este documento é fornecido "no estado em que se encontra". Informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. 
Alguns exemplos aqui representados são fornecidos somente para fins de ilustração e são fictícios. Nenhuma associação ou conexão real é intencional ou deve ser inferida. 
Este documento não fornece a você nenhum direito legal a qualquer propriedade intelectual de qualquer produto da Microsoft. Você pode copiar e usar este documento para fins de consulta interna. 
(c) 2014 Microsoft. Todos os direitos reservados. 


<!--HONumber=46--> 

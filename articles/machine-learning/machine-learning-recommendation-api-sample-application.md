<properties 
	pageTitle="Operações comuns na API de recomendações de Aprendizado de Máquina | Microsoft Azure" 
	description="Aplicativo de exemplo de recomendação do Azure ML" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="luisca"/>


# Operações comuns na API de recomendações de aprendizado de máquina

##Finalidade

Este documento mostra o uso da API de Recomendações do Aprendizado de Máquina do Azure por meio de um [aplicativo de exemplo](http://1drv.ms/1xeO2F3).

Esse aplicativo não deve incluir a funcionalidade completa nem usa todas as APIs. Ele demonstra algumas operações comuns para executar quando você quiser experimentar o serviço de recomendação do Aprendizado de Máquina pela primeira vez.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Introdução ao serviço de recomendação do Aprendizado de Máquina

As recomendações por meio do serviço de recomendação do Aprendizado de Máquina são habilitadas quando você compila um modelo de recomendação com base nos seguintes dados:

* Um repositório dos itens que você deseja recomendar, também conhecido como um catálogo
* Dados que representam o uso de itens por usuário ou sessão (eles podem ser adquiridos ao longo do tempo por meio da aquisição de dados, não como parte do aplicativo de exemplo)

Depois que um modelo de recomendação é compilado, você pode usá-lo para prever itens que um usuário pode achar interessante, de acordo com um conjunto de itens (ou um único item) que o usuário seleciona.

Para habilitar o cenário anterior, faça o seguinte no serviço de recomendação do Aprendizado de Máquina:

* Crie um modelo: esse é um contêiner lógico que mantém os dados (catálogo e uso) e o(s) modelo(s) de previsão. Cada contêiner do modelo é identificado por uma ID exclusiva, que é alocada quando ele é criado. Essa ID é chamada de ID de modelo e é usada pela maioria das APIs. 
* Carregue no catálogo: quando um contêiner de modelo é criado, você pode associar um catálogo a ele.

**Observação**: as etapas para criar um modelo e carregar no catálogo geralmente são executadas uma vez para o ciclo de vida do modelo.

* Carregar uso: isso adiciona dados de uso ao contêiner do modelo.
* Compile um modelo de recomendação: depois que tiver dados suficientes, você poderá criar o modelo de recomendação. Essa operação usará os principais algoritmos do Aprendizado de Máquina para criar um modelo de recomendação. Cada compilação está associada a uma ID exclusiva. Você precisa manter um registro dessa ID porque ela é necessária para a funcionalidade de algumas APIs.
* Monitore o processo de compilação: uma compilação de modelo de recomendação é uma operação assíncrona e pode levar de alguns minutos a várias horas, dependendo da quantidade de dados (catálogo e uso) e dos parâmetros de compilação. Portanto, você precisará monitorar a compilação. Um modelo de recomendação será criado apenas se sua compilação associada for concluída com êxito.
* (Opcional) Escolha uma compilação de modelo de recomendação ativa. Essa etapa será necessária apenas se você tiver mais de um modelo de recomendação integrado a seu contêiner do modelo. Qualquer solicitação para obter recomendações sem indicar o modelo de recomendação ativo será redirecionada automaticamente pelo sistema para a compilação ativa padrão. 

**Observação**: um modelo de recomendação ativo está pronto para produção e foi projetado para cargas de trabalho de produção. Isso difere de um modelo de recomendação inativo, que permanece em um ambiente de teste (às vezes chamado de preparo).

* Obtenha recomendações: depois que tiver um modelo de recomendação, você poderá disparar recomendações para um único item ou uma lista de itens selecionados. 

Você normalmente invocará Obter Recomendação por determinado período de tempo. Durante esse período de tempo, você pode redirecionar dados de uso para o sistema de recomendação do Aprendizado de Máquina, que adiciona esses dados ao contêiner do modelo especificado. Quando tiver dados de uso suficientes, você poderá criar um novo modelo de recomendação que incorpora os dados de uso adicionais.

##Pré-requisitos

* Visual Studio 2013
* Acesso à Internet 

##Solução de aplicativo de exemplo do Aprendizado de Máquina do Azure

Essa solução contém o código-fonte, um exemplo de uso, o arquivo de catálogo e diretivas para baixar os pacotes que são necessários para a compilação.

##As APIs usadas

O aplicativo usa a funcionalidade de recomendação do Aprendizado de Máquina por meio de um subconjunto de APIs disponíveis. As seguintes APIs são demonstradas no aplicativo:

* Criar o modelo: crie um contêiner lógico para armazenar dados e modelos de recomendação. Um modelo é identificado por um nome, e você não pode criar mais de um modelo com o mesmo nome.
* Carregar o arquivo de catálogo: use para carregar dados de catálogo.
* Carregar arquivo de uso: use para carregar dados de uso.
* Disparar compilação: use para criar um modelo de recomendação.
* Monitorar a execução de compilação: use para monitorar o status de uma compilação de modelo de recomendação.
* Escolher um modelo criado para a recomendação: use para indicar qual modelo de recomendação usar por padrão para determinado contêiner do modelo. Essa etapa será necessária apenas se você tiver mais de um modelo de recomendação e desejar ativar uma compilação não ativa como o modelo de recomendação ativo.
* Obter recomendação: use para recuperar o item recomendado de acordo com determinado item único ou um conjunto de itens. 

Para obter uma descrição completa das APIs, consulte a documentação do Microsoft Azure Marketplace.

**Observação**: um modelo pode ter várias compilações ao longo do tempo (não simultaneamente). Cada compilação é criada com o mesmo catálogo ou um catálogo atualizado e dados de uso adicionais.

## Armadilhas comuns

* Você precisa fornecer seu nome de usuário e sua chave de conta principal do Microsoft Azure Marketplace para executar o aplicativo de exemplo.
* A execução consecutiva do aplicativo de exemplo falhará. O fluxo de aplicativo inclui criar, carregar, compilar o monitor e obter recomendações de um modelo predefinido. Portanto, haverá falha na execução consecutiva se você não alterar o nome do modelo entre invocações.
* Recomendações podem ser retornadas sem dados. O aplicativo de exemplo usa um arquivo de catálogo e uso muito pequeno. Portanto, alguns itens do catálogo não terão itens recomendados.

## Isenção de responsabilidade
O aplicativo de exemplo não se destina a ser executado em um ambiente de produção. Os dados fornecidos no catálogo são muito pequenos e não fornecerão um modelo de recomendação significativo. Os dados são fornecidos como uma demonstração.
 

<!---HONumber=AcomDC_1203_2015-->
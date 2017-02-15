---
title: "Criando um modelo com a interface do usuário de Recomendações | Microsoft Docs"
description: "Recomendações do Azure Machine Learning – criando um modelo com a interface do usuário de Recomendações"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 74cba2d29dec0c302fb969371f7b8fa58e4e4603


---
# <a name="building-a-model-with-the-recommendations-ui"></a>Criando um modelo com a interface do usuário de Recomendações
Este documento é um guia passo a passo. Nosso objetivo é conduzir você pelas etapas necessárias para treinar um modelo usando a [interface do usuário de Recomendações](https://recommendations-portal.azurewebsites.net/).
Percorrer o exercício permite compreender o processo para criar um modelo antes que você faça isso programaticamente. Ele também familiariza você com a interface do usuário, que é útil ao começar a usar o serviço.

Este exercício leva cerca de 30 minutos.

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Etapa 1 – Entrar na interface do usuário de Recomendações
1. Se não tiver feito isso, você precisará [inscrever-se](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para uma nova assinatura da [API de recomendações](https://www.microsoft.com/cognitive-services/en-us/recommendations-api). Você pode se inscrever no serviço no **Azure** em [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) e entrar com sua conta do Azure. Instruções detalhadas sobre o processo de inscrição são fornecidas na *Tarefa 1* do [Guia de Introdução](cognitive-services-recommendations-quick-start.md) 
2. Depois que tiver obtido uma **Chave** para sua assinatura de API de Recomendações, vá para [interface do usuário de Recomendações](https://recommendations-portal.azurewebsites.net/). 
3. Faça logon portal do inserindo sua chave no campo **Chave de Conta** e, em seguida, clique no botão **Logon**.
   
    ![Interface do usuário de Recomendações: caixa de diálogo Entrar.][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>Etapa 2 – Vamos reunir alguns dados de treinamento
Antes que você possa criar um build, o mecanismo precisa de duas informações: um arquivo de catálogo e um conjunto de arquivos de uso. 

O arquivo de catálogo contém informações sobre os itens oferecidos ao cliente. Um arquivo de uso contém informações sobre como esses itens são usados ou as transações de sua empresa.

Normalmente, você consultaria seu banco de dados de repositório para obter essas informações. Hoje, nós fornecemos alguns dados de exemplo para você para que você possa aprender como usar a API de Recomendações.

Você pode baixar os dados de [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copie e descompacte **Books.Zip** em uma pasta no computador local. Por exemplo, **c:\data**.

Informações detalhadas sobre o esquema dos arquivos de catálogo e uso podem ser encontradas no artigo [Collecting Data To Train your Model](cognitive-services-recommendations-collecting-data.md) (Coletando Dados para Treinar seu Modelo).

Para este exercício, trabalharemos com um pequeno arquivo para que você não precise esperar muito tempo pelo treinamento. Se você quiser tentar um arquivo mais realista, também colocamos **MsStoreData.zip**, que contém exemplos de transações da Microsoft Store na [mesma localização](http://aka.ms/RecoSampleData).

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Etapa 3 – Criar um projeto e carregar dados de uso e catálogo
Após fazer logon na [interface do usuário de Recomendações](https://recommendations-portal.azurewebsites.net/), você vê a página de projetos. Se você tiver criado algum projeto anteriormente, você deverá vê-lo aqui.
Um projeto (também conhecido como *um modelo de* na referência de API) é um contêiner para os dados de catálogo e uso. Você pode criar vários *builds* dentro do projeto. Guiaremos você pelo processo nas etapas a seguir.

1. Para criar um novo projeto, digite o nome na caixa de texto (algo como "MyFirstModel" funcionaria) e clique em **Adicionar Projeto**.
   
    ![Interface do usuário de Recomendações: página Projetos.][reco_projects]
2. Quando o projeto é criado, clique no botão **Procurar Arquivo** na seção **Adicionar um Arquivo de Catálogo**. 
   Carregue o catálogo que você obteve na etapa 2. Se você o tiver salvo em *c:\data*, você precisará navegar até essa pasta.
   
     ![Interface do usuário de Recomendações: adicionando dados a um projeto.][reco_firstmodel]
3. Depois que o catálogo for carregado, clique no botão **Procurar Arquivo** na seção **Adicionar Arquivos de Uso**. Adicione o arquivo usage_large.txt.

> **E se eu tiver um arquivo grande de dados de uso?**
> 
> Apenas arquivos de uso menores que 200 MB podem ser carregados. Dito isso, o sistema pode conter até 2 GB de dados de transação, portanto, você pode carregar mais de um arquivo se necessário.
> Talvez não seja necessária uma quantidade tão grande de dados para gerar um bom modelo; não é apenas o tamanho dos dados que importa, mas sua qualidade. É comum ver dados de uso em que a maioria das transações são apenas em alguns itens populares e há "pouca sinalização" para outros itens.
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>Etapa 4 – Vamos fazer algum treinamento!
Agora que você carregou o catálogo e os dados de uso, estamos prontos para treinar o mecanismo para que ele possa aprender os padrões de nossos dados.

1. Clique no botão **Novo Build**.
2. Selecione **Recomendações** como o tipo de build. Observe que também damos suporte aos tipos de build conhecidos como Build de Classificação e FBT (Frequentemente Comprados Juntos).
   
   ![Interface do usuário de Recomendações: caixa de diálogo Compilar.][reco_build_dialog.png]

    Um build FBT permite identificar padrões para os produtos que são geralmente comprados e consumidos na mesma transação.
    Um build de classificação é usada para identificar recursos de interesse. 
    Não nos aprofundaremos muito em FBT ou builds de classificação neste workshop, mas se estiver interessado, você deverá conferir a [página de documentação Tipos de build e qualidade do modelo](cognitive-services-recommendations-buildtypes.md).

1. Clique no botão **Compilar**. Se você estiver usando os dados dos Manuais, o processo de build levará cerca de cinco minutos. Ele é mais demorado em conjuntos de dados grandes.

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Etapa 5 – Vamos descobrir o que o computador aprendeu!
Quando a compilação estiver concluída, você observará um novo build na lista de builds. Esse build pode ser consultado para obter recomendações de item e de usuário.

1. Quando o build estiver concluído, clique em **Pontuação**. Isso permite que você brinque com o modelo e veja quais itens são recomendados.
   
    ![Interface do usuário de Recomendações: botão Pontuação][reco_score_button]
2. Selecione um item para ver quais itens são retornados como recomendações para aquele item. Observe que, se não houver transações suficientes para prever uma recomendação para um determinado item, o sistema não retornará nenhuma recomendação para aquele item.  Se por algum motivo você tiver um item que não retorna nenhuma recomendação, tente definir a pontuação de outros itens.

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>Etapa 6 – Próximas etapas
Parabéns! Você treinou um modelo e então obteve recomendações desse modelo.  A interface do Usuário de Recomendações é uma ferramenta útil que permite que você veja o estado de seus projetos e builds. 

Agora que você tem um modelo, talvez queira saber como realizar todas as etapas acima de forma programática. Para saber chamar a API de forma programática, convidamos você a conferir [Referência da API de Recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) e baixar o [Aplicativo de Exemplo de Recomendações](http://go.microsoft.com/fwlink/?LinkID=759344).

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Nov16_HO3-->



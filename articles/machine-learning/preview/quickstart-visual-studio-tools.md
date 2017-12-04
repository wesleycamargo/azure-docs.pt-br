---
title: "Artigo de início rápido para Ferramentas do Visual Studio para o Machine Learning no Azure | Microsoft Docs"
description: "Este artigo descreve como começar a usar as Ferramentas do Visual Studio para Machine Learning, desde a criação de um experimento e o treinamento de um modelo até a operacionalização de um serviço Web."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: get-started-article
ms.date: 11/15/2017
ms.openlocfilehash: 582ec5babf2bac34f20d4e9c7517f78ee2002e0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="visual-studio-tools-for-ai"></a>Ferramentas do Visual Studio para IA
Ferramentas do Visual Studio para IA é uma extensão de desenvolvimento para criar, testar e implantar soluções de IA e de aprendizagem profunda. Ele apresenta uma integração perfeita com o Azure Machine Learning, especialmente uma exibição de histórico de execução, detalhando o desempenho de métricas personalizadas e treinamentos anteriores. Ele oferece uma exibição de gerenciador de amostras, que permite procurar e inicializar um novo projeto com o [Kit de Ferramentas Cognitivas Microsoft (anteriormente conhecido como CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) e outras estruturas de aprendizagem profunda. Por fim, ele fornece um gerenciador para destinos de computação, o que habilita você a enviar trabalhos para treinar modelos em ambientes remotos como Máquinas Virtuais do Azure ou servidores Linux com GPU. Ele também fornece um acesso facilitado a [IA de Lote do Azure (versão prévia)](https://docs.microsoft.com/en-us/azure/batch-ai/).
 
## <a name="getting-started"></a>Introdução 
Para começar, será necessário baixar e instalar o [Visual Studio](https://www.visualstudio.com/downloads/). Depois que você tiver aberto o Visual Studio, execute as etapas a seguir:
1. Clique na barra de menus no Visual Studio e selecione "Extensões e Atualizações..."
2. Clique na guia "Online" e selecione "Pesquisar no Visual Studio Marketplace".
3. Pesquise por "Visual Studio para IA". 
3. Clique no botão **Baixar** . 
4. Após a instalação, reinicie o Visual Studio. 

Após o recarregamento do Visual Studio, a extensão será ativada. [Saiba mais sobre como localizar extensões](hhttps://docs.microsoft.com/en-us/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> As Ferramentas do Visual Studio para IA precisa do Visual Studio 2015 ou 2017, edição profissional ou corporativa. Não há suporte à versão do OSX da Apple. 


## <a name="exploring-project-samples"></a>Explorando amostras de projeto
As Ferramentas do Visual Studio para IA acompanham um gerenciador de amostras. O gerenciador de amostras torna fácil descobrir amostras e testá-las com apenas alguns cliques. Para abrir o gerenciador, faça o seguinte:   
1. Na barra de menus, clique em **Ferramentas de IA**.
2. Clique em "Galeria do Azure Machine Learning".

Uma guia com todos os Exemplos de ML do Azure é aberta.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Criar um novo projeto do gerenciador de amostras 
Você pode procurar diferentes amostras e obter mais informações sobre elas. Procuraremos a amostra "Classificando a Íris" até localizá-la. Para criar um novo projeto com base nessa amostra, faça o seguinte:
1. Clique no botão **Instalar** no exemplo de projeto, isso abrirá uma nova caixa de diálogo. 
2. Selecione um grupo de recursos, uma conta e um espaço de trabalho.
3. Deixe o tipo de projeto como Geral.
4. Insira um caminho de projeto e um nome de projeto e, depois pressione enter. 
5. Uma caixa de diálogo é aberta solicitando a gravação de uma solução. Clique em Salvar. 

Uma vez concluído, um novo projeto é aberto em uma nova instância do Visual Studio. 

> [!TIP]
> Você precisa estar conectado para acessar o recurso do Azure. Do terminal inserido, digite "az login" e siga as instruções. 

## <a name="submitting-experiment-with-the-new-project"></a>Enviando um experimento com o novo projeto
Se o novo projeto estiver aberto no Visual Studio, poderemos enviar um trabalho para um destino de computação (local e VM com Docker).
Para enviar o trabalho, faça o seguinte: 
1. No gerenciador de soluções, clique com botão direito no arquivo que você deseja enviar e selecione **Definir como Arquivo de Inicialização**.
2. Selecione o nome do projeto, clique com o botão direito e selecione **Enviar Trabalho...**
3. Uma nova caixa de diálogo será aberta, permitindo que você escolha o cluster (ou o destino de computação) para executar o script.
4. Clique em **Enviar**

Depois que o trabalho é enviado, o terminal inserido exibe o progresso das execuções.

## <a name="view-list-of-jobs"></a>Exibir lista de trabalhos
Após o envio do trabalho, você poderá listar os trabalhos no histórico de execuções.
1. Em **Gerenciador de Servidores**, clique em **Ferramentas de IA**.
2. Depois, selecione **Azure Machine Learning**
3. Clique no menu **Trabalhos**.

O Gerenciador de trabalho lista todos os experimentos enviados para esse projeto. 

## <a name="view-job-details"></a>Exibir detalhes do trabalho
Com a exibição do Explorador de trabalho aberta, clique na primeira execução na lista.
Isso carregará o painel Resumo do Trabalho e o painel de Logs e Saídas.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Como configurar o Azure Machine Learning para funcionar com um IDE](./how-to-configure-your-IDE.md)

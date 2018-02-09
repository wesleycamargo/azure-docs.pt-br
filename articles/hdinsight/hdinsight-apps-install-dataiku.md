---
title: "Instalar aplicativo publicado – Dataiku DDS – HDInsight do Azure | Microsoft Docs"
description: Instale e use o aplicativo de Hadoop de terceiros do Dataiku DDS.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 835f433e0bf79e8bc4d9b30963196f65bc53cd0e
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---dataiku-dds"></a>Instalar aplicativo publicado – Dataiku DDS

Este artigo descreve como instalar e executar o aplicativo de Hadoop publicado do [Dataiku DDS](https://www.dataiku.com/) no HDInsight do Azure. Para obter uma visão geral da plataforma do aplicativo HDInsight e uma lista dos aplicativos publicados de Independent Software Vendor (ISV), consulte [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md). Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>Sobre DSS do Dataiku

O [Studio de ciência de dados (DSS)](https://www.dataiku.com/dss/features/connectivity/) do Dataiku é uma plataforma de ciência de dados de colaboração que permite aos cientistas de dados compilarem e fornecerem soluções analíticas. Oferecer o DSS como um aplicativo do HDInsight permite que você use ciência de dados para compilar soluções Big Data e executá-las a escala e nível corporativo.

Você pode usar o DSS para implementar uma solução de análise completa, começando com a preparação, processamento e ingestão de dados. Uma solução DSS também pode incluir treinamento e aplicação de modelos de aprendizado de máquina, visualização e, em então, operacionalização.

Você pode instalar p DSS no HDInsight usando clusters Hadoop ou Spark. Você pode instalar o DSS em clusters em execução existentes ou ao criar novos clusters. O DSS também oferece suporte ao uso de armazenamento de Blobs do Azure como um conector para leitura de dados.

Você pode usar o DSS para compilar projetos, que então podem gerar trabalhos do MapReduce ou Spark. Esses trabalhos são executados como trabalhos normais do MapReduce ou Spark no HDInsight, para que você possa dimensionar o cluster sob demanda.

## <a name="prerequisites"></a>pré-requisitos

Para instalar este aplicativo em um novo cluster do HDInsight ou em um cluster existente, você deve ter a seguinte configuração:

* Camada(s) do cluster: Standard, Premium
* Tipo(s) de cluster: Hadoop, Spark
* Versão do cluster: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Instalar o aplicativo publicado DSS do Dataiku

Para obter instruções passo a passo sobre como instalar esse e outros aplicativos ISV disponíveis, leia [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Inicializar o DSS do Dataiku

1. Após a instalação, você pode inicializar DSS do seu cluster no portal do Azure acessando o painel **Configurações** e, em seguida, clicando em **Aplicativos** na categoria **Geral**. O painel de Aplicativos Instalados lista todos os aplicativos instalados.

    ![Aplicativo DSS do Dataiku instalado](./media/hdinsight-apps-install-dataiku/app.png)

2. Ao selecionar o DSS no HDInsight, você vê um link para a página da web e o caminho do ponto de extremidade SSH. Selecione o link de página da Web.

3. Na primeira inicialização, você verá um formulário para criar uma nova conta do Dataiku gratuitamente ou para entrar em uma conta existente. Você também tem a opção de iniciar uma avaliação gratuita de 2 semanas de [Enterprise Edition](https://www.dataiku.com/dss/editions/). A partir desse ponto, você tem a opção de continuar com a inserção de uma chave de licença para o Enterprise Edition ou a usar o Community Edition.

4. Após concluir sua opção de licença selecionada, você verá um formulário de logon. Insira as credenciais padrão exibidas antes do formulário de logon.

As etapas a seguir fornecem uma demonstração simples.

1. [Baixe o CSV de ordens de exemplo](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. No painel DSS, selecione o link **+** (Novo projeto) no menu esquerdo para criar um novo projeto.

    ![Link do novo projeto](./media/hdinsight-apps-install-dataiku/new-project.png)

3. No formulário do novo projeto, digite um **Nome**. A **Chave do projeto** é preenchida automaticamente com um valor sugerido. Nesse caso, digite "Ordens". Clique em **CRIAR**.

    ![Novo formulário do projeto](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Selecione **+ IMPORTAR SEU PRIMEIRO CONJUNTO DE DADOS** em sua nova página do projeto.

    ![Carregar arquivos](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Selecione **Carregar seus arquivos** na lista do conjunto de dados de **Arquivos**. Você verá a caixa de diálogo de carregamento. Clique em Adicionar um arquivo, selecione o arquivo `haiku_shirt_sales.csv` que você baixou e valide-o.

6. O arquivo é carregado no DSS. Verifique se o DSS detectou o formato CSV corretamente clicando no botão de versão prévia.

    ![Carregar arquivos](./media/hdinsight-apps-install-dataiku/preview.png)

7. A importação está quase perfeita. O arquivo CSV está usando um separador de guias. Você pode ver que os dados estão em um formato tabular, com colunas chamadas de recursos e as linhas que representam as observações. O erro é que, aparentemente, o arquivo continha uma linha em branco entre o cabeçalho e os dados. Para corrigir esse erro, digite `1` no campo **Ignorar as próximas linhas**.

    ![Salvar](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Nomeie o novo conjunto de dados. Digite **haiku_shirt_sales** no campo na parte superior da tela, e selecione o botão **Criar**.

9. Você vê uma exibição tabular dos seus dados onde você pode começar a explorá-los. Para cada coluna, você deve ver que o Estúdio de Ciência do Dataiku detectou um tipo de dados em _azul_ - neste caso, Texto, Número ou Data (não analisado). Um medidor indica que o índice da coluna para a qual os valores parecem não corresponde ao tipo (em vermelho) ou estão ausentes (em branco). Neste conjunto de dados de exemplo, o departamento tem valores vazios e dados inválidos.

    ![Exibição tabular](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulação de dados

Os dados do mundo real quase sempre são confusos, e raramente ficam devidamente separados em pacotes. Normalmente, limpar os dados exige uma cadeia de scripts e lógica de negócios associada. O DSS do Dataiku fornece uma ferramenta **Laboratório** para tornar essa tarefa mais intuitiva.

1. Clique em **Laboratório** no canto superior direito.

    ![Botão do Laboratório](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. A janela do Laboratório abre. O laboratório é onde você trabalha iterativamente em seu conjunto de dados para adentrá-lo. Este tutorial demonstra o aspecto de análise Visual. Selecione o botão **Novo** abaixo da análise Visual. Você precisará especificar um nome para sua análise. Deixe o nome padrão por enquanto, e clique em **CRIAR**.

    ![Criar Laboratório](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Selecione o botão **Estatísticas de colunas rápidas** no canto superior direito da página.

    ![Estatísticas de colunas rápidas](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Você vê as estatísticas de tipos de dados e os valores exibidos em gráficos com base em linha do tempo no painel **Exibição de colunas rápidas**.

    ![Exibição de colunas rápidas](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Agora você pode explorar o DSS usando os dados de exemplo. Você pode limpar e trabalhar com os dados, e criar novas visualizações.

Para obter tutoriais detalhados, leia [Conheça o DSS do Dataiku](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Próximas etapas

* [Documentação do DSS do Dataiku](https://doc.dataiku.com/dss/latest/).
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar clusters HDInsight, e testar e hospedar aplicativos HDInsight.

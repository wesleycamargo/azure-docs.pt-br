---
title: "Instalar aplicativo publicado – Datameer – HDInsight do Azure | Microsoft Docs"
description: Instale e use o aplicativo de Hadoop de terceiros da Datameer.
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
ms.openlocfilehash: 8a898b4f82cf2d7e05e8c3895e5eddd8cf02b173
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---datameer"></a>Instalar aplicativo publicado – Datameer

Este artigo descreve como instalar e executar o aplicativo de Hadoop publicado da [Datameer](https://www.datameer.com/) no HDInsight do Azure. Para obter uma visão geral da plataforma do aplicativo HDInsight e uma lista dos aplicativos publicados de Independent Software Vendor (ISV), consulte [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md). Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>Sobre o Datameer

O Datameer é um aplicativo nativo para a plataforma de Hadoop, que estende os recursos existentes de HDInsight do Azure e fornece rápida integração, preparação e análise de dados estruturados e não estruturados. O Datameer pode acessar mais de 70 fontes e formatos: estruturados, semi-estruturados e não estruturados. Você pode carregar dados diretamente ou usar os links exclusivos de dados para efetuar pull de dados sob demanda. A funcionalidade de autoatendimento do Datameer e a interface familiar de planilha reduz a complexidade da tecnologia de Big Data e acelera o tempo de insight. A interface de planilha fornece um mecanismo simples para inserir fórmulas declarativas que são, então, traduzidas para a otimização de trabalhos do Hadoop. Com o Datameer, o business intelligence (BI) e habilidades de Excel, você pode usar o Hadoop na nuvem rapidamente. Para obter mais informações, consulte a [documentação do Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>pré-requisitos

Para instalar este aplicativo em um novo cluster do HDInsight ou em um cluster existente, você deve ter a seguinte configuração:

* Camada de cluster: Padrão
* Tipo de cluster: Hadoop
* Versão do cluster: 3.4

## <a name="install-the-datameer-published-application"></a>Instalar o aplicativo publicado do Datameer

Para obter instruções passo a passo sobre como instalar esse e outros aplicativos ISV disponíveis, leia [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Inicializar o Datameer

1. Após a instalação, você pode inicializar o Datameer do seu cluster no portal do Azure acessando o painel **Configurações** e, em seguida, clicando em **Aplicativos** na categoria **Geral**. O painel de Aplicativos Instalados lista todos os aplicativos instalados.

    ![Aplicativo Datameer instalado](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Ao selecionar o Datameer, você vê um link para a página da web e o caminho do ponto de extremidade SSH. Selecione o link de página da Web.

3. Na primeira inicialização, há duas opções de licença: uma avaliação gratuita de 14 dias, ou ativar uma licença existente.

    ![Opções de licença](./media/hdinsight-apps-install-datameer/license.png)

4. Após concluir sua opção de licença selecionada, você verá um formulário de logon. Insira as credenciais padrão exibidas antes do formulário de logon. Após efetuar logon, aceite o contrato de software para continuar.

    ![Logon](./media/hdinsight-apps-install-datameer/login.png)

As etapas a seguir mostram uma demonstração de "Olá, Mundo".

1. [Faça o download do CSV de amostra](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Clique no símbolo **+** na parte superior do painel do Datameer e, em seguida, clique em **Upload de arquivos**.

    ![Upload de arquivos](./media/hdinsight-apps-install-datameer/upload.png)

3. Na caixa de diálogo de upload, procure e selecione o arquivo **Hello World.csv** que você baixou. Verifique se o **Tipo de Arquivo** está definido como CSV/TSV. Selecione **Avançar**. Continue clicando em **Avançar** até atingir o final do assistente.

    ![Upload de arquivos](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Nomeie o arquivo **Olá, Mundo** sob uma Nova Pasta. Renomeie a nova pasta como "Demonstração". Selecione **Salvar**.

    ![Salvar](./media/hdinsight-apps-install-datameer/save.png)

5. Clique no símbolo **+** mais uma vez e selecione **Pasta de trabalho** para criar uma nova pasta de trabalho para os dados.

    ![Adicionar pasta de trabalho](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Expanda a pasta **Dados**, **FileUploads**, em seguida, a pasta **Demonstração** que você criou ao salvar o arquivo "Olá, Mundo". Selecione o formulário **Olá, Mundo** na lista de arquivos, em seguida, clique em **Adicionar Dados**.

    ![Salvar](./media/hdinsight-apps-install-datameer/select-file.png)

7. Você vê os dados carregados em uma interface de planilha. Para selecionar um subconjunto dos dados, selecione o botão **Filtro** na barra de ferramentas.

    ![Botão Filtrar](./media/hdinsight-apps-install-datameer/filter-button.png)

8. Na caixa de diálogo Aplicar filtro, selecione a coluna **Cidade**, o operador **Igual a** e digite **Chicago** na caixa de texto do filtro. Verifique a caixa de seleção **Criar filtro na nova planilha**, em seguida, selecione **Criar Filtro**.

    ![Aplicar Filtro](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Salve a pasta de trabalho clicando em **Arquivo**, em seguida, em **Salvar**. Forneça um nome, como "Pasta de Trabalho Olá, Mundo".

10. Você verá opções para como e quando executar a Pasta de trabalho. Por enquanto, deixe todas as opções com seus valores padrão e verifique o **Iniciar o processo de cálculo imediatamente após salvar** e selecione **Salvar**.

    ![Salvar pasta de trabalho](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. O Datameer fornece ferramentas de visualização poderosas. Para exibir os dados, crie um Infográfico. Selecione o símbolo **+** na parte superior do painel de controle, em seguida, selecione **Infográfico**.

    ![Adicionar Infográfico](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Arraste um widget de Gráfico de Barras na lista de widgets à esquerda, conforme mostrado na etapa 1 no diagrama a seguir. Em seguida, navegue pela pasta Dados no navegador de dados à direita, expanda a Pasta de trabalho, em seguida, a planilha adicionada com o filtro (etapa 2). Arraste a coluna **Nome** na parte superior do gráfico de barras e solte no destino **Rótulo** para definir a coluna Nome da pasta de trabalho como o campo de rótulo do gráfico (etapa 3).

    ![Infográfico](./media/hdinsight-apps-install-datameer/infographic.png)

13. Para definir Idade como o eixo de Y do gráfico, arraste a coluna **Idade** para o campo **Dados** do gráfico.

    ![Infográfico](./media/hdinsight-apps-install-datameer/infographic-age.png)

Parabéns! Você criou uma visualização dos dados sem gravar nenhum código. Agora você pode explorar variações e visualizações adicionais.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar clusters HDInsight, e testar e hospedar aplicativos HDInsight.

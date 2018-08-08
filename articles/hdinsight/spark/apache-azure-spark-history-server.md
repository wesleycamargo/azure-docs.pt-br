---
title: Uso estendido de servidor de histórico do Spark para depurar e diagnosticar aplicativos do Spark – Azure HDInsight | Microsoft Docs
description: Uso estendido de servidor de histórico do Spark para depurar e diagnosticar aplicativos do Spark – Azure HDInsight.
services: hdinsight
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: ''
ms.topic: article
ms.date: 07/12/2018
ms.author: jejiang
ms.openlocfilehash: 61e857b01f75cb30e3d213703bd8ee8014d902d6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309491"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Uso estendido de servidor de histórico do Spark para depurar e diagnosticar aplicativos Spark

Este artigo fornece orientação sobre como usar o extended servidor de histórico do Spark para depurar e diagnosticar aplicativos do Spark concluídos e em execução. A extensão atualmente inclui guias de dados e gráfico. Na guia dados, os usuários podem verificar os dados de entrada e saída do trabalho do Spark. Na guia gráfico, os usuários podem verificar o fluxo de dados e repetir o grafo de trabalho.

## <a name="open-the-spark-history-server"></a>Abrir o servidor de histórico do Spark

O Servidor de Histórico do Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Para abrir a interface do usuário da Web de servidor de histórico do Spark no portal do Azure

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Em **Links Rápidos**, clique em **Painel do Cluster** e, em seguida, em **Servidor de Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster Spark. 

    ![Spark History Server](./media/apache-azure-spark-history-server/launch-history-server.png "Spark History Server")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>Para abrir o servidor Web IU do histórico Spark pela URL
Substituir de abrir o servidor de histórico do Spark, navegando até a URL a seguir, <ClusterName> com o nome do cluster Spark do cliente.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

A interface do usuário da Web do Servidor de Histórico do Spark tem esta aparência:

![Servidor de Histórico do HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Abra a guia de dados do servidor de histórico do Spark
Selecione a ID do trabalho e clique em **dados** no menu de ferramenta para obter a exibição de dados.

+ Verifique as **entradas**, **saídas**, e **operações de tabela** selecionando as guias separadamente.

    ![Guias de dados](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Copiar todas as linhas clicando no botão **cópia**.

    ![Cópia de dados](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Salvar todos os dados como arquivo CSV clicando no botão **csv**.

    ![Salvar Dados](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Pesquisa digitando palavras-chave no campo **pesquisa**, o resultado da pesquisa será exibida imediatamente.

    ![Pesquisa de dados](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Clique no cabeçalho de coluna para classificar a tabela, clique no sinal de adição para expandir uma linha para mostrar mais detalhes ou clique no sinal de menos para recolher uma linha.

    ![Tabela de dados](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Baixar um único arquivo clicando no botão **Download parcial** que coloque à direita, em seguida, o arquivo selecionado será baixado para o local, se o arquivo não existir mais, ele abrirá uma nova guia para mostrar as mensagens de erro.

    ![Linha de download de dados](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiar caminho completo ou relativo, selecionando o **Copiar caminho completo**, **Copiar caminho relativo** que se expande de menu download. Para arquivos do armazenamento do azure data lake **aberto no Gerenciador de armazenamento do Azure** iniciará o Gerenciador de armazenamento do Azure e localizar para a pasta quando entrar.

    ![Caminho de cópia de dados](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Clique em navegar o número abaixo da tabela de páginas quando muito muitas linhas para exibir em uma única página. 

    ![Página de dados](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Passe o mouse sobre o ponto de interrogação ao lado de dados para mostrar a dica de ferramenta, ou clique no ponto de interrogação para obter mais informações.

    ![Mais informações de dados](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Enviar comentários com problemas clicando **fornecer comentários**.

    ![comentários de gráfico](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Abra a guia de dados do servidor de histórico do Spark
Selecione o ID do trabalho e clique em **dados** no menu de ferramenta para obter a exibição de dados.

+ Verifique a visão geral do seu trabalho, o grafo de trabalho gerado. 

+ Por padrão, ele mostrará todos os trabalhos, e ele pode ser filtrado por **ID do trabalho**.

    ![ID do trabalho de gráfico](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Por padrão, **Progresso** é selecionado, o usuário pode verificar o fluxo de dados selecionando **Leitura / Escrita** na lista suspensa de **Exibição**.

    ![exibição de gráfico](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    A exibição do nó de gráfico na cor que mostra o mapa de calor.

    ![gráfico de mapa de calor](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Reproduzir o trabalho clicando no botão **reprodução** e parar a qualquer momento clicando no botão Parar. A exibição de tarefas na cor para mostrar o status diferentes quando a reprodução:

    + Verde para bem-sucedido: O trabalho foi concluído com êxito.
    + Laranja para repetida: instâncias das tarefas que falharam, mas não afetam o resultado final do trabalho. Essas tarefas tinham duplicar ou repita instâncias que podem ter êxito posteriormente.
    + Vermelho para falha: A tarefa falhou.
    + Azul para execução: A tarefa está em execução.
    + Aguardando ou em branco para ignorada: A tarefa está esperando para ser executado ou o estágio foi ignorada.

    ![amostra de cor do gráfico, em execução](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![amostra de cor do gráfico, com falha](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > Reprodução para cada trabalho é permitida. Quando um trabalho não tem qualquer estágio ou não tiver concluído, não há suporte para a reprodução.


+ Rola o mouse para aplicar zoom de entrada/saída grafo do trabalho, ou clique em **Aplicar Zoom para ajustar** para torná-lo a ajustar à tela.
 
    ![Aplicar zoom de gráfico para ajustar](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Passe o mouse no nó de gráfico para ver a dica de ferramenta quando há falha de tarefas e clique no Palco para abrir a página de estágio.

    ![Dica de ferramenta do gráfico](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ O nó de gráfico do trabalho exibirá as informações a seguir de cada estágio:
    + ID.
    + Nome ou descrição.
    + Número total de tarefas.
    + Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.
    + Gravação de dados: a soma do tamanho de saída e em ordem aleatória de tamanho de gravação.
    + Tempo de execução: o tempo entre a hora de início da primeira tentativa e tempo de conclusão da última tentativa.
    + Contagem de linhas: a soma dos registros de entrada, registros de saída, em ordem aleatória de registros de leitura e gravação registros em ordem aleatória.
    + Progresso.

    > [!NOTE]
    > Por padrão, o nó de gráfico do trabalho exibirá informações da última tentativa de cada estágio (com exceção de tempo de execução do estágio), mas durante o gráfico de reprodução de nó mostrará informações de cada tentativa.

    > [!NOTE]
    > Para o tamanho dos dados de leitura e gravação que podemos usar 1 MB = 1000 KB = 1000 * 1000 Bytes.

+ Enviar comentários com problemas clicando **fornecer comentários**.

    ![comentários de gráfico](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>Perguntas frequentes

### <a name="1-revert-to-community-version"></a>1. Reverter para versão de comunidade

Para reverter para a versão de comunidade, execute as seguintes etapas:

1. Cluster aberto no Ambari. Clique em **Spark2** no painel esquerdo.
2. Clique na guia **configurações**.
3. Expanda o grupo **spark2-defaults personalizados**.
4. Clique em **adicionar propriedade**, adicione **spark.ui.enhancement.enabled=false**, salvar.
5. A propriedade define **falsos** agora.
6. Clique em **Salvar** para salvar a configuração.

    ![desativa o recurso](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Clique em **Spark2** no painel à esquerda, em **resumo** , clique em **servidor de histórico de Spark2**.

    ![reiniciar servidor1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Reinicie o servidor de histórico clicando **reinicie** de **Spark2 History Server**.

    ![reiniciar servidor2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Atualizar a web de servidor de histórico do Spark da interface do usuário, ele será revertido para a versão da comunidade.

### <a name="2-upload-history-server-event"></a>2. Carregar eventos de servidor de histórico

Se você encontrar erros de servidor de histórico, siga as etapas para fornecer o evento:
1. Baixe o evento clicando **baixar** na web da interface do usuário do servidor de histórico.

    ![evento de download](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Clique em **fornecer comentários** da guia de dados/gráfico.

    ![comentários de gráfico](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Forneça o título e descrição do erro, arraste o arquivo zip para o campo de edição, e clique em **enviar novo problema**.

    ![problema de arquivo](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Atualizar o arquivo jar para o cenário de hotfix

Se você quiser atualizar com o hotfix, use o script a seguir, que atualizará enhancement.jar* spark.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Uso**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Exemplo**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**Para usar o arquivo de bash do portal do Azure**

1. Inicie [Portal do Azure](https://ms.portal.azure.com)e selecione seu cluster.
2. Clique em **ações de Script**, em seguida, **enviar novo**. Conclua o **enviar ação de script** de formulário e, em seguida, clique no botão **criar**.
    
    + Para **Tipo de Script**, selecione **Personalizar**.
    + **Nome**: Especifique um nome de script.
    + **URI do script bash**: Carregue o arquivo de bash no cluster privado, em seguida, copie a URL aqui. Como alternativa, use o URI fornecido.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Verificar **Cabeçalho** e **trabalho**.
    + **Parâmetros**: definir o parâmetros seguem o uso do bash.

    ![carregar log ou atualização hotfix](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>Problema conhecido

1.  Atualmente, ele só funciona para o cluster Spark 2.3.

2.  Dados de entrada/saída usando o RDD não serão exibidos na guia de dados.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos para um cluster Spark no HDInsight](apache-spark-resource-manager.md)
* [Definir configurações do Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Fale conosco

Se você tiver comentários ou se você encontrar problemas ao usar essa ferramenta, envie um email em ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
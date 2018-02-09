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
ms.openlocfilehash: 4b83f2a2228ef0dd7fa56b5a71b267d1e4302620
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Instalar aplicativo publicado – Plataforma de Aplicativo de Dados de Cask (CDAP)

Este artigo descreve como instalar e executar o aplicativo de Hadoop publicado da [CDAP](http://cask.co/products/cdap/) no HDInsight do Azure. Para obter uma visão geral da plataforma do aplicativo HDInsight e uma lista dos aplicativos publicados de Independent Software Vendor (ISV), consulte [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md). Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>Sobre a CDAP

O desenvolvimento de aplicativos no Hadoop pode ser desafiador.  Há um número grande e crescente de extensões de tecnologia do Hadoop, que podem levar algum tempo para serem integradas. Monitorar o fluxo de dados e coletar métricas pode exigir a criação de uma solução separada.

### <a name="how-does-cdap-help"></a>Como a CDAP ajuda?

A Plataforma de Aplicativo de Dados de Cask (CDAP) é uma plataforma de integração para Big Data. A CDAP permite que você se concentre na criação de aplicativos e não na infraestrutura subjacente.

A CDAP usa conceitos e abstrações de alto nível que são familiares para desenvolvedores. Essas abstrações ocultam as complexidades de sistemas internos e incentivam a reutilização de soluções.

Uma extensão da CDAP chamada [Cask Hydrator](http://cask.co/products/hydrator/) fornece uma interface de usuário para desenvolver e gerenciar os pipelines de dados. Um pipeline de dados é composto por vários *plug-ins que executam tarefas como aquisição de dados, transformação, análise e operações de pós-execução.

Cada plug-in da CDAP tem uma interface bem definida para que tecnologias diferentes de avaliação sejam apenas uma questão de substituição de um plug-in por outro, sem precisar tocar o restante do aplicativo.

Os *pipelines* da CDAP fornecem um fluxo ilustrativo de alto nível dos dados em seu aplicativo. Esta visualização mostra o fluxo de ponta a ponta dos dados de ingestão, por meio de transformações e análises de dados e, finalmente, em um armazenamento de dados externo.

O exemplo a seguir de um pipeline de dados ingere dados do twitter em tempo real, em seguida, filtra alguns tweets com base em critérios predefinidos. O pipeline de dados transforma dados brutos de tweets e projetos em um formato mais legível, em seguida, agrupa os tweets de acordo com um conjunto de valores e grava os resultados em um armazenamento HBase.

![Pipeline da CDAP](./media/hdinsight-apps-install-cask/pipeline.png)

Esse pipeline de ponta a ponta é criado usando a **IU do Cask Hydrator**, usando sua interface de plug-in e funcionalidade de arrastar e soltar para formem conexões entre cada estágio. Você pode isolar e modificar a funcionalidade de cada plug-in de forma independente. Usando a CDAP, pipelines semelhantes podem ser criados e validados em horas. No mundo Hadoop típico, construir essas soluções pode levar vários dias.

A CDAP também fornece uma extensão chamada [Cask Tracker](http://cask.co/products/tracker/) para visualmente rastrear dados conforme eles fluem por meio do aplicativo. O Cask Tracker adiciona *governança de dados* no sistema de forma que os ativos de dados sejam gerenciados formalmente em todo o aplicativo. Você pode rastrear a linhagem de cada ponto de dados, coletar métricas relevantes e auditar a trilha de dados ao longo do processo.

Aqui está uma ilustração de como os dados estão fluindo no pipeline acima:

![Rastreador da CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>pré-requisitos

Para instalar este aplicativo em um novo cluster do HDInsight ou em um cluster existente, você deve ter a seguinte configuração:

* Camada de cluster: Padrão
* Tipo de cluster: HBase
* Versão do cluster: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Instalar o aplicativo publicado da CDAP

Para obter instruções passo a passo sobre como instalar esse e outros aplicativos ISV disponíveis, leia [Instalar aplicativos de Hadoop de terceiros](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Inicializar CDAP

1. Após a instalação, inicialize a CDAP do seu cluster no portal do Azure acessando o painel **Configurações** e, em seguida, selecionando **Aplicativos** na categoria **Geral**. O painel Aplicativos Instalados lista todos os aplicativos instalados.

    ![Aplicativo da CDAP instalado](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Ao selecionar a CDAP, você vê um link para a página da Web e o Ponto de Extremidade HTTP, além do caminho do ponto de extremidade SSH. Selecione o link de página da Web.

3. Quando solicitado, insira suas credenciais de administrador do cluster.

    ![Autenticação](./media/hdinsight-apps-install-cask/auth.png)

4. Depois de entrar, você deve ver a página inicial da GUI do Cask CDAP.

    ![Página inicial da GUI do Cask](./media/hdinsight-apps-install-cask/gui.png)

5. Para explorar a interface da CDAP, clique no link do menu **Cask Market** na parte superior da página.

    ![Link do Cask Market](./media/hdinsight-apps-install-cask/cask-market.png)

6. Selecione **Amostra do log de acesso** na lista.

    ![Amostra de log de acesso](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Clique em **Carregar** para confirmar.

    ![Clicar em Carregar](./media/hdinsight-apps-install-cask/market-load.png)

8. Uma exibição dos dados de amostra é exibida. Selecione **Avançar**.

    ![Amostra de log de acesso – Exibir dados](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Selecione **Fluxo de dados** como o Tipo de Destino, digite um Nome de Destino e selecione **Concluir**.

    ![Amostra de log de acesso – Selecionar destino](./media/hdinsight-apps-install-cask/market-destination.png)

10. Depois que o pacote de dados tiver sido carregado com êxito, selecione **Exibir detalhes do fluxo de dados**.

    ![Pacote de dados carregado com êxito](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Para habilitar os metadados para o namespace, selecione **Habilitar** na guia Uso na página de detalhes de Log de acesso.

    ![Amostra de log de acesso – habilitar metadados](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Depois que os metadados estiverem habilitados, você verá um gráfico exibindo informações de mensagens de auditoria.

    ![Amostra de log de acesso – Metadados habilitados](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Para explorar os dados de log, selecione o ícone **Explorar** na parte superior da página.

    ![Amostra de log de acesso – Explorar](./media/hdinsight-apps-install-cask/log-explore.png)

14. Você verá uma amostra de consulta SQL. Fique à vontade para modificá-la conforme desejado, em seguida, selecione **Executar**.

    ![Amostra de log de acesso – Explorar o conjunto de dados com uma consulta](./media/hdinsight-apps-install-cask/log-query.png)

15. Após a conclusão da consulta, selecione o ícone **Exibição** na coluna Ações.

    ![Amostra de log de acesso – Exibir consulta concluída](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Você verá os resultados da consulta.

    ![Amostra de log de acesso – Resultados da consulta](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Cask](http://cask.co/resources/documentation/).
* [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar clusters HDInsight, e testar e hospedar aplicativos HDInsight.

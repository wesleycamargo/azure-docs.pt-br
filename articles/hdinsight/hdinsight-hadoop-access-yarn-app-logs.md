---
title: Acesso aos logs de aplicativo YARN no Hadoop de forma programática – Azure
description: Acessar logs do aplicativo programaticamente em um cluster de Hadoop no HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: e92f9f7bb49b0b7cc33c73a9c5eb2d0ca7532592
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094394"
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Acessar logs de aplicativo YARN no HDInsight baseado em Windows
Este documento explica como acessar os logs para aplicativos YARN que terminaram em um cluster Hadoop baseado no Windows no Microsoft Azure HDInsight

> [!IMPORTANT]
> As informações contidas neste documento são aplicadas apenas aos clusters HDInsight baseados no Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para obter informações sobre os logs de YARN nos clusters HDInsight baseados em Linux, consulte [Acessar logs do aplicativo YARN no Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Pré-requisitos
* Um cluster HDInsight baseado no Windows.  Confira [Crie clusters Hadoop baseados no Windows no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>Servidor de linha do tempo do YARN
O <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de linha do tempo do YARN</a> fornece informações genéricas sobre aplicativos concluídos, bem como informações de aplicativo específicas da estrutura, por meio de duas interfaces diferentes. Especificamente:

* O armazenamento e a recuperação de informações do aplicativo genérico em clusters HDInsight estão habilitados na versão 3.1.1.374 ou superior.
* O componente de informações específicas do framework de aplicativo do servidor do cronograma não está atualmente disponível em clusters HDInsight.

As informações genéricas sobre aplicativos incluem os seguintes tipos de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

Nos clusters HDInsight essas informações são armazenadas pelo Azure Resource Manager. As informações são salvas no armazenamento de histórico no armazenamento padrão do cluster. Esses dados genéricos em aplicativos concluídos podem ser recuperados por meio de uma API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Aplicativos e logs YARN
O YARN dá suporte a vários modelos de programação, desacoplando o gerenciamento de recursos do monitoramento/agendamento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

* Cada aplicativo pode consistir em várias *tentativas do aplicativo*. 
* Contêineres são concedidos a uma tentativa específica de um aplicativo. 
* Um contêiner fornece o contexto para uma unidade básica de trabalho. 
* O trabalho realizado no contexto de um contêiner é executado no único nó de trabalho ao qual o contêiner foi alocado. 

Para obter mais informações, consulte [Conceitos YARN][YARN-concepts].

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma ótima estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso [Agregação de Logs][log-aggregation]. O recurso Agregação de Logs torna o acesso aos logs de aplicativos mais determinista, uma vez que agrega os logs de todos os contêineres em um nó de trabalho e os armazena como um arquivo de log agregado por nó de trabalho no sistema de arquivos padrão após o encerramento de um aplicativo. O aplicativo pode usar centenas ou milhares de contêineres, mas os logs de todos os contêineres que executam em um único nó de trabalhador são agregados em um único arquivo, resultando em um arquivo por nó de trabalho usado pelo aplicativo. A Agregação de Logs está habilitada por padrão nos clusters HDInsight (versão 3.0 ou superior) e os logs agregados se encontram no contêiner padrão de seu cluster no seguinte local:

    wasb:///app-logs/<user>/logs/<applicationId>

Nesse local, *user* é o nome do usuário que iniciou o aplicativo e *applicationId* é o identificador exclusivo de um aplicativo, conforme atribuído pelo RM do YARN.

Os logs agregados não são diretamente legíveis, já que são gravados em um [TFile][T-file], [formato binário][binary-format] indexado pelo contêiner. O YARN fornece ferramentas CLI para despejar os logs como texto sem formatação para aplicativos ou contêineres de interesse. Você pode exibir esses logs como texto sem formatação, executando um dos seguintes comandos de YARN diretamente em nós de cluster (depois de se conectar a ele via RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN
A interface de usuário ResourceManager do YARN é executada no nó de cabeçalho do cluster e pode ser acessada pelo painel do portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, clique em **Procurar**, clique em **Clusters HDInsight**, clique em um cluster baseado no Windows cujos logs de aplicativo YARN você deseja acessar.
3. No menu superior, clique em **Painel**. Você vê uma página aberta em uma nova guia do navegador chamada **Console de Consulta do HDInsight**.
4. Em **Console de Consulta do HDInsight**, clique em **Interface do Usuário do YARN**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

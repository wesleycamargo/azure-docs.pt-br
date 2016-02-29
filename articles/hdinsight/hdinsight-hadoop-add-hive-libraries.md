<properties
pageTitle="Adicionar bibliotecas Hive durante a criação do cluster HDInsight | Azure"
description="Saiba como adicionar bibliotecas Hive (arquivos jar) a um cluster HDInsight durante a criação do cluster."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

#Adicionar bibliotecas do Hive durante a criação do cluster HDInsight

Se você tiver bibliotecas que usa frequentemente com Hive no HDInsight, este documento contém informações sobre como usar uma Ação de Script para pré-carregar as bibliotecas durante a criação do cluster. Isso torna as bibliotecas globalmente disponíveis no Hive (sem necessidade de usar [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las).

##Como ele funciona

Ao criar um cluster, se desejar, você pode especificar uma Ação de Script que executa um script nos nós do cluster enquanto ele está sendo criado. O script neste documento aceita um único parâmetro, que é um local WASB que contém as bibliotecas (armazenadas como arquivos jar) que serão pré-carregadas.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o diretório `/usr/lib/customhivelibs/` nos nós de cabeçalho e de trabalho e adiciona-os à propriedade `hive.aux.jars.path` no arquivo `core-site.xml`. Em clusters baseados em Linux, ele também atualiza o arquivo `hive-env.sh` com o local dos arquivos.

> [AZURE.NOTE] Usar as ações de script neste artigo disponibiliza as bibliotecas nos seguintes cenários:
>
> * __HDInsight baseado no Linux__: ao usar a __linha de comando Hive__, __WebHCat__ (Templeton,) e __HiveServer2__.
> * __HDInsight baseado no Windows__: ao usar a __linha de comando Hive__ e __WebHCat__ (Templeton).

##O script

__Local do script__

Para __clusters baseados no Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para __clusters baseados no Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Requisitos__

* Os scripts devem ser aplicados nos __Nós de cabeçalho__ e nos __Nós de trabalho__.

* Os jars que você deseja instalar devem estar armazenados no Armazenamento de Blobs do Azure em um __único contêiner__.

* A conta de armazenamento contendo a biblioteca de arquivos jar __deve__ ser vinculada ao cluster HDInsight durante a criação. Isso pode ser feito de duas maneiras:

    * Estando em um contêiner na conta de armazenamento padrão do cluster.
    
    * Estando em um contêiner em um contêiner de armazenamento vinculado. Por exemplo, no portal, é possível usar __Configuração Opcional__, __Contas de armazenamento vinculadas__ para adicionar mais armazenamento.

* O caminho WASB para o contêiner deve ser especificado como um parâmetro para a Ação de Script. Por exemplo, se os jars estivessem armazenados em um contêiner denominado __libs__ em uma conta de armazenamento denominada __mystorage__, o parâmetro seria \_\___wasb://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Este documento supõe que você já criou uma conta de armazenamento, um contêiner de blobs, e carregou os arquivos nela.
    >
    > Se ainda não criou uma conta de armazenamento, você poderá fazer isso usando o [Portal do Azure](https://portal.azure.com). Lá, você pode usar um utilitário como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) para criar um novo contêiner na conta e carregar os arquivo nele.

##Criar um cluster usando o script

> [AZURE.NOTE] As etapas a seguir criam um novo cluster HDInsight baseado no Linux. Para criar um novo cluster baseado no Windows, escolha __Windows__ como o sistema operacional do cluster ao criar o cluster e use o script do Windows (PowerShell) em vez do script bash.
> 
> Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não conclua o provisionamento.

2. Na folha **Configuração Opcional**, selecione as **Ações de Script** e forneça as informações mostradas abaixo:

    * __NOME__: insira um nome amigável para a ação de script.
    * __URI DO SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __CABEÇALHO__: marque esta opção
    * __TRABALHO__: marque esta opção.
    * __ZOOKEEPER__: deixe essa opção em branco.
    * __PARÂMETROS__: insira o endereço WASB para o contêiner e a conta de armazenamento que contém os jars. Por exemplo, \_\___wasb://libs@mystorage.blob.core.windows.net/__.

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração.

4. Na folha **Configuração Opcional**, escolha __Contas de Armazenamento Vinculadas__ e escolha o link __Adicionar uma chave de armazenamento__. Escolha a conta de armazenamento que contém os jars e use os botões de __seleção__ para salvar configurações e retornar à folha __Configuração Opcional__.

5. Use o botão **Selecionar** na parte inferior da folha **Configuração Opcional** para salvar as informações de configuração opcional.

6. Continue o provisionamento do cluster, como descrito em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Assim que a criação do cluster for concluída, você poderá usar os jars adicionados por meio desse script do Hive sem precisar usar a instrução `ADD JAR`.

##Próximas etapas

Neste documento, você aprendeu a adicionar bibliotecas Hive contidas nos arquivos jar a um cluster HDInsight durante a criação do cluster. Para saber mais sobre como trabalhar com o Hive, confira [Usar o Hive com o HDInsight](hdinsight-use-hive.md).

<!---HONumber=AcomDC_0218_2016-->
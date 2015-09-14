<properties
    pageTitle="Desenvolvimento de Ação de Script com o HDInsight | Microsoft Azure"
	description="Saiba como personalizar os clusters do Hadoop com a Ação de Script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
    ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="larryfr"/>

# Desenvolvimento de Ação de Script com o HDInsight

Ações de Script são um modo de personalizar clusters HDInsight do Azure por meio da especificação de configurações de cluster durante a instalação, ou por meio da instalação de serviços, ferramentas ou outros softwares adicionais no cluster.

> [AZURE.NOTE]As informações neste documento são específicas de clusters HDInsight baseados em Linux. Para obter informações sobre como usar Ações de Script com clusters baseados no Windows, consulte [Desenvolvimento de Ação de Script com o HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## O que são Ações de Script?

Ações de Script são scripts de Bash que são executados em nós de cluster durante o provisionamento. A Ação de Script é executada como raiz e concede direitos de acesso completo a nós do cluster.

A Ação de Script pode ser usada ao provisionar um cluster usando o __portal de visualização do Azure__, o __Azure PowerShell__ ou o __SDK do .NET do HDInsight__.

Para um passo a passo de como personalizar um cluster usando Ações de Script, consulte [Personalizar os clusters HDInsight usando Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de scripts

Ao desenvolver um script personalizado para um cluster HDInsight, há várias práticas recomendadas para se considerar:

- [Direcionar para a versão do Hadoop](#bPS1)
- [Fornecer links estáveis para recursos de script](#bPS2)
- [Certifique-se de que o script de personalização do cluster seja idempotente](#bPS3)
- [Garantir alta disponibilidade da arquitetura de cluster](#bPS5)
- [Configurar os componentes personalizados para usar armazenamento de Blob do Azure](#bPS6)
- [Gravar informações para STDOUT e STDERR](#bPS7)
- [Salvar arquivos como ASCII com terminações de linha LF](#bps8)

### <a name="bPS1"></a>Direcionar para a versão do Hadoop

Diferentes versões do HDInsight têm diferentes versões de componentes e serviços do Hadoop instaladas. Se seu script espera uma versão específica de um serviço ou componente, você deve usar somente o script com a versão do HDInsight que inclui os componentes necessários. Você pode encontrar informações sobre versões dos componentes incluídos com o HDInsight usando o documento [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md).

### <a name="bPS2"></a>Fornecer links estáveis para recursos de script

Os usuários devem ter certeza de que todos os scripts e recursos usados pelo script permaneçam disponíveis durante o tempo de vida do cluster e que as versões desses arquivos não sejam alterados durante este período. Esses recursos serão necessários se os nós no cluster forem recriados.

A melhor prática é baixar e arquivar tudo em uma conta de Armazenamento do Azure em sua assinatura.

> [AZURE.IMPORTANT]A conta de armazenamento usada deve ser a conta de armazenamento padrão para o cluster ou então em um contêiner público somente leitura em qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pela Microsoft são armazenados na conta de armazenamento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/), que é um contêiner público somente leitura mantido pela equipe do HDInsight.

### <a name="bPS3"></a>Certifique-se de que o script de personalização do cluster seja idempotente

Você deve esperar que os nós de um cluster HDInsight sejam recriados durante o tempo de vida do cluster e que o script de personalização do cluster seja usado quando isso acontece. Esse script deve ser projetado para ser idempotente no sentido de que, após a recriação, o script deve garantir que o cluster retorne para o mesmo estado cada vez que for executado.

Por exemplo, se um script personalizado instala um aplicativo em /usr/local/bin em sua primeira execução, este script deve verificar, em cada execução subsequente, se o aplicativo já existe no local /usr/local/bin antes de prosseguir com outras etapas no script.

### <a name="bPS5"></a>Garantir alta disponibilidade da arquitetura de cluster

Clusters HDInsight baseados em Linux fornecem dois nós de cabeçalho que estão ativos dentro do cluster; Ações de Script são executadas para ambos esses nós. Se os componentes que você instala esperam apenas um nó de cabeçalho, você deve criar um script que instalará o componente em apenas um dos dois nós de cabeçalho no cluster. Os nós de cabeçalho são nomeados **headnode0** e **headnode1**.

> [AZURE.IMPORTANT]Serviços padrão instalados como parte do HDInsight são projetados para fazer failover entre os dois nós de cabeçalho, conforme necessário; no entanto, essa funcionalidade não se estende a componentes personalizados instalados por meio de Ações de Script. Se você precisa que os componentes instalados por meio de uma Ação de Script fiquem altamente disponíveis, você deve implementar seu próprio mecanismo de failover que use os dois nós de cabeçalho disponíveis.

### <a name="bPS6"></a>Configurar os componentes personalizados para usar armazenamento de Blob do Azure

Os componentes que você instala no cluster podem ter uma configuração padrão que usa o armazenamento HDFS (Sistema de Arquivos Distribuído do Hadoop). Ao restaurar o cluster ao estado anterior, o sistema de arquivos HDFS é formatado e você perde todos os dados armazenados ali. Você deve alterar a configuração para usar o WASB (armazenamento de Blob do Azure) em vez disso, pois esse é o armazenamento padrão para o cluster e é mantido até mesmo quando o cluster é excluído.

Por exemplo, a amostra a seguir copia o arquivo giraph-Examples.jar do sistema de arquivos local para o WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Gravar informações para STDOUT e STDERR

As informações gravadas para STDOUT e STDERR são registradas e podem ser exibidas após o cluster ter sido provisionado usando a interface do usuário Web da Ambari.

A maioria dos pacotes de instalação e utilitários já gravará informações para STDOUT e STDERR; no entanto, talvez você queira adicionar registro em log adicional. Para enviar texto para STDOUT, use `echo`. Por exemplo:

        echo "Getting ready to install Foo"

Por padrão, `echo` enviará a cadeia de caracteres para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo:

        >&2 echo "An error occured installing Foo"

Isso redireciona as informações enviadas a STDOUT (1, que é o padrão e, portanto, não listado aqui) para STDERR (2). Para obter mais informações sobre redirecionamento de E/S, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para saber mais sobre exibição de informações registradas em log por Ações de Script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a> Salvar arquivos como ASCII com terminações de linha LF

Scripts de Bash devem ser armazenados com formato ASCII, com linhas terminadas em LF. Se os arquivos são armazenados como UTF-8, que pode incluir uma marca de ordem de byte no início do arquivo ou terminações de linha de CRLF, o que é comum para os editores do Windows, o script falhará com erros semelhantes ao mostrado a seguir:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Os métodos auxiliares da Ação de Script são recursos que podem ser usados durante a gravação de scripts personalizados. Eles são definidos em [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh), e podem ser incluídos em seus scripts usando o seguinte:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Isso torna os auxiliares a seguir disponíveis para uso em seu script:

| Uso do auxiliar | Descrição |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Baixa um arquivo da URL de origem para o caminho de arquivo especificado. Por padrão, ele não substituirá um arquivo existente. |
| `untar_file TARFILE DESTDIR` | Extrai um arquivo tar (usando `-xf`) para o diretório de destino. |
| `test_is_headnode` | Se executado em um nó de cabeçalho do cluster, retorna 1. Caso contrário, 0. |
| `test_is_datanode` | Se o nó atual é um nó de dados (de trabalho), retorna 1; caso contrário, 0. |
| `test_is_first_datanode` | Se o nó atual é o primeiro nó de dados (de trabalho, chamado workernode0), retorna 1; caso contrário, retorna 0. |

## <a name="commonusage"></a>Padrões comuns de uso

Esta seção fornece orientações sobre como implementar alguns dos padrões comuns de uso que você pode encontrar ao escrever seu próprio script personalizado.

### Passando parâmetros para um script

Em alguns casos, seu script pode exigir parâmetros. Por exemplo, a senha do administrador para o cluster talvez seja necessária para que você possa recuperar as informações da API REST do Ambari.

Os parâmetros passados para o script são conhecidos como _parâmetros posicionais_, e são atribuídos a `$1` para o primeiro parâmetro, `$2` para o segundo e assim por diante. `$0` contém o nome do próprio script.

Valores passados para o script como parâmetros devem estar entre aspas simples (') para que o valor passado seja tratado como um valor literal; além disso, não se dá tratamento especial para caracteres incluídos, como '!'.

### Configurando variáveis de ambiente

Definir uma variável de ambiente é uma ação realizada pelo seguinte:

    VARIABLENAME=value

Em que VARIABLENAME é o nome da variável. Para acessar a variável depois disso, use `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente denominada PASSWORD, use o seguinte:

    PASSWORD=$1

O acesso subsequente às informações poderia então usar `$PASSWORD`.

Variáveis de ambiente definidas no script existem somente dentro do escopo do script. Em alguns casos, talvez seja necessário adicionar variáveis de ambiente gerais do sistema que persistirão após a conclusão do script. Geralmente, isso é para os usuários se conectando ao cluster via SSH poderem usar os componentes instalados pelo script. Isso pode ser feito adicionando-se a variável de ambiente a `/etc/environment`. Por exemplo, o demonstrado a seguir adiciona __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### Acesso a locais onde estão armazenados os scripts personalizados

Scripts usados para personalizar um cluster precisam estar na conta de armazenamento padrão para o cluster ou, se estiver em qualquer outra conta de armazenamento, em um contêiner público somente leitura. Se o seu script acessa recursos localizados em outro lugar, estes também precisam acessíveis publicamente (pelo menos ser de tipo somente leitura público). Por exemplo, você talvez queira baixar um arquivo para o cluster usando `download_file`.

Armazenar o arquivo em uma conta de armazenamento do Azure acessível por meio do cluster (por exemplo, a conta de armazenamento padrão) fornecerá acesso rápido, pois trata-se de armazenamento dentro da rede do Azure.

## <a name="deployScript"></a>Lista de verificação para implantação de uma ação de script

Aqui estão as etapas que utilizamos ao se preparar para implantar esses scripts:

- Coloque os arquivos que contêm os scripts personalizados em um local que seja acessível pelos nós de cluster durante a implantação. Esse local pode ser qualquer uma das contas de armazenamento padrão ou adicionais especificadas no momento da implantação de cluster, ou qualquer outro contêiner de armazenamento acessível publicamente.

- Adicione as verificações em scripts para certificar-se de que elas são executadas impotentemente, para que o script possa ser executado várias vezes no mesmo nó.

- Use um diretório de arquivos temporário, /tmp, para manter os arquivos baixados usados pelos scripts e em seguida limpá-los, depois de os scripts terem sido executados.

- Caso as configurações de nível de sistema operacional ou arquivos de configuração de serviço do Hadoop sejam alterados, talvez você deseje reiniciar os serviços do HDInsight para que eles possam pegar qualquer configuração de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts.

## <a name="runScriptAction"></a>Como executar uma Ação de Script

Você pode usar Ações de Script para personalizar os clusters HDInsight usando o Portal do Azure, o Azure PowerShell ou o SDK do .NET do HDInsight. Para obter instruções, consulte [Como usar a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#howto).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes em um cluster HDInsight. Os scripts de exemplo e instruções sobre como usá-los estão disponíveis nos links abaixo:

- [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md)
- [Instalar e usar o Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Instalar e usar R em clusters Hadoop do HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE]Os documentos vinculados acima são específicos de clusters HDInsight baseados em Linux. Para scripts que funcionam com o HDInsight baseados em Windows, consulte [Desenvolvimento de Ação de Script com o HDInsight (Windows)](hdinsight-hadoop-script-actions.md) ou use os links disponíveis na parte superior de cada artigo.

##Solucionar problemas

Estes são erros que você pode encontrar ao usar scripts desenvolvidos por você:

__Erro__: `$'\r': command not found`. Às vezes, seguido por `syntax error: unexpected end of file`.

_Causa_: este erro é gerado quando as linhas em um script terminam com CRLF. Os sistemas UNIX esperam apenas LF como terminação da linha.

Esse problema ocorre geralmente quando o script é criado em um ambiente Windows, já que CRLF é uma terminação de linha comum para muitos editores de texto no Windows.

_Solução_: se for uma opção em seu editor de texto, selecione o formato Unix ou LF para o final da linha. Você também pode usar os seguintes comandos em um sistema Unix para alterar o CRLF para um LF:

> [AZURE.NOTE]Os comandos a seguir são a grosso modo equivalentes, no sentido que ambos devem alterar as terminações de linha CRLF para LF. Selecione um deles com base nos utilitários disponíveis no sistema.

| Command | Observações |
| ------- | ----- |
| `unix2dos -b INFILE` | O backup do arquivo original será feito com uma extensão .BAK |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE conterá uma versão apenas com terminações LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Isto modificará o arquivo diretamente, sem criar um novo arquivo |
| ```sed 's/$'"/`echo \\r`/" INFILE > OUTFILE``` | OUTFILE conterá uma versão apenas com terminações LF.

__Erro__: `line 1: #!/usr/bin/env: No such file or directory`.

_Causa_: este erro ocorre quando o script foi salvo como UTF-8 com uma BOM (marca de ordem de byte).

_Solução_: salve o arquivo como ASCII ou UTF-8, sem nenhuma BOM. Você também pode usar o seguinte comando em um sistema Linux ou Unix para criar um novo arquivo sem a BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Para o comando acima, substitua __INFILE__ pelo arquivo que contém a BOM. __OUTFILE__ deve ser um novo nome de arquivo, que conterá o script sem a BOM.

## <a name="seeAlso"></a>Consulte também

[Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md)

<!---HONumber=September15_HO1-->
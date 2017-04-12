---
title: "Desenvolvimento de ação de script com o HDInsight baseado em Linux | Microsoft Docs"
description: "Como personalizar os clusters HDInsight baseados em Linux com a Ação de script. As ações de script são um modo de personalizar clusters HDInsight do Azure por meio da especificação de configurações de cluster, ou por meio da instalação de serviços, ferramentas ou software adicionais no cluster. "
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9068e0e92e15491d3377a1b8f42071b56373396e
ms.lasthandoff: 04/12/2017


---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com o HDInsight

As ações de script são um modo de personalizar clusters HDInsight do Azure por meio da especificação de configurações de cluster, ou por meio da instalação de serviços, ferramentas ou software adicionais no cluster. Você pode usar ações de script durante a criação do cluster ou em um cluster em execução.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="what-are-script-actions"></a>O que são ações de script?

As ações de script são scripts Bash que o Azure executa em nós de cluster para fazer alterações de configuração ou para instalar software. A Ação de Script é executada como raiz e concede direitos de acesso completo a nós do cluster.

As ações de script podem ser aplicadas por meio dos seguintes métodos:

| Use esta opção para aplicar um script... | Durante a criação do cluster... | Em um cluster em execução... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI do Azure |&nbsp; |✓ |
| SDK do .NET do HDInsight |✓ |✓ |
| Modelo do Azure Resource Manager |✓ |&nbsp; |

Para saber mais sobre o uso desses métodos para aplicar ações de script, confira [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de scripts

Ao desenvolver um script personalizado para um cluster HDInsight, há várias práticas recomendadas para se considerar:

* [Direcionar para a versão do Hadoop](#bPS1)
* [Direcionar para a versão do sistema operacional](#bps10)
* [Fornecer links estáveis para recursos de script](#bPS2)
* [Usar os recursos pré-compilados](#bPS4)
* [Certifique-se de que o script de personalização do cluster seja idempotente](#bPS3)
* [Garantir alta disponibilidade da arquitetura de cluster](#bPS5)
* [Configurar os componentes personalizados para usar armazenamento de Blob do Azure](#bPS6)
* [Gravar informações para STDOUT e STDERR](#bPS7)
* [Salvar arquivos como ASCII com terminações de linha LF](#bps8)
* [Usar a lógica de repetição para recuperar-se de erros transitórios](#bps9)

> [!IMPORTANT]
> Ações de script devem ser concluídas em 60 minutos ou atingirão o tempo limite. Durante o provisionamento de nó, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos, como tempo de CPU ou largura de banda rede, pode fazer com que o script leve mais tempo para ser concluído comparado ao seu tempo de conclusão no ambiente de desenvolvimento.

### <a name="bPS1"></a>Direcionar para a versão do Hadoop

Diferentes versões do HDInsight têm diferentes versões de componentes e serviços do Hadoop instaladas. Se seu script espera uma versão específica de um serviço ou componente, você deve usar somente o script com a versão do HDInsight que inclui os componentes necessários. Você pode encontrar informações sobre versões dos componentes incluídos com o HDInsight usando o documento [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md) .

### <a name="bps10"></a>Direcionar para a versão do sistema operacional

O HDInsight baseado em Linux baseia-se na distribuição do Ubuntu do Linux. Versões diferentes do HDInsight contam com versões diferentes do Ubuntu, o que pode afetar o comportamento do seu script. Por exemplo, HDInsight 3.4 e versões mais recentes são baseados em versões do Ubuntu que usam o Upstart. A versão 3.5 baseia-se no Ubuntu 16.04, que usa Systemd. O Systemd e Upstart contam com comandos diferentes, portanto seu script deve ser escrito para funcionar com ambos.

Outra diferença importante entre o HDInsight 3.4 e o 3.5 é que o `JAVA_HOME` agora aponta para o Java 8.

Você pode verificar a versão do sistema operacional usando `lsb_release`. Os trechos de código a seguir, do script de instalação Hue, demonstram como determinar se o script está sendo executado no Ubuntu 14 ou 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Você pode encontrar o script completo que contém esses trechos de código em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu, que é usada pelo HDInsight, consulte o documento [Versão do componente HDInsight](hdinsight-component-versioning.md).

Para entender as diferenças entre Systemd e Upstart, consulte [Systemd para usuários do Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer links estáveis para recursos de script

Você deve ter certeza de que os scripts e recursos usados pelo script permaneçam disponíveis durante o tempo de vida do cluster e que as versões desses arquivos não sejam alterados durante este período. Esses recursos serão necessários se novos nós forem adicionados ao cluster durante as operações de dimensionamento.

A melhor prática é baixar e arquivar tudo em uma conta de Armazenamento do Azure em sua assinatura.

> [!IMPORTANT]
> A conta de armazenamento usada deve ser a conta de armazenamento padrão para o cluster ou então em um contêiner público somente leitura em qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pela Microsoft são armazenados na conta de armazenamento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) , que é um contêiner público somente leitura mantido pela equipe do HDInsight.

### <a name="bPS4"></a>Usar os recursos pré-compilados

Para reduzir o tempo necessário de execução do script, evite operações que compilem recursos do código-fonte. Em vez disso, pré-compile os recursos e armazene a versão binária no armazenamento de Blob do Azure para que eles possam ser baixados rapidamente para o cluster do seu script.

### <a name="bPS3"></a>Certifique-se de que o script de personalização do cluster seja idempotente

Os scripts devem ser projetados para serem idempotentes no sentido de que, se forem executados várias vezes, deverão garantir o retorno do cluster para o mesmo estado sempre que forem executados.

Por exemplo, se um script personalizado instala um aplicativo em /usr/local/bin em sua primeira execução, este script deve verificar, em cada execução subsequente, se o aplicativo já existe no local /usr/local/bin antes de prosseguir com outras etapas no script.

### <a name="bPS5"></a>Garantir alta disponibilidade da arquitetura de cluster

Os clusters HDInsight baseados em Linux fornecem dois nós de cabeçalho que estão ativos dentro do cluster; as ações de script são executadas para ambos esses nós. Se os componentes que você instala esperam apenas um nó de cabeçalho, você deve criar um script que instalará o componente em apenas um dos dois nós de cabeçalho no cluster.

> [!IMPORTANT]
> Serviços padrão instalados como parte do HDInsight são projetados para fazer failover entre os dois nós de cabeçalho, conforme necessário; no entanto, essa funcionalidade não se estende a componentes personalizados instalados por meio de ações de script. Se precisar que os componentes instalados por meio de uma ação de script fiquem altamente disponíveis, você deverá implementar seu próprio mecanismo de failover que use os dois nós de cabeçalho disponíveis.

### <a name="bPS6"></a>Configurar os componentes personalizados para usar armazenamento de Blob do Azure

Os componentes que você instala no cluster podem ter uma configuração padrão que usa o armazenamento HDFS (Sistema de Arquivos Distribuído do Hadoop). O HDInsight usa o Armazenamento de Blobs do Azure (WASB) como o armazenamento padrão. Isso fornece um sistema de arquivos compatível com HDFS que faz os dados persistirem mesmo que o cluster seja excluído. Você deve configurar os componentes instalados para usar o WASB em vez de HDFS.

Por exemplo, a amostra a seguir copia o arquivo giraph-Examples.jar do sistema de arquivos local para o WASB:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

### <a name="bPS7"></a>Gravar informações para STDOUT e STDERR

As informações gravadas em STDOUT e em STDERR durante a execução do script são registradas em log e podem ser exibidas usando a interface do usuário Web do Ambari.

> [!NOTE]
> O Ambari só estará disponível se o cluster for criado com êxito. Se você usar uma ação de script durante a criação do cluster e a criação falhar, confira a seção de solução de problemas [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para conhecer outras maneiras de acessar informações registradas em log.

A maioria dos pacotes de instalação e utilitários já gravará informações para STDOUT e STDERR; no entanto, talvez você queira adicionar registro em log adicional. Para enviar texto para STDOUT, use `echo`. Por exemplo:

```bash
echo "Getting ready to install Foo"
```

Por padrão, `echo` enviará a cadeia de caracteres para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Isso redireciona as informações enviadas a STDOUT (1, que é o padrão e, portanto, não listado aqui) para STDERR (2). Para obter mais informações sobre redirecionamento de E/S, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para saber mais sobre exibição de informações registradas em log por ações de script, confira [Personalizar clusters HDInsight usando ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Salvar arquivos como ASCII com terminações de linha LF

Scripts de Bash devem ser armazenados com formato ASCII, com linhas terminadas em LF. Se os arquivos são armazenados como UTF-8, que pode incluir uma marca de ordem de byte no início do arquivo ou terminações de linha de CRLF, o que é comum para os editores do Windows, o script falhará com erros semelhantes ao mostrado a seguir:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Usar a lógica de repetição para recuperar-se de erros transitórios

Ao baixar arquivos de instalação de pacotes usando apt-get ou outras ações que transmitem dados pela Internet, a ação pode falhar devido a erros de rede temporários. Por exemplo, o recurso remoto com o qual você está se comunicando pode estar em processo de failover para um nó de backup.

Para tornar o script resistente a erros transitórios, você pode implementar lógica de repetição. Veja a seguir um exemplo de uma função que executará qualquer comando passado a ele e, se o comando falhar, repeti-lo até três vezes. Ele aguardará dois segundos entre cada repetição.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Veja a seguir exemplos de como usar essa função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Os métodos auxiliares da ação de script são utilitários que podem ser usados quando você escreve scripts personalizados. Eles são definidos em [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh), e podem ser incluídos em seus scripts usando o seguinte:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Isso torna os auxiliares a seguir disponíveis para uso em seu script:

| Uso do auxiliar | Descrição |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Baixa um arquivo da URI de origem para o caminho de arquivo especificado. Por padrão, ele não substituirá um arquivo existente. |
| `untar_file TARFILE DESTDIR` |Extrai um arquivo tar (usando `-xf`) para o diretório de destino. |
| `test_is_headnode` |Se executado em um nó de cabeçalho do cluster, retorna 1. Caso contrário, 0. |
| `test_is_datanode` |Se o nó atual é um nó de dados (de trabalho), retorna 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual é o primeiro nó de dados (de trabalho, chamado workernode0), retorna 1; caso contrário, retorna 0. |
| `get_headnodes` |Retorna o nome de domínio totalmente qualificado dos nós de cabeçalho primários no cluster. Os nomes são delimitados por vírgula. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_primary_headnode` |Obtém o nome de domínio totalmente qualificado do nó de cabeçalho primário. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_secondary_headnode` |Obtém o nome de domínio totalmente qualificado do nó de cabeçalho secundário. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_primary_headnode_number` |Obtém o sufixo numérico do nó de cabeçalho primário. Uma cadeia de caracteres vazia retorna em caso de erro. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico do nó de cabeçalho secundário. Uma cadeia de caracteres vazia retorna em caso de erro. |

## <a name="commonusage"></a>Padrões comuns de uso

Esta seção fornece orientações sobre como implementar alguns dos padrões comuns de uso que você pode encontrar ao escrever seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passando parâmetros para um script

Em alguns casos, seu script pode exigir parâmetros. Por exemplo, a senha do administrador para o cluster talvez seja necessária para que você possa recuperar as informações da API REST do Ambari.

Os parâmetros passados para o script são conhecidos como *parâmetros posicionais* e são atribuídos a `$1` para o primeiro parâmetro, `$2` para o segundo e assim por diante. `$0` contém o nome do próprio script.

Valores passados para o script como parâmetros devem estar entre aspas simples (') para que o valor passado seja tratado como um valor literal; além disso, não se dá tratamento especial para caracteres incluídos, como '!'.

### <a name="setting-environment-variables"></a>Configurando variáveis de ambiente

Definir uma variável de ambiente é uma ação realizada pelo seguinte:

    VARIABLENAME=value

Em que VARIABLENAME é o nome da variável. Para acessar a variável depois disso, use `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente denominada PASSWORD, use o seguinte:

    PASSWORD=$1

O acesso subsequente às informações poderia, então, usar `$PASSWORD`.

Variáveis de ambiente definidas no script existem somente dentro do escopo do script. Em alguns casos, talvez seja necessário adicionar variáveis de ambiente gerais do sistema que persistirão após a conclusão do script. Geralmente, isso é para os usuários se conectando ao cluster via SSH poderem usar os componentes instalados pelo script. Isso pode ser feito adicionando-se a variável de ambiente a `/etc/environment`. Por exemplo, o seguinte adiciona **HADOOP\_CONF\_DIR**:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde estão armazenados os scripts personalizados

Scripts usados para personalizar um cluster devem ser armazenados em um dos seguintes locais:

* A __conta de armazenamento padrão__ para o cluster.

* Um __conta de armazenamento adicional__ associada ao cluster.

* Um __URI publicamente legível__, como o OneDrive, o Dropbox, etc.

* Uma __conta do Azure Data Lake Store__ que está associada com o cluster HDInsight. Para obter mais informações sobre o uso do Azure Data Lake Store com o HDInsight, veja [Criar um cluster HDInsight com o Repositório Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > A entidade de serviço que HDInsight usa para acessar o Data Lake Store deve ter acesso de leitura para o script.

Se o seu script acessa recursos localizados em outro lugar, estes também precisam acessíveis publicamente (pelo menos ser de tipo somente leitura público). Por exemplo, você talvez queira baixar um arquivo para o cluster usando `download_file`.

Armazenar o arquivo em uma conta de armazenamento do Azure acessível por meio do cluster (por exemplo, a conta de armazenamento padrão) fornecerá acesso rápido, pois trata-se de armazenamento dentro da rede do Azure.

> [!NOTE]
> O formato de URI usado para referenciar o script difere dependendo do serviço que está sendo usado. Para contas de armazenamento associadas ao cluster HDInsight, use `wasb://` ou `wasbs://`. Para URIs lidas publicamente, use `http://` ou `https://`. Para Data Lake Store, use `adl://`.

### <a name="checking-the-operating-system-version"></a>Verificando a versão do sistema operacional

Versões diferentes do HDInsight contam com versões específicas do Ubuntu. Pode haver diferenças entre as versões de sistema operacional, que você deve verificar em seu script. Por exemplo, talvez seja necessário instalar um binário que esteja vinculado à versão do Ubuntu.

Para verificar a versão do sistema operacional, use `lsb_release`. Por exemplo, o exemplo a seguir demonstra como fazer referência a um arquivo tar diferente dependendo da versão do sistema operacional:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Lista de verificação para implantação de uma ação de script

Aqui estão as etapas que utilizamos ao se preparar para implantar esses scripts:

* Coloque os arquivos que contêm os scripts personalizados em um local que seja acessível pelos nós de cluster durante a implantação. Esse local pode ser qualquer uma das contas de armazenamento padrão ou adicionais especificadas no momento da implantação de cluster, ou qualquer outro contêiner de armazenamento acessível publicamente.
* Adicione as verificações em scripts para certificar-se de que elas são executadas impotentemente, para que o script possa ser executado várias vezes no mesmo nó.
* Use um diretório de arquivos temporário, /tmp, para manter os arquivos baixados usados pelos scripts e em seguida limpá-los, depois de os scripts terem sido executados.
* Caso as configurações de nível de sistema operacional ou arquivos de configuração de serviço do Hadoop sejam alterados, talvez você deseje reiniciar os serviços do HDInsight para que eles possam pegar qualquer configuração de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts.

## <a name="runScriptAction"></a>Como executar uma Ação de Script

Você pode usar ações de script para personalizar os clusters HDInsight usando o portal do Azure, o Azure PowerShell, os modelos do Azure Resource Manager ou o SDK do .NET do HDInsight. Para obter instruções, confira [Como usar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes em um cluster HDInsight. Os scripts de exemplo e instruções sobre como usá-los estão disponíveis nos links abaixo:

* [Instalar e usar o Hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalar e usar R em clusters Hadoop do HDInsight](hdinsight-hadoop-r-scripts-linux.md)
* [Instalar e usar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalar e usar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [!NOTE]
> Os documentos vinculados acima são específicos de clusters HDInsight baseados em Linux. Para scripts que funcionam com o HDInsight baseados em Windows, consulte [Desenvolvimento de Ação de Script com o HDInsight (Windows)](hdinsight-hadoop-script-actions.md) ou use os links disponíveis na parte superior de cada artigo.

## <a name="troubleshooting"></a>Solucionar problemas

Estes são erros que você pode encontrar ao usar scripts desenvolvidos por você:

**Erro**: `$'\r': command not found`. Às vezes, seguido por `syntax error: unexpected end of file`.

*Causa*: este erro é gerado quando as linhas em um script terminam com CRLF. Os sistemas UNIX esperam apenas LF como terminação da linha.

Esse problema ocorre geralmente quando o script é criado em um ambiente Windows, já que CRLF é uma terminação de linha comum para muitos editores de texto no Windows.

*Solução*: se for uma opção em seu editor de texto, selecione o formato Unix ou LF para terminação de linha. Você também pode usar os seguintes comandos em um sistema Unix para alterar o CRLF para um LF:

> [!NOTE]
> Os comandos a seguir são a grosso modo equivalentes, no sentido que ambos devem alterar as terminações de linha CRLF para LF. Selecione um deles com base nos utilitários disponíveis no sistema.

| Command | Observações |
| --- | --- |
| `unix2dos -b INFILE` |O backup do arquivo original será feito com uma extensão .BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE conterá uma versão apenas com terminações LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` |Isto modificará o arquivo diretamente, sem criar um novo arquivo |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE conterá uma versão apenas com terminações LF. |

**Erro**: `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: este erro ocorre quando o script foi salvo como UTF-8 com uma BOM (Marca de Ordem de Byte).

*Solução*: salve o arquivo como ASCII ou UTF-8, sem nenhuma BOM. Você também pode usar o seguinte comando em um sistema Linux ou Unix para criar um novo arquivo sem a BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Para o comando acima, substitua **INFILE** pelo arquivo que contém a BOM. **OUTFILE** deve ser um novo nome de arquivo, que conterá o script sem a BOM.

## <a name="seeAlso"></a>Próximas etapas

* Saiba como [Personalizar os clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
* Use a [referência do SDK do .NET do HDInsight](https://msdn.microsoft.com/library/mt271028.aspx) para saber mais sobre a criação de aplicativos .NET que gerenciam o HDInsight
* Use a [API REST do HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a usar o REST para executar ações de gerenciamento em clusters HDInsight.


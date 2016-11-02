<properties
    pageTitle="Solução de contêineres no Log Analytics | Microsoft Azure"
    description="A solução de contêineres no Log Analytics ajuda a exibir e gerenciar os hosts de contêiner do Docker em uma única localização."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>




# <a name="containers-(preview)-solution-log-analytics"></a>Log Analytics da solução de contêineres (visualização)

Este artigo descreve como configurar e usar a solução de contêineres no Log Analytics, que ajuda você a exibir e gerenciar os hosts de contêiner do Docker em uma única localização. O Docker é um sistema de virtualização de software usado para criar contêineres que automatizam a implantação de software para infraestrutura de TI.

Com a solução, você pode ver quais contêineres estão em execução em seus hosts de contêiner e quais imagens estão em execução nos contêineres. Você pode exibir informações detalhadas de auditoria, mostrando os comandos usados com contêineres. E você pode solucionar problemas de contêineres exibindo e pesquisando logs centralizados sem precisar exibir remotamente os hosts do Docker. Você pode encontrar contêineres que podem estar com ruídos e consumindo recursos em excesso em um host. E você pode exibir o uso de CPU, memória, armazenamento e rede e informações de desempenho centralizadas para contêineres.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução

Use as informações a seguir para instalar e configurar a solução.

Adicione a solução de contêineres ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).

Há duas maneiras de instalar e usar o Docker com o OMS:

- Em sistemas operacionais Linux com suporte, instale e execute o Docker e, em seguida, instale e configure o Agente do OMS para Linux
- No CoreOS, instale e execute o Docker e configure o OMSAgent para ser executado dentro de um contêiner

Veja as versões de sistema operacional Linux e Docker com suporte para seu host do contêiner no [GitHub](https://github.com/Microsoft/OMS-docker).

>[AZURE.IMPORTANT] O Docker deve estar em execução **antes** de instalar o [Agente do OMS para Linux](log-analytics-linux-agents.md) em seus hosts de contêiner. Se você já tiver instalado o agente antes de instalar o Docker, precisará reinstalar o Agente do OMS para Linux. Para obter mais informações sobre o Docker, consulte o [site do Docker](https://www.docker.com).

Você precisa das seguintes configurações definidas em seus hosts de contêiner antes de poder monitorar os contêineres.

## <a name="configure-settings-for-the-linux-container-host"></a>Definir as configurações para o host de contêiner do Linux

Depois de instalar o Docker, use as seguintes definições para o host do contêiner para configurar o agente para uso com o Docker. O CoreOS não dá suporte a esse método de configuração.

### <a name="to-configure-settings-for-the-container-host---systemd-(suse,-opensuse,-centos-7.x,-rhel-7.x,-and-ubuntu-15.x-and-higher)"></a>Para definir as configurações para o host do contêiner – systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x e Ubuntu 15.x e superior)

1. Edite docker.service para adicionar o seguinte:

    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```

2. Adicione $DOCKER\_OPTS em &quot;ExecStart=/usr/bin/docker daemon&quot; no seu arquivo docker.service. Usando o exemplo a seguir.

    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```

3. Reinicie o serviço Docker. Por exemplo:

    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-(ubuntu-14.x)"></a>Para definir configurações para o host do contêiner – Upstart (Ubuntu 14.x)

1. Edite /etc/default/docker e adicione o seguinte:

    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Salve o arquivo e, em seguida, reinicie os serviços Docker e OMS.

    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Para definir configurações para o host do contêiner – Amazon Linux

1. Edite /etc/sysconfig/docker e adicione o seguinte:

    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Salve o arquivo e, em seguida, reinicie o serviço Docker.

    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Definir configurações para contêineres CoreOS

Depois de instalar o Docker, use as seguintes configurações para o CoreOS executar o Docker e criar um contêiner. Você pode usar qualquer versão com suporte do Linux — incluindo CoreOS, com este método de configuração. Você precisará da sua [chave e ID do espaço de trabalho do OMS](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Para usar o OMS para todos os contêineres com CoreOS

- Inicie o contêiner do OMS que você deseja monitorar. Modifique e use o exemplo a seguir.

  ```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Alternando do uso de um agente instalado para um em um contêiner

Se anteriormente você usava o agente instalado diretamente e em vez disso deseja usar um agente em execução em um contêiner, é necessário primeiro remover o OMSAgent. Consulte [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (Etapas para instalar o Agente do OMS para Linux).

## <a name="containers-data-collection-details"></a>Detalhes da coleta de dados dos contêineres

A solução de contêineres coleta vários dados de log e métricas de desempenho de hosts do contêiner e contêineres usando os agentes do OMS para Linux que você tenha habilitado no OMSAgent em execução em contêineres.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para os contêineres.

| plataforma | Agente do OMS para Linux | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
| Linux|![Sim](./media/log-analytics-containers/oms-bullet-green.png)|![Não](./media/log-analytics-containers/oms-bullet-red.png)|![Não](./media/log-analytics-containers/oms-bullet-red.png)|            ![Não](./media/log-analytics-containers/oms-bullet-red.png)|![Não](./media/log-analytics-containers/oms-bullet-red.png)| a cada 3 minutos|


A tabela a seguir mostra exemplos de tipos de dados coletados pela solução de contêineres:

| Tipo de dados | Campos |
| --- | --- |
| Desempenho de hosts e contêineres | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventário de contêiner | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventário de imagem de contêiner | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log do contêiner | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log do serviço de contêiner | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Monitorar contêineres

Depois de habilitar a solução no portal do OMS, você verá o bloco **Contêineres** mostrando informações resumidas sobre seus hosts de contêiner e os contêineres em execução nos hosts.

![Bloco Contêineres](./media/log-analytics-containers/containers-title.png)

O bloco mostra uma visão geral de quantos contêineres existem no ambiente e se eles estão com falha, em execução ou parados.

### <a name="using-the-containers-dashboard"></a>Usando o painel de Contêineres

Clique no bloco **Contêineres**. Ali, você verá exibições organizadas por:

- Eventos de contêiner
- Erros
- Status dos contêineres
- Inventário de imagem de contêiner
- Desempenho de CPU e memória

Cada painel no painel é uma representação visual de uma pesquisa que é executada nos dados coletados.

![Painel de Contêineres](./media/log-analytics-containers/containers-dash01.png)

![Painel de Contêineres](./media/log-analytics-containers/containers-dash02.png)

Na folha **Status do Contêiner**, clique na área superior, como mostrado abaixo.

![Status dos contêineres](./media/log-analytics-containers/containers-status.png)

A Pesquisa de Log é aberta, mostrando informações sobre os hosts e os contêineres em execução neles.

![Pesquisa de Log para contêineres](./media/log-analytics-containers/containers-log-search.png)

Aqui, você pode editar a consulta de pesquisa para modificá-la para localizar as informações específicas nas quais está interessado. Para obter mais informações sobre as Pesquisas de Log, consulte [Pesquisas de log no Log Analytics](log-analytics-log-searches.md).

Por exemplo, você pode modificar a consulta de pesquisa para que ela mostre todos os contêineres parados em vez dos contêineres em execução alterando **Em Execução** para **Parado** na consulta de pesquisa.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Solucionar problemas localizando um contêiner com falha

O OMS marcará um contêiner como **Com Falha** se ele tiver sido encerrado com um código de saída diferente de zero. Você pode conferir uma visão geral dos erros e falhas no ambiente na folha **Contêineres com Falha**.

### <a name="to-find-failed-containers"></a>Para localizar contêineres com falha

1. Clique na folha **Eventos de Contêiner**.  
  ![eventos de contêineres](./media/log-analytics-containers/containers-events.png)
2. A Pesquisa de Log é aberta, mostrando o status dos contêineres, semelhante ao seguinte.  
  ![estado dos contêineres](./media/log-analytics-containers/containers-container-state.png)
3. Em seguida, clique no valor com falha para exibir informações adicionais, como o tamanho da imagem e o número de imagens paradas e com falha. Expanda **mostrar mais** para exibir a ID da imagem.  
  ![contêineres com falha](./media/log-analytics-containers/containers-state-failed.png)
4. Em seguida, localize o contêiner que está executando esta imagem. Digite o seguinte na consulta de pesquisa.
  `Type=ContainerInventory <ImageID>` Isso exibe os logs. Você pode rolar para ver o contêiner com falha.  
  ![contêineres com falha](./media/log-analytics-containers/containers-failed04.png)


## <a name="search-logs-for-container-data"></a>Pesquisar nos logs por dados do contêiner

Quando você estiver solucionando um erro específico, pode ajudar ver onde ele está ocorrendo em seu ambiente. Os tipos de log a seguir ajudarão você a criar consultas para retornar as informações desejadas.

- **ContainerInventory** – use este tipo quando desejar obter informações sobre a localização do contêiner, quais são seus nomes e quais imagens eles estão executando.
- **ContainerImageInventory** – use este tipo quando estiver tentando localizar informações organizadas por imagem e para exibir informações da imagem como os tamanhos ou IDs da imagem.
- **ContainerLog** – use este tipo quando desejar localizar entradas e informações de log de erro específicas.
- **ContainerServiceLog** – use este tipo quando estiver tentando localizar informações de trilha de auditoria para o daemon do Docker, como os comandos start, stop, delete ou pull.

### <a name="to-search-logs-for-container-data"></a>Para pesquisar nos logs por dados do contêiner

- Escolha uma imagem que você saiba que falhou recentemente e encontre os logs de erros dela. Comece localizando um nome de contêiner que está executando a imagem com uma pesquisa **ContainerInventory**. Por exemplo, pesquise por `Type=ContainerInventory ubuntu Failed`  
    ![Pesquisar por contêineres do Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Anote o nome do contêiner a lado de **Nome** e pesquise por esses logs. Neste exemplo, é `Type=ContainerLog adoring_meitner`.

**Exibir informações de desempenho**

Quando você começar a construir consultas, pode ajudar ver o que é possível primeiro. Por exemplo, para ver todos os dados de desempenho, tente uma consulta ampla digitando a seguinte consulta de pesquisa.

```
Type=Perf
```

![desempenho de contêineres](./media/log-analytics-containers/containers-perf01.png)



Você pode ver isso em um formato mais gráfico quando clicar na palavra **Métricas** nos resultados.

![desempenho de contêineres](./media/log-analytics-containers/containers-perf02.png)



Você pode definir o escopo dos dados de desempenho que está vendo para um contêiner específico digitando o nome dele à direita da sua consulta.

```
Type=Perf <containerName>
```

Isso mostra a lista de métricas de desempenho que são coletadas para um contêiner individual.

![desempenho de contêineres](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log

Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-los de acordo com seu ambiente. Como ponto de partida, você pode experimentar com a folha **Consultas Notáveis** para ajudá-lo a criar consultas mais avançadas.

![Consultas de contêineres](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Salvando as consultas de pesquisa de log

Salvar consultas é um recurso padrão do Log Analytics. Ao salvá-las, você terá aquelas que considerou úteis acessíveis para uso futuro.

Depois de criar uma consulta que considerar útil, salve-a clicando em **Favoritos** na parte superior da página Pesquisa de Log. Depois, você pode acessá-la facilmente pela página **Meu Painel**.

## <a name="next-steps"></a>Próximas etapas

- [Pesquise nos logs](log-analytics-log-searches.md) para exibir registros de dados de contêiner detalhados.



<!--HONumber=Oct16_HO2-->



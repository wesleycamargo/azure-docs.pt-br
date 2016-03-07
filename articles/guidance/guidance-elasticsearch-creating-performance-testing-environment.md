<properties
   pageTitle="Criando um ambiente de teste de desempenho para o Elasticsearch | Microsoft Azure"
   description="Como configurar um ambiente de teste de desempenho de um cluster Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Criando um ambiente de teste de desempenho para Elasticsearch no Azure

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Este documento descreve como configurar um ambiente de teste de desempenho de um cluster Elasticsearch. Essa configuração foi usada para testar o desempenho da ingestão de dados e cargas de trabalho de consulta, conforme descrito em [Ajustando o desempenho da ingestão de dados com o Elasticsearch no Azure][].

O processo de teste de desempenho usou [Apache JMeter](http://jmeter.apache.org/), com o [conjunto padrão](http://jmeter-plugins.org/wiki/StandardSet/) de plug-ins instalados em uma configuração mestre/subordinado usando um conjunto de VMs dedicadas (que não faz parte do cluster Elasticsearch) configurado especificamente para esse objetivo.

O [PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/) foi instalado em cada nó do Elasticsearch. As seções a seguir fornecem instruções para recriar o ambiente de teste para que você possa conduzir seu próprios testes de desempenho com o JMeter. Essas instruções pressupõem que você já criou um cluster Elasticsearch com nós conectados por meio de uma rede virtual do Azure.

Observe que o ambiente de teste também é executado como um conjunto de máquinas virtuais do Azure gerenciado por meio de um único grupo de recursos do Azure.

O [Marvel](https://www.elastic.co/products/marvel) também foi instalado e configurado para permitir que os aspectos internos do cluster Elasticsearch sejam monitorados e analisados mais facilmente. Se as estatísticas do JMeter mostraram um pico ou depressão no desempenho, as informações disponíveis por meio do Marvel podem ser valiosas para ajudar a determinar a causa das flutuações.

A imagem a seguir mostra a estrutura de todo o sistema.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

Observe os seguintes pontos:

- A VM JMeter mestre executa o Windows Server para fornecer o ambiente de GUI para o console JMeter. A VM JMeter mestre fornece a interface gráfica do usuário (o aplicativo *jmeter*) para habilitar um testador a criar e executar testes e visualizar os resultados. Essa VM coordena com VMs do servidor JMeter que efetivamente enviarão as solicitações que constituem os testes.

- As VMs JMeter subordinadas executam o servidor Ubuntu (Linux); não há nenhum requisito de GUI para essas VMs. As VMs de servidor JMeter executam o software de servidor JMeter (o aplicativo *server-jmeter*) para enviar solicitações ao cluster Elasticsearch.

- Nós de cliente dedicados não foram usados, mas os nós mestre dedicados foram.

- O número de nós de dados do cluster pode variar dependendo do cenário que está sendo testado.

- Todos os nós no cluster Elasticsearch executam o Marvel para observar o desempenho no tempo de execução, e o agente de servidor JMeter para coletar dados de monitoramento para análise posterior.

- Ao testar o Elasticsearch 2.0.0 e versões posteriores, um dos nós de dados também executará o Kibana. Isso é exigido pela versão do Marvel que é executada no Elasticsearch 2.0.0 e posterior.

## Criando um Grupo de Recursos do Azure para as máquinas virtuais

O JMeter mestre precisa ser capaz de se conectar diretamente a cada um de nós no cluster Elasticsearch para coletar dados de desempenho. Se a rede virtual JMeter é diferente da rede virtual do cluster Elasticsearch, isso envolve configurar cada nó Elasticsearch com um endereço IP público. Se isso for um problema com a configuração de Elasticsearch, considere implementar as VMs JMeter na mesma rede virtual do cluster Elasticsearch usando o mesmo grupo de recursos. Nesse caso, você pode omitir esse primeiro procedimento.

Em primeiro lugar, [crie um grupo de recursos](../articles/resource-group-portal/#create-resource-group-and-resources). Esse documento presume que seu grupo de recursos se chama *JMeterPerformanceTest*. Se você deseja executar as VMs JMeter na mesma rede virtual do cluster Elasticsearch, use o mesmo grupo de recursos como cluster em vez de criar um novo.

## Criando a máquina virtual JMeter mestre

Em seguida, [crie uma máquina virtual Windows](../articles/virtual-machines-windows-tutorial/) usando a imagem do *Windows Server 2008 R2 SP1*. Recomendamos selecionar um tamanho de VM com núcleos e memória suficientes para executar os testes de desempenho. O ideal é que seja um computador com pelo menos dois núcleos e 3,5 GB de RAM (A2 Standard ou maior).

<!-- TODO add info on why disabling diagnostics is positive -->

Recomendamos que você desabilite o diagnóstico. Ao criar a VM no portal, isso é feito na folha *Configurações* na seção *Monitoramento* em *Diagnóstico*. Deixe as outras configurações em seus valores padrão.

Verifique se a máquina virtual e todos os recursos associados foram criados com êxito [examinando o grupo de recursos](../articles/resource-group-portal/#browse-resource-groups) no portal. Os recursos listados devem consistir em uma VM, um grupo de segurança de rede e um endereço IP público com o mesmo nome, e uma conta de armazenamento e de interface de rede com nomes baseados no da máquina virtual.

## Criando máquinas virtuais JMeter subordinadas

Agora [crie uma VM Linux](../articles/virtual-machines-linux-tutorial-portal-rm/) usando a imagem *Ubuntu Server 14.04 LTS*. Da mesma forma que a VM JMeter mestre, selecione um tamanho de VM com núcleos e memória suficientes para executar os testes de desempenho. O ideal é que seja um computador com pelo menos dois núcleos e 3,5 GB de RAM (Standard A2 ou maior).

Novamente, recomendamos que você desabilite o diagnóstico.

Você pode criar quantas VMs subordinadas desejar.

## Instalando o servidor JMeter nas VMs JMeter subordinadas

As VMs JMeter subordinadas executam o Linux e, por padrão, você não pode se conectar a elas abrindo uma RDP (conexão de área de trabalho remota). Ou você pode [usar PuTTY para abrir uma janela de linha de comando](../articles/virtual-machines-linux-how-to-log-on/) em cada máquina virtual.

Depois de se conectar a uma das VMs subordinadas, vamos usar bash configurar o JMeter.

Primeiro, instale o Java Runtime Environment necessário para executar o JMeter.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

Agora, baixe o software de JMeter empacotado como um arquivo zip.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Instale o comando descompactar e use-o para expandir o software JMeter. O software é copiado para uma pasta chamada **apache-jmeter-2.13**.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Altere para o diretório *bin* contendo os executáveis JMeter e torne os programas *server jmeter* e *jmeter* executáveis.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

Agora precisamos editar o arquivo `jmeter.properties` localizado na pasta atual (use o editor de texto com o qual você está mais familiarizado, como *vi* ou *vim*). Localize as seguintes linhas:

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Remova os comentários (remova os caracteres ## principais) e modifique essas linhas, conforme mostrado abaixo. Em seguida, salve o arquivo e feche o editor:

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

Agora, execute os seguintes comandos para abrir a porta 4441 para o tráfego TCP de entrada (essa é a porta você na qual você configurou *server-jmeter* para escutar):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

Baixe um arquivo zip que contém a coleção padrão de plug-ins para JMeter (esses plug-ins fornecem contadores de monitoramento do desempenho) e descompacte o arquivo. A descompactação do arquivo nesse local coloca os plug-ins na pasta correta.

Se você for solicitado a substituir o arquivo de LICENÇA, digite A (para todos):

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Use `nohup` para iniciar o servidor JMeter em segundo plano. Ela deve responder exibindo uma ID de processo e uma mensagem indicando ter criado um objeto remoto e estar pronta para começar a receber comandos.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] Se a VM for desligada, o programa de servidor JMeter será encerrado. Você precisará conectar-se à VM e reiniciá-la manualmente. Como alternativa, você pode configurar o sistema para executar o comando *server-jmeter* automaticamente na inicialização, adicionando os seguintes comandos ao arquivo `/etc/rc.local` (antes do comando *exit 0*):

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Substitua `<username>` pelo seu nome de logon.

Talvez seja útil manter a janela do terminal aberta para que você possa monitorar o progresso do servidor JMeter enquanto o teste está em andamento.

Você precisará repetir essas etapas para cada VM JMeter subordinada.

## Instalando o agente de servidor JMeter em nós do Elasticsearch

Esse procedimento pressupõe que você tem acesso de logon aos nós do Elasticsearch. Se você tiver criado o cluster usando o modelo do ARM, poderá se conectar a cada nó por meio da VM Jumpbox, conforme ilustrado na seção [Topologia de produção do Azure](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies). Você pode se conectar à Jumpbox usando PuTTY também.

A partir daí, você pode usar o comando *ssh* para entrar em cada um dos nós no cluster Elasticsearch.

Entre em um de nós do Elasticsearch como administrador. No prompt de comando do bash, digite os seguintes comandos a fim de criar uma pasta para conter o agente de servidor JMeter e mova para essa pasta:

```bash
mkdir server-agent
cd server-agent
```

Execute os seguintes comandos para instalar o comando *descompactar* (se já não estiver instalado), baixe o software do agente do servidor JMeter e descompacte-o:

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Execute o seguinte comando para configurar o firewall e habilitar o tráfego TCP para passar pela porta 4444 (essa é a porta usada pelo agente de servidor JMeter):

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Execute o seguinte comando para iniciar o agente de servidor JMeter em segundo plano:

```bash
nohup ./startAgent.sh &
```

O agente de servidor JMeter deve responder com mensagens indicando que ele foi iniciado e está escutando na porta 4444. Pressione Enter para obter um prompt de comando e execute o comando a seguir. Substitua `<nodename>` pelo nome do seu nó. Se você não tiver certeza do nome do nó, pode localizá-lo executando o comando `hostname`:

```bash
telnet <nodename> 4444
```

Esse comando abre uma conexão telnet com a porta 4444 no computador local. Você pode usar essa conexão para verificar se o agente de servidor JMeter está funcionando corretamente.

Se o agente de servidor JMeter não está em execução, você receberá a resposta

`*telnet: Unable to connect to remote host: Connection refused*.`

Se o agente de servidor JMeter estiver sendo executado e porta 4444 foi configurada corretamente, você verá a seguinte resposta:

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] A sessão de telnet não fornece qualquer tipo de prompt depois de se conectar.

Na sessão de telnet, digite o seguinte comando:

``` 
test
```

Se o agente de servidor JMeter está configurado e escuta corretamente, ele deve indicar que ele recebeu o comando e responder com a mensagem *Yep*.

> [AZURE.NOTE] Você pode digitar em outros comandos para obter dados de monitoramento de desempenho. Por exemplo, o comando `metric-single:cpu:idle` lhe dará a proporção atual do tempo em que a CPU está ociosa (como um instantâneo). Para obter uma lista completa de comandos, visite a página [PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/).

Na sessão de telnet, digite o seguinte comando para encerrar a sessão e retornar ao prompt de comando bash:

``` 
exit
```

> [AZURE.NOTE] Assim como acontece com as VMs JMeter subordinadas, se você sair ou se o computador for desligado e reiniciado em seguida, o agente de servidor JMeter precisará ser reiniciado manualmente usando o comando `startAgent.sh`. Se você deseja que o agente de servidor JMeter inicie automaticamente, adicione o comando a seguir ao final do arquivo `/etc/rc.local`, antes do comando *exit 0*. Substitua `<username>` pelo seu nome de logon:

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Substitua `<username>` pelo seu nome de logon.

Você pode agora ou repetir todo esse processo para todos os outros nós no cluster Elasticsearch ou pode usar o comando `scp` para copiar a pasta e o conteúdo do agente de servidor para cada nó e usar o comando `ssh` para iniciar o agente de servidor JMeter, conforme mostrado abaixo. Substitua `<username>` pelo seu nome de usuário e `<nodename>` pelo nome do nó em que deseja copiar e executar o software (você poderá ser solicitado a fornecer a senha ao executar cada comando):

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## Instalando e configurando o JMeter na VM JMeter mestre

No portal do Azure, clique em *Grupos de recursos*. Na folha *Grupos de recursos*, clique no grupo de recursos que contém VMs JMeter mestres e subordinadas. Na folha *grupo de recursos*, clique na VM JMeter mestre. Na folha de máquina virtual, na barra de ferramentas, clique em *Conectar*. Abra o arquivo RDP quando solicitado pelo navegador da Web. O Windows cria uma conexão de área de trabalho remota para sua VM. Insira o nome do usuário e a senha para a máquina virtual quando solicitado.

Na VM, usando o Internet Explorer, mova para a página [Baixar Java para Windows](http://www.java.com/en/download/ie_manual.jsp). Siga as instruções para baixar e executar o instalador do Java.

No navegador da Web, navegue até a página [Download Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) e faça o download do zip que contém o binário mais recente. Salve o zip em um local conveniente na sua VM.

Mova para o site [Plug-ins personalizados JMeter](http://jmeter-plugins.org/) e baixe o conjunto padrão de plug-ins. Salve o zip na mesma pasta que o download do JMeter da etapa anterior.

No Windows Explorer, mova para a pasta que contém o arquivo zip apache-jmeter -*xx* (em que *xx* é a versão atual do JMeter) e extraia os arquivos para a pasta atual (o zip criará uma subpasta chamada apache-jmeter-*xxx*).

Extraia os arquivos do arquivo JMeterPlugins-Standard-*yyy*.zip (em que *yyy* é a versão atual dos plug-ins) para a pasta apache-jmeter-*xxx*. Isso adicionará os plug-ins à pasta correta para o JMeter (você pode mesclar as pastas lib com segurança e substituir os arquivos Leiame e de licença, se solicitado).

Mova paa a pasta apache-jmeter-*xxx*/bin e edite o arquivo jmeter.properties usando o Bloco de notas. No arquivo `jmeter.properties`, localize a seção denominada *Hosts remotos e configuração RMI*. Nesta seção do arquivo, localize a seguinte linha:

```yaml
remote_hosts=127.0.0.1
```

Altere essa linha e substitua a lista de endereços 127.0.0.1 com uma lista separada por vírgulas de endereços IP ou nomes de host para cada um dos servidores JMeter subordinados. Por exemplo:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Localize a seguinte linha, remova o caractere `#` no início dessa linha e modifique o valor das configurações client.rmi.localport de:

```yaml
#client.rmi.localport=0
```

para:

```yaml
client.rmi.localport=4440
```

Salve o arquivo e feche o bloco de notas.

Na barra de ferramentas do Windows, clique em *Iniciar*, clique em *Ferramentas Administrativas* e clique em *Firewall do Windows com segurança avançada*. Na janela *Firewall do Windows com Segurança Avançada*, no painel esquerdo, clique com o botão direito do mouse em *Regras de Entrada* e clique em *Nova Regra*.

No *Assistente* *de nova regra de entrada*, na página *Tipo de Regra* página, selecione *Porta* e clique em *Avançar*. Na página *Protocolos e Portas*, selecione *TCP*, selecione *Portas locais específicas*. Na caixa de texto, digite `4440-4444` e clique em *Avançar*. Na página *Ação*, selecione *Permitir a conexão* e clique em *Avançar*. Na página *Perfil*, deixe todas as opções marcadas e clique em *Avançar*. Na página *Nome*, na caixa de texto *Nome*, digite *JMeter* e clique em *Concluir*. Feche a janela *Firewall do Windows com segurança avançada*.

No Windows Explorer, na pasta apache-jmeter-*xx*/bin, clique duas vezes no arquivo *jmeter* de lote do Windows para iniciar a interface gráfica do usuário. A interface do usuário deve ser exibida:

![](./media/guidance-elasticsearch/performance-image17.png)

Na barra de menus, clique em *Executar*, clique em *Inicialização Remota* e verifique se as duas máquinas JMeter subordinadas estão listadas:

![](./media/guidance-elasticsearch/performance-image18.png)

Agora você está pronto para iniciar o teste de desempenho.

## Instalando e configurando o Marvel

O modelo de início rápido do Elasticsearch para o Azure instalará e configurará a versão apropriada do Marvel automaticamente se você definir os parâmetros MARVEL e KIBANA como true durante a criação do cluster:

![](./media/guidance-elasticsearch/performance-image19.png)

Se você estiver adicionando o Marvel a um cluster existente, precisará executar a instalação manualmente, e o processo é diferente se você estiver usando a versão do Elasticsearch 1.7.x ou a 2. x, conforme descrito nos procedimentos a seguir.

### Instalando Marvel com Elasticsearch 1.73 ou anterior

Se você estiver usando o Elasticsearch 1.7.3 ou anterior, execute as seguintes etapas *em cada nó* no cluster:

- Entre n o nó e mova para o diretório base do Elasticsearch. No Linux, o diretório base típico é `/usr/share/elasticsearch`.

-  Execute o comando abaixo para baixar e instalar o plug-in Marvel para Elasticsearch:

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Pare e reinicie o Elasticsearch no nó:

```bash
sudo service elasticsearch restart
```

- Para verificar se o Marvel foi instalado corretamente, abra um navegador da Web e vá para a URL `http://<server>:9200/_plugin/marvel`. Substitua `<server>` com o nome ou endereço IP de qualquer servidor Elasticsearch no cluster. Verifique se aparece uma página semelhante à mostrada abaixo:

![](./media/guidance-elasticsearch/performance-image20.png)


### Instalando Marvel com Elasticsearch 2.0.0 ou posterior

Se você estiver usando o Elasticsearch 2.0.0 ou posterior, execute as seguintes tarefas *em cada nó* no cluster:

Entre no nó e mova-o para o diretório base do Elasticsearch (geralmente `/usr/share/elasticsearch`). Execute os comandos abaixo para baixar e instalar o plug-in Marvel para Elasticsearch:

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Pare e reinicie o Elasticsearch no nó:

```bash
sudo service elasticsearch restart
```

Substitua `<kibana-version>` pela 4.2.2 se você estiver usando o Elasticsearch 2.0.0 ou Elasticsearch 2.0.1, ou pela 4.3.1 se você estiver usando o Elasticsearch 2.1.0 ou posterior. Substitua `<marvel-version>` por 2.0.0 se você estiver usando o Elasticsearch 2.0.0 ou Elasticsearch 2.0.1, ou por 2.1.0 se você estiver usando Elasticsearch 2.1.0 ou posterior. Execute as seguintes tarefas *em um nó* no cluster:

Entre no nó e baixe a compilação apropriada do Kibana para sua versão do Elasticsearch do [site de download do Elasticsearch](https://www.elastic.co/downloads/past-releases) e extraia o pacote:

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Abra a porta 5601 para aceitar solicitações de entrada:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Mova para a pasta de configuração kibana (`kibana-<kibana-version>-linux-x64/config`), edite o `kibana.yml` arquivo e adicione a linha a seguir. Substitua `<server>` pelo nome ou endereço IP de um servidor Elasticsearch no cluster:

```yaml
elasticsearch.url: "http://<server>:9200"
```

Mova para a pasta bin kibana (`kibana-<kibana-version>-linux-x64/bin`) e execute o seguinte comando para integrar o plug-in Marvel ao Kibana:

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Inicie o Kibana:

```bash
sudo nohup ./kibana &
```

Para verificar a instalação do Marvel, abra um navegador da Web e vá para a URL `http://<server>:5601/app/marvel`. Substitua `<server>` pelo nome ou endereço IP do servidor que executa o Kibana.

Verifique se aparece uma página semelhante à mostrada abaixo (o nome do seu cluster provavelmente irá variar daquele mostrado na imagem).

![](./media/guidance-elasticsearch/performance-image21.png)

Clique no link que corresponde ao seu cluster (elasticsearch210 na imagem acima). Uma página semelhante à mostrada abaixo deve ser exibida:

![](./media/guidance-elasticsearch/performance-image22.png)

[Ajustando o desempenho da ingestão de dados com o Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->
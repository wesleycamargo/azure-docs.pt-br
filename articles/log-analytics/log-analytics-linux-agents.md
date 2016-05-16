<properties
	pageTitle="Conectar computadores Linux ao Log Analytics | Microsoft Azure"
	description="Usando o Log Analytics, você pode coletar os dados gerados em computadores Linux e tomar ações em relação a eles."
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
	ms.date="04/19/2016"
	ms.author="banders"/>

# Conectar computadores Linux ao Log Analytics

Usando o Log Analytics, você pode coletar os dados gerados em computadores Linux e tomar ações em relação a eles. A adição dos dados coletados no Linux para OMS permite que você gerencie sistemas Linux e soluções de contêiner como o Docker, independentemente de onde se encontram os computadores, virtualmente em qualquer lugar. Portanto, essas fontes de dados podem residir em seu datacenter local como servidores físicos, computadores virtuais em um serviço hospedado em nuvem, como o AWS (Amazon Web Services) ou o Microsoft Azure, ou até mesmo o laptop em sua mesa. Além disso, o OMS também coleta dados de computadores Windows da mesma forma e, portanto, oferece suporte a um ambiente de TI verdadeiramente híbrido.

Você pode exibir e gerenciar dados de todas essas fontes com o Log Analytics no OMS com um único portal de gerenciamento. Isso reduz a necessidade de monitorá-los usando vários sistemas diferentes, facilita seu consumo e é possível exportar todos os dados desejados para qualquer solução ou sistema de análise de negócios.

Este artigo é um guia de início rápido que ajudará você a coletar e a gerenciar dados para seus computadores Linux usando o Agente do OMS para Linux. Para obter mais detalhes técnicos, você encontrará essas informações na [visão geral do Agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) e na [documentação completa do Agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) no Github.


No momento, você pode coletar os seguintes tipos de dados em computadores Linux:

- Métricas de desempenho
- Eventos de syslog
- Alertas do Nagios e do Zabbix
- Logs, inventário e métricas de desempenho do contêiner Docker

## Versões do Linux com suporte

As versões x86 e x64 têm suporte oficial em uma variedade de distribuições do Linux. No entanto, o Agente do OMS para Linux também pode ser executado em outras distribuições não listadas.

- Amazon Linux 2012.09 a 2015.09
- CentOS Linux 5,6 e 7
- Oracle Linux 5,6 e 7
- Red Hat Enterprise Linux Server 5,6 e 7
- Debian GNU/Linux 6, 7 e 8
- Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
- SUSE Linux Enterprise Server 11 e 12

## Agente do OMS para Linux
O Agente do Operations Management Suite para Linux consiste em vários pacotes. O arquivo de versão contém os seguintes pacotes, disponíveis por meio da execução do pacote do shell com `--extract`.

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1\.1.0 | O Agente do Operations Management Suite para Linux
omsconfig | 1\.1.1 | Agente de configuração para o Agente do OMS
omi | 1\.0.8.3 | OMI (Open Management Infrastructure) – um servidor CIM leve
scx | 1\.6.2 | Provedores de CIM OMI para métricas de desempenho do sistema operacional
apache-cimprov | 1\.0.0 | Provedor de monitoramento de desempenho do Servidor HTTP Apache para OMI. Instalado somente se o Servidor HTTP Apache for detectado.
mysql-cimprov | 1\.0.0 | Provedor de monitoramento de desempenho do Servidor MySQL para OMI. Instalado somente se o servidor MySQL/MariaDB for detectado.
docker-cimprov | 0\.1.0 | Provedor do Docker para OMI. Instalado somente se o Docker for detectado.

### Artefatos adicionais de instalação
Após a instalação do agente do OMS para pacotes do Linux, serão aplicadas as seguintes alterações de configuração adicionais em todo o sistema. Esses artefatos serão removidos quando o pacote omsagent for desinstalado.
- Um usuário sem privilégios chamado: `omsagent` é criado. O daemon omsagent é executado como essa conta
- Um arquivo “include” sudoers é criado em /etc/sudoers.d/omsagent Isso autoriza o omsagent a reiniciar os daemons syslog e omsagent. Se o sudo incluir diretivas sem suporte na versão instalada do sudo, essas entradas serão gravadas em /etc/sudoers.
- A configuração de syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para saber mais, confira a seção **Configuração da Coleta de Dados** abaixo

### Detalhes da coleta de dados do Linux

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados.

| fonte | Agente Direct | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Zabbix|![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|1 minuto|
|Nagios|![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|na chegada|
|syslog|![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|do armazenamento do Azure: 10 minutos; do agente: na chegada|
|Contadores de desempenho do Linux|![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|Como agendado, mínimo de 10 segundos|
|controle de alterações|![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Não](./media/log-analytics-linux-agents/oms-bullet-red.png)|por hora|



### Requisitos de pacote
| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |	Biblioteca GNU C | 2\.5-12|
|Openssl | Bibliotecas OpenSSL | 0\.9.8e ou 1.0|
Curl | cliente Web cURL | 7\.15.5
|Python-ctypes |bibliotecas de função | n/d|
|PAM | Módulos de autenticação conectáveis |n/d |

>[AZURE.NOTE] Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## Instalação rápida

Execute os comandos a seguir para baixar o omsagent, validar a soma de verificação e então instalar e integrar o agente. Os comandos são para a versão de 64 bits. A ID do Espaço de Trabalho e a Chave Primária podem ser encontradas no portal do OMS em **Configurações** na guia **Fontes Conectadas**.

![detalhes do espaço de trabalho](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Há uma variedade de outros métodos para instalar o agente e atualizá-lo. Leia mais sobre eles em [Etapas para instalar o Agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).


## Escolha o método de coleta de dados do Linux

Como escolher os tipos de dados que serão coletados depende se você deseja usar o portal do OMS ou se você deseja editar vários arquivos de configuração diretamente nos clientes Linux. Se você optar por usar o portal, a configuração será enviada para todos os clientes Linux automaticamente. Se precisar de configurações distintas para diferentes clientes Linux, será preciso editar os arquivos de cliente individualmente ou usar uma alternativa, como o PowerShell DSC, o Chef ou o Puppet.

Você pode especificar os eventos e contadores de desempenho de syslog que deseja coletar usando arquivos de configuração nos computadores Linux. *Se optar por configurar a coleta de dados ao editar arquivos de configuração do agente, deverá desabilitar a configuração centralizada.* As instruções são fornecidas abaixo para configurar a coleta de dados em arquivos de configuração do agente e para desabilitar a configuração central para todos os agentes do OMS para Linux ou para computadores individuais.

### Desabilitar o gerenciamento do OMS para um computador Linux individual

A coleta de dados centralizada para dados de configuração está desabilitada para um computador Linux individual com o script OMS\_MetaConfigHelper.py. Isso poderá ser útil se um subconjunto de computadores tiver uma configuração especializada.

Para desabilitar a configuração centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Para reabilitar a configuração centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## Contadores de desempenho do Linux

Os contadores de desempenho do Linux são semelhantes aos contadores de desempenho do Windows. Ambos funcionam da mesma forma. Você pode usar os procedimentos a seguir para adicioná-los e configurá-los. Depois que eles forem adicionados ao OMS, os dados serão coletados para eles a cada 30 segundos.

### Para adicionar um contador de desempenho do Linux no OMS

1. Para configurar os Agentes do OMS para Linux usando o portal do OMS, você pode adicionar contadores de desempenho do Linux na página Configurações, clique em **Dados**. ![data](./media/log-analytics-linux-agents/oms-settings-data01.png)
2. Na página **Configurações**, em **Dados**, clique em **Contadores de desempenho do Linux** e, à esquerda do ícone de adição, digite o nome do contador que você deseja adicionar. ![Contadores de desempenho do Linux](./media/log-analytics-linux-agents/oms-linuxperfcounter01.png)
3. Se você não souber o nome completo do contador, comece a digitar um nome parcial e uma lista de contadores disponíveis será exibida. Quando você encontrar o contador que deseja adicionar, clique no nome na lista e clique no ícone de adição para adicionar o contador.
4. Após adicionar o contador, ele aparecerá na lista de contadores realçada com uma barra colorida.
5. Por padrão, a opção **Aplicar a configuração abaixo aos meus computadores** está selecionada. Se você quiser desabilitar o envio de dados de configuração, desmarque a seleção.
6. Quando terminar de modificar os contadores de desempenho, na parte inferior da página, clique em **Salvar** para finalizar suas alterações. As alterações de configuração feitas são então enviadas a todos os Agentes do OMS para Linux que, por sua vez, são registrados no OMS, normalmente em cinco minutos.

### Configurar contadores de desempenho do Linux no OMS

Para os contadores de desempenho do Windows, você pode escolher uma instância específica para cada contador de desempenho. No entanto, para os contadores de desempenho do Linux, qualquer instância de um contador escolhida se aplicará a todos os contadores filhos do contador pai. A tabela a seguir mostra as instâncias comuns disponíveis para os contadores de desempenho do Linux e do Windows.

| **Nome da instância** | **Significado** |
| --- | --- |
| \_Total | Total de todas as instâncias |
| * | Todas as instâncias |
| (/|/var) | Corresponde às instâncias chamadas: / ou /var |


Da mesma forma, o intervalo de exemplo escolhido para um contador pai se aplicará a todos os contadores filho. Em outras palavras, todos os intervalos e instâncias de exemplo do contador filho são vinculados.

### Adicionar e configurar métricas de desempenho com o Linux

As métricas de desempenho a serem coletadas são controladas pela configuração em /etc/opt/microsoft/omsagent/conf/omsagent.conf. Veja [Métricas de desempenho disponíveis](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) para as classes e as métricas disponíveis para o agente do OMS para Linux.

Cada objeto, ou categoria, de métricas de desempenho a ser coletado deve ser definido no arquivo de configuração como um único elemento `<source>`. A sintaxe segue o padrão abaixo.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Os parâmetros configuráveis desse elemento são:

- **Object\_name**: o nome do objeto da coleção.
- **Instance\_regex**: uma *expressão regular* que define quais instâncias serão coletadas. O valor: `.*` especifica todas as instâncias. Para coletar métricas de processador somente para a instância \_Total, você poderia especificar `_Total`. Para coletar métricas de processador somente para a instância \_Total, você poderia especificar: `(crond|sshd)`.
- **Counter\_name\_regex**: uma *expressão regular* que define os contadores (para o objeto) a serem coletados. Para coletar todos os contadores para o objeto, especifique: `.*`. Para coletar somente os contadores de espaço de permuta para o objeto de memória, você poderia especificar: `.+Swap.+`
- **Interval:**: a frequência na qual os contadores do objeto são coletados.

A configuração padrão para as métricas de desempenho é:

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### Habilitar os contadores de desempenho do MySQL usando os comandos do Linux

Se o Servidor MySQL ou MariaDB for detectado no computador quando o pacote omsagent for instalado, um provedor de monitoramento de desempenho para o Servidor MySQL será automaticamente instalado. Esse provedor se conecta ao servidor MySQL/MariaDB local para expor as estatísticas de desempenho. Você precisa configurar as credenciais de usuário do MySQL para que o provedor possa acessar o Servidor MySQL.

Para definir uma conta de usuário padrão para o servidor MySQL no localhost, use o seguinte exemplo de comando.

>[AZURE.NOTE] O arquivo de credenciais deve ser legível para a conta do omsagent. É recomendável executar o comando mycimprovauth como omsgent.


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Como alternativa, você pode especificar as credenciais necessárias do MySQL em um arquivo, ao criar o arquivo: /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Para saber mais sobre o gerenciamento de credenciais do MySQL para o monitoramento por meio do arquivo mysql-auth, confira [Gerenciar as credenciais de monitoramento do MySQL no arquivo de autenticação](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Confira [Permissões de banco de dados necessárias para contadores de desempenho do MySQL](#database-permissions-required-for-mysql-performance-counters) para obter detalhes sobre as permissões de objeto necessárias ao usuário do MySQL para coletar dados de desempenho do Servidor MySQL.

### Habilitar os contadores de desempenho do Servidor HTTP Apache usando os comandos do Linux

Se o Servidor HTTP Apache for detectado no computador quando o pacote omsagent for instalado, um provedor de monitoramento de desempenho para o Servidor HTTP Apache será automaticamente instalado. Esse provedor se baseia em um "módulo" do Apache que deve ser carregado no Servidor HTTP Apache para acessar os dados de desempenho.

Você pode carregar o módulo com o seguinte comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descarregar o módulo de monitoramento do Apache, execute o seguinte comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### Para exibir dados de desempenho com o Log Analytics

1. No portal do Operations Management Suite, clique no bloco Pesquisa de Log.
2. Na barra de pesquisa, digite `* (Type=Perf)` para exibir todos os contadores de desempenho.


Como o OMS também coleta dados de contador de desempenho do Windows, reduza o escopo da pesquisa para os dados específicos do Linux. Assim, o exemplo a seguir mostraria dados de desempenho específicos de um servidor Linux de exemplo chamado Chorizo21.

```
Type=Perf Computer=chorizo*
```

![servidor de exemplo mostrado nos resultados da pesquisa](./media/log-analytics-linux-agents/oms-perfsearch01.png)

Nos resultados, você pode clicar em **Métricas** para exibir os contadores para os quais os dados são coletados. Os dados em tempo real são mostrados como gráficos para cada contador.

![metrics](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## Syslog

O Syslog é um protocolo de registro em log de eventos semelhante aos Logs de Eventos do Windows. Ambos funcionam de forma parecida ao serem exibidos no OMS.

### Para adicionar um novo recurso de syslog do Linux ao OMS

1. Na página **Configurações**, em **Dados** , clique em **Syslog** e, à esquerda do ícone de adição, digite o nome do recurso de syslog que você deseja adicionar. ![Syslog do Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.	Se você não souber o nome completo do recurso, poderá começar a digitar um nome parcial e será exibida uma lista de recursos de syslog disponíveis. Quando encontrar o recurso de syslog que deseja adicionar, clique no nome na lista e clique no ícone de adição para adicionar o recurso de syslog.
3.	Depois que você adicionar o recurso de syslog, ele aparecerá na lista realçada com uma barra colorida. Em seguida, escolha as severidades (categorias de informações de recurso de syslog) que você deseja coletar.
4.	Na parte inferior da página, clique em **Salvar** para finalizar suas alterações. As alterações de configuração feitas são então enviadas a todos os Agentes do OMS para Linux que, por sua vez, são registrados no OMS, normalmente em cinco minutos.


### Configurar recursos de syslog do Linux no Linux

Os eventos de syslog são enviados a partir do daemon syslog, por exemplo, rsyslog ou syslog-ng, para uma porta local em que o agente está escutando. Por padrão, a porta 25224. Quando o agente é instalado, uma configuração de syslog padrão é aplicada. Isso é encontrado em:


Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf


A configuração de syslog do agente OMS padrão carrega os eventos de syslog de todos os recursos com uma severidade de aviso ou superior.

>[AZURE.NOTE] Se você editar a configuração de syslog, deverá reiniciar o daemon syslog para que as alterações entrem em vigor.

A configuração de syslog padrão para o Agente do OMS para Linux para o OMS é:

#### Rsyslog

```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### Syslog-ng

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### Para exibir todos os eventos de Syslog com o Log Analytics

1. No portal do Operations Management Suite, clique no bloco **Pesquisa de Log**.
2. No agrupamento **Gerenciamento de Log**, escolha uma pesquisa de syslog predefinida e selecione uma para executá-la.

Este exemplo mostra todos os eventos de Syslog.

![Eventos de Syslog mostrados na Pesquisa de Log](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Agora você pode analisar os resultados da pesquisa.

## Alertas do Linux

Se você usar o Nagios ou o Zabbix para gerenciar os computadores Linux, o OMS poderá receber os alertas gerados a partir dessas ferramentas. Entretanto, não há um método para configurar dados de alerta de entrada usando o portal do OMS. Em vez disso, você precisará editar um arquivo de configuração para começar a enviar alertas para o OMS.



### Coletar alertas a partir do Nagios

Para coletar alertas de um servidor Nagios, será preciso fazer as seguintes alterações de configuração.

1. Conceda ao usuário **omsagent** acesso de leitura ao arquivo de log do Nagios (ou seja, /var/log/nagios/nagios.log/var/log/nagios/nagios.log). Supondo que o arquivo nagios.log pertença ao grupo **nagios**, você poderá adicionar o usuário **omsagent** ao grupo **nagios**.

    ```
    sudo usermod –a -G nagios omsagent
    ```

2. Modifique o arquivo omsagent.confconfiguration (/etc/opt/microsoft/omsagent/conf/omsagent.conf). Verifique se as entradas a seguir estão presentes e se não foram comentadas:

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```

3. Reinicie o daemon omsagent:

    ```
    sudo service omsagent restart
    ```

### Coletar alertas do Zabbix

Para coletar alertas de um servidor Zabbix, você executará etapas semelhantes às mostradas acima para o Nagios, exceto que será necessário especificar um usuário e senha em *texto não criptografado*. Isso não é o ideal, mas provavelmente será alterado em breve. Para resolver esse problema, recomendamos que você crie o usuário e conceda a ele permissão somente para monitorar.

Uma seção de exemplo do arquivo de configuração omsagent.conf (/etc/opt/microsoft/omsagent/conf/omsagent.conf) para Zabbix deve ser semelhante à seguinte:

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### Exibir alertas na pesquisa do Log Analytics

Depois de configurar os computadores Linux para enviar alertas para o OMS, você poderá usar algumas consultas de pesquisa de log simples para exibir os alertas. O exemplo de consulta de pesquisa a seguir retorna todos os alertas registrados gerados. Por exemplo, se ocorrer algum tipo de problema em sua infraestrutura de TI, então os resultados da consulta de exemplo a seguir poderiam indicar a origem do problema. Além disso, você pode facilmente fazer uma busca detalhada de alertas por sistema de origem para ajudar a restringir sua investigação. A vantagem é que você não precisa necessariamente acessar vários sistemas de gerenciamento desde o início, desde que os alertas sejam enviados para o OMS, você pode iniciar lá.

```
Type=Alert
```

#### Para exibir todos os eventos do Nagios com o Log Analytics
1. No portal do Operations Management Suite, clique no bloco **Pesquisa de Log**.
2. Na barra de consulta, digite a seguinte consulta de pesquisa

    ```
    Type=Alert SourceSystem=Nagios
    ```
![Alertas do Nagios mostrados na Pesquisa de Log](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Depois de ver os resultados da pesquisa, você poderá analisar os detalhes adicionais, como *AlertState*.

### Para exibir todos os eventos do Zabbix com o Log Analytics
1. No portal do Operations Management Suite, clique no bloco **Pesquisa de Log**.
2. Na barra de consulta, digite a seguinte consulta de pesquisa

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![Alertas do Zabbix mostrados na Pesquisa de Log](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Depois de ver os resultados da pesquisa, você poderá analisar os detalhes adicionais, como *AlertName*.


## Compatibilidade com o System Center Operations Manager

O Agente do OMS para Linux compartilha arquivos binários de agente com o agente do System Center Operations Manager. A instalação do Agente do OMS para Linux em um sistema atualmente gerenciado pelo Operations Manager, atualiza os pacotes OMI e SCX no computador para uma versão mais recente. O Agente do OMS para Linux e o System Center 2012 R2 são compatíveis. No entanto, o **System Center 2012 SP1 e versões anteriores atualmente não são compatíveis com o Agente do OMS para Linux ou não têm suporte.**

>[AZURE.NOTE] Se o agente do OMS para Linux for instalado em um computador que atualmente não seja gerenciado pelo Operations Manager e se depois você quiser gerenciar o computador com o Operations Manager, você deverá modificar a configuração do OMI antes de descobrir o computador. **Esta etapa não será necessária se o agente do Operations Manager for instalado antes do Agente do OMS para Linux.**

### Para permitir que o Agente do OMS para Linux se comunique com o Operations Manager

1. Edite o arquivo /etc/opt/omi/conf/omiserver.conf
2. Verifique se a linha que começa com **httpsport=** define a porta 1270. Como `httpsport=1270`
3. Reinicie o servidor OMI:

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## Permissões de banco de dados necessárias para contadores de desempenho do MySQL

Para conceder permissões a um usuário de monitoramento do MySQL, o usuário que receber a concessão deverá ter o privilégio “GRANT option”, além do privilégio concedido.

Para que o Usuário do MySQL retorne dados de desempenho de que o usuário precisará para acessar as consultas a seguir:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Além dessas consultas, o usuário do MySQL requer acesso SELECT às seguintes tabelas padrão:

- information\_schema
- MySQL

Esses privilégios podem ser concedidos por meio da execução dos seguintes comandos de concessão.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## Gerenciar as credenciais de monitoramento do MySQL no arquivo de autenticação

As seções a seguir ajudam a gerenciar as credenciais do MySQL.

### Configurar o provedor de OMI do MySQL

O provedor de OMI do MySQL requer um usuário do MySQL pré-configurado e a instalação de bibliotecas do cliente do MySQL para consultar as informações de desempenho/integridade da instância do MySQL.

### Arquivo de autenticação de OMI do MySQL

O provedor de OMI do MySQL usa um arquivo de autenticação para determinar o endereço de ligação e a porta em que a instância do MySQL está escutando e quais credenciais usar para obter métricas de porta. Durante a instalação, o provedor de OMI do MySQL examinará os arquivos de configuração my.cnf do MySQL (locais padrão) para obter o endereço de ligação e a porta e para definir parcialmente o arquivo de autenticação de OM do MySQL.

Para concluir o monitoramento de uma instância do servidor MySQL, adicione um arquivo de autenticação de OMI do MySQL previamente gerado no diretório correto.

### Formato do arquivo de autenticação

O arquivo de autenticação de OMI do MySQL é um arquivo de texto que contém informações sobre:

- Port
- Endereço de Ligação
- Nome de usuário do MySQL
- Senha codificada em Base64

O arquivo de autenticação de OMI do MySQL concede privilégios de leitura/gravação ao usuário do Linux que o gerou.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Um arquivo de autenticação de OMI do MySQL padrão contém uma instância e um número de porta padrão, dependendo de quais informações estarão disponíveis e serão analisadas a partir do arquivo de configuração do MySQL encontrado.

A instância padrão é um meio para facilitar o gerenciamento de várias instâncias do MySQL em um host Linux e é indicada pela instância com a porta 0. Todas as instâncias adicionadas herdarão as propriedades definidas na instância padrão. Por exemplo, se a instância do MySQL que estiver escutando na porta “3308” for adicionada, o endereço de ligação, o nome de usuário e a senha codificada em Base64 da instância padrão serão usados para experimentar e monitorar a instância que escuta na porta 3308. Se a instância na porta 3308 estiver vinculada a outro endereço e usar o mesmo par de nome de usuário e senha do MySQL, somente uma nova especificação do endereço de ligação será necessária e as outras propriedades serão herdadas.

Os exemplos de arquivo de autenticação são semelhantes ao seguinte.

Instância padrão e instância com porta 3308:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Instância padrão e instância com porta 3308 + senha codificada em Base64 diferente:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **Propriedade** | **Descrição** |
| --- | --- |
| Port | A porta representa a porta atual em que a instância do MySQL está escutando. A porta 0 significa que as propriedades a seguir são usadas para a instância padrão. |
| Endereço de Ligação | O Endereço de Ligação é o endereço de ligação atual do MySQL |
| Nome de Usuário | É o nome de usuário do usuário do MySQL que você deseja usar para monitorar a instância do servidor MySQL. |
| Senha codificada em Base64 | É a senha do usuário de monitoramento do MySQL codificado em Base64. |
| Atualização Automática | Quando o Provedor de OMI do MySQL é atualizado, o provedor faz uma nova varredura em busca de alterações no arquivo my.cnf e substitui o arquivo de Autenticação de OMI do MySQL. Defina esse sinalizador como verdadeiro ou falso, dependendo das atualizações necessárias para o arquivo de autenticação de OMI do MySQL. |

#### Local do arquivo de autenticação

O arquivo de autenticação de OMI do MySQL deve estar no local a seguir e deve se chamar "mysql-auth":

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

O arquivo (e o diretório auth/omsagent) deve pertencer ao usuário omsagent.

## Logs do Agente

Os logs do Agente do OMS para Linux estão em:

/var/opt/microsoft/omsagent/log/

Os logs do Agente do OMS para Linux para o programa omsconfig (configuração do agente) estão em:

/var/opt/microsoft/omsconfig/log/

Os logs para os componentes OMI e SCX (que fornecem dados de métrica de desempenho) estão em:

/var/opt/omi/log/ e /var/opt/microsoft/scx/log

## Limitações conhecidas
Examine as seções a seguir para saber mais sobre as limitações atuais do Agente do OMS para Linux.

### Diagnóstico do Azure

Para as máquinas virtuais do Linux em execução no Azure, podem ser necessárias etapas adicionais para permitir a coleta de dados pelo Diagnóstico do Azure e pelo Operations Management Suite. A **Versão 2.2** da Extensão de Diagnóstico para Linux é necessária para compatibilidade com o Agente do OMS para Linux.

Para saber mais sobre como instalar e configurar a Extensão de Diagnóstico para Linux, confira [Usar o comando CLI do Azure para habilitar a Extensão de Diagnóstico do Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension/#use-the-azure-cli-command-to-enable-linux-diagnostic-extension).

**Atualização da Extensão de Diagnóstico do Linux do ASM da CLI do Azure 2.0 para 2.2:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

Esses exemplos de comando fazem referência a um arquivo chamado PrivateConfig.json. O formato desse arquivo deve se parecer com o exemplo a seguir.

```
	{
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
	}
```

### Não há suporte para Sysklog.

Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog. Para saber mais sobre como substituir o sysklog pelo rsyslog, veja [Instalar o RPM de rsyslog recém-criado](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM).

## Próximas etapas

- [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e obter dados.
- Familiarize-se com as [pesquisas de log](log-analytics-log-searches.md) para exibir informações detalhadas reunidas por soluções.
- Use [painéis](log-analytics-dashboards.md) para salvar e exibir suas próprias pesquisas personalizadas.

<!---HONumber=AcomDC_0504_2016-->
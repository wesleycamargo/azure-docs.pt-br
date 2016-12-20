---
title: Conectar computadores Linux ao Log Analytics | Microsoft Docs
description: "Usando o Log Analytics, você pode coletar os dados gerados em computadores Linux e tomar ações em relação a eles."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 218ffec4601c5b0b4ee9872b5bbd03489cb3ddcf


---
# <a name="connect-linux-computers-to-log-analytics"></a>Conectar computadores Linux ao Log Analytics
Usando o Log Analytics, você pode coletar os dados gerados em computadores Linux e tomar ações em relação a eles. A adição dos dados coletados no Linux para OMS permite que você gerencie sistemas Linux e soluções de contêiner como o Docker, independentemente de onde se encontram os computadores, virtualmente em qualquer lugar. Portanto, essas fontes de dados podem residir em seu datacenter local como servidores físicos, computadores virtuais em um serviço hospedado em nuvem, como o AWS (Amazon Web Services) ou o Microsoft Azure, ou até mesmo o laptop em sua mesa. Além disso, o OMS também coleta dados de computadores Windows da mesma forma e, portanto, oferece suporte a um ambiente de TI verdadeiramente híbrido.

Você pode exibir e gerenciar dados de todas essas fontes com o Log Analytics no OMS com um único portal de gerenciamento. Isso reduz a necessidade de monitorá-los usando vários sistemas diferentes, facilita seu consumo e é possível exportar todos os dados desejados para qualquer solução ou sistema de análise de negócios.

Este artigo é um guia de início rápido que ajudará você a coletar e a gerenciar dados para seus computadores Linux usando o Agente do OMS para Linux. Para obter mais detalhes técnicos, como a configuração do servidor proxy, informações sobre as métricas do CollectD e fontes de dados JSON personalizadas, acesse a [Visão geral do Agente OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) e a [Documentação completa do Agente OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) no Github.

No momento, você pode coletar os seguintes tipos de dados em computadores Linux:

* Métricas de desempenho
* Eventos de syslog
* Alertas do Nagios e do Zabbix
* Logs, inventário e métricas de desempenho do contêiner Docker

## <a name="supported-linux-versions"></a>Versões do Linux com suporte
As versões x86 e x64 têm suporte oficial em uma variedade de distribuições do Linux. No entanto, o Agente do OMS para Linux também pode ser executado em outras distribuições não listadas.

* Amazon Linux 2012.09 a 2015.09
* CentOS Linux 5, 6 e 7
* Oracle Linux 5, 6 e 7
* Red Hat Enterprise Linux Server 5, 6 e 7
* Debian GNU/Linux 6, 7 e 8
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
* SUSE Linux Enterprise Server 11 e 12

## <a name="oms-agent-for-linux"></a>Agente do OMS para Linux
O Agente do Operations Management Suite para Linux consiste em vários pacotes. O arquivo de versão contém os seguintes pacotes, disponíveis por meio da execução do pacote do shell com `--extract`.

| **Pacote** | **Versão** | **Descrição** |
| --- | --- | --- |
| omsagent |1.1.0 |O Agente do Operations Management Suite para Linux |
| omsconfig |1.1.1 |Agente de configuração para o Agente do OMS |
| omi |1.0.8.3 |OMI (Open Management Infrastructure) – um servidor CIM leve |
| scx |1.6.2 |Provedores de CIM OMI para métricas de desempenho do sistema operacional |
| apache-cimprov |1.0.0 |Provedor de monitoramento de desempenho do Servidor HTTP Apache para OMI. Instalado somente se o Servidor HTTP Apache for detectado. |
| mysql-cimprov |1.0.0 |Provedor de monitoramento de desempenho do Servidor MySQL para OMI. Instalado somente se o servidor MySQL/MariaDB for detectado. |
| docker-cimprov |0.1.0 |Provedor do Docker para OMI. Instalado somente se o Docker for detectado. |

### <a name="additional-installation-artifacts"></a>Artefatos adicionais de instalação
Após a instalação do agente do OMS para pacotes do Linux, serão aplicadas as seguintes alterações de configuração adicionais em todo o sistema. Esses artefatos serão removidos quando o pacote omsagent for desinstalado.

* Um usuário sem privilégios chamado: `omsagent` é criado. O daemon omsagent é executado como essa conta
* Um arquivo “include” sudoers é criado em /etc/sudoers.d/omsagent Isso autoriza o omsagent a reiniciar os daemons syslog e omsagent. Se o sudo incluir diretivas sem suporte na versão instalada do sudo, essas entradas serão gravadas em /etc/sudoers.
* A configuração de syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para saber mais, confira a seção **Configuração da Coleta de Dados** abaixo

### <a name="linux-data-collection-details"></a>Detalhes da coleta de dados do Linux
A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados.

| fonte | Agente Direct | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 minuto |
| Nagios |![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |na chegada |
| syslog |![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |do armazenamento do Azure: 10 minutos; do agente: na chegada |
| Contadores de desempenho do Linux |![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |Como agendado, mínimo de 10 segundos |
| controle de alterações |![Sim](./media/log-analytics-linux-agents/oms-bullet-green.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |![Não](./media/log-analytics-linux-agents/oms-bullet-red.png) |por hora |

### <a name="package-requirements"></a>Requisitos de pacote
| **Pacote necessário** | **Descrição** | **Versão mínima** |
| --- | --- | --- |
| Glibc |Biblioteca GNU C |2.5-12 |
| Openssl |Bibliotecas OpenSSL |0.9.8e ou 1.0 |
| Curl |cliente Web cURL |7.15.5 |
| Python-ctypes |bibliotecas de função |n/d |
| PAM |Módulos de autenticação conectáveis |n/d |

> [!NOTE]
> Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.
> 
> 

## <a name="quick-install"></a>Instalação rápida
Execute os comandos a seguir para baixar o omsagent, validar a soma de verificação e então instalar e integrar o agente. Os comandos são para a versão de 64 bits. A ID do Espaço de Trabalho e a Chave Primária podem ser encontradas no portal do OMS em **Configurações** na guia **Fontes Conectadas**.

![detalhes do espaço de trabalho](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Há uma variedade de outros métodos para instalar o agente e atualizá-lo. Leia mais sobre eles em [Etapas para instalar o Agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).

Você também pode exibir o [Passo a passo em vídeo do Azure](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Escolha o método de coleta de dados do Linux
Como escolher os tipos de dados que serão coletados depende se você deseja usar o portal do OMS ou se você deseja editar vários arquivos de configuração diretamente nos clientes Linux. Se você optar por usar o portal, a configuração será enviada para todos os clientes Linux automaticamente. Se precisar de configurações distintas para diferentes clientes Linux, será preciso editar os arquivos de cliente individualmente ou usar uma alternativa, como o PowerShell DSC, o Chef ou o Puppet.

Você pode especificar os eventos e contadores de desempenho de syslog que deseja coletar usando arquivos de configuração nos computadores Linux. *Se optar por configurar a coleta de dados ao editar arquivos de configuração do agente, deverá desabilitar a configuração centralizada.*   As instruções são fornecidas abaixo para configurar a coleta de dados em arquivos de configuração do agente e para desabilitar a configuração central para todos os agentes do OMS para Linux ou para computadores individuais.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Desabilitar o gerenciamento do OMS para um computador Linux individual
A coleta de dados centralizada para dados de configuração está desabilitada para um computador Linux individual com o script OMS_MetaConfigHelper.py. Isso poderá ser útil se um subconjunto de computadores tiver uma configuração especializada.

Para desabilitar a configuração centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Para reabilitar a configuração centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Contadores de desempenho do Linux
Os contadores de desempenho do Linux são semelhantes aos contadores de desempenho do Windows. Ambos funcionam da mesma forma. Você pode usar os procedimentos a seguir para adicioná-los e configurá-los. Depois que eles forem adicionados ao OMS, os dados serão coletados para eles a cada 30 segundos.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Para adicionar um contador de desempenho do Linux no OMS
1. Para configurar os Agentes do OMS para Linux usando o portal do OMS, você pode adicionar contadores de desempenho do Linux na página Configurações, clique em **Dados**.  
2. Na página **Configurações**, em **Dados**, clique em **Contadores de desempenho do Linux** e selecione ou digite o nome do contador que você deseja adicionar.  
    ![dados](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Se você não souber o nome completo do contador, comece a digitar um nome parcial e uma lista de contadores disponíveis será exibida. Quando você encontrar o contador que deseja adicionar, clique no nome na lista e clique no ícone de adição para adicionar o contador.
4. Após adicionar o contador, ele aparecerá na lista de contadores realçada com uma barra colorida.
5. Por padrão, a opção **Aplicar a configuração abaixo aos meus computadores** está selecionada. Se você quiser desabilitar o envio de dados de configuração, desmarque a seleção.
6. Quando terminar de modificar os contadores de desempenho, na parte inferior da página, clique em **Salvar** para finalizar suas alterações. As alterações de configuração feitas são então enviadas a todos os Agentes do OMS para Linux que, por sua vez, são registrados no OMS, normalmente em cinco minutos.

### <a name="configure-linux-performance-counters-in-oms"></a>Configurar contadores de desempenho do Linux no OMS
Para os contadores de desempenho do Windows, você pode escolher uma instância específica para cada contador de desempenho. No entanto, para os contadores de desempenho do Linux, qualquer instância de um contador escolhida se aplicará a todos os contadores filhos do contador pai. A tabela a seguir mostra as instâncias comuns disponíveis para os contadores de desempenho do Linux e do Windows.

| **Nome da instância** | **Significado** |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Corresponde às instâncias chamadas: / ou /var |

Da mesma forma, o intervalo de exemplo escolhido para um contador pai se aplicará a todos os contadores filho. Em outras palavras, todos os intervalos e instâncias de exemplo do contador filho são vinculados.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Adicionar e configurar métricas de desempenho com o Linux
As métricas de desempenho a serem coletadas são controladas pela configuração em /etc/opt/microsoft/omsagent/conf/omsagent.conf. Veja [Métricas de desempenho disponíveis](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) para as classes e as métricas disponíveis para o agente do OMS para Linux.

Cada objeto, ou categoria, de métricas de desempenho a ser coletado deve ser definido no arquivo de configuração como um único elemento `<source>` . A sintaxe segue o padrão abaixo.

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

* **Object\_name**: o nome do objeto da coleção.
* **Instance\_regex**: uma *expressão regular* que define quais instâncias serão coletadas. O valor: `.*` especifica todas as instâncias. Para coletar métricas de processador somente para a instância \_Total, você poderia especificar `_Total`. Para coletar métricas de processador somente para a instância _Total, você poderia especificar: `(crond|sshd)`.
* **Counter\_name\_regex**: uma *expressão regular* que define os contadores (para o objeto) a serem coletados. Para coletar todos os contadores para o objeto, especifique: `.*`. Para coletar somente os contadores de espaço de permuta para o objeto de memória, você poderia especificar: `.+Swap.+`
* **Interval:**: a frequência na qual os contadores do objeto são coletados.

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

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Habilitar os contadores de desempenho do MySQL usando os comandos do Linux
Se o Servidor MySQL ou MariaDB for detectado no computador quando o pacote omsagent for instalado, um provedor de monitoramento de desempenho para o Servidor MySQL será automaticamente instalado. Esse provedor se conecta ao servidor MySQL/MariaDB local para expor as estatísticas de desempenho. Você precisa configurar as credenciais de usuário do MySQL para que o provedor possa acessar o Servidor MySQL.

Para definir uma conta de usuário padrão para o servidor MySQL no localhost, use o seguinte exemplo de comando.

> [!NOTE]
> O arquivo de credenciais deve ser legível para a conta do omsagent. É recomendável executar o comando mycimprovauth como omsgent.
> 
> 

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Como alternativa, você pode especificar as credenciais necessárias do MySQL em um arquivo, ao criar o arquivo: /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Para saber mais sobre o gerenciamento de credenciais do MySQL para o monitoramento por meio do arquivo mysql-auth, confira [Gerenciar as credenciais de monitoramento do MySQL no arquivo de autenticação](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Confira [Permissões de banco de dados necessárias para contadores de desempenho do MySQL](#database-permissions-required-for-mysql-performance-counters) para obter detalhes sobre as permissões de objeto necessárias ao usuário do MySQL para coletar dados de desempenho do Servidor MySQL.

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Habilitar os contadores de desempenho do Servidor HTTP Apache usando os comandos do Linux
Se o Servidor HTTP Apache for detectado no computador quando o pacote omsagent for instalado, um provedor de monitoramento de desempenho para o Servidor HTTP Apache será automaticamente instalado. Esse provedor se baseia em um "módulo" do Apache que deve ser carregado no Servidor HTTP Apache para acessar os dados de desempenho.

Você pode carregar o módulo com o seguinte comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para descarregar o módulo de monitoramento do Apache, execute o seguinte comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Para exibir dados de desempenho com o Log Analytics
1. No portal do Operations Management Suite, clique no bloco Pesquisa de Log.
2. Na barra de pesquisa, digite `* (Type=Perf)` para exibir todos os contadores de desempenho.

Como o OMS também coleta dados de contador de desempenho do Windows, reduza o escopo da pesquisa para os dados específicos do Linux. Assim, o exemplo a seguir mostraria dados de desempenho específicos de um servidor Linux de exemplo chamado Chorizo21.

```
Type=Perf Computer=chorizo*
```

![servidor de exemplo mostrado nos resultados da pesquisa](./media/log-analytics-linux-agents/oms-perfsearch01.png)

Nos resultados, você pode clicar em **Métricas** para exibir os contadores para os quais os dados são coletados. Os dados em tempo real são mostrados como gráficos para cada contador.

![Métricas](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
O Syslog é um protocolo de registro em log de eventos semelhante aos Logs de Eventos do Windows. Ambos funcionam de forma parecida ao serem exibidos no OMS.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Para adicionar um novo recurso de syslog do Linux ao OMS
1. Na página **Configurações**, em **Dados**, clique em **Syslog** e, à esquerda do ícone de adição, digite o nome do recurso de syslog que você deseja adicionar.
    ![Syslog do Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. Se você não souber o nome completo do recurso, poderá começar a digitar um nome parcial e será exibida uma lista de recursos de syslog disponíveis. Quando encontrar o recurso de syslog que deseja adicionar, clique no nome na lista e clique no ícone de adição para adicionar o recurso de syslog.
3. Depois que você adicionar o recurso de syslog, ele aparecerá na lista realçada com uma barra colorida. Em seguida, escolha as severidades (categorias de informações de recurso de syslog) que você deseja coletar.
4. Na parte inferior da página, clique em **Salvar** para finalizar suas alterações. As alterações de configuração feitas são então enviadas a todos os Agentes do OMS para Linux que, por sua vez, são registrados no OMS, normalmente em cinco minutos.

### <a name="configure-linux-syslog-facilities-in-linux"></a>Configurar recursos de syslog do Linux no Linux
Os eventos de syslog são enviados a partir do daemon syslog, por exemplo, rsyslog ou syslog-ng, para uma porta local em que o agente está escutando. Por padrão, a porta 25224. Quando o agente é instalado, uma configuração de syslog padrão é aplicada. Isso é encontrado em:

Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf

A configuração de syslog do agente OMS padrão carrega os eventos de syslog de todos os recursos com uma severidade de aviso ou superior.

> [!NOTE]
> Se você editar a configuração de syslog, deverá reiniciar o daemon syslog para que as alterações entrem em vigor.
> 
> 

A configuração de syslog padrão para o Agente do OMS para Linux para o OMS é:

#### <a name="rsyslog"></a>Rsyslog
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

#### <a name="syslog-ng"></a>Syslog-ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Para exibir todos os eventos de Syslog com o Log Analytics
1. No portal do Operations Management Suite, clique no bloco **Pesquisa de Log** .
2. No agrupamento **Gerenciamento de Log** , escolha uma pesquisa de syslog predefinida e selecione uma para executá-la.

Este exemplo mostra todos os eventos de Syslog.

![Eventos de Syslog mostrados na Pesquisa de Log](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Agora você pode analisar os resultados da pesquisa.

## <a name="linux-alerts"></a>Alertas do Linux
Se você usar o Nagios ou o Zabbix para gerenciar os computadores Linux, o OMS poderá receber os alertas gerados a partir dessas ferramentas. Entretanto, não há um método para configurar dados de alerta de entrada usando o portal do OMS. Em vez disso, você precisará editar um arquivo de configuração para começar a enviar alertas para o OMS.

### <a name="collect-alerts-from-nagios"></a>Coletar alertas a partir do Nagios
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

### <a name="collect-alerts-from-zabbix"></a>Coletar alertas do Zabbix
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

### <a name="view-alerts-in-log-analytics-search"></a>Exibir alertas na pesquisa do Log Analytics
Depois de configurar os computadores Linux para enviar alertas para o OMS, você poderá usar algumas consultas de pesquisa de log simples para exibir os alertas. O exemplo de consulta de pesquisa a seguir retorna todos os alertas registrados gerados. Por exemplo, se ocorrer algum tipo de problema em sua infraestrutura de TI, então os resultados da consulta de exemplo a seguir poderiam indicar a origem do problema. Além disso, você pode facilmente fazer uma busca detalhada de alertas por sistema de origem para ajudar a restringir sua investigação. A vantagem é que você não precisa necessariamente acessar vários sistemas de gerenciamento desde o início, desde que os alertas sejam enviados para o OMS, você pode iniciar lá.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Para exibir todos os eventos do Nagios com o Log Analytics
1. No portal do Operations Management Suite, clique no bloco **Pesquisa de Log** .
2. Na barra de consulta, digite a seguinte consulta de pesquisa
   
    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![Alertas do Nagios mostrados na Pesquisa de Log](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Depois de ver os resultados da pesquisa, você poderá analisar os detalhes adicionais, como *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Para exibir todos os eventos do Zabbix com o Log Analytics
1. No portal do Operations Management Suite, clique no bloco **Pesquisa de Log** .
2. Na barra de consulta, digite a seguinte consulta de pesquisa
   
    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![Alertas do Zabbix mostrados na Pesquisa de Log](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Depois de ver os resultados da pesquisa, você poderá analisar os detalhes adicionais, como *AlertName*.

## <a name="compatibility-with-system-center-operations-manager"></a>Compatibilidade com o System Center Operations Manager
O Agente do OMS para Linux compartilha arquivos binários de agente com o agente do System Center Operations Manager. A instalação do Agente do OMS para Linux em um sistema atualmente gerenciado pelo Operations Manager, atualiza os pacotes OMI e SCX no computador para uma versão mais recente. O Agente do OMS para Linux e o System Center 2012 R2 são compatíveis. No entanto, o **System Center 2012 SP1 e versões anteriores atualmente não são compatíveis com o Agente do OMS para Linux ou não têm suporte.**

> [!NOTE]
> Se o agente do OMS para Linux for instalado em um computador que atualmente não seja gerenciado pelo Operations Manager e se depois você quiser gerenciar o computador com o Operations Manager, você deverá modificar a configuração do OMI antes de descobrir o computador. **Esta etapa não será necessária se o agente do Operations Manager for instalado antes do Agente do OMS para Linux.**
> 
> 

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Para permitir que o Agente do OMS para Linux se comunique com o Operations Manager
1. Edite o arquivo /etc/opt/omi/conf/omiserver.conf
2. Verifique se a linha que começa com **httpsport=** define a porta 1270. Como `httpsport=1270`
3. Reinicie o servidor OMI:
   
    ```
    service omiserver restart or systemctl restart omiserver
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>Permissões de banco de dados necessárias para contadores de desempenho do MySQL
Para conceder permissões a um usuário de monitoramento do MySQL, o usuário que receber a concessão deverá ter o privilégio “GRANT option”, além do privilégio concedido.

Para que o Usuário do MySQL retorne dados de desempenho de que o usuário precisará para acessar as consultas a seguir:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Além dessas consultas, o usuário do MySQL requer acesso SELECT às seguintes tabelas padrão:

* information_schema
* MySQL

Esses privilégios podem ser concedidos por meio da execução dos seguintes comandos de concessão.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Gerenciar as credenciais de monitoramento do MySQL no arquivo de autenticação
As seções a seguir ajudam a gerenciar as credenciais do MySQL.

### <a name="configure-the-mysql-omi-provider"></a>Configurar o provedor de OMI do MySQL
O provedor de OMI do MySQL requer um usuário do MySQL pré-configurado e a instalação de bibliotecas do cliente do MySQL para consultar as informações de desempenho/integridade da instância do MySQL.

### <a name="mysql-omi-authentication-file"></a>Arquivo de autenticação de OMI do MySQL
O provedor de OMI do MySQL usa um arquivo de autenticação para determinar o endereço de ligação e a porta em que a instância do MySQL está escutando e quais credenciais usar para obter métricas de porta. Durante a instalação, o provedor de OMI do MySQL examinará os arquivos de configuração my.cnf do MySQL (locais padrão) para obter o endereço de ligação e a porta e para definir parcialmente o arquivo de autenticação de OM do MySQL.

Para concluir o monitoramento de uma instância do servidor MySQL, adicione um arquivo de autenticação de OMI do MySQL previamente gerado no diretório correto.

### <a name="authentication-file-format"></a>Formato do arquivo de autenticação
O arquivo de autenticação de OMI do MySQL é um arquivo de texto que contém informações sobre:

* Port
* Endereço de Ligação
* Nome de usuário do MySQL
* Senha codificada em Base64

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
| Port |A porta representa a porta atual em que a instância do MySQL está escutando.  A porta 0 significa que as propriedades a seguir são usadas para a instância padrão. |
| Endereço de Ligação |O Endereço de Ligação é o endereço de ligação atual do MySQL |
| Nome de Usuário |É o nome de usuário do usuário do MySQL que você deseja usar para monitorar a instância do servidor MySQL. |
| Senha codificada em Base64 |É a senha do usuário de monitoramento do MySQL codificado em Base64. |
| Atualização Automática |Quando o Provedor de OMI do MySQL é atualizado, o provedor faz uma nova varredura em busca de alterações no arquivo my.cnf e substitui o arquivo de Autenticação de OMI do MySQL. Defina esse sinalizador como verdadeiro ou falso, dependendo das atualizações necessárias para o arquivo de autenticação de OMI do MySQL. |

#### <a name="authentication-file-location"></a>Local do arquivo de autenticação
O arquivo de autenticação de OMI do MySQL deve estar no local a seguir e deve se chamar "mysql-auth":

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

O arquivo (e o diretório auth/omsagent) deve pertencer ao usuário omsagent.

## <a name="agent-logs"></a>Logs do Agente
Os logs do Agente do OMS para Linux estão em:

/var/opt/microsoft/omsagent/log/

Os logs do Agente do OMS para Linux para o programa omsconfig (configuração do agente) estão em:

/var/opt/microsoft/omsconfig/log/

Os logs para os componentes OMI e SCX (que fornecem dados de métrica de desempenho) estão em:

/var/opt/omi/log/ e /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Solucionando problemas do Agente do OMS para Linux
Use as informações a seguir para diagnosticar e solucionar problemas comuns.

Se nenhuma das informações de solução de problemas nesta seção for útil, você também poderá usar os seguintes recursos para ajudar a resolver o problema.

* Os clientes com suporte Premier podem registrar um caso de suporte via [Premier](https://premier.microsoft.com/)
* Os clientes com contratos de suporte do Azure podem registrar os casos de suporte no [Portal do Azure](https://manage.windowsazure.com/?getsupport=true)
* Arquive um [Problema no GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)
* Fórum de comentários para obter ideias e criar um relatório de bug [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>Locais de log importantes
| Arquivo | Caminho |
| --- | --- |
| Agente do OMS para arquivo de log do Linux |`/var/opt/microsoft/omsagent/log/omsagent.log ` |
| Agente do OMS para arquivo de log de configuração |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>Arquivos de configuração importantes
| Categoria | Local do arquivo |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf` ou `/etc/rsyslog.conf` ou `/etc/rsyslog.d/95-omsagent.conf` |
| Desempenho, Nagios, Zabbix, saída do OMS e agente geral |`/etc/opt/microsoft/omsagent/conf/omsagent.conf` |
| Configurações adicionais |`/etc/opt/microsoft/omsagent/conf.d/*.conf` |

> [!NOTE]
> Os arquivos de configuração de edição para contadores de desempenho e syslog serão substituídos se a Configuração do Portal do OMS estiver habilitada. Você pode desabilitar a configuração no Portal do OMS (para todos os nós) ou para nós únicos ao executar o seguinte:
> 
> 

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Habilitar o log de depuração
Para habilitar o log de depuração, você pode usar o plug-in de saída e a saída detalhada do OMS.

#### <a name="oms-output-plugin"></a>Plug-in de saída do OMS
O FluentD permite que o plug-in especifique níveis de log para níveis de log diferentes para entradas e saídas. Para especificar um nível de log diferente para a saída do OMS, edite a configuração do agente geral no arquivo `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

Na parte inferior do arquivo de configuração, altere a propriedade `log_level` de `info` para `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

O log de depuração permite que você veja os carregamentos em lote para o Serviço OMS separados por tipo, por número de itens de dados e pelo tempo necessário para enviar.

*Log habilitado para depuração de exemplo:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Saída detalhada
Em vez de usar o plug-in de saída do OMS, você também poderá gerar itens de dados diretamente para o `stdout`, que fica visível no arquivo de log do Agente do OMS para Linux.

No arquivo de configuração do agente geral do OMS no `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, comente o plug-in de saída do OMS ao adicionar um `#` na frente de cada linha.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Abaixo do plug-in de saída, retire o comentário da seção a seguir removendo o símbolo `#` do início de cada linha.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>As mensagens do Syslog encaminhadas não aparecem no log
#### <a name="probable-causes"></a>Causas prováveis
* A configuração aplicada ao servidor Linux não permite a coleta dos recursos e/ou dos níveis de log enviados
* O Syslog não está sendo encaminhado corretamente para o servidor Linux
* O número de mensagens encaminhadas por segundo é muito grande para a configuração de base do Agente do OMS para Linux lidar

#### <a name="resolutions"></a>Resoluções
* Verifique se a configuração no Portal do OMS para Syslog tem todos os recursos e os níveis de log corretos
  * **Portal do OMS > Configurações > Dados > Syslog**
* Verifique se esses daemons de mensagens de syslog nativos (`rsyslog`, `syslog-ng`) são capazes de receber as mensagens encaminhadas
* Verifique as configurações de firewall no servidor Syslog para garantir que as mensagens não estejam sendo bloqueadas
* Simule uma mensagem do Syslog para o OMS usando o comando `logger` - por exemplo:
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Problemas de conexão do OMS quando um proxy é utilizado
#### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado na instalação e na configuração do agente está incorreto
* Os pontos de extremidade do Serviço OMS não estão na lista branca do seu datacenter

#### <a name="resolutions"></a>Resoluções
* Reinstale o Agente do OMS para Linux usando o comando a seguir com a opção `-v` habilitada. Isso permite a saída detalhada do agente que está se conectando por meio do proxy ao Serviço OMS.
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * Leia a documentação do proxy do OMS em [Configurando o agente para uso com um servidor proxy HTTP](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server)
* Verifique se os seguintes pontos de extremidade do Serviço OMS estão na lista de permissões

| Recurso de agente | Portas |
| --- | --- |
| &#42;.ods.opinsights.azure.com |Porta 443 |
| &#42;.oms.opinsights.azure.com |Porta 443 |
| ods.systemcenteradvisor.com |Porta 443 |
| &#42;.blob.core.windows.net/ |Porta 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>É exibido um erro 403 durante a integração
#### <a name="probable-causes"></a>Causas prováveis
* A data e a hora estão incorretas no servidor Linux
* A ID e a Chave do Espaço de Trabalho usadas estão incorretas

#### <a name="resolution"></a>Resolução
* Verifique a hora no servidor Linux com o comando `date`. Se os dados forem maiores ou menores do que 15 minutos da hora atual, a integração falhará. Para corrigir esse problema, atualize a data e/ou o fuso horário do servidor Linux.
* A versão mais recente do Agente do OMS para Linux notifica se houver uma diferença de hora causando a falha de integração
* Reintegração usando a ID do Espaço de Trabalho e a Chave do Espaço de Trabalho. Confira [Integração usando a linha de comando](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) para saber mais.

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>Um erro 500 ou erro 404 aparece no arquivo de log após a integração
Isso é um problema conhecido que ocorre durante o primeiro carregamento de dados do Linux em um espaço de trabalho do OMS. Ele não afeta os dados que estão sendo enviados ou outros problemas. Você pode ignorar os erros no início da integração.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Os dados do Nagios não aparecem no Portal do OMS
#### <a name="probable-causes"></a>Causas prováveis
* O usuário omsagent não tem permissões para ler o arquivo de log do Nagios
* As seções de origem e de filtro do Nagios ainda estão comentadas no arquivo omsagent.conf

#### <a name="resolutions"></a>Resoluções
* Adicione o usuário omsagent para ler o arquivo do Nagios. Confira [Alertas do Nagios](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) para saber mais.
* No arquivo de configuração geral do Agente do OMS para Linux no `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, verifique se **ambas** as seções de origem e de filtro do Nagios tiveram os comentários removidos, semelhante ao exemplo a seguir.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Os dados do Linux não aparecem no Portal do OMS
#### <a name="probable-causes"></a>Causas prováveis
* Falha na integração com o Serviço OMS
* A conexão com o Serviço OMS está bloqueada
* O backup dos dados do Agente do OMS para Linux foi feito

#### <a name="resolutions"></a>Resoluções
* Verifique se essa integração ao Serviço OMS foi bem-sucedida ao verificar a existência do `/etc/opt/microsoft/omsagent/conf/omsadmin.conf`.
* Reintegração usando a linha de comando omsadmin.sh. Confira [Integração usando a linha de comando](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) para saber mais.
* Se estiver usando um proxy, utilize as etapas de solução de problemas do proxy acima
* Em alguns casos, quando o Agente do OMS para Linux não puder se comunicar com o Serviço OMS, será feito backup dos dados do Agente até o tamanho total do buffer, 50 MB. Reinicie o Agente do OMS para Linux executando os comandos `service omsagent restart` ou `systemctl restart omsagent`.
  >[AZURE.NOTE] Esse problema foi corrigido nas versões 1.1.0-28 e posteriores do Agente.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>A configuração do contador de desempenho do Syslog do Linux não é aplicada no portal do OMS
#### <a name="probable-causes"></a>Causas prováveis
* O agente de configuração do Agente do OMS para Linux não recuperou a configuração mais recente do portal do OMS.
* As configurações revisadas no portal não foram aplicadas

#### <a name="resolutions"></a>Resoluções
`omsconfig` é o agente de configuração do Agente do OMS para Linux que recupera alterações de configuração do portal do OMS a cada cinco minutos. Essa configuração é então aplicada aos arquivos de configuração do Agente do OMS para Linux localizados em `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

* Em alguns casos, o agente de configuração do Agente do OMS para Linux pode não conseguir se comunicar com o serviço de configuração do portal, fazendo com que a configuração mais recente não seja aplicada.
* Verifique se o agente `omsconfig` está instalado com o seguinte:
  
  * `dpkg --list omsconfig` ou `rpm -qi omsconfig`
  * Se não estiver instalado, reinstale a versão mais recente do Agente do OMS para Linux
* Verifique se o agente `omsconfig` pode se comunicar com o serviço OMS
  
  * Execute o comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`
    * O comando anterior retorna a configuração que o agente recupera do portal, incluindo configurações do Syslog, contadores de desempenho do Linux e logs personalizados
    * Se o comando acima falhar, execute o comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Esse comando força o agente omsconfig a se comunicar com o Serviço OMS para recuperar a configuração mais recente.

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>Os dados de log personalizado do Linux não aparecem no Portal do OMS
#### <a name="probable-causes"></a>Causas prováveis
* Falha na integração com o Serviço OMS
* A configuração **Aplicar a configuração a seguir aos meus Servidores Linux** não foi selecionada
* O omsconfig não coletou o log personalizado mais recente do portal
* O uso do `omsagent` é incapaz de acessar o log personalizado devido a um problema de permissões ou `omsagent` não foi encontrado. Nesse caso, você verá a seguinte saída:
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* Esse é um problema conhecido como Condição de Corrida, corrigido na versão 1.1.0-217 do Agente do OMS para Linux

#### <a name="resolutions"></a>Resoluções
* Verifique se a integração obteve êxito ao determinar se o arquivo `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` existe.
  * Se necessário, integre novamente usando a linha de comando omsadmin.sh. Confira [Integração usando a linha de comando](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) para saber mais.
* No Portal do OMS, em **Configurações** na guia **Dados**, verifique se a configuração **Aplicar a configuração a seguir aos meus servidores Linux** está selecionada  
  ![aplicar configuração](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* Verifique se o agente `omsconfig` pode se comunicar com o serviço OMS
  
  * Execute o comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`
  * O comando anterior retorna a configuração que o agente recupera do Portal, incluindo configurações do Syslog, contadores de desempenho do Linux e Logs personalizados
  * Se o comando acima falhar, execute o comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Esse comando força o agente omsconfig a se comunicar com o serviço OMS e recuperar a configuração mais recente.

Em vez do usuário do Agente do OMS para Linux executar como um usuário `root` com privilégios, o Agente do OMS para Linux é executado como o usuário `omsagent`. Na maioria dos casos, a permissão explícita deve ser concedida para que o usuário leia determinados arquivos.

Para conceder permissão para o usuário `omsagent`, execute os seguintes comandos:

1. Adicione o usuário `omsagent` a um grupo específico com `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Conceda acesso universal de leitura para o arquivo necessário com `sudo chmod -R ugo+rw <FILE DIRECTORY>`

Há um problema conhecido com a Condição de Corrida que foi corrigido na versão 1.1.0-217 do Agente do OMS para Linux. Após a atualização para o agente mais recente, execute o seguinte comando para obter a versão mais recente do plug-in de saída:

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/conf/omsagent.conf
```

## <a name="known-limitations"></a>Limitações conhecidas
Examine as seções a seguir para saber mais sobre as limitações atuais do Agente do OMS para Linux.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure
Para as máquinas virtuais do Linux em execução no Azure, podem ser necessárias etapas adicionais para permitir a coleta de dados pelo Diagnóstico do Azure e pelo Operations Management Suite. **Versão 2.2** da Extensão de Diagnóstico para Linux é necessária para compatibilidade com o Agente do OMS para Linux.

Para saber mais sobre como instalar e configurar a Extensão de Diagnóstico para Linux, confira [Usar o comando CLI do Azure para habilitar a Extensão de Diagnóstico do Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension).

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

### <a name="sysklog-is-not-supported"></a>Não há suporte para Sysklog.
Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog. Para saber mais sobre como substituir o sysklog pelo rsyslog, veja [Instalar o RPM de rsyslog recém-criado](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM).

## <a name="next-steps"></a>Próximas etapas
* [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidades e reunir dados.
* Familiarize-se com as [pesquisas de log](log-analytics-log-searches.md) para exibir informações detalhadas reunidas por soluções.
* Use [painéis](log-analytics-dashboards.md) para salvar e exibir suas próprias pesquisas personalizadas.




<!--HONumber=Nov16_HO3-->



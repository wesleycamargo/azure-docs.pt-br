---
title: "Configurar o Mapa do Serviço no OMS (Operations Management Suite) | Microsoft Docs"
description: "O Mapa do Serviço é uma solução do OMS (Operations Management Suite) que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços.  Este artigo fornece detalhes sobre a implantação do Mapa do Serviço em seu ambiente e sobre como usá-lo em diversos cenários."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 1ce47043f85e30f616c8b22e1107b192d4962d8a
ms.openlocfilehash: 73c35da427f1e2080ab6fdd086d3168dad495415
ms.lasthandoff: 02/16/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>Configurar a solução Mapa do Serviço no OMS (Operations Management Suite)
O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais.  O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente.

Este artigo descreve os detalhes sobre a configuração do Mapa do Serviço e dos agentes de integração.  Para saber mais sobre como usar o Mapa do Serviço, confira [Como usar a solução Mapa do Serviço no OMS (Operations Management Suite)](operations-management-suite-service-map.md)


## <a name="connected-sources"></a>Fontes conectadas
O Mapa de Serviço obtém seus dados do Microsoft Dependency Agent.  O Dependency Agent depende do agente do OMS para suas conexões ao OMS.  Isso significa que um servidor deve ter o agente do OMS instalado e configurado primeiro e, em seguida, o Dependency Agent pode ser instalado.  A tabela a seguir descreve as fontes conectadas com suporte da solução Mapa do Serviço.

| Fonte Conectada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | O Mapa do Serviço analisa e coleta dados de computadores de agente do Windows.  <br><br>Além do [Agente do OMS](../log-analytics/log-analytics-windows-agents.md), os agentes do Windows exigem o Microsoft Dependency Agent.  Veja os [Sistemas operacionais com suporte](#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Agentes do Linux | Sim | O Mapa do Serviço analisa e coleta dados de computadores de agente do Linux.  <br><br>Além do [Agente do OMS](../log-analytics/log-analytics-linux-agents.md), os agentes do Linux exigem o Microsoft Dependency Agent.  Veja os [Sistemas operacionais com suporte](#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Grupo de gerenciamento do SCOM | Sim | O Mapa do Serviço analisa e coleta dados de agentes do Windows e do Linux em um [grupo de gerenciamento do SCOM (System Center Operations Manager)](../log-analytics/log-analytics-om-agents.md) conectado. <br><br>É necessária uma conexão direta do computador do agente do SCOM ao OMS. Os dados são enviados diretamente do grupo de gerenciamento para o repositório do OMS.|
| Conta de Armazenamento do Azure | Não | O Mapa do Serviço coleta dados de computadores do agente e, portanto, não há nenhum dado dele a ser coletado do armazenamento do Azure. |

O Mapa do Serviço só oferece suporte a plataformas de 64 bits.

No Windows, o MMA (Agente de Monitoramento da Microsoft) é usado pelo SCOM e pelo OMS para coletar e enviar dados de monitoramento.  (Esse agente é chamado de Agente do SCOM, Agente do OMS, MMA ou Agente Direto, dependendo do contexto).  O SCOM e o OMS fornecem versões prontas diferentes do MMA, mas essas versões podem se reportar ao SCOM, ao OMS ou a ambos.  No Linux, o Agente do OMS para Linux coleta e envia os dados de monitoramento para o OMS.  Você pode usar o Mapa do Serviço em servidores com Agentes Diretos do OMS ou em servidores que estejam conectados ao OMS por meio de grupos de gerenciamento do SCOM.  Nesta documentação, vamos nos referir a todos os agentes – no Linux ou no Windows, se conectados a um SCOM MG ou diretamente ao OMS – como o "Agente do OMS", a menos que o nome de implantação específico do agente seja necessário para o contexto.

O agente do Mapa do Serviço não transmite todos os dados em si e não requer alterações em portas ou em firewalls.  Os dados do Mapa do Serviço sempre são transmitidos pelo Agente do OMS para o OMS, diretamente ou por meio do Gateway do OMS.

![Agentes do Mapa do Serviço](media/oms-service-map/agents.png)

Se você for um cliente do SCOM com um grupo de gerenciamento conectado ao OMS:

- Se os agentes do SCOM puderem acessar a Internet para se conectar ao OMS, nenhuma configuração adicional será necessária.  
- Se os agentes do SCOM não puderem acessar o OMS pela Internet, será necessário configurar o Gateway do OMS para trabalhar com o SCOM.
  
Se você estiver usando o Agente Direto do OMS, precisará configurar o próprio Agente de OMS para que ele se conecte ao OMS ou ao Gateway do OMS.  O Gateway do OMS pode ser baixado de [https://www.microsoft.com/pt-br/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Evitar dados duplicados

Se você for um cliente do SCOM, não deverá configurar os agentes do SCOM para se comunicar diretamente com o OMS ou os dados serão relatados duas vezes.  No Mapa do Serviço, isso resultará em computadores aparecendo duas vezes na Lista de Computadores.

A configuração do OMS só deve acontecer em um dos seguintes locais:

- O painel SCOM Console Operations Management Suite para Computadores Gerenciados
- Configuração do Azure Operational Insights nas propriedades do MMA

Usar as duas configurações com o *mesmo* espaço de trabalho causará a duplicação de dados. Usar as duas configurações com espaços de trabalho *diferentes* pode resultar em configuração conflitante (uma com a solução Mapa do Serviço habilitada e a outra sem), o que poderá impedir que os dados fluam para o Mapa do Serviço.  

Mesmo se o próprio computador não for especificado na configuração de OMS do Console do SCOM, se um grupo de instâncias, como o "Grupo de Instâncias do Windows Server" estiver ativo, ainda poderá fazer com que o computador receba a configuração do OMS via SCOM.



## <a name="management-packs"></a>Pacotes de gerenciamento
Quando o Mapa do Serviço é ativado em um espaço de trabalho do OMS, um Pacote de Gerenciamento de 300 KB é enviado para todos os Agentes de Monitoramento da Microsoft no espaço de trabalho.  Se você estiver usando agentes do SCOM em um [grupo de gerenciamento conectado](../log-analytics/log-analytics-om-agents.md), o Pacote de Gerenciamento do Mapa do Serviço será implantado do SCOM.  Se os agentes estiverem diretamente conectados, o Pacote de Gerenciamento será entregue pelo OMS.

O Pacote de Gerenciamento chama-se Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Ele é gravado em *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\*.  A fonte de dados usada pelo pacote de gerenciamento é *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuração
Além da instalação de um agente conectado ao OMS nos computadores Windows e Linux, o instalador do Dependency Agent deverá ser baixado da solução Mapa do Serviço e instalado como root ou Administrador em cada servidor gerenciado.  Depois que o agente do Mapa do Serviço for instalado em um servidor que esteja se reportando ao OMS, os mapas de dependências do Mapa do Serviço aparecerão em 10 minutos.


### <a name="migrating-from-bluestripe-factfinder"></a>Migração do BlueStripe FactFinder
O Mapa do Serviço entregará a tecnologia BlueStripe ao OMS em fases. O FactFinder ainda tem suporte para os clientes existentes, mas não está mais disponível para compra individual.  Esta versão de visualização do Dependency Agent só pode se comunicar com o OMS.  Se você for um cliente do FactFinder, identifique um conjunto de servidores de teste para o Mapa do Serviço que não sejam gerenciados pelo FactFinder. 

### <a name="download-the-dependency-agent"></a>Baixar o Dependency Agent
Além do MMA (Agente de Monitoramento da Microsoft) e do Agente do OMS para Linux, que fornecem a conexão entre o computador e o OMS, todos os computadores analisados pelo Mapa do Serviço deverão ter o Dependency Agent instalado.  No Linux, o Agente do OMS para Linux deve ser instalado antes do Dependency Agent. 

![Bloco Mapa do Serviço](media/oms-service-map/additional_configuration.png)

Para baixar o Dependency Agent, clique em **Configurar Solução** no bloco **Mapa do Serviço** para abrir a folha **Dependency Agent**.  A folha Dependency Agent tem links para os agentes do Windows e do Linux. Veja as seções a seguir para obter detalhes sobre como instalar o agente em sistemas diferentes.

### <a name="install-the-dependency-agent"></a>Instalar o Dependency Agent

#### <a name="microsoft-windows"></a>Microsoft Windows
São necessários privilégios de administrador para instalar ou desinstalar o agente.

O Dependency Agent é instalado em computadores com o Windows com InstallDependencyAgent-Windows.exe. Se você executar o arquivo executável sem opções, ele iniciará um assistente que você poderá seguir para executar a instalação interativamente.  

Use as etapas a seguir para instalar o Dependency Agent em cada computador com o Windows:

1.    Certifique-se de que o Agente do OMS seja instalado usando as instruções em Conectar computadores diretamente ao OMS.
2.    Baixe o agente do Windows e o execute com o comando a seguir: <br>*InstallDependencyAgent-Windows.exe*
3.    Acompanhe o assistente para instalar o agente.
4.    Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em Agentes do Windows, o diretório de log será *C:\Arquivos de Programas\Microsoft Dependency Agent\logs*. 

O Dependency Agent para Windows pode ser desinstalado por um administrador por meio do Painel de Controle.


#### <a name="linux"></a>Linux
O acesso root é necessário para instalar ou configurar o agente.

O Dependency Agent é instalado em computadores com o Linux com InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração automática. Você pode executar o arquivo com sh ou adicionar permissões de execução ao próprio arquivo.
 
Use as seguintes etapas para instalar o Dependency Agent em cada computador com o Linux:

1.    Certifique-se de que o Agente do OMS seja instalado usando as instruções em [Coletar e gerenciar dados de computadores com o Linux.  O Agente do OMS deve ser instalado antes do Dependency Agent para Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.    Instale o Dependency Agent para Linux como root usando o comando a seguir:<br>*sh InstallDependencyAgent-Linux64.bin*.
3.    Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Desinstalação do Dependency Agent no Linux
Para desinstalar completamente o Dependency Agent do Linux, você deve remover o agente propriamente dito e o Conector instalado automaticamente com o agente.  Você pode desinstalar ambos com o seguinte comando único:

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Instalação de uma linha de comando
A seção anterior fornece orientação sobre como instalar o agente do Monitor da Dependência usando as opções padrão.  As seções a seguir fornecem diretrizes para instalar o agente de uma linha de comando usando opções personalizadas.

#### <a name="windows"></a>Windows
Use as opções da tabela a seguir para instalar a partir de uma linha de comando. Para ver uma lista dos sinalizadores de instalação, execute o instalador com o sinalizador /? da seguinte maneira.

    InstallDependencyAgent-Windows.exe /?

| Sinalizador | Descrição |
|:--|:--|
| /S | Realize uma instalação silenciosa sem solicitações ao usuário. |

Os arquivos do Windows Dependency Agent são colocados em *C:\Arquivos de Programas\Microsoft Dependency Agent* por padrão.


#### <a name="linux"></a>Linux
Use as opções da tabela a seguir para a instalação. Para ver uma lista dos sinalizadores de instalação, execute o programa de instalação com o sinalizador -help da seguinte maneira.

    InstallDependencyAgent-Linux64.bin -help

| Descrição do sinalizador
|:--|:--|
| -s | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Os arquivos do Dependency Agent são colocados nos diretórios a seguir:

| Arquivos | Local |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>Solucionar problemas
Se você tiver problemas com o Mapa do Serviço, poderá reunir informações de solução de problemas de vários componentes usando as informações a seguir.

### <a name="windows-agents"></a>Agentes do Windows

#### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
Para gerar dados de solução de problemas do Dependency Agent, abra um Prompt de Comando como administrador e execute o script CollectDependencyAgentData.vbs usando o comando a seguir.  Você pode adicionar o sinalizador --help para exibir opções adicionais.

    cd C:\Program Files\Microsoft Dependency Agent\scripts
    cscript CollectDependencyData.vbs

O Pacote de Dados de Suporte é salvo no diretório %USERPROFILE% do usuário atual.  Você pode usar a opção --file <filename> para salvá-lo em um local diferente.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Microsoft Dependency Agent Management Pack para MMA
O Dependency Agent Management Pack é executado dentro do Microsoft Management Agent.  Ele recebe dados do Dependency Agent e os encaminha para o serviço de nuvem do Mapa do Serviço.
  
Verifique se o pacote de gerenciamento foi baixado ao executar as etapas a seguir:

1.    Procure um arquivo chamado Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp in C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.  
2.    Se o arquivo não estiver presente e se o agente estiver conectado a um grupo de gerenciamento do SCOM, verifique se ele foi importado para o SCOM ao verificar os Pacotes de Gerenciamento do espaço de trabalho de Administração do Console de Operações.

O Pacote de Gerenciamento do Mapa do Serviço grava os eventos no log de eventos do Windows no Operations Manager.  O log pode ser [pesquisado no OMS](../log-analytics/log-analytics-log-searches.md) por meio da solução de log do sistema, onde você pode configurar quais arquivos de log serão carregados.  Se os eventos de depuração estiverem habilitados, eles serão gravados no log de eventos do aplicativo, com a origem do evento *ADMConnector*.

#### <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Para coletar rastreamentos de diagnóstico, abra um Prompt de Comando como administrador e execute os seguintes comandos: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Os rastreamentos são gravados em c:\Windows\Logs\OpsMgrTrace.  Você pode parar o rastreamento com Stoptracing.cmd.


### <a name="linux-agents"></a>Agentes do Linux

#### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
Para gerar dados de solução de problemas do Dependency Agent, faça logon com uma conta que tenha privilégios sudo ou root e execute o comando a seguir.  Você pode adicionar o sinalizador --help para exibir opções adicionais.

    /opt/microsoft/dependency-agent/lib/scripts/collect-dependency-agent-data.sh

O pacote de suporte de dados é salvo em /var/opt/microsoft/dependency-agent/log (se for o root) no diretório de instalação do agente ou no diretório base do usuário que executa o script (se não for o root).  Você pode usar a opção --file <filename> para salvá-lo em um local diferente.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Plug-in Fluentd do Microsoft Dependency Agent para Linux
O Plug-in Fluentd do Dependency Agent é executado dentro do Agente do OMS para Linux.  Ele recebe dados do Dependency Agent e os encaminha para o serviço de nuvem do Mapa do Serviço.  

Os logs são gravados nos dois arquivos a seguir.

- /var/opt/microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agente do OMS para Linux
Um recurso de solução de problemas para conectar servidores Linux ao OMS pode ser encontrado aqui: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Os logs do Agente do OMS para Linux estão localizados em */var/opt/microsoft/omsagent/log/*.  

Os logs do omsconfig (configuração do agente) estão localizados em */var/opt/microsoft/omsconfig/log/*.
 
O log dos componentes OMI e SCX que fornecem dados de métrica de desempenho estão localizados em */var/opt/omi/log/* e em */var/opt/Microsoft/scx/log.*.


## <a name="data-collection"></a>Coleta de dados
Você pode esperar que cada agente transmita aproximadamente 25 MB por dia, dependendo da complexidade das dependências do seu sistema.  Os dados de dependência do Mapa do Serviço são enviados por cada agente a cada 15 segundos.  

O Dependency Agent geralmente consome 0,1% da memória do sistema e 0,1% da CPU do sistema.


## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent.   Não há suporte para as arquiteturas de&32; bits de qualquer sistema operacional.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Área de Trabalho do Windows
- WIndows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com Kernel RHEL)
- Somente as versões de kernel padrão e Linux SMP têm suporte.
- As versões de kernel não-padrão, como PAE e Xen, não têm suporte para qualquer distribuição do Linux. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão "2.6.16.21-0.8-xen".
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão
- Não há suporte para o kernel do CentOS Plus.
- O Oracle Unbreakable Kernel (UEK) será abordado em outra seção a seguir.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versão do SO | Versão do kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versão do SO | Versão do kernel |
|:--|:--|
| 6,0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versão do SO | Versão do kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux com Unbreakable Kernel (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versão do SO | Versão do kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versão do SO | Versão do kernel
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versão do SO | Versão do kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versão do SO | Versão do kernel
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Mapa do Serviço. A Microsoft usa esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço Mapa do Serviço. Os dados incluem informações sobre a configuração do software, como o sistema operacional e a versão, e também incluem o endereço IP, o nome DNS e nome da Estação de Trabalho a fim de fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para saber mais sobre o uso e a coleta de dados, veja a [Declaração de Privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Próximas etapas
- Saiba como [usar o Mapa do Serviço](operations-management-suite-service-map.md) depois que ele tiver sido implantado e configurado.


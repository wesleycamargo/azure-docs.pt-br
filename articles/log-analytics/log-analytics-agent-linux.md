---
title: Conectar computadores Linux ao Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como conectar computadores Linux hospedados no Azure, em outra nuvem ou localmente ao Log Analytics com o Agente do OMS para Linux.
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: magoedte
ms.openlocfilehash: 56c666d1a18937df21a6aca8acde87beda1cad8e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Conectar computadores Linux ao Log Analytics 

Com o Azure Log Analytics, você pode coletar e agir sobre os dados gerados em computadores Linux e soluções de contêiner como o Docker, residindo em seu data center local como servidores físicos ou máquinas virtuais, máquinas virtuais em um serviço hospedado em nuvem como AWS (Amazon Web Services) ou Microsoft Azure. Você também pode usar soluções de gerenciamento disponíveis na [Automação do Azure](../automation/automation-intro.md), tais como o Controle de Alterações, para identificar alterações de configuração e Gerenciamento de Atualizações para gerenciar atualizações de software para gerenciar proativamente o ciclo de vida das VMs Linux. 

O Agente do OMS para Linux se comunica na saída com os serviços Log Analytics e Automação do Azure na porta TCP 443 e, caso o computador se conecte a um firewall ou servidor proxy para se comunicar pela Internet, examine [Configurando o agente para uso com um servidor proxy ou Gateway do OMS](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) para entender quais alterações de configuração precisarão ser aplicadas.  Se você estiver monitorando o computador com o System Center 2016 – Operations Manager ou Operations Manager 2012 R2, ele poderá ser multihomed com o serviço Log Analytics para coletar dados e encaminhe para o serviço e ainda ser monitorado pelo Operations Manager.  Os computadores Linux monitorados por um grupo de gerenciamento do Operations Manager integrado ao Log Analytics (atualmente referenciado como Operations Management Suite no console de Operações do Operations Manager) não recebem a configuração de fontes de dados e encaminham os dados coletados pelo grupo de gerenciamento.  O agente do OMS não pode ser configurado para se reportar a mais de um espaço de trabalho do Log Analytics.  

Se suas políticas de segurança não permitem que computadores em sua rede se conectem à Internet, o agente pode ser configurado para se conectar ao Gateway do OMS para receber informações de configuração e enviar os dados coletados dependendo da solução habilitada. Para obter mais informações e etapas sobre como configurar o Agente do OMS para Linux para se comunicar através de um Gateway do OMS aos serviços, consulte [Conectar computadores ao OMS usando o Gateway do OMS](log-analytics-oms-gateway.md).  

O diagrama a seguir ilustra a conexão entre os computadores Linux gerenciados por agente e o Log Analytics, incluindo a direção e as portas.

![diagrama Comunicação do agente direto com os Serviços do Azure](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Requisitos do sistema
Antes de começar, examine os detalhes a seguir para verificar se você atende aos pré-requisitos.

### <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte
As seguintes distribuições Linux têm suporte oficialmente.  No entanto, o Agente do OMS para Linux também pode ser executado em outras distribuições não listadas.

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

### <a name="network"></a>Rede
As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente do Linux se comunique com o Log Analytics e a Automação do Azure. O tráfego é de saída da rede para o serviço. 

|Recurso de agente| Portas |  
|------|---------|  
|*.ods.opinsights.azure.com | Porta 443|   
|*.oms.opinsights.azure.com | Porta 443|   
|*.blob.core.windows.net/ | Porta 443|   
|*.azure-automation.net | Porta 443|  

### <a name="package-requirements"></a>Requisitos do pacote

 **Pacote necessário**   | **Descrição**   | **Versão mínima**
--------------------- | --------------------- | -------------------
Glibc | Biblioteca GNU C   | 2.5-12 
Openssl | Bibliotecas OpenSSL | 0.9.8e ou 1.0
Curl | cliente Web cURL | 7.15.5
Python-ctypes | | 
PAM | Módulos de autenticação conectáveis | 

> [!NOTE]
>  Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão dessas distribuições, o daemon rsyslog deve ser instalado e configurado para substituir sysklog. 

O agente inclui vários pacotes. O arquivo de versão contém os seguintes pacotes, disponíveis por meio da execução do pacote do shell com `--extract`:

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.4.0 | O Agente do Operations Management Suite para Linux
omsconfig | 1.1.1 | Agente de configuração para o Agente do OMS
omi | 1.2.0 | OMI (Open Management Infrastructure) – um servidor CIM leve
scx | 1.6.3 | Provedores de CIM OMI para métricas de desempenho do sistema operacional
apache-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Servidor HTTP Apache para OMI. Instalado se o Servidor HTTP Apache for detectado.
mysql-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Servidor MySQL para OMI. Instalado se o servidor MySQL/MariaDB for detectado.
docker-cimprov | 1.0.0 | Provedor do Docker para OMI. Instalado se o Docker for detectado.

### <a name="compatibility-with-system-center-operations-manager"></a>Compatibilidade com o System Center Operations Manager
O Agente do OMS para Linux compartilha arquivos binários de agente com o agente do System Center Operations Manager. Se você instalar o Agente do OMS para Linux em um sistema atualmente gerenciado pelo Operations Manager, ele atualiza os pacotes OMI e SCX no computador para uma versão mais recente. Nesta versão, o OMS e o System Center 2016 – agentes do Operations Manager/Operations Manager 2012 R2 para Linux são compatíveis. 

> [!NOTE]
> O System Center 2012 SP1 e versões anteriores atualmente não são compatíveis com o Agente do OMS para Linux ou não têm suporte.<br>
> Se o Agente do OMS para Linux for instalado em um computador que não é monitorado pelo Operations Manager no momento e você desejar monitorar o computador com o Operations Manager, será necessário modificar a [configuração do OMI](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) antes de descobrir o computador. **Esta etapa não *será* necessária se o agente do Operations Manager for instalado antes do Agente do OMS para Linux.**

### <a name="system-configuration-changes"></a>Alterações de configuração do sistema
Após a instalação dos pacotes do Agente do OMS para Linux, serão aplicadas as seguintes alterações de configuração adicionais em todo o sistema. Esses artefatos serão removidos quando o pacote omsagent for desinstalado.

* Um usuário sem privilégios chamado: `omsagent` é criado. O daemon omsagent é executado como essa conta.
* Um arquivo "include" sudoers é criado em /etc/sudoers.d/omsagent. Isso autoriza o omsagent a reiniciar os daemons syslog e omsagent. Se o sudo incluir diretivas sem suporte na versão instalada do sudo, essas entradas serão gravadas em /etc/sudoers.
* A configuração de syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para saber mais, confira a seção **Configuração da Coleta de Dados** abaixo

### <a name="upgrade-from-a-previous-release"></a>Atualizar de uma versão anterior
A atualização de versões anteriores à 1.0.0-47 tem suporte nesta versão. Executar a instalação com o comando `--upgrade` atualizará todos os componentes do agente para a versão mais recente.

## <a name="installing-the-agent"></a>Instalando o agente

Esta seção descreve como instalar o agente do OMS para Linux manualmente usando um pacote, que contém pacotes Debian e RPM para cada um dos componentes do agente.  Ele pode ser instalado diretamente ou extraído para recuperar os pacotes individuais.  Se você planeja instalar o agente em uma VM Linux do Azure, consulte o tópico [Coletar dados sobre as máquinas virtuais do Azure](log-analytics-quick-collect-azurevm.md) a seguir para saber como instalar o agente usando a extensão de VM do Log Analytics.  Siga as etapas na seção *Habilitar a Extensão de VM do Log Analytics*.  Para computadores Linux hospedados no seu ambiente, você pode simplificar o processo de instalação usando um método de script descrito no artigo de [Coletar dados de computadores Linux hospedados no seu ambiente](log-analytics-quick-collect-linux-computer.md).  

> [!NOTE]
> Embora os dois artigos mencionados acima se destinem a fazer com que alguém não familiarizado com o Log Analytics comece a usar o serviço rapidamente, as etapas para configuração do computador são relevantes.  Se você já tem um espaço de trabalho e busca conectar o computador Linux, selecione um espaço de trabalho existente caso seja uma VM Linux do Azure ou, para um computador hospedado fora do Azure, copie a chave e a ID do espaço de trabalho para passar ao script.  

Antes de instalar o agente do OMS para Linux, você precisa da ID do espaço de trabalho e da chave para o espaço de trabalho do Log Analytics.  

1. Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. Em sua lista de espaços de trabalho do Log Analytics, selecione aquele para o qual você deseja que o computador envie relatórios.
3. Selecione **Configurações avançadas**.<br><br> ![Configurações avançadas do Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Fontes Conectadas** e, em seguida, selecione **Servidores Linux**.   
5. O valor à direita da **ID do Espaço de Trabalho** e **Chave Primária**. Copie e cole os dois em seu editor favorito.  
6. Baixe o versão mais recente [agente do OMS para Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) ou [agente do OMS para Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) do GitHub.  
7. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
8. Instale o pacote usando o argumento `--install` ou `--upgrade`. 

    > [!NOTE]
    > Se houver qualquer pacote existente instalado, como quando o agente do System Center Operations Manager para Linux já está instalado, use o argumento `--upgrade`. Para se conectar ao Operations Management Suite durante a instalação, forneça os parâmetros `-w <WorkspaceID>` e `-s <Shared Key>`.


#### <a name="to-install-and-onboard-directly"></a>Para instalar e integrar diretamente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Para atualizar o pacote do agente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Para instalar e integrar para um espaço de trabalho no Nuvem do Governo dos EUA
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>Configurando o agente para ser usado com um servidor proxy ou um gateway do OMS
O Agente do OMS para Linux dá suporte à comunicação por meio de um servidor proxy ou de um gateway do OMS para o serviço Log Analytics usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  

### <a name="proxy-configuration"></a>Configuração de Proxy
O valor de configuração de proxy tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

Propriedade|Descrição
-|-
Protocolo|HTTPS
usuário|Nome de usuário opcional para autenticação de proxy
Senha|Senha opcional para autenticação de proxy
proxyhost|Endereço ou FQDN do servidor proxy/Gateway do OMS
porta|Número da porta opcional para o servidor proxy/OMS do Gateway

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

O servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf após a instalação.   

### <a name="specify-proxy-configuration-during-installation"></a>Especificar a configuração de proxy durante a instalação
O argumento `-p` ou `--proxy` para o pacote de instalação omsagent especifica a configuração de proxy a ser usada. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Definir a configuração de proxy em um arquivo
A configuração de proxy pode ser definida nos arquivos `/etc/opt/microsoft/omsagent/proxy.conf` e `/etc/opt/microsoft/omsagent/conf/proxy.conf `. Os arquivos podem ser criados ou editados diretamente, mas as permissões devem ser atualizadas para conceder ao usuário omiuser as permissões de leitura nos arquivos. Por exemplo:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Removendo a configuração de proxy
Para remover uma configuração de proxy definida anteriormente e reverter para a conectividade direta, remova o arquivo proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-log-analytics"></a>Integração com o Log Analytics
Se uma ID e chave do espaço de trabalho não foram fornecidas durante a instalação do pacote, o agente deverá ser subsequentemente registrado com o Log Analytics.

### <a name="onboarding-using-the-command-line"></a>Integração usando a linha de comando
Execute o comando omsadmin.sh fornecendo a ID e a chave do espaço de trabalho. Este comando deve ser executado como raiz (com elevação sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="register-using-a-file"></a>Registrar-se usando um arquivo
1.  Crie o arquivo `/etc/omsagent-onboard.conf`. O arquivo deve ser legível e gravável para a raiz.
`sudo vi /etc/omsagent-onboard.conf`
2.  Insira as seguintes linhas no arquivo com a ID e a Chave Compartilhada do Espaço de Trabalho:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Execute o seguinte comando para registrar-se no Log Analytics: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  O arquivo é excluído na integração bem-sucedida.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Habilitar o Agente do OMS para Linux para reportar para o System Center Operations Manager
Execute as seguintes etapas para configurar o Agente do OMS para Linux para reportar para um grupo de gerenciamento do System Center Operations Manager.  

1. Edite o arquivo `/etc/opt/omi/conf/omiserver.conf`
2. Verifique se a linha que começa com **httpsport=** define a porta 1270. Como: `httpsport=1270`
3. Reinicie o servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Logs do Agente
Os logs do Agente do OMS para Linux podem ser encontrados em: `/var/opt/microsoft/omsagent/<workspace id>/log/` Os logs do programa omsconfig (configuração do agente) pode ser encontrado em: `/var/opt/microsoft/omsconfig/log/` Os Logs dos componentes OMI e SCX (que fornecem dados de métrica de desempenho) podem ser encontrados em: `/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Configuração de rotação de log##
A configuração de rotação de log para omsagent pode ser encontrada em: `/etc/logrotate.d/omsagent-<workspace id>`

As configurações padrão são: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Desinstalar o Agente do OMS para Linux
Os pacotes de agente podem ser desinstalados por meio da execução do arquivo bundle.sh com o argumento `--purge`, que remove completamente o agente e a configuração do computador.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problema: não é possível se conectar por meio do proxy ao Log Analytics

#### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* Os pontos de extremidade de serviço do Log Analytics e da Automação do Azure não estão na lista de permissões em seu datacenter 

#### <a name="resolutions"></a>Resoluções
1. Reintegre-se ao serviço Log Analytics com o Agente do OMS para Linux usando o comando a seguir com a opção `-v` habilitada. Isso permite a saída detalhada do agente que está se conectando por meio do proxy ao Serviço OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Examine a seção [Configurando o agente para ser usado com um servidor proxy ou um gateway do OMS](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) para verificar se você configurou corretamente o agente para se comunicar por meio de um servidor proxy.    
* Verifique uma segunda vez se os seguintes pontos de extremidade do serviço Log Analytics estão na lista de permissões:

    |Recurso de agente| Portas |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Porta 443|   
    |*.oms.opinsights.azure.com | Porta 443|   
    |ods.systemcenteradvisor.com | Porta 443|   
    |*.blob.core.windows.net/ | Porta 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: você recebe um erro 403 quando tentar integrar

#### <a name="probable-causes"></a>Causas prováveis
* A data e a hora estão incorretas no servidor Linux 
* A ID e a Chave do Espaço de Trabalho usadas estão incorretas

#### <a name="resolution"></a>Resolução

1. Verifique a hora no servidor Linux com o comando date. Se a hora for +/-15 minutos da hora atual, a integração falhará. Para corrigir esse problema, atualize a data e/ou o fuso horário do servidor Linux. 
2. Verifique se você instalou a última versão do Agente do OMS para Linux.  A versão mais recente agora notifica se a distorção de horário está causando a falha de integração.
3. Reintegre usando a ID do Espaço de Trabalho e a Chave do Espaço de Trabalho corretas seguindo as instruções de instalação descritas anteriormente neste tópico.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Você vê um erro 404 e 500 no arquivo de log logo após a integração
Esse é um problema conhecido que ocorre durante o primeiro upload de dados do Linux em um espaço de trabalho do Log Analytics. Isso não afeta os dados sendo enviados ou a experiência do serviço.

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: você não está vendo nenhum dado no Portal do Azure

#### <a name="probable-causes"></a>Causas prováveis

- Falha na integração com o serviço Log Analytics
- A conexão com o serviço Log Analytics está bloqueada
- Os dados do Agente do OMS para Linux têm o backup realizado

#### <a name="resolutions"></a>Resoluções
1. Verifique se a integração do serviço Log Analytics foi bem-sucedida verificando se os seguintes arquivos existem: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reintegração usando as instruções de linha de comando do `omsadmin.sh`
3. Se estiver usando um proxy, consulte as etapas de resolução de proxy fornecidas anteriormente.
4. Em alguns casos, quando o Agente do OMS para Linux não pode se comunicar com o serviço, os dados no agente são enfileirados até todo o tamanho do buffer, que é 50 MB. O Agente do OMS para Linux deve ser reiniciado executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Esse problema foi corrigido nas versões 1.1.0-28 e posteriores do Agente.

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>Problema: O OMSAgent cria um número excessivo de processos do usuário no computador e nunca os termina
Quando você habilita soluções de gerenciamento que dão suporte ao gerenciamento de suas VMs Linux, isso inicia um número de processos no agente do Linux. Mas antes do processo terminar, outro processo é iniciado devido a um problema conhecido. 

#### <a name="resolutions"></a>Resoluções
Para alterar o número de processos de usuário que podem ser gerados pelo OMSAgent, você deve configurar o agente usando omsadmin.sh.  O número de processos que são gerados por padrão é 75 e, antes de alterar o limite, você deve primeiro executar o comando a seguir para ver quantos processos do OMSAgent estão atualmente em execução: `ps aux | grep -E '^omsagent' | wc -l`.  
Você pode verificar o valor para o qual o limite atual está definido executando o seguinte comando: `cat /etc/security/limits.conf | grep -E '^omsagent'`

Use os comandos a seguir para configurar um limite de processos personalizado ou para definir o limite de processos de volta para o valor padrão.

1. Para definir o limite de processos do OMSAgent: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`.<br>Observe que o limite mínimo para o qual ele pode ser definido é 5.  

2. Para definir o limite de processos do OMSAgent de volta para o valor padrão: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`

Verifique se a nova configuração foi aplicada, executando o seguinte comando: `cat /etc/security/limits.conf | grep -E '^omsagent'`.  Se você não vir a nova configuração aplicada, é possível que você tenha definido o limite de processos para um valor baixo demais.  

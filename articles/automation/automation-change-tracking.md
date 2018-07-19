---
title: Controlar alterações com a Automação do Azure
description: A solução Controle de Alterações ajuda a identificar alterações no software e Serviço Windows que ocorrem no ambiente.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 891206e88173d72a0bacf6694a331c4ad8cc9acc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069440"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar alterações no ambiente com a solução Controle de Alterações

Este artigo ajuda você a usar a solução de Controle de Alterações para identificar facilmente as alterações em seu ambiente. A solução controla as alterações no software Windows e Linux, nos arquivos Windows e chaves do Registro, nos serviços Windows e daemons do Linux. Identificar as alterações de configuração pode ajudá-lo a detectar problemas operacionais.

Alterações no software instalado, nos serviços Windows, nos arquivos e registros do Windows e daemons do Linux nos servidores monitorados são enviadas ao serviço do Log Analytics na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2008 Service Pack 1 (SP1) ou posterior
* Windows 7 SP1 e posterior.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

As seguintes distribuições Linux têm suporte oficialmente. No entanto, o agente para Linux também pode ser executado em outras distribuições não listadas. Salvo indicação em contrário, todas as versões secundárias são compatíveis com cada versão principal listada.  

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)  
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="enable-change-tracking-and-inventory"></a>Habilitar Controle de Alterações e Inventário

Para iniciar o controle de alterações, é necessário ativar a solução Controle de Alterações e Inventário para a sua Conta de Automação.

1. No Portal do Azure, navegue até sua Conta de Automação
1. Selecione **Controle de Alterações** em **CONFIGURAÇÃO**.
1. Selecione um espaço de trabalho do Log Analytics existente ou **Criar novo espaço de trabalho** e clique em **Habilitar**.

Isso habilita a solução para sua conta de automação. A solução pode demorar até 15 minutos para habilitar. A faixa azul notifica-o quando a solução estiver habilitada. Navegue de volta para a página **Controle de Alterações** para gerenciar a solução.

## <a name="configuring-change-tracking-and-inventory"></a>Configurar o Controle de Alterações e Inventário

Para saber como integrar computadores para a solução, visite: [Integração das soluções de Automação do Azure](automation-onboard-solutions-from-automation-account.md). Quando você tem uma integração de máquina com a solução de Controle de Alterações e de inventário, você pode configurar os itens para rastrear. Ao habilitar um uma chave de registro ou arquivo para controlar, ele será habilitado tanto para o Controle de Alterações como para Inventário.

Para rastrear alterações em arquivos no Windows e Linux, os hashes MD5 dos arquivos são usados. Esses hashes são usados para detectar se uma alteração foi feita desde o último inventário.

### <a name="configure-linux-files-to-track"></a>Configurar arquivos do Linux para controle

Use as etapas a seguir para configurar o acompanhamento de arquivo em computadores Linux:

1. Na sua Conta de Automação, selecione **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página **Controle de Alterações**, selecione **Arquivos Linux** e, em seguida, clique em **+ Adicionar** para adicionar o novo arquivo para controle.
3. Em **Adicionar o Arquivo do Linux para o Controle de Alterações**, insira as informações para o arquivo ou para o diretório rastrear e clique em **Salvar**.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo. Por exemplo: “/etc/* .conf”       |
|Tipo de caminho     | Tipo de item a ser rastreado; possíveis valores são: Arquivo e Diretório.        |
|Recursão     | Determina se a recursão é usada ao procurar o item a ser rastreado.        |
|Usar o Sudo     | Essa configuração determina se o Sudo será usado durante a verificação do item.         |
|Links     | Essa configuração determina como os links simbólicos lidam ao passar diretórios.<br> **Ignorar** - Ignora os links simbólicos e não inclui os arquivos/diretórios referenciados.<br>**Seguir** - Segue os links simbólicos durante a recursão e inclui também os arquivos/diretórios referenciados.<br>**Gerenciar** - Segue os links simbólicos e permite a alteração do conteúdo retornado.     |
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **verdadeiro** ou **falso**.|

> [!NOTE]
> A opção "Gerenciar" links não é recomendada. Não há suporte para a recuperação de conteúdo do arquivo.

### <a name="configure-windows-files-to-track"></a>Configurar os arquivos do Windows para controlar

Use as etapas a seguir para configurar o acompanhamento de arquivos em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página **Controle de Alterações**, selecione **Arquivos do Windows** e, em seguida, clique em **+ Adicionar** para adicionar o novo arquivo para controle.
3. Em **Adicionar o Arquivo do Windows para o Controle de Alterações**, insira as informações para o arquivo a controlar e clique em **Salvar**.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Inserir o Caminho     | O caminho para verificar o arquivo, por exemplo: "c:\temp\myfile.txt"       |
|Carregar o conteúdo do arquivo para todas as configurações| Habilita ou desabilita o upload de conteúdo do arquivo em alterações controladas. Opções disponíveis: **verdadeiro** ou **falso**.|

## <a name="configure-file-content-tracking"></a>Configurar o controle de conteúdo do arquivo

Você pode exibir o conteúdo antes e após uma alteração de um arquivo com Controle de Alterações de conteúdo do arquivo. Isso está disponível para arquivos do Windows e Linux, para cada alteração no arquivo, o conteúdo do arquivo é armazenado em uma conta de armazenamento e mostra o arquivo antes e após a alteração, embutido ou lado a lado. Para obter mais informações, confira [Exibir o conteúdo de um arquivo controlado](change-tracking-file-contents.md).

![exibir alterações em um arquivo](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurar chaves do Registro do Windows para rastrear

Use as etapas a seguir para configurar as chaves do registro para acompanhamento em computadores Windows:

1. Na sua Conta de Automação, selecione **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**. Clique em **Editar Configurações** (o símbolo de engrenagem).
2. Na página **Controle de Alterações**, selecione **Registro do Windows** e, em seguida, clique em **+ Adicionar** para adicionar um novo registro para controle.
3. Em **Adicionar Registro do Windows para Controle de Alterações**, insira as informações para a chave rastrear e clique em **Salvar**.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|habilitado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser rastreado.        |
|Agrupar     | Um nome de grupo para o agrupamento lógico de arquivos.        |
|Chave de Registro do Windows   | O caminho para verificar o arquivo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitações

A solução de Controle de Alterações atualmente não dá suporte ao seguinte:

* Pastas (diretórios) para o controle de arquivos do Windows
* Recursão para o controle de arquivos do Windows
* Caracteres curingas para o controle de arquivos do Windows
* Recursão para o rastreamento de registro do Windows
* Variáveis de caminho
* Sistemas de arquivos de rede
* Conteúdo do arquivo

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos no ciclo de coleta de 30 minutos, o desempenho da solução poderá ser prejudicado.
* Quando o tráfego da rede é alto, os registros de alteração podem demorar até seis horas para serem exibidos.
* Se você modificar a configuração enquanto um computador for desligado, o computador poderá lançar as alterações que pertenciam à configuração anterior.

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a solução Controle de Alterações está enfrentando os seguintes problemas:

* Atualizações de hotfix não são coletadas para Atualização do Windows 10 para Criadores e computadores Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Detalhes de coleta de dados do Controle de Alterações

A tabela a seguir mostra a frequência da coleta de dados para os tipos de alterações. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas:

| **Alterar tipo** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços do Windows | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

### <a name="windows-service-tracking"></a>Rastreamento de serviço do Windows

A frequência da coleta padrão para os serviços do Windows é de 30 minutos. Para configurar a frequência, acesse **Controle de Alterações**. Em **Editar Configurações** na guia **Serviços do Windows**, há um controle deslizante que permite alterar a frequência de coleta para os serviços do Windows de 10 segundos para até 30 minutos. Mova o controle deslizante para a frequência desejada e ele a salvará automaticamente.

![Controle deslizante de serviços do Windows](./media/automation-change-tracking/windowservices.png)

O agente controla somente as alterações e isso otimiza o desempenho do agente. Se um limite muito alto for definido, as alterações poderão ser perdidas se o serviço for revertido para o estado original. Definir a frequência para um valor menor permite capturar alterações que poderiam ser perdidas de outra forma.

> [!NOTE]
> Embora o agente possa controlar as alterações em um intervalo de apenas 10 segundos, os dados ainda demoram alguns minutos para serem exibido no portal. As alterações durante o tempo de exibição no portal do ainda são controladas e registradas.
  
### <a name="registry-key-change-tracking"></a>Controle de alterações de chave do Registro

O objetivo de monitorar alterações às chaves do registro é identificar os pontos de extensibilidade em que código de terceiros e o malware podem ser ativados. A lista a seguir mostra a lista de chaves do Registro pré-configuradas. Essas chaves estão configuradas, mas não habilitadas. Para controlar essas chaves do Registro, é necessário habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora scripts que são executados na inicialização.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora scripts que são executados no desligamento.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora as chaves que são carregadas antes que o usuário entre na conta do Windows. A chave é usada para programas de 32 bits em execução em computadores de 64 bits.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora as alterações nas configurações do aplicativo.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores entradas comuns de inicialização automática que conectam-se diretamente ao Windows Explorer e geralmente são executadas no processo com o Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores o registro do manipulador de sobreposição de ícone.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora o registro do manipulador de sobreposição de ícone para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o modelo DOM (Modelo de Objeto do Documento) da página atual e para controlar a navegação para programas de 32 bits em execução em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões da barra de ferramentas personalizada.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramentas personalizada para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à seção [drivers] no arquivo SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora os drivers de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits executados em computadores de 64 bits. Semelhante à seção [drivers] no arquivo SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora a lista de DLLs de sistema conhecidas ou comumente usadas; esse sistema impede que pessoas explorem as permissões de diretório de aplicativo fracas via depósito de versões com cavalo de troia das DLLs do sistema.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitora a lista de pacotes capaz de receber notificações de eventos do Winlogon, o modelo de suporte de logon interativo para o sistema operacional Windows.|

## <a name="use-change-tracking"></a>Use o Controle de Alterações

Depois que a solução estiver habilitada, você poderá exibir o resumo das alterações de seus computadores monitorados, selecionando **Controle de Alterações** em **GERENCIAMENTO DE CONFIGURACÃO** na sua conta de Automação.

É possível exibir as alterações nos computadores e, em seguida, analisar detalhadamente cada evento. Os menus suspensos estão disponíveis na parte superior do gráfico para limitar o gráfico e as informações detalhadas com base no tipo de alteração e intervalos de tempo. Você também pode clicar e arrastrar no gráfico para selecionar um intervalo de tempo personalizado.

![imagem do painel Controle de Alterações](./media/automation-change-tracking/change-tracking-dash01.png)

Ao clicar em uma alteração ou evento, as informações detalhadas sobre essa alteração serão exibidas. Como possível visualizar no exemplo, o tipo de inicialização do serviço foi alterado de Manual para Automático.

![imagem dos detalhes do controle de alterações](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Pesquisar logs

Além dos detalhes fornecidos no portal, é possível fazer as pesquisas nos logs. Com a página **Controle de Alterações** aberta, clique em **Log Analytics**, isso abrirá a página **Pesquisa de Logs**.

### <a name="sample-queries"></a>Consultas de exemplo

A tabela a seguir fornece pesquisas de logs de exemplo para os registros de alterações coletados por essa solução:

|Consultar  |DESCRIÇÃO  |
|---------|---------|
|ConfigurationData<br>&#124; onde   ConfigDataType == "WindowsServices" e SvcStartupType == "Auto"<br>&#124; onde SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) por SoftwareName, Computer         | Mostra os registros de inventário mais recentes para os Serviços do Windows que foram configurados para Automático, mas foram relatados como Parados<br>Os resultados estão limitados ao registro mais recente desse SoftwareName e Computador      |
|ConfigurationChange<br>&#124; onde ConfigChangeType == "Software" e ChangeCategory == "Removed"<br>&#124; ordenar por TimeGenerated desc|Mostra os registros de alterações do software removido|

## <a name="next-steps"></a>Próximas etapas

Visite o tutorial sobre Controle de Alterações para saber mais sobre o uso da solução:

> [!div class="nextstepaction"]
> [Solucionar problemas de alterações em seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Use [Pesquisas de log no Log Analytics](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados do controle de alterações.

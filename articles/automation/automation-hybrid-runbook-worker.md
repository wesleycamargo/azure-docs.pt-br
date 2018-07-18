---
title: Runbook Workers Híbridos da Automação do Azure
description: Este artigo fornece informações sobre como instalar e usar Hybrid Runbook Worker, que é um recurso da Automação do Azure que permite que você execute runbooks em máquinas no seu datacenter local ou provedor de nuvem.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194614"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatizar recursos em seu data center ou nuvem com Hybrid Runbook Worker

Runbooks na automação do Azure pode não ser capazes de acessar recursos em outras nuvens ou em seu ambiente local, desde que eles executados na nuvem do Azure. O recurso Hybrid Runbook Worker da Automação do Azure permite executar runbooks diretamente no computador que hospeda a função e em recursos no ambiente para gerenciar esses recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados.

Essa funcionalidade está ilustrada na imagem a seguir:

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Runbook Worker Híbrido

Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente. Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado. Cada operador no grupo de sonda de automação do Azure para ver se todos os trabalhos estão disponíveis. Se houver um trabalho o primeiro worker para obter o trabalho tem-lo. Você não pode especificar um trabalhador específico.

## <a name="installing-a-hybrid-runbook-worker"></a>Instalando o Runbook Worker Híbrido

O processo para instalar um trabalho de Runbook híbrido é diferente dependendo do sistema operacional. A tabela a seguir contém links para os diferentes métodos que você pode usar para instalar um Hybrid Runbook Worker. Para instalar e configurar um Hybrid Runbook Worker do Windows, há dois métodos disponíveis. O método recomendado é usar um runbook de Automação para automatizar completamente o processo necessário para configurar um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente. Para máquinas Linux, você executa um script de Python para instalar o agente na máquina

|SO  |Tipo de Implantação  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>Manual        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Para gerenciar a configuração de seus servidores que dão suporte à função Hybrid Runbook Worker com o DSC (Configuração de Estado Desejado), você precisará adicioná-los como nós DSC. Para saber mais sobre a integração deles para gerenciamento com DSC, confira [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md).
>
>Se você habilitar o [solução de gerenciamento de atualizações](automation-update-management.md), qualquer computador conectado ao seu espaço de trabalho de análise de Log é automaticamente configurado como um Hybrid Runbook Worker para dar suporte a runbooks incluído nesta solução. No entanto, ele não estará registrado com nenhum grupo Hybrid Worker que você já tenha definido em sua Conta de automação. O computador pode ser adicionado a um grupo Hybrid Runbook Worker na sua Conta de automação para dar suporte a runbooks de automação enquanto você estiver usando a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

Examine o [informações para planejar sua rede](#network-planning) antes de você começar a implantar um Hybrid Runbook Worker. Depois de você implantar com êxito um runbook worker, leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

## <a name="removing-hybrid-runbook-worker"></a>Removendo o Hybrid Runbook Worker

Você pode remover um ou mais Trabalhadores de Runbook Híbridos de um grupo ou remover o grupo, dependendo dos seus requisitos. Para remover um Hybrid Runbook Worker de um computador local, execute as etapas a seguir:

1. No portal do Azure, abra sua Conta de Automação.
2. Na folha **Configurações**, selecione **Teclas** e anote os valores para o campo **URL** e **Tecla de Acesso Primária**. Você precisará dessas informações para a próxima etapa.

### <a name="windows"></a>Windows

Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir ‑ . Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Para remover computadores antigos do grupo do Hybrid Worker, use o parâmetro opcional `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Isso não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração da função do Hybrid Runbook Worker.

## <a name="remove-hybrid-worker-groups"></a>Remover grupos do Hybrid Worker

Para remover um grupo, primeiro você precisa remover o Hybrid Runbook Worker de cada computador membro do grupo usando o procedimento mostrado anteriormente e, em seguida, executar as seguintes etapas para remover o grupo.

1. Abra a conta de Automação no Portal do Azure.
1. Em **Automação de Processo**, selecione **Grupos de trabalho híbrido**. Selecione o grupo que você deseja excluir. Depois de selecionar o grupo específico, o **grupo do Hybrid worker** página de propriedades é exibida.

   ![Página de grupo do Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Na página de propriedades para o grupo selecionado, clique em **excluir**. Uma mensagem será exibida solicitando que você confirme a ação. Selecione **Sim** se tiver certeza de que deseja continuar.

   ![Caixa de diálogo de confirmação de exclusão de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Esse processo pode levar vários segundos e você pode acompanhar o progresso no menu **Notificações**.

## <a name="network-planning"></a>Configurar sua rede

### <a name="hybrid-worker-role"></a>Função de trabalhador híbrido

Para o Hybrid Runbook Worker conectar e registrar com Log Analytics, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. Além das [portas e URLs necessárias para o Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) conectar o Log Analytics.

Se você usar um servidor proxy para comunicação entre o agente e o serviço do Log Analytics, verifique se os recursos apropriados estão acessíveis. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso.

A porta e URLs a seguir são necessárias para a função do Hybrid Runbook Worker se comunicar com a Automação do Azure:

* Porta: Somente a TCP 443 é necessária para acesso de Internet de saída.
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região.

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. Os intervalos de computação, SQL e de armazenamento são incluídos no arquivo.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.
>
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Usuários do ExpressRoute do Azure devem observar que esse arquivo é usado para atualizar o anúncio do Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês.

### <a name="update-management"></a>Gerenciamento de atualizações

Além do padrão endereços e portas que exige o Hybrid Runbook Worker, os endereços a seguir são necessários especificamente para gerenciamento de atualizações. A comunicação para esses endereços é feita pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>solução de problemas

O operador de Runbook híbrido depende de um agente para se comunicar com sua conta de automação para registrar o trabalho, recebe trabalhos de runbook e status de relatórios. Para este agente do Windows é o Microsoft Monitoring Agent. Para Linux é o agente do OMS para Linux. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro:

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>O Hybrid Worker está usando um proxy ou firewall

Verifique se o computador tem acesso de saída para *.azure automation.net na porta 443.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>O computador em que o Hybrid Worker está em execução não tem requisitos de hardware mínimos

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de serem designados para hospedar esse recurso. Caso contrário, dependendo da utilização de recursos de outros processos em segundo plano e da contenção provocada por runbooks durante a execução, o computador fica sobrecarregados e causará atrasos de trabalho de runbook ou tempos limite.

Confirme se o computador designado para executar o recurso Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se isso acontecer, monitore a utilização de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows. Se não houver memória ou pressão da CPU, isso pode indicar a necessidade de atualizar ou adicionar mais processadores ou aumentar a memória para eliminar o gargalo de recursos e resolver o erro. Como alternativa, selecione um recurso de computação diferente que consiga dar suporte aos requisitos mínimos e ajuste a escala quando a demanda da carga de trabalho indicar que um aumento é necessário.

Para obter informações adicionais sobre solução de problemas para um sistema operacional específico, consulte [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#troubleshooting) ou [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#troubleshooting)

## <a name="next-steps"></a>Próximas etapas

Leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

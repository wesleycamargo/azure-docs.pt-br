---
title: Resolver problemas de integração de gerenciamento de atualizações, controle de alterações e inventário
description: Aprenda a solucionar erros de integração com as soluções Gerenciamento de atualizações, Controle de alterações e Inventário
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064185"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Resolver erros quando soluções de integração

Você pode encontrar erros ao integrar soluções como o Gerenciamento de Atualizações ou o Rastreamento de Mudanças e o Inventário. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="general-errors"></a>Erros gerais

### <a name="computer-grou-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Esse código de erro significa que a consulta de pesquisa salva referente ao grupo de computadores e usada para a solução de destino não foi formatada corretamente. 

#### <a name="cause"></a>Causa

Você pode ter alterado a consulta ou ela pode ter sido alterada pelo sistema.

#### <a name="resolution"></a>Resolução

Você pode excluir a consulta dessa solução e reintegrar a solução, o que recria a consulta. A consulta pode ser encontrada em seu workspace, em **Pesquisas salvas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome da solução associada a essa consulta. Se várias soluções estiverem habilitadas, o **MicrosoftDefaultComputerGroup** mostra várias vezes em**Pesquisas salvas**.

### <a name="policy-violation"></a>Cenário: PolicyViolation

#### <a name="issue"></a>Problema

Esse código de erro significa que a implantação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está em vigor que está impedindo que a operação seja concluída.

#### <a name="resolution"></a>Resolução

Para implantar a solução com êxito, você precisa considerar alterar a política indicada. Como há muitos tipos diferentes de políticas que podem ser definidas, as alterações específicas necessárias dependem da política violada. Por exemplo, se uma política for definida em um grupo de recursos sem permissão para alterar o conteúdo de certos tipos de recursos nesse grupo de recursos, você pode, por exemplo, fazer qualquer uma das seguintes ações:

* Remover completamente a política.
* Tentar se integrar a um grupo de recursos diferente.
* Revisar a política, por exemplo:
  * Refazer a segmentação da política para um recurso específico (como para uma conta de automação específica).
  * Revisando o conjunto de recursos ao qual a política foi configurada para negar.

Verifique as notificações no canto superior direito do portal do Azure ou navegue para o grupo de recursos que contém sua conta de automação e selecione **Implantações** em **Configurações** para exibir a implantação com falha. Para saber mais sobre o Azure Policy, consulte: [Visão geral do Azure Policy](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>falhas de extensão do MMA

Ao implantar uma solução, vários recursos relacionados são implantados. Um desses recursos é o Microsoft Monitoring Agent Extension ou o OMS Agent for Linux. Estas são Extensões de Máquina Virtual instaladas pelo Agente Convidado da máquina virtual responsável pela comunicação com o Workspace configurado do Operations Management Suite (OMS), com a finalidade de coordenação posterior do download de binários e outros arquivos da dependência da solução uma vez que começa a execução.
Em geral, você primeiro toma conhecimento das falhas de instalação do MMA ou do Agente do OMS para Linux a partir de uma notificação exibida no Hub de Notificações. Clicar nessa notificação fornece mais informações sobre a falha específica. A navegação para o recurso Grupos de Recursos e, em seguida, para o elemento Deployments dentro dele também fornece detalhes sobre as falhas de implantação que ocorreram.
A instalação do Agente MMA ou OMS para Linux pode falhar por diversos motivos, e as etapas a serem tomadas para solucionar essas falhas variam, dependendo do problema. Seguem etapas específicas de solução de problemas.

A seção a seguir descreve vários problemas que você pode encontrar durante a integração que causam uma falha na implantação da extensão MMA.

### <a name="webclient-exception"></a>Cenário: ocorreu uma exceção durante uma solicitação de WebClient

A extensão do MMA na máquina virtual não consegue se comunicar com recursos externos e a implantação falha.

#### <a name="issue"></a>Problema

A seguir, exemplos de mensagens de erro retornadas:

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Algumas causas possíveis para esse erro são:

* Existe um proxy configurado na VM, que permite apenas portas específicas.

* Uma configuração de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de ter as portas e os endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte [planejando sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Cenário: a instalação falhou devido a problemas ambientais transitórios

A instalação da extensão do Microsoft Monitoring Agent falhou durante a implantação devido a outra instalação ou ação bloqueando a instalação

#### <a name="issue"></a>Problema

A seguir, exemplos de mensagens de erro podem ser retornados:

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Causa

Algumas causas possíveis para esse erro são:

* Outra instalação está em progresso
* O sistema foi acionado para reiniciar durante a implementação do modelo

#### <a name="resolution"></a>Resolução

Este erro é um erro transitório na natureza. Repita a implantação para instalar a extensão.

### <a name="installation-timeout"></a> Cenário: tempo limite de instalação

A instalação da extensão MMA não foi concluída devido a um tempo limite.

#### <a name="issue"></a>Problema

Segue-se um exemplo de uma mensagem de erro que pode ser devolvida:

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Este erro é devido à máquina virtual estar sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar a extensão MMA quando a VM estiver sob uma carga menor.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

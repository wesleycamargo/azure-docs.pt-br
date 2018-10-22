---
title: Solucionar erros com o Gerenciamento de Atualizações
description: Aprenda a solucionar problemas com o Gerenciamento de Atualizações
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 08/08/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 2e47320d5ad88edfa8ea6122f3a0abd104230974
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42139980"
---
# <a name="troubleshooting-issues-with-update-management"></a>Resolução de problemas com o Gerenciamento de Atualizações

Este artigo discute soluções para resolver problemas que você pode encontrar ao usar o Gerenciamento de Atualizações.

## <a name="general"></a>Geral

### <a name="components-enabled-not-working"></a>Cenário: Os componentes da solução de 'Gerenciamento de Atualizações' foram habilitados e, agora, esta máquina virtual está sendo configurada

#### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a integração:

```
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado pelos seguintes motivos:

1. A comunicação com a Conta de Automação está sendo bloqueada.
2. A VM que está sendo integrada pode ter vindo de um computador clonado sem sysprep com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

1. Visite [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas devem ter permissão para que Gerenciamento de Atualizações funcione.
2. Se estiver usando uma imagem clonada, primeiro faça sysprep da imagem e instale o agente MMA em seguida.

## <a name="windows"></a>Windows

Se você encontrar problemas ao tentar integrar a solução em uma máquina virtual, verifique o log de eventos **Operations Manager** em **Logs de Aplicativos e Serviços** na máquina local para eventos com o ID de evento **4502** e mensagem de evento contendo **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

A seção a seguir destaca mensagens de erro específicas e uma possível resolução para cada uma delas. Para outros problemas de integração, consulte [como solucionar problemas de integração de soluções](onboarding.md).

### <a name="machine-already-registered"></a>Cenário: a máquina já está registrada em uma conta diferente

#### <a name="issue"></a>Problema

Você vê a seguinte mensagem de erro:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Causa

A máquina já está integrada a outro workspace para o Gerenciamento de Atualizações.

#### <a name="resolution"></a>Resolução

Execute a limpeza de artefatos antigos na máquina, [excluindo o grupo de runbooks híbridos](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) e tente novamente.

### <a name="machine-unable-to-communicate"></a>Cenário: a máquina não consegue se comunicar com o serviço

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Causa

Pode haver um proxy, gateway ou firewall bloqueando a comunicação de rede.

#### <a name="resolution"></a>Resolução

Revise sua rede e garanta que portas e endereços apropriados sejam permitidos. Veja [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning), para uma lista de portas e endereços que são exigidos pelo Gerenciamento de Atualizações e pelos Trabalhadores de Runbooks Híbridos.

### <a name="unable-to-create-selfsigned-cert"></a>Cenário: não é possível criar certificado autoassinado

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Causa

O Hybrid Runbook Worker não conseguiu gerar um certificado auto-assinado

#### <a name="resolution"></a>Resolução

Verifique se a conta do sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Cenário: A execução da atualização falha ao iniciar

#### <a name="issue"></a>Problema

Uma atualização é executada falha em iniciar em uma máquina Linux.

#### <a name="cause"></a>Causa

O Linux Hybrid Worker não é saudável.

#### <a name="resolution"></a>Resolução

Faça uma cópia do seguinte arquivo de log e preserve-o para fins de solução de problemas:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Cenário: A execução da atualização é iniciada, mas encontra erros

#### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

#### <a name="cause"></a>Causa

Causas possíveis podem ser:

* Gerenciador de pacotes não é saudável
* Pacotes específicos podem interferir na correção baseada em nuvem
* Outras razões

#### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização após a inicialização bem-sucedida no Linux, verifique a saída da tarefa da máquina afetada na execução. Você pode encontrar mensagens de erro específicas do gerenciador de pacotes de sua máquina, sobre as quais você pode pesquisar e realizar ações. O Gerenciamento de Atualizações requer que o gerenciador de pacotes seja saudável para implantações de atualização bem-sucedidas.

Em alguns casos, as atualizações de pacote podem interferir no Gerenciamento de Atualizações impedindo que uma implantação de atualização seja concluída. Se você perceber isso, terá que excluir esses pacotes de futuras atualizações ou instalá-los manualmente.

Se você não conseguir resolver um problema de patch, faça uma cópia do seguinte arquivo de log e preserve-o **antes de** a próxima implantação de atualização começar para fins de solução de problemas:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
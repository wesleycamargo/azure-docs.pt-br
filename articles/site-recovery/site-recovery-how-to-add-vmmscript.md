---
title: "Como adicionar scripts ao plano de recuperação no Azure Site Recovery | Microsoft Docs"
description: "Descreve os pré-requisitos para adicionar um novo script a um plano de recuperação no VMM para o Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>Adicionar scripts VMM a um plano de recuperação


Este artigo fornece informações sobre como criar e adicionar scripts VMM para planos de recuperação em [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>Pré-requisitos para adicionar um script ao plano de recuperação

Você pode usar scripts do PowerShell em seus planos de recuperação. Você precisará criá-los e colocá-los na biblioteca do VMM para que fiquem acessíveis do plano de recuperação. Abaixo estão algumas considerações enquanto você grava o script.

* Certifique-se de que os scripts usem blocos try-catch para que as exceções sejam tratadas normalmente.
    - Se houver uma exceção no script, ele será interrompido e a tarefa será mostrada como com falha.
    - Se ocorrer um erro, qualquer parte restante do script não é executado.
    - Se ocorrer um erro quando você executa um failover não planejado, o plano de recuperação continuará.
    - Se ocorrer um erro quando você executa um failover planejado, o plano de recuperação será interrompido. Você precisa corrigir o script, verificar se ele funciona conforme esperado e executar novamente o plano de recuperação.
        - O comando Write-Host não funciona em um script de plano de recuperação e o script falhará. Para criar, crie um script de proxy que por sua vez executa o script principal. Certifique-se de que toda a saída é direcionada usando o >> comando.
        - O script expira se não retornar em até 600 segundos.
        - Se nada for escrito em STDERR, o script será classificado como com falha. Essa informação é exibida nos detalhes de execução do script.

* Os scripts em um plano de recuperação são executados no contexto da conta de Serviço VMM. Verifique se que essa conta tem permissões de leitura para o compartilhamento remoto no qual o script está localizado. Teste o script seja executado no nível de privilégio da conta de serviço do VMM.
* Os cmdlets do VMM são entregues em um módulo do Windows PowerShell. O módulo é instalado quando você instala o console do VMM. Pode ser carregado em seu script, usando o seguinte comando no script:
   - Import-Module-Name virtualmachinemanager. [Saiba mais](https://technet.microsoft.com/library/hh875013.aspx).
* Verifique se você possui pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão, o caminho de compartilhamento da biblioteca de um servidor VMM pode ser encontrado localmente no servidor VMM com o nome de pasta MSCVMMLibrary.
    * Se o caminho de compartilhamento da biblioteca for remoto (ou local, mas não compartilhado com MSCVMMLibrary), configure o compartilhamento da seguinte forma (usando \\libserver2.contoso.com\share\ como um exemplo):
      * Abra o Editor do Registro e navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Edite o valor de **ScriptLibraryPath** e coloque o valor como \\libserver2.contoso.com\share\. Especifique o FQDN completo. Forneça permissões para o local de compartilhamento. Observe que este é o nó raiz do compartilhamento. **Para verificar isso, você poderá procurar a biblioteca no nó raiz no VMM. O caminho aberto será a raiz, aquela que você precisará usar na variável**.
      * Certifique-se de que você teste o script com uma conta de usuário que tenha as mesmas permissões que a conta de serviço do VMM. Isso verifica que autônomos testados os scripts são executados da mesma maneira como serão nos planos de recuperação. No servidor VMM, defina a política de execução a ser ignorada da seguinte maneira:
        * Abra o console do **Windows PowerShell de 64 bits** usando privilégios elevados.
        * Digite: **Set-executionpolicy bypass**. [Saiba mais](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> Você deve definir a política de execução para Ignorar apenas o PowerShell de 64 bits. Se você tiver definido isso para o PowerShell de 32 bits, os scripts não serão executados.

## <a name="add-the-script-to-the-vmm-library"></a>Adicionar o script à biblioteca do VMM

Se você tiver um site de origem do VMM, será possível criar um script no servidor VMM e incluí-lo no plano de recuperação.

1. Crie uma nova pasta no compartilhamento de biblioteca. Por exemplo, \<VMMServerName > \MSSCVMMLibrary\RPScripts. Coloque-a nos servidores VMM de origem e de destino.
2. Crie o script (por exemplo, RPScript) e verifique se ele funciona conforme esperado.
3. Coloque o script no local \<NomeDoServidorVMM>\MSSCVMMLibrary nos servidores VMM de origem e de destino.

## <a name="add-the-script-to-a-recovery-plan"></a>Adicionar o script a um plano de recuperação

Você pode adicionar o script ao grupo padrão depois que VMs ou grupos de replicação são adicionados a ele, e criar o plano.

1. Abra o plano de recuperação.
2. Clique em um item de **etapa** lista e, em seguida, clique em **Script** ou **ação Manual**.
3. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Use os botões **Mover para Cima** e **Mover para Baixo** para mover o script para cima ou para baixo.
4. Se você adicionar um script do VMM, selecione **Failover no script do VMM**. Em **caminho do Script de**, digite o caminho relativo para o compartilhamento. No exemplo do VMM abaixo, você pode especificar o caminho: **\rpscripts\rpscript.ps1**.
5. Se você adicionar um runboook da automação do Azure, especifique a Conta de Automação do Azure na qual o runbook está localizado e selecione o script de runbook do Azure apropriado.
6. Faça um failover de teste do plano de recuperação para garantir que o script funciona conforme o esperado.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-failover.md) sobre a execução de failovers.

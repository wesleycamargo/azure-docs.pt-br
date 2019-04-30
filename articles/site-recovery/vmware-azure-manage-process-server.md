---
title: Gerenciar um servidor de processo para recuperação de desastre de VMs e servidores físicos do VMware no Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como gerenciar um servidor de processo configurado para recuperação de desastre de VMs e servidor físico do VMware no Azure usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0a0b6c83f800c0a479ba7a16c91b497d1a11da9e
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62732478"
---
# <a name="manage-process-servers"></a>Gerenciar servidores de processo

Por padrão, o servidor em processo usado quando você replica servidores físicos ou VMs do VMware no Azure é instalado no computador do servidor de configuração local. Há duas instâncias nas quais você precisa configurar um servidor em processo separado:

- Para implantações grandes, você pode precisar de servidores em processo locais adicionais para dimensionar a capacidade.
- Para o failback, você precisa de um servidor em processo temporário configurado no Azure. Você pode excluir essa VM após o failback. 

Este artigo resume as tarefas típicas de gerenciamento para esses servidores em processo adicionais.

## <a name="upgrade-a-process-server"></a>Atualizar um servidor em processo

Atualize um servidor em processo executado localmente ou no Azure (para fins de failback), da seguinte maneira:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   Normalmente, quando você usa a Imagem da Galeria do Azure para criar um servidor em processo no Azure para fins de failback, ele executa a última versão disponível. As equipes do Site Recovery liberam correções e melhorias regularmente, e recomendamos que você mantenha os servidores em processo atualizados.

## <a name="balance-the-load-on-process-server"></a>Balancear a carga no servidor de processo

Para balancear a carga entre dois servidores de processo,

1. Navegue até **Cofre dos Serviços de Recuperação** > **Gerenciar** > **Infraestrutura do Site Recovery** > **Para VMware e máquinas físicas** > **Servidores de Configuração**.
2. Clique no servidor de configuração para o qual os servidores de processo são registrados.
3. A lista de servidores de processo registrados aos servidores de configuração estão disponíveis na página.
4. Clique no servidor de processo no qual você deseja modificar a carga de trabalho.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. Você pode usaras opções **Balancear carga** ou **Alternar**, conforme explicado abaixo, de acordo com o requisito.

### <a name="load-balance"></a>Balancear carga

Por meio dessa opção, você pode selecionar uma ou mais máquinas virtuais e transferi-las para outro servidor de processo.

1. Clique em **Balancear a carga**, selecione o servidor de processo de destino na lista suspensa. Clique em **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Clique em **Selecionar máquinas**, escolha as máquinas virtuais que você deseja mover do servidor de processo atual para o servidor de processo de destino. Detalhes de alteração de dados médio são exibidos em cada máquina virtual.
3. Clique em **OK**. Monitore o progresso do trabalho em **Cofre de Serviços de Recuperação** > **Monitorando** >  **trabalhos de Recuperação do Site**.
4. Leva 15 minutos para que as alterações reflitam a conclusão desta operação bem-sucedida ou [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) para efeito imediato.

### <a name="switch"></a>Switch

Por meio dessa opção, toda carga de trabalho protegida em um servidor de processo é movida para outro servidor de processo.

1. Clique em **Alternar**, selecione o Servidor de processo de destino, clique em **OK**.

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. Monitore o progresso do trabalho em **Cofre de Serviços de Recuperação** > **Monitorando** >  **trabalhos de Recuperação do Site**.
3. Leva 15 minutos para que as alterações reflitam a conclusão desta operação bem-sucedida ou [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) para efeito imediato.

## <a name="process-server-selection-guidance"></a>Diretrizes de seleção de servidor de processo

O Azure Site Recovery identifica automaticamente se o servidor de processo está se aproximando seus limites de uso. Orientação é fornecida quando você configurar uma expansão servidor de processo.

|Status de Integridade  |Explicação  | Disponibilidade de recursos  | Recomendações|
|---------|---------|---------|---------|
| Íntegro (verde)    |   Servidor de processo está conectado e está íntegro      |Utilização de CPU e memória está abaixo de 80%; Disponibilidade de espaço livre estiver acima de 30%| Este servidor de processo pode ser usado para proteger os servidores adicionais. Certifique-se de que a nova carga de trabalho está dentro de [definidos limites do servidor de processo](vmware-azure-set-up-process-server-scale.md#sizing-requirements).
|Aviso (laranja)    |   Servidor de processo está conectado, mas determinados recursos estão prestes a atingir os limites máximos  |   Utilização de CPU e memória é entre 80% a 95%; Disponibilidade de espaço livre é entre 25% a 30%       | Uso do servidor de processo está próximo valores de limite. Adicionando novos servidores ao mesmo servidor de processo levará a cruzar os valores de limite e pode afetar os itens protegidos existentes. É aconselhável [configurar um servidor de processo de escalonamento horizontal](vmware-azure-set-up-process-server-scale.md#before-you-start) para replicações novo.
|Aviso (laranja)   |   Servidor de processo está conectado, mas dados não foi carregados no Azure nos últimos 30 min  |   Utilização de recursos esteja dentro dos limites de limite       | Solucionar problemas [falhas de carregamento de dados](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) antes de adicionar novas cargas de trabalho **ou** [configurar um servidor de processo de escalonamento horizontal](vmware-azure-set-up-process-server-scale.md#before-you-start) para replicações novo.
|Crítico (vermelho)    |     Servidor de processo pode estar desconectado  |  Utilização de recursos esteja dentro dos limites de limite      | Solucionar problemas [problemas de conectividade do servidor de processo](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) ou [configurar um servidor de processo de escalonamento horizontal](vmware-azure-set-up-process-server-scale.md#before-you-start) para replicações novo.
|Crítico (vermelho)    |     Utilização de recursos ultrapassou o limite de limites |  Utilização de CPU e memória estiver acima de 95%; Disponibilidade de espaço livre é menor que 25%.   | Adicionando novas cargas de trabalho ao mesmo servidor de processo está desabilitada porque o limite de recursos, limites tenham sido atendidos. Portanto, [configurar um servidor de processo de escalonamento horizontal](vmware-azure-set-up-process-server-scale.md#before-you-start) para replicações novo.
Crítico (vermelho)    |     Dados não foi carregados do Azure para o Azure nos últimos 45 min. |  Utilização de recursos esteja dentro dos limites de limite      | Solucionar problemas [falhas de carregamento de dados](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) antes de adicionar novas cargas de trabalho ao mesmo servidor de processo ou [configurar um servidor de processo de escalonamento horizontal](vmware-azure-set-up-process-server-scale.md#before-you-start)

## <a name="reregister-a-process-server"></a>Registrar um servidor em processo novamente

Caso precise registrar novamente um servidor em processo executado localmente ou no Azure, com o servidor de configuração, faça o seguinte:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Depois de salvar as configurações, faça o seguinte:

1. No servidor em processo, abra um prompt de comando do administrador.
2. Navegue para a pasta **%PROGRAMDATA%\ASR\Agent** e execute o comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificar as configurações de proxy de um servidor em processo local

Se o servidor em processo usa um proxy para se conectar ao Site Recovery no Azure, use este procedimento caso precise modificar as configurações de proxy existentes.

1. Faça logon no computador do servidor em processo. 
2. Abra uma janela de comando do PowerShell de administrador e execute o comando a seguir:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Navegue para a pasta **%PROGRAMDATA%\ASR\Agent** e execute o seguinte comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Remover um servidor em processo

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Gerenciar software antivírus em servidores de processo

Se o software antivírus estiver ativo em um servidor de processos independente ou servidor de destino mestre, exclua as seguintes pastas das operações de antivírus:


- C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Diretório de instalação do servidor de processo, Exemplo: C:\Program Files (x86)\Microsoft Azure Site Recovery
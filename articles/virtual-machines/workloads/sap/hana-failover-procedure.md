---
title: Procedimento de failover do HANA para site de desastres para o SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como executar failover para site de recuperação de desastres para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48bea302f2f8e878275e6a7c222d703e0f31f239
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492798"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de failover de recuperação de desastre


>[!IMPORTANT]
>Esta documentação não substitui a documentação de administração do SAP HANA ou as Notas do SAP. Espera-se que o leitor tenha um sólido reconhecimento e experiência em administração e operações do SAP HANA, especialmente com os tópicos de backup, restauração, alta disponibilidade e recuperação de desastre. Nesta documentação, são mostradas capturas de tela do SAP HANA Studio. O conteúdo, a estrutura e a natureza das telas das ferramentas de administração do SAP e as próprias ferramentas podem mudar de uma versão do SAP HANA para outra versão.

Há dois casos a serem considerados durante o failover para o site de recuperação de desastre:

- É necessário que o banco de dados do SAP HANA retorne para o status mais recente dos dados. Nesse caso, há um script de autoatendimento com o qual você pode fazer failover sem precisar entrar em contato com a Microsoft. No entanto, para o failback, você precisa trabalhar com a Microsoft.
- Se quiser restaurar para um instantâneo de armazenamento que não seja o último instantâneo replicado. Nesse caso, você precisa trabalhar com a Microsoft. 

>[!NOTE]
>As etapas a seguir precisam ser executadas na unidade do SAP HANA nas Instâncias Grandes, que representa a unidade de DR. 
 
Para restaurar os instantâneos de armazenamento replicados mais recentes, execute as etapas a seguir: 

1. Desligue a instância de não produção do HANA na unidade de recuperação de desastre do SAP HANA nas Instâncias Grandes que você está executando. Isso ocorre porque há uma instância de produção do HANA inativa pré-instalada.
1. Certifique-se de que não há nenhum processo do SAP HANA em execução. Use o comando a seguir para essa verificação: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A saída agora deve mostrar o processo **hdbdaemon** em um estado interrompido e nenhum outro processo do HANA em estado de execução ou iniciado.
1. Na unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre, execute o script *azure_hana_dr_failover.pl*. O script está solicitando que um SID do SAP HANA seja restaurado. Quando solicitado, digite o SID do SAP HANA que foi replicado e que é mantido no arquivo *HANABackupCustomerDetails.txt* na unidade do SAP HANA em Instâncias Grandes no site de DR. 

      Se você quiser que várias instâncias do SAP HANA em Instâncias Grandes sejam substituídas, será necessário executar o script várias vezes. Quando solicitado, digite o SID do SAP HANA que você deseja fazer failover e restaurar. Na conclusão, o script mostrará uma lista de pontos de montagem dos volumes que são adicionados à unidade do SAP HANA em Instâncias Grandes. Essa lista inclui os volumes de recuperação de desastre restaurados.

1. Monte os volumes de recuperação de desastre restaurados usando comandos do sistema operacional Linux na unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre. 
1. Inicie a instância de produção do SAP HANA inativa.
1. Se optar por copiar logs de backup de log de transações para reduzir o tempo de RPO, será necessário mesclar esses backups de log de transações no diretório /hana/logbackups da recuperação de desastre montado recentemente. Não substitua backups existentes. Copie os backups mais recentes que não foram replicados com a replicação mais recente de um instantâneo de armazenamento.
1. Também é possível restaurar arquivos únicos dos instantâneos que foram replicados para o volume /hana/shared/PRD na região do Azure de recuperação de desastre. 

Você pode testar o failover de recuperação de desastres sem afetar a relação de replicação real. Para fazer um failover de teste, siga as etapas anteriores 1 e 2 e continue com a seguinte etapa 3.

>[!IMPORTANT]
>*Não* execute nenhuma transação de produção na instância que você criou no site de DR por meio do processo de **teste de um failover** com o script introduzido na etapa 3. Esse comando cria um conjunto de volumes sem relação com o site primário. Como resultado, a sincronização de volta para o site primário *não* é possível. 

Etapa 3 para o teste de failover:

Na unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre, execute o script **azure_hana_test_dr_failover.pl**. Esse script *não* está interrompendo a relação de replicação entre o site primário e site de recuperação de desastre. Em vez disso, o script está clonando os volumes de armazenamento da recuperação de desastres. Quando o processo de clonagem for bem-sucedido, os volumes clonados serão restaurados para o estado do instantâneo mais recente e, em seguida, serão montados na unidade de recuperação de desastres. O script está solicitando que um SID do SAP HANA seja restaurado. Digite um ou o único SID do SAP HANA que foi replicado e é mantido no *HANABackupCustomerDetails.txt* na unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre. 

Se você quiser ter várias instâncias do SAP HANA para testar, será necessário executar o script várias vezes. Quando solicitado, digite o SID do SAP HANA da instância que você deseja testar para fazer failover. Após a conclusão, o script mostrará uma lista de pontos de montagem dos volumes que são adicionados à unidade do SAP HANA em Instâncias Grandes. Essa lista inclui os volumes de recuperação de desastres clonados.

Prossiga para a etapa 4.

   >[!NOTE]
   >Se você precisar fazer failover no site de recuperação de desastre para resgatar alguns dados que foram excluídos há algumas horas e precisar que os volumes de DR sejam definidos para um instantâneo anterior, este procedimento se aplicará. 

1. Desligue a instância de não produção do HANA na unidade de recuperação de desastre do SAP HANA nas Instâncias Grandes que você está executando. Isso ocorre porque há uma instância de produção do HANA inativa pré-instalada.
1. Certifique-se de que não há nenhum processo do SAP HANA em execução. Use o comando a seguir para essa verificação: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A saída agora deve mostrar o processo **hdbdaemon** em um estado interrompido e nenhum outro processo do HANA em estado de execução ou iniciado.
1. Determine para qual nome de instantâneo ou ID de backup do SAP HANA você deseja ter a recuperação de desastre restaurada. Em casos de recuperação de desastres reais, esse instantâneo é geralmente o instantâneo mais recente. Se você precisar recuperar dados perdidos, escolha um instantâneo mais antigo.
1. Contate o Suporte do Azure por meio de uma solicitação de suporte de alta prioridade. Solicite a restauração desse instantâneo (com o nome e a data do instantâneo) ou a ID de backup do HANA no site de DR. O padrão é que o lado das operações restaura apenas o volume /hana/data. Se você quiser ter os volumes /hana/logbackups também, será necessário declarar isso especificamente. *Não restaure o volume/hana/shared.* Em vez disso, você deve escolher arquivos específicos, como global.ini do diretório **.snapshot** e de seus subdiretórios após montar novamente o volume /hana/shared para PRD. 

   No lado das operações, ocorrem as seguintes etapas:

   a. A replicação de instantâneos do volume de produção para volumes de recuperação de desastre é interrompida. Isso pode já ter acontecido se uma interrupção no local de produção for o motivo de você precisar realizar o procedimento de recuperação de desastre.
   
   b. O nome do instantâneo de armazenamento ou o instantâneo com a ID de backup escolhido é restaurado nos volumes de recuperação de desastre.
   
   c. Após a restauração, os volumes de recuperação de desastre estão disponíveis para serem montados para as unidades do SAP HANA em Instâncias Grandes na região de recuperação de desastre.
      
1. Monte os volumes de recuperação de desastre para a unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre. 
1. Inicie a instância de produção do SAP HANA inativa.
1. Se optar por copiar logs de backup de log de transações para reduzir o tempo de RPO, será necessário mesclar esses backups de log de transações no diretório /hana/logbackups da recuperação de desastre montado recentemente. Não substitua backups existentes. Copie os backups mais recentes que não foram replicados com a replicação mais recente de um instantâneo de armazenamento.
1. Também é possível restaurar arquivos únicos dos instantâneos que foram replicados para o volume /hana/shared/PRD na região do Azure de recuperação de desastre.

A próxima sequência de etapas envolve a recuperação da instância de produção SAP HANA com base no instantâneo de armazenamento restaurado e nos backups de log de transações disponíveis:

1. Altere o local de backup para **hana/logbackups** usando o SAP HANA Studio.
   ![Alterar o local de backup para recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. O SAP HANA verifica os locais do arquivo de backup e sugere o backup de log de transações mais recente para o qual ser restaurado. A verificação pode levar alguns minutos até que uma tela como a seguinte seja exibida: ![Lista de backups de log de transações para a recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas destas configurações padrão:

      - Limpe **Usar backups delta**.
      - Selecione **Inicializar área de log**.

   ![Defina o Inicializar a área de log](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Concluir a restauração da recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em mente que o exemplo é de uma restauração de recuperação de desastre de uma configuração de expansão do SAP HANA de três nós.

![Progresso da restauração](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se a restauração parece parar de responder na tela **Concluir** e não for mostrada na tela de andamento, confirme se todas as instâncias do SAP HANA nos nós de trabalho estão em execução. Se necessário, inicie manualmente as instâncias SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback de recuperação de desastre para um site de produção
É possível executar failback de uma recuperação de desastre para um local de produção. Vejamos um cenário em que o failover no site de recuperação de desastre foi causado por problemas na região de produção do Azure e não pela necessidade de recuperar dados perdidos. Você estava executando sua carga de trabalho de produção SAP por um tempo no site de recuperação de desastre. Conforme são resolvidos os problemas no local de produção, convém executar failback no local de produção. Como não é possível perder dados, a etapa no local de produção envolve várias etapas e cooperação com a equipe de operações do SAP HANA no Azure. Cabe a você acionar a equipe de operações para iniciar a sincronização de volta ao site de produção, depois que os problemas forem resolvidos.

Esta é a sequência de etapas a serem seguidas:

1. A equipe de operações do SAP HANA do Azure obtém o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastre, que agora representam o estado de produção. Nesse estado, a unidade de Instância Grande do HANA no local de produção é desligada.
1. A equipe de operações do SAP HANA do Azure monitora a replicação e garante que seja obtida antes de informá-lo.
1. Desligue os aplicativos que usam a instância do HANA de produção no site de recuperação de desastre. Em seguida, faça um backup de log de transações do HANA. Depois, você interrompe a instância do HANA em execução nas unidades do SAP HANA em Instâncias Grandes no site de recuperação de desastre.
1. Após a instância do HANA em execução na unidade do SAP HANA em Instâncias Grandes no site de recuperação de desastre ser desligada, a equipe de operações sincronizará manualmente os volumes de disco novamente.
1. A equipe de operações do SAP HANA no Azure inicia a unidade da Instância Grande do HANA no local de produção novamente e a passa para você. Garanta que a instância do SAP HANA está em um estado de desligamento no tempo de inicialização da unidade do SAP HANA em Instâncias Grandes.
1. Execute as mesmas etapas de restauração de banco de dados como fez no failover para o site de recuperação de desastre anteriormente.

## <a name="monitor-disaster-recovery-replication"></a>Monitorar a replicação de recuperação de desastre

É possível monitorar o status do andamento da sua replicação de armazenamento executando o script `azure_hana_replication_status.pl`. Esse script deve ser executado a partir de uma unidade em execução no site de recuperação de desastre para funcionar conforme o esperado. O script funciona independentemente da replicação estar ativa. O script pode ser executado para cada unidade de Instância Grande HANA do seu locatário no local de recuperação de desastres. Ele não pode ser usado para obter detalhes sobre o volume de inicialização.

Chame o script com este comando:
```
./azure_hana_replication_status.pl
```

A saída é dividida, por volume, nas seguintes seções:  

- Status do link
- Atividade de replicação atual
- Instantâneo mais recente replicado 
- Tamanho do instantâneo mais recente
- Tempo de retardo atual entre instantâneos (entre a última replicação de instantâneo concluída e agora)

O status do link será mostrado como **Ativo**, a menos se o link entre os locais estiver inativo ou houver um evento de failover em andamento no momento. A atividade de replicação resolve se dados estão sendo replicados no momento ou se estão ociosos ou se outras atividades estão ocorrendo no momento para o link. O último instantâneo replicado deve ser exibido apenas como `snapmirror…`. O tamanho do último instantâneo é exibido. Por fim, o tempo de retardo é mostrado. O tempo de retardo representa o tempo desde a replicação agendada até o momento em que replicação é concluída. Um tempo de retardo pode ser maior do que uma hora para a replicação de dados, principalmente na replicação inicial, mesmo que a replicação tenha sido iniciada. O tempo de retardo continua aumentando até que a replicação em andamento seja concluída.

A seguir, é apresentando um exemplo da saída:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```

**Próximas etapas**
- Veja [Monitoramento e solução de problemas do HANA](hana-monitor-troubleshoot.md).

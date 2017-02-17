---
title: Recuperar dados de um Servidor de Backup do Azure | Microsoft Docs
description: "Recupere os dados já protegidos por um cofre de Backup do Azure de qualquer Servidor de Backup do Azure registrado nesse cofre."
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: adigan;giridham;trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 14cc190a7d1cde1181a6f26ef83095bc601f7fbb
ms.openlocfilehash: 36c4e1865c99dd1c55be798e1d310a02f2af0864


---
# <a name="recovering-data-from-another-azure-backup-server-in-the-backup-vault"></a>Recuperando dados de outro Servidor de Backup do Azure no cofre de backup
Agora você pode recuperar os dados já protegidos em um cofre de Backup do Azure de qualquer Servidor de Backup do Azure registrado nesse cofre. O processo para isso é totalmente integrado ao console de gerenciamento do ABS e é semelhante a outros fluxos de trabalho de recuperação.

> [!NOTE]
> Este artigo e as etapas a seguir se aplicam ao [System Center Data Protection Manager UR7] (https://support.microsoft.com/en-us/kb/3065246) com o [agente de Backup do Azure mais recente](http://aka.ms/azurebackup_agent).
>
>

## <a name="recover-data-from-another-azure-backup-server"></a>Recuperar dados de outro Servidor de Backup do Azure
Para recuperar dados de outro Servidor de Backup do Azure:

1. Na guia **Recuperação** no console de gerenciamento do ABS, clique em **'Adicionar DPM Externa'** (na parte superior esquerda da tela).   
    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Baixe as novas **credenciais do cofre** associadas ao **Servidor de Backup do Azure** cujos dados serão recuperados, escolha o Servidor de Backup do Azure na lista de Servidores de Backup do Azure registrados no cofre de backup e forneça a **senha de criptografia** associada ao servidor cujos dados serão recuperados.

    ![Credenciais do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Somente os Servidores de Backup do Azure associados ao mesmo cofre de registro podem recuperar dados um do outro.
   >
   >

    Depois que o Servidor de Backup do Azure externo é adicionado com êxito, você pode procurar os dados do servidor externo e do Servidor de Backup do Azure local na guia **Recuperação**.
3. Procure a lista de servidores de produção protegidos pelo Servidor de Backup do Azure externo disponíveis e selecione a fonte de dados apropriada.

    ![Procurar o servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **o mês e o ano** da lista suspensa **Pontos de recuperação**, selecione a **Data de recuperação** de quando o ponto de recuperação foi criado e selecione o **Tempo de recuperação**.

    Uma lista de arquivos e pastas será exibida no painel inferior, que pode ser pesquisado e recuperado em qualquer local.

    ![Pontos de recuperação de servidor DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique com o botão direito do mouse no item apropriado e clique em **Recuperar**.

    ![Recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/recover.png)
6. Examine **Recuperar seleção**. Verifique a data e a hora da cópia de backup que está sendo recuperada, bem como a fonte da qual a cópia de backup foi criada. Se a seleção estiver incorreta, clique em **Cancelar** para voltar à guia de recuperação e selecionar o ponto de recuperação apropriado. Se a seleção estiver correta, clique em **Avançar**.

    ![Resumo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **Recuperar em um local alternativo**. **Navegue** até o local correto para a recuperação.

    ![Local alternativo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionada a **criar cópia**, **Ignorar** ou **Substituir**.

   * **Criar cópia** criará uma cópia do arquivo em caso de conflito de nomes.
   * **Ignorar** ignorará a recuperação do arquivo em caso de conflito de nomes.
   * **Substituir** substituirá a cópia existente no local especificado em caso de conflito de nomes.

     Escolha a opção apropriada para **Restaurar a segurança**. Você pode aplicar as configurações de segurança do computador de destino onde os dados serão recuperados ou as configurações de segurança que eram aplicáveis ao produto no momento em que o ponto de recuperação foi criado.

     Identifique se uma **Notificação** será enviada quando a recuperação for concluída com êxito.

     ![Notificações de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. A tela **Resumo** lista as opções escolhidas até agora. Depois de clicar em **“Recuperar”**, os dados serão recuperados na localização local adequada.

    ![Resumo de opções de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > O trabalho de recuperação pode ser monitorado na guia **Monitoramento** do Servidor de Backup do Azure.
   >
   >

    ![Recuperação de monitoramento](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Você pode clicar em **Limpar DPM Externo** na guia **Recuperação** do servidor DPM para remover o modo de exibição do servidor DPM externo.

    ![Limpar o DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Solucionando problemas de mensagens de erro
| Nº | Mensagem de erro | Etapas para solucionar problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registrado no cofre especificado nas credenciais do cofre. |**Causa:** esse erro aparece quando o arquivo de credencial do cofre selecionado não pertence ao cofre de backup associado ao Servidor de Backup do Azure em que ocorre a tentativa de recuperação. <br> **Resolução:** baixe o arquivo de credencial de cofre do cofre de backup no qual o Servidor de Backup do Azure está registrado. |
| 2. |Os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM. |**Causa:** não existem outros Servidores de Backup do Azure registrados no cofre de backup, os servidores ainda não carregaram os metadados ou o servidor selecionado é não um Servidor de Backup do Azure (também conhecido como Windows Server ou Windows Client). <br> **Resolução:** se houver outros Servidores de Backup do Azure registrados no cofre de backup, verifique se o agente do Backup do Azure mais recente está instalado. <br>Se houver outros Servidores de Backup do Azure registrados no cofre de backup, aguarde um dia após a instalação para iniciar o processo de recuperação. A tarefa noturna carregará os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registrado no cofre. |**Causa:** não há nenhum outro Servidor de Backup do Azure registrado no cofre em que a recuperação está sendo tentada.<br>**Resolução:** se houver outros Servidores de Backup do Azure registrados no cofre de backup, verifique se o agente do Backup do Azure mais recente está instalado.<br>Se houver outros Servidores de Backup do Azure registrados no cofre de backup, aguarde um dia após a instalação para iniciar o processo de recuperação. A tarefa noturna carregará os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação. |
| 4. |A senha de criptografia fornecida não corresponde à senha associada ao seguinte servidor: **<server name>** |**Causa:** a senha de criptografia usada no processo de criptografia de dados dos dados do Servidor de Backup do Azure que estão sendo recuperados não corresponde à senha de criptografia fornecida. O agente não pode descriptografar os dados. Portanto, a recuperação falha.<br>**Resolução:** Forneça exatamente a mesma senha de criptografia associada ao Servidor de Backup do Azure cujos dados serão recuperados. |

## <a name="frequently-asked-questions"></a>Perguntas frequentes:
1. **Por que não consigo adicionar um servidor DPM externo de outro servidor DPM após instalar o UR7 e o agente de Backup do Azure mais recente? (Aplica-se caso você esteja usando SC DPM 2012 R2)**

    A) Para os servidores DPM existentes com fontes de dados que estão protegidas na nuvem (usando um pacote cumulativo de atualizações anterior ao Pacote de Cumulativo de Atualizações 7), é necessário aguardar pelo menos um dia depois de instalar o UR7 e o agente de Backup do Azure mais recente para começar a *Adicionar servidor DPM externo*. Isso é necessário para carregar os metadados dos grupos de proteção do DPM no Azure. Isso ocorre na primeira vez por meio de um trabalho noturno.
2. **Qual é a versão mínima necessária do agente de Backup do Azure?**

    A) A versão mínima do agente de Backup do Azure para habilitar esse recurso é a 2.0.8719.0.  A versão do agente de Backup do Azure pode ser verificada navegando até o Painel de Controle **>** Todos os Itens do Painel de Controle **>** Programas e recursos **>** Agente dos Serviços de Recuperação do Microsoft Azure. Se a versão for anterior a 2.0.8719.0, baixe o [agente do Backup do Azure mais recente](https://go.microsoft.com/fwLink/?LinkID=288905) e o instale.

    ![Limpar o DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Próximas etapas:
•    [Perguntas frequentes sobre o Backup do Azure](backup-azure-backup-faq.md)



<!--HONumber=Jan17_HO4-->



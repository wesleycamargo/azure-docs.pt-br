<properties
	pageTitle="Recuperando dados de outro servidor DPM no cofre de backup | Microsoft Azure"
	description="Recupere os dados já protegidos por um cofre de Backup do Azure de qualquer servidor DPM registrado nesse cofre."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="giridham;jimpark"/>

# Recuperando dados de outro servidor DPM no cofre de backup
Agora você pode recuperar os dados já protegidos em um cofre de Backup do Azure de qualquer servidor DPM registrado nesse cofre. O processo para isso é totalmente integrado ao console de gerenciamento do DPM e é semelhante a outros fluxos de trabalho de recuperação.

Para recuperar dados de outro servidor DPM no cofre de backup, você precisará do [System Center Data Protection Manager UR7](https://support.microsoft.com/pt-BR/kb/3065246) e do [agente do Backup do Azure mais recente](http://aka.ms/azurebackup_agent).

## Recuperar dados de outro Servidor DPM
Para recuperar dados de outro servidor DPM:

1. Na guia **Recuperação** no console de gerenciamento do DPM, clique em **“Adicionar DPM Externa”** (na parte superior esquerda da tela).

    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)

2. Baixe as novas **credenciais do cofre** associadas ao **servidor DPM** cujos dados serão recuperados, escolha o servidor DPM na lista de servidores DPM registrados no cofre de backup e forneça a **senha de criptografia** associada ao servidor DPM cujos dados serão recuperados.

    ![Credenciais do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

    >[AZURE.NOTE]Somente os servidores DPM associados ao mesmo cofre de registro podem recuperar dados um do outro.

    Depois que o servidor DPM externo for adicionado com êxito, é possível procurar os dados dos servidores DPM externo e local na guia **Recuperação**.

3. Procure a lista de servidores de produção protegidos pelo servidor DPM externo disponíveis e selecione a fonte de dados apropriada.

    ![Procurar o servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)

4. Selecione **o mês e o ano** na lista suspensa **Pontos de recuperação**, selecione a **Data de recuperação** solicitada para a criação do ponto de recuperação e selecione a **hora de recuperação**.

    Uma lista de arquivos e pastas será exibida no painel inferior, que pode ser pesquisado e recuperado em qualquer local.

    ![Pontos de recuperação de servidor DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)

5. Clique com o botão direito do mouse no item apropriado e clique em **Recuperar**.

    ![Recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/recover.png)

6. Examine **Recuperar seleção**. Verifique a data e a hora da cópia de backup que está sendo recuperada, bem como a fonte da qual a cópia de backup foi criada. Se a seleção estiver incorreta, clique em **Cancelar** para voltar à guia de recuperação e selecionar o ponto de recuperação apropriado. Se a seleção estiver correta, clique em **Avançar**.

    ![Resumo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)

7. Selecione **Recuperar em um local alternativo**. **Navegue** até o local correto para a recuperação.

    ![Local alternativo de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)

8. Escolha a opção relacionada a **criar cópia**, **Ignorar** ou **Substituir**.
    - **Criar cópia** criará uma cópia do arquivo em caso de conflito de nomes.
    - **Ignorar** ignorará a recuperação do arquivo em caso de conflito de nomes.
    - **Substituir** substituirá a cópia existente no local especificado em caso de conflito de nomes.

    Escolha a opção apropriada para **Restaurar a segurança**. Você pode aplicar as configurações de segurança do computador de destino onde os dados serão recuperados ou as configurações de segurança que eram aplicáveis ao produto no momento em que o ponto de recuperação foi criado.

    Identifique se uma **Notificação** será enviada quando a recuperação for concluída com êxito.

    ![Notificações de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)

9. A tela **Resumo** lista as opções escolhidas até agora. Depois de clicar em **“Recuperar”**, os dados serão recuperados na localização local adequada.

    ![Resumo de opções de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

    >[AZURE.NOTE]O trabalho de recuperação pode ser monitorado na guia **Monitoramento** do servidor DPM.

    ![Recuperação de monitoramento](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)

10. Você pode clicar em **Limpar DPM Externo** na guia **Recuperação** do servidor DPM para remover o modo de exibição do servidor DPM externo.

    ![Limpar o DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## Solucionando problemas de mensagens de erro
|Nº |	Mensagem de erro |	Etapas para solucionar problemas |
| :-------------: |:-------------| :-----|
|1\.|		Este servidor não está registrado no cofre especificado nas credenciais do cofre.|	**Causa:** esse erro aparece quando o arquivo de credencial do cofre selecionado não pertence ao cofre de backup associado ao servidor DPM em que ocorre a tentativa de recuperação. <br> **Resolução:** baixe o arquivo de credencial de cofre do cofre de backup no qual o servidor DPM está registrado.|
|2\.|		Os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM.|	**Causa:** não existem outros servidores DPM com o DPM 2012 R2 UR7 registrado no cofre de backup, ou os servidores DPM com o DPM 2012 R2 UR7 ainda não carregaram os metadados ou o servidor selecionado é não um servidor DPM (também conhecido como Windows Server ou Windows Client). <br> **Resolução:** se houver outros servidores DPM registrados no cofre de backup, assegure-se de que o SCDPM 2012 R2 UR7 e o agente do Backup do Azure mais recente estão instalados. <br>Se houver outros servidores DPM registrados no cofre de backup com DPM 2012 R2 UR7, aguarde um dia após a instalação do UR7 para iniciar o processo de recuperação. A tarefa noturna carregará os metadados de todos os backups protegidos anteriormente para a nuvem. Os dados estarão disponíveis para recuperação.|
|3\.|		Nenhum outro servidor DPM está registrado no cofre.|	**Causa:** não há nenhum outro servidor DPM com o DPM 2012 R2 UR7 ou posterior que esteja registrado no cofre do qual há uma tentativa de recuperação.<br>**Resolução:** Se houver outros servidores DPM registrados no cofre de backup, garanta que o SCDPM 2012 R2 UR7 e a versão mais recente do agente do Backup do Azure estão instalados.<br>Se houver outros servidores DPM registrados no cofre de backup com o DPM 2012 R2 UR7, aguarde um dia após a instalação do UR7 para iniciar o processo de recuperação. A tarefa noturna carregará os metadados de todos os backups protegidos anteriormente para a nuvem. Os dados estarão disponíveis para recuperação.|
|4\.|		A senha de criptografia fornecida não corresponde à senha associada ao seguinte servidor: **<server name>**|	**Causa:** a senha de criptografia usada no processo de criptografia de dados dos dados do servidor DPM que estão sendo recuperados não corresponde à senha de criptografia fornecida. O agente não pode descriptografar os dados. Portanto, a recuperação falha.<br>**Resolução:** Forneça exatamente a mesma senha de criptografia associada ao servidor DPM cujos dados serão recuperados.|

## Perguntas frequentes:
1. **Por que não consigo adicionar um servidor DPM externo de outro servidor DPM após instalar o UR7 e o agente de Backup do Azure mais recente?**

    A) Para os servidores DPM existentes com fontes de dados que estão protegidas na nuvem (usando um pacote cumulativo de atualizações anterior ao Pacote de Cumulativo de Atualizações 7), é necessário aguardar pelo menos um dia depois de instalar o UR7 e o agente de Backup do Azure mais recente para começar a *Adicionar servidor DPM externo*. Isso é necessário para carregar os metadados dos grupos de proteção do DPM no Azure. Isso ocorre na primeira vez por meio de um trabalho noturno.

2. **Qual é a versão mínima necessária do agente de Backup do Azure?**

    A) A versão mínima do agente de Backup do Azure para habilitar esse recurso é a 2.0.8719.0. A versão do agente de Backup do Azure pode ser verificada navegando até o Painel de Controle **>** Todos os Itens do Painel de Controle **>** Programas e recursos **>** Agente dos Serviços de Recuperação do Microsoft Azure. Se a versão for anterior a 2.0.8719.0, baixe o [agente do Backup do Azure mais recente](https://go.microsoft.com/fwLink/?LinkID=288905) e o instale.

    ![Limpar o DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## Próximas etapas:
• [Perguntas frequentes sobre o Backup do Azure](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_1217_2015-->
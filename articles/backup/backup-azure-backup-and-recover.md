<properties
   pageTitle="Backup do Azure - Backup e restauração de um cliente Windows ou Windows Server"
   description="Saiba como fazer backup e restaurar em um cliente Windows ou Windows Server. O artigo também aborda a recuperação de servidor alternativo"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# Backup e restauração de um computador cliente Windows ou Windows Server
Este artigo aborda as etapas necessárias para backup de um computador cliente Windows ou Windows Server. Ele também aborda as etapas necessárias para restaurar os arquivos de backup no mesmo computador e as etapas necessárias para restaurar arquivos de backup em qualquer outro computador.

## Arquivos de backup
1. Depois que o computador for registrado, abra o snap-in do MMC do Backup do Microsoft Azure. <br/> ![Resultado da pesquisa][1]

2. Clique em **Agendar Backup** <br/> ![Agendar backup][2]

3. Selecione os itens dos quais você deseja fazer backup. O Backup do Azure em um cliente Windows/Windows Server (ou seja, sem o System Center Data Protection Manager) permite proteger arquivos e pastas. <br/> ![Itens para backup][3]

4. Especifique o agendamento de backup e a política de retenção, que é explicada em detalhes no [artigo](backup-azure-backup-cloud-as-tape.md) a seguir

5. Escolha o método de envio do backup inicial. Sua escolha de concluir a propagação inicial depende da quantidade de dados de que você deseja fazer backup e a velocidade do link de carregamento da Internet. Se você planeja fazer backup de GBs/ TBs de dados em uma conexão de baixa largura de banda e alta latência, é recomendável realizar o backup inicial enviando um disco para o datacenter do Azure mais próximo. Isso é chamado de "Backup Offline" e é abordado em detalhes neste [artigo](https://msdn.microsoft.com/library/azure/dn894419.aspx). Se você tiver uma conexão com largura de banda suficiente, é recomendável que conclua o backup inicial pela rede. <br/> ![Backup inicial][4]

6. Quando o fluxo de trabalho for concluído, vá novamente ao snap-in do MMC e clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede. <br/> ![Fazer backup agora][5]

7. Depois que a propagação inicial for concluída, a exibição **Trabalhos** no console do Backup do Azure indicará o status. <br/> ![IR completo][6]

## Recuperar dados no mesmo computador
Se você excluiu acidentalmente um arquivo e quiser restaurar um arquivo/volume no mesmo computador (do qual o backup é feito), as etapas a seguir ajudariam a recuperar os dados.

1. Abra o snap-in do **Backup do Microsoft Azure**.

2. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho. <br/> ![Recuperar dados][7]

3. Selecione a opção **Neste servidor (*nomedasuamáquina*)** quando planejar restaurar o arquivo de backup no mesmo computador. <br/> ![Mesmo computador][8]

4. Você pode optar por **Procurar arquivos** ou **Pesquisar arquivos**. Deixe a opção padrão se você planeja restaurar um ou mais arquivos cujo caminho é conhecido. Se você não tiver certeza sobre a estrutura de pastas, mas gostaria de procurar por um arquivo, selecione a opção **Pesquisar arquivos**. Para fins desta seção, prosseguiremos com a opção padrão. <br/> ![Procurar arquivos][9]

5. Na próxima tela, selecione o volume do qual você deseja restaurar o arquivo. A tela permite restaurar de qualquer ponto no tempo. As datas que aparecem em **negrito** no controle de calendário indicam a disponibilidade de um ponto de restauração. Quando uma data é selecionada, com base em seu agendamento de backup (e o sucesso de uma operação de backup), você pode selecionar um ponto no tempo no menu suspenso **Tempo**. <br/> ![Volume e data][10]

6. Na próxima tela, selecione os itens que você deseja recuperar. Você pode selecionar várias pastas/arquivos que deseja restaurar. <br/> ![Selecionar arquivos][11]

7. Na próxima tela, especifique os parâmetros de recuperação. <br/> ![Opções de recuperação][12]
  + Você tem uma opção de restaurar no local original (em que a pasta/o arquivo será substituído) ou em outro local no mesmo computador.

  + Se o arquivo/pasta que você deseja restaurar existir no local de destino, você tem a opção de criar cópias (duas versões do mesmo arquivo), substituir os arquivos no local de destino ou ainda ignorar a recuperação dos arquivos que existem no destino.

  + É altamente recomendável que você deixe a opção padrão de restaurar as ACLs nos arquivos que estão sendo recuperados.

8. Após essas entradas serem fornecidas, o fluxo de trabalho de recuperação é iniciado, o que restaura os arquivos nesse computador.

## Recuperar em um computador alternativo
Se o servidor inteiro for perdido, você ainda poderá recuperar o arquivo/volume em um computador diferente. As etapas a seguir ilustram o fluxo de trabalho.

A nomenclatura usada nas etapas é a seguinte: + *Computador de origem* – O computador original do qual o backup foi feito e que está indisponível no momento.

  + *Computador de destino* – O computador no qual os dados estão sendo recuperados.

  + *Cofre de exemplo* – Cofre de backup em que o *Computador de origem* e o *Computador de destino* são registrados. <br/>

> [AZURE.NOTE]Os backups de um computador não podem ser restaurados em um computador que esteja executando uma versão anterior do sistema operacional. Por exemplo, se um backup for de um computador com Windows 7, ele poderá ser restaurado em um computador com Windows 8 ou superior. No entanto, o contrário não pode ocorrer.

1. Abra o snap-in do **Backup do Microsoft Azure** no *Computador de destino*.

2. Certifique-se de que o *Computador de destino* e o *Computador de origem* sejam restaurados no mesmo cofre de backup (neste artigo, *Cofre de exemplo*).

3. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho. <br/> ![Recuperar dados][7]

4. Selecione **Outro servidor** <br/> ![Outro servidor][13]

5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo*. Se o arquivo de credencial do cofre for inválido (ou estiver expirado), baixe um novo arquivo de credencial do *Cofre de exemplo* no Portal do Azure. Depois que o arquivo de credencial de cofre for fornecido, o cofre de backup relacionado ao arquivo de credencial de cofre será exibido.

6. Selecione o *Computador de origem* na lista de computadores exibidos. <br/> ![Lista de computadores][14]

7. Como antes, selecione **Pesquisar arquivos** ou **Procurar arquivos**. Para fins desta seção, vamos usar a opção **Pesquisar arquivos**. <br/> ![Pesquisar][15]

8. Na próxima tela, selecione o volume e a data. Procure o nome do arquivo/pasta que você deseja restaurar. <br/> ![Pesquisar itens][16]

9. Selecione o local no qual os arquivos precisam ser restaurados. <br/> ![Restaurar local][17]

10. Forneça a senha de criptografia que foi fornecida durante o registro do *Computador de origem* no *Cofre de exemplo*. <br/> ![Criptografia][18]

Após a entrada ser fornecida, clique no botão **Recuperar** que inicia a restauração dos arquivos de backup no destino fornecido.

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png
 

<!---HONumber=62-->
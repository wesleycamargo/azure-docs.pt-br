<properties
	pageTitle="Backup do Azure - backup e restauração a partir de um cliente Windows ou Windows Server"
	description="Saiba como fazer backup e restaurar a partir de um cliente Windows ou Windows Server. O artigo também aborda a recuperação em servidor alternativo"
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

# Backup e restauração de um computador de cliente do Windows ou o Windows server
Este artigo aborda as etapas necessárias para backup de um servidor do Windows ou uma máquina de cliente do Windows. Ela também aborda as etapas necessárias para restaurar o backup de arquivos no mesmo computador e as etapas necessárias para restaurar o backup dos arquivos em qualquer outro computador.

## Arquivos de backup
1. Quando a máquina estiver registrada, abra o snap-in mmc de Backup do Microsoft Azure. <br/> ![Resultado da pesquisa][1]

2. Clique em **Agendar Backup** <br/> ![Agendar Backup][2]

3. Selecione os itens que você deseja fazer backup. O Backup do Azure em um cliente de servidor de Windows do Windows (ou seja sem o System Center Data Protection Manager) permite proteger arquivos e pastas. <br/> ![Itens para Backup][3]

4. Especificar a política de retenção e da agenda de backup que é explicada em detalhes a seguir [artigo](backup-azure-backup-cloud-as-tape.md)

5. Escolha o método de envio o backup inicial. Sua escolha de concluir a propagação inicial depende da quantidade de dados que você deseja fazer backup e a velocidade de link de carregamento da internet. Se você planeja backup GB / TB de dados em uma alta latência, de conexões de largura de banda baixa, é recomendável que você conclua o backup inicial pelo envio de um disco para o mais próximo data center do Azure. Isso é chamado de "Backup Offline" e é abordado em detalhes nesta [artigo](https://msdn.microsoft.com/library/azure/dn894419.aspx). Se você tiver conexões de largura de banda suficiente é recomendável que você conclua o backup inicial pela rede. <br/> ![Backup inicial][4]

6. Quando o fluxo de trabalho for concluído, vá novamente ao mmc snap-in e clique em **backup agora** para concluir a propagação inicial pela rede. <br/> ![Fazer backup agora][5]

7. Depois que a propagação inicial for concluída, o **trabalhos** exibição no console do Azure Backup indica o status. <br/> ![RI completa][6]

## Recuperar dados na mesma máquina
Se você excluiu acidentalmente um arquivo e se você quiser restaurar um arquivo/volume na mesma máquina (do qual o backup é feito), as etapas a seguir seriam ajudá-lo a recuperar os dados.

1. Abra o **Microsoft Azure Backup** encaixar no.

2. Clique em **recuperar dados** para iniciar o fluxo de trabalho. <br/> ![Recuperar dados][7]

3. Selecione **neste servidor (* * nomedasuamáquina)** opção quando você planeja restaurar o backup de arquivo na mesma máquina. <br/> ![Mesma máquina][8]

4. Você pode optar por **Procurar arquivos** ou **Pesquisar arquivos**. Deixe a opção padrão, se você planeja restaurar um ou mais arquivos cujo caminho é conhecido. Se você não tiver certeza sobre a estrutura de pasta, mas gostaria de procurar por um arquivo, selecione o **Pesquisar arquivos** opção. Para fins desta seção, prosseguiremos com a opção padrão. <br/> ![Procurar arquivos][9]

5. Na próxima tela, selecione o volume do qual você deseja restaurar o arquivo. Habilita a tela que restaurar de qualquer ponto no tempo. Datas que aparecem no **negrito** no controle de calendário indicar a disponibilidade de um ponto de restauração. Quando uma data é selecionada, com base em sua agenda de backup (e o sucesso de uma operação de backup), você pode selecionar um ponto no tempo para o **tempo** suspensa. <br/> ![Volume e data][10]

6. Na próxima tela, selecione os itens que você deseja recuperar. Você pode selecionados várias pastas/arquivos que você deseja restaurar. <br/> ![Selecionar arquivos][11]

7. Na próxima tela, especifique os parâmetros de recuperação. <br/> ![Opções de recuperação][12]
  + Você tem uma opção de restauração no local original (no qual o arquivo será substituído) ou em outro local na mesma máquina.

  + Se o arquivo/pasta que você deseja restaurar, existir no local de destino, você tem a opção de criar cópias (duas versões do mesmo arquivo), ou substituir os arquivos no local de destino ou ignorar a recuperação dos arquivos que existem no destino.

  + É altamente recomendável que você deixe a opção padrão de restaurar as ACLs nos arquivos que estão sendo recuperados.

8. Depois que essas entradas são fornecidas, o fluxo de trabalho de recuperação é iniciado, que restaura os arquivos neste computador.

## Recuperar em uma máquina alternativa
Se o servidor inteiro for perdido, você ainda poderá recuperar o arquivo/volume em uma máquina diferente. As etapas a seguir ilustram o fluxo de trabalho.

A nomenclatura usada nas etapas são as seguintes: + *máquina de origem* – a máquina original do que o backup foi feito e que está disponível no momento.

  + *Máquina de destino* – a máquina na qual os dados estão sendo recuperados.

  + *Cofre exemplo* – Cofre de Backup o que o *máquina de origem* e *máquina de destino* são registrados. <br/>

> [AZURE.NOTE]Os backups de uma máquina não podem ser restaurados em um computador que esteja executando uma versão anterior do sistema operacional. Por exemplo, se os backups são obtidos de um computador com Windows 7, ele pode ser restaurado em um Windows 8 ou superior a máquina. No entanto o contrário não se efetivará.

1. Abra o **Microsoft Azure Backup** encaixar no *máquina de destino*.

2. Verifique se o *máquina de destino* e o *máquina de origem* são restaurados no cofre de Backup mesmo (neste artigo - *cofre exemplo*).

3. Clique em **recuperar dados** para iniciar o fluxo de trabalho. <br/> ![Recuperar dados][7]

4. Selecione **outro servidor** <br/> ![Outro servidor][13]

5. Forneça o arquivo de credencial de cofre que corresponde do *cofre exemplo*. Se o arquivo de credencial do cofre é inválido (ou expirado), baixe um novo arquivo de credencial do cofre do *cofre exemplo* no portal do Azure. Depois que o arquivo de credencial do cofre é fornecido, o Cofre de backup com o arquivo de credencial do cofre é exibido.

6. Selecione o *máquina de origem* da lista de computadores exibidas. <br/> ![Lista de máquinas][14]

7. Selecione como antes de **Pesquisar arquivos** ou **Procurar arquivos** opção. Para fins desta seção, vamos usar o **Pesquisar arquivos** opção. <br/> ![Pesquisar][15]

8. Na próxima tela, selecione o volume e data. Procure o nome do arquivo/pasta que você deseja restaurar. <br/> ![Pesquisar itens][16]

9. Selecione o local para o qual os arquivos precisam ser restaurados. <br/> ![Restaurar local][17]

10. Forneça a senha de criptografia que foi fornecida durante *da máquina de origem* registro *cofre exemplo*. <br/> ![Criptografia][18]

Depois que a entrada for fornecida, clique no **recuperar** botão que dispara a restaura o backup de arquivos no destino fornecido.

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

<!---HONumber=GIT-SubDir-->
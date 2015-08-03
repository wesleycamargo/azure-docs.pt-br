<properties
   pageTitle="Backup do Azure - Backup offline ou propagação inicial usando o serviço de Importação/Exportação do Azure | Microsoft Azure"
   description="Saiba como o Backup do Azure permite que você envie dados fora da rede usando o serviço de Importação/Exportação do Azure. Este artigo explica a propagação offline de dados de backup iniciais usando o serviço de Importação/Exportação do Azure"
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/> <tags ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/14/2015" ms.author="aashishr"; "jimpark"/>

# Fluxo de trabalho de backup offline no Backup do Azure

O Backup do Azure é profundamente integrado ao serviço de Importação/Exportação do Azure, que permite transferir dados de backup iniciais rapidamente. Se você tiver TBs de dados de backup iniciais que precisam ser transferidos através de uma rede de alta latência e baixa largura de banda, pode usar o serviço de Importação/Exportação do Azure para enviar a cópia de backup inicial em um ou mais discos rígidos para um data center do Azure. Este artigo fornece uma visão geral das etapas necessárias para realizar esse fluxo de trabalho.

## Visão geral

Com o Backup do Azure e a Importação/Exportação do Azure, é simples e fácil carregar os dados no Azure offline por meio de discos. Em vez de transferir a cópia completa inicial através da rede, os dados de backup são gravados em um *local de preparo*. O local de preparo pode ser um armazenamento de conexão direta ou um compartilhamento de rede. Depois que a cópia inicial for concluída, usando a *Ferramenta de Importação/Exportação do Azure*, esses dados são gravados em uma unidade SATA que posteriormente é enviada para o datacenter do Azure. Dependendo do tamanho do backup inicial, uma ou mais unidades SATA podem ser necessárias para concluir essa operação. A ferramenta de Importação/Exportação do Azure é responsável por esses cenários. Depois que os backups são gravados no disco, eles podem ser enviados para a instalação de datacenter mais próxima para serem carregados no Azure. Em seguida, o Backup do Azure copia os dados de backup para o cofre de backup e os backups incrementais são agendados.

## Pré-requisitos

1. É importante familiarizar-se com o fluxo de trabalho de exportação da Importação do Azure listado [aqui](../storage-import-export-service.md).
2. Antes de iniciar o fluxo de trabalho, certifique-se de que foi criado um cofre de backup do Azure, as credenciais do cofre foram baixadas, o agente do Backup do Azure foi instalado no seu Windows Server/cliente Windows/System Center Data Protection Manager (SCDPM) e de que o computador está registrado no cofre do Backup do Azure.
3. Baixe as configurações do arquivo de publicação do Azure [aqui](https://manage.windowsazure.com/publishsettings) no computador por meio do qual você planeja fazer backup dos nossos dados.
4. Prepare um *local de preparo*, que pode ser um compartilhamento de rede ou um disco adicional no computador. Certifique-se de que o local de preparo tenha espaço em disco suficiente para armazenar sua cópia inicial. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparo tenha pelo menos 500 GB (embora uma quantidade menor seja usada). A área de preparo é um “armazenamento provisório” e é usada temporariamente durante esse fluxo de trabalho.
5. Gravador de unidade SATA externa e uma unidade SATA externa de 3,5 polegadas. Somente discos rígidos SATA II de 3,5 polegadas dão suporte ao uso com o serviço Importar/Exportar. Não há suporte para discos rígidos com mais de 4 TB. Você pode anexar um disco SATA II/III externamente a maioria dos computadores usando um adaptador USB para SATA II/III. Consulte a documentação da Importação/Exportação do Azure para saber o conjunto mais recente de unidades aceitas pelo serviço.
6. Habilite o BitLocker no computador ao qual o gravador de unidade SATA está conectado.
7. Baixe a ferramenta de Importação/Exportação do Azure [aqui](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) no computador ao qual o gravador de unidade SATA está conectado.

## Fluxo de trabalho
As informações fornecidas nesta seção são para concluir o fluxo de trabalho de **Backup Offline** para que os dados possam ser entregues em um datacenter do Azure e carregados no armazenamento do Azure. Se você tiver dúvidas sobre o serviço de Importação ou qualquer aspecto do processo, consulte a visão geral do serviço de Importação indicada [acima](../storage-import-export-service.md).

### Iniciar backup offline

1. Como parte do agendamento de um backup, você encontrará a tela a seguir (no Windows Server, cliente Windows ou SCDPM).

    ![Tela de importação](./media/backup-azure-backup-import-export/importscreen.png)

    A tela correspondente no SCDPM será semelhante ao seguinte. <br/> ![Tela de importação do DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    onde:

    - **Local de preparo**: refere-se ao local de armazenamento temporário no qual a cópia de backup inicial é gravada. Pode ser em um compartilhamento de rede ou no computador local.
    - **Nome do trabalho de importação do Azure**: como parte desse fluxo de trabalho, você precisará criar um *Trabalho de importação* no Portal do Azure (abordado na parte posterior do documento). Forneça uma entrada que você planeja usar posteriormente no Portal do Azure também.
    - **Configurações de publicação do Azure**: esse é um arquivo XML que contém informações sobre seu perfil de assinatura. Ele também contém credenciais seguras associadas à sua assinatura. O arquivo pode ser baixado [aqui](https://manage.windowsazure.com/publishsettings). Forneça o caminho local para o arquivo de configurações de publicação.
    - **ID da assinatura do Azure**: forneça a ID de assinatura do Azure no qual você planeja iniciar o trabalho de importação do Azure. Se você tiver várias assinaturas do Azure, use a ID associada ao trabalho de importação.
    - **Conta de armazenamento do Azure**: insira o nome da conta de armazenamento do Azure que será associado a esse trabalho de importação.
    - **Contêiner de armazenamento do Azure**: insira o nome do blob de armazenamento de destino onde os dados desse trabalho serão importados.

2. Conclua o fluxo de trabalho e selecione **Fazer Backup Agora** no MMC do Backup do Azure para iniciar a cópia de backup offline. O backup inicial é gravado na área de preparo como parte dessa etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para habilitar o fluxo de trabalho correspondente no SCDPM, clique no **Grupo de Proteção** e escolha a opção **Criar ponto de recuperação**. Em seguida, escolha a opção **Proteção Online**.

    ![Fazer backup agora do DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Quando a operação for concluída, um arquivo *.AIBBlob* e um *.BaseBlob* serão criados no local de preparo.

    ![Saída](./media/backup-azure-backup-import-export/opbackupnow.png)

### Preparar a unidade SATA

1. Baixe a [ferramenta de Importação/Exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) no *computador de cópia*. Verifique se o local de preparo (na etapa anterior) está acessível no computador no qual você pretende executar o próximo conjunto de comandos. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

2. Descompacte o arquivo *WAImportExport.zip*. Execute a ferramenta *WAImportExport*, que formatará a unidade SATA, gravará os dados de backup na unidade SATA e vai criptografá-la. Antes de executar o comando a seguir, certifique-se de que o BitLocker esteja habilitado no computador. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*


| Parâmetro | Descrição
|-------------|-------------|
| /j:<*JournalFile*>| O caminho para o arquivo de diário. Cada unidade deve ter exatamente um arquivo de diário. Observe que o arquivo de diário não deve residir na unidade de destino. A extensão de arquivo de diário é .jrn e é criada como parte da execução desse comando.|
|/id:<*SessionId*> | A ID de sessão identifica uma *sessão de cópia*. Ela é usada para garantir a recuperação correta de uma sessão de cópia interrompida. Os arquivos copiados em uma sessão de cópia são armazenados em um diretório nomeado como a ID de sessão na unidade de destino.|
| /sk:<*StorageAccountKey*> | A chave da conta de armazenamento para a qual os dados serão importados. |
| /BlobType | Especifique **PageBlob**, esse fluxo de trabalho terá êxito somente se a opção PageBlob for especificada. Essa não é a opção padrão e deve ser mencionada nesse comando. |
|/t:<*TargetDriveLetter*> | A letra da unidade do disco rígido de destino para a sessão de cópia atual, sem dois-pontos no final.|
|/format | Especifique esse parâmetro quando for necessário formatar a unidade; caso contrário, omita-o. Antes de a ferramenta formatar a unidade, ela solicitará uma confirmação no console. Para suprimir a confirmação, especifique o parâmetro /silentmode.|
|/encrypt | Especifique esse parâmetro quando a unidade ainda não tiver sido criptografada com o BitLocker e precisar ser criptografada pela ferramenta. Se a unidade já tiver sido criptografada com o BitLocker, omita esse parâmetro e especifique o parâmetro /bk, fornecendo a chave do BitLocker existente. Se você especificar o parâmetro /format, também deverá especificar o parâmetro /encrypt. |
|/srcdir:<*SourceDirectory*> | O diretório de origem que contém os arquivos a serem copiados para a unidade de destino. O caminho do diretório deve ser um caminho absoluto (não um caminho relativo).|
|/dstdir:<*DestinationBlobVirtualDirectory*> | O caminho para o diretório virtual de destino em sua conta de armazenamento do Microsoft Azure. Certifique-se de usar nomes de contêineres válidos ao especificar diretórios virtuais ou blobs de destino. Tenha em mente que os nomes de contêiner devem estar em minúsculas.|

  >[AZURE.NOTE]Um arquivo de diário é criado na pasta WAImportExport que captura todas as informações do fluxo de trabalho. Ao criar um trabalho de importação no Portal do Azure, você precisará desse arquivo.

  ![Saída do PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Criar trabalho de importação no Portal do Azure
1. Navegue até a sua conta de armazenamento no [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Importação/Exportação** e, em seguida, em **Criar Trabalho de Importação** no painel de tarefas.

    ![Portal](./media/backup-azure-backup-import-export/azureportal.png)

2. Na Etapa 1 do assistente, indique que você preparou a unidade e que o arquivo de diário de unidade está disponível. Na Etapa 2 do assistente, forneça as informações de contato da pessoa responsável por esse trabalho de importação.
3. Na Etapa 3, carregue os arquivos de diário de unidade obtidos durante a seção anterior.
4. Na Etapa 4, digite um nome descritivo para o trabalho de importação. Observe que o nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar com letra e não pode conter espaços. Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.
5. Em seguida, selecione a região do data center na lista. A região do data center indica o data center e o endereço para o qual você deve enviar seu pacote.

    ![CONTROLADOR DE DOMÍNIO](./media/backup-azure-backup-import-export/dc.png)

6. Na Etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft vai usar esta conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de importação.

7. Envie o disco e insira o número de acompanhamento para consultar o status da remessa. Depois que o disco chega ao datacenter, ele é copiado para a conta de armazenamento e o status é atualizado.

    ![Status de conclusão](./media/backup-azure-backup-import-export/complete.png)

### Concluindo o fluxo de trabalho
Quando os dados de backup iniciais estiverem disponíveis em sua conta de armazenamento, o agente de Backup do Azure copiará o conteúdo dos dados dessa conta para a conta de armazenamento de backup multilocatários. No próximo horário de backup agendado, o agente de Backup do Azure executará o backup incremental sobre a cópia de backup inicial.

## Próximas etapas
- Se tiver dúvidas sobre o fluxo de trabalho de importação/exportação do Azure, consulte este [artigo](../storage-import-export-service.md).
- Consulte a seção “Backup Offline” das [perguntas frequentes](backup-azure-backup-faq.md) do Backup do Azure se tiver dúvidas sobre o fluxo de trabalho

<!---HONumber=July15_HO4-->
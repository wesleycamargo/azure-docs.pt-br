<properties
	pageTitle="Backup do Azure - Backup Offline ou propagação inicial usando o serviço de importação/exportação do Azure"
	description="Saiba como o Backup do Azure permite que você envie dados fora da rede usando o serviço de importação/exportação do Azure. Este artigo explica a propagação offline dos dados de backup iniciais usando o serviço de importação/exportação do Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/07/2015"
	ms.author="prvijay"/>

# Fluxo de trabalho de Backup offline no Backup do Azure

O Backup do Azure está profundamente integrado com o serviço de importação/exportação do Azure que permite que você transfira os dados de backup iniciais rapidamente. Se você tiver TBs iniciais dos dados de backup que precisa ser transferida uma alta latência e a rede de baixa largura de banda, você pode usar o serviço de importação/exportação do Azure para enviar a cópia de backup inicial em um ou mais discos rígidos, para um data center do Azure. Este artigo fornece uma visão geral das etapas necessárias para concluir este fluxo de trabalho.

## Visão geral

Com o Backup do Azure e importação/exportação do Azure, é simples e direto para carregar os dados no Azure offline por meio de discos. Em vez de transferir a cópia completa inicial através da rede, os dados de backup são gravados um *local de preparo*. O local de preparo pode poderia ser um armazenamento com conexão direta ou um compartilhamento de rede. Depois que a cópia inicial for concluída, usando o *ferramenta de importação/exportação do Azure*, esses dados são gravados em uma unidade SATA que eventualmente é enviada para o data center do Azure. Dependendo do tamanho do backup inicial, uma ou mais unidades SATA podem ser necessário para concluir esta operação. A ferramenta de importação/exportação do Azure é responsável por esses cenários. Depois que os backups são gravados no disco, pode ser enviados para o local mais próximo do data center para carregar no Azure. Backup do Azure, em seguida, copia os dados de backup para o Cofre de Backup e os backups incrementais são programados.

## Pré-requisitos

1. É importante familiarizar-se com o fluxo de trabalho de exportação de importação do Azure que está listado [aqui](../storage-import-export-service.md).

2. Antes de iniciar o fluxo de trabalho, certifique-se de que foi criado um cofre de Backup do Azure, as credenciais do cofre tiverem sido descarregadas, Azure Backup agent foi instalado no servidor de seu Windows Server/Windows Client ou o System Center Data Protection Manager (SCDPM) e se a máquina está registrada com o Cofre de Backup do Azure.

3. Baixar as configurações do arquivo de publicação no Azure da [aqui](https://manage.windowsazure.com/publishsettings) no computador do qual você planeja fazer o backup de nossos dados.

4. Preparar um *local de preparo* que pode ser um compartilhamento de rede ou disco adicional na máquina. Verifique se o local de preparo tem espaço em disco suficiente para manter sua cópia inicial. Para por exemplo, se você está tentando fazer o backup de um servidor de arquivos de 500GB, certifique-se de que a área de preparação é pelo menos 500GB (embora uma quantidade menor será usada). A área de preparação é 'armazenamento temporário' e é usada temporariamente durante esse fluxo de trabalho.

5. Gravador de unidade SATA externo e uma unidade externa de SATA de 3,5 polegadas. Somente discos rígidos SATA II de 3,5 polegadas dão suporte ao uso com o serviço Importar/Exportar. Não há suporte para discos rígidos com mais de 4 TB. Você pode anexar um disco SATA II/III externamente a maioria dos computadores usando um adaptador USB para SATA II/III. Verifique a documentação de importação/exportação do Azure para o último conjunto de unidades que são suportados pelo serviço.

5. Habilite o BitLocker no computador ao qual o gravador de unidade SATA está conectado.

6. Baixe a ferramenta de importação/exportação do Azure em [aqui](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) no computador ao qual o gravador de unidade SATA está conectado.


## Fluxo de trabalho
As informações fornecidas nesta seção são para concluir o **Backup Offline** fluxo de trabalho para que os dados podem ser entregues a um data center do Azure e carregados no armazenamento do Azure. Se você tiver dúvidas sobre o serviço de importação ou em qualquer aspecto do processo, consulte a visão geral do serviço importação referenciado [acima](../storage-import-export-service.md).

### Iniciar Backup Offline

1. Como parte do agendamento de um backup, você encontrará a tela a seguir (no Windows Server, Windows client ou SCDPM). <br/> ![ImportScreen][1]

  A tela coresponding em SCDPM será semelhante ao seguinte. <br/> ![Tela de importação do DPM][8]

onde:

+ **Local de preparação** -refere-se ao local de armazenamento temporário para o qual a cópia de backup inicial é gravada. Isso pode ser um compartilhamento de rede ou no computador local.

+ **Nome do trabalho de importação do azure** -como parte da conclusão esse fluxo de trabalho, você precisará criar um *trabalho de importação* no portal do Azure (abordado na parte superior do documento). Fornece uma entrada que você planeja usar posteriormente no portal do Azure também.

+ **Configurações de publicação do azure** -esse é um arquivo XML que contém informações sobre seu perfil de assinatura. Ele também contém credenciais seguras associadas à sua assinatura. O arquivo pode ser baixado de [aqui](https://manage.windowsazure.com/publishsettings). Forneça o caminho local para o arquivo de configurações de publicação.

+ **ID da assinatura do azure** -forneça o id de assinatura do Azure no qual você planeja iniciar o trabalho de importação do Azure. Se você tiver várias assinaturas do Azure, use a ID associada ao trabalho de importação.

+ **Conta de armazenamento do azure** -insira o nome da conta de armazenamento do Azure que será associado este trabalho de importação.

+ **o contêiner de armazenamento do azure** -insira o nome do blob de armazenamento de destino onde os dados dessa tarefa serão importados.


Concluir o fluxo de trabalho e selecione **Backup agora** no mmc do Backup do Azure, para iniciar a cópia de backup offline. O backup inicial é gravado para a área temporária como parte dessa etapa.<br/>

  ![Fazer backup agora][2]

O fluxo de trabalho correspondente SCDPM está habilitado, clicando no **grupo de proteção** e escolhendo o **criar ponto de recuperação** opção. Em seguida, escolhendo o **proteção on-line** opção.<br/> ![Agora o Backup do DPM][9]

Quando a operação for concluída, um *. AIBBlob* e *. BaseBlob* arquivo é criado no local temporário. <br/> ![Saída][3]

### Preparar a unidade SATA

1. Baixe o [ferramenta de importação/exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) para o *computador cópia*. Verifique se o local de preparo (na etapa anterior) é acessado no computador no qual você pretende executar o próximo conjunto de comandos. Se necessário, o computador de cópia pode ser o mesmo que a máquina de origem.

2. Descompacte o *WAImportExport.zip* arquivo. Execute o *WAImportExport* ferramenta que formatar a unidade SATA, gravar os dados de backup na unidade SATA e criptografá-la. Antes de executar o comando a seguir Certifique-se de que o BitLocker é habilitado no computador. <br/>

*.\WAImportExport.exe PrepImport /j: < * JournalFile*> .jrn /id: < * SessionId * > /sk: < * StorageAccountKey * > /BlobType: * * PageBlob * * /t: < * TargetDriveLetter * >/Formatar / criptografar /srcdir: < * preparo local * > /dstdir: < * DestinationBlobVirtualDirectory * > / *


| Parâmetro | Descrição|
|-------------|-------------|
| /j: < * JournalFile * >| O caminho para o arquivo de diário. Cada unidade deve ter exatamente um arquivo de diário. Observe que o arquivo de diário não deve residir na unidade de destino. A extensão de arquivo de diário é .jrn e é criada como parte da execução do comando.|
|/ID: < * SessionId * > | A sessão de identificação identifica um *Copiar sessão*. Ele é usado para garantir a recuperação precisa de uma sessão de cópia interrompida. Arquivos que são copiados em uma sessão de cópia são armazenados em um diretório chamado após a identificação da sessão na unidade de destino.|
| /SK: < * StorageAccountKey * > | A chave de conta para a conta de armazenamento para o qual os dados serão importados. |
| / BlobType | Especifique **PageBlob**, esse fluxo de trabalho só terá êxito se a opção PageBlob for especificada. Isso não é a opção padrão e deve ser mencionado neste comando. |
|/t: < * TargetDriveLetter * > | A letra da unidade de disco rígido de destino para a sessão de cópia atual, sem os dois pontos à direita.|
|/Format | Especificar esse parâmetro quando a unidade deve ser formatada; Caso contrário, omiti-lo. Antes da ferramenta formata a unidade, ele solicitará uma confirmação do console. Para suprimir a confirmação, especifique o parâmetro /silentmode.|
|/ criptografar | Especifique esse parâmetro quando a unidade ainda não tiver sido criptografada com o BitLocker e precisa ser criptografada pela ferramenta. Se a unidade já foi criptografada com BitLocker, omita esse parâmetro e especifique o parâmetro /bk, fornecendo a chave de BitLocker existente. Se você especificar o parâmetro /format, você também deve especificar o / criptografar parâmetro. |
|/srcdir: < * SourceDirectory * > | O diretório de origem que contém os arquivos a serem copiados para a unidade de destino. O caminho do diretório deve ser um caminho absoluto (não um caminho relativo).|
|/dstdir: < * DestinationBlobVirtualDirectory * > | O caminho para o diretório virtual de destino em sua conta de armazenamento do Microsoft Azure. Certifique-se de usar nomes de contêineres válidos ao especificar diretórios virtuais de destino ou blobs. Tenha em mente que os nomes de contêiner devem estar em minúsculos.|

  >[AZURE.NOTE]Um arquivo de diário é criado na pasta WAImportExport que captura as informações de toda o fluxo de trabalho. Ao criar um trabalho de importação no portal do Azure, você precisará desse arquivo.

  ![Saída do PowerShell][4]

### Criar trabalho de importação no portal do Azure
1. Navegue até sua conta de armazenamento do [Portal de gerenciamento](https://manage.windowsazure.com/), e clique em **importação/exportação** e, em seguida, em **Criar trabalho de importação** no painel de tarefas. <br/> ![Portal][5]

2. Na Etapa 1 do assistente, indique que você preparou a unidade e que o arquivo de diário de unidade está disponível. Na etapa 2 do assistente, forneça as informações de contato da pessoa responsável por esse trabalho de importação.

3. Na etapa 3, carregue os arquivos de diário de unidade obtidos durante a seção anterior.

4. Na Etapa 4, digite um nome descritivo para o trabalho de importação. Observe que o nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar com letra e não pode conter espaços. Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.

5. Em seguida, selecione a região do data center na lista. A região do data center indicará o data center e o endereço para o qual você deve enviar seu pacote. <br/> ![CONTROLADOR DE DOMÍNIO][6]

6. Na Etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft vai usar esta conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de importação.

7. Entregar o disco e insira o número de controle para controlar o status da remessa. Depois que o disco chega no data center, ele é copiado para a conta de armazenamento e o status é atualizado. <br/>

![Status de conclusão][7]

### Concluir o fluxo de trabalho
Quando os dados de backup iniciais estiverem disponíveis em sua conta de armazenamento, o Azure Backup agent copia o conteúdo dos dados desta conta para a conta de armazenamento de backup com inquilinos múltiplas. No próximo agendamento backup horário, o Azure Backup agent executa o backup incremental sobre a cópia de backup inicial.

## Próximas etapas
+ Para todas as questões sobre o fluxo de trabalho de importação/exportação do Azure, consulte este [artigo](../storage-import-export-service.md).

+ Consulte a seção Backup Offline do Backup do Azure [FAQ](backup-azure-backup-faq.md) dúvidas sobre o fluxo de trabalho

<!--Image references-->
[1]: ./media/backup-azure-backup-import-export/importscreen.png
[2]: ./media/backup-azure-backup-import-export/backupnow.png
[3]: ./media/backup-azure-backup-import-export/opbackupnow.png
[4]: ./media/backup-azure-backup-import-export/psoutput.png
[5]: ./media/backup-azure-backup-import-export/azureportal.png
[6]: ./media/backup-azure-backup-import-export/dc.png
[7]: ./media/backup-azure-backup-import-export/complete.png
[8]: ./media/backup-azure-backup-import-export/dpmoffline.png
[9]: ./media/backup-azure-backup-import-export/dpmbackupnow.png

<!---HONumber=GIT-SubDir--> 
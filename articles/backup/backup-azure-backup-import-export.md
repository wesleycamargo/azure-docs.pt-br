<properties
   pageTitle="Backup do Azure - Backup offline ou propagação inicial usando o serviço de Importação/Exportação do Azure | Microsoft Azure"
   description="Saiba como o Backup do Azure permite que você envie dados fora da rede usando o serviço de Importação/Exportação do Azure. Este artigo explica a propagação offline de dados de backup iniciais usando o serviço de Importação/Exportação do Azure"
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/16/2016"
   ms.author="jimpark;saurabhsensharma;nkolli;trinadhk"/>

# Fluxo de trabalho de backup offline no Backup do Azure
O Backup do Azure tem vários mecanismos internos eficientes para reduzir os custos de armazenamento e de rede durante os primeiros backups 'completos' de dados no Azure. Os primeiros backups "completos" transferem grandes quantidades de dados e, portanto, exigem mais largura de banda em comparação com os backups subsequentes, que transferem apenas os deltas/incrementais. Além de comprimir os primeiros backups, o backup do Azure, pelo processo de propagação offline, fornece um mecanismo para carregar os dados do backup inicial comprimido offline no Azure por meio de discos.

O processo de propagação offline do Backup do Azure está totalmente integrado com o [serviço Importação/Exportação do Azure](../storage/storage-import-export-service.md), que permite transferir dados para o Azure rapidamente usando discos. Se você tiver TBs de dados de backup iniciais que precisam ser transferidos através de uma rede de alta latência e baixa largura de banda, pode usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um data center do Azure. Este artigo fornece uma visão geral das etapas necessárias para realizar esse fluxo de trabalho.

## Visão geral

Com a capacidade de propagação offline do Backup do Azure e a Importação/Exportação do Azure, é simples e fácil carregar os dados offline no Azure por meio de discos. Em vez de transferir a cópia completa inicial através da rede, os dados de backup são gravados em um *local de preparo*. Quando a cópia inicial para o *local de preparo* é concluída, usando a *ferramenta Importação/Exportação do Azure*, esses dados são gravados para uma ou mais unidades SATA, dependendo do tamanho do backup inicial. Essas unidades são enviadas para o data center do Azure mais próximo.

A [atualização do Backup do Azure de agosto de 2016 (e superior)](http://go.microsoft.com/fwlink/?LinkID=229525&clcid=0x409) é fornecida com uma *ferramenta de preparação do Azure Disk* chamada **AzureOfflineBackupDiskPrep**, que:

   - Ajuda você a preparar suas unidades para a importação do Azure com facilidade, usando a ferramenta Importação/Exportação do Azure
   - Cria automaticamente um trabalho de importação do Azure para o serviço Importação/Exportação do Azure no [Portal Clássico do Azure](https://manage.windowsazure.com) em vez de criá-lo manualmente com versões mais antigas do Backup do Azure

Quando o carregamento dos dados de backup no Azure é concluído, o Backup do Azure copia os dados de backup para o cofre de backup e os backups incrementais são agendados.

  > [AZURE.NOTE] Para usar a ferramenta de preparação do Azure Disk, verifique se você instalou a atualização do Backup do Azure de agosto de 2016 (ou superior) e execute todas as etapas do fluxo de trabalho com ele. Se você está usando uma versão mais antiga do Backup do Azure, pode preparar a unidade SATA usando a *ferramenta Importação/Exportação do Azure*, conforme detalhado nas seções posteriores deste artigo.

## Pré-requisitos

1. É importante familiarizar-se com o fluxo de trabalho de exportação da Importação do Azure listado [aqui](../storage/storage-import-export-service.md).
2. Antes de iniciar o fluxo de trabalho, certifique-se de que foi criado um cofre de backup do Azure, as credenciais do cofre foram baixadas, o agente do Backup do Azure foi instalado no seu Windows Server/cliente Windows/System Center Data Protection Manager (SCDPM) e de que o computador está registrado no cofre do Backup do Azure.
3. Baixe as configurações do arquivo de publicação do Azure [aqui](https://manage.windowsazure.com/publishsettings) no computador por meio do qual você planeja fazer backup dos nossos dados.
4. Prepare um *local de preparo*, que pode ser um compartilhamento de rede ou um disco adicional no computador. O local de preparo é um “armazenamento provisório” e é usado temporariamente durante esse fluxo de trabalho. Certifique-se de que o local de preparo tenha espaço em disco suficiente para armazenar sua cópia inicial. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, certifique-se de que a área de preparo tenha pelo menos 500 GB (embora uma quantidade menor seja usada devido à compressão).
5. Um ou mais disco rígidos internos SATA II/III de 3,5 polegadas. Somente discos rígidos SATA II/III internos de 3,5 polegadas têm suporte para uso com o serviço de Importação/Exportação do Azure. Não há suporte para discos rígidos com mais de 10 TB. Esses discos rígidos podem ser conectados internamente, usando um conector SATA compatível, ou externamente, usando um adaptador USB SATA II/III. Consulte a [documentação da Importação/Exportação do Azure](../storage/storage-import-export-service.md) para saber o conjunto mais recente de discos rígidos e adaptadores USB SATA com suporte pelo serviço.
6. Habilite o BitLocker no computador ao qual o gravador de unidade SATA está conectado.
7. Baixe a ferramenta de Importação/Exportação do Azure [aqui](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) no computador ao qual o gravador de unidade SATA está conectado. Essa etapa não será necessária se você tiver baixado e instalado a atualização de agosto de 2016 do Backup do Azure (ou superior).

## Fluxo de trabalho
As informações fornecidas nesta seção são para concluir o fluxo de trabalho de **Backup Offline** para que os dados possam ser entregues em um datacenter do Azure e carregados no armazenamento do Azure. Se você tem dúvidas sobre o serviço de Importação ou qualquer aspecto do processo, confira a documentação sobre a [Visão geral do serviço de importação](../storage/storage-import-export-service.md) indicada acima.

### Iniciar backup offline

1. Como parte do agendamento de um backup, você vê a tela a seguir (no Windows Server, cliente Windows ou SCDPM).

    ![Tela de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)
    
    A tela correspondente no SCDPM será semelhante ao seguinte. <br/> ![Tela de importação do DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das entradas é a seguinte:

    - **Local de preparo**: refere-se ao local de armazenamento temporário no qual a cópia de backup inicial é gravada. Pode ser em um compartilhamento de rede ou no computador local. Se o *computador de cópia* e o *computador de origem* são diferentes, é recomendável especificar o caminho completo de rede do local de preparo.
    - **Nome do trabalho de importação do Azure** - refere-se ao nome exclusivo pelo qual o serviço Importação do Azure e o Backup do Azure controlam a transferência de dados enviados usando discos no Azure.
    - **Configurações de publicação do Azure**: arquivo XML que contém informações sobre seu perfil de assinatura. Ele também contém credenciais seguras associadas à sua assinatura. O arquivo pode ser baixado [aqui](https://manage.windowsazure.com/publishsettings). Forneça o caminho local para o arquivo de configurações de publicação.
    - **ID da assinatura do Azure**: a ID de assinatura do Azure no qual você planeja iniciar o trabalho de importação do Azure. Se você tem várias assinaturas do Azure, use a ID à qual deseja associar o trabalho de importação.
    - **Conta de Armazenamento do Azure** - a conta de armazenamento do tipo "Clássica" na assinatura do Azure fornecida que será associada ao trabalho de importação do Azure.
    - **Contêiner de Armazenamento do Azure** -o nome do blob de armazenamento de destino na Conta de Armazenamento do Azure onde os dados desse trabalho são importados.
    
    > [AZURE.NOTE] Se você registrou o servidor em um Cofre dos Serviços de Recuperação no [novo portal do Azure](https://ms.portal.azure.com) para seus backups e não está em uma assinatura de CSP (Provedor de Serviços de Nuvem), pode ainda criar uma conta de armazenamento do tipo "Clássica" no novo portal do Azure e usá-la para o fluxo de trabalho de backup offline.
    
    Salve todas essas informações separadamente, pois elas precisarão ser reinseridas nas etapas a seguir. Somente o *local de preparo* será necessário se a *ferramenta de preparação do Azure Disk* for usada para preparar os discos
    

2. Conclua o fluxo de trabalho e selecione **Fazer Backup Agora** no console de gerenciamento do Backup do Azure para iniciar a cópia de backup offline. O backup inicial é gravado na área de preparo como parte dessa etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)
    
    Para habilitar o fluxo de trabalho correspondente no SCDPM, clique com o botão direito do mouse no **Grupo de Proteção** e escolha a opção **Criar ponto de recuperação**. Essa etapa é seguida com a escolha da opção **Proteção Online**.

    ![Fazer backup agora do DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Quando a operação for concluída, o local de preparo estará pronto para ser usado na preparação de disco

    ![Saída](./media/backup-azure-backup-import-export/opbackupnow.png)

###Prepare a unidade SATA e crie o trabalho de importação do Azure usando a *Ferramenta de preparação de disco do Azure*
A *Ferramenta de preparação do disco do Azure* está disponível no diretório de instalação do agente dos Serviços de Recuperação do Microsoft Azure (Atualização de agosto de 2016 e posterior) no caminho a seguir.

   *\\Serviços* *de Recuperação* *do Microsoft* *Azure* *Agente\\Utilitários*

1. Navegue até o diretório acima e copie o diretório **AzureOfflineBackupDiskPrep** em um *computador de cópia* no qual as unidades a serem preparadas estão montadas. Verifique os itens a seguir em relação ao *computador de cópia*

      - O *Local de preparação* fornecido para o fluxo de trabalho de propagação offline pode ser acessado no *computador de cópia* usando o mesmo caminho de rede fornecido durante o fluxo de trabalho **Iniciar Backup Offline**
      
      - O BitLocker é habilitado no computador.
      
      - O computador tem acesso ao portal do Azure
      
      Se necessário, o *computador de cópia* pode ser o mesmo que o *computador de origem*.

2. Abra um prompt de comando no computador de cópia com o diretório da ferramenta de preparação do Azure Disk como o diretório atual e execute o comando a seguir

      *.\\AzureOfflineBackupDiskPrep.exe* s:<*Caminho do local de preparo*> [p:<*Caminho do PublishSettingsFile*>]

| Parâmetro | Descrição
|-------------|-------------|
|s: <*Caminho do local de preparo*> | Entrada obrigatória, usada para fornecer o Caminho do Local de Preparo inserido durante a Inicialização do Backup Offline |
|p: <*Caminho do PublishSettingsFile*> | Entrada opcional, usada para fornecer o caminho do Arquivo de Configurações de Publicação inserido durante a inicialização do Backup Offline |

   > [AZURE.NOTE] O Arquivo de Configurações de Publicação é uma entrada obrigatória quando o *computador de cópia* e o *computador de origem* são diferentes

Na execução do comando, a ferramenta solicita a seleção do Trabalho de Importação do Azure correspondente para o qual as unidades precisam estar preparadas. Se houver somente um único trabalho de importação associado ao local de preparo fornecido, uma tela é exibida como a mostrada abaixo.

   ![Entrada da ferramenta de preparação do disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Insira a **letra da unidade** do disco montado, sem os dois pontos à direita, que você deseja preparar para a transferência para o Azure. Em seguida, confirme a formatação da unidade quando solicitado.

A ferramenta começa a preparar o disco com os dados de backup. Talvez seja necessário anexar mais discos quando solicitado pela ferramenta, caso o disco fornecido não tenha espaço suficiente para acomodar os dados de backup. <br/>

No final da execução bem-sucedida da ferramenta, um ou mais discos que você forneceu estarão preparados para envio ao Azure e um trabalho de importação com o nome fornecido durante o fluxo de trabalho **Iniciar Backup Offline** será criado no Portal Clássico do Azure. Além disso, a ferramenta exibe o endereço de entrega para o Datacenter do Azure para onde os discos precisam ser enviados e o link para localizar o trabalho de importação no Portal Clássico do Azure.

   ![Preparação de disco do Azure concluída](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
4. Envie os discos para o endereço fornecido pela ferramenta e mantenha o número de controle para referência futura.<br/>
5. Ao navegar até o link exibido pela ferramenta, você será direcionado para a **Conta de Armazenamento do Azure** especificada durante o fluxo de trabalho de backup offline. Aqui você será capaz de ver o trabalho de importação recém-criado na guia **IMPORTAÇÃO/EXPORTAÇÃO** da Conta de Armazenamento

   ![Trabalho de importação criado](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>
6. Clique em **INFORMAÇÕES DE ENVIO** na parte inferior da página para atualizar seus **Detalhes de contato**, conforme mostrado abaixo. A Microsoft usa essas informações para enviar os discos para você quando o trabalho de importação é concluído.

   ![Informações de contato](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>
7. Insira os **Detalhes de envio** na próxima tela, conforme mostrado abaixo. Forneça os detalhes da transportadora e o número de rastreamento correspondentes aos discos enviados para o Datacenter do Azure
   
   ![Informações de envio](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### Concluindo o fluxo de trabalho
Quando o trabalho de importação for concluído, os dados de backup inicias estarão disponíveis na sua conta de armazenamento. O agente dos Serviços de Recuperação do Microsoft Azure copia o conteúdo dos dados dessa conta para o Cofre de Backup ou para um Cofre dos Serviços de Recuperação, dependendo do caso. No próximo horário de backup agendado, o agente de Backup do Azure executará o backup incremental sobre a cópia de backup inicial.


> [AZURE.NOTE] As seções a seguir se aplicam a usuários de versões anteriores do Backup do Azure que não têm acesso à *Ferramenta de preparação do Azure Disk*

### Preparar a unidade SATA

1. Baixe a [ferramenta de Importação/Exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) no *computador de cópia*. Verifique se o local de preparo (na etapa anterior) está acessível no computador no qual você pretende executar o próximo conjunto de comandos. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

2. Descompacte o arquivo *WAImportExport.zip*. Execute a ferramenta *WAImportExport*, que formata a unidade SATA, grava os dados de backup na unidade SATA e os criptografa. Antes de executar o comando a seguir, verifique se o BitLocker está habilitado no computador. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*local de preparo*> /dstdir: <*DestinationBlobVirtualDirectory*>/*
    
    > [AZURE.NOTE] Se você instalou a atualização do Backup do Azure de agosto de 2016 (ou posterior), verifique se o *local de preparo* inserido é o mesmo que o exibido na tela **Fazer Backup Agora** e se contém os arquivos *AIB* e *Blob de Base*.

| Parâmetro | Descrição
|-------------|-------------|
| /j:<*JournalFile*>| O caminho para o arquivo de diário. Cada unidade deve ter exatamente um arquivo de diário. O arquivo de diário não deve residir na unidade de destino. A extensão de arquivo de diário é .jrn e é criada como parte da execução desse comando.|
|/id:<*SessionId*> | A ID de sessão identifica uma *sessão de cópia*. Ela é usada para garantir a recuperação correta de uma sessão de cópia interrompida. Os arquivos copiados em uma sessão de cópia são armazenados em um diretório nomeado como a ID de sessão na unidade de destino.|
| /sk:<*StorageAccountKey*> | A chave da conta de armazenamento para a qual os dados são importados. Ela precisa ser a mesma que foi inserida durante a criação do grupo de política de backup/proteção.|
| /BlobType | Especifique **PageBlob**; esse fluxo de trabalho somente terá êxito se a opção PageBlob for especificada. Essa não é a opção padrão e deve ser mencionada nesse comando. |
|/t:<*TargetDriveLetter*> | A letra da unidade do disco rígido de destino para a sessão de cópia atual, sem dois-pontos no final.|
|/format | Especifique esse parâmetro quando for necessário formatar a unidade; caso contrário, omita-o. Antes de a ferramenta formatar a unidade, ela solicitará uma confirmação no console. Para suprimir a confirmação, especifique o parâmetro /silentmode.|
|/encrypt | Especifique esse parâmetro quando a unidade ainda não tiver sido criptografada com o BitLocker e precisar ser criptografada pela ferramenta. Se a unidade já tiver sido criptografada com o BitLocker, omita esse parâmetro e especifique o parâmetro /bk, fornecendo a chave do BitLocker existente. Se você especificar o parâmetro /format, também deverá especificar o parâmetro /encrypt. |
|/srcdir:<*SourceDirectory*> | O diretório de origem que contém os arquivos a serem copiados para a unidade de destino. Verifique se o nome de diretório especificado aqui é o caminho completo (não um caminho relativo).|
|/dstdir:<*DestinationBlobVirtualDirectory*> | O caminho para o diretório virtual de destino em sua conta de armazenamento do Microsoft Azure. Certifique-se de usar nomes de contêineres válidos ao especificar diretórios virtuais ou blobs de destino. Tenha em mente que os nomes de contêiner devem estar em minúsculas. Esse nome de contêiner deve ser o mesmo que foi inserido durante a criação do grupo de política de backup/proteção|

  > [AZURE.NOTE] Um arquivo de diário é criado na pasta WAImportExport que captura todas as informações do fluxo de trabalho. Ao criar um trabalho de importação no Portal do Azure, você precisará desse arquivo.

  ![Saída do PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Criar trabalho de importação no Portal do Azure
1. Navegue até a sua conta de armazenamento no [Portal Clássico do Azure](https://manage.windowsazure.com/), clique em **Importação/Exportação** e em **Criar Trabalho de Importação** no painel de tarefas.

    ![Portal](./media/backup-azure-backup-import-export/azureportal.png)

2. Na Etapa 1 do assistente, indique que você preparou a unidade e que o arquivo de diário de unidade está disponível. Na Etapa 2 do assistente, forneça as informações de contato da pessoa responsável por esse trabalho de importação.
3. Na Etapa 3, carregue os arquivos de diário de unidade obtidos durante a seção anterior.
4. Na etapa 4, digite um nome descritivo para o trabalho de importação que foi inserido durante a criação da política de backup/do Grupo de Proteção. O nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar por letra e não pode conter espaços. O nome escolhido será usado para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.
5. Em seguida, selecione a região do data center na lista. A região do data center indica o data center e o endereço para o qual você deve enviar seu pacote.

    ![CONTROLADOR DE DOMÍNIO](./media/backup-azure-backup-import-export/dc.png)

6. Na Etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft usa essa conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de importação.

7. Envie o disco e insira o número de acompanhamento para consultar o status da remessa. Depois que o disco chega ao datacenter, ele é copiado para a conta de armazenamento e o status é atualizado.

    ![Status de conclusão](./media/backup-azure-backup-import-export/complete.png)

### Concluindo o fluxo de trabalho
Depois que os dados de backup iniciais estão disponíveis em sua conta de armazenamento, o agente dos Serviços de Recuperação do Microsoft Azure copia o conteúdo dos dados dessa conta para o Cofre de Backup ou para o Cofre dos Serviços de Recuperação, dependendo do caso. No próximo horário de backup agendado, o agente de Backup do Azure executará o backup incremental sobre a cópia de backup inicial.

## Próximas etapas
- Se tiver dúvidas sobre o fluxo de trabalho de importação/exportação do Azure, consulte este [artigo](../storage/storage-import-export-service.md).
- Consulte a seção “Backup Offline” das [perguntas frequentes](backup-azure-backup-faq.md) do Backup do Azure se tiver dúvidas sobre o fluxo de trabalho

<!---HONumber=AcomDC_0824_2016-->
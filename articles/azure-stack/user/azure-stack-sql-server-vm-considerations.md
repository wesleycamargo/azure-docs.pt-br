---
title: Práticas recomendadas de desempenho para o SQL Server em máquinas de virtuais do Azure Stack
description: Fornece as práticas recomendadas para otimizar o desempenho do SQL Server no Microsoft pilha máquinas virtuais do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d9855f107f9888fbfbcb10a3df849e78c87c0605
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246755"
---
# <a name="optimize-sql-server-performance"></a>Otimizar o desempenho do SQL Server

Este artigo fornece diretrizes para otimizar o desempenho do SQL Server em máquinas de virtuais do Microsoft Azure Stack. Ao executar o SQL Server em máquinas virtuais do Azure Stack, use as mesmo banco de dados ajuste de desempenho opções aplicáveis ao SQL Server em um ambiente de servidor local. O desempenho do banco de dados relacional em uma nuvem do Azure Stack depende de muitos fatores. Fatores incluem o tamanho de família de uma máquina virtual e a configuração dos discos de dados.

Durante a criação de imagens do SQL Server [considere provisionar suas máquinas virtuais no portal do Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Baixe a extensão SQL IaaS do gerenciamento do Marketplace no Portal de administração do Azure Stack e baixar de sua escolha de discos rígidos virtuais de máquina virtual SQL (VHDs). Eles incluem SQL2014SP2, SQL2016SP1 e SQL2017.

> [!NOTE]  
> Embora o artigo descreve como provisionar uma máquina virtual do SQL Server usando o portal do Azure global, as diretrizes também se aplica ao Azure Stack com as seguintes diferenças: SSD não está disponível para o disco do sistema operacional, discos gerenciados não estão disponíveis e há pequenas diferenças na configuração de armazenamento.

Introdução a *melhor* desempenho para o SQL Server em máquinas virtuais do Azure Stack é o foco deste artigo. Se sua carga de trabalho for menos exigente, você talvez não exijam cada otimização recomendada. Considere suas necessidades de desempenho e padrões de carga de trabalho ao avaliar essas recomendações.

> [!NOTE]  
> Para diretrizes de desempenho do SQL Server em máquinas virtuais do Azure, consulte [deste artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Antes de começar
É a lista de verificação a seguir para otimizar o desempenho do SQL Server em máquinas virtuais do Azure Stack:


|Área|Otimizações|
|-----|-----|
|Tamanho da máquina virtual |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ou superior para o SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ou superior para o SQL Server Standard edition e Web edition.|
|Armazenamento |Use uma família de máquina virtual que dá suporte a [armazenamento Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Discos |Use um mínimo de dois discos de dados (um para arquivos de log) e outro para o arquivo de dados e TempDB e escolha o tamanho do disco com base em suas necessidades de capacidade. Defina o padrão locais de arquivo de dados para esses discos durante a instalação do SQL Server.<br><br>Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou registro em log.<br>Distribua vários discos de dados do Azure para obter maior taxa de transferência de e/s usando espaços de armazenamento.<br><br>Formate com os tamanhos de alocação documentados.|
|E/S|Habilite a inicialização instantânea de arquivos para arquivos de dados.<br><br>Limite o crescimento automático nos bancos de dados com incrementos fixos razoavelmente pequenos (64 MB - 256 MB).<br><br>Desabilite a redução automática no banco de dados.<br><br>Configure os locais de arquivo de backup e banco de dados de padrão em discos de dados, não no disco do sistema operacional.<br><br>Habilite as páginas bloqueadas.<br><br>Aplica service packs do SQL Server e as atualizações cumulativas.|
|Recursos específicos|Fazer backup diretamente no armazenamento de BLOBs (se houver suporte pela versão do SQL Server em uso).|
|||

Para obter mais informações sobre *como* e *por que* para tornar essas otimizações, examine os detalhes e as diretrizes fornecidas nas seções a seguir.

## <a name="virtual-machine-size-guidance"></a>Diretrizes de tamanho de máquina virtual

Para aplicativos sensíveis ao desempenho, o seguinte [tamanhos de máquina virtual](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) são recomendadas:

- **SQL Server Enterprise edition:** DS3 ou superior

- **SQL Server Standard edition e Web edition:** DS2 ou superior

Com o Azure Stack, não há nenhuma diferença de desempenho entre as séries DS e DS_v2 família série das máquinas virtuais.

## <a name="storage-guidance"></a>Orientação de armazenamento

Máquinas virtuais de série DS (juntamente com a série DSv2) no Azure Stack fornecer a máxima do sistema operacional disco e dados de taxa de transferência disco (IOPS). Uma máquina virtual da série DS ou DSv2 fornece até 1.000 IOPS para o disco do sistema operacional e até 2.300 IOPS por disco de dados, não importa o tipo ou o tamanho do disco escolhido.

Taxa de transferência de disco de dados é determinada exclusivamente com base na série de família de máquina virtual. Você pode [consulte este artigo](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) para identificar a taxa de transferência do disco de dados por série família da máquina virtual.

> [!NOTE]  
> Para cargas de trabalho de produção, selecione uma máquina virtual da série DS ou série DSv2 para fornecer o máximo possível de IOPS no sistema operacional do disco e discos de dados.

Ao criar uma conta de armazenamento no Azure Stack, a opção de replicação geográfica não tem efeito porque essa funcionalidade não está disponível no Azure Stack.

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos de disco principal em uma máquina de virtual do Azure Stack:

- **Disco do sistema operacional:** Quando você cria uma máquina de virtual do Azure Stack, a plataforma anexa pelo menos um disco (rotulado como a **C** unidade) para a máquina virtual para o disco do sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento.

- **Disco temporário:** As máquinas virtuais de pilha do Azure contêm outro disco denominado disco temporário (rotulado como a **1!d** unidade). É um disco localizado no nó que pode ser usado como espaço de rascunho.

- **Discos de dados:** Você pode anexar outros discos à sua máquina virtual como discos de dados, e esses discos são armazenados no armazenamento como blobs de página.

As seções a seguir descrevem as recomendações para usar esses diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e é rotulado como a unidade **C** .

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a **1!d** unidade, não é persistente. Não armazene todos os dados que não está dispostos a perder, como seus arquivos de banco de dados de usuário ou arquivos de log de transações do usuário, na **1!d** unidade.

É recomendável armazenar o TempDB em um disco de dados, pois cada disco de dados fornece um máximo de até 2.300 IOPS por disco de dados.

### <a name="data-disks"></a>Discos de dados

- **Use discos de dados para arquivos de log e de dados.** Se você não estiver usando a distribuição de disco, use dois discos de dados de uma máquina virtual que dá suporte a armazenamento Premium, em que um disco contém os arquivos de log e o outro contém os dados e arquivos de TempDB. Cada disco de dados fornece um número de IOPS e largura de banda (MB/s), dependendo da família de máquina virtual, conforme descrito em [tamanhos de máquina Virtual com suporte no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Se você estiver usando uma técnica de distribuição de disco, como espaços de armazenamento, coloque todos os arquivos de log e de dados na mesma unidade (incluindo TempDB). Essa configuração fornece o número máximo de IOPS disponível para o SQL Server consumir, não importa qual precisa do arquivo-los em um determinado momento.

> [!NOTE]  
> Quando você provisiona uma máquina de virtual do SQL Server no portal, você tem a opção de editar sua configuração de armazenamento. Dependendo da sua configuração, o Azure Stack configura um ou mais discos. Vários discos são combinados em um único pool de armazenamento. Tanto os dados quanto os arquivos de log residem juntos nessa configuração.

- **Distribuição de disco:** Para mais taxa de transferência, você pode adicionar mais discos de dados e usar a distribuição de disco. Para determinar o número de discos de dados, que você precisa analisar o número de IOPS e largura de banda necessária para os arquivos de log e para seus dados e arquivos de TempDB. Observe que os limites IOPS por disco de dados com base na família da série de máquina virtual e não se baseia no tamanho da máquina virtual. Limites de largura de banda de rede, no entanto, se baseiam o tamanho da máquina virtual. Consulte as tabelas sobre [tamanhos de máquina Virtual no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) para obter mais detalhes. Use as seguintes diretrizes:

    - Para o Windows Server 2012 ou posterior, use [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

        1.  Defina a intercalação (tamanho de faixa) como 64 KB (65.536 bytes) para cargas de trabalho (OLTP) e 256 KB (262.144 bytes) para cargas de trabalho de data warehouse para evitar o impacto no desempenho devido ao desalinhamento da partição de processamento de transações online. Isso deve ser definido com o PowerShell.

        2.  Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de oito discos (não o Gerenciador do servidor UI).

            Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação definido como 64 KB e o número de colunas para 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que tamanhos de máquina virtual diferente permite que diferentes números de discos de dados anexados. Para obter mais informações, consulte [tamanhos de máquina Virtual com suporte no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Para obter o máximo possível de IOPS para discos de dados, a recomendação é adicionar o número máximo de discos de dados com suporte pelo seu [tamanho da máquina virtual](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) e usar a distribuição de disco.
- **Tamanho da unidade de alocação NTFS:** Ao formatar o disco de dados, recomendamos o uso de um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log e também para o TempDB.
- **Práticas recomendadas de gerenciamento de disco:** Ao remover um disco de dados, pare o serviço do SQL Server durante a alteração. Além disso, não altere as configurações de cache nos discos que ele não fornece qualquer melhorias de desempenho.

> [!WARNING]  
> Falha ao interromper o serviço do SQL durante essas operações pode causar corrupção de banco de dados.

## <a name="io-guidance"></a>Diretrizes de E/S

- Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para tirar proveito da inicialização instantânea de arquivo, você concede a conta de serviço do SQL Server (MSSQLSERVER) com **SE_MANAGE_VOLUME_NAME** e adicione-o para o **executar tarefas de manutenção de Volume** segurança política. Se você estiver usando uma imagem de plataforma do SQL Server para o Azure, a conta de serviço padrão (**NT Service\MSSQLSERVER**) não é adicionada para o **executar tarefas de manutenção de Volume** política de segurança. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume** , reinicie o serviço do SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para obter mais informações, consulte [Inicialização de Arquivo de Banco de Dados](https://msdn.microsoft.com/library/ms175935.aspx).
- **Crescimento automático** é uma contingência de crescimento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, aumente previamente o arquivo usando o **tamanho** alternar.
- Verifique se a **redução automática** está desabilitada a fim de evitar uma sobrecarga desnecessária que pode afetar negativamente o desempenho.
- Configure os locais do arquivo de banco de dados e backup padrão. Use as recomendações neste artigo e faça as alterações na janela de propriedades do servidor. Para obter instruções, confira [Exibir ou alterar os locais padrão de arquivos de log e de dados (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Captura de tela a seguir demonstra onde fazer essas alterações:

    > ![Exibir ou alterar os locais padrão](./media/sql-server-vm-considerations/image1.png)

- Estabeleça páginas bloqueadas a fim de reduzir a ES e quaisquer atividades de paginação. Para saber mais, confira [Habilitar a opção Bloquear Páginas na Memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Considere compactar os arquivos de dados durante a transferência de entrada/saída do Azure Stack, incluindo backups.

## <a name="feature-specific-guidance"></a>Diretriz específica do recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudar a melhorar o desempenho:

- **Fazer backup no Azure** **armazenamento.** Ao realizar backups para o SQL Server em execução em máquinas virtuais do Azure Stack, você pode usar o SQL Server Backup to URL. Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados.

    Quando o seu backup ou restauração usando o armazenamento do Azure, siga as recomendações fornecidas [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx) e [restaurando de Backups armazenados no Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Você também pode automatizar esses backups usando o [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Fazer backup no armazenamento do Azure Stack.** Você pode fazer backup no armazenamento do Azure Stack de forma semelhante como ocorre com backup no armazenamento do Azure. Quando você cria um backup dentro do SQL Server Management Studio (SSMS), você precisa inserir as informações de configuração manualmente. Você não pode usar o SSMS para criar o contêiner de armazenamento ou a assinatura de acesso compartilhado. Somente o SSMS se conectará às assinaturas do Azure, não as assinaturas do Azure Stack. Em vez disso, você precisará criar a conta de armazenamento, contêiner e assinatura de acesso compartilhado no portal do Azure Stack ou com o PowerShell.

    Quando você coloca as informações na caixa de diálogo de Backup do SQL Server:

    ![Backup do SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > A assinatura de acesso compartilhado é o token SAS no portal do Azure Stack, sem o entrelinhamento '?' na cadeia de caracteres. Se você usar a função de cópia a partir do portal, você precisa excluir o entrelinhamento '?' para o token trabalhar dentro do SQL Server.

    Depois do destino de Backup configurado e configurado no SQL Server, você pode, em seguida, fazer backup para o armazenamento de BLOBs do Azure Stack.

## <a name="next-steps"></a>Próximas etapas

[Usando os serviços ou criação de aplicativos para o Azure Stack](azure-stack-considerations.md)
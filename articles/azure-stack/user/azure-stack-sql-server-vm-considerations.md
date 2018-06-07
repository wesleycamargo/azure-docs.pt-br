---
title: Práticas recomendadas de desempenho para o SQL Server em máquinas de virtuais de pilha do Azure
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
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701452"
---
# <a name="optimize-sql-server-performance"></a>Otimizar o desempenho do SQL Server

Este artigo fornece diretrizes para otimizar o desempenho do SQL Server em máquinas virtuais da pilha do Microsoft Azure. Ao executar o SQL Server em máquinas virtuais da pilha do Azure, use as mesmo banco de dados ajuste de desempenho opções aplicáveis ao SQL Server em um ambiente de servidor local. O desempenho de um banco de dados relacional em uma nuvem de pilha do Azure depende de muitos fatores. Fatores incluem o tamanho de família de uma máquina virtual e a configuração dos discos de dados.

Ao criar imagens do SQL Server, [considerar o provisionamento de máquinas virtuais no portal do Azure pilha](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Baixe a extensão SQL IaaS do gerenciamento do Marketplace no Portal de administração de pilha do Azure e fazer o download de sua escolha de discos rígidos virtuais de máquina virtual SQL (VHDs). Esses incluem SQL2014SP2, SQL2016SP1 e SQL2017.

> [!NOTE]  
> Enquanto o artigo descreve como provisionar uma máquina virtual do SQL Server usando o portal do Azure global, a orientação também se aplica a pilha do Azure com as seguintes diferenças: SSD não está disponível para o disco do sistema operacional, discos gerenciados não estiverem disponíveis, e Há pequenas diferenças na configuração de armazenamento.

Obtendo o *melhor* desempenho para o SQL Server em máquinas virtuais de pilha do Azure é o foco deste artigo. Se sua carga de trabalho é menos exigente, você não precisará cada otimização recomendada. Considere suas necessidades de desempenho e padrões de carga de trabalho ao avaliar essas recomendações.

> [!NOTE]  
> Para obter orientação sobre desempenho para o SQL Server em máquinas virtuais do Azure, consulte [neste artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Antes de começar
É a seguinte lista de verificação para o desempenho ideal do SQL Server em máquinas virtuais de pilha do Azure:


|Área|Otimizações|
|-----|-----|
|Tamanho da máquina virtual |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ou superior para o SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) ou superior para o SQL Server Standard edition e Web edition.|
|Armazenamento |Use uma família de máquina virtual que oferece suporte a [armazenamento Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Discos |Usar um mínimo de dois discos de dados (um para arquivos de log) e um arquivo de dados e TempDB e escolha o tamanho do disco com base em suas necessidades de capacidade. Defina o padrão locais de arquivo de dados para esses discos durante a instalação do SQL Server.<br><br>Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou registro em log.<br>Distribua vários discos de dados do Azure para obter maior taxa de transferência de e/s usando espaços de armazenamento.<br><br>Formate com os tamanhos de alocação documentados.|
|E/S|Habilite a inicialização instantânea de arquivos para arquivos de dados.<br><br>Limitar o crescimento automático nos bancos de dados com razoavelmente pequenos incrementos fixos (64 MB - 256 MB).<br><br>Desabilite a redução automática no banco de dados.<br><br>Configure os locais padrão de banco de dados e backup de arquivos em discos de dados, não o disco do sistema operacional.<br><br>Habilite as páginas bloqueadas.<br><br>Aplica o SQL Server service packs e atualizações cumulativas.|
|Recursos específicos|Fazer backup diretamente no armazenamento de blob (se houver suporte pela versão do SQL Server em uso).|
|||

Para obter mais informações sobre *como* e *por* para fazer essas otimizações, examine os detalhes e orientações fornecidas nas seções a seguir.

## <a name="virtual-machine-size-guidance"></a>Diretrizes de tamanho de máquina virtual

Para aplicativos que reconhecem de desempenho, o seguinte [tamanhos de máquina virtual](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) são recomendadas:

- **SQL Server Enterprise edition:** DS3 ou superior

- **SQL Server Standard edition e Web edition:** DS2 ou superior

Com a pilha do Azure, não há nenhuma diferença de desempenho entre a série de família DS e DS_v2 máquina virtual.

## <a name="storage-guidance"></a>Orientação de armazenamento

Máquinas virtuais de série DS (junto com a série de série DSv2) na pilha do Azure fornecem a máxima do sistema operacional em disco e os dados em disco taxa de transferência (IOPS). Uma máquina virtual da série DS ou série DSv2 fornece até 1.000 IOPS do disco do sistema operacional e até 2.300 IOPS por disco de dados, não importa o tipo ou o tamanho do disco escolhido.

Taxa de transferência de disco de dados é determinada exclusivamente com base na série de família de máquina virtual. Você pode [consulte este artigo](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) para identificar a taxa de transferência do disco de dados por séries de família de máquina virtual.

> [!NOTE]  
> Para cargas de trabalho de produção, selecione uma máquina virtual da série DS ou série dsv2 para fornecer o máximo de IOPS possíveis no sistema operacional do disco e discos de dados.

Ao criar uma conta de armazenamento na pilha do Azure, a opção de replicação geográfica não tem efeito porque esse recurso não está disponível na pilha do Azure.

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos de disco principal em uma máquina virtual de pilha do Azure:

- **Disco do sistema operacional:** quando você cria uma máquina virtual de pilha do Azure, a plataforma anexa pelo menos um disco (rotulado como a **C** unidade) para a máquina virtual para o disco do sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento.

- **Disco temporário:** máquinas virtuais de pilha do Azure contêm outro disco denominado disco temporário (rotulado como a **D** unidade). É um disco localizado no nó que pode ser usado como espaço de rascunho.

- **Discos de dados:** você pode anexar discos adicionais à sua máquina virtual como discos de dados, e esses discos são armazenados no armazenamento como blobs de página.

As seções a seguir descrevem as recomendações para usar esses diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e é rotulado como a unidade **C** .

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a **D** unidade, não é persistente. Não armazene todos os dados que é dispostos a perder, como seus arquivos de banco de dados de usuário ou arquivos de log de transações do usuário, no **D** unidade.

É recomendável armazenar TempDB em um disco de dados, como cada disco de dados fornece um máximo de até 2.300 IOPS por disco de dados.

### <a name="data-disks"></a>Discos de dados

- **Use os discos de dados para dados e arquivos de log.** Se você não estiver usando a distribuição de disco, use dois discos de dados de uma máquina virtual que dá suporte ao armazenamento Premium, em que um disco contém os arquivos de log e a outra contém os dados e arquivos de TempDB. Cada disco de dados fornece um número de IOPS e largura de banda (MB/s) dependendo da família de máquina virtual, conforme descrito em [tamanhos de máquina Virtual com suporte no Azure pilha](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Se você estiver usando uma técnica de particionamento de disco, como espaços de armazenamento, coloque todos os arquivos de log e de dados na mesma unidade (incluindo TempDB). Essa configuração fornece o número máximo de IOPS disponível para o SQL Server consumir, não importa qual arquivo necessidades-los a qualquer momento determinado.

> [!NOTE]  
> Quando você provisionar uma máquina de virtual do SQL Server no portal, você tem a opção de edição de sua configuração de armazenamento. Dependendo da configuração, a pilha do Azure configura um ou mais discos. Vários discos são combinados em um pool de armazenamento único. Tanto os dados quanto os arquivos de log residem juntos nessa configuração.

- **Distribuição de disco:** para mais taxa de transferência, você pode adicionar mais discos de dados e usar a distribuição de disco. Para determinar o número de discos de dados, que você precisa analisar o número de IOPS e largura de banda necessária para os arquivos de log e para seus dados e arquivos de TempDB. Observe que os limites IOPS por disco de dados com base na família de série de máquina virtual e não com base no tamanho da máquina virtual. Limites de largura de banda de rede, no entanto, se baseiam no tamanho da máquina virtual. Consulte as tabelas em [tamanhos de máquina Virtual no Azure pilha](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) para obter mais detalhes. Use as seguintes diretrizes:

    - Para o Windows Server 2012 ou posterior, use [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

        1.  Defina a intercalação (tamanho de distribuição) a 64 KB (65.536 bytes) para cargas de trabalho (OLTP) e 256 KB (262.144 bytes) para cargas de trabalho de data warehouse para evitar o impacto no desempenho devido ao desalinhamento da partição de processamento de transações online. Isso deve ser definido com o PowerShell.

        2.  Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de oito discos (não o Gerenciador do servidor UI).

            Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação definido a 64 KB e o número de colunas para 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que os tamanhos de máquina virtual diferente permitem que diferentes números de discos de dados anexado. Para obter mais informações, consulte [tamanhos de máquina Virtual com suporte no Azure pilha](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Para obter o máximo de IOPS possíveis para discos de dados, a recomendação é para adicionar o número máximo de discos de dados suportado pelo seu [tamanho da máquina virtual](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) e usar a distribuição de disco.
- **Tamanho da unidade de alocação NTFS:** ao formatar o disco de dados, é recomendável que você use um tamanho de unidade de alocação de 64 KB para dados e arquivos de log, bem como TempDB.
- **Práticas recomendadas de gerenciamento de disco:** ao remover um disco de dados, pare o serviço do SQL Server durante a alteração. Além disso, não altere as configurações de cache nos discos que ele não fornece qualquer melhorias de desempenho.

> [!WARNING]  
> Falha ao parar o serviço SQL durante essas operações pode causar corrupção do banco de dados.

## <a name="io-guidance"></a>Diretrizes de E/S

- Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para aproveitar a inicialização instantânea de arquivo, você conceder a conta de serviço do SQL Server (MSSQLSERVER) com **SE_MANAGE_VOLUME_NAME** e adicioná-lo para o **executar tarefas de manutenção de Volume** segurança diretiva. Se você estiver usando uma imagem de plataforma do SQL Server para o Azure, a conta de serviço padrão (**NT Service\MSSQLSERVER**) não é adicionada para o **executar tarefas de manutenção de Volume** política de segurança. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume** , reinicie o serviço do SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para obter mais informações, consulte [Inicialização de Arquivo de Banco de Dados](https://msdn.microsoft.com/library/ms175935.aspx).
- **Crescimento automático** é uma contingência de crescimento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, previamente o crescimento de arquivo usando o **tamanho** alternar.
- Verifique se a **redução automática** está desabilitada a fim de evitar uma sobrecarga desnecessária que pode afetar negativamente o desempenho.
- Configure os locais do arquivo de banco de dados e backup padrão. Use as recomendações neste artigo e faça as alterações na janela de propriedades do servidor. Para obter instruções, confira [Exibir ou alterar os locais padrão de arquivos de log e de dados (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Captura de tela a seguir demonstra onde fazer essas alterações:

    > ![Exibir ou alterar os locais padrão](./media/sql-server-vm-considerations/image1.png)

- Estabeleça páginas bloqueadas a fim de reduzir a ES e quaisquer atividades de paginação. Para saber mais, confira [Habilitar a opção Bloquear Páginas na Memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Considere compactar qualquer arquivo de dados durante a transferência de entrada/saída da pilha do Azure, incluindo backups.

## <a name="feature-specific-guidance"></a>Diretriz específica do recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudá-lo a melhorar o desempenho:

- **Fazer backup do Azure** **armazenamento.** Ao realizar backups para o SQL Server em execução em máquinas virtuais da pilha do Azure, você pode usar o Backup do SQL Server para URL. Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados.

    Quando o seu backup ou restauração usando o armazenamento do Azure, siga as recomendações fornecidas no [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx) e [restaurando de Backups armazenados no Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Você também pode automatizar esses backups usando o [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Fazer backup no armazenamento de pilha do Azure.** Você pode fazer backup no armazenamento do Azure pilha de maneira semelhante como em backup no armazenamento do Azure. Quando você cria um backup dentro do SQL Server Management Studio (SSMS), você precisa inserir as informações de configuração manualmente. Você não pode usar o SSMS para criar o contêiner de armazenamento ou a assinatura de acesso compartilhado. SSMS conecta-se somente a assinaturas do Azure, não as assinaturas de pilha do Azure. Em vez disso, você precisa criar a conta de armazenamento, o contêiner e a assinatura de acesso compartilhado no portal do Azure pilha ou com o PowerShell.

    Quando você coloca as informações na caixa de diálogo de Backup do SQL Server:

    ![Backup do SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > A assinatura de acesso compartilhado é o token SAS no portal de pilha do Azure, sem o prefixo '?' na cadeia de caracteres. Se você usar a função de cópia a partir do portal, você precisa excluir os principais '?' para o token trabalhar no SQL Server.

    Uma vez o destino do Backup configurado e configurado no SQL Server, você pode, em seguida, fazer backup para o armazenamento de BLOBs do Azure pilha.

## <a name="next-steps"></a>Próximas etapas

[Usando os serviços ou criação de aplicativos para a pilha do Azure](azure-stack-considerations.md)
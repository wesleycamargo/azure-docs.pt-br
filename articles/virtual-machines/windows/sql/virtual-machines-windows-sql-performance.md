---
title: Diretrizes de desempenho para o SQL Server no Azure | Microsoft Docs
description: Fornece diretrizes para otimização do desempenho do SQL Server em VMs do Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8d31f04c355b47720a1c9b0334042ba2f6654768
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477309"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Diretrizes de desempenho para o SQL Server em Máquinas Virtuais do Azure

## <a name="overview"></a>Visão geral

Este artigo fornece diretrizes para otimizar o desempenho do SQL Server na Máquina Virtual do Microsoft Azure. Durante a execução do SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando as mesmas opções de ajuste de desempenho aplicáveis ao SQL Server no ambiente de servidor local. No entanto, o desempenho de um banco de dados relacional em uma nuvem pública depende de muitos fatores como, por exemplo, o tamanho de uma máquina virtual e a configuração dos discos de dados.

As [imagens do SQL Server provisionadas no portal do Azure](quickstart-sql-vm-create-portal.md) seguem as melhores práticas de configuração de armazenamento gerais (para mais informações sobre como o armazenamento é configurado, consulte [Configuração de armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Após o provisionamento, considere aplicar outras otimizações discutidas neste artigo. Baseie suas escolhas na carga de trabalho e verifique por meio de testes.

> [!TIP]
> Normalmente, há uma compensação entre a otimização para custos e a otimização para desempenho. Este artigo concentra-se na obtenção do *melhor* desempenho para o SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todos os aprimoramentos relacionados abaixo. Considere suas necessidades de desempenho, custos e padrões de carga de trabalho ao avaliar essas recomendações.

## <a name="quick-check-list"></a>Lista de verificação rápida

Veja a seguir uma lista de verificação rápida para obter o melhor desempenho do SQL Server em máquinas virtuais do Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) ou versão superior para o SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) ou versão superior para o SQL Standard Edition e Web Edition. |
| [Armazenamento](#storage-guidance) | - Use [SSDs Premium](../disks-types.md). O armazenamento padrão é recomendado somente para desenvolvimento/teste.<br/><br/> - Mantenha a [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e a VM do SQL Server na mesma região.<br/><br/> * Desabilite o [armazenamento com redundância geográfica](../../../storage/common/storage-redundancy.md) (replicação geográfica) do Azure na conta de armazenamento. |
| [Discos](#disks-guidance) | - Use no mínimo dois [discos P30](../disks-types.md#premium-ssd) (um para arquivos de log e um para arquivos de dados, incluindo o TempDB). Para cargas de trabalho que exigem aproximadamente 50.000 IOPS, considere o uso de um SSD Ultra. <br/><br/> - Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou registro em log.<br/><br/> - Habilite o cache nos discos que hospedam os arquivos de dados e os arquivos de dados TempDB.<br/><br/> - Não habilite o cache em discos que hospedam o arquivo de log.  **Importante**: Interrompa o serviço do SQL Server ao alterar as configurações de cache para um disco de VM do Azure.<br/><br/> - Particione vários discos de dados do Azure para obter maior taxa de transferência de E/S.<br/><br/> - Formate com os tamanhos de alocação documentados. <br/><br/> - Coloque o TempDB no SSD local para cargas de trabalho do SQL Server críticas (depois de escolher o tamanho correto de VM). |
| [E/S](#io-guidance) |- Habilite a compactação de página do banco de dados.<br/><br/> - Habilite a inicialização instantânea de arquivos para arquivos de dados.<br/><br/> - Limite o aumento automático do banco de dados.<br/><br/> - Desabilite a redução automática no banco de dados.<br/><br/> - Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema.<br/><br/> - Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados.<br/><br/> - Configure os locais do arquivo de banco de dados e de backup padrão.<br/><br/> - Habilite as páginas bloqueadas.<br/><br/> - Aplique as correções de desempenho do SQL Server. |
| [Recursos específicos](#feature-specific-guidance) | - Faça backup diretamente no armazenamento de blobs. |

Para saber mais sobre *como* e *por que* fazer essas otimizações, examine os detalhes e diretrizes fornecidos nas seções a seguir.

## <a name="vm-size-guidance"></a>Diretrizes de tamanho de VM

Para aplicativos sensíveis ao desempenho, é recomendável o uso dos seguintes [tamanhos de máquinas virtuais](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 ou superior
* **SQL Server Standard e Web Editions**: DS2_v2 ou superior

As VMs da [série DSv2](../sizes-general.md#dsv2-series) dão suporte ao armazenamento premium, que é recomendado para obter o melhor desempenho. Os tamanhos recomendados aqui são linhas de base, mas o tamanho real da máquina a ser selecionado dependerá das suas demandas de carga de trabalho. As VMs da série DSv2 são VMs de uso geral, ideais para uma variedade de cargas de trabalho, enquanto outros tamanhos de máquinas são otimizados para tipos de carga de trabalho específicos. Por exemplo, a [série M](../sizes-memory.md#m-series) oferece o nível mais alto de contagem de vCPU e de memória, para as maiores cargas de trabalho do SQL Server. A [série GS](../sizes-memory.md#gs-series) e a [série DSv2 11 a 15](../sizes-memory.md#dsv2-series-11-15) são otimizadas para requisitos de memória grande. Essas duas série também estão disponíveis em [tamanhos limitados de núcleos](../../windows/constrained-vcpu.md), o que economiza em custos de cargas de trabalho com demandas de computação inferiores. As máquinas da [série Ls](../sizes-storage.md) são otimizadas para níveis altos de taxa de transferência e de E/S de disco. É importante considerar sua carga de trabalho específica do SQL Server e aplicá-la à sua seleção de série e tamanho de VM.

## <a name="storage-guidance"></a>Orientação de armazenamento

As VMs da série DS (com as séries DSv2 e GS) dão suporte a [SSDs Premium](../disks-types.md). Os SSDs Premium são recomendados para todas as cargas de trabalho de produção.

> [!WARNING]
> Os SSDs e HDDs Standard têm largura de banda e latências variáveis e só são recomendado para cargas de trabalho de desenvolvimento e teste. As cargas de trabalho de produção devem usar os SSDs Premium.

Além disso, recomendamos a criação de sua conta de armazenamento do Azure no mesmo data center que suas máquinas virtuais do SQL Server a fim de reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação geográfica, pois não há garantia para uma ordem de gravação consistente em vários discos. Em vez disso, considere a configuração de uma tecnologia de recuperação de desastres do SQL Server entre dois data centers do Azure. Para saber mais, confira [Alta disponibilidade e recuperação de desastre para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos principais de discos em uma VM do Azure:

* **Disco do SO**: Quando você cria uma Máquina Virtual do Azure, a plataforma anexa pelo menos um disco (identificado como unidade **C**) à VM para o disco do seu sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento.
* **Disco temporário**: Máquinas virtuais do Azure contêm outro disco denominado disco temporário (rotulado como a unidade **D**:). É um disco localizado no nó que pode ser usado como espaço de rascunho.
* **Discos de dados**: Você também pode anexar outros discos à sua máquina virtual como discos de dados, e eles serão armazenados no armazenamento como blobs de página.

As seções a seguir descrevem as recomendações para usar esses diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e é rotulado como a unidade **C** .

A política padrão de caching no disco do sistema operacional é de **Leitura/Gravação**. Para aplicativos sensíveis ao desempenho, recomendamos o uso de discos de dados em vez do disco do sistema operacional. Consulte a seção sobre Discos de Dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a unidade **D**:, não é mantida no armazenamento de blob do Azure. Não armazene seus arquivos de banco de dados do usuário ou arquivos de log de transações do usuário na unidade **D**:.

Para VMs da série D, série Dv2 e série G, a unidade temporária dessas VMs é baseada em SSD. Se sua carga de trabalho faz uso intenso de TempDB (como objetos temporários ou junções complexas), armazenar o TempDB na unidade **D** pode resultar em maior produtividade e menor latência de TempDB. Para um cenário de exemplo, confira a discussão sobre TempDB na seguinte postagem no blog: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm) (Diretrizes de configuração de armazenamento para VM do SQL).

Para VMs que têm suporte para SSDs Premium (séries DS, DSv2 e GS), é recomendável armazenar o TempDB em um disco que dê suporte aos SSDs Premium com o cache de leitura habilitado.

Há uma exceção a essa recomendação: _se o uso do TempDB apresenta gravação intensa, obtenha um melhor desempenho armazenando o TempDB na unidade local **D**, que é também baseada em SSD nesses tamanhos de computador._

### <a name="data-disks"></a>Discos de dados

* **Usar discos de dados para arquivos de log e de dados**: Se você não estiver usando divisão de disco, use dois discos P30 SSD Premium em que um disco contém os arquivos de log e o outro contém os dados e os arquivos TempDB. Cada SSD Premium fornece um número de IOPs e largura de banda (MB/s) dependendo do tamanho, conforme descrito no artigo, [Selecionar um tipo de disco](../disks-types.md). Se você estiver usando uma técnica de distribuição de disco, como Espaços de Armazenamento, obterá o desempenho ideal tendo dois pools, um para os arquivos de log e outro para os arquivos de dados. No entanto, se você planeja usar FCI (Instâncias de Cluster de Failover) do SQL Server, deve configurar um pool.

   > [!TIP]
   > - Para resultados de teste em várias configurações de disco e de carga de trabalho, confira a postagem no blog a seguir: [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) (Diretrizes de configuração de armazenamento para VM do SQL).
   > - Para desempenho de missão crítica de SQL Servers que precisem de aproximadamente 50.000 IOPS, considere substituir 10 discos -P30 por um SSD Ultra. Para obter mais informações, consulte a postagem do blog abaixo: [Mission critical performance with Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/) (Desempenho crítico com SSD Ultra).

   > [!NOTE]
   > Quando você provisiona uma VM do SQL Server no portal, tem a opção de editar sua configuração de armazenamento. Dependendo de sua configuração, o Azure configura um ou mais discos. Vários discos são combinados em um único pool de armazenamento com distribuição. Tanto os dados quanto os arquivos de log residem juntos nessa configuração. Para saber mais, confira [Configuração de armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Distribuição de Discos**: Para produtividade mais alta, você pode adicionar outros discos de dados e usar a Distribuição de Discos. Para determinar o número de discos de dados, você precisa analisar o número de IOPS e a largura de banda necessários para os arquivos de log e para os arquivos de dados e do TempDB. Observe que diferentes tamanhos de VM têm diferentes limites no número de IOPS e na largura de banda com suporte; consulte as tabelas sobre IOPS por [tamanho de VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Use as seguintes diretrizes:

  * Para Windows 8/Windows Server 2012 ou posterior, use os [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina a intercalação (tamanho de faixa) como 64 KB (65536 bytes) para cargas de trabalho OLTP e 256 KB (262144 bytes) para cargas de trabalho de data warehouse, a fim de evitar o impacto no desempenho devido ao desalinhamento da partição. Isso deve ser definido com o PowerShell.
      2. Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de 8 discos (não interface do usuário do Gerenciador do Servidor). 

    Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação de 64 KB e o número de colunas de 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, é possível usar discos dinâmicos (volumes de SO distribuídos) e o tamanho da faixa será sempre 64 KB. Observe que essa opção tornou-se obsoleta no Windows 8/Windows Server 2012. Para obter informações, veja a declaração de suporte em [O Serviço de Disco Virtual está em transição para a API de Gerenciamento de Armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se você estiver usando [Espaços de Armazenamento Diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) com [Instâncias de Cluster de Failover do SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), será necessário configurar um único pool. Observe que, embora diferentes volumes possam ser criados nesse único pool, todos eles compartilharão as mesmas características, como a mesma política de cache.

  * Determine o número de discos associados ao seu pool de armazenamento com base nas suas expectativas de carga. Tenha em mente que tamanhos de VM diferentes permitem quantidades diferentes de discos de dados anexados. Para obter mais informações, confira [Tamanhos das Máquinas Virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se você não estiver usando os SSDs Premium (cenários de desenvolvimento/teste), a recomendação será adicionar o número máximo de discos de dados com suporte pelo [Tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usar a Distribuição de Discos.

* **Política de cache**: Observe as recomendações a seguir para a política de cache, dependendo da configuração de armazenamento.

  * Se você estiver usando discos separados para dados e arquivos de log, habilite o cache de leitura nos discos de dados que hospedam os arquivos de dados e arquivos de dados do TempDB. Isso pode resultar em um benefício de desempenho significativo. Não habilite o cache no disco que contém o arquivo de log, pois isso causa uma pequena diminuição no desempenho.

  * Se você estiver usando a distribuição de disco em um único pool de armazenamento, a maioria das cargas de trabalho se beneficiará do cache de leitura. Se você tiver pools de armazenamento separados para os arquivos de log e de dados, habilite o cache de leitura apenas no pool de armazenamento para os arquivos de dados. Em determinadas cargas de trabalho de gravação pesada, um melhor desempenho pode ser obtido sem o cache. Isso somente pode ser determinado por meio de testes.

  * As recomendações anteriores aplicam-se aos SSDs Premium. Se você não estiver usando os SSDs Premium, não habilite o caching em discos de dados.

  * Para obter instruções sobre como configurar o cache de disco, consulte os artigos a seguir. Para o modelo de implantação clássico (ASM), confira: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Para o modelo de implantação do Azure Resource Manager, confira: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) e [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Interrompa o serviço SQL Server ao alterar a configuração do cache dos discos da VM do Azure a fim de evitar a possibilidade de corrupção do banco de dados.

* **Tamanho da unidade de alocação de NTFS**: Ao formatar o disco de dados, recomendamos o uso de um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log e também para o TempDB.

* **Práticas recomendadas de gerenciamento de disco**: Ao remover um disco de dados ou alterar seu tipo de cache, interrompa o serviço SQL Server durante a alteração. Quando as configurações do cache são alteradas no disco do SO, o Azure interrompe a VM, altera o tipo de cache e reinicia a VM. Quando as configurações do cache de um disco de dados são alteradas, a VM não é interrompida, mas o disco de dados é desanexado da VM durante a mudança e depois reanexado.

  > [!WARNING]
  > A não interrupção do serviço SQL Server durante essas operações pode causar corrupção no banco de dados.


## <a name="io-guidance"></a>Diretrizes de E/S

* Os melhores resultados com os SSDs Premium são obtidos ao paralelizar seu aplicativo e as solicitações. Os SSDs Premium foram projetados para cenários nos quais a profundidade da fila de E/S é maior do que um. Portanto, você verá pouco ou nenhum ganho de desempenho para solicitações em série de segmento único (mesmo se usarem muito armazenamento). Por exemplo, isso pode afetar os resultados do teste de segmento único de ferramentas de análise de desempenho, por exemplo, o SQLIO.

* Considere o uso da [compactação de página do banco de dados](https://msdn.microsoft.com/library/cc280449.aspx) , pois isso pode ajudar a melhorar o desempenho de cargas de trabalho com uso intenso de E/S. No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor de banco de dados.

* Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para aproveitar a inicialização instantânea de arquivo, conceda a SE_MANAGE_VOLUME_NAME à conta de serviço do SQL Server (MSSQLSERVER) e adicione-a à política de segurança **Executar Tarefas de Manutenção de Volume**. Se você estiver usando uma imagem da plataforma do SQL Server para o Azure, a conta de serviço padrão (NT Service\MSSQLSERVER) não será adicionada à política de segurança **Executar Tarefas de Manutenção de volume**. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume** , reinicie o serviço do SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para obter mais informações, consulte [Inicialização de Arquivo de Banco de Dados](https://msdn.microsoft.com/library/ms175935.aspx).

* **aumento automático** é considerado meramente uma contingência para o aumento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, aumente previamente o arquivo usando a opção Tamanho.

* Verifique se a **redução automática** está desabilitada a fim de evitar uma sobrecarga desnecessária que pode afetar negativamente o desempenho.

* Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema. Para saber mais, confira [Mover bancos de dados do sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados Isso pode ser feito no SQL Server Configuration Manager clicando duas vezes na Instância do SQL Server e selecionando Propriedades. As configurações do arquivo de rastreamento e do log de erros podem ser alteradas na guia **Parâmetros de Inicialização**. O Diretório de Despejo é especificado na guia **Avançado**. A captura de tela a seguir mostra onde procurar o parâmetro de inicialização do log de erro.

    ![Captura de Tela de Log de Erros do SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configure os locais do arquivo de banco de dados e backup padrão. Use as recomendações neste artigo e faça as alterações na janela de propriedades do Servidor. Para obter instruções, confira [Exibir ou alterar os locais padrão de arquivos de log e de dados (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A captura de tela a seguir demonstra onde fazer essas alterações.

    ![Log de Dados de SQL e Arquivos de Backup](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Estabeleça páginas bloqueadas a fim de reduzir a ES e quaisquer atividades de paginação. Para saber mais, confira [Habilitar a opção Bloquear Páginas na Memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se você estiver executando o SQL Server 2012, instale a Atualização Cumulativa 10 do Service Pack 1. Essa atualização contém a correção para o desempenho ruim de E/S ao executar a instrução seleção na tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](https://support.microsoft.com/kb/2958012).

* Considere a compactação de qualquer arquivo de dados durante a transferência de entrada/saída do Azure.

## <a name="feature-specific-guidance"></a>Diretriz específica do recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudá-lo a obter um desempenho maior:

* **Backup no armazenamento do Azure**: Ao realizar backups do SQL Server em execução em máquinas virtuais do Azure, você poderá usar [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados. Quando fizer backup ou restaurar de/para o armazenamento do Azure, siga as recomendações fornecidas em [Práticas Recomendadas de Backup do SQL Server para URL e Solução de Problemas e Restauração de Backups Armazenados no Armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Você também pode automatizar esses backups usando o [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

    Antes do SQL Server 2012, use a [Ferramenta de backup do SQL Server para o Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar a taxa de transferência do backup utilizando vários alvos de distribuição de backup.

* **Arquivos de dados do SQL Server no Azure**: Este recurso novo, [Arquivos de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível desde o SQL Server 2014. A execução do SQL Server com os arquivos de dados no Azure demonstra características de desempenho comparáveis as dos discos de dados do Azure.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre armazenamento e desempenho, consulte [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/) (Diretrizes de configuração de armazenamento para SQL Server na VM do Azure)

Para conferir as práticas recomendadas de segurança, consulte [Considerações sobre segurança para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-security.md).

Revise outros artigos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

---
title: Matriz de suporte para backup de computadores que executam o agente MARS (Serviços de Recuperação do Microsoft Azure) com o Backup do Azure
description: Este artigo resume o suporte de Backup do Azure quando você faz backup de máquinas que estão executando o agente do Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9799914cdabf1f64fccfd6bfd891f9498b860e39
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922995"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente MARS (Serviços de Recuperação do Microsoft Azure)

Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de máquinas locais e aplicativos e para fazer backup de máquinas virtuais (VMs). Este artigo resume as configurações de suporte e limitações ao usar o agente do Microsoft Azure Recovery Services (MARS) para fazer backup de máquinas.

## <a name="the-mars-agent"></a>O agente MARS

O Backup do Azure usa o agente MARS para fazer backup de dados de máquinas locais e VMs do Azure para um cofre de serviços de recuperação de backup no Azure. O agente do MARS pode:
- Execute nas máquinas do Windows local para que eles podem fazer backup diretamente em um cofre de serviços de recuperação de backup no Azure.
- Execute em VMs do Windows para que eles podem fazer backup diretamente em um cofre.
- Executado no servidor de Backup do Microsoft Azure (MABS) ou um servidor do System Center Data Protection Manager (DPM). Nesse cenário, computadores e cargas de trabalho de backup em MABS ou para o servidor DPM. O agente de MARS, em seguida, faz backup nesse servidor em um cofre no Azure.

As opções de backup dependem de onde o agente está instalado. Para obter mais informações, consulte [arquitetura de Backup do Azure usando o agente do MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup MABS e o DPM, consulte [fazer backup de DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [requisitos de](backup-support-matrix-mabs-dpm.md) para a arquitetura de backup.

**Instalação** | **Detalhes**
--- | ---
Baixe o agente de MARS mais recente | Você pode baixar do cofre a versão mais recente do agente ou [baixá-lo diretamente](https://aka.ms/azurebackup_agent).
Instalar diretamente em um computador | Você pode instalar o agente do MARS diretamente em um servidor do Windows local ou em uma VM do Windows que está executando qualquer um dos [sistemas operacionais com suporte](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalar em um servidor de backup | Quando você configura o DPM ou MABS para fazer backup no Azure, você baixa e instala o agente MARS no servidor. Você pode instalar o agente em [sistemas operacionais com suporte](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por padrão, as VMs do Azure que são habilitados para backup têm uma instalação da extensão de Backup do Azure. Essa extensão faz backup de toda a VM. Você poderá instalar e executar o agente MARS em uma VM do Azure junto com a extensão se você quiser fazer backup de pastas e arquivos específicos, em vez da VM completa.
> Quando você executa o agente do MARS em uma VM do Azure, ele faz o backup de arquivos ou pastas que estão no armazenamento temporário na VM. Backups falham se os arquivos ou pastas são removidas do armazenamento temporário ou se o armazenamento temporário é removido.


## <a name="cache-folder-support"></a>Suporte a pasta de cache

Quando você usa o agente MARS para fazer backup de dados, o agente tira um instantâneo dos dados e o armazena em uma pasta de cache local antes de enviar os dados para o Azure. A pasta de cache (zero) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  Espaço livre na pasta de cache deve ser pelo menos de 5 a 10 por cento do tamanho geral dos seus dados de backup.
Local padrão | A pasta de cache deve ser armazenada localmente no computador que está sendo feito backup, e ele deve estar online. A pasta de cache não deve ser um compartilhamento de rede, mídia removível ou em um volume offline.
Pasta | A pasta de cache deve ser criptografada em um volume com eliminação de duplicação ou em uma pasta que é compactado, que é esparsa ou que tem uma nova análise de ponto.
Alterações de localização | Você pode alterar o local do cache, interrompendo o mecanismo de backup (`net stop bengine`) e copiar a pasta de cache para uma nova unidade. (Certifique-se que a nova unidade tem espaço suficiente.) Em seguida, atualize as duas entradas do registro em **Backup do Azure HKLM\SOFTWARE\Microsoft\Windows** (**Config/ScratchLocation** e **CloudBackupProvider/Config/ScratchLocation**) para o novo local e reinicie o mecanismo.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-access"></a>Acesso à URL

O agente MARS precisa de acesso a estas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Suporte de limitação

**Recurso** | **Detalhes**
--- | ---
Controle de Largura de Banda |  Com suporte. No agente de MARS, use **alterar propriedades** para ajustar a largura de banda.
Limitação de rede | Não disponível para máquinas de backup que executam o Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para backups diretos

Você pode usar o agente MARS para fazer backup diretamente no Azure em alguns sistemas operacionais que são executados em computadores locais e VMs do Azure. Os sistemas operacionais deve ser de 64 bits e deve estar executando o mais recentes Service packs e atualizações. A tabela a seguir resume esses sistemas operacionais:

**Sistema operacional** | **Arquivos/pastas** | **Estado do sistema**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não 
Windows 8.1 (Enterprise, Pro)| Sim |Não 
Windows 8 (Enterprise, Pro) | Sim | Não 
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Básico, Starter) | Sim | Não 
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sim | Sim
Windows Server 2012 (Standard, Datacenter, Foundation) | Sim | Sim
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Sim | Sim
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Sim | Não 
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sim | Não 

Para obter mais informações, consulte [sistemas operacionais com suporte MABS e DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites do Backup

O Backup do Azure limita o tamanho de uma fonte de dados de arquivo ou pasta que possa ser feito backup. Os itens que você pode fazer backup de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema operacional** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |  54.400 GB
Windows Server 2008 R2 SP1 |    1.700 GB
Windows Server 2008 SP2 | 1.700 GB
Windows 8 ou superior  | 54.400 GB
Windows 7   | 1.700 GB


## <a name="supported-file-types-for-backup"></a>Tipos de arquivo compatíveis para backup

**Tipo** | **Suporte**
--- | ---
Criptografado   |  Com suporte.
Compactado |  Com suporte.
Esparsos |  Com suporte.
Compactados e esparsos |  Com suporte.
Links físicos  | Sem suporte. Ignorado.
Ponto de nova análise   | Sem suporte. Ignorado.
Criptografados e esparsos |  Sem suporte. Ignorado.
Fluxo compactado   | Sem suporte. Ignorado.
Fluxo esparso   | Sem suporte. Ignorado.
OneDrive (arquivos sincronizados são fluxos esparsos)  | Sem suporte.

## <a name="supported-drives-or-volumes-for-backup"></a>Com suporte de unidades ou volumes no backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes somente leitura   | Sem suporte | Volume Copy Shadow Service (VSS) só funcionará se o volume é gravável.
Volumes offline | Sem suporte |   O VSS só funcionará se o volume estiver online.
Compartilhamento de rede   | Sem suporte |   O volume deve ser local no servidor.
Volumes protegidos pelo BitLocker | Sem suporte |   O volume deve ser desbloqueado antes do backup é iniciado.
Identificação do sistema de arquivos  | Sem suporte |   Apenas NTFS tem suporte.
Mídia removível | Sem suporte |   Todas as fontes de item de backup devem ter uma *corrigido* status.
Unidades com eliminação de duplicação | Com suporte | O Backup do Azure converte dados com eliminação de duplicação em dados normais. Ele otimiza, criptografa, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para o backup offline inicial

O Backup do Azure dá suporte a *propagação offline* para transferir dados do backup inicial para o Azure usando discos. Esse suporte é útil se o backup inicial é a probabilidade de estar no intervalo de tamanho de terabytes (TBs). O backup offline é compatível com:

- Backup direto de arquivos e pastas em computadores locais que estão executando o agente do MARS.
- Backup de cargas de trabalho e arquivos de um servidor DPM ou MABS.

Backup offline não pode ser usado para arquivos de estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para restauração de dados

Usando o [a restauração instantânea](backup-instant-restore-capability.md) recurso de Backup do Azure, você pode restaurar dados antes de serem copiado no cofre. A máquina estiver fazendo backup deve estar executando o .NET Framework 4.5.2 ou superior.

Backups não podem ser restaurados para um computador de destino que está executando uma versão anterior do sistema operacional. Por exemplo, um backup feito em um computador que está executando o Windows 7 pode ser restaurado no Windows 8 ou posterior. Mas um backup feito em um computador que está executando o Windows 8 não pode ser restaurado em um computador que está executando o Windows 7.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [fazer backup de arquitetura que usa o agente do MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Saiba o que tem suporte quando você [executar o agente de MARS em um servidor DPM ou MABS](backup-support-matrix-mabs-dpm.md).

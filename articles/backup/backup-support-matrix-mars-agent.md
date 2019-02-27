---
title: Matriz de suporte para backup de computadores que executam o agente MARS (Serviços de Recuperação do Microsoft Azure) com o Backup do Azure
description: Este artigo resume o suporte do Backup do Azure quando você faz backup de computadores que executam o agente MARS (Serviços de Recuperação do Microsoft Azure).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430930"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente MARS (Serviços de Recuperação do Microsoft Azure)

Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de aplicativos e computadores locais e VMs do Azure. Este artigo resume as configurações de suporte e as limitações para fazer backup de computadores com o agente MARS (Serviços de Recuperação do Microsoft Azure).

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O agente MARS é usado pelo Backup do Azure para fazer backup de dados de computadores locais e VMs do Azure para um cofre dos Serviços de Recuperação de backup no Azure. O agente MARS pode ser usado da seguinte maneira:
- Execute o agente em computadores Windows locais para que eles possam fazer backup diretamente em um cofre dos Serviços de Recuperação de backup no Azure.
- Execute o agente em VMs do Microsoft Azure para que elas possam fazer backup diretamente em um cofre.
- Execute o agente em um MABS (Servidor de Backup do Microsoft Azure) ou em um servidor do System Center DPM (Data Protection Manager). Nesse cenário, computadores e cargas de trabalho fazem backup em um MABS/DPM e, em seguida, o MABS/DPM faz backup em um cofre no Azure usando o agente MARS. 

O que você poderá fazer backup dependerá do local em que o agente estiver instalado.

- [Saiba mais](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sobre arquitetura de backup usando o agente MARS.
- [Saiba mais]() sobre a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs)e os [requisitos](backup-support-matrix-mabs-dpm.md) do MABS/DPM.


## <a name="supported-installation"></a>Instalação compatível

**Instalar** | **Detalhes**
--- | ---
**Baixar o agente MARS mais recente** | Você pode baixar do cofre a versão mais recente do agente ou [baixá-lo diretamente](https://aka.ms/azurebackup_agent).
**Instalar diretamente em um computador** | Você pode instalar o agente MARS diretamente em um servidor do Windows local ou em uma VM do Microsoft Azure executando qualquer um dos [sistemas operacionais compatíveis]().
**Instalar em um servidor de backup** | Quando você configura o DPM ou MABS para fazer backup no Azure, você baixa e instala o agente MARS no servidor. O agente pode ser instalado de acordo com os [sistemas operacionais compatíveis](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por padrão as VMs do Azure habilitadas para backup têm uma instalação da extensão de Backup do Azure. Essa extensão faz backup de toda a VM. Você poderá instalar e executar o agente MARS em uma VM do Azure junto com a extensão se você quiser fazer backup de pastas e arquivos específicos, em vez da VM completa.
> Quando você executa o agente MARS em uma VM do Azure, ele faz backup de arquivos/pastas localizados em armazenamento temporário na VM. Os backups falharão se os arquivos/pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.


## <a name="cache-folder-support"></a>Suporte a pasta de cache

Quando você faz backup com o agente MARS, o agente obtém um instantâneo dos dados e os armazena em uma pasta de cache local antes de enviá-los para o Azure. A pasta de cache (zero) tem uma série de requisitos.

**Cache** | **Detalhes**
--- | ---
**Tamanho do cache** |  O espaço livre no tamanho da pasta de cache deve ser pelo menos de 5 a 10% do tamanho total dos seus dados de backup. 
**Localização do cache** | A pasta de cache deve ser local em relação ao computador do qual está sendo feito backup e deve estar online.<br/><br/> A pasta de cache não deve ser localizada em um compartilhamento de rede, em mídia removível nem em um volume offline. 
**Pasta de cache** | A pasta de cache deve ser criptografada em um volume com eliminação de duplicação ou em uma pasta que é compactada/esparsa/ponto de nova análise
**Modificar a localização do cache** | Você pode alterar a localização do cache interrompendo o mecanismo de backup (net stop bengine) e copiando a pasta de cache para uma nova unidade (certifique-se de que ela tenha espaço suficiente). Depois, atualize duas entradas do Registro em HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation e Config/CloudBackupProvider/ScratchLocation) para a nova localização e reinicie o mecanismo.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-access"></a>Acesso à URL

O agente MARS precisa de acesso a estas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>Suporte de limitação

**Recurso** | **Detalhes**
--- | ---
Controle de Largura de Banda | Com suporte<br/><br/> Use **Alterar Propriedades** no agente MARS para ajustar a largura de banda.
Limitação de rede | Não está disponível para computadores em que foi feito backup que executam o Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para backups diretos

A tabela a seguir resume quais sistemas operacionais em execução em computadores locais e VMs do Azure pode ser feitos backup diretamente para o Azure com o agente MARS.

- Todos os sistemas operacionais são de 64 bits.
- Todos os sistemas operacionais devem estar executando os pacotes e as atualizações de serviços mais recentes.
- Para obter detalhes de quais servidores DPM e MABS pode ser feito backup com o agente MARS, examine [esta tabela](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

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
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup | Sim | Não 


## <a name="backup-limits"></a>Limites do Backup

O Backup do Azure impõe um limite no tamanho de uma fonte de dados de arquivo/pasta do qual possa ser feito backup. O tamanho dos itens selecionados para backup de um único volume não pode exceder os tamanhos resumidos na tabela.

**Sistema operacional** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |  54.400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 ou superior  | 54.400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Tipos de arquivo compatíveis para backup

**Tipo** | **Com suporte** 
--- | --- 
Criptografado   | Sim 
Compactado | Sim
Esparsos | Sim 
Compactados e esparsos | Sim
Links físicos  | Sem suporte<br/><br/> Ignorado
Ponto de nova análise   | Sem suporte<br/><br/> Ignorado
Criptografados e esparsos |  Sem suporte<br/><br/> Ignorado
Fluxo compactado   | Sem suporte<br/><br/> Ignorado
Fluxo esparso   | Sem suporte<br/><br/> Ignorado
One Drive (arquivos sincronizados são fluxos esparsos)    | Sem suporte 

## <a name="supported-drivesvolumes-for-backup"></a>Unidades/volumes compatíveis para backup

**Unidade/volume** | **Com suporte** | **Detalhes**
--- | --- | ---
Volumes somente leitura   | Sem suporte | O volume deve ser gravável para que o VSS funcione.
Volumes offline | Sem suporte |   O volume deve estar online para que o VSS funcione.
Compartilhamento de rede   | Sem suporte |   O volume deve ser local no servidor para backup.
Volumes protegidos pelo BitLocker | Sem suporte |   O volume deve ser previamente desbloqueado para que o backup funcione.
Identificação de Sistema de Arquivos  | Sem suporte |   Apenas NTFS.
Mídia removível | Sem suporte |   Todas as fontes de itens de backup devem ter o status de fixas.
Unidades com eliminação de duplicação |  Com suporte.<br/><br/> O Backup do Azure converte dados com eliminação de duplicação em dados normais. Ele otimiza os dados, criptografa-os, armazena e envia-os para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para o backup offline inicial

O Backup do Azure é compatível com a "propagação offline" para transferir dados de backup inicial para o Azure usando discos. Isso é útil se o backup inicial está provavelmente na casa dos TBs (terabytes). O backup offline é compatível com:

- Backup direto de arquivos e pastas em computadores locais executando o agente MARS.
- Backup de cargas de trabalho e arquivos de um servidor DPM ou MABS.
- O backup offline não pode ser usado para arquivos de estado do sistema.


## <a name="support-for-restore"></a>Suporte para restauração

- A nova versão de [restauração instantânea](/backup-instant-restore-capability.md) do Backup do Azure permite que você restaure dados antes de serem copiado no cofre.<br/><br/> Para usar esse recurso, o computador do qual está sendo feito backup deve estar executando o .NET Framework 4.5.2 ou superior.
- Os backups não podem ser restaurados em um computador de destino executando uma versão anterior do sistema operacional. Por exemplo, um backup feito em um computador com Windows 7 pode ser restaurado no Windows 8 ou mais recente. Entretanto, um backup feito em um computador com Windows 8 não pode ser restaurado em um computador com Windows 7.


## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sobre arquitetura de backup com o agente MARS.
- [Saiba](backup-support-matrix-mabs-dpm.md) o que é compatível quando você executa o agente MARS no System Center DPM ou no Servidor de Backup do Microsoft Azure (MABS).



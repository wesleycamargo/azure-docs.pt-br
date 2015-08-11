<properties
	pageTitle="Introdução ao backup do DPM do Azure | Microsoft Azure"
	description="Uma introdução ao backup de servidores de DPM usando o serviço de Backup do Azure"
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="sammehta"; "jimpark"/>

# Introdução ao Backup do DPM do Azure

Este artigo fornece uma introdução ao uso do Backup do Microsoft Azure para proteger seus servidores e cargas de trabalho do Center Data Protection Manager. Lendo-o, você entenderá:

- Como funciona o backup do servidor DPM do Azure
- Os pré-requisitos para obter uma experiência positiva de backup
- Os erros típicos encontrados e como lidar com eles
- Cenários com suporte

O System Center DPM faz backup dos dados de arquivos e aplicativos. O backup dos dados no DPM pode ser feito em fita, em disco, ou no Azure com o Backup do Microsoft Azure. O DPM interage com o Backup do Azure da seguinte maneira:

- **DPM implantado como servidor físico ou máquina virtual local** — Se o DPM for implantado como servidor físico ou máquina virtual local Hyper-V, é possível fazer backup dos dados em um cofre de Backup do Azure além do backup em disco e em fita.
- **DPM implantado como máquina virtual do Azure** — No System Center 2012 R2 com atualização 3, o DPM pode ser implantado como máquina virtual do Azure. Se o DPM for implantado como máquina virtual do Azure, é possível fazer backup de dados em discos do Azure anexados à máquina virtual do Azure do DPM, ou descarregar o armazenamento de dados por meio de backup em um cofre de Backup do Azure.

## Por que fazer backup de seus servidores DPM?

Os benefícios comerciais do uso do Backup do Azure para backup de servidores DPM são:

- Para a implantação do DPM local, é possível usar o backup do Azure como alternativa para a implantação de longo prazo em fita.
- Para implantações do DPM no Azure, o Backup do Azure possibilita descarregar o armazenamento do disco do Azure, permitindo escalar verticalmente ao armazenar os dados mais antigos no Backup do Azure e os dados novos em disco.

## Como funciona o backup do servidor DPM?
Para fazer backup de uma máquina virtual, primeiro é necessário um instantâneo pontual dos dados. O serviço de Backup do Azure inicia o trabalho de backup no horário agendado e dispara a extensão de backup para obter um instantâneo. A extensão de backup é coordenada com o serviço VSS no convidado para obter consistência e chama a API de instantâneo de blob do serviço de Armazenamento do Azure depois que a consistência é atingida. Isso é feito para obter um instantâneo consistente dos discos da máquina virtual, sem a necessidade de desligá-la.

Depois que o instantâneo é criado, os dados são transferidos pelo serviço do Backup do Azure para o cofre de backup. O serviço se encarrega de identificar e transferir somente os blocos que foram alterados desde o último backup, tornando o armazenamento e a rede de backups eficientes. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado. Esse ponto de recuperação pode ser visto no portal de gerenciamento do Azure.

>[AZURE.NOTE]Para máquinas virtuais do Linux, é possível apenas o backup consistente com arquivos.

## Pré-requisitos
Prepare o Backup do Azure para fazer backup dos dados do DPM da seguinte maneira:

1. **Crie um cofre de Backup** — Crie um cofre no console do Backup do Azure.
2. **Baixe credenciais do cofre** — No Backup do Azure, carregue o certificado de gerenciamento que você criou para o cofre.
3. **Instale o agente de Backup do Azure e registre o servidor** — No Backup do Azure, instale o agente em cada servidor DPM e registre o servidor DPM no cofre de backup.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## Requisitos (e limitações)

- O DPM pode ser executado como servidor físico ou máquina virtual Hyper-V instalado no System Center 2012 SP1 ou System Center 2012 R2. Também pode ser executado como máquina virtual do Azure em execução no System Center 2012 R2 com pelo menos Pacote cumulativo de atualizações 3 do DPM 2012 R2 ou máquina virtual do Windows em VMWare em execução no System Center 2012 R2 com pelo menos Pacote cumulativo de atualizações 5.
- Se você estiver executando o DPM com o System Center 2012 SP1, instale o Rollup de atualização 2 do System Center Data Protection Manager SP1. Isso é necessário antes da instalação do Agente de Backup do Azure.
- O servidor DPM deve ter o Windows PowerShell e o .net Framework 4.5 instalados.
- O DPM pode fazer backup da maioria das cargas de trabalho no Backup do Azure. Para obter uma lista completa do que tem suporte, consulte os itens de suporte do Backup do Azure abaixo.
- Os dados armazenados no Backup do Azure não podem ser recuperados com a opção "copiar em fita".
- Você precisará de uma conta Azure com o recurso de Backup do Azure habilitado. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre os [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
- O uso o Backup do Azure requer que o Agente de Backup do Azure esteja instalado nos servidores onde você deseja fazer backup. Cada servidor deve ter pelo menos 2,5 GB de espaço de armazenamento livre local para o local do cache, embora sejam recomendados 15 GB de espaço de armazenamento livre local para o local do cache.
- Os dados serão armazenados no armazenamento do cofre do Azure. Não há nenhum limite para a quantidade de dados de backup em um cofre de Backup do Azure, mas o tamanho de uma fonte de dados (por exemplo, máquina virtual ou banco de dados) não deve exceder 1,65 TB.

No Azure, é possível fazer backup dos seguintes tipos de arquivo:

- Criptografados (apenas backups completos)
- Compactados (suporte para backups incrementais)
- Esparsos (suporte para backups incrementais)
- Compactados e esparsos (tratados como esparsos)

E os seguintes não têm suporte:

- Não há suporte para servidores em sistemas de arquivo que diferenciam maiúsculas de minúsculas.
- Links físicos (ignorados)
- Pontos de nova análise (ignorados)
- Criptografados e compactados (ignorados)
- Criptografados e esparsos (ignorados)
- Fluxo compactado
- Fluxo esparso

>[AZURE.NOTE]No System Center 2012 DPM com SP1 em diante, é possível fazer backup de cargas de trabalho protegidas por DPM para o Azure usando o Backup do Microsoft Azure.

<!-----HONumber=July15_HO4-->
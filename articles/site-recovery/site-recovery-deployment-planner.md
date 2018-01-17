---
title: "Planejador de implantação do Azure Site Recovery para VMware ao Azure | Microsoft Docs"
description: "Este é o guia do usuário do planejador de implantação do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/04/2017
ms.author: nisoneji
ms.openlocfilehash: 2985ed0b4bf5d9525bc2274d71b703922524f5a8
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Planejador de Implantações do Azure Site Recovery para VMware para o Azure
Este artigo é o guia do usuário do Planejador de Implantação do Azure Site Recovery para implantações de produção do VMware para o Azure.

## <a name="overview"></a>Visão geral

Antes de começar a proteger VMs (máquinas virtuais) VMware usando o Site Recovery, aloque largura de banda suficiente, com base em sua taxa de alteração de dados diária, para atender ao RPO (objetivo de ponto de recuperação) desejado. Implante o número correto de servidores de configuração e servidores de processo locais.

Você também precisa criar o tipo e o número correto de contas de armazenamento do Azure de destino. Você cria contas de armazenamento standard ou premium, acrescentando o crescimento nos servidores de produção de origem devido ao aumento no uso ao longo do tempo. Você escolhe o tipo de armazenamento por VM, com base nas características de carga de trabalho (por exemplo, operações de E/S de leitura/gravação por segundo [IOPS] ou variação de dados) e nos limites do Site Recovery.

O planejador de implantação do Azure Site Recovery é uma ferramenta de linha de comando para ambos os cenários de recuperação de desastre do Hyper-V para o Azure e do VMware para o Azure. Você pode criar o perfil remotamente das VMs do VMware usando essa ferramenta (sem impacto na produção) para entender os requisitos de largura de banda e do Armazenamento do Azure para o êxito da replicação e do failover de teste. Você pode executar a ferramenta sem instalar componentes do Site Recovery no local. No entanto, para obter resultados precisos da taxa de transferência obtida, recomendamos que você execute o planejador em um Windows Server que atenda aos requisitos mínimos do servidor de configuração de Site Recovery que você provavelmente precisará implantar como uma das primeiras etapas da implantação de produção.

A ferramenta fornece os seguintes detalhes:

**Avaliação de compatibilidade**

* Avaliação de qualificação de uma VM com base no número de discos, no tamanho do disco, no IOPS, na variação, no tipo de inicialização (EFI/BIOS) e na versão do sistema operacional
 
**Largura de banda de rede necessária versus avaliação de RPO**

* A largura de banda de rede estimada que é necessária para a replicação delta
* A taxa de transferência que o Site Recovery pode obter do local para o Azure
* O número de VMs a serem incluídas em um lote com base na largura de banda estimada para concluir a replicação inicial em determinado período de tempo
* O RPO que pode ser obtido para uma determinada largura de banda
* Impacto sobre o RPO desejado se for provisionada menos largura de banda.

**Requisitos de infraestrutura do Azure**

* O requisito de tipo de armazenamento (conta de armazenamento standard ou premium) para cada VM
* O número total de contas de armazenamento standard e premium a serem configuradas para replicação
* Sugestões de nomes de contas de armazenamento com base na orientação do Armazenamento do Azure
* O posicionamento da conta de armazenamento para todas as VMs
* O número de núcleos do Azure a serem configurados antes do failover de teste ou do failover na assinatura
* O tamanho de VM do Azure recomendado para cada VM local

**Requisitos de infraestrutura local**
* O número necessário de servidores de configuração e servidores de processo a serem implantados no local

**Custo estimado de recuperação de desastre para o Azure**
* Custo total estimado de recuperação de desastre para o Azure: custos de licença de computação, armazenamento, rede e do Azure Site Recovery
* Detalhes de análise de custo por VM


>[!IMPORTANT]
>
>Como o uso provavelmente aumentará ao longo do tempo, todos os cálculos da ferramenta anterior são efetuados supondo um fator de crescimento de 30% nas características de carga de trabalho e usando um valor do 95º percentil de todas as métricas de criação de perfil (IOPS de leitura/gravação, variação e assim por diante). Esses dois elementos (fator de crescimento e cálculo de percentil) são configuráveis. Para saber mais sobre o fator de crescimento, confira a seção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, confira a seção "Valor de percentil usado para o cálculo".
>

## <a name="support-matrix"></a>Matriz de suporte

| | **VMware no Azure** |**Hyper-V para Azure**|**Azure para Azure**|**Hyper-V para site secundário**|**VMware para o site secundário**
--|--|--|--|--|--
Cenários com suporte |Sim|Sim|Não |Sim*|Não 
Versão com suporte | vCenter 6.5, 6.0 ou 5.5| Windows Server 2016, Windows Server 2012 R2 | ND |Windows Server 2016, Windows Server 2012 R2|ND
Configuração com suporte|vCenter, ESXi| cluster do Hyper-V, host do Hyper-V|ND|cluster do Hyper-V, host do Hyper-V|ND|
Número de servidores que podem ser analisados por instância em execução do Planejador de Implantações do Azure Site Recovery |Único (máquinas virtuais que pertencem a um vCenter Server ou um servidor ESXi podem ser criados em um momento)|Vários (as máquinas virtuais em vários hosts ou clusters de hosts podem ser analisadas de cada vez)| ND |Vários (as máquinas virtuais em vários hosts ou clusters de hosts podem ser analisadas de cada vez)| ND

*A ferramenta destina-se principalmente ao cenário de recuperação de desastre do Hyper-V para o Azure. Para a recuperação de desastre do Hyper-V para o site secundário, ela só pode ser usado para entender as recomendações do lado de origem, como a largura de banda de rede necessária, o espaço livre de armazenamento necessário em cada um dos servidores Hyper-V de origem e os números e as definições de lote da replicação inicial.  Ignore as recomendações do Azure e os custos do relatório. Além disso, a operação Obter Taxa de Transferência não é aplicável ao cenário de recuperação de desastre do Hyper-V para o site secundário.

## <a name="prerequisites"></a>Pré-requisitos
A ferramenta tem duas fases principais: criação de perfil e geração de relatórios. Também há uma terceira opção para calcular apenas a taxa de transferência. Os requisitos para o servidor do qual a medida de taxa de transferência e criação de perfil é iniciada são apresentados na seguinte tabela:

| Requisito de servidor | DESCRIÇÃO|
|---|---|
|Medida de taxa de transferência e criação de perfil| <ul><li>Sistema operacional: Microsoft Windows Server 2016 ou Microsoft Windows Server 2012 R2<br>(a correspondência ideal são pelo menos as [recomendações de tamanho para o servidor de configuração](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configuração de máquina: 8 vCPUs, 16 GB de RAM, 300 GB de disco rígido</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Microsoft Visual C++ redistribuível para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure neste servidor</li><li>Conta de Armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre em disco (supondo que haja 1000 VMs com uma média de três discos em cada, com criação de perfil por 30 dias)</li><li>As configurações no nível das estatísticas do VMware vCenter devem ser definidas para o nível 2 ou superior</li><li>Permitir porta 443: o Planejador de implantação da ASR usa essa porta para se conectar ao host de ESXi/servidor do vCenter</ul></ul>|
| Geração de relatórios | Um PC Windows ou Windows Server com o Microsoft Excel 2013 ou posterior |
| Permissões de usuário | Permissão somente leitura para a conta de usuário que é usada para acessar o servidor vCenter VMware/host ESXi vSphere VMware durante a criação de perfil |

> [!NOTE]
>
>A ferramenta pode criar o perfil apenas para VMs com discos VMDK e RDM. Ela não pode criar o perfil de VMs com discos iSCSI ou NFS. O Site Recovery dá suporte a discos iSCSI e NFS para servidores VMware, mas, como o planejador de implantação não está no convidado e cria perfis apenas usando contadores de desempenho do vCenter, a ferramenta não tem visibilidade para esses tipos de disco.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Baixe e extraia a ferramenta de planejamento de implantação
1. Baixe a versão mais recente do [planejador de implantação do Azure Site Recovery](https://aka.ms/asr-deployment-planner).  
A ferramenta é empacotada em uma pasta .zip. A versão atual da ferramenta dá suporte apenas ao cenário do VMware para o Azure.

2. Copie a pasta .zip para o servidor Windows do qual você deseja executar a ferramenta.  
Você poderá executar a ferramenta no Windows Server 2012 R2 se o servidor tiver acesso à rede para se conectar ao servidor vCenter/host ESXi vSphere que mantém as VMs para a criação de perfil. No entanto, recomendamos que você execute a ferramenta em um servidor cuja configuração de hardware atenda à [orientação de dimensionamento do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components). Se já tiver implantado os componentes do Site Recovery no local, execute a ferramenta no servidor de configuração.

 Recomendamos que você tenha a mesma configuração de hardware que o servidor de configuração (que tem um servidor de processo integrado) no servidor em que executa a ferramenta. Essa configuração garante que a taxa de transferência obtida relatada pela ferramenta corresponda à taxa de transferência real que o Site Recovery pode alcançar durante a replicação. O cálculo de taxa de transferência depende da largura de banda disponível no servidor e da configuração de hardware (CPU, armazenamento e assim por diante) do servidor. Se você executar a ferramenta de qualquer outro servidor, a taxa de transferência será calculada desse servidor para o Microsoft Azure. Além disso, como a configuração de hardware do servidor pode ser diferente daquela do servidor de configuração, a taxa de transferência obtida relatada pela ferramenta de relatórios pode ser imprecisa.

3. Extraia a pasta .zip.  
A pasta contém vários arquivos e subpastas. O arquivo executável é ASRDeploymentPlanner.exe na pasta pai.

    Exemplo:  
    Copie o arquivo .zip para a unidade E:\ e extraia-o.
   E:\ASR Deployment Planner_v2.1zip

    E:\ASR Deployment Planner_v2.1\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Atualização para a versão mais recente do Planejador de Implantações
Se você tiver uma versão anterior do Planejador de Implantações, siga um destes procedimentos:
 * Se a versão mais recente não contém uma correção de criação de perfil, e a criação de perfil já está em andamento na versão atual do planejador, continue a criação de perfil.
 * Se a versão mais recente contém uma correção de criação de perfil, é recomendável parar criação de perfil na versão atual e reiniciá-la com a nova versão.


 >[!NOTE]
 >
 >Ao iniciar a criação de perfil com a nova versão, passe o mesmo caminho de diretório de saída para que a ferramenta acrescente dados de perfil aos arquivos existentes. Um conjunto completo de dados com criação de perfil será ser usado para gerar o relatório. Se você passar um diretório de saída diferente, novos arquivos serão criados, e os dados de criação de perfil antigos não serão usados para gerar o relatório.
 >
 >Cada novo planejador de implantação é uma atualização cumulativa do arquivo .zip. Você não precisa copiar os arquivos mais recentes para a pasta anterior. Você pode criar e usar uma nova pasta.


## <a name="version-history"></a>Histórico de versão
A versão mais recente da ferramenta Planejador de Implantações do ASR é a 2.1.
Consulte a página [Histórico de versões do Planejador de Implantações do ASR](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) para obter as correções adicionadas em cada atualização.

## <a name="next-steps"></a>Próximas etapas
* [Execute o Planejador de Implantações](site-recovery-vmware-deployment-planner-run.md).

<properties
 pageTitle="Adicionar nós de disparo contínuo a um cluster de HPC Pack | Microsoft Azure"
 description="Saiba como expandir a capacidade do cluster HPC Pack sob demanda adicionando instâncias de função de trabalho em execução em um serviço de nuvem"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/15/2016"
 ms.author="danlep"/>

# Adicionar nós de “intermitência” sob demanda a um cluster HPC Pack no Azure



Este artigo mostra como adicionar os nós de “disparo contínuo” do Azure (instâncias de função de trabalho em execução em um serviço de nuvem) sob demanda como recursos de computação a um nó de cabeçalho do HPC Pack existente no Azure. Isso permite escalar verticalmente a capacidade de computação do cluster HPC no Azure sob demanda, sem manter um conjunto de VMs de nó de computação pré-configuradas.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nós de disparo contínuo][burst]

As etapas neste artigo ajudarão você a adicionar nós do Azure rapidamente a uma VM de nó de cabeçalho do HPC Pack baseado em nuvem para uma implantação de teste ou de prova de conceito. O procedimento é basicamente o mesmo que o usado para o “disparo contínuo para o Azure” para adicionar a capacidade de computação em nuvem a um cluster HPC Pack local. Para obter um tutorial, veja [Configurar um cluster de cálculo híbrido com o Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para obter diretrizes e considerações detalhadas sobre implantações de produção, veja [Disparo contínuo para o Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

Para ver considerações sobre o uso do tamanho A8 ou A9 de instância de computação intensiva, veja [Sobre as instâncias de computação intensiva A8, A9, A10 e A11](virtual-machines-windows-a8-a9-a10-a11-specs.md).

## Pré-requisitos

* **Nó de cabeçalho do HPC Pack implantado em uma VM do Azure** – Você pode usar uma VM de nó de cabeçalho autônomo ou uma que faça parte de um cluster maior. Para criar um nó de cabeçalho autônomo, veja [Criar o nó principal de um cluster de Pacote HPC em uma VM do Azure com uma imagem do Marketplace](virtual-machines-windows-hpcpack-cluster-headnode.md). Para obter opções de implantação do cluster HPC Pack, veja [Opções para criar e gerenciar um cluster HPC (computação de alto desempenho) do Windows no Azure com o Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Se você usar o [script de implantação de IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para criar o cluster no Azure, é possível incluir os nós de disparo contínuo do Azure em sua implantação automatizada. Consulte os exemplos nesse artigo.

* **Assinatura do Azure** - Para adicionar nós do Azure, você pode escolher a mesma assinatura usada para implantar a VM de nó de cabeçalho ou uma assinatura (ou assinaturas) diferente.

* **Cota de núcleos** - Talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós do Azure com tamanhos de vários núcleos. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

## Etapa 1: criar um serviço de nuvem e uma conta de armazenamento para adicionar nós do Azure

Use o portal clássico do Azure ou ferramentas equivalentes para configurar o seguinte, que é necessário para implantar seus nós do Azure:

* Um novo serviço de nuvem do Azure
* Uma nova conta de armazenamento do Azure

>[AZURE.NOTE] Não reutilize um serviço de nuvem existente em sua assinatura.

**Considerações**

* Configure um serviço de nuvem separado para cada modelo de nó do Azure que você pretende criar. No entanto, é possível usar a mesma conta de armazenamento para vários modelos de nó.

* Você geralmente deve localizar o serviço de nuvem e a conta de armazenamento para a implantação na mesma região do Azure.




## Etapa 2: configurar um certificado de gerenciamento do Azure

Para adicionar nós do Azure como recursos de computação, você precisará ter um certificado de gerenciamento no nó de cabeçalho e carregar um certificado correspondente na assinatura do Azure usada para a implantação.

Para este cenário, é possível escolher o **Certificado padrão de gerenciamento do HPC Azure** que o HPC Pack instala e configura automaticamente no nó de cabeçalho. Este certificado é útil para implantações de prova de conceito e fins de teste. Para usar esse certificado, basta carregar o arquivo C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer da VM de nó de cabeçalho na assinatura. Você poderá fazer isso no [portal clássico do Azure](https://manage.windowsazure.com). Clique em **Configurações** > **Certificados de Gerenciamento**.

Para obter opções adicionais para configurar o certificado de gerenciamento, veja [Cenários para configurar o certificado de gerenciamento do Azure para implantações de disparo contínuo do Azure](http://technet.microsoft.com/library/gg481759.aspx).

## Etapa 3: implantar nós do Azure ao cluster



As etapas para adicionar e iniciar os nós do Azure neste cenário geralmente são as mesmas usadas com um nó de cabeçalho local. Para obter mais informações, veja as seguintes seções em [Etapas para implantar nós do Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Criar um modelo de nó do Azure

* Adicionar nós do Azure ao cluster Windows HPC

* Iniciar (provisionar) os nós do Azure

Depois de adicionar e iniciar os nós, eles estarão prontos para uso para a execução de trabalhos de cluster.

Se tiver problemas ao implantar nós do Azure, veja [Troubleshoot Deployments of Azure Nodes with Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx) (Solucionar problemas de nós do Azure com o Microsoft HPC Pack).

## Próximas etapas

* Caso deseje aumentar ou reduzir automaticamente os recursos de computação do Azure de acordo com a atual carga de trabalho de trabalhos e tarefas no cluster, veja [Aumentar e reduzir automaticamente os recursos do cluster HPC Pack no Azure conforme a carga de trabalho do cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png

<!---HONumber=AcomDC_0720_2016-->
<properties
 pageTitle="Adicionar nós de disparo contínuo a um cluster de HPC Pack | Microsoft Azure"
 description="Saiba como adicionar instâncias de função de trabalho em execução em um serviço de nuvem sob demanda como recursos de computação a um nó de cabeçalho do HPC Pack no Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Adicionar nós de “disparo contínuo” sob demanda (instâncias de função de trabalho) como recursos de computação a um cluster HPC Pack no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Este artigo mostra como adicionar os nós de “disparo contínuo” do Azure (instâncias de função de trabalho em execução em um serviço de nuvem) sob demanda como recursos de computação a um nó de cabeçalho do HPC Pack existente no Azure. Isso permite escalar verticalmente a capacidade de computação do cluster HPC no Azure, sem manter um conjunto de VMs de nó de computação pré-configuradas.

![Nós de disparo contínuo][burst]

>[AZURE.TIP]Se você usar o [script de implantação de IaaS do HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) para criar o cluster no Azure, é possível incluir os nós de disparo contínuo do Azure em sua implantação automatizada.

As etapas neste artigo ajudarão você a adicionar nós do Azure rapidamente a uma VM de nó de cabeçalho do HPC Pack baseado em nuvem para uma implantação de teste ou de prova de conceito. O procedimento é basicamente o mesmo que o usado para o “disparo contínuo para o Azure” para adicionar a capacidade de computação em nuvem a um cluster HPC Pack local. Para obter um tutorial, veja [Configurar um cluster de cálculo híbrido com o Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para obter orientações e considerações detalhadas sobre implantações de produção, veja [Disparo contínuo para o Azure com o Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493).

Se desejar usar o tamanho de instância A8 ou A9 de uso intensivo de computação, veja [Sobre as instâncias A8, A9, A10 e A11 de computação intensiva](virtual-machines-a8-a9-a10-a11-specs.md).

## Pré-requisitos

* **Nó de cabeçalho do HPC Pack implantado em uma VM do Azure** -Veja [Implantar um nó de cabeçalho do HPC Pack em uma VM do Azure](virtual-machines-hpcpack-cluster-headnode.md) para obter as etapas para criar um nó de cabeçalho do cluster no modelo de implantação clássico (Gerenciamento de Serviço).

* **Assinatura do Azure** - Para adicionar nós do Azure, você pode escolher a mesma assinatura usada para implantar a VM de nó de cabeçalho ou uma assinatura (ou assinaturas) diferente.

* **Cota de núcleos** - Talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós do Azure com tamanhos de vários núcleos. Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

## Etapa 1: criar um serviço de nuvem e uma conta de armazenamento para adicionar nós do Azure

Use o portal do Azure ou ferramentas equivalentes para configurar o seguinte, que é necessário para implantar seus nós do Azure:

* Um novo serviço de nuvem do Azure
* Uma nova conta de armazenamento do Azure

>[AZURE.NOTE]Não reutilize um serviço de nuvem existente em sua assinatura. Também não implante um pacote de serviço de nuvem separado e personalizado neste serviço de nuvem. O HPC Pack implanta automaticamente um pacote de serviço de nuvem quando você inicia (provisiona) os nós do Azure.

**Considerações**

* Configure um serviço de nuvem separado para cada modelo de nó do Azure que você pretende criar. No entanto, é possível usar a mesma conta de armazenamento para vários modelos de nó.

* Você geralmente deve localizar o serviço de nuvem e a conta de armazenamento para a implantação na mesma região.




## Etapa 2: configurar um certificado de gerenciamento do Azure

Para adicionar nós do Azure como recursos de computação, você precisará ter um certificado de gerenciamento no nó de cabeçalho e carregar um certificado correspondente na assinatura do Azure usada para a implantação.

Para este cenário, é possível escolher o **Certificado padrão de gerenciamento do HPC Azure** que o HPC Pack instala e configura automaticamente no nó de cabeçalho. Este certificado é útil para implantações de prova de conceito e fins de teste. Para usar esse certificado, basta carregar o arquivo C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer da VM de nó de cabeçalho na assinatura.

Para obter opções adicionais para configurar o certificado de gerenciamento, veja [Cenários para configurar o certificado de gerenciamento do Azure para implantações de disparo contínuo do Azure](http://technet.microsoft.com/library/gg481759.aspx).

## Etapa 3: implantar nós do Azure ao cluster



As etapas para adicionar e iniciar os nós do Azure neste cenário geralmente são as mesmas usadas com um nó de cabeçalho local. Para obter mais informações, veja as seguintes seções em [Etapas para implantar nós do Azure com o Microsoft HPC Pack]((https://technet.microsoft.com/library/gg481758(v=ws.10).aspx):

* Criar um modelo de nó do Azure

* Adicionar nós do Azure ao cluster Windows HPC

* Iniciar (provisionar) os nós do Azure

Depois de adicionar e iniciar os nós, eles estarão prontos para uso para a execução de trabalhos de cluster.

Se você encontrar problemas ao implantar nós do Azure, veja [Solucionar problemas de implantações de nós do Azure com o Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097(v=ws.10).aspx).

## Próximas etapas

* Se desejar uma maneira de aumentar ou reduzir automaticamente os recursos de computação do Azure de acordo com a atual carga de trabalho de trabalhos e tarefas no cluster, veja [Aumentar e reduzir os recursos de computação do Azure em um cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-hpcpack-cluster-node-burst/burst.png

<!---HONumber=Nov15_HO3-->
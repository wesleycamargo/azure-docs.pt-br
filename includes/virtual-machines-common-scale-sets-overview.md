

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização. Para ver uma introdução aos conjuntos de dimensionamento de VM, confira o recente [Comunicado no blog do Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/).

Examine esses vídeos para saber mais sobre conjuntos de dimensionamento de VM:

* [Mark Russinovich fala sobre os Conjuntos de Dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Criando e gerenciando conjuntos de dimensionamento de VM
Os conjuntos de dimensionamento de VM podem ser definidos e implantados usando modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx) , assim como as VMs individuais do Gerenciador de Recursos do Azure. Portanto, quaisquer métodos de implantação do Gerenciador de Recursos do Azure padrão podem ser usados. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../articles/resource-group-authoring-templates.md).

Um conjunto de modelos de exemplo para conjuntos de dimensionamento de VM pode ser encontrado no repositório GitHub de modelos do Guia de Início Rápido do Azure, aqui:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - procure os modelos com *vmss* no título.

Nas páginas de detalhes para esses modelos, você verá um botão que vincula ao recurso de implantação do portal. Para implantar o conjunto de dimensionamento de VM, clique no botão e preencha todos os parâmetros obrigatórios no portal. Se você não tiver certeza se um recurso oferece suporte a letras maiúsculas ou combinadas, é mais seguro usar sempre valores de parâmetro em letras minúsculas. Também é uma prática dissecar vídeo de um modelo de conjunto de dimensionamento de VM aqui:

[Dissecação de Modelo do Conjunto de Dimensionamento de VM](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Expandindo e reduzindo um conjunto de dimensionamento de VM
Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de dimensionamento de VM, basta alterar a propriedade *capacity* e reimplantar o modelo. Essa simplicidade facilita escrever sua própria camada de dimensionamento personalizada se você quiser definir eventos de escala personalizada que não têm suporte do dimensionamento automático do Azure.

Se estiver reimplantando um modelo para alterar a capacidade, você pode definir um modelo muito menor que inclui apenas a SKU e a capacidade atualizada. Um exemplo disso é mostrado em: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Para percorrer as etapas para criar um conjunto de dimensionamento que foi dimensionado automaticamente, confira [Dimensionar automaticamente as máquinas em um Conjunto de dimensionamento de máquina virtual](../articles/virtual-machines/virtual-machines-windows-vmss-powershell-creating.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="monitoring-your-vm-scale-set"></a>Monitoramento de seu conjunto de dimensionamento de VM
Atualmente recomenda-se a utilização do [Azure Resource Explorer](https://resources.azure.com) para exibir os conjuntos de dimensionamento de VM. Os conjuntos de dimensionamento de VM são um recurso encontrado em Microsoft.Compute, de modo que nesse site, você pode vê-los expandindo os seguintes links:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Cenários do conjunto de dimensionamento de VM
Esta seção lista alguns cenários típicos de conjunto de dimensionamento de VM. Além disso, alguns serviços do Azure de nível mais alto (como Lote, Service Fabric, Serviço de Contêiner do Azure) usarão esses cenários.

* **RDP / SSH para instâncias de conjunto de dimensionamento de VM** - Um conjunto de dimensionamento de VM é criado dentro de uma rede virtual e as VMs individuais dentro dele não são endereços IP públicos alocados. Isso é bom, pois geralmente você não quer as despesas e os custos gerais de gerenciamento de alocar endereços IP separados para todos os recursos sem estado na sua grade de computação, e você pode se conectar facilmente a essas VMs de outros recursos na sua rede virtual, incluindo aqueles que têm endereços IP públicos como balanceadores de carga ou máquinas virtuais autônomas.
* **Conectar-se a VMs usando regras de NAT** -Você pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir regras NAT de entrada que mapeiam uma porta no endereço IP para uma porta em uma máquina virtual no conjunto de dimensionamento de VM. Por exemplo
  
   Public IP->Port 50000 -> vmss\_0->Port 22  Public IP->Port 50001 -> vmss\_1->Port 22  Public IP->Port 50002-> vmss\_2->Port 22
  
   Veja um exemplo de como criar um conjunto de dimensionamento de VM que usa regras NAT para habilitar a conexão SSH para cada VM em um conjunto de dimensionamento usando um único IP público: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Veja um exemplo de como fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Conectar às VMs usando uma "jumpbox"** - Se você criar um conjunto de dimensionamento de VM e uma VM autônoma na mesma rede virtual, a VM autônoma e o conjunto de dimensionamento de VM podem se conectar um ao outro usando seus endereços IP internos, conforme definido pela sub-rede/rede virtual. Se você criar um endereço IP público e atribuí-lo à VM autônoma, pode realizar RDP ou SSH para a VM autônoma e conectar-se nessa máquina para suas instâncias de conjunto de dimensionamento de VM. Você pode perceber, nesse momento, que um simples conjunto de escalas de VM é inerentemente mais seguro do que uma simples VM autônoma com um endereço IP público em sua configuração padrão.
  
   Para obter um exemplo dessa abordagem, este modelo cria um cluster Mesos simples que consiste em uma VM mestre autônoma que gerencia um cluster de VMs baseado no conjunto de dimensionamento: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)
* **Balanceamento de carga round-robin para instâncias de conjunto de dimensionamento de VM** - Se desejar entregar trabalho a um cluster de VMs de computação usando uma abordagem "round-robin", você poderá configurar um balanceador de carga do Azure com regras de balanceamento de carga apropriadas. Também é possível definir investigações para verificar se o aplicativo está em execução ao executar ping de portas com um protocolo especificado, intervalo e caminho de solicitação.
  
   Veja um exemplo que cria um conjunto de escalas de VM de VMs que executam o servidor Web IIS e usa um balanceador de carga para equilibrar a carga que cada VM recebe. Ele também usa o protocolo HTTP para executar ping de uma URL específica em cada VM: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) — observe o tipo de recurso Microsoft.Network/loadBalancers, bem como networkProfile e extensionProfile no virtualMachineScaleSet.
* **Implantar um  conjunto de dimensionamento de VM como um cluster de computação em um gerenciador de cluster de PaaS** - Os  conjuntos de dimensionamento de VM são muitas vezes descritos como uma função de trabalho de próxima geração. É uma descrição válida, mas há o risco de confundir recursos do conjunto de escalas com recursos da função de trabalho PaaS v1. De certo modo, os conjuntos de escalas de VM fornecem uma verdadeira "função de trabalho" ou recurso de trabalho, já que eles fornecem um recurso de computação generalizado que é independente de plataforma/tempo de execução, personalizável e se integra à IaaS do Gerenciador de Recursos do Azure.
  
   Uma função de trabalho PaaS v1, embora limitada em termos de suporte à plataforma/tempo de execução (apenas imagens da plataforma Windows) também inclui serviços como troca de VIP, definições de atualização configuráveis, configurações específicas de implantação de aplicativo/tempo de execução, que *ainda* não estão disponíveis nos conjuntos de dimensionamento de VMs ou serão fornecidos por outros serviços PaaS de nível mais alto, como o Service Fabric. Com isso em mente, você pode observar os conjuntos de escalas de VM como uma infraestrutura que oferece suporte à PaaS. Isto é, as soluções PaaS, como o Service Fabric ou os gerenciadores de clusters, como o Mesos, podem ser criadas com base nos conjuntos de escalas de VM como uma camada de computação escalonável.
  
   Veja um exemplo de um cluster Mesos que implanta um conjunto de escalas de VM na mesma rede virtual como uma VM autônoma. A VM autônoma é um mestre Mesos, e o conjunto de dimensionamento de VM representa um conjunto de nós subordinados: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). As versões futuras do [Serviço de Contêiner do Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implantarão versões mais complexas/reforçadas desse cenário com base nos conjuntos de dimensionamento de VM.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Orientação de dimensionamento e desempenho do conjunto de dimensionamento de VM
* Durante a visualização pública, não crie mais de 500 VMs em vários conjuntos de escalas de VM por vez.
* Planeje-se para ter não mais que 40 VMs por conta de armazenamento.
* Diversifique o máximo possível as primeiras letras dos nomes da conta de armazenamento.  Os modelos de VMSS de exemplo nos [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) fornecem exemplos de como fazer isso.
* Se estiver usando VMs personalizadas, planeje-se para ter não mais que 40 VMs por conjunto de escalas de VM, em uma única conta de armazenamento.  Você precisará da imagem previamente copiada na conta de armazenamento para poder começar a implantação do conjunto de escalas de VM. Consulte as Perguntas frequentes para obter mais informações.
* Planeje-se para ter não mais que 2048 VMs por rede virtual.  Esse limite será aumentado no futuro.
* O número de VMs que você pode criar é limitado pela sua cota de núcleo de computação em qualquer região. Talvez seja necessário contatar o Atendimento ao Cliente para aumentar o limite da cota de computação mesmo que hoje você tenha um limite alto de núcleos para uso com serviços de nuvem ou IaaS v1. Para consultar sua cota, você pode executar o seguinte comando da CLI do Azure: *azure vm list-usage* e o seguinte comando do PowerShell: *Get-AzureRmVMUsage* (se estiver usando uma versão do PowerShell abaixo de 1.0, use *Get-AzureVMUsage*).

## <a name="vm-scale-set-frequently-asked-questions"></a>Conjunto de dimensionamento de VM - Perguntas frequentes
**P.** Quantas VMs você pode ter em um conjunto de dimensionamento de VM?

**A.** &100;, se você usar imagens de plataforma que podem ser distribuídas entre várias contas de armazenamento. Se você usar imagens personalizadas, até 40, desde que as imagens personalizadas sejam limitadas a uma única conta de armazenamento durante a visualização.

**P** Que outros limites de recurso existem para conjuntos de dimensionamento de VM?

**A.** Você está limitado a criar não mais de 500 VMs nos vários conjuntos de dimensionamento por região durante o período de visualização. Os [Limites de serviço de assinatura do Azure/](../articles/azure-subscription-service-limits.md) existentes se aplicam.

**P.** Há suporte para os discos de dados nos conjuntos de dimensionamento de VM?

**A.** Não na versão inicial. Suas opções de armazenamento de dados são:

* Arquivos do Azure (unidades compartilhada de SMB)
* Unidade do sistema operacional
* Unidade TEMP (local, não tem relação com o armazenamento do Azure)
* Serviço de dados externo (por exemplo, banco de dados remoto, tabelas do Azure, blobs do Azure)

**P.** Quais são as regiões do Azure que oferecem suporte aos conjuntos de dimensionamento de VM?

**A.** Qualquer região que ofereça suporte ao Gerenciador de Recursos do Azure oferece suporte aos conjuntos de escalas de VM.

**P.** Como criar conjuntos de dimensionamento de VM usando uma imagem personalizada?

**A.** Deixe a propriedade vhdContainers em branco, por exemplo:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**P.** Se eu reduzir a capacidade do meu conjunto de dimensionamento de VM de 20 para 15, quais VMs serão removidas?

**A.** Máquinas virtuais são removidas do conjunto de dimensionamento uniformemente entre domínios de atualização e domínios de falha para maximizar a disponibilidade.

**P.** E se eu aumentar a capacidade de 15 para 18?

**A.** Se você aumentar para 18, as VMs com índice 15, 16, 17 serão criadas. Em ambos os casos, as VMs são balanceadas entre FDs e UDs.

**P.** Ao usar várias extensões em um conjunto de dimensionamento de VM, posso impor uma sequência de execução?

**A.** Não diretamente, mas para a extensão customScript, seu script pode aguardar por outra extensão para ser concluído (por exemplo, monitorando o log de extensão — confira [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**P.** Os conjuntos de dimensionamento de VM funcionam com os conjuntos de disponibilidade do Azure?

**A.** Sim. Um conjunto de dimensionamento de VM é um conjunto de disponibilidade implícito com 3 FDs e 5 UDs. Não é preciso configurar nada em virtualMachineProfile. Em versões futuras, os conjuntos de dimensionamento de VM provavelmente incluirão vários locatários, mas, por enquanto, um conjunto de dimensionamento é um único conjunto de disponibilidade.



<!--HONumber=Jan17_HO5-->



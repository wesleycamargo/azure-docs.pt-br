<properties
 pageTitle="Criar um nó principal do Pacote HPC em uma VM do Azure | Microsoft Azure"
 description="Saiba como usar o portal do Azure e o modelo clássico de implantação para criar um nó principal do Pacote HPC da Microsoft em uma VM do Azure."
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

# Criar o nó principal de um cluster de Pacote HPC em uma VM do Azure com uma imagem do Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Este artigo mostra como usar a [imagem de máquina virtual do Pacote HPC da Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) no Azure Marketplace para criar o nó principal de um cluster Windows HPC no Azure no modelo de implantação clássico (Gerenciamento de Serviço). O nó principal precisa ser adicionado a um domínio do Active Directory em uma rede virtual do Azure. Você pode usar esse nó principal como prova de implantação de conceito do Pacote HPC no Azure e para adicionar recursos de computação ao cluster para executar cargas de trabalho do HPC.


![Nó principal do Pacote HPC][headnode]

>[AZURE.NOTE]Atualmente, a imagem da VM do Pacote HPC baseia-se no Windows Server 2012 R2 Datacenter com Pacote HPC 2012 R2 Update 2 pré-instalado. O Microsoft SQL Server 2014 Express também vem instalado.


Para uma implantação de produção de um cluster de Pacote HPC no Azure, é recomendável um método de implantação automatizada, como o [script de implantação de IaaS do Pacote HPC](virtual-machines-hpcpack-cluster-powershell-script.md) ou um [modelo de início rápido](https://azure.microsoft.com/documentation/templates/) do Gerenciador de recursos do Azure

## Considerações sobre planejamento

* **Domínio do Active Directory**: o nó principal do Pacote HPC deve ser associado a um domínio do Active Directory no Azure antes de iniciar os serviços do HPC. Uma opção é implantar um controlador de domínio e floresta separados no Azure aos quais você pode adicionar a VM. Para obter uma implantação prova de conceito, você pode promover a VM criada para o nó principal como controlador de domínio antes de iniciar os serviços do HPC.

* **Rede virtual do azure**: se você planeja adicionar VMs do nó de computação do cluster para o cluster HPC ou criar um controlador de domínio separado para o cluster, precisará implantar o nó principal em uma rede virtual do Azure (VNet). Sem uma VNet, você ainda pode adicionar nós “intermitentes” do Azure ao cluster.

## Etapas para criar o nó principal

A seguir estão as etapas de nível superior para criar uma VM do Azure para o nó principal do Pacote HPC. Você pode usar uma variedade de ferramentas do Azure para realizar essas etapas no modelo de implantação clássico (Gerenciamento de Serviço) do Azure.


1. Se você planeja criar uma VNet para a VM do nó principal, consulte [Criar uma rede virtual (clássica) usando o portal de visualização do Azure](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md).

    **Considerações**

    * Você pode aceitar a configuração padrão de sub-redes e do espaço de endereço da rede virtual.

    * Se você planeja usar um tamanho de instância de computação intensiva (A8 – A11) para o nó principal do Pacote HPC ou posteriormente ao adicionar recursos ao cluster de computação, escolha uma região na qual as instâncias estejam disponíveis. Ao usar instâncias A8 ou A9 para cargas de trabalho MPI, verifique se o espaço de endereço da rede virtual não se sobrepõe ao espaço de endereço reservado pela rede RDMA no Azure (172.16.0.0/12). Para saber mais, consulte [Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação](virtual-machines-a8-a9-a10-a11-specs.md).

2. Se você precisar criar uma nova floresta do Active Directory em uma máquina virtual separada, consulte [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](../active-directory/active-directory-new-forest-virtual-machine.md).

    **Considerações**

    * Para muitas implantações de teste, você pode criar um controlador de domínio único no Azure. Para garantir a alta disponibilidade do domínio do Active Directory, você pode implantar um controlador de domínio adicional, o backup.

    * Para obter prova de implantação do conceito, você pode pular essa etapa e promover posteriormente a VM do nó principal como controlador de domínio.

3. No portal do Azure ou no portal de visualização do Azure, crie uma VM clássica selecionando a imagem do Pacote HPC 2012 R2 no Azure Marketplace. (Consulte as etapas para o portal do Azure [aqui](virtual-machines-windows-tutorial-classic-portal.md).)

    **Considerações**

    * Selecione um tamanho de VM pelo menos A4.

    * Se você quiser implantar o nó principal em uma VNet, especifique a VNet na configuração da VM.

    * É recomendável que você crie um novo serviço de nuvem para a VM.

4. Depois que a VM for criada e estiver em execução, adicione a máquina virtual a uma floresta de domínio existente ou crie uma nova floresta de domínio na VM.

    **Considerações**

    * Se você criou a VM em uma VNet do Azure com uma floresta de domínio existente, conecte-se à VM. Use ferramentas padrão do Server Manager ou do Windows PowerShell para associá-la à floresta de domínio. Em seguida, reinicie.

    * Se a VM não tiver sido criada em uma VNet do Azure, ou tiver sido criada em uma VNet sem uma floresta de domínio existente, promova-a a controlador de domínio. Para isso, conecte-se à VM e use ferramentas padrão do Server Manager ou do Windows PowerShell. Para obter etapas detalhadas, consulte [Instalar uma nova floresta do Active Directory no Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Depois que a máquina virtual estiver em execução e fizer parte de uma floresta do Active Directory, inicie os serviços do Pacote HPC no nó principal. Para fazer isso:

    a. Conecte-se à VM com uma conta de domínio que seja membro do grupo Administradores local. Por exemplo, você poderia usar a conta de administrador que foi configurada quando você criou a VM do nó principal.

    b. Para uma configuração de nó principal padrão, inicie o Windows PowerShell como administrador e digite o seguinte:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Pode levar vários minutos para que os serviços do Pacote HPC iniciem.

    Para opções de configuração adicionais de nó principal, digite `get-help HPCHNPrepare.ps1`.


## Próximas etapas

* Agora você pode trabalhar com o nó principal do cluster do Windows HPC. Por exemplo, você pode iniciar o Gerenciador de Cluster HPC ou começar a trabalhar com os cmdlets do PowerShell do HPC.

* [Adicionar VMs de nó de computação](virtual-machines-hpcpack-cluster-node-manage.md) ao seu cluster ou adicionar [nós intermitentes do Azure](virtual-machines-hpcpack-cluster-node-burst.md) em um serviço de nuvem.

* Tente executar uma carga de trabalho de teste no cluster. Para obter um exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144) do Pacote HPC.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=Nov15_HO3-->
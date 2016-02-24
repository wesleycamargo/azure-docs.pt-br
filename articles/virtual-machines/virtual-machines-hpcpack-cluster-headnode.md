<properties
 pageTitle="Criar um nó principal do Pacote HPC em uma VM do Azure | Microsoft Azure"
 description="Saiba como usar o portal do Azure e o modelo de implantação do Gerenciador de Recursos para criar um nó principal do Pacote HPC da Microsoft em uma VM do Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Criar o nó principal de um cluster de Pacote HPC em uma VM do Azure com uma imagem do Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Este artigo mostra como usar uma [imagem de máquina virtual do Pacote HPC da Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) no Azure Marketplace para criar o nó principal de um cluster HPC usando o portal do Azure. Esta imagem da VM do Pacote HPC baseia-se no Windows Server 2012 R2 Datacenter com Pacote HPC 2012 R2 Atualização 3 pré-instalado. Use esse nó principal para uma implantação de prova de conceito do Pacote HPC no Azure. Você pode adicionar recursos de computação ao cluster para executar cargas de trabalho HPC.


![Nó principal do Pacote HPC][headnode]

>[AZURE.TIP]Para uma implantação de produção de um cluster completo do Pacote HPC no Azure, recomendamos que você use um método automatizado, como o [script de implantação IaaS do Pacote HPC](virtual-machines-hpcpack-cluster-powershell-script.md) ou um modelo do Gerenciador de Recursos, como o modelo [cluster de Pacote HPC para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Consulte as [Opções de cluster do Pacote HPC no Azure](virtual-machines-hpcpack-cluster-options.md) para ver modelos adicionais.

## Considerações sobre planejamento

* **Domínio do Active Directory** - o nó principal do Pacote HPC deve ser associado a um domínio do Active Directory no Azure antes de iniciar os serviços do HPC na VM. Como mostrado neste artigo, para obter uma implantação prova de conceito, você pode promover a VM criada para o nó principal como controlador de domínio antes de iniciar os serviços do HPC. Outra opção é implantar um controlador de domínio e floresta separados no Azure aos quais você pode adicionar a VM.

* **Rede virtual do azure** - conforme mostrado neste artigo, quando implanta o nó principal do Pacote HPC usando o modelo de implantação do Gerenciador de Recursos no portal do Azure, você especifica ou cria uma rede virtual do Azure (VNet). Você precisará usar a rede virtual posteriormente para adicionar VMs do nó de computação do cluster ao cluster HPC, ou se você adicionar o nó principal a um domínio do Active Directory existente.

    
## Etapas para criar o nó principal

Estar são as etapas gerais para criar uma VM do Azure para o nó principal do Pacote HPC usando o modelo de implantação do Gerenciador de Recursos no portal do Azure.


1. Se você quiser criar uma nova floresta do Active Directory no Azure com VMs do controlador de domínio separadas, uma opção será usar um [o modelo do Gerenciador de Recursos](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para uma prova de conceito simples da implantação, é suficiente omitir isso e configurar a própria VM do nó principal como um controlador de domínio, conforme descrito em uma etapa posterior.
    
2. No [portal do Azure](https://portal.azure.com), selecione a imagem do Pacote HPC 2012 R2 no Azure Marketplace. Para fazer isso, clique em **Novo** e pesquise o **Pacote HPC** no Marketplace. Selecione **Pacote HPC 2012 R2 no Windows Server 2012 R2**.

3. Na página do **Pacote HPC 2012 R2 no Windows Server 2012 R2**, selecione o modelo de implantação do **Gerenciador de Recursos** e clique em **Criar**.

    ![Imagem do Pacote HPC][marketplace]

4. Use o portal para definir as configurações e criar a VM. Para obter etapas detalhadas, siga o tutorial [Criar uma máquina virtual do Windows no Portal do Azure](virtual-machines-windows-tutorial.md). Para uma primeira implantação você geralmente pode aceitar várias configurações padrão ou recomendadas.

    **Considerações sobre a rede virtual**

   * Se você criar uma nova rede virtual nas **Configurações**, especifique um intervalo de endereços de rede privada, como 10.0.0.0/16, e um intervalo de endereços de sub-rede, como 10.0.0.0/24.
    
4. Depois de criar a máquina virtual e a quando a máquina virtual estiver em execução, [conecte-se à VM](virtual-machines-log-on-windows-server-preview.md). 

5. Adicione a máquina virtual a uma floresta de domínio existente ou crie uma nova floresta de domínio na VM.

    **Considerações sobre o domínio do Active Directory**

    * Se você criou a VM em uma rede virtual do Azure com uma floresta de domínio existente, use o Gerenciador do Servidor padrão ou ferramentas do Windows PowerShell para associá-la à floresta de domínio. Em seguida, reinicie.

    * Se criou a VM em uma VNet sem uma floresta de domínio existente, promova-a a controlador de domínio. Para fazer isso, use ferramentas padrão do Gerenciador do Servidor ou do Windows PowerShell para instalar e configurar a função de Serviços de Domínio do Active Directory. Para obter etapas detalhadas, consulte [Instalar uma nova floresta do Active Directory no Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Depois que a máquina virtual estiver em execução e fizer parte de uma floresta do Active Directory, inicie os serviços do Pacote HPC no nó principal. Para fazer isso:

    a. Conecte-se à VM com uma conta de domínio que seja membro do grupo Administradores local. Por exemplo, você poderia usar a conta de administrador que foi configurada quando você criou a VM do nó principal.

    b. Para uma configuração de nó principal padrão, inicie o Windows PowerShell como administrador e digite o seguinte:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Pode levar vários minutos para que os serviços do Pacote HPC iniciem.

    Para opções de configuração adicionais de nó principal, digite `get-help HPCHNPrepare.ps1`.


## Próximas etapas

* Agora, você pode trabalhar com o nó principal do cluster do Pacote HPC. Por exemplo, inicie o Gerenciador de Cluster do HPC e conclua a [Lista de Tarefas Pendentes de Implantação](https://technet.microsoft.com/library/jj884141.aspx).
* Adicione [nós de disparo do Azure](virtual-machines-hpcpack-cluster-node-burst.md) em um serviço de nuvem para aumentar a capacidade de computação sob demanda do cluster. 

* Tente executar uma carga de trabalho de teste no cluster. Para obter um exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144) do Pacote HPC.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0211_2016-->
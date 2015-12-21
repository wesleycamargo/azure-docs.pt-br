<properties
 pageTitle="Configurar um cluster de RDMA do Windows para executar aplicativos MPI | Microsoft Azure"
 description="Saiba como criar um cluster do Windows HPC Pack com VMs de tamanho A8 ou A9 para usar a rede RDMA do Azure e executar aplicativos MPI."
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
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Configurar um cluster do Windows RDMA com o HPC Pack e instâncias A8 e A9 para executar aplicativos MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Ele mostra como configurar um cluster do Windows RDMA no Azure com o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [instâncias de computação intensiva tamanho A8 e A9](virtual-machines-a8-a9-a10-a11-specs.md) para execução de aplicativos MPI (Message Passing Interface) paralelos. Quando você configura instâncias de tamanho A8 e A9 baseadas no Windows Server para executar uma implementação de MPI com suporte, os aplicativos MPI se comunicam de modo eficiente por uma rede de baixa latência e alta taxa de transferência baseada na tecnologia RDMA (acesso remoto direto à memória).

>[AZURE.NOTE]Atualmente, o Windows RDMA do Azure tem suporte com aplicativos MPI que usam a interface Microsoft Network Direct para se comunicar entre as instâncias A8 e A9.
>
> O Azure também fornece instâncias de computação intensiva A10 e A11, com recursos de processamento idênticos às instâncias A8 e A9, mas sem uma conexão com uma rede de back-end RDMA. Para executar cargas de trabalho MPI no Azure, você geralmente terá melhor desempenho com as instâncias A8 e A9.

Se quiser executar cargas de trabalho MPI em VMs Linux que acessam a rede RDMA do Azure, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md).

## Opções de implantação do cluster do Windows HPC
O Microsoft HPC Pack é uma ferramenta recomendada para criar clusters de HPC com base no Windows Server no Azure. Quando usado com instâncias A8 e A9, o HPC Pack fornece um meio eficiente de executar aplicativos MPI baseados em Windows que acessam a rede RDMA no Azure. O HPC Pack inclui um ambiente de tempo de execução para a implementação por parte da Microsoft da Message Passing Interface para Windows.

Este artigo apresenta dois cenários para implantar instâncias A8 e A9 clusterizadas com o Microsoft HPC Pack.

* Cenário 1. Implantar instâncias de função de trabalho de computação intensiva (PaaS)

* Cenário 2: Implantar nós de computação em VMs de computação intensiva (IaaS)

## Pré-requisitos

* **Examine as [informações básicas e considerações](virtual-machines-a8-a9-a10-a11-specs.md)** sobre as instâncias de computação intensiva


* **Assinatura do Azure**: se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).


* **Cota de núcleos**: talvez seja necessário aumentar a cota de núcleos para implantar um cluster de VMs A8 ou A9. Por exemplo, você precisará de pelo menos 128 núcleos se quiser implantar instâncias A8 e A9 com o HPC Pack. Para aumentar uma cota, [abra uma solicitação de atendimento ao cliente online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

## Cenário 1. Implantar instâncias de função de trabalho de computação intensiva (PaaS)


Em um cluster existente do HPC Pack, adicione recursos de computação extras nas instâncias de função de trabalho do Azure (nós do Azure) em execução em um serviço de nuvem (PaaS). Esse recurso, também chamado de "disparo no Azure" do HPC Pack, dá suporte a uma variedade de tamanhos para as instâncias de função de trabalho. Para usar as instâncias de computação intensiva, bastará especificar um tamanho de A8 ou A9 ao adicionar nós do Azure.

Veja a seguir as etapas para disparar as instâncias A8 ou A9 do Azure de um cluster existente (geralmente locais). Use procedimentos semelhantes para adicionar instâncias de função de trabalho a um nó de cabeçalho do HPC Pack que é implantado em uma VM do Azure.

>[AZURE.NOTE]Para ver um tutorial de como disparar no Azure com o HPC Pack, consulte [Configurar um cluster híbrido com o HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Observe que as considerações nas etapas abaixo se aplicam especificamente aos nós de tamanho A8 e A9 do Azure.

![Disparo no Azure][burst]



4. **Implantar e configurar um nó de cabeçalho do HPC Pack 2012 R2**

    Baixe o pacote de instalação mais recente do HPC Pack do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Para ver requisitos e instruções de como se preparar para uma implantação de disparo do Azure, consulte [Guia de Introdução ao HPC Pack](https://technet.microsoft.com/library/jj884144.aspx) e [Disparar no Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

5. **Configurar um certificado de gerenciamento na assinatura do Azure**

    Configure um certificado para proteger a conexão entre o nó de cabeçalho e o Azure. Para ver opções e procedimentos, consulte [Cenários para configurar o certificado de gerenciamento do Azure para HPC Pack](http://technet.microsoft.com/library/gg481759.aspx).

6. **Criar um novo serviço de nuvem e uma conta de armazenamento**

    Use o portal clássico do Azure para criar um serviço de nuvem e uma conta de armazenamento para a implantação em uma região em que as instâncias de computação intensiva estejam disponíveis. (Não associe o serviço de nuvem e a conta de armazenamento a um grupo de afinidades existente usado para outras implantações).

7. **Criar um modelo de nó do Azure**

    Use o Assistente para Criar Modelo de Nó no Gerenciador de Cluster do HPC. Para ver as etapas, consulte [Criar um modelo de nó do Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) em "Etapas para implantar nós do Azure com o Microsoft HPC Pack".

    Para fazer testes iniciais, recomendamos configurar uma política de disponibilidade manual no modelo.

8. **Adicionar nós ao cluster**

    Use o Assistente para Adicionar Nós no Gerenciador de Cluster do HPC. Para saber mais, consulte [Adicionar nós do Azure ao cluster do Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Ao especificar o tamanho dos nós, selecione A8 ou A9.

9. **Iniciar (provisionar) os nós e ativá-los para executar trabalhos**

    Selecione os nós e use a ação **Iniciar** no Gerenciador de Cluster do HPC. Quando o provisionamento estiver concluído, selecione os nós e use a ação **Colocar Online** no Gerenciador de Cluster do HPC. Os nós estão prontos para executar trabalhos.

10. **Enviar trabalhos para o cluster**

    Use ferramentas de envio de trabalho do HPC Pack para executar trabalhos de cluster. Consulte [Microsoft HPC Pack: gerenciamento de trabalhos](http://technet.microsoft.com/library/jj899585.aspx).

11. **Parar (desprovisionar) os nós**

    Quando você terminar de executar trabalhos, coloque os nós offline e use a ação **Parar** no Gerenciador de Cluster do HPC.


### Considerações adicionais

* **Nós de proxy**: em cada disparo na implantação do Azure com as instâncias de computação intensiva, o HPC Pack implanta automaticamente um mínimo de 2 instâncias A8 adicionais como nós de proxy, além das instâncias de função de trabalho do Azure que você especifica. Para saber mais, consulte [Definir o número de nós de proxy do Azure](https://technet.microsoft.com/library/jj899633.aspx). Os nós de proxy usam núcleos que são alocados para a assinatura e incorrem em cobranças juntamente com as instâncias de função de trabalho do Azure.

* **Rede virtual**: atualmente, o HPC Pack dá suporte à configuração de uma VPN ponto a site para implantações de PaaS.


## Cenário 2: Implantar nós de computação em VMs de computação intensiva (IaaS)

Neste cenário, você implanta o nó de cabeçalho do HPC Pack e os nós de computação de cluster em VMs que ingressaram em um domínio do Active Directory em uma rede virtual do Azure. O [script de implantação de IaaS do HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) automatiza a maior parte desse processo e fornece opções de implantação flexíveis, incluindo a capacidade de especificar o tamanho da VM A8 ou A9 para os nós de cluster. As etapas a seguir orientam você a usar esse método de implantação automatizado. Se preferir, implante o cluster com o modelo de implantação do Gerenciador de Recursos usando um modelo de início rápido do Azure. Para implantações de teste, também é possível implantar manualmente o domínio do Active Directory, a VM do nó de cabeçalho, VMs do nó de computação e outras partes da infraestrutura de cluster do HPC Pack no Azure. Consulte [Opções de cluster do HPC com Microsoft HPC Pack no Azure](virtual-machines-hpcpack-cluster-options.md).

![Cluster em VMs do Azure][iaas]


1. **Criar um nó de cabeçalho de cluster e VM de nó de computação executando o script de implantação de IaaS do HPC Pack em um computador cliente**

 Baixe o pacote Script de Implantação de IaaS do HPC do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

 Para preparar o computador cliente, criar o arquivo de configuração de script e executar o script, consulte [Criar um cluster de HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md). Para implantar os nós de computação A8 e A9, consulte as considerações adicionais, mais adiante neste artigo.

2. **Colocar os nós de computação online para executar trabalhos**

    Selecione os nós e use a ação **Colocar Online** no Gerenciador de Cluster do HPC. Os nós estão prontos para executar trabalhos.

3. **Enviar trabalhos para o cluster**

    Conecte-se ao nó de cabeçalho para enviar trabalhos ou configure um computador local para fazer isso. Para saber mais, consulte [Enviar trabalhos para um cluster do HPC no Azure](virtual-machines-hpcpack-cluster-submit-jobs.md).

4. **Colocar os nós offline e pará-los/desalocá-los**

    Quando você terminar a execução de trabalhos, coloque os nós offline no Gerenciador de Cluster do HPC. Em seguida, use as ferramentas de gerenciamento do Azure para desativá-los.

### Considerações adicionais para execução do script de implantação do cluster
* **Rede virtual**: especifique uma nova rede virtual em uma região em que as instâncias A8 e A9 estejam disponíveis.


* **Sistema operacional Windows Server**: para dar suporte à conectividade RDMA, especifique um sistema operacional Windows Server 2012 R2 ou Windows Server 2012 para as VMs do nó de computação A8 ou A9.


* **Serviços de nuvem**: é recomendável implantar o nó de cabeçalho em um serviço de nuvem e seus nós de computação A8 e A9 em outro serviço de nuvem.


* **Tamanho do nó de cabeçalho**: ao adicionar VMs de nó de computação no tamanho A8 ou A9, considere um tamanho de, pelo menos, A4 (Extra Grande) para o nó de cabeçalho.


* **Extensão HpcVmDrivers**: o script de implantação instala o agente de VM do Azure e a extensão HpcVmDrivers automaticamente quando você implanta os nós de computação A8 ou A9 com um sistema operacional Windows Server. A extensão HpcVmDrivers instala drivers nas VMs do nó de computação para que elas possam se conectar à rede RDMA. Consulte [Recursos e extensões de VM do Azure](virtual-machines-extensions-features.md).


* **Configuração de rede do cluster**: o script de implantação configura automaticamente o cluster do HPC Pack na topologia 5 (todos os nós na rede corporativa). Essa topologia é necessária para todas as implantações de cluster do HPC Pack em VMs, incluindo aquelas com nós de computação tamanho A8 ou A9. Não altere a topologia de rede do cluster posteriormente.

## Executar aplicativos MPI nas instâncias A8 e A9

### Exemplo: executar mpipingpong em um cluster do HPC Pack

Para verificar uma implantação do HPC Pack das instâncias de computação intensiva, execute o comando **mpipingpong** do HPC Pack no cluster. **mpipingpong** envia pacotes de dados entre nós emparelhados repetidamente para calcular medições de latência e taxa de transferência, bem como estatísticas para a rede de aplicativo compatível com RDMA. Este exemplo mostra um padrão típico para executar um trabalho MPI (nesse caso, **mpipingpong**) usando o comando **mpiexec** do cluster.

Este exemplo presume que você adicionou nós do Azure em uma configuração de "disparo no Azure" ([cenário 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS)) neste artigo). Se você implantou o HPC Pack em um cluster de VMs do Azure, será preciso modificar a sintaxe de comando a fim de especificar um grupo de nós diferente e definir variáveis de ambiente adicionais de modo a direcionar o tráfego de rede para a rede RDMA.


Para executar mpipingpong no cluster:


1. No nó de cabeçalho ou em um computador cliente configurado corretamente, abra uma janela de comando.

2. Para estimar a latência entre pares de nós em uma implantação de disparo do Azure de 4 nós, digite o seguinte comando a fim de enviar um trabalho para executar mpipingpong com um pacote pequeno e um grande número de iterações:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    O comando retorna a ID do trabalho enviado.

    Se você tiver implantado o cluster do HPC Pack implantado em VMs do Azure, especifique um grupo de nós que contenha VMs do nó de computação implantadas em um único serviço de nuvem e modifique o comando **mpiexec** como se segue:

  ```
  job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
  ```

3. Quando o trabalho for concluído, para exibir a saída (nesse caso, a saída da tarefa 1 do trabalho), digite o seguinte

    ```
    task view <JobID>.1
    ```

    em que &lt;*JobID*&gt; é a ID do trabalho enviado.

    A saída incluirá resultados de latência semelhantes ao seguinte.

    ![Latência ping pong][pingpong1]

4. Para estimar a taxa de transferência entre pares dos nós de disparo do Azure, digite o comando a seguir a fim de enviar um trabalho para execução de **mpipingpong** com um pacote grande e um pequeno número de iterações:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    O comando retorna a ID do trabalho enviado.

    Em um cluster do HPC Pack implantado em VMs do Azure, modifique o comando, como observado na etapa 2.

5. Quando o trabalho for concluído, para exibir a saída (nesse caso, a saída da tarefa 1 do trabalho), digite o seguinte:

    ```
    task view &lt;JobID&gt;.1
    ```
    
  A saída incluirá resultados de taxa de transferência semelhantes ao seguinte.

  ![Taxa de transferência de ping pong][pingpong2]


### Considerações de aplicativos MPI


Veja a seguir as considerações para execução de aplicativos MPI nas instâncias do Azure. Algumas se aplicam somente à implantações de nós do Azure (instâncias de função de trabalho adicionadas em uma configuração de "disparo no Azure").

* As instâncias de função de trabalho em um serviço de nuvem são reprovisionadas periodicamente sem aviso pelo Azure (por exemplo, para manutenção do sistema ou no caso de uma instância falhar). Se uma instância for reprovisionada enquanto estiver executando um trabalho MPI, ela perderá todos os seus dados e retornará para o estado em que estava quando foi implantada pela primeira vez, o que pode fazer com que o trabalho MPI falhe. Quanto mais nós forem usados para um único trabalho MPI e quanto maior for a execução do trabalho, maior a probabilidade de que uma das instâncias seja reprovisionada enquanto um trabalho estiver em execução. Você também deve levar isso em consideração se designar um único nó na implantação como um servidor de arquivos.


* Não é preciso usar as instâncias A8 e A9 para executar trabalhos MPI no Azure. Você pode usar qualquer tamanho de instância que tenha o suporte do HPC Pack. No entanto, as instâncias A8 e A9 são recomendadas para executar trabalhos MPI de escala relativamente grande que sejam sensíveis à latência e à largura de banda da rede que conecta os nós. Se você usar instâncias diferentes de A8 e A9 para executar trabalhos MPI sensíveis à latência e à largura de banda, é recomendável executar trabalhos pequenos, nos quais uma única tarefa é executada em apenas alguns nós.

* Os aplicativos implantados em instâncias do Azure estão sujeitos aos termos de licenciamento associados ao aplicativo. Verifique com o fornecedor de qualquer aplicativo comercial quanto ao licenciamento ou outras restrições de execução na nuvem. Nem todos os fornecedores oferecem licenciamento pré-pago.


* As instâncias do Azure precisam de outras configurações para acessar os nós, os compartilhamentos e os servidores de licenças locais. Por exemplo, para permitir que os nós do Azure acessem um servidor de licenças local, você pode configurar uma rede virtual site a site do Azure.


* Para executar aplicativos MPI em instâncias do Azure, registre cada aplicativo MPI no Firewall do Windows nas instâncias executando o comando **hpcfwutil**. Isso permite que as comunicações MPI ocorram em uma porta atribuída dinamicamente pelo firewall.

    >[AZURE.NOTE]Para implantações de disparo no Azure, você também pode configurar um comando de exceção de firewall para ser executado automaticamente em todos os nós novos do Azure que são adicionados ao cluster. Depois de executar o comando **hpcfwutil** e verificar se seu aplicativo funciona, adicione o comando ao script de inicialização dos seus nós do Azure. Para saber mais, consulte [Usar um script de inicialização para nós do Azure] (https://technet.microsoft.com/library/jj899632(v=ws.10).aspx).



* O HPC Pack usa a variável de ambiente do cluster CCP\_MPI\_NETMASK para especificar um intervalo de endereços aceitáveis para comunicação MPI. A partir do HPC Pack 2012 R2, a variável de ambiente do cluster CCP\_MPI\_NETMASK afeta apenas a comunicação MPI entre nós de computação de cluster que ingressaram no domínio (no local ou em VMs do Azure). A variável é ignorada pelos nós adicionados em uma configuração de disparo no Azure.


* Os trabalhos MPI não podem ser executados nas instâncias do Azure que estiverem implantadas em serviços de nuvem diferentes (por exemplo, em implantações de disparo no Azure com diferentes modelos de nó ou em nós de computação da VM do Azure implantados em vários serviços de nuvem). Se você tiver várias implantações de nó do Azure que são iniciadas com modelos de nó diferentes, o trabalho MPI deverá ser executado apenas em um conjunto de nós do Azure.


* Quando você adicionar nós do Azure ao cluster e colocá-los online, o Serviço do Agendador de Trabalho do HPC tentará imediatamente iniciar trabalhos nos nós. Se apenas uma parte da carga de trabalho puder ser executada no Azure, não se esqueça de atualizar ou criar modelos de trabalho para definir quais tipos de trabalho podem ser executados no Azure. Por exemplo, para garantir que trabalhos enviados com um modelo de trabalho sejam executados somente em nós do Azure, adicione a propriedade Grupos de Nós ao modelo de trabalho e selecione AzureNodes como o valor necessário. Para criar grupos personalizados para seus nós do Azure, é possível usar o cmdlet Add-HpcGroup HPC PowerShell.


## Próximas etapas

* Se quiser executar aplicativos MPI do Linux que acessam a rede RDMA do Azure, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=AcomDC_1210_2015-->
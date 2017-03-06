---
title: "Criar uma conexão VPN Site a Site entre duas redes virtuais em diferentes ambientes Azure Stack PoC | Microsoft Docs"
description: "Procedimento passo a passo que permite que um administrador de nuvem crie uma conexão VPN Site a Site entre dois ambientes POC de um nó em TP2."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732
ms.lasthandoff: 02/09/2017


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Criar uma conexão VPN Site a Site entre duas redes virtuais em diferentes ambientes Azure Stack PoC
## <a name="overview"></a>Visão geral
Este artigo mostra como criar uma Conexão de VPN Site a Site entre duas redes virtuais em dois ambientes POC (Prova de Conceito) do Azure Stack separados. Ao configurar as conexões, você verá como funcionam os gateways de VPN no Azure Stack.

> [!NOTE]
> Este documento se aplica especificamente à POC TP2 do Azure Stack.
> 
> 

### <a name="connection-diagram"></a>Diagrama de conexão
O seguinte diagrama mostra a aparência da configuração quando você terminar:

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir essa configuração, verifique se você tem os seguintes itens antes de começar:

* Dois Servidores que atendam aos requisitos de hardware de POC do Azure Stack definidos pelos [Pré-requisitos de Implantação do Azure Stack](azure-stack-deploy.md) e aos outros pré-requisitos definidos pelo documento.
* O Pacote de Implantação 2 de Visualização Técnica do Azure Stack.

## <a name="deploy-the-poc-environments"></a>Implantar os ambientes de POC
Você implantará os dois ambientes POC do Azure Stack para concluir essa configuração.

* Para cada POC que implantar, você poderá seguir as instruções de implantação detalhadas no artigo [Implantar POC do Azure Stack](azure-stack-run-powershell-script.md).
  Cada ambiente POC neste documento é mencionado genericamente como *POC1* e *POC2*.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Configurar cotas para computação, rede e armazenamento
Primeiro, você precisa configurar *Cotas* para computação, rede e armazenamento. Esses serviços podem ser associados a um *Plano*e uma *Oferta* que os locatários podem assinar.

> [!NOTE]
> Você precisa executar essas etapas para cada ambiente POC do Azure Stack.
> 
> 

A criação de cotas de serviços foi alterada em relação a TP1. As etapas para criar cotas em TP2 podem ser encontradas em <http://aka.ms/mas-create-quotas>. Você pode aceitar os padrões para todas as configurações de cota para esse exercício.

## <a name="create-a-plan-and-offer"></a>Criar um plano e uma oferta
Os [planos](azure-stack-key-features.md) são agrupamentos de um ou mais serviços. Como provedor, é possível criar planos para oferecer a seus locatários. Por sua vez, os locatários assinam suas ofertas para usar os planos e os serviços incluídos nelas.

> [!NOTE]
> Você precisa executar essas etapas para cada ambiente POC do Azure Stack.
> 
> 

1. Primeiro, crie um Plano. Para fazer isso, você pode seguir as etapas do artigo online [Criar um Plano](azure-stack-create-plan.md).
2. Crie uma oferta seguindo as etapas descritas em [Criar uma oferta no Azure Stack](azure-stack-create-offer.md).
3. Faça logon no portal como um administrador de locatário e [assine a Oferta que você criou](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>Criar os recursos de rede na POC 1
Agora você criará realmente os recursos de que precisa para definir sua configuração. As etapas a seguir ilustram o que você fará. Estas instruções mostram como criar recursos usando o portal, mas o mesmo pode ser feito usando o PowerShell.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Fazer logon como um locatário
Um administrador de serviços pode fazer logon como um locatário para testar os planos, as ofertas e as assinaturas que seus locatários podem usar. Se você ainda não tem uma, [crie uma conta de locatário](azure-stack-add-new-user-aad.md) antes de fazer logon.

### <a name="create-the-virtual-network--vm-subnet"></a>Criar a rede virtual e a sub-rede de VM
1. Faça logon usando uma conta de locatário.
2. No portal do Azure, clique em **Novo**.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Selecione **Rede** no menu do Marketplace.
4. Clique no item **Rede virtual** no menu.
5. Clique em **Criar** próximo à parte inferior da folha de descrição do recurso. Insira os valores a seguir nos campos apropriados de acordo com essa tabela.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | Nome |vnet-01 |
   | Espaço de endereço |10.0.10.0/23 |
   | Nome da sub-rede |subnet-01 |
   | Intervalo de endereços da sub-rede |10.0.10.0/24 |
6. Você deverá ver a assinatura que criou anteriormente populada no campo **Assinatura**.
7. Para o grupo de recursos, você pode criar um novo grupo de recursos ou, se já tiver um, selecione **Usar existente**.
8. Verifique o local padrão.
9. Clique em **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de Rede Virtual que você acabou de criar (Vnet-01) no painel.
2. Na folha Configurações, selecione **Sub-redes**.
3. Clique em **Sub-rede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.
   Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.
5. No campo **Intervalo de endereços**, digite **10.0.11.0/24**.
6. Clique em **Criar** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o Gateway de Rede Virtual
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Gateway de Rede Virtual** na lista de recursos de rede.
4. Examine a descrição e clique em **Criar**.
5. No campo **Nome**, digite **GW1**.
6. Clique no item **Rede virtual** para escolher uma rede virtual.
   Selecione **Vnet-01** na lista.
7. Clique no item de menu **Endereço IP público**. Quando a folha **Escolher o endereço IP público** for aberta, clique em **Criar novo**.
8. No campo **Nome**, digite **GW1-PiP** e clique em **OK**.
9. O **Tipo de gateway** deve ter **VPN** selecionado por padrão. Mantenha essa configuração.
10. O **Tipo de VPN** deve ter **Baseado em rota** selecionado por padrão.
    Mantenha essa configuração.
11. Verifique se **Assinatura** e **Local** estão corretos. Você poderá fixar o recurso no Painel, se desejar. Clique em **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o Gateway de Rede Local
A implementação de um *gateway de rede local* nessa implantação de avaliação do Azure Stack é um pouco diferente do que uma implantação real do Azure.

Assim como no Azure, você tem o conceito de um gateway de rede local. No entanto, em uma implantação do Azure, um gateway de rede local representa um dispositivo físico local (no locatário) que você usa para se conectar a um gateway de rede virtual no Azure. Porém, nessa implantação de avaliação do Azure Stack, ambas as extremidades da conexão são gateways de rede virtual!

Uma maneira de entender isso de forma mais genérica que o recurso de Gateway de Rede Local sempre deve indicar o gateway remoto na outra extremidade de conexão. Devido ao modo como a POC foi criada, você precisa fornecer o endereço IP do adaptador de rede externo na VM NAT da outra POC como o Endereço IP Público do Gateway de Rede Local. Em seguida, você criará mapeamentos NAT na VM NAT para garantir que ambas as extremidades sejam conectadas corretamente.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Faça logon no computador físico do Azure Stack para POC2.
2. Pressione a [tecla Windows] + R para abrir o menu **Executar**, digite **mstsc** e pressione Enter.
3. No campo **Computador**, digite o nome **MAS-BGPNAT01** e clique em  **Conectar**.
4. Clique no menu Iniciar, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como Administrador**.
5. Digite **ipconfig/all**.
6. Localize o Adaptador Ethernet que está conectado à rede local e anote o endereço IPv4 associado a esse adaptador. No ambiente de exemplo, é **10.16.167.195**, mas o seu será diferente.
7. Registre esse endereço. Isso é o que você usará como Endereço IP Público do recurso de Gateway de Rede Local que criará em POC1.

### <a name="create-the-local-network-gateway-resource"></a>Criar o Recurso de Gateway de Rede Local
1. Faça logon no computador físico do Azure Stack para POC1.
2. No campo **Computador**, digite o nome **MAS-CON01** e clique em **Conectar**.
3. No portal do Azure, clique em **Novo**.
   
4. Selecione **Rede** no menu do Marketplace.
5. Selecione **gateway de rede local** na lista de recursos.
6. No campo **Nome**, digite **POC2-GW**.
7. Você ainda não sabe o endereço IP do outro gateway, mas não há problema, pois pode voltar e alterá-lo mais tarde. Por enquanto, digite **10.16.167.195** no campo **Endereço IP**.
8. No campo **Espaço de Endereço**, digite o espaço de endereço da Vnet que você criará em POC2. Será **10.0.20.0/23**. Portanto, digite esse valor.
9. Verifique se **Assinatura**, **Grupo de Recursos** e **Local** estão todos corretos e clique em **Criar**.

### <a name="create-the-connection"></a>Criar a Conexão
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Conexão** na lista de recursos.
4. Na folha de configurações **Básica**, escolha **Site a site (IPSec)** como **Tipo de conexão**.
5. Selecione **Assinatura**, **Grupo de Recursos** e **Local** e clique em **OK**.
6. Na folha **Configurações**, escolha o **Gateway de Rede Virtual** (**GW1**) criado anteriormente.
7. Escolha o **Gateway de Rede Local** (**POC2 GW**) criado anteriormente.
8. No campo **Nome da Conexão**, digite **POC1-POC2**.
9. No campo **Chave Compartilhada (PSK)**, digite **12345** e clique em **OK**.

### <a name="create-a-vm"></a>Criar uma máquina virtual
Para validar dados que trafegam por meio da Conexão VPN, você precisa de VMs para enviar e receber dados em cada POC. Crie agora uma máquina virtual em POC1 e coloque-a na sub-rede de VM na rede virtual.

1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Máquinas Virtuais** no menu do Marketplace.
3. Na lista de imagens de máquinas virtuais, selecione a imagem **Windows Server 2012 R2 Datacenter**.
4. Na folha **Fundamentos**, no campo **Nome**, digite **VM01**.
5. Digite um nome de usuário e uma senha válidos. Você usará essa conta para fazer logon na VM depois que ela for criada.
6. Forneça uma **Assinatura**, um **Grupo de Recursos** e um **Local** e clique em **OK**.
7. Na folha **Tamanho**, escolha um tamanho de VM para essa instância e clique em **Selecionar**.
8. Na folha **Configurações**, você pode aceitar os padrões. Apenas verifique se a rede Virtual selecionada é **VNET-01** e a sub-rede está definida como **10.0.10.0/24**. Clique em **OK**.
9. Examine as configurações na folha **Resumo** e clique em **OK**.

## <a name="create-the-network-resources-in-poc-2"></a>Criar os recursos de rede na POC 2
### <a name="log-in-as-a-tenant"></a>Fazer logon como um locatário
Um administrador de serviços pode fazer logon como um locatário para testar os planos, as ofertas e as assinaturas que seus locatários podem usar. Se você ainda não tem uma, [crie uma conta de locatário](azure-stack-add-new-user-aad.md) antes de fazer logon.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM
1. Faça logon usando uma conta de locatário.
2. No portal do Azure, clique em **Novo**.
   
3. Selecione **Rede** no menu do Marketplace.
4. Clique em **Rede virtual** no menu.
5. Clique em **Criar** próximo à parte inferior da folha de descrição do recurso. Digite os valores a seguir para os campos apropriados listados na tabela a seguir.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | Nome |vnet-02 |
   | Espaço de endereço |10.0.20.0/23 |
   | Nome da sub-rede |subnet-02 |
   | Intervalo de endereços da sub-rede |10.0.20.0/24 |
6. Você deverá ver a Assinatura que criou anteriormente populada no campo **Assinatura**.
7. Para o grupo de recursos, você pode criar um novo grupo de recursos ou, se já tiver um, selecione **Usar existente**.
8. Verifique o **local** padrão. Se desejar, você poderá fixar a rede virtual no Painel para facilitar o acesso.
9. Clique em **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de rede Virtual criado (**Vnet&02;**) por meio do Painel.
2. Na folha **Configurações**, selecione **Sub-redes**.
3. Clique em **Sub-rede de gateway** para adicionar uma sub-rede de gateway à rede virtual.
   
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.
   Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.
5. No campo **Intervalo de endereços**, digite **10.0.20.0/24**.
6. Clique em **Criar** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o Gateway de Rede Virtual
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Gateway de Rede Virtual** na lista de recursos de rede.
4. Examine a descrição e clique em **Criar**.
5. No campo **Nome**, digite **GW2**.
6. Clique em **Rede virtual** para escolher uma rede virtual.
   Selecione **Vnet-02** na lista.
7. Clique em **Endereço IP público**. Quando a folha **Escolher o endereço IP público** for aberta, clique em **Criar novo**.
8. No campo **Nome**, digite **GW2-PiP** e clique em **OK**.
9. O **Tipo de gateway** deve ter **VPN** selecionado por padrão. Mantenha essa configuração.
10. O **Tipo de VPN** deve ter **Baseado em rota** selecionado por padrão.
    Mantenha essa configuração.
11. Verifique se **Assinatura** e **Local** estão corretos. Você poderá fixar o recurso no Painel, se desejar. Clique em **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o Gateway de Rede Local
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Faça logon no computador físico do Azure Stack para POC1.
2. Pressione e segure a [tecla Windows] + R para abrir o menu **Executar**, digite **mstsc** e pressione enter.
3. No campo **Computador**, digite o nome **MAS-BGPNAT01** e clique em  **Conectar**.
4. Clique no menu Iniciar, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como Administrador**.
5. Digite **ipconfig/all**.
6. Localize o adaptador Ethernet que está conectado à rede local e anote o endereço IPv4 associado a esse adaptador. No ambiente de exemplo, é **10.16.169.131**, mas o seu será diferentes.
7. Registre esse endereço. Isso é o que você usará posteriormente como Endereço IP Público do recurso de Gateway de Rede Local que criará em POC1.

#### <a name="create-the-local-network-gateway-resource"></a>Criar o Recurso de Gateway de Rede Local
1. Faça logon no computador físico do Azure Stack para POC2.
2. No campo **Computador**, digite o nome **MAS-CON01** e clique em **Conectar**.
3. No portal do Azure, clique em **Novo**.
   
4. Selecione **Rede** no menu do Marketplace.
5. Selecione **gateway de rede local** na lista de recursos.
6. No campo **Nome**, digite **POC1-GW**.
7. Agora você precisa do Endereço IP Público que registrou para o gateway de rede Virtual em POC1. Digite **10.16.169.131** no campo **Endereço IP**.
8. No campo **Espaço de endereço**, digite o espaço de endereço de **Vnet-01** de POC1 - **10.0.0.0/16**.
9. Verifique se **Assinatura**, **Grupo de Recursos** e **Local** estão todos corretos e clique em **Criar**.

## <a name="create-the-connection"></a>Criar a Conexão
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Conexão** na lista de recursos.
4. Na folha de configurações **Básica**, escolha **Site a site (IPSec)** como **Tipo de conexão**.
5. Selecione **Assinatura**, **Grupo de Recursos** e **Local** e clique em **OK**.
6. Na folha **Configurações**, escolha o **Gateway de Rede Virtual** (**GW1**) criado anteriormente.
7. Escolha o **Gateway de Rede Local** (**POC1 GW**) criado anteriormente.
8. No campo **Nome da Conexão**, digite **POC2-POC1**.
9. No campo **Chave Compartilhada (PSK)**, digite **12345**. Se escolher um valor diferente, lembre-se de que ele deverá corresponder ao valor de chave compartilhada que você criou em POC1. Clique em **OK**.

## <a name="create-a-vm"></a>Criar uma máquina virtual
Crie agora uma máquina virtual em POC1 e coloque-a na sub-rede de VM na rede virtual.

1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Máquinas Virtuais** no menu do Marketplace.
3. Na lista de imagens de máquinas virtuais, selecione a imagem **Windows Server 2012 R2 Datacenter**.
4. Na folha **Fundamentos**, no campo **Nome**, digite **VM02**.
5. Digite um nome de usuário e uma senha válidos. Você usará essa conta para fazer logon na VM depois que ela for criada.
6. Forneça uma **Assinatura**, um **Grupo de Recursos** e um **Local** e clique em **OK**.
7. Na folha **Tamanho**, escolha um tamanho de VM para essa instância e clique em **Selecionar**.
8. Na folha Configurações, você pode aceitar os padrões. Apenas verifique se a rede virtual selecionada é **VNET-02** e a sub-rede está definida como **20.0.0.0/24**. Clique em **OK**.
9. Examine as configurações na folha **Resumo** e clique em **OK**.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Configurar a VM NAT em cada POC para percurso de gateway
Como a POC foi projetada para ser independente e isolada da rede na qual o host físico é implantado, a rede VIP "Externa" à qual os gateways estão conectados não é realmente externa, mas está oculta por trás de um roteador que está realizando NAT (Conversão de Endereços de Rede). O roteador é realmente uma VM do Windows Server (**MAS-BGPNAT01**) que executa a função de RRAS (Serviços de Roteamento e Acesso Remoto) na infraestrutura POC. Você deve configurar NAT na VM MAS-BGPNAT01 para permitir que a Conexão VPN Site a Site se conecte em ambas as extremidades. Para fazer isso, você deve criar um mapeamento de NAT estático que mapeie a interface externa na VM BGPNAT para o VIP do Pool de Gateway de Borda para as portas necessárias a uma Conexão VPN.

> [!NOTE]
> Essa configuração é necessária apenas para ambientes POC.
> 
> 

### <a name="configure-nat"></a>Configurar NAT
Você precisa seguir essas etapas em AMBOS os ambientes POC.

1. Faça logon no computador físico do Azure Stack para POC1.
2. Pressione e segure a [tecla Windows] + R para abrir o menu **Executar**, digite **mstsc** e pressione **Enter**.
3. No campo **Computador**, digite o nome **MAS-BGPNAT01** e clique em **Conectar**.
4. Clique no menu Iniciar, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como Administrador**.
5. Digite **ipconfig/all**.
6. Localize o Adaptador Ethernet que está conectado à rede local e anote o endereço IPv4 associado a esse adaptador. No ambiente de exemplo, é **10.16.169.131** (circulado em vermelho abaixo), mas o seu será diferente.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Digite o comando do PowerShell a seguir para designar o endereço NAT externo para as portas para autenticação IKE. Lembre-se de alterar o endereço IP para aquele que corresponde a seu ambiente.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. Em seguida, você cria um mapeamento NAT estático para mapear o endereço externo para o Endereço IP Público do Gateway e mapear a porta ISAKMP 500 para FASE 1 do túnel IPSEC.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> O parâmetro `-InternalAddress` aqui é o Endereço IP Público do Gateway de Rede Virtual que você criou anteriormente.  Para localizar esse endereço IP, examine as propriedades da folha de Gateway de Rede Virtual e encontre o valor do Endereço IP Público.       

9. Por fim, você deve configurar o percurso NAT que usa a porta 4500 para estabelecer com êxito o túnel IPEC completo em dispositivos NAT.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> O parâmetro `-InternalAddress` aqui é o Endereço IP Público do Gateway de Rede Virtual que você criou anteriormente.  Para localizar esse endereço IP, examine as propriedades da folha de Gateway de Rede Virtual e encontre o valor do Endereço IP Público.       

10. Repita as etapas de 1 a 9 em POC2.

## <a name="test-the-connection"></a>Testar a conexão
Agora que foi estabelecida a conexão Site a Site, você deve validar que pode fazer com que o tráfego flua através dela. Essa tarefa é simples, pois envolve apenas o logon em uma das VMs criadas em um dos ambientes POC e a execução de ping na VM criada no outro ambiente. Para garantir que você está enviando o tráfego através da conexão Site a Site, convém executar ping no endereço DIP (IP Direto) da VM na sub-rede remota, não no VIP. Para fazer isso, você precisa localizar e anotar o endereço na outra extremidade de conexão.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Faça logon na VM de locatário em POC1
1. Faça logon na máquina física do Azure Stack para POC1 e faça logon no Portal usando uma conta de locatário.
2. Clique em **Máquinas Virtuais** na barra de navegação à esquerda.
3. Localize **VM01** que você criou anteriormente na lista de VMs e clique nele.
4. Na folha da máquina virtual, clique em **Conectar**.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Abra um Prompt de comando de dentro da VM e digite **ipconfig/all**.
6. Localize o **endereço IPv4** na saída e anote-o. Esse é o endereço para o qual você executará ping de POC2. No ambiente de exemplo, o endereço é **10.0.10.4**, mas, em seu ambiente, pode ser diferente. No entanto, ele deve estar dentro da sub-rede **10.0.10.0/24** que foi criada anteriormente.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Faça logon na VM de locatário em POC2
1. Faça logon na máquina física no Azure Stack para POC2 e faça logon no portal usando uma conta de locatário.
2. Clique em **Máquinas Virtuais** na barra de navegação à esquerda.
3. Localize **VM02** que você criou anteriormente na lista de VMs e clique nele.
4. Na folha da máquina virtual, clique em **Conectar**.
   
5. Abra um Prompt de comando de dentro da VM e digite **ipconfig/all**.
6. Você deve ver um endereço IPv4 que esteja dentro de 10.0.20.0/24. No ambiente de exemplo, o endereço é 10.0.20.4, mas o seu pode ser diferente.
7. Agora, da VM em POC2, você deseja executar ping para a VM em POC1, através do túnel. Para fazer isso, você executa ping para o DIP que registrou na VM01.
   No ambiente de exemplo, é 10.0.10.4, mas não se esqueça de executar ping no endereço que você anotou no laboratório. Você verá um resultado parecido com este:
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. Uma resposta da VM remota indica um teste com êxito! Você pode fechar a janela Conexão de VM. Ou você pode tentar fazer algumas outras transferências de dados, como uma cópia de arquivo, para testar a conexão.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Exibindo estatísticas de transferência de dados com a conexão de gateway
Se você quer saber a quantidade de dados que estão passando pela conexão Site a Site, essas informações estão disponíveis na folha Conexão. Esse teste também é outra boa maneira de verificar se o ping que você acabou de enviar realmente atravessou a conexão VPN.

1. Enquanto ainda conectado à VM de locatário em POC2, faça logon na **Portal POC do Microsoft Azure Stack** usando sua conta de locatário.
2. Clique no item de menu **Procurar** e selecione **Conexões**.
3. Clique na conexão **POC2-POC1** na lista.
4. Na folha Conexão, você pode ver as estatísticas de **Dados de entrada** e **Dados de saída**. Na captura de tela a seguir, você vê que alguns números maiores do que aqueles que apenas um ping criará. Isso também é devido a algumas transferências de arquivos adicionais. Você deverá ver alguns valores diferentes de zero.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)



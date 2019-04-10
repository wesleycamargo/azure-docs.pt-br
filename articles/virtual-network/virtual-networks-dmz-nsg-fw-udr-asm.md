---
title: Exemplo de rede de perímetro – proteger redes com a rede de perímetro, consistindo de um firewall, UDR e NSGs | Microsoft Docs
description: Crie uma rede de perímetro (também conhecida como DMZ) com um firewall, roteamento definido pelo usuário (UDR) e grupos de segurança de rede (NSGs).
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426521"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Exemplo 3: Crie uma rede de perímetro para proteger as redes com um firewall, UDR e NSGs

[Voltar à página Práticas recomendadas de limite de segurança][HOME]

Neste exemplo, você cria uma rede de perímetro (também conhecido como DMZ, zona desmilitarizada e sub-rede filtrada). O exemplo implementa um firewall, quatro servidores Windows, roteamento definido pelo usuário (UDR), o encaminhamento de IP e grupos de segurança de rede (NSGs). Este artigo orienta você por meio de cada um dos comandos relevantes para fornecer uma compreensão mais profunda de cada etapa. A seção de cenário de tráfego também explica detalhadamente como o tráfego passa pelas camadas de defesa da rede de perímetro. Por fim, a seção de referências contém todo o código e instruções para criar este ambiente para que você possa testar e experimentar diversos cenários.

![Rede de perímetro bidirecional com NVA, NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente

Este exemplo usa uma assinatura que contém os seguintes componentes:

* Três serviços de nuvem: SecSvc001, FrontEnd001 e BackEnd001
* Uma rede virtual (CorpNetwork) com três sub-redes: SecNet, front-end e back-end
* Uma solução de virtualização de rede: um firewall, conectada à sub-rede SecNet
* Um servidor Windows que representa um servidor de aplicativos web: IIS01
* Dois servidores Windows que representam servidores de back-end do aplicativo: AppVM01, AppVM02
* Um servidor Windows que representa um servidor DNS: DNS01

O [seção referências](#references) contém um script do PowerShell que cria a maior parte do ambiente descrito aqui. Este artigo caso contrário, não fornece instruções detalhadas para a criação das máquinas virtuais (VMs) e redes virtuais.

Para compilar o ambiente:

1. Salve o arquivo XML de configuração de rede incluído na [seção referência](#references). Você precisará atualizá-lo com nomes, localização e endereços IP para coincidir com o cenário dado.
1. Atualize as variáveis de usuário no script completo para corresponder ao seu ambiente específico (por exemplo, assinaturas, nomes de serviço e assim por diante).
1. Execute o script do PowerShell.

> [!NOTE]
> A região especificada no script do PowerShell deve corresponder a região especificada no arquivo XML de configuração de rede.

Depois que o script é executado com êxito, execute as seguintes etapas:

1. Configure as regras de firewall. Consulte a [regras de firewall](#firewall-rules) seção.
1. Opcionalmente, use os dois scripts na seção de referências para configurar um aplicativo web no servidor web e no servidor de aplicativo para permitir que o teste de configuração desta DMZ.

## <a name="user-defined-routing"></a>Roteamento definido pelo usuário

Por padrão, as rotas do sistema a seguir são definidas como:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal é sempre os prefixos de endereço definido para essa rede virtual específica. Por exemplo, ele será alterado de rede virtual a rede virtual, dependendo de como cada rede virtual específica é definida. As rotas do sistema restantes são estáticas e padrão conforme mostrado.

Como prioridade, as rotas são processadas por meio do método de correspondência de prefixo mais longo (LPM). Portanto, a rota mais específica na tabela aplica-se a um determinado endereço de destino.

Portanto, o tráfego destinado a um servidor como DNS01 (10.0.2.4) no local (10.0.0.0/16) de rede é roteado através da rede virtual por causa da rota 10.0.0.0/16.  Para 10.0.2.4, a rota de 10.0.0.0/16 é a rota mais específica. Essa regra se aplica mesmo que o 10.0.0.0/8/8 e 0.0.0.0/0 também podem ser aplicáveis. Eles são menos específicos, no entanto, para que elas não afetam esse tráfego. O tráfego para 10.0.2.4 tem a rede virtual local como seu próximo salto, portanto, é roteada ao destino.

Por exemplo, a rota 10.0.0.0/16 não se aplica ao tráfego que é destinado para 10.1.1.1. A rota de sistema 10.0.0.0/8 é o mais específico para que o tráfego é descartado ou "buraco negro" como o próximo salto é Null.

Se o destino não se aplica a qualquer um dos prefixos Null ou prefixos da VNETLocal, o tráfego segue a rota menos específica (0.0.0.0/0). Ele é roteado para a internet como o próximo salto e sair de borda de internet do Azure.

Se houver dois prefixos idênticos na tabela de rotas, a ordem de preferência baseia-se no atributo de origem da rota:

1. VirtualAppliance: Uma rota definida pelo usuário adicionada manualmente à tabela.
1. VPNGateway: Uma rota dinâmica (BGP quando usada com redes híbridas) adicionadas por um protocolo de rede dinâmica. Essas rotas podem mudar ao longo do tempo, conforme o protocolo dinâmico reflete automaticamente as alterações na rede emparelhada.
1. Padrão: As rotas do sistema, a rede virtual local e as entradas estáticas, como mostrado na tabela de rotas acima.

> [!NOTE]
> Agora você pode usar o roteamento definido pelo usuário (UDR) com ExpressRoute e Gateways de VPN para forçar o tráfego de entrada e saída entre locais seja roteado para um dispositivo de rede virtual (NVA).

### <a name="create-local-routes"></a>Criar rotas locais

Este exemplo usa duas tabelas de roteamento, uma para as sub-redes de front-end e back-end. Cada tabela é carregada com as rotas estáticas apropriadas para determinada sub-rede. Para fins deste exemplo, cada tabela tem três rotas:

1. Tráfego de sub-rede local sem próximo salto definido. Essa rota permite que o tráfego de sub-rede local ignore o firewall.
2. Tráfego de rede virtual com um próximo salto definido como firewall. Essa rota substitui a regra padrão que permite que o tráfego de rede virtual local seja roteado diretamente.
3. Todo o tráfego restante (0/0) com um próximo salto definido como o firewall.

Depois que as tabelas de roteamento são criadas, elas são associadas às respectivas sub-redes. A tabela de roteamento da sub-rede front-end deve parecer com:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

Este exemplo usa os comandos a seguir para criar a tabela de rotas, adicionar uma rota definida pelo usuário e, em seguida, associar a tabela de rotas a uma sub-rede. Itens que começam com `$`, tais como `$BESubnet`, são variáveis definidas pelo usuário do script na seção de referência.

1. Primeiro, crie a tabela de roteamento de base. O trecho de código a seguir cria a tabela para a sub-rede de back-end. O script completo também cria uma tabela correspondente para a sub-rede de front-end.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Depois de criar a tabela de rotas, você pode adicionar rotas específicas definidas pelo usuário. O trecho de código a seguir especifica que todo o tráfego (0.0.0.0/0) é roteado por meio do dispositivo virtual. Uma variável `$VMIP[0]` é usado para passar o endereço IP atribuído quando o dispositivo virtual foi criado anteriormente no script. O script completo também cria uma regra correspondente na tabela de front-end.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. A entrada de rota anterior substitui a rota "0.0.0.0/0" padrão, mas a regra padrão do 10.0.0.0/16 ainda permite o tráfego na rede virtual para rotear diretamente para o destino e não para o dispositivo de rede virtual. Para corrigir esse comportamento, você precisa adicionar a regra a seguir:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Neste ponto, você precisa fazer uma escolha. As duas regras anteriores rotear todo o tráfego para o firewall para avaliação, incluindo o tráfego dentro de uma única sub-rede. Você pode desejar esse comportamento. Se você não fizer isso, no entanto, você pode permitir o tráfego de sub-rede seja roteado localmente sem o envolvimento do firewall. Adicionar um terceiro, a regra específica que roteia diretamente qualquer endereço destinado para a sub-rede local (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Por fim, depois que a tabela de roteamento é criada e preenchida com rotas definidas pelo usuário, você precisa associar a tabela a uma sub-rede. O trecho de código a seguir associa a tabela para a sub-rede de back-end. O script completo também associa a tabela de rotas de front-end para a sub-rede de front-end.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Encaminhamento IP

Encaminhamento de IP é um recurso complementar para UDR. Essa configuração em um dispositivo virtual permite que ele receba o tráfego não endereçado para a solução e, em seguida, encaminhar esse tráfego para seu destino final.

Por exemplo, se o tráfego de AppVM01 fizer uma solicitação para o servidor DNS01, o UDR roteia o tráfego para o firewall. Com o encaminhamento IP habilitado, o tráfego com o destino DNS01 (10.0.2.4) é aceito pelo dispositivo de firewall (10.0.0.4) e, em seguida, encaminhado para seu destino final (10.0.2.4). Sem o encaminhamento IP habilitado no firewall, o tráfego não é aceito pelo dispositivo, embora a tabela de rota tenha o firewall como o próximo salto.

> [!IMPORTANT]
> Lembre-se de habilitar o encaminhamento de IP em conjunto com o roteamento definido pelo usuário.

Encaminhamento de IP pode ser habilitado com um único comando no momento da criação de VM. Você chama a instância VM que é o seu dispositivo virtual de firewall e habilitar o encaminhamento de IP. Lembre-se de que os itens em vermelho que começam com `$`, como `$VMName[0]`, são variáveis definidas pelo usuário do script na seção de referência deste documento. O zero entre colchetes, `[0]`, representa a primeira VM na matriz de VMs. Para o script de exemplo funcionar sem modificação, a primeira máquina virtual (VM 0) deve ser o firewall. No script completo, o trecho de código relevante é agrupado com os comandos UDR perto do fim.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Grupos de segurança de rede

Neste exemplo, você cria um grupo de segurança de rede (NSG) e, em seguida, carregá-lo com uma única regra. O exemplo, em seguida, associa o NSG apenas às sub-redes de front-end e back-end (não a SecNet). A regra que você carregar para o NSG é da seguinte maneira:

* Todo tráfego (todas as portas) da internet para a rede virtual inteira (todas as sub-redes) é negado

Embora NSGs sejam usados neste exemplo, sua finalidade principal é como uma segunda camada de defesa contra erros de configuração manual. Você deseja bloquear todo tráfego de entrada da internet para as sub-redes de front-end ou back-end. O tráfego deve fluir somente pela sub-rede SecNet para o firewall, após o qual apenas o tráfego adequado é roteado para as sub-redes de front-end ou back-end. Além disso, as regras UDR rotear todo o tráfego que chega a sub-redes de front-end ou back-end para o firewall. O firewall vê-lo como um fluxo assimétrico e descarta o tráfego de saída.

Três camadas de segurança protegem as sub-redes de front-end e back-end:

1. Nenhum ponto de extremidade aberto nos serviços de nuvem FrontEnd001 e BackEnd001
1. Os NSGs negam o tráfego da internet
1. O firewall descarta o tráfego assimétrico

Um ponto interessante sobre o NSG neste exemplo é que ele contém apenas uma regra, mostrada abaixo. Esta regra nega o tráfego de internet para toda a rede virtual, incluindo a sub-rede de segurança.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Porque o NSG está associado apenas às sub-redes de front-end e back-end, a regra não é aplicada para tráfego de entrada para a sub-rede Security. Como resultado, o tráfego flui para a sub-rede de segurança.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Regras de firewall

Você deve criar regras de encaminhamento no firewall. Como o firewall bloqueia ou encaminha todo o tráfego de entrada, saído e rede intrafamiliares virtuais, você precisa de várias regras de firewall. Além disso, o firewall tem que processar todo o tráfego de entrada para o endereço IP público do serviço de segurança (em portas diferentes). Para evitar retrabalho mais tarde, siga a prática recomendada por fluxos de lógica antes de configurar as sub-redes e as regras de firewall de diagramação. A figura a seguir é uma exibição lógica das regras de firewall para este exemplo:

![Exibição lógica das regras de firewall][2]

> [!NOTE]
> Portas de gerenciamento variam dependendo do dispositivo de rede virtual. Este exemplo mostra um Barracuda NextGen Firewall que usa as portas 22, 801 e 807. Consulte a documentação do fornecedor de dispositivo para localizar as portas exatas para gerenciar o dispositivo.

### <a name="logical-rule-description"></a>Descrição da regra lógica

Diagrama lógico acima não mostra a sub-rede de segurança porque o firewall é o único recurso nessa sub-rede. Este diagrama mostra as regras de firewall, como eles logicamente permitem ou negam o tráfego flui, mas não o real roteado caminho. Além disso, as portas externas selecionadas para o tráfego de protocolo de área de trabalho remota (RDP) são portas intervalos maior (8014 – 8026) escolhidas para facilitar a leitura para se alinhar com os dois últimos octetos dos endereços IP locais. Por exemplo, o endereço do servidor local 10.0.1.4 está associado à porta externa 8014. Você pode, no entanto, usar todas as portas não conflitantes superiores.

Você precisará os seguintes tipos de regras para este exemplo:

* Regras externas para o tráfego de entrada:
  1. Regra de firewall de gerenciamento: permite que o tráfego passe para as portas de gerenciamento de dispositivo de rede virtual.
  2. Regras de RDP para cada servidor do windows: permite o gerenciamento de servidores individuais via RDP.  Dependendo dos recursos do seu dispositivo de rede virtual, você poderá agrupar as regras em um.
  3. Regras de tráfego do aplicativo: um para o tráfego da web de front-end e outro para o tráfego de back-end (por exemplo, o servidor web para a camada de dados). A configuração dessas regras depende de arquitetura de rede e o tráfego de fluxos.

     * A primeira regra permite o tráfego de aplicativo real alcançar o servidor de aplicativos. Ao contrário das regras de segurança, gerenciamento e assim por diante, as regras de aplicativo permitem que usuários ou serviços externos acessem os aplicativos. Este exemplo tem um único servidor web na porta 80, que permite que uma regra de firewall único aplicativo redirecionar o tráfego destinado a um endereço IP externo em vez disso, rotear para o endereço IP interno do servidor web. A sessão de tráfego redirecionado é remapeada pelo NAT para o servidor interno.
     * A segunda regra de tráfego do aplicativo é a regra de back-end para permitir que o servidor web para usar qualquer porta para rotear o tráfego para o servidor AppVM01, mas não para o servidor AppVM02.

* Regras internas para tráfego de rede intrafamiliares virtuais:
  1. Regra de internet de saída para: permite o tráfego de qualquer rede passe para as redes selecionadas. Essa regra é geralmente um padrão no firewall, mas em um estado desabilitado. Habilite essa regra para este exemplo.
  2. Regra DNS: permite somente tráfego DNS (porta 53) passe para o servidor DNS. Para esse ambiente, a maior parte do tráfego do front-end para o back-end está bloqueado. Essa regra permite especificamente DNS de qualquer sub-rede local.
  3. Regra sub-rede para sub-rede: permite que qualquer servidor na sub-rede de back-end para se conectar a qualquer servidor na sub-rede de front-end, mas não o contrário.

* Regra à prova de falhas para o tráfego que não atende a qualquer um dos critérios acima:
  1. Regra Negar todo o tráfego: sempre a última regra em termos de prioridade. Se o fluxo de tráfego falhar na correspondência a todas as regras anteriores, esta regra bloqueará. É uma regra padrão. Como normalmente é ativada, nenhuma modificação é necessária.

> [!TIP]
> Na segunda regra de tráfego do aplicativo, qualquer porta é permitida para manter esse exemplo simples. Em um cenário real, você deve usar a porta específica e intervalos de endereços para reduzir a superfície de ataque dessa regra.


> [!IMPORTANT]
> Depois de criar as regras, é importante que você analise a prioridade de cada regra para garantir que o tráfego é permitido ou negado como desejado. Neste exemplo, as regras estão em ordem de prioridade. É fácil obter bloqueado pelo firewall se as regras são incorretamente ordenadas. Certifique-se de definir a regra de firewall de gerenciamento como a maior prioridade absoluta.

### <a name="rule-prerequisites"></a>Pré-requisitos de regra

Pontos de extremidade públicos são necessários para a máquina virtual que executa o firewall. Esses pontos de extremidade públicos devem estar abertos para que o firewall pode processar o tráfego. Há três tipos de tráfego neste exemplo:

1. Tráfego de gerenciamento para controlar o firewall e regras de firewall
1. Tráfego de RDP para controlar os windows servers
1. Tráfego de aplicativo

Os tipos de tráfego aparecem na metade superior da firewall regras diagrama lógico acima.

> [!IMPORTANT]
> Lembre-se de que *todos os* tráfego chega por meio do firewall. Área de trabalho remota para o servidor IIS01, você precisa para se conectar ao firewall na porta 8014 e, em seguida, permitir que o firewall roteie a solicitação RDP internamente para a porta RDP do IIS01. O portal do Azure **Connect** botão não funcionará porque não há nenhum caminho direto de RDP para IIS01 no que diz como o portal pode ver. Todas as conexões da internet são para o serviço de segurança e uma porta (por exemplo, secscv001.cloudapp.net:xxxx). O diagrama acima para o mapeamento de porta externa e interna IP e porta de referência.

Você pode abrir um ponto de extremidade no momento da criação da VM ou após a compilação. O script de exemplo e o seguinte trecho de código aberto um ponto de extremidade depois que a VM é criada.

Lembre-se de que os itens que começam com `$`, tais como `$VMName[$i]`, são variáveis definidas pelo usuário do script na seção de referência. O `[$i]` representa o número de matriz de uma VM específica em uma matriz de VMs.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Embora ele claramente não é mostrado aqui devido a variáveis, você só deve abrir os pontos de extremidade no serviço de nuvem de segurança. Essa precaução ajuda a garantir que o firewall lida com todo o tráfego de entrada, se tiver roteadas, remapeados pelo NAT ou descartado.

Instale um cliente de gerenciamento em um PC para gerenciar o firewall e crie as configurações necessárias. Para obter detalhes sobre como gerenciar o firewall ou outro NVA, consulte a documentação do fornecedor. O restante desta seção, bem como a **criação de regras de Firewall** seção descrevem a configuração do firewall. Use o cliente de gerenciamento do fornecedor, e não o portal do Azure ou PowerShell.

Vá para [administrador do NG Barracuda](https://techlib.barracuda.com/NG61/NGAdmin) para baixar o cliente de gerenciamento e saiba como se conectar ao firewall Barracuda.

Depois que você está conectado ao firewall, defina objetos de serviço e de rede antes de criar as regras de firewall. Essas duas classes de objeto de pré-requisito podem tornar a criação de regras.

Neste exemplo, defina três objetos de rede nomeada para cada:

* Subrede front-end
* Subrede de back-end
* Endereço IP do servidor DNS

Para criar uma rede nomeada no painel de cliente do administrador do NG Barracuda:

1. Vá para o **guia de configuração**.
1. Selecione **Ruleset** na **configuração operacional** seção
1. Selecione **redes** sob o **Firewall objetos** menu.
1. Selecione **New** na **Editar redes** menu.
1. Crie o objeto de rede, adicionando o nome e o prefixo:

   ![Criar um objeto de rede de front-ed][3]

As etapas anteriores criam uma rede nomeada para a sub-rede de front-end. Crie um objeto semelhante para a sub-rede de back-end também. Agora as sub-redes podem ser mais facilmente referenciadas pelo nome nas regras de firewall.

Para o objeto de servidor DNS:

![Criar um objeto de servidor DNS][4]

Essa referência de endereço IP única é usada em uma regra DNS posteriormente neste documento.

A classe do objeto inclui objetos de serviços, que representam as portas de conexão de RDP para cada servidor. O objeto de serviço RDP existente está associado à porta RDP padrão, 3389. Assim, você pode criar novos serviços para permitir o tráfego de portas externas (8014 – 8026). Você também pode adicionar as novas portas para o serviço RDP existente. No entanto, para facilitar a demonstração, você pode fazer uma regra individual para cada servidor. Para criar uma nova regra RDP para um servidor no painel de cliente do administrador do NG Barracuda:

1. Vá para o **guia de configuração**.
1. Selecione **Ruleset** na **configuração operacional** seção.
1. Selecione **Services** sob o **Firewall objetos** menu.
1. Role para baixo a lista de serviços e selecione **RDP**.
1. Com o botão direito e selecione Copiar, clique com botão direito e selecione Colar.
1. Agora é um objeto de serviço RDP-Copy1 que pode ser editado. Clique com botão direito **RDP-Copy1** e selecione **editar**.
1. O **Editar objeto de serviço** janela aparece como mostrado aqui:

   ![Cópia de regra RDP padrão][5]

1. Edite os valores para representar o serviço RDP para um servidor específico. Para a AppVM01, a regra RDP padrão deve ser modificada para refletir um novo serviço **nome**, **descrição**e a porta RDP externa usada no diagrama da Figura 8. Tenha em mente que as portas são alteradas do padrão de 3389 RDP para a porta externa para esse servidor específico. Por exemplo, a porta externa para AppVM01 é 8025. A regra do serviço modificado é mostrada aqui:

   ![Regra de AppVM01][6]

Repita esse processo para criar serviços RDP para os servidores restantes: AppVM02, DNS01 e IIS01. Esses serviços Verifique as regras na próxima seção mais óbvias e mais simples de criar.

> [!NOTE]
> Um serviço RDP para o firewall não é necessária porque a VM do firewall é uma imagem baseada em Linux, portanto, o SSH é usado na porta 22 para o gerenciamento de VM em vez de RDP. Além disso, a porta 22 e duas outras portas são permitidas para conectividade de gerenciamento. Consulte a **regra de Firewall de gerenciamento** na próxima seção.

### <a name="firewall-rules-creation"></a>Criação de regras de firewall

Há três tipos de regras de firewall usadas neste exemplo, todos com ícones diferentes:

A regra de redirecionamento do aplicativo: ![Ícone de Redirecionamento do Aplicativo][7]

A regra NAT de destino: ![Ícone de NAT de Destino][8]

A regra de passagem: ![Ícone de Passagem][9]

Para obter mais informações sobre essas regras podem ser encontradas no site do Barracuda.

Para criar as regras a seguir ou verificar as regras padrão existentes:

1. No painel de cliente do administrador do NG Barracuda, vá para o **configuração** guia.
1. No **configuração operacional** seção, selecione **Ruleset**.
1. O **regras principais** grade mostra existente Active Directory e desativado regras neste firewall. Selecionar o verde **+** no canto superior direito para criar uma nova regra. Se seu firewall está bloqueado para que as alterações, você verá um botão marcado **bloqueio** e não é possível criar ou editar regras. Selecione o **bloqueio** botão Desbloquear o conjunto de regras e permitir a edição. Com uma regra que deseja editar e selecione o botão direito **Editar regra**.

Depois de criar ou modificar quaisquer regras, enviar por push para o firewall e ativá-los. Caso contrário, as alterações de regra não terão efeito. O processo de envio por push e ativação é descrito em [ativação de regra](#rule-activation).

Aqui estão as especificidades de cada regra necessária para concluir este exemplo:

* **Regra de firewall de gerenciamento**: Essa regra de redirecionamento do aplicativo permite que o tráfego passe para as portas de gerenciamento de dispositivo de rede virtual, neste exemplo, um NextGen Firewall Barracuda. As portas de gerenciamento são 801 807 e, opcionalmente, 22. As portas internas e externas são os mesmos, nenhuma conversão de porta. Essa regra é chamada SETUP-MGMT-ACCESS. Ele é uma regra padrão e habilitado por padrão no Barracuda NextGen Firewall, a versão 6.1.
  
    ![Regra de Gerenciamento de Firewall][10]

  > [!TIP]
  > É o espaço de endereço de origem nesta regra **qualquer**. Se os intervalos de endereços IP de gerenciamento forem conhecidos, a redução desse escopo também reduz a superfície de ataque às portas de gerenciamento.

* **Regras RDP**:  Essas regras NAT de destino permitem o gerenciamento dos servidores individuais via RDP. Os campos críticos para essas regras são:
  * Código-fonte. Para permitir RDP de qualquer lugar, use a referência **qualquer** no campo de origem.
  * Serviço. Use o objeto de serviço do RDP criado anteriormente: **AppVM01 RDP**. As portas externas redirecionam para o endereço IP do servidor local e a porta do RDP padrão 3386. Essa regra específica destina-se ao acesso RDP para AppVM01.
  * Destino. Use a porta local no firewall: **IP Local do DHCP 1** ou **eth0** se você estiver usando IPs estáticos. O número ordinal (eth0, eth1 e assim por diante) podem ser diferente se o seu dispositivo de rede tenha várias interfaces locais. O firewall usa essa porta para o envio e pode ser o mesmo que a porta de recebimento. O destino roteado real está no **lista de destino** campo.
  * Redirecionamento. Configure para informar a solução de virtualização para onde redirecionar esse tráfego. O redirecionamento mais simples é colocar o IP no campo lista de destino. Você também pode especificar a porta e, em seguida, NAT redireciona a porta e o endereço IP. Se você não especificar uma porta, o dispositivo virtual usa a porta de destino na solicitação de entrada.

    ![Regra RDP do firewall][11]

    Crie quatro regras RDP:

    | Nome da Regra | Servidor | Serviço | Lista de destino |
    | --- | --- | --- | --- |
    | RDP-para-IIS01 |IIS01 |RDP de IIS01 |10.0.1.4:3389 |
    | RDP-para-DNS01 |DNS01 |RDP de DNS01 |10.0.2.4:3389 |
    | RDP-para-AppVM01 |AppVM01 |RDP de AppVM01 |10.0.2.5:3389 |
    | RDP-para-AppVM02 |AppVM02 |RDP de AppVm02 |10.0.2.6:3389 |

  > [!TIP]
  > Restringir o escopo dos campos de origem e de serviço reduz a superfície de ataque. Use o escopo mais limitado que permite que a funcionalidade.

* **As regras de aplicativo**: Há duas regras de tráfego do aplicativo. Uma é para o tráfego da web de front-end. O outro aborda o tráfego de back-end, como o servidor web para a camada de dados. Essas regras dependem de arquitetura de rede e fluxos de tráfego.
  
  * A regra de front-end para o tráfego da web:
  
    ![Regra Web do firewall][12]
  
    Essa regra de NAT de destino permite que o tráfego de aplicativo real alcançar o servidor de aplicativos. Ao contrário das regras de segurança, gerenciamento e tabelas, as regras de aplicativo permitem que usuários ou serviços externos acessem os aplicativos. Este exemplo tem um único servidor web na porta 80, que permite que uma regra de firewall único aplicativo redirecionar o tráfego destinado a um endereço IP externo em vez disso, rotear para o endereço IP interno do servidor web. A sessão de tráfego redirecionado é remapeada pelo NAT para o servidor interno.

    > [!NOTE]
    > Não há nenhuma porta atribuída na **lista de destino** campo. Portanto, a porta de entrada 80 (ou 443 para o serviço selecionado) é usada no redirecionamento do servidor web. Se o servidor web estiver escutando em uma porta diferente, como 8080, você pode atualizar o campo da lista de destino para 10.0.1.4: 8080 para permitir o redirecionamento de porta também.
  
  * A regra de back-end permite que o servidor web para se comunicar com o servidor AppVM01, mas não com AppVM02 via **qualquer** serviço:
  
    ![Regra da AppVM01 do firewall][13]
  
    Essa regra aprovar permite que qualquer servidor IIS na sub-rede front-end para alcançar a AppVM01 (10.0.2.5) em qualquer porta usando qualquer protocolo para que os dados podem ser acessados pelo aplicativo web.
  
    Nesta captura de tela `<explicit-dest>` é usado em de **destino** campo para significar 10.0.2.5 como o destino. Você pode especificar o endereço IP explicitamente, conforme mostrado na captura de tela. Você também pode usar um objeto de rede nomeado, como nos pré-requisitos para o servidor DNS. O administrador do firewall pode escolher qual método usar. Para adicionar 10.0.2.5 como um destino explícito, clique duas vezes na primeira linha em branco em `<explicit-dest>` e insira o endereço na caixa de diálogo que é aberta.
  
    Com essa regra aprovar, nenhuma NAT será necessária porque ela está tratando o tráfego interno. Você pode definir as **método de Conexão** para `No SNAT`.
  
    > [!NOTE]
    > A rede de origem nesta regra é qualquer recurso na sub-rede front-end se houver apenas um. Se sua arquitetura Especifica um número conhecido de servidores web, você pode criar um recurso de objeto de rede para ser mais específico daqueles endereços IP exatos em vez de toda a sub-rede de front-end.

    > [!TIP]
    > Esta regra usa o serviço **qualquer** para facilitar o aplicativo de exemplo para instalação e uso. Ele permite que o ICMPv4 (ping) em uma única regra. No entanto, para reduzir o ataque de superfície por esse limite, é recomendável restringir os serviços de portas e protocolos para o mínimo possível que permitem que a operação do aplicativo.

    > [!TIP]
    > Embora essa regra de exemplo usa `<explicit-dest>` referência, você deve usar uma abordagem consistente em toda a configuração de firewall. É recomendável usar um objeto de rede nomeado para facilitar a legibilidade e a capacidade de suporte. O `<explicit-dest>` mostrada aqui é apenas para mostrar um método alternativo de referência. Não é geralmente recomendável, especialmente para configurações complexas.

* **Regra de saída para a internet**: Essa regra aprovar permite que o tráfego de qualquer rede origem passe para as redes de destino selecionadas. O firewall NextGen Barracuda normalmente tem essa regra de "on" por padrão, mas em um estado desabilitado. Clique duas vezes em que essa regra para acessar o **ativar regra** comando. Modifique a regra mostrada na captura de tela para adicionar os objetos de rede para sub-redes de back-end e front-end para o atributo de origem dessa regra. Você criou esses objetos de rede na seção pré-requisito deste artigo.
  
    ![Regra de saída do firewall][14]

* **Regra DNS**: Essa regra aprovar permite que somente o tráfego DNS (porta 53) passe para o servidor DNS. Para esse ambiente, a maioria do tráfego do front-end para back-end está bloqueado para que essa regra permite especificamente o tráfego DNS.
  
    ![Regra DNS do firewall][15]
  
    > [!NOTE]
    > O **método de Conexão** é definido como `No SNAT` porque esta regra é para o IP interno do tráfego de endereço IP interno e nenhum novo roteamento por meio de NAT é necessário.

* **Regra sub-rede para sub-rede**: Essa regra de passagem de padrão ativada e modificada para permitir que qualquer servidor na sub-rede de back-end para se conectar a qualquer servidor na sub-rede de front-end. Essa regra coves somente tráfego interno para que o **método de Conexão** pode ser definido como `No SNAT`.

    ![Regra entre Redes Virtuais do firewall][16]
  
    > [!NOTE]
    > O **bidirecionais** caixa de seleção não está selecionada aqui para que esta regra aplica-se em uma única direção. Uma conexão pode ser iniciado da sub-rede de back-end para a rede de front-end, mas não o contrário. Se essa caixa de seleção foram selecionada, essa regra permitirá tráfego bidirecional, o que especificamos como indesejável no nosso diagrama lógico.

* **Regra Negar todo o tráfego**: Essa regra deve ser sempre a última regra em termos de prioridade. Se o fluxo de tráfego não corresponde a nenhuma das regras anteriores, essa regra faz com que ela seja interrompida. A regra normalmente está ativada por padrão, portanto, nenhuma modificação é necessária.
  
    ![Regra Negar do firewall][17]

> [!IMPORTANT]
> Depois que todas as regras anteriores forem criadas, examine a prioridade de cada regra para garantir que o tráfego é permitido ou negado como desejado. Neste exemplo, as regras são listadas na ordem em que elas devem aparecer na grade de principal do cliente de gerenciamento do Barracuda das regras de encaminhamento.

## <a name="rule-activation"></a>Ativação de regra

Depois de modificar a conjunto de regras para atender às especificações do diagrama lógico, você precisará carregar o conjunto de regras do firewall e ativá-lo.

![Ativação de regra de firewall][18]

Procure no canto superior direito da janela de cliente de gerenciamento e selecione **enviar alterações** para carregar as regras modificadas para o firewall. Selecione **Ativar**.

Quando você ativa o conjunto de regras de firewall, esse ambiente de exemplo foi concluído.

## <a name="traffic-scenarios"></a>Cenários de tráfego

> [!IMPORTANT]
> Lembre-se de que *todos os* tráfego chega por meio do firewall. Área de trabalho remota para o servidor IIS01, você precisa para se conectar ao firewall na porta 8014 e, em seguida, permitir que o firewall roteie a solicitação RDP internamente para a porta RDP do IIS01. O portal do Azure **Connect** botão não funcionará porque não há nenhum caminho direto de RDP para IIS01 no que diz como o portal pode ver. Todas as conexões da internet são para o serviço de segurança e uma porta (por exemplo, secscv001.cloudapp.net:xxxx). O diagrama acima para o mapeamento de porta externa e interna IP e porta de referência.

Nestes cenários, as seguintes regras de firewall devem estar funcionando:

1. Gerenciamento de firewall (Gerenc. FW)
2. RDP para IIS01
3. RDP para DNS01
4. RDP para AppVM01
5. RDP para AppVM02
6. Tráfego de aplicativo para a Web
7. Tráfego de aplicativo para AppVM01
8. Saída para a internet
9. Front-end para DNS01
10. Tráfego entre sub-redes (somente back-end para front-end)
11. Negar Tudo

Seu conjunto de regras de firewall real provavelmente exigirão mais regras que aqueles neste exemplo. Eles provavelmente têm números de prioridade diferentes também. Você deve consultar essa lista e números associados para sua prioridade relativa entre si. Por exemplo, a regra "RDP para IIS01" pode ser regra número 5 no firewall real, mas, desde que seu abaixo de "gerenciamento do Firewall" e acima de "RDP para DNS01" de regra, o conjunto se alinha com a intenção desta lista. Essa lista também ajuda a simplificar as instruções para cenários que seguem. Por exemplo, "regra de Firewall 9 (DNS)." Lembre-se de que as quatro regras RDP são coletivamente chamadas de "as regras RDP" quando o cenário de tráfego está relacionado ao RDP.

Lembre-se também de que os grupos de segurança de rede (NSGs) estão em vigor para tráfego de internet de entrada nas sub-redes de front-end e back-end.

### <a name="allowed-internet-to-web-server"></a>(Permitido) Internet para servidor web

1. Um usuário da internet solicita uma página HTTP de SecSvc001.CloudApp.Net (serviço de nuvem da internet).
1. O serviço de nuvem passa o tráfego por meio de um ponto de extremidade aberto na porta 80 para a interface do firewall em 10.0.0.4: 80.
1. Nenhum NSG é atribuído à sub-rede de segurança, portanto, as regras NSG do sistema permitem o tráfego para o firewall.
1. O tráfego atinge um endereço IP interno do firewall (10.0.1.4).
1. O firewall executa o processamento da regra:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regras de firewall 2-5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Regra de firewall 6 (aplicativo: Web) se aplicam. O tráfego é permitido. Firewall redireciona o tráfego por meio de NAT para 10.0.1.4 (IIS01).
1. A sub-rede de front-end executa o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (bloquear internet). O firewall redirecionado esse tráfego por meio de NAT para que o endereço de origem agora é o firewall. Como o firewall está na sub-rede Security e é exibido como o tráfego local para o NSG da sub-rede front-end, o tráfego é permitido. Mover para a próxima regra.
   1. Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, por isso esse tráfego é permitido. Pare o processamento da regra de NSG.
1. O IIS01 está escutando o tráfego da web. Ele recebe essa solicitação e começa a processar a solicitação.
1. O IIS01 tenta iniciar uma sessão FTP para AppVM01 na sub-rede de back-end.
1. A rota UDR na sub-rede front-end transforma o firewall o próximo salto.
1. Não há nenhuma regra de saída na sub-rede de front-end para que o tráfego é permitido.
1. O firewall inicia o processamento de regras:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regras de firewall 2-5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Regra de firewall 6 (aplicativo: Não se aplica a Web). Mover para a próxima regra.
   1. Regra de firewall 7 (aplicativo: back-end) que se aplicam. O tráfego é permitido. O firewall encaminha o tráfego para 10.0.2.5 (AppVM01).
1. A sub-rede de back-end executa o processamento da regra de entrada:
    1. Não se aplica a regra NSG 1 (bloquear internet). Mover para a próxima regra.
    1. Regras NSG padrão permitem o tráfego de sub-rede para sub-rede. O tráfego é permitido. Pare o processamento da regra de NSG.
1. AppVM01 recebe a solicitação, inicia a sessão e responde.
1. A rota UDR na sub-rede de back-end transforma o firewall o próximo salto.
1. Não há nenhuma regra NSG de saída na sub-rede de back-end para que a resposta é permitida.
1. Porque ele está retornando o tráfego em uma sessão estabelecida, o firewall passa a resposta de volta para o servidor web (IIS01).
1. Subrede front-end executa o processamento da regra de entrada:
    1. Não se aplica a regra NSG 1 (bloquear internet). Mover para a próxima regra.
    1. As regras NSG padrão permitem que o tráfego de sub-rede para sub-rede para que esse tráfego é permitido. Pare o processamento da regra de NSG.
1. O servidor IIS recebe a resposta e conclui a transação com AppVM01. Em seguida, o servidor conclui a criação da resposta HTTP e envia ao solicitante.
1. Não há nenhuma regra NSG de saída na sub-rede front-end para que a resposta é permitida.
1. A resposta HTTP chega ao firewall. Porque é uma resposta a uma sessão NAT estabelecida, o firewall o aceita.
1. O firewall redireciona a resposta de volta para o usuário da internet.
1. Não há nenhuma regra NSG de saída ou saltos UDR na sub-rede de front-end para que a resposta é permitida. O usuário da internet recebe a página da web solicitada.

### <a name="allowed-internet-rdp-to-back-end"></a>(Permitido) Internet RDP para back-end

1. Um administrador do servidor na internet solicita uma sessão RDP para AppVM01 via SecSvc001.CloudApp.Net:8025. 8025 é o número de porta atribuída ao usuário para a regra de firewall 4 (RDP AppVM01).
1. O serviço de nuvem passa o tráfego por meio do ponto de extremidade aberto na porta 8025 para interface do firewall em 10.0.0.4: 8025.
1. Nenhum NSG é atribuído à sub-rede de segurança, portanto, as regras NSG do sistema permitirem o tráfego para o firewall.
1. O firewall executa o processamento da regra:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 2 (IIS RDP) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 3 (DNS01 RDP) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 4 (RDP AppVM01) se aplicam para que o tráfego é permitido. O firewall redireciona-lo por meio de NAT para 10.0.2.5: 3386 (porta RDP em AppVM01).
1. A sub-rede de back-end executa o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (bloquear internet). O firewall redirecionado esse tráfego por meio de NAT para que o endereço de origem agora é o firewall, que está na sub-rede Security. Ele é visto pelo NSG da sub-rede de back-end como tráfego local e é permitido. Mover para a próxima regra.
   1. Regras NSG padrão permitem o tráfego de sub-rede para sub-rede, por isso esse tráfego é permitido. Pare o processamento da regra de NSG.
1. AppVM01 está ouvindo o tráfego de RDP e responde.
1. Não há nenhuma regra NSG de saída para que se aplicam as regras padrão. Tráfego de retorno é permitido.
1. UDR roteia o tráfego de saída para o firewall como o próximo salto.
1. Porque ele está retornando o tráfego em uma sessão estabelecida, o firewall passa a resposta de volta para o usuário da internet.
1. Sessão RDP está habilitado.
1. O AppVM01 solicita a senha do nome de usuário.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do servidor Web no servidor DNS

1. Servidor Web IIS01 solicita um alimentação de dados em http\:\/\/www.data.gov, mas precisa resolver o endereço.
1. A configuração de rede para as listas de rede virtual DNS01 (10.0.2.4 na sub-rede de back-end) que o servidor DNS primário. IIS01, envia a solicitação de DNS para DNS01.
1. UDR roteia o tráfego de saída para o firewall como o próximo salto.
1. Nenhuma regra NSG de saída é associada à sub-rede de front-end. O tráfego é permitido.
1. Firewall executa o processamento da regra:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regra de firewall de 2 a 5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Não se aplicam a regras de firewall, 6 e 7 (regras de aplicativo). Mover para a próxima regra.
   1. Não se aplica a regra de Firewall 8 (para a internet). Mover para a próxima regra.
   1. Aplicar a regra de firewall 9 (DNS). O tráfego é permitido. Firewall encaminha o tráfego para 10.0.2.4 (DNS01).
1. A sub-rede de back-end executa o processamento da regra de entrada:
   1. Não se aplica a regra NSG 1 (bloquear internet). Mover para a próxima regra.
   1. Regras NSG padrão permitem o tráfego de sub-rede para sub-rede. O tráfego é permitido. Pare o processamento da regra de NSG.
1. O servidor DNS recebe a solicitação.
1. O servidor DNS não tem o endereço armazenado em cache e solicita que um servidor DNS raiz na internet.
1. UDR roteia o tráfego de saída para o firewall como o próximo salto.
1. Há nenhuma regra NSG de saída na sub-rede de back-end para o tráfego é permitido.
1. Firewall executa o processamento da regra:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regra de firewall de 2 a 5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Não se aplicam a regras de firewall, 6 e 7 (regras de aplicativo). Mover para a próxima regra.
   1. Aplicar a regra de Firewall 8 (para a internet). O tráfego é permitido. A sessão será reencaminhada via SNAT para o servidor DNS de raiz na internet.
1. O DNS da internet responde do servidor. Esta sessão foi iniciada do firewall para que a resposta será aceita pelo firewall.
1. Esta sessão já foi estabelecida para que o firewall encaminha a resposta para o servidor que está iniciando, DNS01.
1. A sub-rede de back-end executa o processamento da regra de entrada:
    1. Não se aplica a regra NSG 1 (bloquear internet). Mover para a próxima regra.
    1. Regras NSG padrão permitem o tráfego de sub-rede para sub-rede para esse tráfego é permitido. Pare o processamento da regra de NSG.
1. O servidor DNS recebe e armazena em cache a resposta e, em seguida, responde à solicitação inicial para IIS01.
1. A rota UDR na sub-rede de back-end transforma o firewall o próximo salto.
1. Nenhuma regra NSG de saída existe na sub-rede de back-end para que o tráfego é permitido.
1. Esta sessão já foi estabelecida no firewall para que o firewall redireciona a resposta de volta ao servidor do IIS.
1. A sub-rede de front-end executa o processamento da regra de entrada:
    1. Não há nenhuma regra NSG para tráfego de entrada da sub-rede de back-end para a sub-rede de front-end para que nenhuma das regras de NSG se aplica.
    1. A regra padrão do sistema permite o tráfego entre sub-redes. O tráfego é permitido.
1. O IIS01 recebe a resposta do DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Permitido) Servidor de back-end para o servidor front-end

1. Um administrador fez logon na AppVM02 via RDP solicita um arquivo diretamente do servidor IIS01 por meio do Explorador de arquivos do windows.
1. A rota UDR na sub-rede de back-end transforma o firewall o próximo salto.
1. Não há nenhuma regra NSG de saída na sub-rede de back-end para que a resposta é permitida.
1. O firewall executa o processamento da regra:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regra de firewall de 2 a 5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Não se aplicam a regras de firewall, 6 e 7 (regras de aplicativo). Mover para a próxima regra.
   1. Não se aplica a regra de Firewall 8 (para a internet). Mover para a próxima regra.
   1. Regra de firewall 9 (DNS) não se aplica. Mover para a próxima regra.
   1. Aplicar a regra de firewall 10 (entre: subredes). O tráfego é permitido. O firewall passa o tráfego para 10.0.1.4 (IIS01).
1. A sub-rede de front-end inicia o processamento da regra de entrada:
   1. Regra NSG 1 (bloquear internet) não se aplicam; vá para a próxima regra
   1. As regras NSG padrão permitem que o tráfego de sub-rede para sub-rede para que o tráfego é permitido. Pare o processamento da regra de NSG.
1. Supondo que a devida autenticação e autorização, o IIS01 aceita a solicitação e responde.
1. A rota UDR na sub-rede de front-end transforma o firewall o próximo salto.
1. Não há nenhuma regra NSG de saída na sub-rede front-end para que a resposta é permitida.
1. Esta sessão já existe no firewall, portanto, essa resposta é permitida. O firewall retorna a resposta a AppVM02.
1. A sub-rede de back-end executa o processamento da regra de entrada:
    1. Não se aplica a regra NSG 1 (bloquear internet). Mover para a próxima regra.
    2. As regras NSG padrão permitem que o tráfego de sub-rede para sub-rede para que o tráfego é permitido. Pare o processamento da regra de NSG.
1. A AppVM02 recebe a resposta.

### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta ao servidor web

1. Um usuário da internet tenta acessar o servidor web IIS01 por meio do serviço FrontEnd001.CloudApp.Net.
1. Não há nenhum ponto de extremidade aberto para tráfego HTTP para que esse tráfego não passa pelo serviço de nuvem. O tráfego não chegam ao servidor.
1. Se os pontos de extremidade estiverem abertos por algum motivo, o NSG (bloquear internet) na sub-rede front-end bloqueia esse tráfego.
1. Por fim, a rota UDR da sub-rede front-end envia qualquer tráfego de saída de IIS01 para o firewall como o próximo salto. O firewall vê como tráfego assimétrico e descarta a resposta de saída.

>Assim, há pelo menos três camadas de defesa entre a internet e IIS01 independentes. O serviço de nuvem impede o acesso não autorizado ou inadequado.

### <a name="denied-internet-to-back-end-server"></a>(Negado) Internet para servidor de back-end

1. Um usuário da internet tenta acessar um arquivo em AppVM01 por meio do serviço BackEnd001.CloudApp.Net.
2. Não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, portanto, esta solicitação não passa o serviço de nuvem. O tráfego não chegam ao servidor.
3. Se os pontos de extremidade estiverem abertos por algum motivo, o NSG (bloquear internet) bloqueia esse tráfego.
4. Por fim, a rota UDR envia qualquer tráfego de saída de AppVM01 do firewall como o próximo salto. O firewall vê como tráfego assimétrico e descarta a resposta de saída.

> Assim, há pelo menos três camadas de defesa entre a internet e AppVM01 independentes. O serviço de nuvem impede o acesso não autorizado ou inadequado.

### <a name="denied-front-end-server-to-back-end-server"></a>(Negado) Servidor front-end para o servidor back-end

1. IIS01 está comprometido e esteja executando o código mal-intencionado tentando verificar os servidores da sub-rede de back-end.
1. A rota UDR da sub-rede front-end envia qualquer tráfego de saída de IIS01 para o firewall como o próximo salto. VM comprometida não é possível alterar esse roteamento.
1. O firewall processa o tráfego. Se a solicitação for para AppVM01 ou para o servidor DNS para pesquisas de DNS, o firewall pode potencialmente permitir o tráfego devido a regras de Firewall, 7 e 9. Todos os outros tráfegos são bloqueados pela regra de Firewall 11 (Negar tudo).
1. Se você habilitar a detecção de ameaças avançada no firewall, o tráfego que contém assinaturas ou padrões que sinalizem uma regra avançada de ameaças conhecidos poderia ser evitado. Essa medida pode funcionar mesmo se o tráfego é permitido de acordo com as regras básicas de encaminhamento discutidas neste artigo. Detecção avançada de ameaças não é abordada neste documento. Consulte a documentação do fornecedor para seu dispositivo de rede específica, recursos de ameaça avançados.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa DNS da Internet no servidor DNS

1. Um usuário da internet tenta procurar um registro DNS interno em DNS01 por meio do serviço BackEnd001.CloudApp.Net.
1. Como não há nenhum ponto de extremidade aberto para tráfego DNS, esse tráfego não passa pelo serviço de nuvem. Ele não chegam ao servidor.
1. Se os pontos de extremidade estiverem abertos por algum motivo, a regra NSG (bloquear internet) na sub-rede front-end bloqueia esse tráfego.
1. Por fim, a rota UDR da sub-rede de back-end envia qualquer tráfego de saída de DNS01 para o firewall como o próximo salto. O firewall vê isso como tráfego assimétrico e descarta a resposta de saída.

> Assim, há pelo menos três camadas de defesa entre a internet e DNS01 independentes. O serviço de nuvem impede o acesso não autorizado ou inadequado.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Internet para acesso ao SQL por meio do firewall

1. Um usuário da internet solicita dados SQL de SecSvc001.CloudApp.Net voltado para a internet do serviço de nuvem.
1. Não há nenhum ponto de extremidade aberto para SQL para que esse tráfego não passa o serviço de nuvem. Ele não atinge o firewall.
1. Se os pontos de extremidade do SQL estiverem abertos por algum motivo, o firewall executa o processamento da regra:
   1. Regra de firewall 1 (Gerenc. FW) não se aplica. Mover para a próxima regra.
   1. Regras de firewall 2-5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Não se aplicam a regras de firewall, 6 e 7 (regras de aplicativo). Mover para a próxima regra.
   1. Não se aplica a regra de Firewall 8 (para a internet). Mover para a próxima regra.
   1. Regra de firewall 9 (DNS) não se aplica. Mover para a próxima regra.
   1. Não se aplica a regra de firewall 10 (entre: subredes). Mover para a próxima regra.
   1. Aplicar a regra de firewall 11 (Negar tudo). O tráfego é bloqueado. Pare o processamento da regra.

## <a name="references"></a>Referências

Esta seção contém os seguintes itens:

* Script completo. Salve-o em um arquivo de script do PowerShell.
* Configuração de rede. Salve-o em um arquivo denominado NetworkConf2.xml.

Modifique as variáveis definidas pelo usuário nos arquivos conforme necessário. Execute o script e, em seguida, siga as instruções de configuração de regra de Firewall listadas anteriormente neste artigo.

### <a name="full-script"></a>Script completo

Depois de definir as variáveis definidas pelo usuário, execute este script para:

1. Conectar-se a uma assinatura do Azure
1. Criar uma nova conta de armazenamento
1. Criar uma nova rede virtual e três sub-redes, conforme definido no arquivo de configuração de rede
1. Criar cinco máquinas virtuais: um firewall e quatro VMs do Windows Server
1. Configure o UDR:
   1. Crie duas novas tabelas de rota
   1. Adicionar rotas às tabelas
   1. Associar tabelas às sub-redes apropriadas
1. Habilitar o Encaminhamento IP no NVA
1. Configure NSG:
   1. Criar um NSG
   1. Adicionar uma regra
   1. Associe o NSG às sub-redes apropriadas

Execute este PowerShell script localmente em uma internet computador ou servidor conectado.

> [!IMPORTANT]
> Quando você executa esse script, avisos ou outras mensagens informativas talvez surja no PowerShell. Apenas as mensagens de erro em vermelho são motivo de preocupação.

```powershell
    <#
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Arquivo de configuração de rede

Salve esse arquivo XML com localização atualizada. Alterar o `$NetworkConfigFile` variável no script completo acima para vincular ao arquivo de configuração de rede salvo.

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

## <a name="next-steps"></a>Próximas etapas

Você pode instalar um aplicativo de exemplo para ajudá-lo com este exemplo de rede de perímetro.

> [!div class="nextstepaction"]
> [Exemplo de script de aplicativo](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidirecional com NVA, NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Exibição lógica das regras de firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Criar um objeto de rede de front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Criar um objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Cópia da regra RDP padrão"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regra de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ícone de redirecionamento do aplicativo"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ícone de NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ícone de passagem"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regra de Gerenciamento de Firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regra RDP do firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regra Web do firewall"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regra de AppVM01 do firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regra de saída do firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regra de DNS do firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regra entre VNets do firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regra Negar do firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md

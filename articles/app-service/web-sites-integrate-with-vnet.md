---
title: Integrar o aplicativo com a rede Virtual do Azure - Serviço de Aplicativo do Azure
description: Mostra como conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma rede virtual do Azure nova ou existente
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 768179f8569eac14166bcbb0a888e1cdbe41d497
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128408"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de visualização de integração de rede virtual do Serviço de Aplicativo do Azure e mostra como configurá-lo com os aplicativos no [Serviço de Aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). As VNets ([Redes Virtuais do Azure][VNETOverview]) permitem que você coloque qualquer um dos recursos do Azure em uma rede não roteável para a Internet com acesso controlado. Essas redes podem ser conectadas às redes locais usando tecnologias de VPN. 

O Serviço de Aplicativo do Azure tem duas formas. 

1. Os sistemas multilocatário que dão suporte a toda a gama de planos de preço, exceto Isolado
2. O ASE (Ambiente do Serviço de Aplicativo) que implanta em sua VNet. 

Este documento percorre o recurso de Integração VNet, que é destinado para uso no Serviço de Aplicativo multilocatário.  Se o aplicativo está no [Ambiente do Serviço de Aplicativo][ASEintro], ele já está em uma rede virtual e não requer o uso do recurso de Integração VNet para acessar recursos na mesma VNet.

A Integração VNet concede ao seu aplicativo Web acesso a recursos da sua rede virtual, mas não permite acesso privado ao seu aplicativo Web por meio da rede virtual. Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. O acesso de site privado só está disponível com um ASE configurado com um ILB (Balanceador de carga interno). Para obter detalhes sobre como usar um ASE com ILB, comece com este artigo: [Criar e usar um ASE com um ILB][ILBASE]. 

A Integração VNet geralmente é usada para habilitar o acesso de aplicativos a um banco de dados e a serviços Web em execução na VNet. Com a integração VNet, você não precisa expor um ponto de extremidade público para aplicativos em sua VM, mas pode usar, em vez disso, endereços roteáveis privados fora da Internet. 

O recurso de integração VNet:

* requer um plano de preços Standard, Premium ou PremiumV2 
* funciona com VNet clássica ou do Gerenciador de Recursos 
* dá suporte a TCP e UDP
* funciona com aplicativos Web, móveis, de API e aplicativos de função
* permite que um aplicativo se conecte somente a uma VNet por vez
* permite a integração de até cinco VNets com um Plano do Serviço de Aplicativo 
* permite que a mesma VNet seja usada por vários aplicativos em um Plano do Serviço de Aplicativo
* requer um Gateway de Rede Virtual configurado com a VPN de Ponto a Site
* dá suporte a um SLA de 99,9% devido ao SLA no gateway

Há algumas coisas a que a Integração VNet não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios
* acesso a site privado
* acessar recursos por ExpressRoute 
* acessar recursos por pontos de extremidade de serviço 

### <a name="getting-started"></a>Introdução
Veja aqui algumas coisas para se ter em mente antes de conectar seu aplicativo Web a uma rede virtual:

* Uma rede virtual de destino precisa ter a VPN ponto a site habilitada com um gateway baseado em rota antes que possa ser conectada a um aplicativo. 
* A VNet deve estar na mesma assinatura que o ASP (Plano do Serviço de Aplicativo).
* Os aplicativos que se integram a uma VNet usam o DNS especificado daquela VNet.

## <a name="enabling-vnet-integration"></a>Habilitar a integração VNet

Há uma nova versão do recurso de Integração VNet que está em versão prévia. Ela não depende de VPN de ponto a site e também dá suporte ao acesso a recursos entre ExpressRoute ou Pontos de Extremidade de Serviço. Para saber mais sobre a nova funcionalidade em versão prévia, vá até o final deste documento. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Configurar um gateway em sua VNet ###

Se você já tiver um gateway configurado com endereços de ponto a site, você poderá pular para a configuração da Integração VNet com seu aplicativo.  
Para criar um gateway:

1. [Criar uma sub-rede de gateway][creategatewaysubnet] em sua VNet.  

1. [Criar o gateway de VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços de ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 precisará ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço precisa estar em um dos blocos de endereço a seguir:

* 10.0.0.0/8 – isso significa um intervalo de endereços IP de 10.0.0.0 a 10.255.255.255
* 172.16.0.0/12 – isso significa um intervalo de endereços IP de 172.16.0.0 a 172.31.255.255 
* 192.168.0.0/16 – isso significa um intervalo de endereços IP de 192.168.0.0 a 192.168.255.255

Se você estiver apenas criando o gateway para usar com a integração de VNet do serviço de aplicativo, em seguida, você não precisará carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo com sua VNet até que o gateway seja provisionado. 

### <a name="configure-vnet-integration-with-your-app"></a>Configurar a Integração VNet com o aplicativo ###

Para habilitar a Integração VNet com o aplicativo: 

1. Vá para seu aplicativo no portal do Azure e abra as configurações do aplicativo e selecione Rede > Integração VNet. Dimensione o ASP para um SKU Standard ou superior antes que você possa configurar a Integração VNet. 
 ![Interface do usuário de Integração VNet][1]

1. Selecione **Adicionar VNet**. 
 ![Adicionar Integração VNet][2]

1. Selecione sua VNet. 
  ![Selecione sua VNet][8]
  
Seu aplicativo será reiniciado após essa última etapa.  

## <a name="how-the-system-works"></a>Como o sistema funciona
O recurso Integração VNet é criado sobre a tecnologia de VPN ponto a site. Aplicativos no Serviço de Aplicativo do Azure são hospedados em um sistema multilocatário, que impede o provisionamento de um aplicativo diretamente em uma VNet. A tecnologia ponto a site limita o acesso à rede apenas à máquina virtual que está hospedando o aplicativo. Os aplicativos são restritos apenas ao envio de tráfego para a Internet, por meio de Conexões Híbridas ou por meio da Integração VNet. 

![Como a Integração VNet funciona][3]

## <a name="managing-the-vnet-integrations"></a>Gerenciar as integrações VNet
A capacidade de se conectar e desconectar de uma VNet está no nível dos aplicativos. As operações que podem afetar a Integração VNet entre vários aplicativos estão no nível do Plano do Serviço de Aplicativo. Da UID do aplicativo, você pode obter detalhes sobre sua VNet. Essas informações também são mostradas no nível do ASP. 

 ![Detalhes da VNet][4]

Na página Status do Recurso de Rede, você pode ver se seu aplicativo está conectado à sua VNet. Se o gateway da VNet estiver inativo por qualquer motivo, seu status aparecerá como não conectado. 

As informações que você tem no aplicativo na interface de usuário da Integração VNet no nível de aplicativo são as mesmas detalhadas originadas no ASP. Estes são os itens:

* Nome da VNet – links para a interface do usuário da rede virtual
* Localização – reflete a localização de sua VNet. A integração com uma rede virtual em outra localização pode causar problemas de latência para seu aplicativo. 
* Status do certificado – reflete se os certificados estão sincronizados entre o Plano do Serviço de Aplicativo e a VNet.
* Status do gateway – se os gateways ficarem inativos por algum motivo, seu aplicativo não poderá acessar recursos na VNet. 
* Espaço de endereço da VNet – mostra o espaço de endereços IP para sua VNet. 
* Espaço de endereço ponto a site – mostra o espaço de endereços IP ponto a site para sua VNet. Ao fazer chamadas em sua VNet, o endereço de seu aplicativo será um desses endereços. 
* Espaço de endereço site a site – você pode usar as VPNs site a site para conectar sua VNet a seus recursos locais ou a outras VNet. Os intervalos de IP definidos com essa conexão VPN são mostrados aqui.
* Servidores DNS – mostra os servidores DNS configurados com sua VNet.
* Endereços IP roteados para a rede virtual – mostra os blocos de endereços roteados usado para direcionar o tráfego em sua VNet 

A única operação que você pode executar no modo de exibição de aplicativo da Integração VNet é desconectar-se da VNet à qual seu aplicativo está atualmente conectado. Para desconectar o aplicativo de uma VNet, selecione **Desconectar**. Seu aplicativo será reiniciado quando você se desconectar de uma VNet. Desconectar-se não altera a VNet. A VNet e sua configuração, incluindo os gateways, permanece inalterada. Se você quiser excluir sua VNet, precisa primeiro excluir os recursos nela, incluindo os gateways. 

Para acessar a interface do usuário da Integração VNet do ASP, abra a interface do usuário do ASP e selecione **Rede**.  Na Integração VNet, selecione **Clique aqui para configurar** para abrir a interface do usuário do Status de Recurso de Rede.

![Informações da Integração VNet do ASP][5]

A interface do usuário da Integração VNet do ASP mostrará todas as redes virtuais que são usadas pelos aplicativos no seu ASP. Você pode ter até 5 VNets conectadas por qualquer número de aplicativos em seu Plano do Serviço de Aplicativo. Cada aplicativo pode ter apenas uma integração configurada. Para ver detalhes em cada VNet, clique na VNet que lhe interessa. Existem duas ações que você pode executar aqui.

* **Sincronizar rede**. A operação sincronizar rede garante que seus certificados e informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS da VNet, será necessário executar uma operação **Sincronizar rede**. Esta operação reiniciará todos os aplicativos usando essa VNet.
* **Adicionar rotas** A adição de rotas orientará o tráfego de saída em sua VNet.

**Roteamento** As rotas que são definidas em sua VNet são usadas para direcionar o tráfego para sua VNet vindo de seu aplicativo. Se você precisar enviar o tráfego de saída adicional para a VNet, você poderá adicionar esses blocos de endereço aqui.   

**Certificados** Quando a Integração VNet está habilitada, há uma troca de certificados necessária para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.
Se os certificados ou as informações de rede forem alterados, você precisará clicar em "Sincronizar Rede". Quando clica em "Sincronizar Rede", você causa uma breve interrupção na conectividade entre o aplicativo e a VNet. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente. 

## <a name="accessing-on-premises-resources"></a>Como acessar recursos locais
Aplicativos podem acessar recursos locais ao integrarem-se com VNets que têm conexões site a site. Para acessar recursos localmente, você precisará atualizar as rotas de gateway de VPN locais com seus blocos de endereço ponto a site. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam de acordo com o gateway, e não são descritos aqui. Além disso, você não pode ter o BGP configurado com uma conexão VPN site a site.

> [!NOTE]
> O recurso de Integração VNet não integra um aplicativo com uma VNet que tem um Gateway ExpressRoute. Mesmo se o Gateway ExpressRoute estiver configurado no [modo de coexistência][VPNERCoex], a Integração VNet não funcionará. Se você precisar acessar recursos usando uma conexão ExpressRoute, poderá usar um [Ambiente de Serviço de Aplicativo][ASE] que seja executado em sua VNet. 
> 
> 

## <a name="peering"></a>Emparelhamento
Você pode usar a Integração VNet para acessar recursos em redes virtuais emparelhadas com a rede virtual à qual você está conectado. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione que uma conexão de emparelhamento na rede virtual à qual o aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na rede virtual que está sendo emparelhada à rede virtual à qual você está conectado. Ao adicionar a conexão de emparelhamento à VNet de destino, habilite **Permitir acesso à rede virtual** e marque **Permitir tráfego encaminhado** e **Permitir gateways remotos**.
1. Vá para o Plano do Serviço de Aplicativo > Rede > Interface do usuário da Integração VNet no portal.  Selecione a VNet à qual o aplicativo se conecta. Na seção de roteamento, adicione o intervalo de endereços da VNet que está emparelhada com a VNet à qual o aplicativo está conectado.  


## <a name="pricing-details"></a>Detalhes de preço
Há três cobranças relacionadas ao uso do recurso de Integração VNet:

* Requisitos de tipo de preço do ASP
* Custos de transferência de dados
* Custos de gateway de VPN.

Os aplicativos precisam estar em um Plano de Serviço de Aplicativo Standard, Premium ou PremiumV2. Você pode ver mais detalhes sobre esses custos aqui: [Preços do Serviço de Aplicativo][ASPricing]. 

Há um encargo de saída de dados, mesmo se a rede virtual está no mesmo data center. Esses encargos são descritos em [Detalhes de Preços de Transferência de Dados][DataPricing]. 

Há um custo para o gateway de VNet necessário para a VPN ponto a site. Os detalhes estão na página [Preços de Gateway de VPN][VNETPricing].

## <a name="troubleshooting"></a>solução de problemas
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo. Há dois consoles que você pode usar. Uma é o console do Kudu e a outra é o console no portal do Azure. Para acessar o console do Kudu do aplicativo, vá para Ferramentas -> Kudu. Isso é o mesmo que ir para [nomedosite].scm.azurewebsites.net. Depois de aberto, vá para a guia do console Depuração. Para obter a console hospedado no portal do Azure, em seu aplicativo, vá para Ferramentas -> Console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup** e **tracert** não funcionarão no console devido a restrições de segurança. Para compensar essa ausência, duas ferramentas separadas foram adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe. A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar **nameresolver** para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** informa se você pode acessar um host específico e uma porta. Ele só pode mostrar êxito se: houver um aplicativo escutando na combinação de porta e host, e houver acesso de rede de seu aplicativo para o host e porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurar o acesso a recursos hospedados por VNet
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos. Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho.** Se houver um firewall no caminho, você atingirá o tempo limite de TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso. 
* **O DNS não está acessível.** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNet está configurada.
* **O intervalo de endereços de ponto a site é inválido.** O intervalo de IP de ponto a site deve estar nos intervalos de IP privados RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255). Se o intervalo usar IPs fora disso, as coisas não funcionarão. 

Se esses itens não resolverem seu problema, procure por coisas simples primeiro, como: 

* O Gateway é mostrado como estando em execução no Portal?
* Os certificados são mostrados como sincronizados?
* Alguém alterou a configuração de rede sem fazer uma “Sincronização de Rede” nos ASPs afetados? 

Se o gateway estiver inativo, ative-o. Se os certificados estiverem fora de sincronia, vá para a exibição ASP da integração VNet e pressione "Sincronizar Rede". Se você suspeitar que houve uma alteração feita à sua configuração de VNet que não foi sincronizada com seus ASPs, pressione "Sincronizar Rede".  Uma operação de "Sincronizar Rede" reiniciará todos os aplicativos no ASP que estão integrados com essa VNet. 

Se tudo estiver bem, você precisará se aprofundar um pouco mais:

* Há outros aplicativos que usam a Integração VNet para acessar recursos na mesma VNet? 
* Você consegue ir ao console do aplicativo e usar tcpping para acessar outros recursos na sua VNet? 

Se alguma das opções acima é verdadeira, a Integração VNet está boa e o problema está em outro lugar. Essa hipótese é mais complicada, já que não há uma maneira simples de ver por que você não consegue acessar o host:porta. Algumas das causas incluem:

* você tem um firewall no seu host que impede o acesso à porta do aplicativo usando o intervalo de IP ponto a site. O cruzamento de sub-redes geralmente exige acesso Público.
* o host de destino está inoperante
* seu aplicativo está inoperante
* você tinha o IP ou nome de host incorreto
* seu aplicativo está escutando em uma porta diferente da que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade. 
* os grupos de segurança de rede estão configurados de modo a impedir o acesso ao host do aplicativo e à porta do intervalo de IP ponto a site.

Lembre-se de que você não sabe quais IPs no intervalo IP Ponto a Site seu aplicativo usará; portanto, é necessário permitir o acesso a todo o intervalo. 

As etapas de depuração adicionais incluem:

* conecte-se a uma VM na sua VNet e tente acessar o host:porta do recurso de lá. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe do é:

      test-netconnection hostname [optional: -Port]

* abra um aplicativo em uma VM e teste o acesso àquele host e porta usando o console do seu aplicativo

#### <a name="on-premises-resources"></a>Recursos locais ####

Se o aplicativo não puder acessar um recurso local, verifique se é possível acessar o recurso da sua VNet. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se sua VM não conseguir acessar o recurso local, verifique se a conexão de VPN site a site está funcionando. Se estiver funcionando, verifique os mesmos pontos observados anteriormente, bem como a configuração e o status do gateway local. 

Se a VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não, isso provavelmente ocorrerá devido a um dos seguintes motivos:

* suas rotas não estão configuradas com os intervalos de IP ponto a site em seu gateway local
* seus grupos de segurança de rede estão bloqueando o acesso para o intervalo de IP Ponto a Site
* os firewalls locais estão bloqueando o tráfego do intervalo de IP Ponto a Site


## <a name="powershell-automation"></a>Automação do PowerShell

Você pode integrar o Serviço de Aplicativo com uma Rede Virtual do Azure usando o PowerShell. Para um script pronto para uso, consulte [Conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma Rede Virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Conexões híbridas e ambientes de serviço de aplicativo
Há três recursos que habilitam o acesso a recursos hospedados em VNet. Eles são:

* Integração VNet
* Conexões Híbridas
* Ambientes de Serviço de Aplicativo

As conexões híbridas exigem que você instale um agente de retransmissão chamado HCM (gerente de conexões híbridas) na sua rede. O HCM precisa ser capaz de se conectar ao Azure e também a seu aplicativo. Conexões Híbridas não requer um ponto de extremidade de entrada acessível pela Internet para a rede remota, como é necessário para uma conexão VPN. O HCM só é executado no Windows e você pode ter até cinco instâncias em execução para fornecer alta disponibilidade. No entanto, as conexões híbridas só dão suporte a TCP e cada ponto de extremidade de HC tem que corresponder a uma combinação de host:porta específica. 

O recurso Ambiente do Serviço de Aplicativo permite a execução de uma única instância de locatário do Serviço de Aplicativo do Azure em sua VNet. Se os aplicativos estão em um Ambiente do Serviço de Aplicativo, os aplicativos podem acessar recursos na VNet sem nenhuma etapa adicional. Com um ambiente de serviço de aplicativo, seus aplicativos executados nos trabalhos mais potentes e podem escalar verticalmente até 100 instâncias ASP. Os Ambientes do Serviço de Aplicativo funcionam com todos os recursos de rede, incluindo o ExpressRoute e pontos de extremidade de serviço.  

Embora haja sobreposição de caso de uso, nenhum desses recursos pode substituir o outro. Saber qual recurso usar depende de suas necessidades. Por exemplo: 

* Se você for um desenvolvedor e quiser executar um site no Azure que possa acessar um banco de dados na estação de trabalho na sua mesa, o mais fácil será usar as Conexões Híbridas. 
* Se você tem uma grande organização que deseja colocar um grande número de propriedades da Web na nuvem pública e gerenciá-las em sua própria rede, a melhor opção é o Ambiente de Serviço de Aplicativo. 
* Se você tiver vários aplicativos que precisam acessar recursos em sua rede virtual, a integração da VNet é a melhor opção. 

Quando a VNet já estiver conectada à rede local, o uso da Integração VNet ou de um Ambiente do Serviço de Aplicativo será uma maneira fácil de consumir recursos locais. Se a VNet não estiver conectada à rede local, será muito mais trabalhoso configurar uma VPN site a site com a VNet do que instalar o HCM. 

Além das diferenças funcionais, há também diferenças de preço. O recurso Ambiente de Serviço de Aplicativo é uma oferta do serviço Premium, mas oferece a maioria das possibilidades de configuração de rede, além de outros recursos incríveis. A Integração VNet pode ser usada com ASPs Standard ou Premium e é perfeita para consumir com segurança recursos em sua VNet do Serviço de Aplicativo multilocatário. O Conexões Híbridas atualmente depende de um conta BizTalk, com preços que variam de gratuito a mais caros baseado na quantidade necessária. Quando se trata de trabalhar em várias redes, no entanto, não há nenhum outro recurso como Conexões Híbridas, que pode permitir que você acesse recursos em mais de 100 redes separadas. 

## <a name="new-vnet-integration"></a>Nova integração da VNet ##

Há uma nova versão da funcionalidade Integração da VNet que não depende da tecnologia VPN Ponto a Site. Ao contrário do recurso já existente, a nova versão prévia do recurso funcionará com o ExpressRoute e pontos de extremidade de serviço. 

A nova funcionalidade está disponível apenas de unidades de escala do Serviço de Aplicativo do Azure mais recentes. Se você pode dimensionar para PremiumV2, significa que você está em uma unidade de escala do Serviço de Aplicativo mais recente. A interface do usuário da Integração VNet no portal informará você se o aplicativo pode usar o novo recurso de Integração VNet. 

A nova versão está em versão prévia e tem as características a seguir.

* Nenhum gateway é necessário para usar o novo recurso de Integração de VNet
* Você pode acessar recursos em conexões do ExpressRoute sem nenhuma configuração adicional além da integração com a VNet conectada ao ExpressRoute.
* O aplicativo e a VNet devem estar na mesma região
* O novo recurso requer uma sub-rede não usada na VNet do Resource Manager.
* Seu Plano de Serviço de Aplicativo precisa ser um plano Standard, Premium ou PremiumV2
* Cargas de trabalho de produção não são compatíveis com o novo recurso enquanto ele está em versão prévia
* O aplicativo deve estar em uma implantação do Serviço de Aplicativo do Azure capaz de escalar verticalmente para Premium v2.
* O novo recurso de Integração VNet não funciona para aplicativos em um Ambiente do Serviço de Aplicativo.
* Você não pode excluir uma VNet com um aplicativo integrado.  
* Tabelas de rotas e emparelhamento global ainda não estão disponíveis com a nova Integração VNet.  
* Um endereço é usado para cada instância de plano do serviço de aplicativo. Uma vez que o tamanho da sub-rede não pode ser alterado após a atribuição, use uma sub-rede que possa cobrir com folga seu tamanho máximo de escala. Um /27 com 32 endereços é o tamanho recomendado, pois acomodará um plano de Serviço de Aplicativo dimensionado para 20 instâncias.
* Você pode consumir recursos protegidos do ponto de extremidade de serviço usando a nova funcionalidade de Integração VNet. Para fazer isso, habilite os pontos de extremidade de serviço na sub-rede usada para a Integração VNet.

Para usar o novo recurso:

1. Vá para a interface do usuário de rede no portal. Se o aplicativo for capaz de usar o novo recurso, você verá uma funcionalidade para usar a nova versão prévia do recurso.  

   ![Selecione a nova versão prévia da Integração VNet][6]

1. Selecione **Adicionar uma VNet (versão prévia)**.  

1. Selecione a VNet do Resource Manager que você deseja integrar e, em seguida, crie uma nova sub-rede ou escolha uma sub-rede vazia já existente. A integração leva menos de um minuto para ser concluída. Durante a integração, o aplicativo é reiniciado.  Quando a integração for concluída, você verá detalhes sobre a VNet que integrou, com uma faixa na parte superior que informa que o recurso está em versão prévia.

   ![Selecionar a VNet e a sub-rede][7]

Para habilitar o aplicativo a usar o servidor DNS configurado com a VNet, crie uma configuração de aplicativo em que o nome seja WEBSITE_DNS_SERVER e o valor seja o endereço IP do servidor.  Se você tiver um servidor DNS secundário, crie outra configuração de aplicativo em que o nome seja WEBSITE_DNS_ALT_SERVER e o valor seja o endereço IP do servidor. 

Para desconectar o aplicativo da VNet, selecione **Desconectar**. Isso reiniciará o aplicativo Web. 

O novo recurso de integração de VNet permite que você use pontos de extremidade de serviço.  Para usar pontos de extremidade de serviço com seu aplicativo, use a nova Integração VNet para se conectar a uma VNet selecionada e, em seguida, configure pontos de extremidade de serviço na sub-rede que você usou para a integração. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool

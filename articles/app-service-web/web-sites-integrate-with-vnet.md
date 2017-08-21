---
title: Integrar um aplicativo a uma Rede Virtual do Azure
description: "Mostra como conectar um aplicativo do Azure no Serviço de Aplicativo do Azure a uma rede virtual do Azure nova ou existente"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 31b4f69a2870e619255feac6bed3679efb03f568
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo Web a uma Rede Virtual do Azure
Este documento descreve o recurso de visualização de integração de rede virtual do Serviço de Aplicativo do Azure e mostra como configurá-lo com os aplicativos no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Se você estiver familiarizado com VNETs (Redes Virtuais do Azure), isso é um recurso que permite  colocar muitos dos recursos do Azure em uma rede que não pode ser roteada pela Internet, cujo acesso você pode controlar.  Essas redes podem ser conectadas às redes locais usando uma variedade de tecnologias de VPN.  Para saber mais sobre redes virtuais do Azure, confira [Visão geral da Rede Virtual do Azure][VNETOverview].  

O Serviço de Aplicativo do Azure tem duas formas.  

1. Os sistemas de vários locatários que dão suporte a toda a gama de planos de preço.
2. O recurso premium do ASE (Ambiente do Serviço de Aplicativo) que implanta em sua VNET.  

Este documento passa pela Integração VNET e não pelo Ambiente de Serviço de Aplicativo.  Se você quiser saber mais sobre o recurso ASE, comece com as informações fornecidas aqui: [Introdução ao Ambiente de Serviço de Aplicativo][ASEintro].

A integração VNET concede ao seu aplicativo Web acesso a recursos da sua rede virtual, mas não concede acesso privado ao seu aplicativo Web por meio da rede virtual.  O acesso de site privado só está disponível com um ASE configurado com um ILB (Balanceador de carga interno).  Para obter detalhes sobre como usar um ASE com ILB, comece com este artigo: [Criação e uso de um ASE com ILB][ILBASE]. 

Um cenário comum de uso da Integração VNET é a habilitação do acesso de seu aplicativo Web a um banco de dados ou serviços Web em execução em uma máquina virtual em sua rede virtual do Azure.  Com a integração VNET, você não precisa expor um ponto de extremidade público para aplicativos em sua VM, mas pode usar, em vez disso, endereços roteáveis privados fora da Internet.  

O recurso de integração VNET:

* requer um plano de preços Standard ou Premium 
* funcionará com VNET Clássica (V1) ou Resource Manager(V2) 
* dá suporte a TCP e UDP
* funciona com aplicativos Web, móveis e de API
* permite que um aplicativo se conecte somente a uma VNET por vez
* permite a integração de até cinco VNETs com um Plano de Serviço de Aplicativo 
* permite que a mesma VNET seja usada por vários aplicativos em um Plano de Serviço de Aplicativo
* dá suporte a um SLA de 99,9% devido a uma dependência no Gateway de VNET

Há algumas coisas a que a integração VNET não dá suporte, incluindo:

* montagem de unidade
* integração ao AD 
* NetBios
* acesso a site privado

### <a name="getting-started"></a>Introdução
Veja aqui algumas coisas para se ter em mente antes de conectar seu aplicativo Web a uma rede virtual:

* A integração da VNET só funciona com aplicativos em um plano de preços **Standard** ou **Premium**.  Se você habilitar o recurso e dimensionar seu Plano de Serviço de Aplicativo para um plano de preços sem suporte, seus aplicativos perderão suas conexões com as VNETs que estão usando.  
* Se a sua rede virtual de destino já existe, ela deve ter a VPN ponto a site habilitada com um gateway de roteamento dinâmico antes que possa ser conectada a um aplicativo.  Você não pode habilitar a opção ponto a site da VPN (rede virtual privada) com se o gateway está configurado com roteamento estático.
* A VNET deve estar na mesma assinatura que o ASP (Plano do Serviço de Aplicativo).  
* Os aplicativos que se integram a uma VNET usarão o DNS específico daquela VNET.
* Por padrão, os aplicativos integrados apenas rotearão o tráfego em sua VNET baseados nas rotas que são definidas em sua VNET.  

## <a name="enabling-vnet-integration"></a>Habilitando a integração VNET
Este documento concentra-se principalmente em usar o Portal do Azure para a Integração VNET.  Para habilitar a Integração VNET com o seu aplicativo usando o PowerShell, siga as instruções aqui: [Conectar seu aplicativo à rede virtual usando o PowerShell][IntPowershell].

Você tem a opção de conectar seu aplicativo a uma rede virtual nova ou existente.  Se você criar uma nova rede como parte a sua integração, além de criar apenas uma VNET, um gateway de roteamento dinâmico será pré-configurado para você e a VPN Ponto a Site será habilitada.  

> [!NOTE]
> A configuração de uma nova integração da rede virtual pode levar vários minutos.  
> 
> 

Para habilitar a integração VNET, abra as Configurações do aplicativo configurações e selecione Rede.  A interface do usuário oferece três opções de rede.  Este guia só aborda a integração VNET, mas as conexões híbridas e ambientes de serviço de aplicativo são discutidos neste documento.  

Se seu aplicativo não estiver no plano de preços correto, a interface do usuário permitirá que você escalone seu plano para o plano de preços mais alto que queira escolher.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Habilitando a integração VNET com uma VNET já existente
A interface de usuário de integração de VNET permite que você selecione em uma lista de suas VNETs.  As VNETs Clássicas estarão identificadas com a palavra "Clássica" entre parênteses ao lado do nome da Rede Virtual.  A lista é classificada de modo que as Redes Virtuais do Resource Manager estarão listadas primeiro.  Na imagem abaixo, você pode ver que somente uma VNET pode ser selecionada.  Há vários motivos para uma VNET ficar esmaecida, incluindo:

* a VNET está em outra assinatura à qual sua conta tem acesso
* a VNET não tem um ponto a Site habilitado
* a VNET não tem um gateway de roteamento dinâmico

![][2]

Para habilitar a integração, simplesmente clique na VNET que deseja integrar.  Depois de selecionar a VNET, o aplicativo será reiniciado automaticamente para que as alterações entrem em vigor.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Habilitar Ponto a Site em uma VNET Clássica
Se sua VNET não tem um gateway ou ponto a site, você precisa configurar isso primeiro.  Para fazer isso para uma VNET Clássica, acesse o [Portal do Azure][AzurePortal] e exiba a lista de Redes Virtuais (clássicas).  Em seguida, clique na rede com que você deseja integrar e clique na caixa grande em Essentials chamada Conexões VPN.  A partir daqui, você pode criar sua VPN ponto a site e até mesmo fazê-la criar um gateway.  Depois de passar pelo ponto a site com a experiência de criação do gateway, demora cerca de 30 minutos até que ele fique pronto.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Habilitar Ponto a Site em uma VNET do Resource Manager
Para configurar uma VNET do Resource Manager com um gateway e Ponto a Site, você pode usar o PowerShell, conforme documentado em [Configurar uma conexão Ponto a Site com uma rede virtual usando PowerShell][V2VNETP2S] ou usar o Portal do Azure, conforme documentado em [Configurar uma conexão Ponto a Site para uma rede virtual usando o Portal do Azure][V2VNETPortal].  A interface do usuário para executar esse recurso ainda não está disponível. Observe que você não precisará criar certificados para a configuração ponto a site. Isso será automaticamente configurado quando você conectar seu WebApp para a VNET. 

### <a name="creating-a-pre-configured-vnet"></a>Criar uma VNET pré-configurada
Se você quiser criar uma nova VNET que esteja configurada com um gateway e um Ponto a Site, a interface do usuário de rede do Serviço de Aplicativo tem a capacidade de fazer isso, mas somente para VNET de Resource Manager.  Se você quiser criar uma VNET Clássica com um gateway e um Ponto a Site, precisará fazer isso manualmente por meio da interface do usuário de rede. 

Para criar uma VNET de Resource Manager por meio da interface do usuário de Integração VNET, basta selecionar **Criar Nova Rede Virtual** e fornecer:

* Nome da VNET
* Bloco de endereço da VNET
* Nome da sub-rede
* Bloco de endereço da sub-rede
* Bloco de endereço do Gateway
* Bloco de endereço Ponto a site

Se você quiser que essa VNET se conecte a qualquer outra rede, deverá evitar escolher um espaço de endereço IP que se sobreponha a essas redes.  

> [!NOTE]
> A criação da VNET de Resource Manager com um gateway leva cerca de 30 minutos e atualmente não integrará a VNET com o seu aplicativo.  Depois que sua VNET é criada com o gateway, você precisa voltar para a interface do usuário de integração de VNET do seu aplicativo e selecionar sua nova VNET.
> 
> 

![][3]

As redes virtuais do Azure normalmente são criadas dentro de endereços de rede privada.  Por padrão, o recurso Integração de rede virtual roteará o tráfego destinado aos intervalos de endereços IP em sua rede virtual.  Os intervalos de endereços IP privados são:

* 10.0.0.0/8 - esse é o mesmo que 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 - é o mesmo que 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - é o mesmo que 192.168.0.0 - 192.168.255.255

O espaço de endereço de rede virtual precisa ser especificado na notação CIDR.  Se você não estiver familiarizado com notação CIDR, é um método para especificar blocos de endereço usando um endereço IP e um número inteiro que representa a máscara de rede. Como uma referência rápida, considere que 10.1.0.0/24 seria 256 endereços e 10.1.0.0/25 seria 128 endereços.  Um endereço IPv4 com um /32 seria apenas 1 endereço.  

Se você definir as informações do servidor DNS aqui, elas serão definidas para sua rede virtual.  Após a criação da rede virtual, você poderá editar essas informações nas experiências de usuário da rede virtual.

Quando você cria uma VNET Clássica usando a interface de usuário de integração VNET, ela criará uma rede virtual no mesmo grupo de recursos do seu aplicativo. 

## <a name="how-the-system-works"></a>Como o sistema funciona
De modo oculto este recurso se baseia na tecnologia VPN Ponto a site para se conectar ao aplicativo à sua VNET.   Aplicativos no Serviço de Aplicativo do Azure têm uma arquitetura de sistema multilocatário, que impede o provisionamento de um aplicativo diretamente em uma VNET, como ocorre com máquinas virtuais.  Ao desenvolver uma tecnologia ponto a site, limitamos o acesso à rede apenas à máquina virtual que está hospedando o aplicativo.  O acesso à rede é ainda mais limitado nestes hosts do aplicativo, de modo que seus aplicativos poderão acessar apenas as redes que você configurar para eles acessarem.  

![][4]

Se você ainda não configurou um servidor DNS com sua rede virtual será necessário usar endereços IP.  Ao usar endereços IP, lembre-se de que a grande vantagem desse recurso é que ele permite que você use os endereços privados em sua rede privada.  Se você configurar seu aplicativo para usar endereços IP públicos em uma das suas VMs, não estará usando o recurso Integração de rede virtual e estará se comunicando pela internet.

## <a name="managing-the-vnet-integrations"></a>Gerenciando as integrações VNET
A capacidade de se conectar e desconectar de uma rede virtual está no nível dos aplicativos.  As operações que podem afetar a integração da rede virtual entre vários aplicativos estão no nível do ASP.  Você pode obter detalhes sobre sua VNET na interface de usuário que é mostrada no nível de aplicativo.  A maioria dessas informações também é mostrada no nível do ASP.  

![][5]

Na página Status do Recurso de Rede, você pode ver se seu aplicativo está conectado à sua rede virtual.  Se o gateway da rede virtual está inativo por qualquer motivo, ele aparece como não conectado.  

As informações que você tem no aplicativo na interface de usuário da Integração VNET no nível de aplicativo são as mesmas detalhadas originadas no ASP.  Estes são os itens:

* Nome da VNET – esse link abre a interface do usuário da rede
* Local – reflete o local da sua rede virtual.  É possível fazer a integração a uma rede virtual em outro local.
* Status do certificado – há certificados usados para proteger a conexão VPN entre o aplicativo e a rede virtual.  Isso é um teste para garantir que eles estejam em sincronia.
* Status do gateway – se os gateways ficarem inativos por algum motivo, seu aplicativo não poderá acessar recursos na rede virtual.  
* Espaço de endereço da VNET – é o espaço de endereço IP para sua rede virtual.  
* Espaço de endereço ponto a site – é o espaço de endereço IP ponto a site para sua rede virtual.  Seu aplicativo mostrará a comunicação como proveniente de um dos IPs nesse espaço de endereço.  
* Espaço de endereço site a site – você pode usar as VPNs site a site para conectar sua VNET a seus recursos locais ou a outras VNETs.  Se estiver configurado, os intervalos de IP definidos com aquela conexão VPN serão exibidos aqui.
* Servidores DNS – se você tiver servidores DNS configurados com sua rede virtual, eles serão listados aqui.
* IPs roteados para a rede virtual – há uma lista de endereços IP para os quais sua rede virtual definiu o roteamento.  Esses endereços serão exibidos aqui.  

A única operação que você pode executar no modo de exibição de aplicativo da integração de rede virtual é desconectar-se da rede virtual à qual seu aplicativo está atualmente conectado.  Para fazer isso, simplesmente clique em Desconectar na parte superior.  Essa ação não altera sua rede virtual.  A VNET e sua configuração, incluindo os gateways, permanece inalterada.  Se você quiser excluir sua rede virtual, precisa primeiro excluir os recursos nela, incluindo os gateways.  

O modo de exibição do Plano de Serviço de Aplicativo tem algumas operações adicionais.  Ele também acesso diferente do aplicativo.  Para acessar a interface do usuário da rede do ASP basta abrir sua interface do usuário do ASP e rolar para baixo.  Há um elemento de interface do usuário chamado Status do Recurso de Rede.  Ele fornecerá alguns pequenos detalhes sobre a integração da rede virtual.  Clicar nessa interface do usuário abre a interface do usuário Status de Recurso de Rede.  Se você clicar em "Clique aqui para gerenciar", abrirá a interface do usuário que lista as integrações de rede virtual nesse ASP.

![][6]

É bom lembrar do local do ASP ao examinar os locais das redes virtuais com que você estiver integrando.  Quando a rede virtual está em outro local, a probabilidade de perceber os problemas de latência é muito menor.  

As redes virtuais integradas são um lembrete de quantas redes virtuais estão integradas com seus aplicativos no ASP e quantas você pode ter.  

Para ver detalhes adicionados em cada rede virtual, basta clicar na rede virtual que lhe interessa.  Além dos detalhes que foram observados anteriormente, você também verá uma lista dos aplicativos nesse ASP que estão usando aquela rede virtual.  

Em relação a ações, existem duas ações primárias.  A primeira é a capacidade de adicionar rotas que direcionam o tráfego que sai de seu aplicativo para sua rede virtual.  A segunda ação é a capacidade de sincronizar certificados e informações de rede.

![][7]

**Roteamento** Conforme observado anteriormente, as rotas que são definidas em sua rede virtual são usadas para direcionar o tráfego para sua rede virtual vindo de seu aplicativo.  No entanto, há alguns usos, como quando os clientes querem enviar o tráfego de saída adicional de um aplicativo para a rede virtual, e esse recurso é fornecido para eles.  O que acontece com o tráfego depois depende de como o cliente configura sua rede virtual.  

**Certificados** O status do certificado reflete uma verificação de que está sendo executada pelo Serviço de Aplicativo para validar se os certificados que estamos usando para a conexão VPN ainda estão válidos.  Ao habilitar a integração de rede virtual, se essa for a primeira integração com a rede virtual de qualquer aplicativos nesse ASP, há uma troca de certificados necessária para garantir a segurança da conexão.  Juntamente com os certificados, obtemos a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.
Se esses certificados ou informações de rede forem alterados, você precisará clicar em "Sincronizar Rede".  **Observação**: quando você clica em "Sincronização Rede", você pode causar uma breve interrupção na conectividade entre o aplicativo e a rede virtual.  Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente.  

## <a name="accessing-on-premises-resources"></a>Como acessar recursos locais
Um dos benefícios do recurso Integração VNET é que, se a VNET estiver conectada à rede local com uma VPN site a site, seus aplicativos poderão ter acesso aos recursos locais usando seu aplicativo.  Para isso funcionar, talvez seja necessário atualizar o gateway de VPN local com as rotas para o intervalo de IP ponto a site.  Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas, incluindo a VPN ponto a site.  Se você adicionar a VPN ponto a site depois de criar a VPN site a site, precisará atualizar as rotas manualmente.  Os detalhes sobre como fazer isso variam de acordo com o gateway e não são descritos aqui.  

> [!NOTE]
> O recurso de Integração VNET não integra um aplicativo com uma rede VNet que tem um Gateway ExpressRoute. Mesmo que o Gateway ExpressRoute esteja configurado no [modo de coexistência][VPNERCoex], ele não funcionará. Se você precisar acessar recursos usando uma conexão ExpressRoute, poderá usar um [Ambiente de Serviço de Aplicativo][ASE] que seja executado em sua VNet.
> 
> 

## <a name="pricing-details"></a>Detalhes de preço
Existem algumas nuances de preço que você deve levar em conta ao usar o recurso Integração VNET.  Há três cobranças relacionadas ao uso desse recurso:

* Requisitos de tipo de preço do ASP
* Custos de transferência de dados
* Custos de gateway de VPN.

Para que seus aplicativos possam usar o recurso, eles precisam estar em um Plano de Serviço de Aplicativo Standard ou Premium.  Você pode ver mais detalhes sobre esses custos aqui: [Preço do Serviço de Aplicativo][ASPricing]. 

Devido à maneira como as VPNs ponto a site são tratadas, você sempre será cobrado por dados de saída pela conexão da Integração VNET, mesmo que ela não esteja no mesmo data center.  Para ver quais são essas cobranças, dê uma olhada aqui: [Detalhes de Preço da Transferência de Dados][DataPricing].  

O último item é o custo dos gateways de rede virtual.  Se você não precisa dos gateways para algo como VPNs Site a Site, está pagando para que eles deem suporte ao recurso Integração de rede virtual.  Há detalhes sobre esses custos aqui: [Preço de Gateway de VPN][VNETPricing].  

## <a name="troubleshooting"></a>Solucionar problemas
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas.  Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo.  Há duas experiências de console que você pode usar.  Uma é o console Kudu e a outra é o console que você pode acessar no Portal do Azure.  Para acessar o console do Kudu em seu aplicativo, vá para Ferramentas -> Kudu.  Isso é o mesmo que ir para [nomedosite].scm.azurewebsites.net.  Depois de aberto, vá para a guia Console de depuração.  Para obter a console hospedado no portal do Azure, em seu aplicativo, vá para Ferramentas -> Console.  

#### <a name="tools"></a>Ferramentas
As ferramentas ping, nslookup e tracert não funcionarão no console devido a restrições de segurança.  Para compensar essa ausência, duas ferramentas separadas foram adicionadas.  Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe.  A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar a nameresolver para verificar os nomes de host de que seu aplicativo depende.  Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta.  Essa ferramenta é chamada tcpping.exe e a sintaxe é:

    tcpping.exe hostname [optional: port]

Essa ferramenta informará se você pode acessar um host e uma porta específicos, mas não executará a mesma tarefa realizada pelo utilitário ping baseado em ICMP.  O utilitário ping ICMP informará se o host estiver em execução.  Com tcpping, você descobre se pode acessar uma porta específica em um host.  

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurando o acesso a recursos hospedados por VNET
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos.  Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho** Se houver um firewall no caminho, você atingirá o tempo limite de TCP.  Nesse caso, de 21 segundos.  Use a ferramenta tcpping para testar a conectividade.  Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso.  
* **DNS não está acessível** O tempo limite do DNS é de três segundos por servidor DNS.  Se você tiver dois servidores DNS, será de seis segundos.  Use nameresolver para ver se o DNS está funcionando.  Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNET está configurada.
* **Intervalo IP P2S inválido** O intervalo IP ponto a site deve estar dentro dos intervalos IP privados RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) Se o intervalo usar IPs fora disso, nada funcionará.  

Se esses itens não resolverem seu problema, procure por coisas simples primeiro, como:  

* O Gateway é mostrado como estando em execução no Portal?
* Os certificados são mostrados como sincronizados?
* Alguém alterou a configuração de rede sem fazer uma “Sincronização de Rede” nos ASPs afetados? 

Se o gateway estiver inativo, ative-o.  Se os certificados estiverem fora de sincronia, vá para a exibição ASP da integração da rede virtual e pressione "Sincronizar Rede".  Se você suspeitar de que houve uma alteração na configuração da VNET e ela não foi sincronizada com seus ASPs, vá para o modo de exibição ASP da Integração VNET e pressione "Sincronizar Rede". Lembrete: isso paralisará momentaneamente a conexão com sua VNET e com seus aplicativos.  

Se tudo estiver bem, você precisará se aprofundar um pouco mais:

* Há outros aplicativos que usam a Integração VNET para acessar recursos na mesma VNET? 
* Você consegue ir ao console do aplicativo e usar tcpping para acessar outros recursos na sua VNET?  

Se alguma das opções acima é verdadeira, a integração de rede virtual está boa e o problema está em outro lugar.  Essa hipótese é mais complicada, já que não há uma maneira simples de ver por que você não consegue acessar o host:porta.  Algumas das causas incluem:

* você tem um firewall no seu host que impede o acesso à porta do aplicativo usando o intervalo de IP ponto a site.  O cruzamento de sub-redes geralmente exige acesso Público.
* o host de destino está inoperante
* seu aplicativo está inoperante
* você tinha o IP ou nome de host incorreto
* seu aplicativo está escutando em uma porta diferente da que você esperava.  Você pode verificar isso indo até o host e usando "netstat - aon" no prompt de comando.  Isso mostrará qual ID de processo está escutando em qual porta.  
* os grupos de segurança de rede estão configurados de modo a impedir o acesso ao host do aplicativo e à porta do intervalo de IP ponto a site.

Lembre-se de que você não sabe quais IPs no intervalo de IP ponto a site seu aplicativo usará; portanto, é necessário permitir o acesso a todo o intervalo.  

As etapas de depuração adicionais incluem:

* fazer logon em outra VM na rede virtual e tentar acessar o host:porta do recurso a partir daí.  Existem alguns utilitários de ping do TCP que você pode usar para essa finalidade, ou até mesmo usar o telnet, se for o caso.  O objetivo aqui é somente determinar se há conectividade de outra máquina virtual. 
* colocar um aplicativo em outra VM e testar o acesso àquele host e porta usando o console do aplicativo  
  ####<a name="on-premises-resources"></a>on-premises resources####
  Se não puder alcançar os recursos locais, a primeira medida a adotar será verificar se você consegue alcançar um recurso na VNET.  Se isso estiver funcionando, as próximas etapas são muito fáceis.  De uma VM na sua VNET, você precisa tentar alcançar o aplicativo local.  Você pode usar o telnet ou um utilitário de ping de TCP.  Se sua VM não conseguir acessar o recurso local, primeiro verifique se a conexão de VPN site a site está funcionando.  Se estiver funcionando, verifique os mesmos pontos observados anteriormente, bem como a configuração e o status do gateway local.  

Agora, se sua VM hospedada na VNET puder acessar seu sistema local, mas seu aplicativo não, provavelmente, isso ocorre devido a um dos seguintes motivos:

* suas rotas não estão configuradas com os intervalos de IP ponto a site em seu gateway local
* seus grupos de segurança de rede estão bloqueando o acesso para o intervalo de IP Ponto a Site
* os firewalls locais estão bloqueando o tráfego do intervalo de IP Ponto a Site
* você tem uma UDR (Rota Definida pelo Usuário) em sua VNET que impede que o tráfego baseado em Ponto a Site acesse sua rede local

## <a name="hybrid-connections-and-app-service-environments"></a>Conexões híbridas e ambientes de serviço de aplicativo
Há três recursos que habilitam o acesso a recursos hospedados em rede virtual.  Eles são:

* Integração VNET
* Conexões Híbridas
* Ambientes de Serviço de Aplicativo

As conexões híbridas exigem que você instale um agente de retransmissão chamado HCM (gerente de conexões híbridas) na sua rede.  O HCM precisa ser capaz de se conectar ao Azure e também a seu aplicativo.  Essa solução é especialmente boa usando uma rede remota, como sua rede local ou mesmo outra rede hospedada em nuvem, já que não exige um ponto de extremidade acessível pela Internet.  O HCM só é executado no Windows e você pode ter até cinco instâncias em execução para fornecer alta disponibilidade.  No entanto, as conexões híbridas só dão suporte a TCP e cada ponto de extremidade de HC tem que corresponder a uma combinação de host:porta específica.  

O recurso Ambiente de Serviço de Aplicativo permite a execução de uma instância do Serviço de Aplicativo do Azure em sua rede virtual.  Isso permite que seus aplicativos acessem os recursos de sua rede virtual sem etapas adicionais.  Alguns dos outros benefícios de um Ambiente de Serviço de Aplicativo é que você pode usar oito trabalhadores de núcleo dedicados com 14 GB de RAM.  Outra vantagem é que você pode escalonar o sistema para atender às suas necessidades.  Ao contrário de ambientes multilocatários, em que o ASP tem tamanho limitado, em um ASE, você controlar quantos recursos deseja dar ao sistema.  Em relação ao foco de rede deste documento, uma das coisas que você obtém com um ASE que não obtém da Integração VNET é poder funcionar com VPN ExpressRoute.  

Embora haja sobreposição de caso de uso, nenhum desses recursos pode substituir o outro.  Saber qual recurso usar está vinculado às suas necessidades e como você deseja usá-lo.  Por exemplo:

* Se você for um desenvolvedor e quiser simplesmente executar um site no Azure que acesse o banco de dados na estação de trabalho na sua mesa, a coisa mais fácil de usar é o Conexões Híbridas.  
* Se você tem uma grande organização que deseja colocar um grande número de propriedades da Web na nuvem pública e gerenciá-las em sua própria rede, a melhor opção é o Ambiente de Serviço de Aplicativo.  
* Se você tiver uma quantidade de aplicativos hospedados no Serviço de Aplicativo e simplesmente quiser acessar recursos na VNET, a melhor opção será a Integração VNET.  

Além dos casos de uso, há alguns aspectos relativos à simplicidade.  Se sua VNET já estiver conectada à sua rede local, o uso da Integração VNET ou de um Ambiente de Serviço de Aplicativo será uma maneira fácil de consumir recursos locais.  Por outro lado, se sua VNET não estiver conectada à sua rede local, será muito mais trabalhoso configurar uma VPN site a site com a VNET do que instalar o HCM.  

Além das diferenças funcionais, há também diferenças de preço.  O recurso Ambiente de Serviço de Aplicativo é uma oferta do serviço Premium, mas oferece a maioria das possibilidades de configuração de rede, além de outros recursos incríveis.  A Integração de rede virtual pode ser usada com ASPs Standard ou Premium e é perfeita para consumir com segurança recursos em sua rede virtual do Serviço de Aplicativo multilocatário.  O Conexões Híbridas atualmente depende de um conta BizTalk, com preços que variam de gratuito a mais caros baseado na quantidade necessária.  Quando se trata de trabalhar em várias redes, no entanto, não há nenhum outro recurso como Conexões Híbridas, que pode permitir que você acesse recursos em mais de cem redes separadas.    

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[V2VNETPortal]: https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal
[VPNERCoex]: http://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-coexist-resource-manager
[ASE]: http://docs.microsoft.com/azure/app-service/app-service-environment/intro


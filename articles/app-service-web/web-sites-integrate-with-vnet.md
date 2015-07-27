<properties 
	pageTitle="Integrar um aplicativo Web a uma Rede Virtual do Azure" 
	description="Mostra como conectar um aplicativo Web do Azure no Serviço de Aplicativo do Azure a uma rede de virtual do Azure nova ou existente" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Integrar um aplicativo Web a uma Rede Virtual do Azure #
Este documento descreve o recurso de visualização de integração de rede virtual e mostra como configurá-lo com os aplicativos Web no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Se você não está familiarizado como as Redes virtuais do Azure, esta é uma capacidade que permitirá que você desenvolva soluções híbridas com seus recursos locais e do Azure.

Esta integração concede ao seu aplicativo Web acesso a recursos da sua rede virtual, mas não concede acesso ao seu aplicativo Web por meio da rede virtual. Alguns cenários padrão estão onde o seu aplicativo Web precisa de acesso a um banco de dados ou aos serviços Web que estão sendo executados em máquinas virtuais em sua rede virtual ou até em seu próprio data center. Ele não permite que você monte uma unidade. No momento, ele também não dá suporte para habilitar a integração com os sistemas de autenticação na sua rede virtual. O recurso está no modo de visualização e continuará a ser aperfeiçoado antes de alcançar a GA.

Para obter mais detalhes sobre as Redes virtuais do Azure consulte a Visão geral da Rede Virtual do Azure sobre os casos de uso e benefícios de uma Rede virtual do Azure.

## Introdução ##
Veja aqui algumas coisas para se ter em mente antes de conectar seu aplicativo Web a uma rede virtual.

1.	Os aplicativos Web podem apenas ser conectados a uma rede virtual se estão executando em um Plano de Serviço de Aplicativo que esteja na faixa de preço ‘Padrão’. Aplicativos Web nos planos Básico, Gratuito e Compartilhado não podem se conectar a uma rede virtual.
2.	Se a sua rede virtual de destino já existe, ela deve ter a opção ponto a site habilitada com um gateway de roteamento dinâmico antes que possa ser conectada a um aplicativo Web. Você não pode habilitar a opção ponto a site da VPN (rede virtual privada) com se o gateway está configurado com roteamento estático.
3.	Você pode ter apenas 5 redes configuradas no seu Plano de Serviço de Aplicativo. Um aplicativo Web só pode estar conectado a uma rede de cada vez. Estas 5 redes podem ser usadas por qualquer número de aplicativos Web no mesmo Plano de Serviço de Aplicativo.  

Você tem a opção de se conectar a uma rede virtual nova ou existente. Se você criar uma nova rede então um gateway será pré-configurado para você. Observe que ao criar e configurar uma nova rede virtual levará alguns minutos.

Se seu aplicativo Web não estiver em um Plano de Serviço de Aplicativo Padrão, a interface do usuário permite que você saiba e fornece acesso a faixas de preço, caso você deseje fazer uma atualização.

![](./media/web-sites-integrate-with-vnet/upgrade-to-standard.png)

## Como o sistema funciona ##
Nos bastidores, esse recurso usa tecnologia ponto a site de VPN para conectar seu aplicativo Web à sua rede virtual. Aplicativos Web no Serviço de Aplicativo do Azure têm uma arquitetura de sistema multilocatário, que impede o provisionamento de um aplicativo Web diretamente em uma rede virtual como ocorre com máquinas virtuais. Ao desenvolver uma tecnologia ponto a site, limitamos o acesso à rede apenas à máquina virtual que está hospedando o aplicativo Web. O acesso à rede é ainda mais limitado nestes hosts do aplicativo Web, de modo que seus aplicativos Web poderão acessar apenas as redes que você configurar para eles acessarem.

O trabalho exigido para proteger suas redes, disponibilizando-as somente aos aplicativos Web que precisam de acesso, impede a capacidade de criar conexões SMB. Enquanto você pode acessar recursos remotos, isto não inclui ser capaz de montar uma unidade remota.

![](./media/web-sites-integrate-with-vnet/how-it-works.png)
 
Se você ainda não configurou um servidor DNS com sua rede virtual será necessário usar endereços IP. Certifique-se de expor suas portas para seus pontos de extremidade desejados através do seu firewall. Quando se trata de testar sua conexão, o único método atualmente disponível é usar um aplicativo Web ou Trabalho Web que faz uma chamada para seu ponto de extremidade desejado. Ferramentas como ping ou nslookup não funcionam através do console Kudu. Esta é uma área que será aperfeiçoada no futuro próximo.

## Conecte-se a uma rede pré-existente ##
Para se conectar um site a uma Rede Virtual vá para a folha do aplicativo Web, clique no bloco Rede virtual na seção Sistemas de rede e selecione umas das redes pré-existentes.

![](./media/web-sites-integrate-with-vnet/connect-to-existing-vnet.png)
 
O sistema então criará um certificado para autenticar com sua rede virtual se for o primeiro aplicativo Web na sua assinatura a estabelecer uma conexão àquela rede. Para ver o certificado, vá para o [ Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), navegue para Redes virtuais, selecione a rede e selecione a guia Certificados.

Na imagem acima você pode ver uma rede chamada cantConnectVnet que está esmaecida e não pode ser selecionada. Há apenas dois motivos que este deve ser o caso. Significa que você não tem a VPN ponto a site habilitado na sua rede ou você não forneceu um gateway de roteamento dinâmico na sua rede virtual. Quando ambos os itens são satisfeitos então você poderá secionar a rede virtual para integração com seu aplicativo Web.

## Criar e conectar-se a uma nova rede virtual ##
Além de se conectar a uma rede virtual já existente, você também pode criar uma nova rede virtual na interface de usuário do Portal do Azure e conectar-se automaticamente a ela. Para fazer isto siga o mesmo caminho para atingir a interface de usuário da Rede Virtual e selecione Criar nova rede virtual. A interface de usuário que abre permite que você nomeie a rede, especifique o espaço de endereço e defina os endereços para os servidores DNS para serem usados pela rede virtual.

![](./media/web-sites-integrate-with-vnet/create-new-vnet.png)
 
A criação da nova rede virtual com gateways configurados podem levar até 30 minutos para concluir. Durante este período a interface de usuário informará a você que ainda está trabalhando nisto e exibirá a seguinte mensagem.

![](./media/web-sites-integrate-with-vnet/new-vnet-progress.png)

Depois que a rede foi unida ao aplicativo Web, o aplicativo terá acesso aos recursos nessa rede virtual por TCP ou UDP. Se você deseja acessar recursos no seu sistema local que estão disponíveis para sua rede virtual por meio de VPN site a site, será necessário adicionar rotas para sua própria rede corporativa para permitir que o tráfego vá de sua rede para os endereços ponto a site configurados na sua rede virtual.

Depois de concluir com êxito a integração, o Portal do Azure exibirá informações básicas sobre a conexão, fornecerá uma maneira de desconectar o aplicativo Web da rede e também fornecerá a você uma maneira de sincronizar os certificados usados para autenticar a conexão. A sincronização pode ser necessária se um certificado expirou ou foi revogado.

![](./media/web-sites-integrate-with-vnet/vnet-status-portal.png)

##Gerenciando a conexão de rede virtual##
Você pode ver uma lista de todas as redes virtuais atualmente associadas a aplicativos Web em um Plano de Serviço de Aplicativo visitando a folha do Plano de Serviço de Aplicativo. Você pode ter no máximo 5 redes associadas a um Plano de Serviço de Aplicativo 'Padrão'.

Caso o Plano de Serviço de Aplicativo seja reduzido a um plano inferior como o Gratuito, Compartilhado ou Básico, então as conexões de rede virtual que são usadas pelos aplicativos Web naquele plano serão desabilitadas. Caso o plano seja reduzido de volta para um plano Padrão então estas conexões de rede serão reestabelecidas.

Neste momento não é possível no Azure pegar uma máquina virtual existente e movê-la para um rede virtual. A máquina virtual precisa ser fornecida para aquela rede virtual durante a criação.

## Acessar recursos locais ##
Ao trabalhar com uma rede virtual que foi configurada com o VPN site a site, há uma etapa adicional necessária para fornecer acesso aos seus recursos locais por meio do aplicativo Web. Será necessário adicionar rotas para sua rede local para permitir que o tráfego vá de sua rede para os endereços ponto a site configurados na sua rede virtual. Para ver o intervalo IP para sua conectividade ponto a site vá para a área Rede no Portal do Azure conforme demonstrado aqui.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise.png)

## Certificados ##
Para estabelecer uma conexão segura com sua rede virtual, há uma troca de certificados. Você pode ver a impressão digital para o certificado público que os Aplicativos Web do Azure geram do portal Rede atual conforme exibido abaixo.

![](./media/web-sites-integrate-with-vnet/vpn-to-onpremise-certificate.png)

Se os certificados saem de sincronia por algum motivo, como exclusão acidental do portal Rede, então a conectividade será interrompida. Para corrigir as coisas, há uma ação Sincronizar Conexão na interface de usuário de rede virtual do seu aplicativo Web que restabelecerá a conexão.

Esta ação deve ser usado se você adicionar um DNS à sua rede virtual ou se você adicionar a VNP site a site à sua rede.

![](./media/web-sites-integrate-with-vnet/vnet-sync-connection.png)

## Comparar e contrastar com as Conexões Híbridas ##
Há outro recurso oferecido pelos Aplicativos Web do Azure chamado de Conexões Híbridas que é semelhante, de alguma forma, à integração da Rede Virtual. Enquanto há alguma sobreposição de caso de uso, nenhum dos recursos pode substitui o outro. Com as Conexões Híbridas você pode estabelecer conexões a vários pontos de extremidade de aplicativos em uma combinação de redes. O recurso Redes virtuais conecta o seu aplicativo Web a uma rede virtual que pode ser conectada à sua rede local. Isto funciona bem de os seus recursos estiverem todos no escopo daquela rede.

Outra diferença é que você precisa instalar um agente de retransmissão para as Conexões Híbridas funcionarem. Este agente precisa executar em uma instância do Windows Server. Com o recurso da Rede Virtual não há nada para instalar e ele habilita o aceso aos recursos remotos independentemente dos sistemas operacionais hospedados.

Há também diferenças na faixa de preço neste momento entre os dois recursos. Isto ocorre porque no nível menos dispendioso o recurso Conexões Híbridas é extremamente útil para cenários de desenvolvimento e teste e somente fornece acesso a um pequeno número de pontos de extremidade. O recurso de rede virtual fornece a você acesso a tudo na VNET ou ao que está conectado a ela.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->
<properties
   pageTitle="Roteamento Assimétrico | Microsoft Azure"
   description="Este artigo aborda problemas que um cliente pode enfrentar com o roteamento assimétrico em sua rede quando ele tem vários links para um destino."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# Roteamento Assimétrico com vários caminhos de rede

Este artigo explica como o encaminhamento e o retorno do tráfego podem adotar rotas diferentes quando há vários caminhos disponíveis entre a origem e o destino.

Para compreender o roteamento assimétrico, precisamos compreender dois conceitos. Um deles é o impacto de vários caminhos de rede. O outro é o comportamento dos dispositivos que mantêm o estado, como firewalls. Esses dispositivos são chamados de dispositivos com estado. Uma combinação desses dois fatores cria cenários em que o tráfego é ignorado por um dispositivo com estado, pois não encontrou o tráfego originado por si próprio.

## Vários caminhos de rede

Quando uma rede corporativa tem apenas um link para a Internet através de seu provedor de serviços de Internet, todo o tráfego para e da Internet passa pelo mesmo caminho. Geralmente, as empresas adquirem vários circuitos, como caminhos redundantes, para melhorar o tempo de atividade de rede. Nesses casos, é possível que o tráfego que vai para fora da rede em direção à Internet passe por um link, enquanto o tráfego de retorno passa através de uma conexão diferente. Esse fenômeno é conhecido como roteamento assimétrico, em que o tráfego inverso usa um caminho diferente do fluxo original.

![Routing3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Embora a descrição anterior seja para a Internet, ela se aplica a outras combinações de vários caminhos. Exemplos são um caminho de Internet e um caminho particular para o mesmo destino, vários caminhos particulares para o mesmo destino etc.

Cada roteador ao longo do caminho de origem para o destino calcula o melhor caminho para alcançar um destino, com base em seu cálculo de melhor caminho para alcançar o destino. A determinação do melhor caminho possível baseia-se em dois fatores principais.

1.	O roteamento entre redes externas é baseado no protocolo de roteamento Border Gateway Protocol, normalmente conhecido como BGP. O BGP usa anúncios de vizinhos, aplica várias etapas a eles para determinar o melhor caminho para o destino e o instala em sua tabela de roteamento.
2.	Comprimento da máscara de sub-rede associada a uma rota. Se forem recebidos vários anúncios do mesmo endereço IP, mas máscaras de sub-rede diferentes, o anúncio com a máscara de sub-rede mais longa será preferencial, pois ela é considerada uma rota mais específica.

## Dispositivos com estado

Os roteadores examinam o cabeçalho IP do pacote para fins de roteamento. No entanto, existem dispositivos que verificam o pacote de forma ainda mais aprofundada. Normalmente, esses dispositivos examinam os cabeçalhos Layer4 (TCP/UDP) ou até mesmo Layer7 (Application Layer) . Trata-se de dispositivos de otimização de largura de banda ou dispositivos de segurança. O firewall é um exemplo comum de dispositivos com estado. O firewall permite ou nega um pacote através das interfaces com base em vários campos, como protocolo, porta TCP/UDP e cabeçalhos de URL. A inspeção de pacotes coloca grande parte da carga de processamento no dispositivo. Para melhorar o desempenho, o firewall inspeciona o primeiro pacote de um fluxo. Se o pacote for permitido, ele manterá as informações de fluxo em sua tabela de estado. Todos os pacotes subsequentes relacionados a esse fluxo são permitidos com base na decisão inicial. Portanto, quando um pacote que faz parte de um fluxo existente chega ao firewall e o firewall não tem informações de estado anteriores sobre ele, o firewall descarta esse pacote.

## Roteamento assimétrico com a Rota Expressa

Quando você se conecta à Microsoft por meio da Rota Expressa, ocorrem as alterações a seguir em sua rede.

1.	Você tem vários links para a Microsoft. Um link é sua conexão com a Internet existente e outro é por meio da Rota Expressa. Parte do tráfego para a Microsoft pode passar pela Internet, mas voltar por meio da Rota Expressa ou vice-versa.
2.	Você recebe endereços IP mais específicos por meio da Rota Expressa. Portanto, o tráfego da rede para a Microsoft para serviços oferecidos por meio da Rota Expressa sempre prefere a Rota Expressa.

Para entender o impacto dos dois itens acima, vamos analisar alguns cenários. Vamos supor que você tenha apenas um circuito para a Internet e consuma os serviços da Microsoft via Internet. O tráfego de sua rede para a Microsoft e de volta atravessa o mesmo link da Internet e passa através do firewall. O firewall registra o fluxo ao ver o primeiro pacote, e pacotes de retorno são permitidos quando o fluxo existe na tabela de estado.

![Roteamento1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


Agora você ativa a Rota Expressa e consome serviços oferecidos pela Microsoft por meio da Rota Expressa. Todos os outros serviços da Microsoft são consumidos por meio da Internet. Você pode implantar um firewall separado em sua borda conectando-se à Rota Expressa. A Microsoft anunciará prefixos mais específicos à sua rede por meio da Rota Expressa para serviços específicos. Sua infraestrutura de roteamento escolherá a Rota Expressa como caminho preferido para esses prefixos. Se você não estiver anunciando seus endereços IP públicos para a Microsoft na Rota Expressa, a Microsoft se comunicará com seus endereços IP públicos por meio da Internet. Assim, o tráfego encaminhado de sua rede para a Microsoft usará a Rota Expressa, enquanto o tráfego inverso da Microsoft usará a Internet. Quando o firewall na borda vir um pacote de resposta para um fluxo não encontrado na tabela de estado, ele descartará o tráfego de retorno.

Se optar por usar o mesmo pool NAT para a Rota Expressa e para a Internet, você verá problemas semelhantes com os clientes em endereços IP privados em sua rede. A solicitação de serviços como o Windows Update passará pela Internet, pois endereços IP para esses serviços não são divulgados pela Rota Expressa. No entanto, o tráfego de retorno voltará via Rota Expressa. Se a Microsoft receber um endereço IP com a mesma máscara de sub-rede da Internet e da Rota Expressa, preferirá a Rota Expressa à Internet. Se um firewall ou outro dispositivo com estado na borda da rede, voltado para a Rota Expressa, não tiver informações anteriores sobre o fluxo, ele descartará os pacotes que pertencem ao fluxo.

## Soluções para o Roteamento Assimétrico

Há duas maneiras de resolver o problema do Roteamento Assimétrico. Uma delas é via roteamento e a outra é por meio de SNAT (NAT com base na origem).

1. Roteamento

    - Verifique se seus endereços IP públicos são anunciados para os links WAN apropriados. Por exemplo, se você quiser usar a Internet para o tráfego de autenticação e a Rota Expressa para o tráfego de email. Você não deve anunciar seus endereços IP públicos do ADFS na Rota Expressa. Da mesma forma, o servidor do AD FS local não deve ser exposto para endereços IP recebidos pela Rota Expressa. As rotas recebidas pela Rota Expressa são mais específicas. Portanto, elas tornarão a Rota Expressa o caminho preferencial para o tráfego de autenticação para a Microsoft, causando o roteamento assimétrico.

    - Se quiser usar a Rota Expressa para autenticação, você deverá verificar se está anunciando os endereços IP públicos do ADFS na Rota Expressa sem NAT. Assim, o tráfego proveniente da Microsoft para o servidor ADFS local passará pela Rota Expressa, enquanto o tráfego de retorno do cliente para a Microsoft usará a Rota Expressa, pois ela é preferencial em vez da Internet.

2. NAT com base em origem

	Outra maneira de solucionar problemas de Roteamento Assimétrico é por meio de SNAT (NAT de origem). Por exemplo, se você não anunciou o endereço IP público do servidor SMTP local na Rota Expressa, tendo a intenção de usar a Internet para essa comunicação. Uma solicitação originada da Microsoft para o servidor SMTP local atravessa a Internet. Você usar o NAT de origem para a solicitação de entrada para um endereço IP interno. O tráfego reverso do servidor SMTP irá para o firewall de borda (usado para NAT), em vez da Rota Expressa. Dessa forma, o tráfego de retorno voltará via Internet.


![Roteamento2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## Detecção de Roteamento Assimétrico

Traceroute é a melhor maneira de garantir que o tráfego atravesse o caminho esperado. Se você espera que o tráfego do servidor SMTP local para a Microsoft use o caminho da Internet, use traceroute do servidor SMTP para o Office 365. O resultado validará que o tráfego realmente sai de sua rede para a Internet, não na direção da Rota Expressa.

<!---HONumber=AcomDC_0824_2016-->
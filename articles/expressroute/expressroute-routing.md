<properties
   pageTitle="Requisitos de roteamento para Rota Expressa | Microsoft Azure"
   description="Esta página fornece requisitos detalhados para a configuração e gerenciamento de roteamento para circuitos da Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="cherylmc"/>


# Requisitos de roteamento da Rota Expressa  

Para se conectar aos serviços de nuvem da Microsoft usando a Rota Expressa, você precisará configurar e gerenciar o roteamento. Alguns provedores de conectividade oferecem a configuração e o gerenciamento de roteamento como um serviço gerenciado. Verifique se o seu provedor de conectividade oferece esse serviço. Se não oferecer, você deverá atender aos requisitos descritos abaixo.

Consulte o artigo sobre [Circuitos e domínios de roteamento](expressroute-circuit-peerings.md) para obter uma descrição das sessões de roteamento que precisam ser configuradas para possibilitar a conectividade.

**Observação:** a Microsoft não dá suporte a protocolos de redundância de roteador (HSRP e VRRP) para configurações de alta disponibilidade. Contamos com um par redundante de sessões BGP por emparelhamento para alta disponibilidade.

## Endereços IP para emparelhamentos

Você precisará reservar alguns blocos de endereços IP para configurar o roteamento entre a rede e roteadores de borda (MSEEs) corporativos da Microsoft. Esta seção fornece uma lista de requisitos e descreve as regras para a maneira como esses endereços IP devem ser adquiridos e usados.

### Endereços IP para emparelhamento privado do Azure

Você pode usar endereços IP privados ou endereços IP públicos para configurar os emparelhamentos. O intervalo de endereços usado para configurar rotas não deve se sobrepor aos intervalos de endereços usados para criar redes virtuais no Azure.

 - Você deve reservar uma sub-rede /29 ou duas sub-redes /30 para interfaces de roteamento.
 - As sub-redes usadas para roteamento podem ser endereços IP privados ou endereços IP públicos.
 - As sub-redes não devem entrar em conflito com o intervalo reservado pelo cliente para uso na nuvem da Microsoft.
 - Se uma sub-rede /29 for usada, será dividida em duas sub-redes /30. 
	 - A primeira sub-rede /30 será usada para o link principal e a segunda sub-rede /30 será usada para o link secundário.
	 - Para cada uma das sub-redes /30, é necessário o primeiro endereço IP da sub-rede /30 em seu roteador. A Microsoft usará o segundo endereço IP da sub-rede /30 para configurar uma sessão BGP.
	 - Você deve instalar ambas as sessões BGP para que nosso [SLA de disponibilidade](http://azure.microsoft.com/support/legal/sla/) seja válido.  

#### Exemplo para emparelhamento privado

Se você optar por usar a.b.c.d/29 para configurar o emparelhamento, ela será dividida em duas sub-redes /30. No exemplo a seguir, veremos como a sub-rede a.b.c.d/29 é usada.

a.b.c.d/29 será dividida em a.b.c.d/30 e a.b.c.d+4/30 e passada à Microsoft por meio das APIs de provisionamento. Você usará a.b.c.d+1 como o IP VRF para o PE Primário, e a Microsoft consumirá a.b.c.d+2 como o IP VRF para o MSEE primário. Você usará a.b.c.d+5 como o IP VRF para o PE secundário, e a Microsoft usará a.b.c.d+6 como o IP VRF para o MSEE secundário.

Considere um caso em que você seleciona 192.168.100.128/29 para configurar o emparelhamento privado. 192.168.100.128/29 inclui os endereços de 192.168.100.128 a 192.168.100.135, entre os quais:

- 192\.168.100.128/30 será atribuído ao link1, com o provedor usando 192.168.100.129 e a Microsoft usando 192.168.100.130.
- 192\.168.100.132/30 será atribuído ao link2, com o provedor usando 192.168.100.133 e a Microsoft usando 192.168.100.134.

### Endereços IP para o emparelhamento público do Azure e o emparelhamento da Microsoft

Você deve usar endereços IP públicos que possui para configurar as sessões BGP. A Microsoft deve ser capaz de verificar a propriedade dos endereços IP por meio de Registros da Internet de Roteamento e Registros de Roteamento da Internet.

- Você deve usar uma sub-rede /29 exclusiva ou duas sub-redes /30 para configurar o emparelhamento BGP para cada emparelhamento por circuito da Rota Expressa (se houver mais de um). 
- Se uma sub-rede /29 for usada, será dividida em duas sub-redes /30. 
	- A primeira sub-rede /30 será usada para o link principal e a segunda sub-rede /30 será usada para o link secundário.
	- Para cada uma das sub-redes /30, é necessário usar o primeiro endereço IP da sub-rede /30 em seu roteador. A Microsoft usará o segundo endereço IP da sub-rede /30 para configurar uma sessão BGP.
	- Você deve instalar ambas as sessões BGP para que nosso [SLA de disponibilidade](http://azure.microsoft.com/support/legal/sla/) seja válido.

Verifique se o endereço IP e o número de AS estão registrados em um dos registros listados abaixo.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPE NCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)
- [LEVEL3](http://rr.Level3.net/)


## Intercâmbio de roteamento dinâmico

O intercâmbio de roteamento será por meio do protocolo eBGP. As sessões EBGP são estabelecidas entre os MSEEs e os roteadores. A autenticação de sessões BGP não é um requisito. Se necessário, um hash MD5 pode ser configurado. Consulte [Configurar roteamento](expressroute-howto-routing-classic.md) e [Fluxos de trabalho de provisionamento e estados de circuito](expressroute-workflows.md) para saber mais sobre como configurar as sessões BGP.

## Números de sistema autônomos

A Microsoft usará AS 12076 para o emparelhamento público do Azure, o emparelhamento privado do Azure e o emparelhamento da Microsoft . Reservamos AS 65515 para uso interno. Há suporte para números AS de 16 e 32 bits. Você pode usar números AS privados para emparelhamento privado do Azure. Você deve usar números AS públicos registrados para você para o emparelhamento público do Azure e o emparelhamento da Microsoft.

Não há requisitos de simetria de transferência de dados. Os caminhos de encaminhamento e retorno podem atravessar pares de roteadores diferentes. Rotas idênticas devem ser anunciadas de qualquer um dos lados entre vários pares de circuitos que pertencem a você. As métricas de rotas não precisam ser idênticas.

## Agregação de rotas e limites de prefixo

Damos suporte a até 4.000 prefixos anunciados a nós por meio do emparelhamento privado do Azure. Esse número poderá ser aumentado para até 10.000 prefixos se o complemento Premium da Rota Expressa estiver habilitado. Aceitamos até 200 prefixos por sessão BGP para o emparelhamento público do Azure e o emparelhamento da Microsoft.

A sessão BGP será descartada se o número de prefixos exceder o limite. Aceitaremos rotas padrão somente no link de emparelhamento privado. O provedor deve filtrar a rota padrão e endereços IP privados (RFC 1918) dos caminhos de emparelhamento público do Azure e emparelhamento da Microsoft.

## Roteamento de tráfego e de roteamento entre regiões

A Rota Expressa não pode ser configurada como roteadores de tráfego. Você precisará contar com seu provedor de conectividade para serviços de roteamento de tráfego.

## Anunciando rotas padrão

As rotas padrão são permitidas apenas em sessões de emparelhamento privado do Azure. Nesse caso, rotearemos todo o tráfego das redes virtuais associadas para sua rede. Anunciar rotas padrão para emparelhamento privado fará com que o caminho da Internet do Azure seja bloqueado. Você deve contar com sua borda corporativa para rotear o tráfego de e para a Internet para serviços hospedados no Azure.

 Para habilitar a conectividade com outros serviços do Azure e serviços de infraestrutura, verifique se um dos seguintes itens está em vigor:

 - O emparelhamento público do Azure está habilitado para rotear o tráfego para pontos de extremidade públicos
 - Você usa o roteamento definido pelo usuário para permitir a conectividade da Internet para cada sub-rede que requer conectividade com a Internet.

**Observação:** anunciar rotas padrão interromperá o Windows e outras ativações de licenças de VM. Siga as instruções [aqui](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) para solucionar esse problema.

## Suporte a comunidades BGP

Esta seção fornece uma visão geral de como as comunidades BGP serão usadas com a Rota Expressa. A Microsoft anunciará rotas nos caminhos de emparelhamento público e da Microsoft com rotas marcadas com valores de comunidade apropriados. A lógica para fazer isso e os detalhes de valores de comunidade são descritos abaixo. No entanto, a Microsoft não adotará valores de comunidade marcados para rotas anunciadas à Microsoft.

Se estiver se conectando à Microsoft por meio da Rota Expressa em qualquer local de emparelhamento dentro de uma região geopolítica, você terá acesso a todos os serviços de nuvem da Microsoft em todas as regiões dentro dos limites geopolíticos.

Por exemplo, se você estiver conectado à Microsoft em Amsterdã por meio da Rota Expressa, terá acesso a todos os serviços de nuvem da Microsoft hospedados no Norte da Europa e na Europa Ocidental.

Consulte a página [Locais de emparelhamento e parceiros da Rota Expressa](expressroute-locations.md) para obter uma lista detalhada das regiões geopolíticas, regiões associadas do Azure e locais de emparelhamento correspondentes da Rota Expressa.

Você pode adquirir mais de um circuito da Rota Expressa por região geopolítica. Ter várias conexões oferece vantagens significativas para a alta disponibilidade devido à redundância geográfica. Em casos em que há vários circuitos da Rota Expressa, você recebe o mesmo conjunto de prefixos anunciados da Microsoft nos caminhos de emparelhamento público e da Microsoft. Isso significa que você terá vários caminhos de sua rede até a Microsoft. Potencialmente, isso pode fazer com que decisões de roteamento não ideais sejam tomadas em sua rede. Como resultado, você pode ter experiências de conectividade não ideal para diferentes serviços.

A Microsoft marcará prefixos anunciados por meio do emparelhamento público e do emparelhamento da Microsoft com valores de comunidade BGP apropriados indicando a região em que os prefixos estão hospedados. Você pode contar com os valores de comunidade para tomar decisões de roteamento apropriadas e oferecer o roteamento ideal aos clientes.

| **Região Geopolítica** | **Região do Microsoft Azure (o mesmo se aplica ao Office 365)** | **Valor de comunidade BGP** |
|---|---|---|
| **EUA** |	Leste dos EUA | 12076:3004 |
| | Leste dos EUA 2 | 12076:3005 |
| | Oeste dos EUA | 12076:3006 |
| | Centro dos EUA | 12076:3009 |
| | Centro-Norte dos EUA | 12076:3007 |
| | Centro-Sul dos Estados Unidos | 12076:3008 |
| **América do Sul** | Sul do Brasil | 12076:3014 |
| **Europa** | Norte da Europa | 12076:3003 |
| | Europa Ocidental | 12076:3002 |
| **Pacífico Asiático** | Ásia Oriental | 12076:3010 |
| | Sudeste Asiático | 12076:3011 |
| **Japão** | Leste do Japão | 12076:3012 |
| | Oeste do Japão | 12076:3013 |
| **Austrália** | Leste da Austrália | 12076:3015 |
| | Sudeste da Austrália | 12076:3016 |
| **Índia** | Sul da Índia | 12076:3019 |
| | Oeste da Índia | 12076:3018 |
| | Centro da Índia | 12076:3017 |

Todas as rotas anunciadas pela Microsoft serão marcadas com o valor de comunidade apropriado.


Além disso, a Microsoft também marcará prefixos com base no serviço ao qual eles pertencem. Isso se aplica somente ao emparelhamento da Microsoft. A tabela a seguir fornece um mapeamento de serviço para o valor de comunidade BGP.

| **Serviço** |	**Valor de comunidade BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype For Business** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Outros serviços do Office 365** | 12076:5100 |
| **Prefixos globais/Anycast** | 12076:5200 |


### Manipulando preferências de roteamento

A Microsoft não adota valores de comunidade BGP que você define. É necessário configurar um par de sessões BGP por emparelhamento para garantir que os requisitos para o [SLA de disponibilidade](http://azure.microsoft.com/support/legal/sla/) sejam atendidos. No entanto, você pode configurar sua rede para preferir um link a outro usando técnicas padrão de manipulação de rota BGP. Você pode aplicar diferentes preferências locais de BGP a cada link para favorecer um caminho em vez de outro em sua rede para a Microsoft. Você pode preceder o caminho as nos anúncios de rota para influenciar o fluxo do tráfego da Microsoft em sua rede.

## Próximas etapas

- Configurar sua conexão da Rota Expressa.

	- [Criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md)
	- [Configurar o roteamento](expressroute-howto-routing-classic.md)
	- [Vincular uma Rede Virtual a um circuito de Rota Expressa](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO2-->
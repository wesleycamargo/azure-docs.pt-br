<properties
   pageTitle="Perguntas Frequentes sobre Rota Expressa"
   description="As Perguntas Frequentes Sobre Rota Expressa contêm informações sobre Serviços do Azure com Suporte, Custo, Dados e Conexões, SLA, Provedores e Locais, Largura de banda e Detalhes Técnicos adicionais."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/20/2016"
   ms.author="cherylmc"/>

# Perguntas Frequentes sobre Rota Expressa


## O que é Rota Expressa?
Rota Expressa é um serviço do Azure que permite a criação de conexões privadas entre os datacenters da Microsoft e a infraestrutura no seu local ou em uma instalação de colocalização. As conexões da Rota Expressa não passam pela Internet pública e oferecem mais segurança, confiabilidade e velocidades maiores com latências menores do que conexões típicas pela Internet.

### Quais são os benefícios do uso de Rota Expressa e conexões de rede privada?
As conexões de Rota Expressa não passam pela Internet pública e oferecem mais segurança, confiabilidade e velocidades maiores com latências consistentes e menores do que conexões típicas pela Internet. Em alguns casos, o uso de conexões da Rota Expressa para transferir dados entre dispositivos locais e o Azure pode proporcionar relações custo/benefício significativas.

### Quais serviços em nuvem da Microsoft têm suporte na Rota Expressa?
A Rota Expressa dá suporte à maioria dos serviços do Microsoft Azure atualmente, incluindo o Office 365. Procure por atualizações com disponibilidade geral, em breve.

### Onde o serviço está disponível?
Consulte esta página para localização de serviço e disponibilidade: [Locais e Parceiros da Rota Expressa](expressroute-locations.md).

### Como posso usar a Rota Expressa para conexão à Microsoft se eu não tenho parcerias com uma das operadoras parceiras da Rota Expressa?
Você pode selecionar uma operadora regional e conexões Ethernet terrestres para um dos locais com suporte do provedor Exchange. Em seguida, você pode emparelhar com a Microsoft no local do provedor. Verifique a última seção de [Locais e Parceiros da Rota Expressa](expressroute-locations.md) para ver se o seu provedor de serviço está presente em qualquer um dos locais do Exchange. Em seguida, você pode solicitar um circuito de Rota Expressa por meio do provedor de serviço para se conectar ao Azure.

### Quanto custa a Rota Expressa?
Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações a respeito.

### Se eu pago por um circuito de Rota Expressa de uma determinada largura de banda, a conexão VPN que adquiro do meu provedor de serviços de rede precisa ser a mesma velocidade?
Não. Você pode comprar uma conexão VPN de qualquer velocidade de seu provedor de serviços. No entanto, sua conexão com o Azure será limitada à largura de banda do circuito de Rota Expressa que você comprar.

### Se eu pago por um circuito de Rota Expressa de uma determinada largura de banda, tenho a capacidade de chegar até maiores velocidades, se necessário?
Sim. Circuitos de Rota Expressa são configurados para dar suporte a casos em que você pode disparar até duas vezes o limite de largura de banda que você adquiriu, sem nenhum custo adicional. Verifique com seu provedor de serviços se eles dão suporte a essa funcionalidade.

### Posso usar a mesma conexão de rede privada com Rede Virtual e outros serviços do Azure simultaneamente?
Sim. Um circuito de Rota Expressa, uma vez instalado, permitirá que você acesse os serviços em uma Rede Virtual e outros serviços do Azure simultaneamente. Você se conectará às redes virtuais pelo caminho privado de emparelhamento e a outros serviços pelo caminho público de emparelhamento.

### A Rota Expressa oferece um SLA (contrato de nível de serviço)?
Consulte a [página SLA de Rota Expressa](https://azure.microsoft.com/support/legal/sla/) para obter mais informações.

## Serviços com suporte
A maioria dos serviços do Azure tem suporte na Rota Expressa.

- A conectividade com máquinas virtuais e serviços de nuvem implantados em redes virtuais tem suporte pelo caminho privado de emparelhamento.
- Os sites do Azure têm suporte pelo caminho público de emparelhamento.
- Há suporte para o Office 365 no caminho de emparelhamento da Microsoft.
- Todos os outros serviços são acessíveis pelo caminho público de emparelhamento. As exceções são as descritas a seguir

	**Não há suporte para os seguintes serviços:**

	- CDN
	- Teste de carga do Visual Studio Team Services
	- Multi-factor Authentication

## Dados e conexões

### Há limites para a quantidade de dados que posso transferir usando Rota Expressa?
Não podemos definir um limite para a quantidade de transferência de dados. Consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### Quais velocidades de conexão têm suporte pela Rota Expressa?
Ofertas de largura de banda com suporte:

|50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10Gbps|

### Que provedores de serviços estão disponíveis?
Consulte [Locais e Parceiros da Rota Expressa](expressroute-locations.md) para a lista de locais e provedores de serviços.

## Detalhes técnicos

### Quais são os requisitos técnicos para conectar meu espaço local ao Azure?
Consulte a [Página de pré-requisitos para Rota Expressa](expressroute-prerequisites.md) para requisitos.

### As conexões à Rota Expressa são redundantes?
Sim. Cada circuito da Rota Expressa tem um par redundante de conexões cruzadas configurado para fornecer alta disponibilidade.

### Eu perderei a conectividade se um dos meus links de Rota Expressa falhar?
Você não perderá conectividade se uma das conexões cruzadas falhar. Uma conexão redundante estará disponível para dar suporte à carga de sua rede. Além disso, você pode criar vários circuitos em um local de emparelhamento diferente para obter resiliência a falhas.

### <a name="onep2plink"></a>Se eu não estiver colocalizado em uma troca de nuvem e meu provedor de serviços oferecer conexão ponto a ponto, preciso solicitar duas conexões físicas entre minha rede local e a Microsoft? 
Não, você só precisará de uma conexão física se seu provedor de serviços puder estabelecer dois circuitos virtuais de Ethernet por meio da conexão física. A conexão física (por exemplo, uma fibra óptica) será encerrada em um dispositivo de camada 1 (L1) (confira a imagem abaixo). Os dois circuitos virtuais de Ethernet estão marcados com IDs de VLAN diferentes, uma para o circuito primário e outra para o secundário. As IDs de VLAN estão no cabeçalho de Ethernet 802.1Q exterior. O cabeçalho de Ethernet 802.1Q interior (não mostrado) é mapeado para um [domínio de roteamento de Rota Expressa](expressroute-circuit-peerings.md) específico.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)


### Posso estender uma das minhas VLANs ao Azure usando a Rota Expressa?
Não. Não há suporte para extensões de conectividade de camada 2 ao Azure.

### Posso ter mais de um circuito de Rota Expressa em minha assinatura?
Sim. Você pode ter mais de um circuito de Rota Expressa em sua assinatura. O limite padrão para o número de circuitos dedicados está definido como 10. Você pode contatar Suporte da Microsoft para aumentar o limite, se necessário.

### Posso ter circuitos de Rota Expressa de diferentes provedores de serviços?
Sim. Você pode ter circuitos de Rota Expressa de muitos provedores de serviços. Cada circuito de Rota Expressa será associado apenas a um provedor de serviços.

### Como conectar minhas redes virtuais a um circuito da Rota Expressa
As etapas básicas são descritas a seguir.

- Você deve estabelecer um circuito de Rota Expressa e fazer com que o provedor de serviços o habilite.
- Você ou o provedor deve configurar os emparelhamentos BGP.
- É necessário vincular uma Rede Virtual ao circuito de Rota Expressa.

Consulte [Fluxos de trabalho da Rota Expressa para provisionamento e estados do circuito](expressroute-workflows.md) para saber mais.

### Existem limites de conectividade para meu circuito de Rota Expressa?
Sim. A página [Locais e Parceiros da Rota Expressa](expressroute-locations.md) fornece uma visão geral dos limites de conectividade para um circuito de Rota Expressa. A conectividade de um circuito de Rota Expressa é limitada a uma única região geopolítica. A conectividade pode ser expandida para várias regiões geopolíticas habilitando o recurso premium da Rota Expressa.

### Posso vincular mais de uma Rede Virtual a um circuito de Rota Expressa?
Sim. Você pode vincular até 10 redes virtuais a um circuito de Rota Expressa.

### Tenho várias assinaturas do Azure que contêm redes virtuais. Posso conectar redes virtuais pertencentes a assinaturas separadas a um circuito da Rota Expressa?
Sim. Você pode autorizar até 10 outras assinaturas do Azure para usar um único circuito de Rota Expressa. Esse limite pode ser aumentado habilitando o recurso premium da Rota Expressa.

Para obter mais detalhes, consulte [Compartilhando um circuito de Rota Expressa entre várias assinaturas](expressroute-howto-linkvnet-arm.md).

### As redes virtuais estão conectadas ao mesmo circuito e isoladas umas das outras?
Não. Todas as redes virtuais vinculadas ao mesmo circuito de Rota Expressa fazem parte do mesmo domínio de roteamento e não estão isoladas entre si, segundo uma perspectiva de roteamento. Se você precisar de isolamento de rota, você precisará criar um circuito de Rota Expressa separado.

### Posso conectar uma Rede Virtual a mais de um circuito de Rota Expressa?
Sim. Você pode vincular uma única rede virtual a até 4 circuitos de Rota Expressa. Eles devem ser ordenados por meio de quatro [locais diferentes de Rota Expressa](expressroute-locations.md).

### Poderei acessar a Internet por meio de minhas redes virtuais conectadas a circuitos de Rota Expressa?
Sim. Se você não anunciou rotas padrão (0.0.0.0/0) ou prefixos de rotas de Internet ao longo da sessão BGP, você será capaz de se conectar à Internet por meio de uma rede virtual vinculada a um circuito de Rota Expressa.

### Posso bloquear a conectividade à Internet em minhas redes virtuais conectadas a circuitos de Rota Expressa?
Sim. Você pode anunciar rotas padrão (0.0.0.0/0) para bloquear toda a conectividade com a Internet para máquinas virtuais implantadas em uma rede virtual e rotear todo o tráfego de saída através do circuito de Rota Expressa. Observe que se você anunciar rotas padrão, forçaremos o tráfego para serviços oferecidos por emparelhamento público (como o armazenamento do Azure e banco de dados SQL) de volta para o seu local. Você precisará configurar seus roteadores para retornar o tráfego para o Azure através do caminho de emparelhamento público ou pela Internet.

### As redes virtuais vinculadas ao mesmo circuito de Rota Expressa podem conversar entre si?
Sim. Máquinas virtuais implantadas em redes virtuais conectadas ao mesmo circuito de Rota Expressa podem comunicar-se umas com as outras.

### Posso usar a conectividade site a site para redes virtuais em conjunto com a Rota Expressa?
Sim. A Rota Expressa pode coexistir com VPN dos tipos site a site.

### Posso alterar uma rede virtual usando configuração site a site / ponto a site para que ela passe a usar Rota Expressa?
Sim. Você terá que criar um gateway de Rota Expressa em sua rede virtual. Haverá um pequeno tempo de inatividade associado ao processo.

### O que é necessário para conexão ao armazenamento do Azure por meio de Rota Expressa?
Você deve estabelecer um circuito de Rota Expressa e configurar rotas para emparelhamento público.

### Há limites no número de rotas que posso anunciar?
Sim. Aceitamos até 4.000 prefixos de rota para emparelhamento privado e 200 para cada emparelhamento público e da Microsoft. Você poderá aumentar esse limite para 10.000 rotas para emparelhamento privado se habilitar o recurso premium da Rota Expressa.

### Há restrições de intervalos de endereços IP que posso anunciar durante a sessão BGP?
Não aceitamos prefixos privados (RFC1918) na sessão BGP de emparelhamento público.

### O que acontece se eu exceder os limites de BGP?
As sessões BGP serão interrompidas. Elas serão redefinidas quando a contagem de prefixos voltar a ficar abaixo do limite.

### O que é o tempo de espera de BGP da Rota Expressa? Pode ser ajustado?
O tempo de espera é de 180. As mensagens keep-alive são enviadas a cada 60 segundos. Essas são configurações fixas no lado do Microsoft que não podem ser alteradas.

### Após anunciar a rota padrão (0.0.0.0/0) para minhas redes virtuais, eu não posso ativar o Windows sendo executado em minhas VMs do Azure. Como posso corrigir isso?
As etapas a seguir ajudarão o Azure a reconhecer a solicitação de ativação:

1. Estabeleça o emparelhamento público para seu circuito de Rota Expressa.
2. Execute uma pesquisa DNS e localize o endereço IP de **kms.core.windows.net**
3. Então, execute um dos dois itens a seguir para que o Serviço de Gerenciamento de Chaves reconheça que a solicitação de ativação é proveniente do Azure e atenda a solicitação.
	- Em sua rede local, faça o roteamento do tráfego destinado ao endereço IP (obtido na etapa 2) de volta para o Azure por meio de emparelhamento público.
	- Faça com que seu provedor NSP envie o tráfego por loop “hairpin” de volta ao Azure, via emparelhamento público.

### Posso alterar a largura de banda de um circuito de Rota Expressa?
Sim. Você pode aumentar a largura de banda de um circuito de Rota Expressa sem precisar subdividi-lo. Você terá de fazer o acompanhamento junto ao seu provedor de conectividade para garantir que eles atualizem os gargalos em suas redes, para dar suporte ao aumento de largura de banda. Você não poderá, todavia, reduzir a largura de banda de um circuito de Rota Expressa. Precisar reduzir a largura de banda significa uma desmontagem e recriação de um circuito de Rota Expressa.

### Como posso alterar a largura de banda de um circuito de Rota Expressa?
Você pode atualizar a largura de banda do circuito de Rota Expressa usando o cmdlet do PowerShell e API de circuito dedicado de atualização.

## Rota Expressa Premium

### O que é a Rota Expressa Premium?
Rota Expressa premium é uma coleção de recursos listados abaixo.

 - Limite maior na tabela de roteamento, de 4.000 rotas a 10.000 rotas para emparelhamento público e emparelhamento privado.
 - Maior número de VNets que podem ser conectadas ao circuito de Rota Expressa (o padrão é 10). Consulte a tabela seguir para obter mais detalhes.
 - Conectividade global através da rede de núcleo da Microsoft. Agora, você poderá conectar uma VNet em uma região geopolítica a um circuito de Rota Expressa em outra região. **Exemplo:** é possível conectar uma VNet criada na Europa Ocidental a um circuito de Rota Expressa criado no Vale do Silício.
 - Conectividade com serviços do Office 365 e CRM Online.

### Quantas VNets posso vincular a um circuito de Rota Expressa se eu habilitei a Rota Expressa premium?
A tabela a seguir informa os limites aumentados para o número de VNets que você pode conectar a um circuito de Rota Expressa. O limite padrão é 10.

**Limites de circuitos**

| **Tamanho do circuito** | **Número de links de VNet para configuração padrão** | **Número de Links de VNet com Rota Expressa Premium** |
|--------------|----------------------------------------|-----------------------------------------------|
| 50 Mbps | 10 | 10 |
| 100 Mbps | 10 | 20 |
| 200 Mbps | 10 | 25 |
| 500 Mbps | 10 | 40 |
| 1 Gbps | 10 | 50 |
| 2 Gbps | 10 | 60 |
| 5 Gbps | 10 | 75 |
| 10 Gbps | 10 | 100 |



### Como habilito a Rota Expressa premium?
Os recursos da Rota Expressa premium podem ser ativados quando o recurso está habilitado, e podem ser desativados atualizando o estado do circuito. Você pode habilitar a Rota Expressa premium no momento da criação de circuito ou pode chamar o cmdlet do PowerShell/API do circuito dedicado de atualização para habilitar a Rota Expressa premium.

### Como desabilito a Rota Expressa premium?
Você pode desabilitar a Rota Expressa premium chamando o cmdlet do PowerShell/API do circuito dedicado de atualização. Você deve garantir que tenha dimensionado seus requisitos de conectividade para atender os limites padrão antes de desabilitar a Rota Expressa premium. Haverá falha na solicitação para desabilitar a Rota Expressa premium se sua utilização for expandida além dos limites padrão.

### Posso escolher os recursos que desejo do conjunto de recursos premium?
Não. Você não poderá selecionar os recursos de que precisa. Habilitamos todos os recursos quando você ativa a Rota Expressa premium.

### Quanto custa a Rota Expressa premium?
Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para informações sobre custo.

### Eu pago algo pela Rota Expressa premium além dos encargos padrão da Rota Expressa?
Sim. Os encargos da Rota Expressa premium somam-se aos encargos de circuito de Rota Expressa e aos encargos demandados pelo provedor de conectividade.

## Rota Expressa e serviços do Office 365 e CRM Online

### Como criar um circuito de Rota Expressa para conectar aos serviços do Office 365 e CRM Online?

1. Examine a página [Pré-requisitos da Rota Expressa](expressroute-prerequisites.md) para certificar-se de que você atende os requisitos
2. Examine a lista de provedores de serviços e locais em [Locais e parceiros da Rota Expressa](expressroute-locations.md) para garantir que suas necessidades de conectividade são atendidas.
3. Planeje seus requisitos de capacidade, revisando [Planejamento de rede e ajuste de desempenho para o Office 365](http://aka.ms/tune/)
4. Siga as etapas relacionadas nos fluxos de trabalho abaixo para configurar a conectividade [Fluxos de trabalho de Rota Expressa para provisionamento do circuito e estados do circuito](expressroute-workflows.md).

>[AZURE.IMPORTANT] Certifique-se de ter habilitado o complemento premium da Rota Expressa ao configurar a conectividade com os serviços do Office 365 e CRM Online.

### Eu preciso habilitar o emparelhamento público do Azure para me conectar aos serviços do Office 365 e CRM Online?
Não, você precisa habilitar somente o Emparelhamento da Microsoft. O tráfego de autenticação no Azure AD será enviado pelo emparelhamento da Microsoft.

### Meus circuitos da Rota Expressa existentes dão suporte a conectividade com os serviços do Office 365 e CRM Online?
Sim. Seus circuitos da Rota Expressa existentes podem ser configurados para dar suporte a conectividade com os serviços do Office 365. Verifique se você tem capacidade suficiente para se conectar aos serviços do Office 365 e certifique-se de que você habilitou o complemento premium. [Planejamento da rede e ajuste de desempenho para o Office 365](http://aka.ms/tune/) ajudarão você a planejar suas necessidades de conectividade. Veja também [Criar e modificar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md).

### Quais serviços do Office 365 podem ser acessados por uma conexão de Rota Expressa?

Confira a página [Intervalos de endereço IP e URLs do Office 365](http://aka.ms/o365endpoints) para obter uma lista atualizada dos serviços com suporte na Rota Expressa.

### Qual é o custo da Rota Expressa para serviços do Office 365 e CRM Online?
Os serviços do Office 365 e CRM Online requerem o complemento premium para serem habilitados. A [página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) fornece detalhes de custos para a Rota Expressa.

### Em que regiões há suporte para Rota Expressa para Office 365?
Consulte [Locais e parceiros da Rota Expressa](expressroute-locations.md) para obter mais informações sobre a lista de parceiros e os locais onde há suporte para a Rota Expressa.

### Posso acessar o Office 365 pela Internet mesmo se a Rota Expressa foi configurada para minha organização?
Sim. Pontos de extremidade do serviço do Office 365 estão acessíveis pela Internet, embora a Rota Expressa tenha sido configurada para sua rede. Se você estiver em um local configurado para se conectar a serviços do Office 365 por meio de Rota Expressa, você se conectará por meio de Rota Expressa.

<!---HONumber=AcomDC_0427_2016-->
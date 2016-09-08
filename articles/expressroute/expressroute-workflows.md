<properties
   pageTitle="Fluxos de trabalho para a configuração de um circuito da Rota Expressa | Microsoft Azure"
   description="Esta página fornece uma orientação pelos fluxos de trabalho de configuração de emparelhamentos e circuito de Rota Expressa"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="cherylmc"/>

# Fluxos de trabalho da Rota Expressa para provisionamento e estados do circuito

Esta página fornece uma orientação de alto nível pelos fluxos de trabalho de provisionamento do serviço e de configuração do roteamento.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura e as etapas correspondentes a seguir mostram as tarefas que você deve executar para provisionar um circuito da Rota Expressa de ponta a ponta.

1. Use o PowerShell para configurar um circuito da Rota Expressa. Siga as instruções no artigo [Criar circuitos da Rota Expressa](expressroute-howto-circuit-classic.md) para obter mais detalhes.

2. Solicite conectividade do provedor de serviço. Esse processo varia. Entre em contato com o provedor de conectividade para obter mais detalhes sobre a solicitação de conectividade.

3. Confira se o circuito foi provisionado com sucesso verificando o estado de provisionamento do circuito da Rota Expressa por meio do PowerShell.

4. Configure os domínios de roteamento. Se seu provedor de conectividade gerencia a camada 3 para você, ele configurará o roteamento para o circuito. Se o seu provedor de conectividade oferece somente os serviços de camada 2, configure o roteamento de acordo com as diretrizes descritas nas páginas [requisitos de roteamento](expressroute-routing.md) e [configuração de roteamento](expressroute-howto-routing-classic.md).

	-  Habilitar o emparelhamento privado do Azure - Você deve habilitar esse emparelhamento para se conectar a VMs/serviços de nuvem implantados dentro das redes virtuais.
	-  Habilitar o emparelhamento público do Azure - Você deve habilitar o emparelhamento público do Azure se quiser se conectar aos serviços do Azure hospedados em endereços IP públicos. Esse é um requisito para acessar os recursos do Azure se você tiver optado por habilitar o roteamento padrão para emparelhamento privado do Azure.
	-  Habilitar o emparelhamento da Microsoft - você deve habilitar isso para acessar os serviços do Office 365 e do CRM Online.
	
	>[AZURE.IMPORTANT] Use um proxy/borda diferente da usada para a Internet para se conectar à Microsoft. Usar a mesma borda para a Rota Expressa e para a Internet causará o roteamento assimétrico e falhas de conectividade em sua rede.

	![](./media/expressroute-workflows/routing-workflow.png)


5. Vinculando redes virtuais aos circuitos da Rota Expressa - Você pode vincular redes virtuais ao circuito da Rota Expressa. Siga as instruções [para vincular redes virtuais](expressroute-howto-linkvnet-arm.md) ao seu circuito. Essas redes virtuais podem estar na mesma assinatura do Azure que o circuito da Rota Expressa ou podem estar em uma assinatura diferente.


## Estados de provisionamento de circuito da Rota Expressa

Cada circuito de Rota Expressa tem dois estados:

- Estado de provisionamento do provedor de serviço
- Status

O status representa o estado de provisionamento da Microsoft. Essa propriedade pode estar em um dos seguintes estados: *Habilitado*, *Habilitando* ou *Desabilitando*. O circuito da Rota Expressa deve estar no estado Habilitado para que você possa usá-lo.

O estado de provisionamento do provedor de conectividade representa o estado no lado do provedor de conectividade. Ele pode ser *Não Provisionado*, *Provisionando* ou *Provisionado*. O circuito da Rota Expressa deverá estar no estado Provisionado para que possa usá-lo.

### Possíveis estados de um circuito da Rota Expressa

Esta seção lista os possíveis estados de um circuito da Rota Expressa.

#### No momento da criação

Você verá o circuito da Rota Expressa no seguinte estado assim que executar o cmdlet do PowerShell para criar um circuito da Rota Expressa.

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


#### Quando o provedor de conectividade estiver no processo de provisionamento do circuito

Você verá o circuito da Rota Expressa no estado a seguir assim que passar a chave de serviço para o provedor de conectividade, e ele tiver iniciado o processo de provisionamento.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled


#### Quando o provedor de conectividade tiver concluído o processo de provisionamento

Você verá o circuito da Rota Expressa no seguinte estado assim que o provedor de conectividade tiver concluído o processo de provisionamento.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled

Provisionado e Habilitado são os únicos estados nos quais o circuito pode estar para você poder usá-lo. Se você estiver usando um provedor de camada 2, configure o roteamento para o circuito somente quando ele estiver nesse estado.

#### Se o desprovisionamento tiver sido iniciado primeiro no lado da Microsoft

Você verá o circuito da Rota Expressa no seguinte estado assim que executar o cmdlet do PowerShell para excluir um circuito da Rota Expressa.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Disabling

Entre em contato com seu provedor de conectividade para desprovisionar o circuito da Rota Expressa. **Importante:** a Microsoft continuará a cobrar pelo circuito até que você execute o cmdlet do PowerShell para desprovisioná-lo.

#### Se o desprovisionamento tiver sido iniciado no lado do provedor de serviço

Se você tiver solicitado ao provedor de serviços primeiro o desprovisionamento do circuito da Rota Expressa, verá o circuito definido com o estado a seguir, após o provedor de serviços ter concluído o processo de desprovisionamento.


	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled

Você pode optar por habilitá-lo novamente se for necessário, ou executar cmdlets do PowerShell para excluir o circuito. **Importante:** a Microsoft continuará a cobrar pelo circuito até que você execute o cmdlet do PowerShell para desprovisioná-lo.


## Estado de configuração da sessão de roteamento

O estado de provisionamento BGP permite que você saiba se a sessão BGP foi habilitada na borda da Microsoft. O estado deve ser habilitado para que você possa usar o emparelhamento.

É importante verificar o estado da sessão BGP, especialmente para o emparelhamento da Microsoft. Além do estado de provisionamento BGP, há outro estado chamado *estado de prefixos públicos anunciados*. O estado de prefixos públicos anunciados deve ser *configurado*, tanto para que a sessão BGP fique ativa quanto para o roteamento funcionar completamente.

Se o estado de prefixo público anunciado for definido como *validação necessária*, a sessão BGP não estará habilitada, pois os prefixos anunciados não corresponderam ao número AS em qualquer um dos registros do roteamento.

>[AZURE.IMPORTANT] Se o estado de prefixos públicos anunciados for *validação manual*, será necessário abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer provas de que você possui os endereços IP anunciados juntamente com o número do Sistema Autônomo associado.


## Próximas etapas

- Configurar sua conexão da Rota Expressa.

	- [Criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md)
	- [Configurar o roteamento](expressroute-howto-routing-arm.md)
	- [Vincular uma Rede Virtual a um circuito de Rota Expressa](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0824_2016-->
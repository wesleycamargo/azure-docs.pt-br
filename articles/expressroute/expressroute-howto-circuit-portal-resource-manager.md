<properties
   pageTitle="Criar e modificar um circuito da Rota Expressa usando o Gerenciador de Recursos e o portal do Azure | Microsoft Azure"
   description="Este artigo descreve como criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito da Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="cherylmc"/>

# Criar e modificar um circuito da Rota Expressa

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

Este artigo descreve como criar um circuito da Rota Expressa do azure usando o portal do Azure e o modelo de implantação do Azure Resource Manager. As etapas a seguir também mostrarão a você como verificar o status do circuito, como atualizá-lo ou como excluí-lo e desprovisioná-lo.

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Antes de começar


- Examine a página [Pré-requisitos](expressroute-prerequisites.md) e a página [Fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
- Verifique se você tem acesso ao [portal do Azure](https://portal.azure.com).
- Verifique se você tem permissões para criar novos recursos de rede. Se você não tiver as permissões corretas, entre em contato com o administrador da conta.

## Criar e provisionar um circuito da Rota Expressa

### 1\. Entrar no Portal do Azure

Em um navegador, acesse o [Portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.

### 2\. Criar um novo circuito da Rota Expressa

>[AZURE.IMPORTANT] O circuito da Rota Expressa será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação assim que o provedor de conectividade estiver pronto para provisionar o circuito.

- **Etapa 1.** Você pode criar um circuito da Rota Expressa selecionando a opção de criar um novo recurso. Clique em **Novo** **>** **Rede** **>** **Rota Expressa**, conforme mostrado na imagem abaixo. 

	![](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

- **Etapa 2.** Depois de clicar em **Rota Expressa**, você verá a folha **Criar circuito de Rota Expressa**. Quando preencher os valores nessa folha, verifique se você especificou a camada de SKU e os dados de medição corretos.

	- A **camada** determina se um complemento padrão ou premium da Rota Expressa está habilitado. Você pode especificar “Standard” para obter a SKU padrão ou “Premium” para o complemento premium

	- A **medição de dados** determina o tipo de cobrança. Você pode especificar "Limitado" para um plano de dados limitado e “Ilimitado” para um plano de dados ilimitado. **Observação:** você pode alterar o tipo de cobrança de "Limitado" para "Ilimitado", mas não pode alterar o tipo de "Ilimitado" para "Limitado".


		![](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)


### 3\. Exibir circuitos e propriedades

- **Para exibir todos os circuitos** 
	
	Você pode exibir todos os circuitos criados selecionando **Todos os recursos** no menu do lado esquerdo.

	![](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

- **Para exibir propriedades**
	
	Você pode exibir as propriedades do circuito selecionando-o. Nessa folha, anote a chave de serviço para o circuito. Você deve copiar a chave de circuito para o circuito e passá-lo para o provedor de serviços a fim de concluir o processo de provisionamento. A chave de circuito é específica para o circuito.

	![](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### 4\. Enviar a chave de serviço ao seu provedor de conectividade para obter o provisionamento

Nessa folha, o status do provedor fornece informações sobre o estado atual do provisionamento no lado do provedor de serviço e o status do circuito fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, confira o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando você criar um novo circuito da Rota Expressa, ele estará no seguinte estado:


Status do provedor: não provisionado<BR> Status do circuito: habilitado

![](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)


O circuito assumirá o estado a seguir quando o provedor de conectividade estiver no processo de habilitá-lo para você:

Status do provedor: provisionando<BR> Status do circuito: habilitado

Para que você consiga usar um circuito da Rota Expressa, ele deverá estar no seguinte estado:

Status do provedor: provisionado<BR> Status do circuito: habilitado


### 5\. Verificar periodicamente o status e o estado da chave do circuito

Você pode exibir as propriedades do circuito de seu interesse selecionando-o. Verifique o **Status do provedor** e se ele mudou para **Provisionado** antes de continuar.


![](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### 6\. Criar sua configuração de roteamento

Para obter instruções passo a passo, consulte a [configuração do roteamento de circuito da Rota Expressa](expressroute-howto-routing-portal-resource-manager.md) a fim de criar e modificar os emparelhamentos de circuito.

>[AZURE.IMPORTANT] Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de Camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

### 7\. Vincular uma rede virtual a um circuito de Rota Expressa

Em seguida, vincule uma rede virtual a seu circuito da Rota Expressa. Você pode usar o artigo [Vinculando redes virtuais a circuitos da Rota Expressa](expressroute-howto-linkvnet-arm.md) quando trabalha com o modelo de implantação do Gerenciador de Recursos.

## Obtendo o status de um circuito da Rota Expressa

Você pode exibir o status de um circuito selecionando-o.

![](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## Modificando um circuito da Rota Expressa

Você pode modificar certas propriedades de um circuito da Rota Expressa sem afetar a conectividade. Neste momento, você não pode modificar propriedades de circuito da Rota Expressa usando o portal do Azure. No entanto, você pode usar o PowerShell para modificar as propriedades do circuito. Consulte a seção do artigo [Modificando um circuito da Rota Expressa usando o PowerShell](expressroute-howto-circuit-arm.md#modify).

Você pode fazer o seguinte, sem tempo de inatividade:

- Habilitar ou desabilitar o complemento premium da Rota Expressa para seu circuito da Rota Expressa.

- Aumentar a largura de banda de seu circuito da Rota Expressa. Observe que o downgrade da largura de banda de um circuito não tem suporte.

- Altere o plano de medição de Dados Limitados para Dados Ilimitados. Observe que a alteração do plano de medição de Dados Ilimitados para Dados Limitados não tem suporte.

-  Você pode habilitar e desabilitar "Permitir Operações Clássicas"

Para saber mais sobre limites e limitações, consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md).


## Excluindo e desprovisionando um circuito da Rota Expressa

Você pode excluir seu circuito da Rota Expressa selecionando o ícone **Excluir**. Observe o seguinte:

- Você deve ver todas as redes virtuais do circuito na Rota Expressa. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.

- Se o estado de provisionamento do provedor de serviços do circuito da Rota Expressa estiver habilitado, o status passará de habilitado para “desabilitando”. Trabalhe com seu provedor de serviços para desprovisionar o circuito no lado dele. A Microsoft continuará a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.

- Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido para “não provisionado”) antes da execução do cmdlet acima, a Microsoft desprovisionará o circuito e irá interromper a cobrança.

## Próximas etapas

Depois de criar seu circuito, faça o seguinte:

- [Criar e modificar o roteamento do circuito da Rota Expressa](expressroute-howto-routing-portal-resource-manager.md)
- [Vincular a rede virtual ao circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0427_2016-->
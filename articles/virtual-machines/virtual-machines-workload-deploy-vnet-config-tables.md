<properties
	pageTitle="Criar uma rede virtual entre locais usando tabelas de configuração"
	description="Este tópico descreve como configurar uma rede virtual entre locais usando tabelas de configurações predeterminadas."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Criar uma rede virtual entre locais usando tabelas de configuração

Este tópico explica o processo de criação de uma rede virtual entre locais usando as configurações previamente especificadas no seguinte conjunto de tabelas de configuração:

- Tabela V: Configuração de rede virtual entre locais
- Tabela S: Sub-redes na rede virtual
- Tabela D: Servidores DNS locais
- Tabela L: Prefixos de endereços para a rede local

Estas tabelas geralmente são preenchidas em um tópico que descreve a configuração de uma carga de trabalho de TI no Azure e inclui uma rede virtual entre locais. Consulte [Fase 1: Configurar o Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) para obter um exemplo.

O procedimento a seguir faz referência às informações dessas tabelas para orientar você durante o processo de configuração da rede virtual. Se você ainda não tiver especificado as configurações nessas tabelas em outro tópico, mas quiser continuar configurando uma rede virtual entre locais, consulte [Configurar uma conexão site a site entre locais para uma rede virtual do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE]Este procedimento orienta a criação de uma rede virtual que usa uma conexão VPN site a site. Para saber mais sobre como usar a Rota Expressa do Azure para sua conexão site a site, consulte [Visão geral técnica do ExpressRoute](https://msdn.microsoft.com/library/dn606309.aspx).

## Criar uma nova rede de virtual entre locais do Azure usando os valores da sua tabela de configurações

1. Entre no [Portal do Azure](https://manage.windowsazure.com/).
2. Na barra de tarefas, clique em **Novo > Serviços de Rede > Rede Virtual > Criação Personalizada**.
3. Na página **Detalhes da Rede Virtual**:
	- Em **Nome**, digite o nome do Item 1 na Tabela V.
	- Em **Local**, selecione a região do Item 2 na Tabela V.
4. Clique na seta de avanço para continuar.
5. Página **Conectividade de VPN e Servidores DNS**:
	- Em **Servidores DNS**, para cada entrada da Tabela D, configure o nome amigável e o endereço IP dos servidores DNS locais.
	- Em **Conectividade Site a Site**, selecione **Configurar uma VPN site a site**.
	- Se você já tiver configurado uma rede local e desejar usá-la, selecione o nome dela em **Rede Local**. Se quiser criar uma nova rede local, selecione **Especificar uma Nova Rede Local** em **Rede Local**.
	- Se ainda não tiver configurado uma rede local para a sua assinatura, você não verá o campo **Rede Local**.
6. Clique na seta de avanço para continuar.
7. Na página **Conectividade Site a Site** (se você tiver selecionado **Especificar uma Nova Rede Local** na etapa 5):
	- Em **Nome**, digite o nome do Item 3 na Tabela V (o nome da rede local).
	- Em **Endereço IP do Dispositivo VPN**, digite o endereço do Item 4 na Tabela V.
	- Em **Espaço de Endereço**, para cada entrada da Tabela L, insira os espaços de endereço IP da rede da sua empresa em termos de prefixo (em **IP Inicial**) e comprimento do prefixo (em **CIDR (Contagem de Endereços)**).
8. Clique na seta de avanço para continuar.
9. Na página **Espaços de Endereço de Rede Virtual**:
	- Em **Espaço de Endereço**, digite o espaço de endereço IP privado da rede virtual do Item 5 da Tabela V, em termos de prefixo (em **IP Inicial**) e comprimento do prefixo (em **CIDR (Contagem de Endereços)**).
	- Em **Sub-redes**, para cada entrada da Tabela S
		- Digite o nome da sub-rede na coluna **Sub-redes**, substituindo o nome padrão, se necessário.
		- Digite o espaço de endereço IP privado da sub-rede em termos de prefixo (em **IP Inicia**) e comprimento do prefixo (em **CIDR (Contagem de Endereços)**).
	- Clique em **Adicionar Sub-rede de Gateway**.
10. Clique na marca de seleção para concluir a configuração.

## Recursos adicionais

[Visão geral da rede virtual](https://msdn.microsoft.com/library/jj156007.aspx)

[Tarefas de configuração da rede virtual](https://msdn.microsoft.com/library/jj156206.aspx)

[Configurar uma conexão site a site entre locais para uma rede virtual do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)

<!---HONumber=August15_HO6-->
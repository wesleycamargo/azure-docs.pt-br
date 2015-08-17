<properties 
   pageTitle="Como gerenciar as propriedades da rede virtual (VNet)"
   description="Saiba como exibir e editar configurações de rede virtual"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Como gerenciar as propriedades da rede virtual (VNet)
Você pode exibir e modificar configurações da rede virtual usando o portal de gerenciamento.

## Exibir configurações da rede virtual

1. Faça logon no Portal de Gerenciamento.

1. No painel de navegação, clique em **Redes** e, em seguida, clique no nome da rede virtual na coluna **Nome**.

1. Clique em **Painel** para exibir as seguintes informações:

	- **rede virtual:** é a rede virtual e as sub-redes junto com a rede local e o servidor DNS.

	- **dados de entrada, dados de saída:** visível quando você tiver conectividade entre locais. Esses são os dados que entram e saem da sua rede virtual.

	- **endereço IP do gateway:** visível quando você tiver configurado um gateway. É o endereço IP do gateway. É o endereço IP que você usará para configurar seu dispositivo VPN para conectividade entre locais.

	- **recursos:** são os recursos do Azure conectados à rede virtual.

	- **visão rápida:** um lugar para encontrar rapidamente as informações a seguir:

		- **Baixar o pacote VPN do cliente:** esse pacote é usado na criação de conexões VPN entre locais de ponto a site. Baixe o pacote que pertence ao cliente.

		- **Baixar o script do dispositivo VPN:** esses modelos são usados para ajudar a configurar dispositivos VPN para conexões entre locais site a site.

		- **status**

		- **ID da assinatura**
		
		- **ID da rede virtual**
		
		- **local**

1. Clique em **Configurar** para exibir as seguintes informações:

	- **servidores dns:** são os servidores DNS e o endereço IP correspondente que os clientes de sua rede virtual usarão para resolução de nomes.

	- **conectividade ponto a site:** visível quando você tiver configurado sua rede virtual para conexões ponto a site.

	- **conectividade site a site:** visível quando você tiver configurado sua rede virtual para conexões site a site.

	- **rede local:** será mostrado se você tiver conectividade site a site. É a rede local selecionada que representa seu site local. As configurações para essa rede local podem ser configuradas em **redes** na página **Redes Locais**.
	
	- **espaços de endereço de rede virtual:** o espaço de endereço e as sub-redes na sua rede virtual.

1. Clique em **Certificados** para exibir as seguintes informações:

	- **Certificado raiz carregados:** para configurações de VPN ponto a site
	
	- **Impressão digital do certificado**
	
	- **Expiração de certificado**
	
	- **Upload:** carregar um certificado raiz
	
	- **Excluir:** excluir um certificado raiz selecionado

## Editar configurações da rede virtual

Depois que você implantar sua rede virtual, somente determinadas configurações poderão ser modificadas. As etapas a seguir ajudarão você a editar suas definições da configuração da rede virtual usando o Portal de Gerenciamento.

1. Faça logon no Portal de Gerenciamento.

1. No painel de navegação, clique em **Redes** e, em seguida, clique no nome da Rede Virtual na coluna **Nome**.

1. Clique em **Configurar**.

	- Em **servidores dns**, você pode adicionar outros servidores DNS para resolução de nomes. Se você adicionar outros servidores DNS depois de implantar máquinas virtuais em sua rede virtual, reinicie cada máquina virtual para que ela escolha o novo servidor DNS adicional.
	
	- Na seção **conectividade ponto a site**, você pode configurar o espaço de endereço que deseja designar para conexões ponto a site.
	
	- Na seção **conectividade site a site**, você pode definir as configurações de site a site.
	
	- Na seção **espaço de endereço de rede virtual**, você pode adicionar outro espaço de endereço e outras sub-redes. Para adicionar sub-redes, clique em **adicionar sub-redes** e configure o espaço de endereço que você deseja usar. Para adicionar uma sub-rede de gateway para conectividade entre locais, clique em **adicionar sub-rede de gateway**. Você só pode adicionar uma sub-rede de gateway por rede virtual.

1. Clique em **SALVAR** na parte inferior da tela para salvar as alterações de configuração.

## Próximas etapas

[Como gerenciar servidores DNS usados por uma rede virtual (VNet)](../virtual-networks-manage-dns-in-vnet)

[Como usar endereços IP públicos em uma rede virtual](../virtual-networks-public-ip-within-vnet)

[Como excluir uma rede virtual (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=August15_HO6-->
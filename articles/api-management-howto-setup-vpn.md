<properties
	pageTitle="Como configurar conexões VPN no Gerenciamento de API do Azure"
	description="Saiba como configurar uma conexão VPN no Gerenciamento de API do Azure e acessar serviços Web por meio dela."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="antonba"/>

# Como configurar conexões VPN no Gerenciamento de API do Azure

O suporte a VPN do Gerenciamento de API permite que você conecte seu proxy de Gerenciamento de API a uma Rede Virtual do Azure. Isso permite que os clientes do Gerenciamento de API se conectem com segurança a seus serviços Web de back-end locais ou que não estão acessíveis para a Internet pública.

## <a name="enable-vpn"> </a>Habilitar conexões VPN

>A conectividade VPN só está disponível na camada **Premium**. Para alternar para ela, abra o serviço de Gerenciamento de API no [Portal de Gerenciamento][] e, em seguida, abra a guia **Escala**. Na seção **Geral**, selecione a camada Premium e clique em Salvar.

Para habilitar a conectividade VPN, abra o serviço de Gerenciamento de API no [Portal de Gerenciamento][] e alterne para a guia **Configurar**.

Na seção VPN, alterne o status de **Conexão VPN** para **Ativa**.

![Configurar a guia da instância de Gerenciamento de API][api-management-setup-vpn-configure]

Agora você verá uma lista de todas as regiões em que o serviço de Gerenciamento de API é disponibilizado.

Selecione uma VPN e sub-rede para cada região. A lista de VPNs é preenchida com base nas redes virtuais disponíveis em sua assinatura do Azure que estão instaladas na região que você está configurando.

![Selecionar VPN][api-management-setup-vpn-select]

Na parte inferior da tela, clique em **Salvar**. Você não poderá executar outras operações no serviço de Gerenciamento de API no Portal de Gerenciamento do Azure enquanto ele está sendo atualizado. O proxy do serviço permanecerá disponível e chamadas de tempo de execução não deverão ser afetadas.

Observe que o endereço VIP do proxy será alterado sempre que a VPN for habilitada ou desabilitada.

## <a name="connect-vpn"> </a>Conectar a um serviço Web via VPN

Depois que o serviço de Gerenciamento de API é conectado à VPN, acessar serviços Web na Rede Virtual é o mesmo que acessar serviços públicos. Basta digitar o endereço local ou o nome do host (se um servidor DNS tiver sido configurado para a Rede Virtual do Azure) do serviço Web no campo **URL do serviço Web** ao criar uma nova API ou editar uma já existente.

![Adicionar a API da VPN][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>Conteúdo relacionado


 * [Tutorial: Criar uma rede virtual para conectividade site a site entre instalações][]
 * [Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Portal de Gerenciamento]: https://manage.windowsazure.com/

[Tutorial: Criar uma rede virtual para conectividade site a site entre instalações]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure]: api-management-howto-api-inspector.md

<!--HONumber=54-->
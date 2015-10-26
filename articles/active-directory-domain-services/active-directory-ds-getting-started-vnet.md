<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Introdução | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Serviços de Domínio do Azure AD *(Visualização)* - Introdução

## Diretrizes para selecionar uma rede virtual do Azure
Ao selecionar uma rede virtual para usar com os Serviços de Domínio do Azure AD, lembre-se das seguintes diretrizes:

- Certifique-se de selecionar uma rede virtual em uma região que tem suporte pelos Serviços de Domínio do Azure AD. A lista atual de regiões do Azure com suporte está disponível na página [regiões](active-directory-ds-regions.md).
- Se você planeja usar uma rede virtual existente, certifique-se de que se trata de uma rede virtual regional. Redes virtuais que usam o mecanismo de grupos de afinidade herdado não podem ser usadas com os Serviços de Domínio do Azure AD. Você precisará [migrar redes virtuais herdadas para redes virtuais regionais](../virtual-networks-migrate-to-regional-vnet.md).
- Selecione a rede virtual que atualmente hospeda/hospedará máquinas virtuais que precisam de acesso aos Serviços de Domínio do Azure AD. Você não poderá mover os Serviços de Domínio para outra rede virtual posteriormente.


## Etapa 2: Criar uma rede virtual do Azure
A próxima etapa de configuração é criar uma rede virtual do Azure na qual você deseja habilitar os Serviços de Domínio do Azure AD. Se já tiver uma rede virtual que prefere usar, você pode ignorar esta etapa.

> [AZURE.NOTE]Certifique-se de que a rede virtual do Azure que você criar ou escolher usar com os Serviços de Domínio do Azure AD pertença a uma região do Azure com suporte dos Serviços de Domínio do Azure AD. Consulte a página de [regiões](active-directory-ds-regions.md) para ver uma lista das regiões do Azure onde os Serviços de Domínio do Azure AD estão disponíveis.

Você precisará anotar o nome da rede virtual para selecionar a rede virtual correta ao habilitar os Serviços de Domínio do Azure AD em uma etapa de configuração subsequente.

Realize as seguintes etapas de configuração para criar uma rede virtual do Azure na qual você deseja habilitar os Serviços de Domínio do Azure AD.

1. Navegue até o **portal de gerenciamento do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selecione o nó **Redes** no painel esquerdo.
3. Clique em **NOVO** no painel de tarefas na parte inferior da página.

    ![Nó redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. No nó **Serviços de Rede**, selecione **Rede Virtual**.
5. Clique em **Criação Rápida** para criar uma rede virtual.

    ![Rede virtual - criação rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Digite um **Nome** para a sua rede virtual. Você também pode optar por configurar o **Espaço de endereço** ou a **Contagem máxima de VMs** para essa rede. Você pode deixar a configuração do servidor DNS definida como “None” por enquanto. Essa configuração será atualizada após você habilitar os Serviços de Domínio do Azure AD.
7. Verifique se você selecionou uma região do Azure com suporte na lista suspensa **Local**. Consulte a página de [regiões](active-directory-ds-regions.md) para ver uma lista das regiões do Azure nos quais os Serviços de Domínio do Azure AD estão disponíveis. Essa é uma etapa importante. Se selecionar uma rede virtual em uma região do Azure que não tem suporte pelos Serviços de Domínio do Azure AD, você não poderá habilitar o serviço na rede virtual em questão.
8. Clique no botão **Criar uma Rede Virtual** para criar sua rede virtual.

    ![Criar uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**Próxima etapa - Habilitar os Serviços de Domínio do Azure AD.**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=Oct15_HO3-->
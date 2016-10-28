<properties
	pageTitle="Serviços de Domínio do AD do Azure: Criar ou selecionar uma rede virtual | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/03/2016"
	ms.author="maheshu"/>

# Criar ou selecionar uma rede virtual para os Serviços de Domínio do Azure AD

## Diretrizes para selecionar uma rede virtual do Azure
> [AZURE.NOTE] **Antes de começar**: consulte [Considerações de rede para os Serviços de Domínio do Azure AD](active-directory-ds-networking.md).


## Tarefa 2: criar uma rede virtual do Azure
A próxima tarefa de configuração é criar uma rede virtual do Azure e uma sub-rede dentro dela. Você pode habilitar os Serviços de Domínio do Azure AD nessa sub-rede dentro de sua rede virtual. Se já tiver uma rede virtual que prefere usar, você pode ignorar esta etapa.

> [AZURE.NOTE] Certifique-se de que a rede virtual do Azure que você criar ou escolher usar com os Serviços de Domínio do Azure AD pertença a uma região do Azure com suporte dos Serviços de Domínio do Azure AD. Confira a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.

Anote o nome da rede virtual para selecionar a rede virtual correta ao habilitar os Serviços de Domínio do Azure AD em uma etapa de configuração subsequente.

Realize as seguintes etapas de configuração para criar uma rede virtual do Azure na qual você deseja habilitar os Serviços de Domínio do Azure AD.

1. Navegue até o **portal clássico do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó **Redes** no painel esquerdo.

    ![Nó de redes](./media/active-directory-domain-services-getting-started/networks-node.png)

3. Clique em **NOVO** no painel de tarefas na parte inferior da página.

    ![Nó redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. No nó **Serviços de Rede**, selecione **Rede Virtual**.

5. Clique em **Criação Rápida** para criar uma rede virtual.

    ![Rede virtual - criação rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Digite um **Nome** para a sua rede virtual. Você também pode optar por configurar o **Espaço de endereço** ou a **Contagem máxima de VMs** para essa rede. Você pode deixar a configuração do **servidor DNS** definida como ‘None’ por enquanto. É possível atualizar a configuração do servidor DNS depois de habilitar os Serviços de Domínio do Azure AD.

7. Verifique se você selecionou uma região do Azure com suporte na lista suspensa **Local**. Confira a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.

8. Para criar sua rede virtual, clique no botão **Criar uma Rede Virtual**.

    ![Criar uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Depois de criar a rede virtual, selecione a rede virtual e clique na guia **CONFIGURAR**.

    ![Criar uma sub-rede](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Navegue até a seção **espaços de endereço de rede virtual**. Clique em **Adicionar sub-rede** e especifique uma sub-rede com o nome **AaddsSubnet**. Clique em **Salvar** para criar a sub-rede.

    ![Crie uma sub-rede para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## Tarefa 3: habilitar os Serviços de Domínio do AD do Azure
A próxima tarefa de configuração é [habilitar os Serviços de Domínio do Azure AD](active-directory-ds-getting-started-enableaadds.md).

<!---HONumber=AcomDC_1005_2016-->
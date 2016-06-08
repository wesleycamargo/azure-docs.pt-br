<properties
	pageTitle="Definir as configurações de imagem do Azure Marketplace em um çaboratório | Microsoft Azure"
	description="Configurar quais imagens do Azure Marketplace podem ser usadas ao criar uma VM em Laboratórios de Desenvolvimento/Teste"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Definir as configurações de imagem do Azure Marketplace em Laboratórios de Desenvolvimento/Teste

## Visão geral

Os Laboratórios de Desenvolvimento/Teste oferecem suporte à criação de novas VMs com base em imagens do Azure Marketplace, dependendo de como você configurou o uso das imagens do Azure Marketplace em seu laboratório. Este artigo mostrará como especificar quais imagens, se houver alguma, do Azure Marketplace podem ser usadas durante a criação de novas VMs em um laboratório.

## Selecionar quais imagens do Azure Marketplace são permitidas durante a criação de uma VM

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Toque em **Procurar** e toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. A folha **Configurações** do laboratório selecionado será exibida.

1. Na folha **Configurações**, toque em **Imagens do Marketplace**

	![Configurar como as imagens do Azure Marketplace são usadas](./media/devtest-lab-configure-marketplace-images/lab-settings-marketplace-images.png)

1. Especifique se você deseja que todas as imagens qualificadas do Azure Marketplace estejam disponíveis para uso como uma base de uma nova VM. Se você selecionar **Sim**, todas as imagens do Azure Marketplace que atenderem a todos os critérios a seguir serão permitidos no laboratório:

	- A imagem cria uma única VM **e**
	- A imagem usa o Azure Resource Manager para provisionar VMs **e**
	- A imagem não exige a compra de um plano de licenciamento extra
	
	Se você não quiser permitir qualquer imagem, ou se quiser especificar quais imagens poderão ser usadas, selecione **Não**.
 
	![Opção para permitir que todas as imagens do Marketplace sejam usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Se você selecionar **Não** na etapa anterior, a caixa de seleção **Imagens permitidas/Selecionar todas** será habilitada. Você pode usar essa opção junto com a caixa de pesquisa para marcar ou desmarcar rapidamente todos os itens exibidos na lista. Você também pode selecionar individualmente as imagens do Azure Marketplace que deseja permitir para a criação da VM, marcando a caixa de seleção correspondente à cada imagem. Não selecione nada na lista se não quiser permitir que as imagens do Azure Marketplace sejam usadas no laboratório.

	![Você pode especificar quais imagens do Marketplace podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

## Próximas etapas

Depois de configurar como as imagens do Azure Marketplace são permitidas durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0518_2016-->
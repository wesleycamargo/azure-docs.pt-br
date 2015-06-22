<properties 
    pageTitle="Criar uma imagem do RemoteApp com base em uma VM do Azure"
    description="Saiba como criar uma imagem para o RemoteApp começando com uma máquina virtual do Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/27/2015" 
    ms.author="elizapo" />



# Criar uma imagem do RemoteApp com base em uma máquina virtual do Azure

Com base em muitos comentários dos clientes, temos a satisfação de agora dá suporte à criação de imagens do RemoteApp (que mantêm os aplicativos que você compartilha na sua coleção) de uma máquina virtual do Azure. Também adicionamos uma imagem de VM do Azure à galeria de imagens da máquina virtual do Azure que atende a todos os requisitos de imagem do RemoteApp - você pode usar essa imagem de VM como ponto de partida para a sua própria VM se desejar. Basta procurar a imagem "Host da Sessão da Área de Trabalho Remota do Windows Server" na biblioteca.

Há duas etapas - criar a imagem e, em seguida, carregá-la da biblioteca de VM do Azure para o RemoteApp.

## Criando uma imagem personalizada com base em uma VM do Azure

Use estas etapas para criar uma imagem com base em uma VM do Azure.

1. Crie uma máquina virtual do Azure. Você pode usar a imagem do "Host da Sessão de Área da Trabalho Remota do Windows Server" da galeria de imagens da máquina virtual do Azure. Essa imagem atende a todos os requisitos de imagem de modelo do Azure RemoteApp. 

	Para obter detalhes, consulte [Criar uma VM que execute o Windows](virtual-machines-windows-tutorial.md).

2. Conecte-se à VM e instale e configure os aplicativos que deseja compartilhar por meio do RemoteApp. Certifique-se de executar quaisquer configurações adicionais do Windows necessárias para seus aplicativos. 

	Para mais detalhes, consulte [Como fazer logon em uma máquina virtual que execute o Windows Server](virtual-machines-log-on-windows-server.md). 

3. Se você estiver usando a imagem do Host da Sessão da Área de Trabalho Remota do Windows Server, há um script de validação incluído que garantirá a que sua VM cumpra os pré-requisitos do RemoteApp. Para executar o script, clique duas vezes em **ValidateRemoteAppImage** na área de trabalho. Certifique-se de que todos os erros relatados pelo script sejam corrigidos antes de prosseguir para a próxima etapa.

4. O SYSPREP generaliza e captura a imagem. Consulte [Como capturar uma máquina virtual do Windows para usar como modelo](../virtual-machines-capture-image-windows-server.md) para obter instruções.

 

## Importar a imagem para a biblioteca de imagens do RemoteApp

Use estas etapas para importar a nova imagem para o RemoteApp:

1. Na guia **Imagens de Modelo**:
	- Se você não tiver nenhuma imagem existente, clique em **Carregar ou Importar uma Imagem de Modelo**. 
	- Se você já tiver pelo menos uma imagem, clique em **+** para adicionar uma nova imagem.

2. Selecione a biblioteca **Importar uma imagem de suas Máquinas Virtuais** e clique em **Próximo**.

3. Na próxima página, selecione a imagem personalizada na lista e verifique se você seguiu as etapas listadas ao criar a imagem. Clique em **Avançar**.
4. Insira um nome para a nova imagem do RemoteApp, selecione o local e clique na marca de seleção para iniciar o processo de importação.

> [AZURE.NOTE] Você pode importar imagens de qualquer local do Azure com suporte nas Máquinas Virtuais do Azure para qualquer local do Azure que tenha suporte no Azure RemoteApp. Dependendo dos locais, a importação pode levar até 25 minutos.

Agora você está pronto para criar a nova coleção, seja uma coleção de [nuvem](remoteapp-create-cloud-deployment.md) ou uma coleção [híbrida](remoteapp-create-hybrid-deployment.md), dependendo das suas necessidades.


<!--HONumber=52--> 
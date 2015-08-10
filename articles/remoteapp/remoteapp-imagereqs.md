
<properties 
    pageTitle="Requisitos de imagem do Azure RemoteApp"
    description="Saiba mais sobre os requisitos para a criação de imagens a serem usadas com o Azure RemoteApp" 
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
    ms.date="07/06/2015" 
    ms.author="elizapo" />



# Requisitos para as imagens do Azure RemoteApp
O Azure RemoteApp usa uma imagem do Windows Server 2012 R2 para hospedar todos os programas que deseja compartilhar com os seus usuários. Para criar uma imagem personalizada, você pode iniciar com uma imagem existente ou [criar uma nova](remoteapp-create-custom-image.md).

> [AZURE.TIP]Você sabia que a sua assinatura do RemoteApp do Azure fornece acesso a uma imagem do Windows Server 2012 R2 na galeria de VM do Azure que você pode usar para criar a sua própria imagem de modelo? [Confira](remoteapp-image-on-azurevm.md).


Os requisitos para a imagem passiva de upload para o uso com o RemoteApp do Azure são:


- Aplicativos personalizados não armazenam dados localmente na imagem. Essas imagens são sem monitoração de estado e devem conter apenas aplicativos.
- A imagem não contém dados que podem ser perdidos.
- O tamanho da imagem deve ser um múltiplo de MBs. Se você tentar carregar uma imagem que não é um múltiplo exato, o carregamento falhará.
- O tamanho da imagem deve ser de 127 GB ou menor. 
- Deve estar em um arquivo VHD (arquivos VHDX atualmente não têm suporte).
- O VHD não deve ser uma máquina virtual de geração 2.
- O VHD podem ter tamanho fixo ou expandir dinamicamente. O VHD de expansão dinâmica é recomendado, pois demora menos para fazer o upload do Azure que o arquivo VHD de tamanho fixo.
- O disco deve ser inicializado usando o estilo de particionamento do Registro mestre de inicialização (MBR). O estilo de particionamento da tabela de partição GUID (GPT) não tem suporte. 
- O VHD deve conter uma instalação única do Windows Server 2012 R2. Ele deve conter vários volumes, mas apenas um que contenha uma instalação do Windows. 
- A função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience devem estar instalados.
- A função do Agente de Conexão de Área de Trabalho Remota *não* deve ser instalada.
- O Encrypting File System (EFS) deve estar desabilitado.
- A imagem deve ser SYSPREPed usando os parâmetros **/oobe /generalize /shutdown** (NÃO use o parâmetro **/mode:vm**).
- Não há suporte para carregar o VHD de uma cadeia de instantâneo.
 

<!---HONumber=July15_HO5-->
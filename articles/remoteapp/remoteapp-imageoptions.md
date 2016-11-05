---
title: Criar uma imagem do RemoteApp do Azure | Microsoft Docs
description: Saiba mais sobre as opções disponíveis para a criação de imagens para o RemoteApp do Azure
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Criar uma imagem de RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

O Azure RemoteApp usa imagens para manter os aplicativos que você compartilha com seus usuários. (Obtemos sua imagem e a usamos para criar VMs – e é isso o que os usuários acessam quando entram no Azure RemoteApp.) Para criar uma coleção do RemoteApp do Azure com sua escolha de aplicativos, seja ela de nuvem ou híbrida, você começa criando uma imagem com os aplicativos instalados. Em seguida, crie uma coleção que usa essa imagem, atribua usuários à coleção e publique aplicativos aos usuários.

Você tem várias opções para criar ou usar as imagens. O [requisito](remoteapp-imagereqs.md) básico para uma imagem é ela executar o Windows Server 2012 R2 e ter a função de Host da Sessão da Área de Trabalho Remota (RDSH) instalada. Como você obtém isso é onde as coisas ficam interessantes.

Você tem as seguintes opções quando se trata de imagens:

* Você pode importar e usar uma [imagem com base em uma máquina virtual do Azure](remoteapp-image-on-azurevm.md). Isso é bom para aplicativos de linha de negócios que requerem configurações personalizadas. Você pode personalizar a imagem para trabalhar para o aplicativo.
* Você pode [criar e carregar uma imagem personalizada](remoteapp-create-custom-image.md). Isso é bom se você já tiver uma imagem que use para sua implantação de Serviços de Área de Trabalho Remota local.
* Você pode usar uma das [imagens de modelo](remoteapp-images.md) incluídas na sua assinatura do RemoteApp. Essas imagens são criadas e mantidas pela equipe do RemoteApp e contêm alguns aplicativos padrão (como o pacote do Office) que você pode disponibilizar para os usuários. Observe que apenas a imagem do Office 365 Pro Plus pode ser usada em um ambiente de produção.

Independentemente de onde obter sua imagem ou como criá-la, convém certificar-se de que você entende os [requisitos de aplicativo](remoteapp-appreqs.md) para garantir que seu aplicativo funcione bem no RemoteApp. Em seguida, a próxima etapa é criar uma coleção de [nuvem](remoteapp-create-cloud-deployment.md) ou [híbrida](remoteapp-create-hybrid-deployment.md).

<!---HONumber=AcomDC_0817_2016-->
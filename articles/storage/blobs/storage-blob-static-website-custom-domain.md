---
title: 'Tutorial: Habilitar o domínio personalizado com o SSL em um site estático usando a CDN do Azure – Armazenamento do Azure'
description: Saiba como configurar um domínio personalizado para hospedagem de site estático.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110252"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Tutorial: Usar a CDN do Azure para habilitar um domínio personalizado com SSL para um site estático

Este tutorial é a parte dois de uma série. Nele, você aprenderá a habilitar um ponto de extremidade de domínio personalizado com SSL para seu site estático. 

O tutorial mostra como usar a [CDN do Azure](../../cdn/cdn-overview.md) para configurar o ponto de extremidade de domínio personalizado para o site estático. Com a CDN do Azure, é possível provisionar certificados SSL personalizados, usar um domínio personalizado e configurar regras de reescrita personalizadas ao mesmo tempo. Configurar a CDN do Azure resulta em encargos adicionais, mas oferece baixas latências consistentes a seu site em qualquer lugar do mundo. A CDN do Azure também fornece criptografia SSL com o próprio certificado. Para saber mais sobre os preços da CDN do Azure, confira [Azure CDN pricing](https://azure.microsoft.com/pricing/details/cdn/) (Preços da CDN do Azure).

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Criar um ponto de extremidade da CDN no ponto de extremidade de site estático
> * Habilitar domínio personalizado e SSL

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, conclua a parte um, [Tutorial: Hospedar um site estático no Armazenamento de Blobs](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/) para começar.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Criar um ponto de extremidade da CDN no ponto de extremidade de site estático

1. Abra o [portal do Azure](https://portal.azure.com/) no navegador da Web. 
1. Localize sua conta de armazenamento e exiba a visão geral dela.
1. Selecione **CDN do Azure** no menu **Serviço Blob** para configurar a CDN do Azure.
1. Na seção **Novo ponto de extremidade**, preencha os campos para criar um novo ponto de extremidade da CDN.
1. Insira um nome de ponto de extremidade, como *mystaticwebsiteCDN*.
1. Insira seu domínio de site como o nome do host para seu ponto de extremidade de CDN.
1. Para o nome do host de origem, insira o ponto de extremidade do site estático. Para localizar o ponto de extremidade do site estático, navegue até a seção **Site estático** da sua conta de armazenamento e copie o ponto de extremidade. 
1. Teste o ponto de extremidade da CDN navegando até *mywebsitecdn.azureedge.net* no navegador.

## <a name="enable-custom-domain-and-ssl"></a>Habilitar domínio personalizado e SSL

1. Criar um registro CNAME com o provedor de nomes de domínio para redirecionar o domínio personalizado para o ponto de extremidade da CDN. O registro CNAME para o subdomínio *www* deve ser semelhante ao seguinte:

    ![Especificar registro CNAME para subdomínio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. No portal do Azure, clique no ponto de extremidade recém-criado para configurar o domínio personalizado e o certificado SSL.
1. Selecione **Adicionar domínio personalizado**, insira o nome de domínio e clique em **Adicionar**.
1. Selecione o mapeamento de domínio personalizado recém-criado para provisionar um certificado SSL.
1. Defina **HTTPS de domínio personalizado** como **ATIVADO**. Selecione **CDN gerenciada** para fazer a CDN do Azure gerenciar seu certificado SSL. Clique em **Salvar**.
1. Teste seu site acessando a URL dele.

## <a name="next-steps"></a>Próximas etapas

Na segunda parte deste tutorial, você aprendeu como configurar um domínio personalizado com SSL na CDN do Azure para o site estático.

Siga este link para saber mais sobre a hospedagem de site estático no Armazenamento do Azure.

> [!div class="nextstepaction"]
> [Saiba mais sobre sites estáticos](storage-blob-static-website.md)

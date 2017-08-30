---
title: "Configurando credenciais de autenticação nomeadas | Microsoft Docs"
description: "Saiba como fornecer credenciais que o Visual Studio pode usar para autenticar solicitações no Azure para publicar um aplicativo no Azure do Visual Studio ou para monitorar um serviço de nuvem existente. "
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/17/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 613f17081fcb70b126caaae7ade5739d336662a7
ms.contentlocale: pt-br
ms.lasthandoff: 08/19/2017

---
# <a name="setting-up-named-authentication-credentials"></a>Configurando credenciais de autenticação nomeadas
Para publicar um aplicativo no Azure do Visual Studio ou para monitorar um serviço de nuvem existente, você deve fornecer credenciais que o Visual Studio pode usar para autenticar solicitações no Azure. Existem diversos lugares no Visual Studio nos quais você pode entrar para fornecer essas credenciais. Por exemplo, no Gerenciador de Servidores, você pode abrir o menu de atalho do nó do **Azure** e escolher **Conectar-se à Assinatura do Microsoft Azure...**. Quando você entra, as informações de assinatura associadas à sua conta do Azure estão disponíveis no Visual Studio e não é necessário fazer mais nada.

As Ferramentas do Azure também dão suporte a uma maneira mais antiga de fornecer credenciais, usando o arquivo de assinatura (arquivo .publishsettings). Este tópico descreve esse método, que ainda tem suporte no Azure SDK 2.2.

Os itens a seguir são necessários para autenticação no Azure:

* Sua ID de assinatura
* Um certificado X.509 v3 válido

> [!NOTE]
> O comprimento da chave do certificado X.509 v3 deve ser pelo menos 2.048 bits. O Azure descartará qualquer certificado que não atenda a esse requisito ou que não seja válido.
>
>

O Visual Studio usa sua ID da assinatura junto com os dados do certificado como credenciais. As credenciais apropriadas são referenciadas no arquivo de assinatura (arquivo .publishsettings), que contém uma chave pública para o certificado. O arquivo de assinatura pode conter credenciais para mais de uma assinatura.

Você pode editar as informações de assinatura da caixa de diálogo **Nova/Editar Assinatura** , conforme explicado posteriormente neste tópico.

Se você deseja criar um certificado por conta própria, pode consultar as instruções em [Criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e carregar manualmente o certificado para o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Essas credenciais que o Visual Studio requer para gerenciar seus serviços de nuvem não são as mesmas credenciais necessárias para autenticar uma solicitação nos serviços de armazenamento do Azure.
>
>

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modificar ou exportar credenciais de autenticação no Visual Studio
Você também pode configurar, modificar ou exportar suas credenciais de autenticação na nova caixa de diálogo **Assinatura** que aparece se você realizar uma das seguintes ações:

* Em **Gerenciador de Servidores**, abra o menu de atalho para o nó do **Azure**, escolha **Gerenciar e Filtrar Assinaturas...**, escolha a guia **Certificados** e escolha **Importar**, **Novo** ou **Editar**.
* Quando você publica um serviço de nuvem do Azure por meio do assistente **Publicar Aplicativo do Azure**, escolha **Gerenciar** na lista **Escolher sua Assinatura**, depois a guia Certificados e, em seguida, o botão **Novo** ou **Editar**.

O procedimento a seguir pressupõe que a caixa de diálogo **Nova Assinatura** está aberta.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Para configurar credenciais de autenticação no Visual Studio
1. Na lista **Selecionar um certificado existente para autenticação** , escolha um certificado.
2. Escolha o botão **Copiar o caminho completo**. O caminho para o certificado (arquivo .cer) é copiado para a área de transferência.

   > [!IMPORTANT]
   > Para publicar seu aplicativo Azure do Visual Studio, você deve carregar esse certificado no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   >
   >
3. Para carregar o certificado no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885):

   1. Escolha o link Portal do Azure.

        O [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) abre.
   2. Entre no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e selecione o botão **Serviços de Nuvem** .
   3. Escolha o serviço de nuvem que lhe interessa.

       A página do serviço se abre.
   4. Na guia **Certificados**, escolha o botão **Carregar**.
   5. Cole o caminho completo do arquivo .cer que você acabou de criar e, em seguida, digite a senha que especificou.


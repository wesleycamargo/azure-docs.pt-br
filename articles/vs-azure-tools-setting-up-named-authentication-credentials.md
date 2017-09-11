---
title: "Configurando credenciais de autenticação nomeadas | Microsoft Docs"
description: "Saiba como fornecer credenciais que o Visual Studio pode usar para autenticar solicitações no Azure para publicar um aplicativo no Azure do Visual Studio ou para monitorar um serviço de nuvem existente. "
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c486676a70e195ec85ad40540ea4b7caaa86bc48
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

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

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importar, configurar ou editar as credenciais de autenticação no Visual Studio
Você pode importar, configurar ou modificar suas credenciais de autenticação na nova caixa de diálogo **Assinatura** que aparece se você realizar uma das seguintes ações:

* Em **Gerenciador de Servidores**, abra o menu de atalho para o nó do **Azure**, escolha **Gerenciar e Filtrar Assinaturas...**, escolha a guia **Certificados** e execute um dos procedimentos a seguir:

    * Escolha **Importar** para abrir a caixa de diálogo Importar Assinaturas do Microsoft Azure onde você pode baixar o arquivo de assinaturas da assinatura carregada atualmente, navegue até o local de download e, depois, importe-o para uso na autenticação.
    * Escolha **Novo** para abrir a caixa de diálogo Nova Assinatura onde você pode configurar uma nova assinatura para uso em autenticação.
    * Escolha **Editar** (depois de escolher sua assinatura ativa) para abrir a caixa de diálogo Editar Assinatura onde você pode editar uma assinatura existente para uso em autenticação. 

O procedimento a seguir pressupõe que a caixa de diálogo **Nova Assinatura** está aberta.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Para configurar credenciais de autenticação no Visual Studio
1. Na lista **Selecionar um certificado existente para autenticação** , escolha um certificado.
2. Escolha o link **Copiar o caminho completo**. O caminho para o certificado (arquivo .cer) é copiado para a área de transferência.

   > [!IMPORTANT]
   > Para publicar seu aplicativo do Azure do Visual Studio, você deve carregar esse certificado no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Para carregar o certificado no Portal do Azure:

   1. Abra o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   2. Se receber uma solicitação, entre no portal e navegue até **Configurações**, **Certificados de Gerenciamento**.
   3. No painel Certificados de gerenciamento, escolha **Carregar**.
   4. Selecione sua assinatura do Azure, cole o caminho completo do arquivo .cer que você acabou de criar e escolha **Carregar**.


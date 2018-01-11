---
title: "Configurar um nome de domínio personalizado para a sua instância de Gerenciamento de API do Azure | Microsoft Docs"
description: "Este tópico descreve como configurar um nome de domínio personalizado para sua instância de Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: cf8a3eb502a808945e97822e10e44d38137d1161
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado 

Quando você cria uma instância de Gerenciamento de API (APIM), o Azure a atribui a um subdomínio do azure-api.net (por exemplo, `apim-service-name.azure-api.net`). No entanto, você também pode expor seus pontos de extremidade do APIM usando seu próprio nome de domínio, como **contoso.com**. Este tutorial mostra como mapear um nome DNS personalizado existente para pontos de extremidade expostos por uma instância de Gerenciamento de API do Azure.

## <a name="prerequisites"></a>pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Um nome de domínio personalizado que pertence a você. O nome de domínio personalizado que você deseja usar deve ser adquirido separadamente e hospedado em um servidor DNS. Este tópico não dá instruções sobre como hospedar um nome de domínio personalizado.
+ Você deve ter um certificado válido com chaves pública e privada (.PFX). A entidade ou o SAN (nome alternativo da entidade) deve corresponder ao nome de domínio (isso permite que o APIM exponha com segurança URLs sobre SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usar o portal do Azure para definir um nome de domínio personalizado

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Domínios personalizados e SSL**.
    
    Há vários pontos de extremidade aos quais você pode atribuir um nome de domínio personalizado. No momento, os seguintes pontos de extremidade estão disponíveis: 
    + **Proxy** (o padrão é: `<apim-service-name>.azure-api.net`), 
    + **Portal** (o padrão é: `<apim-service-name>.portal.azure-api.net`),     
    + **Gerenciamento** (o padrão é: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (o padrão é: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > Você pode atualizar todos os pontos de extremidade ou alguns deles. Normalmente, os clientes atualizam **Proxy** (essa URL é usada para chamar a API exposta por meio do Gerenciamento de API) e **Portal** (URL do portal do desenvolvedor). Os pontos de extremidade **Gerenciamento** e **SCM** são usados internamente pelos clientes do APIM e, portanto, recebem com menos frequência um nome de domínio personalizado.
3. Selecione o ponto de extremidade que você deseja atualizar. 
4. Na janela à direita, clique em **Personalizado**.

    + Em **Nome de domínio personalizado**, especifique o nome que você deseja usar. Por exemplo, `api.contoso.com`. <br/>Nomes de domínio curinga (por exemplo, *.domain.com) também têm suporte.
    + No **Certificado**, especifique um arquivo .PFX válido que você deseje carregar. 
    + Se o certificado tiver uma senha, insira-a no campo **Senha**.
1. Clique em Aplicar.

    >[!NOTE]
    >O processo de atribuição do certificado pode levar aproximadamente 15 minutos.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Próximas etapas

[Atualizar e colocar em escala o serviço](upgrade-and-scale.md)
---
title: Ativar aplicativos de vários usuários com os Gêmeos Digitais do Azure| Microsoft Docs
description: Noções básicas sobre como registrar os locatários do Azure Active Directory dos seus clientes com Gêmeos Digitais do Azure
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259880"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Ativar aplicativos de vários usuários com Gêmeos Digitais do Azure

Os desenvolvedores que usam os Gêmeos Digitais do Azure geralmente desejam criar aplicativos para vários usuários. Um aplicativo de *multilocatário* é uma única instância provisionada que suporta vários clientes. Cada cliente tem seus próprios dados e privilégios independentes.

Este documento detalha como criar um aplicativo para vários usuários dos Gêmeos Digitais do Azure que ofereça suporte a vários inquilinos e clientes do Azure AD (Azure Active Directory).

## <a name="scenario-summary"></a>Resumo do cenário

Nesse cenário, considere D de desenvolvedor e C de cliente:

- O desenvolvedor D tem uma assinatura do Azure com um locatário do Azure Active Directory.
- O desenvolvedor D implanta uma instância dos Gêmeos Digitais do Azure em sua assinatura do Azure.
- Os usuários do locatário Azure Active Directory do Developer D podem obter tokens em relação ao serviço Azure Digital Twins porque o Azure AD criou uma entidade de serviço no locatário Azure AD do Developer D.
- O desenvolvedor D agora cria um aplicativo para dispositivos móveis que se integra diretamente à API de gerenciamento de Gêmeos Digitais do Azure.
- O desenvolvedor D permite ao cliente C o uso do aplicativo móvel.
- O cliente C deve estar autorizado a usar a API de gerenciamento dos Gêmeos Digitais do Azure no aplicativo do desenvolvedor.

  > [!IMPORTANT]
  > - Quando o Cliente C efetua login no aplicativo do Desenvolvedor D, o aplicativo não pode adquirir tokens para que os usuários do Cliente C conversem com a API de Gerenciamento.
  > - O Microsoft Azure Active Directory lança um erro, que indica que o Azure Digital Twins não é reconhecido no diretório do Cliente C.

## <a name="solution"></a>Solução

Para resolver o cenário anterior, as seguintes ações são necessárias para criar uma entidade de serviço dos Gêmeos Digitais do Azure no locatário do Azure Active Directory do Customer C:

- Se o Cliente C ainda não tiver uma assinatura do Azure com um locatário do Microsoft Azure Active Directory:

  - O administrador de locatários do Microsoft Azure Active Directory do cliente C deve adquirir uma [assinatura do Azure pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - O administrador do locatário do Microsoft Azure Active Directory do cliente C deve [vincular seu locatário à nova assinatura](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- No [Portal do Azure](https://portal.azure.com), o administrador do locatário do Microsoft Azure Active Directory do Cliente C executa as seguintes etapas:

  1. Abrir **assinaturas**.
  1. Selecione a assinatura que tem um locatário do Microsoft Azure Active Directory para ser usado em aplicativo do D do desenvolvedor.
  1. Selecione **Provedores de Recursos**.
  1. Pesquise **Microsoft.IoTSpaces**.
  1. Selecione **Registrar**.
  
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as funções definidas pelo usuário com os Gêmeos Digitais do Azure, leia [UDFs do Azure Digital Twins](how-to-user-defined-functions.md).

Para saber como usar o controle de acesso baseado em função para proteger ainda mais o aplicativo com as atribuições de função, leia [Gêmeos Digitais do Azure baseado em função de controle de acesso](security-create-manage-role-assignments.md).

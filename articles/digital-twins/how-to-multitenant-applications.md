---
title: Como habilitar aplicativos multilocatários com Gêmeos Digitais do Azure | Microsoft Docs
description: Noções básicas sobre como registrar os locatários do Azure Active Directory dos seus clientes com Gêmeos Digitais do Azure
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323709"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Como habilitar aplicativos multilocatários com Gêmeos Digitais do Azure

Os desenvolvedores que usam a de Gêmeos Digitais do Azure provavelmente desejarão criar aplicativos de multilocatários. Um *aplicativo multilocatário* é uma única instância provisionada usada para dar suporte a vários clientes cada com seus próprios dados independentes e privilégios.

Este documento fornece detalhes sobre como criar um aplicativo de multilocatário de Gêmeos Digitais do Azure que dão suporte a vários locatários do Azure Active Directory (AD) e aos clientes.

## <a name="scenario-summary"></a>Resumo do cenário

Nesse cenário, considere D de desenvolvedor e C de cliente:

- O Desenvolvedor D tem uma assinatura do Azure com um locatário do Microsoft Azure Active Directory.
- O Desenvolvedor D implantou uma instância de Gêmeos Digitais em sua assinatura do Azure.
- Usuários no locatário do Microsoft Azure Active Directory do Desenvolvedor D podem obter tokens em relação ao serviço de Gêmeos Digitais, pois o Azure Active Directory tem um entidade de serviço criada no locatário do Azure Active Directory do desenvolvedor D.
- Agora, o desenvolvedor D cria um aplicativo móvel que se integra diretamente com a API de Gerenciamento de Gêmeos Digitais.
- O desenvolvedor D, em seguida, permite que ao cliente C o uso do aplicativo móvel.
- Agora, o cliente C precisará ser autorizado a usar a API de gerenciamento dos Gêmeos Digitais dentro do aplicativo do desenvolvedor D.

  > [!IMPORTANT]
  > - Quando cliente C fizer logon no aplicativo do desenvolvedor D, o aplicativo não poderá adquirir tokens para usuários do cliente C para se comunicar com a API de gerenciamento.
  > - Em seguida, o Azure Active Directory irá gerar um erro indicando que os Gêmeos Digitais não foram reconhecidos dentro do diretório do Cliente C.

## <a name="solution"></a>Solução

Para resolver o cenário acima, as seguintes ações são necessárias para criar um serviço principal de Gêmeos Digitais no locatário do Microsoft Azure Active Directory do cliente C:

- Se o cliente C ainda não tiver uma assinatura do Azure com o locatário do Microsoft Azure Active Directory:

  - O Administrador de locatário do Microsoft Azure Active Directory do cliente do C precisará adquirir uma [assinatura paga do Azure](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - O Administrador de Locatário do Microsoft Azure Active Directory do cliente do C, em seguida, será preciso [vincular seu locatário com a nova assinatura](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Do [Portal do Azure](https://portal.azure.com), Administrador de Locatário do Microsoft Azure Active Directory do cliente C, em seguida, deve:
  1. Abrir **assinaturas**.
  1. Selecione a assinatura que tem um locatário do Azure Active Directory para ser usado em aplicativo do Desenvolvedor D.
  1. Selecione **Provedores de Recursos**.
  1. Pesquise **Microsoft.IoTSpaces**.
  1. Clique em **Registrar**.
  
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar funções definidas pelo usuário com Gêmeos Digitais do Azure, leia [UDFs dos Gêmeos Digitais do Azure](how-to-user-defined-functions.md).

Para saber como usar o controle de acesso baseado em função para proteger ainda mais o aplicativo com as atribuições de função, leia [controle de acesso baseado em função de Gêmeos Digitais](security-create-manage-role-assignments.md).

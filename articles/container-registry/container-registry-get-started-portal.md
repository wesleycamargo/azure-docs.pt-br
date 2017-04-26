---
title: Criar registro privado do Docker - portal do Azure | Microsoft Docs
description: "Introdução à criação e ao gerenciamento de contêiner privado do Docker com o portal do Azure"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Criar um registro de contêiner privado do Docker usando o portal do Azure
Use o portal do Azure para criar um registro de contêiner e gerenciar suas configurações. Também é possível criar e gerenciar registros de contêiner usando os [comandos da CLI 2.0 do Azure](container-registry-get-started-azure-cli.md) ou programaticamente com a [API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) do Registro de Contêiner.

Para obter mais informações e conceitos, confira a [visão geral](container-registry-intro.md).



## <a name="create-a-container-registry"></a>Criar um registro de contêiner
1. No [portal do Azure](https://portal.azure.com), clique em **+Novo**.
2. Pesquise no marketplace para localizar o **registro de contêiner do Azure**.
3. Selecione **Registro de Contêiner do Azure** com o editor **Microsoft**.
    ![Serviço de registro de contêiner no Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Clique em **Criar**. A folha **Registro de Contêiner do Azure** é exibida.

    ![Configurações de registro de contêiner](./media/container-registry-get-started-portal/container-registry-settings.png)
5. Na folha **Registro de Contêiner do Azure**, insira as informações a seguir. Clique em **Criar** quando terminar.

    a. **Nome do registro**: um nome de domínio de nível superior exclusivo para o registro específico. Neste exemplo, o nome do registro é *myRegistry01*, mas substitua-o por um nome exclusivo. O nome pode conter apenas letras e números.

    b. **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo.

    c. **Local**: selecione um local de datacenter do Azure em que o serviço está [disponível](https://azure.microsoft.com/regions/services/), como **Centro-Sul dos EUA**.

    d. **Usuário administrador**: se desejar, habilite um usuário administrador para acessar o registro. Você pode alterar essa configuração após a criação do registro.

    > [!IMPORTANT]
    > Além de fornecer acesso por meio de uma conta de usuário administrador, registros de contêiner dão suporte à autenticação com o apoio de entidades de serviço do Azure Active Directory. Para obter mais informações e considerações, confira [Autenticar em um registro de contêiner](container-registry-authentication.md).


    e. **Conta de armazenamento**: use a configuração padrão para criar uma [conta de armazenamento](../storage/storage-introduction.md) ou selecione uma conta de armazenamento existente no mesmo local. Atualmente, não há suporte para o Armazenamento Premium.


## <a name="manage-registry-settings"></a>Gerenciar configurações do registro
Depois de criar o registro, localize as configurações do Registro, iniciando na folha **Registros de Contêiner** no portal. Por exemplo, talvez você precise das configurações para fazer logon em seu registro ou precise habilitar ou desabilitar o usuário administrador.

1. Na folha **Registros de Contêiner**, clique no nome do registro.

    ![Folha de registro de contêiner](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Para gerenciar as configurações de acesso, clique em **Chave de acesso**.

    ![Acesso ao registro de contêiner](./media/container-registry-get-started-portal/container-registry-access.png)
3. Observe as seguintes configurações:

   * **Servidor de logon** - o nome totalmente qualificado que você usa para fazer logon no registro. Neste exemplo, é `myregistry01.azurecr.io`.
   * **Usuário administrador** -ative/desative para habilitar ou desabilitar a conta de usuário do administrador do registro.
   * **Nome de usuário** e **senha** - as credenciais da conta de usuário administrador (se habilitado) que você pode usar para fazer logon no registro. Opcionalmente, você pode gerar as senhas de novo. As duas senhas são criadas para que você possa manter conexões com o registro usando uma senha enquanto regenera a outra senha. Para autenticar com uma entidade de serviço, confira [Autenticar com um registro do contêiner privado do Docker](container-registry-authentication.md).

## <a name="next-steps"></a>Próximas etapas
* [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md)


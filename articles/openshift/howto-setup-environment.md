---
title: Configurar seu ambiente de desenvolvimento do Azure Red Hat OpenShift | Microsoft Docs
description: Aqui estão os pré-requisitos para trabalhar com o Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: configuração do Red hat openshift configurar
author: TylerMSFT
ms.author: twhitney
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 7c5fbf135c02abf04e90865e20e902a95174598c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078109"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar seu ambiente de desenvolvimento do Azure Red Hat OpenShift

Para compilar e executar aplicativos do Microsoft Azure Red Hat OpenShift, você precisará:

* Comprar instâncias reservada de máquina de virtual do Azure.
* Instalar versão 2.0.64 (ou superior) da CLI do Azure (ou use o Azure Cloud Shell).
* Inscreva-se a `openshiftmanagedcluster` recurso e provedores de recursos associados.
* Crie um locatário do Azure Active Directory (Azure AD).
* Crie um objeto de aplicativo do Azure AD.
* Crie um usuário do AD do Azure.

As instruções a seguir orientará você por meio de todos esses pré-requisitos.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Comprar instâncias reservada de máquina de virtual do Azure

Antes de usar Azure Red Hat OpenShift, você precisará comprar instâncias reservada de máquina de virtual do Azure.

Se você for um cliente do Azure, aqui como [instâncias reservadas de máquina virtual do Azure de compra](https://aka.ms/openshift/buy). Uma reserva reduz seus gastos por pagar previamente para serviços totalmente gerenciados do Azure. Consulte a [ *o que são Azure reservas* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) para saber mais sobre reservas e como eles economizar seu dinheiro.

Se você não for um cliente do Azure, [entre em contato com vendas](https://aka.ms/openshift/contact-sales) e preencha o formulário na parte inferior da página para iniciar o processo de venda.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Azure Red Hat OpenShift requer a versão 2.0.64 ou posterior da CLI do Azure. Se você já tiver instalado a CLI do Azure, você pode verificar qual versão você possui, executando:

```bash
az --version
```

A primeira linha de saída terá a versão da CLI, por exemplo `azure-cli (2.0.64)`.

Aqui estão as instruções para [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se você precisar de uma nova instalação ou atualização.

Como alternativa, você pode usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ao usar o Azure Cloud Shell, certifique-se de selecionar o **Bash** ambiente se você planeja seguir junto com as [criar e gerenciar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) série de tutoriais.

## <a name="register-providers-and-features"></a>Registrar os provedores e recursos

O `Microsoft.ContainerService openshiftmanagedcluster` recurso `Microsoft.Solutions`, e `Microsoft.Network` provedores devem ser registrados para sua assinatura manualmente antes de implantar seu primeiro cluster do Azure Red Hat OpenShift.

Para registrar esses provedores e os recursos manualmente, use as instruções a seguir de um shell Bash, se você tiver instalado a CLI ou da sessão do Azure Cloud Shell (Bash) no portal do Azure:.
1. Se você tiver várias assinaturas do Azure, especifique a ID de assinatura relevante:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Registre o recurso do containerservice openshiftmanagedcluster:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Registre o provedor Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Registre o provedor Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Atualize o registro do provedor de recurso do containerservice:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um locatário do Azure Active Directory (Azure AD)

O serviço do Azure Red Hat OpenShift requer um locatário do Azure AD (Active Directory do Azure) associado que representa a sua organização e sua relação para a Microsoft. Locatário do Azure AD permite que você se registrar, compilar e gerenciar aplicativos, bem como usar outros serviços do Azure.

Se você não tiver um Azure AD para usar como o locatário para seu cluster do Azure Red Hat OpenShift, ou você deseja criar um locatário para teste, siga as instruções em [criar um locatário do Azure AD para seu cluster do Azure Red Hat OpenShift](howto-create-tenant.md) antes continuar com este guia.

## <a name="create-an-azure-ad-application-object-and-user"></a>Criar um objeto de aplicativo do Azure AD e o usuário

Azure Red Hat OpenShift requer permissões para executar tarefas em seu cluster, como a configuração de armazenamento. Essas permissões são representadas por meio de um [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) e são criadas quando você registrar um aplicativo do Azure AD que representa a carga de trabalho que você pretende hospedar no Azure Red Hat OpenShift. Você também desejará criar um novo usuário do Active Directory para testar aplicativos em execução no cluster do Azure Red Hat OpenShift.

Siga as instruções em [criar um objeto de aplicativo do Azure AD e o usuário](howto-aad-app-configuration.md) para saber como criar uma entidade de serviço, gerar um segredo e a autenticação de retorno de chamada URL do cliente para seu aplicativo e criar um novo usuário do Active Directory para teste.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para usar o Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
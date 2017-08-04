---
title: "Use agentes de VM do Azure para a integração contínua com Jenkins."
description: "Agentes de VM do Azure como Jenkins secundários."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: pt-br
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Use agentes de VM do Azure para a integração contínua com Jenkins.

Este guia de início rápido mostra como usar o plug-in de agentes de VM do Azure do Jenkins para criar um agente do Linux (Ubuntu) sob demanda no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Se você ainda não tiver um Jenkins mestre, você pode iniciar com o [modelo de solução](install-jenkins-solution-template.md) 
* Confira [Como criar uma entidade de serviço do Azure com a CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) se você ainda não tiver uma entidade de serviço do Azure.

## <a name="install-azure-vm-agents-plugin"></a>Instale o plug-in de agentes da VM do Azure

Se você iniciar a partir do [modelo de solução](install-jenkins-solution-template.md), o plug-in do agente de VM do Azure estará instalado no Jenkins mestre.

Caso contrário, instale o plug-in do **agentes de VM do Azure**de dentro do painel do Jenkins.

## <a name="configure-the-plugin"></a>Configuração do plug-in

* No painel do Jenkins, clique em **Gerenciar Jenkins -> Configurar o sistema ->**. Role até a parte inferior da página e localize a seção com a lista suspensa **Adicionar nova nuvem**. No menu, selecione **Agentes de VM do Microsoft Azure**
* Selecione uma conta existente na lista suspensa de credenciais do Azure.  Para adicionar uma nova **entidade de serviço do Microsoft Azure**, insira os seguintes valores: ID de assinatura, ID do cliente, o segredo de cliente e ponto de extremidade do token OAuth 2.0.

![Credenciais do Azure](./media/jenkins-azure-vm-agents/service-principal.png)

* Clique em **Verificar configurações** para certificar-se de que a configuração do perfil esteja correta.
* Salve a configuração e continue para a próxima etapa.

## <a name="template-configuration"></a>Configuração do modelo

### <a name="general-configuration"></a>Configuração geral
Em seguida, configure um modelo e use- o para definir um agente de VM do Azure. 

* Clique em **Adicionar** para adicionar um modelo. 
* Forneça um nome para o seu novo modelo. 
* Para o rótulo, insira "ubuntu." Esse rótulo é usado durante a configuração de trabalho.
* Selecione a região desejada na caixa de combinação.
* Selecione o tamanho da VM desejada.
* Especifique o nome da conta de armazenamento do Azure ou deixe em branco para usar o nome-padrão "jenkinsarmst."
* Especifique o tempo de retenção em minutos. Essa configuração define o número de minutos que Jenkins pode aguardar antes de excluir automaticamente um agente ocioso. Especifique 0 se não quiser que os agentes ociosos sejam excluídos automaticamente.

![Configuração geral](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Configuração de imagem

Para criar um agente do Linux (Ubuntu), selecione **Referência da imagem** e use a configuração a seguir como exemplo. Consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) para as últimas imagens suportadas no Azure.

* Publicador de imagem: Canonical
* Oferta de imagem: UbuntuServer
* Sku de imagem: 14.04.5-LTS
* Versão da imagem: mais recente
* Tipo de sistema operacional: Linux
* Inicie o método: SSH
* Forneça credenciais de administrador
* Para o script de inicialização de VM, digite:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Configuração de imagem](./media/jenkins-azure-vm-agents/image-config.png)

* Clique em **Verificar modelo** para verificar a configuração.
* Clique em **Salvar**.

## <a name="create-a-job-in-jenkins"></a>Crie um trabalho no Jenkins

* No painel do Jenkins, clique em **Novo Item**. 
* Insira um nome e selecione **Projeto Freestyle** e clique em **Ok**.
* Na guia **geral** , selecione "Restringir onde o projeto pode ser executado" e digite "ubuntu" na expressão de rótulo. Agora, você verá "ubuntu" na lista suspensa.
* Clique em **Salvar**.

![Configure o trabalho](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Compile o seu novo projeto

* Volte para o painel do Jenkins.
* Clique o botão direito do mouse no novo trabalho que você criou e clique em **Compilar agora**. Uma compilação é iniciada. 
* Quando a compilação estiver concluída, vá para **Saída do Console**. Você verá que a compilação foi executada remotamente no Azure.

![Saída do console](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Referência

* Vídeo de sexta-feira do Azure: [Integração contínua com Jenkins usando agentes de VM do Azure](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* Opções de configuração e as informações de suporte: [Wiki de plug-in do Jenkins de agente de VM do Azure](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 



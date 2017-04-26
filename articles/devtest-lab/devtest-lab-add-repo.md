---
title: "Adicionar um repositório Git a um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Adicionar um repositório Git do GitHub ou do Visual Studio Team Services à sua fonte de artefatos personalizados ou modelos do Azure Resource Manager no Azure DevTest Labs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 102a8a14-e17c-4b91-80fd-ce81640d04c9
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: fa07f37d81b0987d5b8fdd9bbd02669182bf96b0
ms.lasthandoff: 04/18/2017


---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates-for-use-in-azure-devtest-labs"></a>Adicionar um repositório Git para armazenar os artefatos personalizados e modelos do Azure Resource Manager para uso no Azure DevTest Labs

Se você quiser [criar artefatos personalizados](devtest-lab-artifact-author.md) para as VMs em seu laboratório ou [usar modelos do Azure Resource Manager para criar um ambiente de teste personalizado](devtest-lab-create-environment-from-arm.md), adicione também um repositório gito para incluir os artefatos ou modelos do Azure Resource Manager que sua equipe cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou no [VSTS (Visual Studio Team Services)](https://visualstudio.com).

Nós fornecemos um [repositório Github dos artefatos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que podem ser implantados ou personalizados para seus laboratórios. Quando você personaliza ou cria um artefato, você não armazena-os no repositório público, você deve criar seu próprio repositório particular. 

Quando você cria uma máquina virtual, você pode salvar o modelo do Azure Resource Manager, personalizá-lo, caso queira, e usá-lo posteriormente para facilmente criar mais VMs. Você deve criar seu próprio repositório particular para armazenar seus modelos personalizados do Azure Resource Manager.  

* Para saber como criar um repositório no GitHub, confira o [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para saber como criar um projeto do Team Services com um Repositório Git, veja [Conectar ao Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A captura de tela a seguir mostra um exemplo da aparência de um repositório contendo artefatos no GitHub:   
![Repositório de artefatos de exemplo no GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obter as credenciais e informações de repositório
Para adicionar um repositório de artefatos ao laboratório, você deve primeiro obter determinadas informações do seu repositório. As seções a seguir vão orientá-lo para obter essas informações para repositórios hospedados no GitHub e no Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório GitHub e token de acesso pessoal
Para obter a URL de clone do repositório GitHub e token de acesso pessoal, siga estas etapas:

1. Navegue até a página inicial do repositório GitHub que contém o artefato ou definições de modelo do Azure Resource Manager.
2. Selecione **Clonar ou baixar**.
3. Selecione o botão para copiar a **URL de clone HTTPS** para a área de transferência e salvar essa URL para uso posterior.
4. Selecione a imagem de perfil no canto superior direito do GitHub e, em seguida, **Configurações**.
5. No menu **Configurações pessoais** à esquerda, selecione **Tokens de acesso pessoal**.
6. Selecione **Gerar novo token**.
7. Na página **Novo token de acesso pessoal**, insira uma **Descrição do token**, aceite os itens padrão em **Selecionar escopos** e escolha **Gerar Token**.
8. Salve o token gerado, pois você precisará dele mais tarde.
9. Você pode fechar o GitHub agora.   
10. Continue para a seção [Conectar seu laboratório ao repositório](#connect-your-lab-to-the-repository).

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório do Visual Studio Team Services e token de acesso pessoal
Para obter a URL de clone do repositório do Visual Studio Team Services e token de acesso pessoal, siga estas etapas:

1. Abra a página inicial de sua coleção de equipe (por exemplo, `https://contoso-web-team.visualstudio.com`) e selecione o projeto.
2. Na home page do projeto, selecione **Código**.
3. Para exibir a URL de clone, na página **Código** do projeto, selecione **Clone**.
4. Salve a URL, você precisará dela mais tarde neste tutorial.
5. Para criar um Token de Acesso Pessoal, selecione **Meu perfil** no menu suspenso da conta de usuário.
6. Na página de informações de perfil, selecione **Segurança**.
7. Na guia **Segurança**, selecione **Adicionar**.
8. Na página **Criar um token de acesso pessoal** :

   * Insira uma **Descrição** para o token.
   * Selecione **180 dias** na lista **Expira em**.
   * Escolha **Todas as contas acessíveis** na lista **Contas**.
   * Escolha a opção **Todos os escopos** .
   * Escolha **Criar Token**.
9. Quando terminar, o novo token será exibido na lista de **Tokens de Acesso Pessoal** . Selecione **Copiar Token**e salve o valor do token para uso posterior.
10. Continue para a seção [Conectar seu laboratório ao repositório](#connect-your-lab-to-the-repository).

## <a name="connect-your-lab-to-the-repository"></a>Conecte seu laboratório ao repositório
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Na folha do laboratório, selecione **Configuração e Políticas**.
5. Na folha **Configuração e políticas** do laboratório, selecione **Repositórios**.
6. Na folha **Repositório**, selecione **+ Adicionar**.

    ![Adicionar um botão de repositório](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. Na segunda folha **Repositórios**, especifique o seguinte:

   * **Nome** – insira um nome para o repositório.
   * **URL de Clone de Git** – insira a URL HTTPS de clone de Git que você copiou anteriormente do GitHub ou do Visual Studio Team Services.
   * **Ramificação** – insira a ramificação para obter as suas definições.
   * **Token de Acesso Pessoal** – insira o token de acesso pessoal obtido anteriormente do GitHub ou do Visual Studio Team Services.
   * **Caminhos de pasta** – Insira, pelo menos, um caminho de pasta em relação a URL de clone que contém o artefato ou definições de modelo do Azure Resource Manager. Ao especificar um subdiretório, certifique-se de incluir a barra no caminho da pasta.

     ![Folha de repositório](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Depois que você criou seu repositório Git, você pode fazer um ou ambos estes procedimentos, dependendo das suas necessidades:
* Armazene seus [artefatos personalizados](devtest-lab-artifact-author.md), que você pode usar depois para criar novas VMs.
* [Crie ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md) e, então, armazene os modelos em seu repositório particular.

Quando você cria uma VM, você poderá verificar se os artefatos ou os modelos são adicionados ao seu repositório Git. Eles estarão disponíveis imediatamente na lista de artefatos ou modelos, com o nome do seu repositório particular mostrado na coluna que especifica a origem. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Como solucionar problemas de falha de Artefatos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Ingressar uma VM ao domínio de AD existente usando o modelo do ARM no Laboratório de Teste de Desenvolvimento do Azure](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)


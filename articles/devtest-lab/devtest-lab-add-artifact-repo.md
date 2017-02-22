---
title: "Adicionar um repositório de artefatos do Git a um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Adicionar um repositório Git do GitHub ou do Visual Studio Team Services à sua fonte de artefatos personalizados no Azure DevTest Labs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f7656382500682898cd3ed6372630afa3c3f6350
ms.openlocfilehash: 5c595bd2175f1cbc650bd1a757eeacc80cf39e23


---
# <a name="add-a-git-artifact-repository-to-a-lab-in-azure-devtest-labs"></a>Adicionar um repositório de artefatos do Git a um laboratório no Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-add-your-private-Artifacts-Repository-in-a-DevTest-Lab/player]
> 
> 

No Azure DevTest Labs, artefatos são *ações* – como instalar o software ou executar scripts e comandos – quando uma VM é criada. Por padrão, um laboratório inclui artefatos do repositório de artefatos de Laboratórios de Desenvolvimento/Teste oficiais do Azure. Você pode adicionar um repositório de artefatos do Git ao laboratório para incluir os artefatos que a sua equipe cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou no [VSTS (Visual Studio Team Services)](https://visualstudio.com).

* Para saber como criar um repositório no GitHub, confira o [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para saber como criar um projeto do Team Services com um Repositório Git, veja [Conectar ao Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A captura de tela a seguir mostra um exemplo da aparência de um repositório contendo artefatos no GitHub:   
![Repositório de artefatos de exemplo no GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obter as credenciais e informações de repositório
Para adicionar um repositório de artefatos ao laboratório, você deve primeiro obter determinadas informações do seu repositório. As seções a seguir vão orientá-lo para obter essas informações para repositórios de artefatos hospedados no GitHub e no Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório GitHub e token de acesso pessoal
Para obter a URL de clone do repositório GitHub e token de acesso pessoal, siga estas etapas:

1. Navegue até a home page do repositório GitHub que contém as definições de artefato.
2. Selecione **Clonar ou baixar**.
3. Selecione o botão para copiar a **URL de clone HTTPS** para a área de transferência e salvar essa URL para uso posterior.
4. Selecione a imagem de perfil no canto superior direito do GitHub e, em seguida, **Configurações**.
5. No menu **Configurações pessoais** à esquerda, selecione **Tokens de acesso pessoal**.
6. Selecione **Gerar novo token**.
7. Na página **Novo token de acesso pessoal**, insira uma **Descrição do token**, aceite os itens padrão em **Selecionar escopos** e escolha **Gerar Token**.
8. Salve o token gerado, pois você precisará dele mais tarde.
9. Você pode fechar o GitHub agora.   
10. Continue para a seção [Conectar seu laboratório ao repositório de artefatos](#connect-your-lab-to-the-artifact-repository) .

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório do Visual Studio Team Services e token de acesso pessoal
Para obter a URL de clone do repositório do Visual Studio Team Services e token de acesso pessoal, siga estas etapas:

1. Abra a home page de sua coleção de equipe (por exemplo, `https://contoso-web-team.visualstudio.com`) e selecione o projeto de artefato.
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
10. Continue para a seção [Conectar seu laboratório ao repositório de artefatos](#connect-your-lab-to-the-artifact-repository) .

## <a name="connect-your-lab-to-the-artifact-repository"></a>Conectar seu laboratório ao repositório de artefatos
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Na folha do laboratório, selecione **Configuração**.
5. Na folha **Configurações** do laboratório, selecione **Repositório de Artefatos**.
6. Na folha **Repositório de Artefatos**, selecione **+ Adicionar**.
   
    ![Adicionar um botão de repositório de artefatos](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
7. Na segunda folha **Repositórios de Artefatos** , especifique o seguinte:
   
   * **Nome** – insira um nome para o repositório.
   * **URL de Clone de Git** – insira a URL HTTPS de clone de Git que você copiou anteriormente do GitHub ou do Visual Studio Team Services. 
   * **Caminho da pasta** – insira o caminho da pasta em relação à URL de clone que contém as definições de artefato.
   * **Ramificação** – insira a ramificação para obter as suas definições de artefato.
   * **Token de Acesso Pessoal** – insira o token de acesso pessoal obtido anteriormente do GitHub ou do Visual Studio Team Services. 
     
     ![Folha de repositório de artefatos](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)
8. Selecione **Salvar**.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Como solucionar problemas de falha de Artefatos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Ingressar uma VM ao domínio de AD existente usando o modelo do ARM no Laboratório de Teste de Desenvolvimento do Azure](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)




<!--HONumber=Jan17_HO2-->



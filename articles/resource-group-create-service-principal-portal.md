<properties
   pageTitle="Criar uma nova entidade de serviço do Azure usando o portal do Azure"
   description="Descreve como criar uma nova entidade de serviço do Azure que possa ser usada com o controle de acesso baseado em função no Gerenciador de Recursos do Azure, para gerenciar o acesso aos recursos."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Criar uma nova entidade de serviço do Azure usando o portal do Azure

## Visão geral
Uma entidade de serviço é um processo automatizado, aplicativo ou serviço que precisa acessar outros recursos. Usando o Gerenciador de Recursos do Azure, você pode conceder acesso a uma entidade de serviço e autenticá-la para executar as ações de gerenciamento permitidas nos recursos que existem na assinatura ou como um locatário.

Este tópico mostra como criar uma nova entidade de serviço usando o Portal do Azure. Atualmente, você deve usar o portal do Microsoft Azure para criar uma nova entidade de serviço. Essa capacidade será adicionada ao portal de visualização do Azure em uma versão posterior.

## Conceitos
1. AAD (Active Directory do Azure) - um serviço de gerenciamento de identidades e acesso desenvolvido para a nuvem. Para mais detalhes, consulte: [O que é o Active Directory do Azure](./active-directory-whatis/)
2. Entidade de serviço - uma instância de um aplicativo em um diretório.
3. Aplicativo do AD - um registro de diretório no AAD que identifica um aplicativo ao AAD. Para mais detalhes, consulte [Noções básicas de autenticação no AD do Azure](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).


## Criar um aplicativo do Active Directory
1. Faça logon na sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/).

2. Selecione **Active Directory** no painel à esquerda.

   ![selecionar Active Directory][1]

3. Selecione o diretório que você deseja usar para criar o novo aplicativo.

   ![escolher o diretório][2]

3. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.

   ![exibir aplicativos][11]

4. Se você não criou um aplicativo nesse diretório antes de você verá algo semelhante à imagem a seguir. Clique em **ADICIONAR UM APLICATIVO**

   ![adicionar aplicativo][6]

   Ou então, clique em **Adicionar** no painel inferior.

   ![adicionar][12]

5. Selecione o tipo de aplicativo que você deseja criar. Para este tutorial, não usaremos um aplicativo da Galeria.

   ![novo aplicativo][10]

6. Preencha o nome do aplicativo e selecione o tipo de aplicativo que você deseja usar. Já que pretendemos usar a entidade de serviço deste aplicativo para autenticar com o Gerenciador de Recursos do Azure, podemos decidir criar um **APLICATIVO WEB E/OU API WEB** e clicar no botão Avançar.

   ![nomear aplicativo][9]

7. Preencha as propriedades de seu aplicativo. Para **URL DE LOGON**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada. Para **URI DE ID DO APLICATIVO**, forneça o URI que identifica seu aplicativo. A exclusividade ou existência do ponto de extremidade não está validada. Clique em **Concluir** para criar seu aplicativo AAD.

   ![propriedades do aplicativo][4]

## Criar sua senha de entidade de serviço
O portal agora deve estar com seu aplicativo selecionado.

1. Clique na guia **Configurar** para definir a senha do aplicativo.

   ![configurar o aplicativo][3]

2. Role para baixo até a seção **Chaves** e selecione por quanto tempo você gostaria que a senha fosse válida.

   ![chaves][7]

3. Selecione **Salvar** para criar a chave.

   ![salvar][13]

   A chave salva é exibida e você pode copiá-la.

   ![chave salva][8]

4. Você pode agora usar você chave para autenticar como uma entidade de serviço. Você precisará de sua **ID DO CLIENTE** além de sua **CHAVE** para entrar. Vá para **ID DO CLIENTE** e copie-a.
  
   ![id do cliente][5]


Seu aplicativo agora está pronto e a entidade de serviço criada em seu locatário. Ao fazer logon como uma entidade de serviço, certifique-se de usar:

* **ID DO CLIENTE** - como seu nome de usuário.
* **CHAVE** - como sua senha.

## Próximas etapas
Introdução

- [Visão Geral do Gerenciador de Recursos do Azure](./resource-group-overview.md)  
- [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](./powershell-azure-resource-manager.md)
- [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Usando o Portal do Azure para gerenciar os recursos do Azure](azure-portal/resource-group-portal.md)  
  
Criação e implantação de aplicativos
  
- [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md)  
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](azure-portal/resource-group-template-deploy.md)  
- [Solucionando problemas de implantações de grupos de recursos no Azure](virtual-machines/resource-group-deploy-debug.md)  
- [Funções de modelo do Gerenciador de Recursos do Azure](./resource-group-template-functions.md)  
- [Operações avançadas de modelo](./resource-group-advanced-template.md)  
- [Implantar recursos do Azure usando bibliotecas .NET e um modelo](virtual-machines/arm-template-deployment.md)
  
Organização de recursos
  
- [Usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md)  
  
Gerenciar e auditar o acesso
  
- [Gerenciar e auditar o acesso a recursos](azure-portal/resource-group-rbac.md)  
- [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](./resource-group-authenticate-service-principal.md)  

<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=July15_HO1-->
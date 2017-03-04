---
title: "Tutorial: Configurando o Workday para sincronização de entrada | Microsoft Docs"
description: "Saiba como usar o Inbound Synchronization com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8fe96f0a-f142-4d66-b53d-3ac3eb41a661
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 50f75a05cd7e22316be5434c0b37f0f0a2ee8509
ms.openlocfilehash: 75c7565806c9737a464d9fa3fc34e4d15eb6a16b
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Tutorial: Configurando o Workday para sincronização de entrada
> [!NOTE]
> O Azure Active Directory (AD) Premium está disponível para clientes na China usando a instância mundial do AD do Azure.    
> O Azure AD Premium não tem suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China.    
> 
> 

O objetivo deste tutorial é mostrar as etapas necessárias para executar no Workday e no AD do Microsoft Azure para importar as pessoas do Workday para o AD do Microsoft Azure.    
 O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:  

* Uma assinatura válida do Azure  
* Um locatário no Workday  

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:  

1. Habilitando a integração de aplicativos para o Workday  
2. Criação de um usuário do sistema de integração  
3. Criação de um grupo de segurança  
4. Atribuindo o usuário do sistema de integração ao grupo de segurança  
5. Configurando opções de grupo de segurança  
6. Ativando alterações de política de segurança  
7. Configurando a importação de usuário no AD do Microsoft Azure  

## <a name="enabling-the-application-integration-for-workday"></a>Habilitando a integração de aplicativos para o Workday
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Salesforce.    

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o Workday, execute as seguintes etapas:
1. No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.    
   
   ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.    
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.    
   
   ![Aplicativos](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Aplicativos")  
4. Para abrir a **Galeria de Aplicativos**, clique em **Adicionar um Aplicativo** e em **Adicionar um aplicativo a ser utilizado pela minha organização**.    
   
   ![O que você deseja fazer? ] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "O que você deseja fazer?")  
5. Na **caixa de pesquisa**, digite **Workday**.    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")  
6. No painel de resultados, selecione **Workday** e clique em **Concluir** para adicionar o aplicativo.    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")  

## <a name="creating-an-integration-system-user"></a>Criação de um usuário do sistema de integração
1. No **Workday Workbench**, digite **criar usuário** na caixa de pesquisa e clique no link **Criar Usuário do Sistema de Integração**.     
   
   ![criar Usuário](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "Criar Usuário")  
2. Conclua a tarefa de criar usuário do sistema de integração, fornecendo um nome de usuário e senha para um novo usuário do sistema de integração.  Deixe a opção Exigir nova senha no próximo logon desmarcada, porque esse usuário vai efetuar logon por meio de programação.    

 Deixe Minutos de tempo limite de sessão com seu valor padrão de 0, o que impedirá que as sessões do usuário expirarem prematuramente.    
   
   ![Criar Usuário do Sistema de Integração](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Criar Usuário do Sistema de Integração")  

## <a name="creating-a-security-group"></a>Criação de um grupo de segurança
Para o cenário descrito neste tutorial, você precisa criar um grupo de segurança do sistema de integração irrestrita e atribuí-lo a ele.    

1. Insira Criar grupo de segurança na caixa de pesquisa e, em seguida, clique no link Criar grupo de segurança.     
   
   ![Criar Grupo de Segurança](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "Criar Grupo de Segurança")  
2. Conclua a tarefa de criar o grupo de segurança.  Selecione Grupo de segurança de sistema de integração — sem restrições no menu suspenso Tipo de grupo de segurança com locatários, para criar um grupo de segurança ao qual os membros serão adicionados explicitamente.     
   
   ![Criar Grupo de Segurança](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "Criar Grupo de Segurança")  

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Atribuindo o usuário do sistema de integração ao grupo de segurança
1. Insira Editar grupo de segurança na caixa de pesquisa e clique no link **Editar Grupo de Segurança**.     
   
   ![Editar Grupo de Segurança](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Editar Grupo de Segurança")  
2. Procure e selecione o novo grupo de segurança de integração por nome.    
   
   ![Editar Grupo de Segurança](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Editar Grupo de Segurança")  
3. Adicione o novo usuário do sistema de integração ao novo grupo de segurança.       
   
   ![Grupo de Segurança do Sistema](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "Grupo de Segurança do Sistema")  

## <a name="configuring-security-group-options"></a>Configurando opções de grupo de segurança
Nesta etapa, você deve conceder permissões ao novo grupo de segurança para operações Get e Put nos objetos protegidos pelas seguintes políticas de segurança de domínio:  

* Provisionamento de conta externa  
* Dados de trabalho: Relatórios de trabalho público  
* Dados de trabalho: Todas as posições  
* Dados de trabalho: Informações atuais sobre a equipe  
* Dados de trabalho: Cargo de negócios no perfil de trabalhado  

&nbsp;  

1. Insira as políticas de segurança de domínio na caixa de pesquisa e, em seguida, clique no link Políticas de segurança de domínio para a área funcional.     
   
   ![Políticas de Segurança de Domínio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Políticas de Segurança de Domínio")  
2. Procure o sistema e selecione a área funcional do sistema.  Clique no botão rotulado como OK.     
   
   ![Políticas de Segurança de Domínio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Políticas de Segurança de Domínio")  
3. Na lista de políticas de segurança para a área funcional do sistema, expanda a administração de segurança e selecione a política de segurança de domínio, Provisionamento de conta externa.     
   
   ![Políticas de Segurança de Domínio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Políticas de Segurança de Domínio")  
4. Clique no botão Editar permissões e, em seguida, na tela Editar permissões, adicione um novo grupo de segurança à lista de grupos de segurança com permissões de integração Get e Put.     
   
   ![Editar Permissão](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Editar Permissão")  
5. Repita a etapa 1 acima para retornar à tela de seleção de áreas funcionais e, desta vez, pesquise pela equipe, selecione a área funcional do pessoal e clique no botão rotulado como OK.    
   
   ![Políticas de Segurança de Domínio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Políticas de Segurança de Domínio")  
6. Na lista de políticas de segurança para a área funcional de equipe, expanda Dados de trabalho: equipe e repita a etapa 4 acima para cada uma das políticas de segurança restantes:    
   
   * Dados de trabalho: Relatórios de trabalho público  
   * Dados de trabalho: Todas as posições  
   * Dados de trabalho: Informações atuais sobre a equipe  
   * Dados de trabalho: Cargo de negócios no perfil de trabalhado    
   
   ![Políticas de Segurança de Domínio](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Políticas de Segurança de Domínio")  

## <a name="activating-security-policy-changes"></a>Ativando alterações de política de segurança
1. Digite Ativar na caixa de pesquisa e, em seguida, clique no link Ativar alterações de política de segurança pendentes.    
   
   ![Ativar](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Ativar")  
2. Inicie a tarefa Ativar alterações de política de segurança pendentes inserindo um comentário para fins de auditoria e, em seguida, clicando no botão rotulado como OK.      
   
   ![Ativar Segurança Pendente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Ativar Segurança Pendente")  
3. Conclua a tarefa na próxima tela, marcando a caixa de seleção Confirmar e clicando no botão rotulado como OK.     
   
   ![Ativar Segurança Pendente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Ativar Segurança Pendente")  

## <a name="configuring-user-import-in-microsoft-azure-ad"></a>Configurando a importação de usuário no AD do Microsoft Azure
O objetivo desta seção é descrever como configurar o Microsoft Azure AD para importar pessoas do Workday.    

### <a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>Para configurar a importação de usuário no AD do Microsoft Azure, execute as seguintes etapas:
1. Na página de integração de aplicativos do **Workday**, clique em **Configurar importação de usuários** para abrir a caixa de diálogo **Configurar Provisionamento**.    
2. Na página **Configurações e credenciais do administrador** , realize as seguintes etapas e clique em Avançar:    
   
   ![Configurações e credenciais de administrador](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Configurações e credenciais de administrador")    
   
 * Na caixa de texto **Nome de usuário administrador do Workday** , digite o nome de usuário que você criou na seção [Criando um usuário do sistema de integração](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
 * Na caixa de texto **Senha do administrador do Workday** , digite a senha do usuário que você criou na seção [Criando um usuário do sistema de integração](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
 * Na caixa de texto **URL de locatário do Workday** , digite a URL ou o seu locatário do Workday.    
3. Na página **Testar conectividade**, clique em **Iniciar teste** para confirmar a conectividade e clique em **Avançar**.    
   
   ![Testar conexão](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Testar conexão")  
4. Na página **Opções de provisionamento**, clique em **Avançar**.    
   
   ![Opções de provisionamento](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Opções de provisionamento")  
5. Na caixa de diálogo **Iniciar provisionamento**, clique em **Concluir**.    
   
   ![Iniciar o provisionamento](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Iniciar o provisionamento")  

Agora, você pode ir para a seção **Usuários** e verificar se o usuário do Workday foi importado.    



<properties
	pageTitle="O que é o PowerApps Enterprise e como começar a usá-lo | Microsoft Azure"
	description="Introdução ao PowerApps Enterprise e como criar o ambiente do serviço de aplicativo"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# O que é o Microsoft PowerApps enterprise?

O Microsoft PowerApps Enterprise é um novo serviço do Microsoft Azure. O PowerApps Enterprise amplia a criação de aplicativos móveis para os usuários empresariais dentro de sua empresa e permite que os Administradores de TI gerenciem de perto esses aplicativos.

Usando uma interface no estilo Office com faixas de opções e fórmulas do Excel, os usuários empresariais podem criar aplicativos que:

- Mostram dados usando gráficos de linha, de pizza e de barras, assim como no Excel.
- Criam interfaces de usuário que incluem botões, inserção de texto e formatação de uma data.
- Adicionam controles de múltipla escolha, incluindo caixas de listagem, listas suspensas e botões de opção.
- Carregam imagens, tiram uma foto e reproduzem arquivos de áudio/vídeo.
- Usam sistemas "back-end", como Excel e SQL Server, para exibir e atualizar informações.
- Adicionam conectores pré-criados do Serviço de Aplicativo aos seus aplicativos que conectam a programas PaaS como o Twitter e o SharePoint.

Os Administradores de TI podem gerenciar aplicativos criados por usuários empresariais em suas empresas, incluindo:

- Gerenciar esses aplicativos e o acesso do usuário a esses aplicativos.
- Criar APIs e conexões com fontes de dados diferentes. 
- Gerenciar o acesso do usuário às APIs e as conexões a essas fontes de dados. 

## Como começar?

Em primeiro lugar, determine se você precisa criar um novo locatário do Active Directory do Azure (AD do Azure). Se você já tiver um locatário do AD, basta habilitar o PowerApps Enterprise no Portal do Azure, adicionar suas APIs e conexões e começar a gerenciar (comece na *Etapa 2* neste tópico).

Se você não tiver um locatário do AD, crie um novo locatário do AD, habilite o PowerApps Enterprise no Portal do Azure, adicione suas APIs e conexões e comece a gerenciar.

Este tópico lista as especificações.

## Etapa 1: criar um novo locatário do AD do Azure ou usar um existente

Para começar a usar o PowerApps Enterprise, você precisa de um locatário do Active Directory do Azure (AD do Azure). Um locatário é uma instância dedicada do serviço AD do Azure.

Quando sua organização ou empresa se inscreve para um serviço de nuvem do Microsoft Azure, como o Microsoft Intune ou o Office 365, ela recebe automaticamente um locatário próprio do AD. Cada locatário do AD é distinto e separado de outros locatários do AD do Azure.

Use as etapas a seguir para determinar se você já possui um locatário ou saber como criar um novo.

#### Tem uma assinatura existente do Office 365
Se você possui uma assinatura do Office 365 (ou do Microsoft Dynamic CRM Online, Enterprise Mobility Suite ou outros serviços da Microsoft), você tem uma assinatura gratuita do Active Directory do Azure. É possível usar o AD do Azure para criar e gerenciar contas de usuário e de grupo Se você não conseguir entrar no Portal do Azure, provavelmente você precisa ativar a assinatura. Para fazer isso, acesse o [portal clássico do Azure](https://manage.windowsazure.com/) e conclua um processo de registro único. Use estas [etapas](https://technet.microsoft.com/library/dn832618.aspx) para acessar seu locatário do AD do Azure.

#### Tenho uma assinatura existente do Azure associada a uma conta da Microsoft
Se você já se inscreveu em uma assinatura do Azure com sua conta individual da Microsoft (hotmail ou live), você já tem um locatário! No [portal clássico do Azure](https://manage.windowsazure.com/), **Locatário Padrão** está listado em **Todos os Itens** e em **Active Directory**. Você é livre para usar esse locatário como achar melhor - mas você talvez queira criar uma conta de administrador organizacional.

Para fazer isso, execute as seguintes etapas. Como alternativa, você poderá criar um novo locatário e criar um administrador nesse locatário seguindo um processo semelhante.

1.	Entre no [portal clássico do Azure](https://manage.windowsazure.com/) com sua conta individual.
2.	Na barra de menus esquerda, escolha **Active Directory**. 
3.	Escolha **Diretório Padrão** na lista de diretórios disponíveis.
4.	Escolha a guia **Usuários** na parte superior. Há um único usuário listado com "Conta da Microsoft" na coluna Originado de.
5.	Escolha **Adicionar Usuário** na parte inferior. 
6.	No formulário **Adicionar Usuário**, forneça os detalhes a seguir:  
	
	Propriedade | Descrição
--- | ---
Tipo de Usuário | Novo usuário na organização
Nome de usuário | Escolha um nome de usuário para esse administrador
Nome/Sobrenome/Nome de Exibição | Insira os valores
Função | Administrador global
Endereço de Email Alternativo | Insira seu valor
Opcional | Habilitar o Multi-Factor Authentication  
	
	Escolha o botão **CRIAR** para completar e exibir a senha temporária.

Quando terminar, registre essa senha temporária do novo usuário administrativo. Para alterar a senha temporária, entre no [https://login.microsoftonline.com](https://login.microsoftonline.com) usando essa nova conta de usuário e altere a senha. Você também pode enviar a senha diretamente para o usuário, usando um email alternativo.


#### Tenho uma assinatura existente do Azure associada a uma conta organizacional
Se você já se inscreveu anteriormente para uma assinatura do Azure com sua conta organizacional, você já tem um locatário. No [portal clássico do Azure](https://manage.windowsazure.com/), o locatário está listado em **Todos os Itens** em **Active Directory**. Você é livre para usar esse locatário como desejar. Você também pode criar um novo locatário usando o menu **Novo** na barra de tarefas na parte inferior.

#### Não tenho nenhuma delas e desejo começar do zero
Se nenhuma das opções se aplicar a você, visite [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para inscrever-se no Azure com uma nova organização. Depois de inscrito, você terá seu próprio locatário do AD do Azure com seu nome de domínio escolhido. No [portal clássico do Azure](https://manage.windowsazure.com/), você pode ver o locatário no **Active Directory** no menu à esquerda.

## Etapa 2: criar uma assinatura do Azure ou usar uma existente
Agora que você tem seu locatário do AD, pode criar uma assinatura nova do Azure ou usar uma existente. A assinatura do AD do Azure inclui várias edições. Para o PowerApps Enterprise, você pode usar a edição Gratuita. No entanto, se você precisar usar Proxy AAD para criar conectividade híbrida com dados locais, aconselhamos a edição Básica ou Premium.

[Edições do Active Directory do Azure](../active-directory/active-directory-editions.md) lista mais recursos.


## Etapa 3: inscrever-se para o PowerApps Enterprise em sua assinatura corporativa do Azure
> [AZURE.NOTE]As etapas a seguir exigem que o Administrador de assinatura entre no Portal do Azure e envie uma solicitação.

Agora que você tem seu locatário do AD e uma assinatura do Azure, os administradores de assinatura corporativos podem se inscrever para o PowerApps enterprise. O Administrador também pode adicionar usuários da empresa para "administrar" o PowerApps, incluindo a atribuição de permissões de usuários, e para gerenciar o PowerApps publicado em sua assinatura do Azure.

Sem inscrever-se no PowerApps enterprise, você verá uma folha "sem acesso" ao acessar o [Portal do Azure](https://portal.azure.com/) e procurar os PowerApps. Para inscrever-se em sua empresa, o **administrador da assinatura** pode acessar [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) e entrar em contato conosco para saber mais sobre preços e o processo de inscrição.

![][4]

Depois de concluir o processo de inscrição e estar pronto para usar o PowerApps Enterprise, você pode:

- Adicione usuários em sua empresa e, usando o [Controle de acesso baseado em função](../role-based-access-control-configure.md), forneça a esses usuários funções de Administrador do PowerApps para acessar o portal do PowerApps Enterprise.
- Crie um ambiente de serviço de aplicativo dedicado para hospedar seu PowerApps.
- Crie APIs e conexões para execução em seu ambiente de serviço de aplicativo dedicado.
- Além dos aplicativos criados no PowerApps, você pode adicionar outros aplicativos ao seu ambiente de serviço de aplicativo, incluindo aplicativos Web, aplicativos móveis, aplicativos de API e aplicativos lógicos.

No exemplo a seguir, a empresa Contoso inscreveu-se para o PowerApps. Nessa nova folha **PowerApps**, você pode ver um resumo dos tipos diferentes de aplicativos criados usando esse ambiente de serviço de aplicativo. Em **Gerenciar APIs**, você pode ver um resumo das APIs criadas pela Microsoft (gerenciadas pela Microsoft) e ver as APIs criadas pela Contoso (gerenciadas pelo departamento de TI):

![Exemplo de folha do PowerApps da empresa][3]


## Etapa 4: criar um ambiente do serviço de aplicativo
Crie um ambiente do serviço de aplicativo para hospedar suas APIs e conexões do PowerApps, bem como aplicativos móveis, aplicativos Web, aplicativos de API e aplicativos lógicos.

Um ambiente do serviço de aplicativo é um ambiente isolado e dedicado que executa com segurança todos os seus aplicativos. Os recursos de computação são para um ambiente de serviço de aplicativo e são dedicados exclusivamente a executar apenas seus aplicativos. Quando você se inscreve no PowerApps Enterprise, um ambiente de serviço de aplicativo dedicado é usado para hospedar as APIs e as conexões usadas por seus aplicativos. Esse ambiente de serviço de aplicativo é um tipo "especial" de ambiente de serviço de aplicativo. Especificamente:

- Você pode usar esse ambiente de serviço de aplicativo para fazer o que quiser. Ele está vinculado à sua empresa, não à assinatura.
- Configure as APIs e as conexões que serão usadas por seus aplicativos criados no PowerApps. Porém, você também pode adicionar aplicativos Web, aplicativos móveis, aplicativos lógicos e aplicativos de API para esse mesmo ambiente de serviço de aplicativo. 
- A cobrança é fixa e está incluída no PowerApps Enterprise.  
- A escala é gerenciada automaticamente para você. Você não precisa monitorar o ambiente para determinar se os recursos de computação adicionais são necessárias.

O ambiente de serviço de aplicativo regular do Azure possui recursos diferentes. Confira [Introdução ao ambiente de Serviço de Aplicativo](../app-service-app-service-environment-intro.md) para ver esse detalhes.

#### Requisitos para começar

- Assinatura da empresa do Azure
- O Administrador de Assinatura em sua empresa [inscreveu sua empresa para o PowerApps](powerapps-get-started-azure-portal.md) Enterprise.
- Você está conectado no Portal do Azure como Administrador do PowerApps ("proprietário" do PowerApps) ou Administrador de Assinatura.

### Criar um ambiente de serviço de aplicativo
> [AZURE.NOTE]Se você não vir a opção para criar o ambiente de serviço de aplicativo, ele já terá criado para seu locatário. Para exibir os detalhes, selecione **Configurações** para abrir o ambiente de serviço de aplicativo.

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta corporativa. Por exemplo, entre com *seuNomedeUsuário*@*SuaEmpresa*.com. Quando você fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Escolha **Procurar** na barra de tarefas: ![Procurar PowerApps][1]
  
3. Na lista, você poderá rolar a tela para localizar PowerApps ou digitar *powerapps*: ![Pesquisar PowerApps][2]

4. Na folha **PowerApps**, escolha **Criar o Ambiente do Serviço de Aplicativo para começar** ou selecione **Ambiente do Serviço de Aplicativo** em *Configurações*: ![][5]

	> [AZURE.NOTE]Se você clicar em **Criar o Ambiente do Serviço de Aplicativo para começar**, você verá uma folha extra com detalhes sobre o Ambiente do Serviço de Aplicativo. Basta clicar em Criar link nessa folha para iniciar a folha de criação.

5. Em seguida, insira o nome, selecione a assinatura que você deseja usar, selecione ou crie um novo grupo de recursos e selecione uma rede virtual. **Perceba** que depois de escolher uma rede virtual, não será possível alterar: ![][6] Para saber mais sobre como as redes virtuais funcionam com um ambiente de serviço de aplicativo, consulte [Como criar um Ambiente do Serviço de Aplicativo](../app-service-web-how-to-create-an-app-service-environment.md).

6. Selecione **Adicionar** para concluir a criação do ambiente do serviço de aplicativo.

> [AZURE.TIP]Ao criar o ambiente do serviço de aplicativo usando PowerApps, você não receberá solicitações de configuração de pools de recursos de computação. Essa etapa será processada automaticamente.

Lembre-se de que você também pode adicionar aplicativos Web, aplicativos móveis e aplicativos de API para esse mesmo ambiente de serviço de aplicativo. Na verdade, o ambiente é seu para adicionar qualquer coisa que receba suporte do Ambiente do Serviço de Aplicativo.

### Adicionar administradores para gerenciar o Ambiente de Serviço de Aplicativo

Para obter acesso ao ambiente de serviço de aplicativo, criar APIs, conexões e outros recursos, os usuários devem ser adicionados com a função Proprietário.

1. Selecione o ambiente de serviço de aplicativo que você acabou de criar.
2. No Essentials, selecione a propriedade **Grupo de recursos**. Isso abre o grupo de recursos que contém o ambiente do serviço de aplicativo: ![][7]
3. Escolha o ícone RBAC para gerenciar as permissões: ![][8]Adicionar usuários e atribuir funções é o mesmo que usar o [ Controle de acesso baseado em função](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure) do Azure.

> [AZURE.NOTE]No momento, você não pode conceder permissões de RBAC para o ambiente de serviço de aplicativo. Você pode conceder permissões de RBAC no nível do grupo de recursos pai.

## Resumo e próximas etapas
Agora sua empresa está inscrita no PowerApps e tem um ambiente de serviço de aplicativo. Em seguida, você poderá adicionar APIs e conexões que poderão ser usadas por seus aplicativos.

- [Monitorar seus aplicativos do PowerApps](powerapps-manage-monitor-usage.md)
- [Desenvolver uma API para o PowerApps](powerapps-develop-api.md)
- [Adicionar uma nova API, adicionar uma conexão e fornecer acesso aos usuários](powerapps-manage-api-connection-user-access.md)
- [Atualizar uma API existente e suas propriedades](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!---HONumber=AcomDC_1203_2015-->
<properties
	pageTitle="Verificar a utilização de seu aplicativo no PowerApps Enterprise| Microsoft Azure"
	description="Veja todos os aplicativos, APIs, usuários, solicitações de aplicativo e permissões de atualização no portal do Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# Gerenciar e proteger seus PowerApps
Você cria seu ambiente do serviço de aplicativo e adiciona APIs e suas conexões. Agora, os usuários em sua organização podem começar a consumir essas APIs e conexões. Você também pode gerenciar todos os aplicativos criados em sua organização. Estas opções incluem:

- Conferir os diferentes aplicativos em seu ambiente do serviço de aplicativo, incluindo PowerApps, aplicativos Web, aplicativos lógicos, aplicativos móveis e muito mais.
- Conferir todas as APIs usadas por aplicativos específicos.
- Exibir e gerenciar o acesso do usuário aos aplicativos no ambiente do serviço de aplicativo. 
- Exibir e gerenciar o acesso do usuário às APIs e suas conexões. 

Lembre-se, o ambiente do serviço de aplicativo é seu e, por isso, você pode adicionar outros aplicativos, incluindo aplicativos Web e aplicativos lógicos. Você pode abrir o PowerApps Enterprise para ver e gerenciar esses aplicativos.


## Adicionar administradores do PowerApps
Quando o PowerApps Enterprise estiver habilitado e pronto para uso, você poderá adicionar administradores e monitorar outros aplicativos em seu ambiente do serviço de aplicativo.

1. No [portal do Azure](https://portal.azure.com/), abra **PowerApps**.
2. Escolha a opção **Configurações**.
3. Em **configurações**, escolha **Admin**: ![][1]  
4. Em **Usuários**, escolha **Adicionar**.
5. Escolha a função **Proprietário**: ![][2]  

	> [AZURE.IMPORTANT]Lembre-se de escolher a função **Proprietário** se você estiver definindo alguém como Administrador do PowerApps As outras funções listadas não darão aos usuários acesso total para gerenciar os PowerApps.

6. Escolha seus usuários ou grupos.
7. Escolha **OK** para concluir as etapas.

Quando você adiciona Administradores ao PowerApps Enterprise, os usuários e grupos adicionados como administradores podem:

- Adicionar outros usuários como administradores do PowerApps.
- Gerenciar todos os aplicativos e o acesso do usuário.
- Não podem alterar a cobrança.

> [AZURE.IMPORTANT]Os Administradores do PowerApps não podem fazer alterações no Ambiente do Serviço de Aplicativo até que recebam a função de Proprietário no grupo de recursos do ambiente do serviço de aplicativo. Para fazer isso, confira [este artigo](powerapps-get-started-azure-portal.md).

Depois de receberem a função de Proprietário no grupo de recursos do ambiente do serviço de aplicativo, os administradores do PowerApps também poderão:

- Criar e configurar APIs e suas conexões.
- Alterar as configurações do PowerApps, incluindo o ambiente do serviço de aplicativo.
- Adicionar outros usuários e grupos e dar a eles funções e permissões para as APIs, suas conexões e para o ambiente do serviço de aplicativo. 


## Gerenciar seu PowerApps e outros tipos de aplicativos
Depois de habilitar o PowerApps e seu ambiente do serviço de aplicativo, você poderá adicionar outros aplicativos, por exemplo, aplicativos Web e aplicativos lógicos ao mesmo ambiente do serviço de aplicativo. Ao fazer isso, os aplicativos ficarão listados em *Todos os aplicativos*, juntamente com os aplicativos criados no PowerApps. Você pode clicar em cada tipo de aplicativo para navegar por eles.

### Exibir e gerenciar seu PowerApps

1. No [portal do Azure](https://portal.azure.com/), abra **PowerApps**.
2. Do bloco **Todos os aplicativos**, escolha **PowerApps**: ![][3]  
3. Escolha um aplicativo para exibir os detalhes dele, incluindo:  
	- As APIs que ele utiliza
	- Os usuários e grupos que têm acesso ao aplicativo 
	- As análises do aplicativo (em breve)

#### Adicionar um aplicativo

Você não pode adicionar um aplicativo no portal do Azure. Para isso, acesse o [portal do PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715583).

#### Excluir os aplicativos criados no PowerApps
Como Administrador do PowerApps, você pode excluir qualquer aplicativo, incluindo aplicativos criados no PowerApps e outros tipos de aplicativos em seu ambiente do serviço de aplicativo. Para excluir seu aplicativo, escolha o bloco **Todos os aplicativos**, selecione seu aplicativo e escolha **Excluir**: ![][4]


#### Conceder aos usuários ou grupos acesso para uso de um aplicativo
Como Administrador do PowerApps, você pode adicionar ou remover usuários e grupos no PowerApps.

1. No [portal do Azure](https://portal.azure.com/), abra **PowerApps**.
2. No bloco **Todos os aplicativos**, escolha **PowerApps**: ![][3]  
3. Escolha um aplicativo, por exemplo, **Central de Serviços**. 
4. Em **Configurações**, escolha **Acesso de usuário ao aplicativo**: ![][5]  
5. Escolha **Adicionar** para adicionar um novo usuário ou grupo. 
6. Escolha uma função:  
	- Pode Editar
	- Pode Exibir
7. Escolha os usuários ou os grupos.
8. Escolha **OK** para concluir as etapas.

### Exibir e gerenciar seus aplicativos lógicos

1. No [portal do Azure](https://portal.azure.com/), abra **PowerApps**.
2. No bloco **Todos os aplicativos**, escolha **Aplicativos lógicos**: ![][8]  
3. Escolha um aplicativo lógico para exibir os detalhes dele. Escolha a assinatura correta para que o PowerApps liste os aplicativos lógicos correto: ![][7]  

	> [AZURE.IMPORTANT]Talvez você perceba alguma inconsistência na contagem de aplicativos lógicos entre a folha de navegação da visualização pública e a contagem exibida na folha principal do PowerApps. Isso é esperado. O portal está exibindo todos os aplicativos lógicos de todos os planos de hospedagem, e não a filtragem de aplicativos lógicos no ambiente do serviço de aplicativo implantado para o PowerApps. Esse comportamento será corrigido em atualizações futuras.

	**Para saber mais sobre os aplicativos lógicos e como gerenciá-los, confira [estas instruções](https://azure.microsoft.com/documentation/services/app-service/logic/).**

### Exibir e gerenciar seu aplicativos Web

1. No [portal do Azure](https://portal.azure.com/), abra **PowerApps**.
2. No bloco **Todos os aplicativos**, escolha **Aplicativos Web**: ![][9]  

	**Para saber mais sobre aplicativos Web e como gerenciá-los, confira [estas instruções](https://azure.microsoft.com/documentation/services/app-service/web/).**

### Exibir e gerenciar seus Aplicativos Móveis

1. No [portal do Azure](https://portal.azure.com/), abra **PowerApps**.
2. No bloco **Todos os aplicativos**, escolha **Aplicativos móveis**: ![][10]  

	**Para saber mais sobre aplicativos móveis e como gerenciá-los, confira [estas instruções](https://azure.microsoft.com/documentation/services/app-service/mobile/).**


## Revisar as opções de segurança
Vários métodos de segurança são usados, dependendo do que você está fazendo. Você precisa saber do seguinte:

- **Administrador de assinatura**: esses administradores controlam a cobrança e são responsáveis por inscrever sua empresa no PowerApps Enterprise. Somente os Administradores de assinatura podem solicitar a habilitação do PowerApps na assinatura do Azure de sua empresa. 

- **Acesso de usuário em tempo de execução**: há três tipos diferentes de acesso de usuário em tempo de execução:
	- **Acesso de usuário ao aplicativo**: essa permissão controla se o usuário do aplicativo *Pode editar* o aplicativo ou *Pode exibir* o aplicativo.
	- **Acesso de usuário à API**: estas permissões controlam o acesso de tempo de execução. Se os usuários tiverem essa permissão, poderão usar a API em seus aplicativos. Os usuários têm ou não têm permissão para usar a API em tempo de execução. 
	- **Acesso de usuário à conexão**: *Pode exibir* e *Pode editar* são as permissões de usuário em tempo de execução disponíveis para uma conexão. Ao adicionar uma API (ou perfil de conexão) e cria a conexão, você concede aos usuários e grupos essas permissões específicas: ![][6]  

		Por exemplo, você pode conceder ao grupo de Vendas da empresa a permissão *Pode editar* para uma conexão de uma API de conector do SQL. O usuário com a permissão *Pode editar* será capaz de usar a conexão em seus aplicativos, bem como editar a configuração da conexão. O usuário com a permissão *Pode exibir* será capaz de usar a conexão em seus aplicativos, mas não poderá modificar a configuração da conexão, como a cadeia de conexão.

- **Controle de acesso baseado em função** (RBAC): muitas ofertas do Azure usam o controle de acesso baseado em função para determinar quem pode fazer o que. No PowerApps, o RBAC é usado em dois lugares:
	- Ao entrar no portal do PowerApps pela primeira vez, você pode adicionar e gerenciar usuários que devem ser os administradores do PowerApps. 
	- Ao criar o ambiente do serviço de aplicativo, você adiciona usuários ou grupos ao PowerApps, e pode remover usuários ou grupos do PowerApps. Por exemplo, você pode conceder a grupos específicos de administradores dentro da empresa a função de *Proprietários*, o que permitirá a eles criar conexões e APIs. Essas APIs e as conexões são adicionadas aos aplicativos criados no PowerApps.
	- Ao adicionar usuários à aplicativos como aplicativos Web, aplicativos lógicos ou aplicativos móveis. Você pode escolher a função para esses usuários.  
		
		Adicionar usuários e atribuir funções é como usar o [Controle de acesso baseado em função](../role-based-access-control-configure.md) do Azure. As funções incluem:

		Função | Descrição
		--- | ---
		Colaborador | Gerencia tudo, exceto a concessão de acesso aos usuários.
		Leitor | Pode ver tudo, mas não pode fazer alterações
		Proprietário | Pode gerenciar tudo e conceder acesso a usuários.

Com essas funções, você pode conceder ao usuárioA a permissão **Pode Exibir** para um aplicativo diário do Twitter, e ao usuárioB a permissão **Pode Editar** para o aplicativo ShuttleBus. Você pode conceder acesso ao usuárioB para todas as APIs. Você pode detalhar bastante esses direitos ou adicionar todos a uma função específica. Isso realmente depende de suas necessidades comerciais.


## Resumo e próximas etapas
Neste tópico, você leu sobre as diferentes opções de gerenciamento de seu PowerApps e os métodos de segurança implementados no PowerApps.

Agora que sua experiência no portal do Azure está configurada, vamos começar a criar seus aplicativos. Estes são bons pontos de partida:

- [Criar um aplicativo a partir de um modelo no PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715536) 
- [Criar um aplicativo a partir de dados no PowerApps](http://go.microsoft.com/fwlink/?LinkId=715539) 
- [Criar um aplicativo do zero no PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=715538)


[1]: ./media/powerapps-manage-monitor-usage/addadmin.png
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png
[9]: ./media/powerapps-manage-monitor-usage/webapps.png
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png

<!---HONumber=AcomDC_1203_2015-->
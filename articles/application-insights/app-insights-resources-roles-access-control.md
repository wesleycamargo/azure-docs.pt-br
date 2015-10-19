<properties 
	pageTitle="Recursos, funções e controle de acesso no Application Insights" 
	description="Proprietários, colaboradores e leitores de percepções de sua organização." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="awills"/>
 
# Recursos, funções e controle de acesso no Application Insights

Você pode controlar quem tem acesso de leitura e atualização a seus dados no [Application Insights][start] do Visual Studio usando o [Controle de acesso baseado em função no Microsoft Azure](../role-based-access-control-configure.md).

> [AZURE.IMPORTANT]Atribua acesso aos usuários no **grupo de recursos ou assinatura** ao qual o recurso do aplicativo pertence, não no próprio recurso. Atribua a função **Colaborador de componente do Application Insights**. Isso garante o controle uniforme de acesso a testes na Web e alertas, juntamente com o recurso do aplicativo. [Saiba mais](#access).


## Recursos, grupos e assinaturas

Primeiro, algumas definições:

* **Recurso** ‒ uma instância de um serviço do Microsoft Azure. O recurso do Application Insights coleta, analisa e exibe os dados de telemetria enviados de seu aplicativo. Outros tipos de recursos do Azure incluem aplicativos Web, bancos de dados e VMs. 

    Para ver todos os seus recursos, vá para o [Portal do Azure][portal], entre e clique em Procurar.

    ![Escolha Procurar e, depois, Tudo ou Filtrar pelo Application Insights](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos**][group] ‒ cada recurso pertence a um grupo. Um grupo é uma maneira conveniente de gerenciar recursos relacionados, particularmente para controle de acesso. Por exemplo, em um grupo de recursos, você pode colocar um aplicativo Web, um recurso do Application Insights para monitorar o aplicativo e um Recurso de armazenamento para manter os dados exportados.

    
    ![Escolha Procurar e Grupos de recursos e, em seguida, escolha um grupo](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Assinatura**](https://manage.windowsazure.com) ‒ para usar o Application Insights ou outros recursos do Azure, você entra em uma assinatura do Azure. Cada grupo de recursos pertence a uma assinatura do Azure, em que você escolhe o pacote de preços e, se for uma assinatura de organização, escolhe os membros e suas permissões de acesso.
* [**Conta da Microsoft**][account] ‒ o nome de usuário e senha que você usa para entrar em assinaturas do Microsoft Azure, XBox Live, Outlook.com e outros serviços da Microsoft.


## <a name="access"></a> Controlar o acesso no grupo de recursos

É importante entender que, além do recurso criado para seu aplicativo, também há recursos ocultos separados para alertas e testes na Web. Eles estão conectados ao mesmo [grupo de recursos](#resource-group) do seu aplicativo. Você também pode colocar outros serviços do Azure nele, como sites ou armazenamento.

![Recursos no Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Portanto, para controlar o acesso a esses recursos, é recomendável:

* Controlar o acesso no nível de **grupo de recursos ou assinatura**.
* Atribuir a função **Colaborador de componente do Application Insights** aos usuários. Isso permite editar testes na Web, alertas e recursos do Application Insights, sem fornecer acesso a qualquer outro serviço no grupo. 

## Para fornecer acesso a outro usuário

Você deve ter direitos de Proprietário para a assinatura ou o grupo de recursos.

O usuário deve ter uma [Conta da Microsoft][account]. Você pode fornecer acesso a indivíduos e também a grupos de usuários definidos no Azure Active Directory.

#### Navegue até o grupo de recursos

Adicione o usuário a ele.

![Na folha de recursos de seu aplicativo, abra Essentials, abra o grupo de recursos e selecione Configurações/Usuários. Clique em Adicionar.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Ou então, você poderia ir para um nível acima e adicionar o usuário à Assinatura.

#### Selecione uma função

![Selecione uma função para o novo usuário](./media/app-insights-resources-roles-access-control/03-role.png)

Função | No grupo de recursos
---|---
Proprietário | Pode alterar qualquer item, incluindo o acesso do usuário
Colaborador | Pode editar qualquer item, incluindo todos os recursos
Colaborador de componente do Application Insights | Pode editar recursos do Application Insights, testes na Web e alertas
Leitor | Pode exibir, mas não pode alterar nada

A 'edição' inclui a criação, a exclusão e a atualização:

* Recursos
* Testes da Web
* Alertas
* Exportação contínua

#### Selecione o usuário


![Digite o endereço de email de um novo usuário. Selecione o usuário](./media/app-insights-resources-roles-access-control/04-user.png)

Se o usuário desejado não estiver no diretório, você poderá convidar qualquer pessoa com uma conta da Microsoft. (Se ela usa serviços como Outlook.com, OneDrive, Windows Phone ou XBox Live, já tem uma conta da Microsoft.)



## Usuários e funções

* [Controle de acesso baseado em função no Azure](../role-based-access-control-configure.md)



<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-preview-portal-using-resource-groups.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=Oct15_HO2-->
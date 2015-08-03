<properties 
	pageTitle="Gerenciar um espaço de trabalho de Aprendizado de Máquina | Microsoft Azure" 
	description="Gerencie o acesso aos espaços de trabalho de aprendizado de máquina do Azure e implante e gerencie serviços Web da API ML" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Gerenciar um espaço de trabalho de Aprendizado de Máquina do Azure 
Usando o portal de gerenciamento do Azure, você pode gerenciar seus espaços de trabalho do Aprendizado de Máquina para:

- Monitorar como o espaço de trabalho está sendo usado
- Configurar o espaço de trabalho para permitir ou negar acesso
- Gerenciar serviços Web criados no espaço de trabalho
- Excluir o espaço de trabalho

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Além disso, a guia do painel fornece uma visão geral do uso do espaço de trabalho e uma breve explicação das informações do espaço de trabalho.

> [AZURE.TIP]No Estúdio de Aprendizado de Máquina do Azure, na guia **SERVIÇOS WEB**, você pode adicionar, atualizar ou excluir um serviço Web do Aprendizado de Máquina.

Para gerenciar um espaço de trabalho:

1.	Entre em sua conta do Microsoft Azure. Use a conta que está associada à assinatura do Azure.
2.	No [portal de gerenciamento do Azure](https://manage.windowsazure.com/), no painel de serviços do Microsoft Azure, clique em **APRENDIZADO DE MÁQUINA**.
3.	Clique no espaço de trabalho que você deseja gerenciar.

A página do espaço de trabalho tem três guias:

- **PAINEL** - permite que você exiba o uso e as informações do espaço de trabalho
- **CONFIGURAR** - permite que você gerencie o acesso ao espaço de trabalho
- **SERVIÇOS WEB** - permite que você gerencie serviços Web que foram publicados deste espaço de trabalho

  
## Para monitorar como o espaço de trabalho está sendo usado

Clique na guia **PAINEL**.

No painel, você pode exibir o uso geral do espaço de trabalho e ter uma visão rápida das informações do espaço de trabalho.

- O gráfico **COMPUTAR** mostra os recursos de computação que estão sendo usados pelo espaço de trabalho. Você pode alterar o modo de exibição para exibir valores relativos ou absolutos e pode alterar o período de tempo exibido no gráfico.
- **Visão geral de uso** exibe o armazenamento do Azure que está sendo usado pelo espaço de trabalho.
- **Visão rápida** fornece um resumo das informações do espaço de trabalho e links úteis.

> [AZURE.NOTE]O link **Entrar no Estúdio AM** abre o Estúdio de Aprendizado de Máquina usando a conta da Microsoft com a qual você está conectado no momento. A Conta da Microsoft que você usou para entrar no portal do Azure para criar um espaço de trabalho não tem automaticamente permissão para abrir esse espaço de trabalho. Para abrir um espaço de trabalho, você deve estar conectado à Conta da Microsoft que foi definido como proprietária do espaço de trabalho ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho.


## Para conceder ou suspender acesso para usuários ##

Clique na guia **CONFIGURAR**.

Na guia Configuração, você pode
 
- Suspender o acesso ao espaço de trabalho do Aprendizado de Máquina clicando em NEGAR. Os usuários não poderão mais abrir o espaço de trabalho no Estúdio de Aprendizado de Máquina. Para restaurar o acesso, clique em PERMITIR.
- Altere o proprietário do espaço de trabalho especificando uma Conta da Microsoft diferente. 

Para gerenciar quem tem acesso ao espaço de trabalho no Estúdio de Aprendizado de Máquina, clique em **Entrar no Estúdio AM** na guia **PAINEL** (consulte a observação acima sobre **Entrar no Estúdio AM**). Isso abre o espaço de trabalho no Estúdio de Aprendizado de Máquina. Daqui, clique na guia **CONFIGURAÇÕES** e, em seguida, em **USUÁRIOS**. Você pode clicar em **CONVIDAR MAIS USUÁRIOS** para fornecer aos usuários acesso ao espaço de trabalho, ou selecionar um usuário e clicar em **REMOVER**.


## Para gerenciar os serviços Web neste espaço de trabalho

Clique na guia **SERVIÇOS WEB**.

Isso exibe uma lista de serviços Web publicados desse espaço de trabalho. Para gerenciar um serviço Web, clique no nome na lista para abrir a página do serviço Web.

Um serviço Web pode ter um ou mais pontos de extremidade definidos.

- Você pode definir pontos de extremidade adicionais além do ponto de extremidade "Padrão". Para adicionar o ponto de extremidade, clique em **ADICIONAR PONTO DE EXTREMIDADE** na parte inferior da página.

- Para excluir um ponto de extremidade (você não pode excluir o ponto de extremidade "Padrão"), clique em qualquer lugar na linha de ponto de extremidade, exceto no nome, e clique em **EXCLUIR PONTO DE EXTREMIDADE** na parte inferior da página. Isso remove o ponto de extremidade do serviço Web.
 
    > [AZURE.NOTE]Se um aplicativo estiver usando o ponto de extremidade do serviço Web quando o ponto de extremidade for excluído, o aplicativo receberá um erro na próxima vez que tentar acessar o serviço.

Clique no nome de um ponto de extremidade de serviço Web para abri-lo. O gráfico de uso mostra os recursos de computação e de previsão que estão sendo usados pelo ponto de extremidade de serviço Web. Você pode alterar o modo de exibição para exibir valores relativos ou absolutos e pode alterar o período de tempo exibido no gráfico.

Esta página fornece as informações de que você precisa para acessar o ponto de extremidade usando a API REST do serviço Web. Para obter mais informações, consulte [Como utilizar um serviço Web do Aprendizado de Máquina do Azure][consume].

Você também pode publicar o serviço Web para o mercado de dados do Azure nessa página. Para obter mais informações, consulte [Publicar o Serviço Web do Aprendizado de Máquina do Azure no Azure Marketplace][marketplace].

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

 

<!---HONumber=July15_HO4-->
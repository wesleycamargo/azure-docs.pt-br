---
title: "Implantar e explorar um aplicativo SaaS multilocatário que usa o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Implantar e explore o aplicativo SaaS do Wingtip multilicatário, que demonstra os padrões SaaS usando o Banco de dados SQL do Azure."
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: 1ef4355f7234bc6a534d21a57fa52b480983b99b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Implantar e explorar um aplicativo SaaS multilocatário que usa o Banco de dados SQL do Azure

Neste tutorial, você pode implantar e explorar um aplicativo de banco de dados multilocatário de SaaS de exemplo chamado Wingtip tíquetes. O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

Essa implementação de Wingtips usa um padrão de banco de dados multilocatário fragmentado. A fragmentação é pelo identificador do locatário. Dados de locatário são distribuídos para determinado banco de dados de acordo com os valores de identificador de locatário. Independentemente de quantos locatários contém qualquer determinado banco de dados, todos os bancos de dados são multilocatários no sentido de que os esquemas de tabela incluem um identificador de locatário. 

Esse padrão de banco de dados permite que você armazene um ou mais locatários em cada fragmento ou banco de dados. Você pode otimizar o custo mais baixo tendo cada banco de dados a ser compartilhado por vários locatários. Ou você pode otimizar para isolamento, fazer com que cada banco de dados armazenar somente um locatário. Sua escolha de otimização pode ser feita independentemente para cada locatário específico. A opção pode ser feita quando o locatário é armazenado primeiro, ou você pode mudar de ideia mais tarde. O aplicativo é projetado para funcionar bem em qualquer modo.

#### <a name="app-deploys-quickly"></a>Aplicativo implantado rapidamente

A seção de implantação a seguir fornece o **implantar no Azure** botão. Quando o botão for pressionado, o aplicativo Wingtip é totalmente implantado um cinco minutos depois. O aplicativo Wingtip é executado na nuvem do Azure e usa o banco de dados do SQL Azure. Wingtip é implantado em sua assinatura do Azure. Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo.

O aplicativo é implantado com dados para três locatários de exemplo. Os locatários são armazenados juntos em um banco de dados de vários locatários.

Qualquer pessoa pode baixar o código fonte do C# e do PowerShell para Wingtip Tickets em [nosso repositório do GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Neste tutorial, você aprende

> [!div class="checklist"]

> - Como implantar o aplicativo SaaS do Wingtip.
> - Onde obter o código-fonte do aplicativo e scripts de gerenciamento.
> - Sobre os servidores e bancos de dados que constituem o aplicativo.
> - Quantos locatários são mapeados para seus dados com o *catálogo*.
> - Como provisionar um novo locatário.
> - Como monitorar a atividade de locatário no aplicativo.

Há uma série de tutoriais relacionados que utilizam essa implantação inicial. Os tutoriais exploram padrões de design e gerenciamento de um intervalo de SaaS. Quando você trabalhar com os tutoriais, você é incentivado a percorrer os scripts fornecidos para ver como os diferentes padrões de SaaS são implementados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

- O SDK mais recente do Azure PowerShell instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Implantar o aplicativo SaaS do Wingtip

1. Clique no botão **Implantar no Azure** da seguinte maneira.
    - Ele abre o portal do Azure com o modelo de implantação Wingtip tíquetes SaaS. Cada modelo exige dois valores de parâmetro: um nome para um novo grupo de recursos e um nome de usuário que distingue essa implantação de outras implantações do aplicativo. A próxima etapa fornece detalhes para definir esses valores.
        - Certifique-se de observar os valores exatos que usa, pois você precisará inseri-los em um arquivo de configuração.

    [![O botão Implantar no Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Insira os valores de parâmetros necessários para a implantação.

    > [!IMPORTANT]
    > Alguns autenticação e as configurações de firewall de servidor são intencionalmente inseguras para facilitar a demonstração. **Criar um novo grupo de recursos** e não usar grupos de recursos, servidores ou pools existentes. Não use este aplicativo ou todos os recursos que cria, para a produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    É melhor usar apenas letras minúsculas, números e hifens nos nomes de recursos.

    - Para **Grupo de recursos** – selecione **Criar novo** e, em seguida, forneça um **Nome** para o grupo de recursos (diferencia maiúsculas de minúsculas).
        - Recomendamos que todas as letras do nome do grupo de recursos sejam minúsculas.
        - Recomendamos também que você acrescente um traço, seguido por suas iniciais e por um dígito: por exemplo, *wingtip-sa-af1*.
        - Selecione uma **Localização** na lista suspensa.
    - Para **Usuário** – recomendamos escolher um valor curto de **Usuário**, como suas iniciais mais um dígito: por exemplo, *af1*.

3. **Implantar o aplicativo**.

    - Clique se você concordar com os termos e condições.
    - Clique em **Comprar**.

4. Monitore o status da implantação clicando em **Notificações** (o ícone de sino à direita da caixa de pesquisa). A implantação do aplicativo Wingtip SaaS leva aproximadamente cinco minutos.

   ![implantação bem-sucedida](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Baixar e desbloquear os scripts SaaS do Wingtip

Enquanto o aplicativo for implantado, baixe os scripts de gerenciamento e de código-fonte.

> [!IMPORTANT]
> Conteúdos executáveis (scripts, dlls) podem ser bloqueados pelo Windows quando arquivos zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, siga as etapas abaixo para desbloquear o arquivo. zip antes da extração. Ao desbloquear o arquivo. zip é garante que os scripts podem ser executados.

1. Navegue até o [repositório WingtipTicketsSaaS-MultiTenantDb do GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Clique em **Clonar ou baixar**.
3. Clique em **Baixar ZIP** e salve o arquivo.
4. Clique com botão direito no arquivo **WingtipSaaS master.zip** e selecione **Propriedades**.
5. Na guia **Geral**, selecione **Desbloquear** e clique em **Aplicar**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *... \\módulos de aprendizado\\WingtipSaaS mester\\*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Atualize o arquivo de configuração para sua implantação

Antes de executar os scripts, defina o *grupo de recursos* e valores de *usuário* em **UserConfig.psm1**. Defina essas variáveis para os valores definidos durante a implantação.

1. Abra ...\\Módulos de aprendizado\\*UserConfig.psm1* no *ISE do PowerShell*
2. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Salve as alterações.

Os valores definidos no arquivo são usados por todos os scripts, portanto, é importante que eles são precisos. Se você reimplantar o aplicativo, certifique-se de que você escolha um valor de usuário e grupo de recursos diferente. Em seguida, atualize o UserConfig com os novos valores.

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo demonstra locais, como teatros, clubes de jazz, clubes esportivos, que hospedam eventos. Os locais se registram como os clientes (ou locatários) da plataforma Wingtip para uma maneira fácil de listar eventos e vender ingressos. Cada local obtém um aplicativo Web personalizado para gerenciar e listar seus eventos e vender ingressos, independente e isolado de outros locatários. Nos bastidores, os dados de cada locatário são armazenados por padrão em um banco de dados multilocatário fragmentado.

Um **Hub de Eventos** central fornece uma lista de URLs de locatários específica da sua implantação.

1. Abra o *Hub de eventos* no navegador da web:
    - Abra o &lt;Hub de Eventos&gt;: http://events.wtp.&nbsp;USER*.trafficmanager.net (substitua pelo seu nome de usuário de implantação.)*

    ![hub de eventos](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

   ![Eventos](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

Para controlar a distribuição das solicitações de entrada, o aplicativo usa o [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). As páginas de eventos, que são específicas do locatário, exigem que os nomes de locatário sejam incluídos nas URLs. As URLs também incluem o valor específico do usuário e o seguinte formato:

- http://Events.Wingtip. &lt;Usuário&gt;.trafficmanager.net/*fabrikamjazzclub*
 
O aplicativo de eventos analisa o nome do locatário com base na URL e o utiliza para criar uma chave para acessar um catálogo usando o [gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md). O catálogo mapeia a chave para o local do banco de dados do locatário. O **Hub de eventos** lista todos os locatários que estão registrados no catálogo. O **Hub de Eventos** usa metadados estendidos no catálogo para recuperar o nome do locatário associado a cada banco de dados.

Em um ambiente de produção, você normalmente criaria um registro DNS CNAME para [apontar um domínio de Internet da empresa](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que o aplicativo está implantado, vamos colocá-lo em funcionamento! O script do PowerShell *Demo-LoadGenerator* começa uma execução de carga de trabalho em todos os bancos de dados de locatário. A carga real em um aplicativo SaaS é normalmente esporádica e imprevisível. Para simular isso, o gerador de carga gera uma carga aleatória distribuída entre todos os locatários. A carga inclui intermitências aleatórias em cada locatário que ocorrem em intervalos aleatórias. Por isso, levará vários minutos para que o padrão de carga surja, portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Utilitários\\*Demo-LoadGenerator.ps1*.
2. Pressione **F5** para executar o script e iniciar o gerador de carga (deixe os valores de parâmetro padrão por enquanto).

> [!IMPORTANT]
> O *LoadGenerator.ps1 demonstração* script abre outra sessão do PowerShell, em que o gerador de carga é executado. O gerador de carga é executado nessa sessão como uma tarefa de primeiro plano que invoca os trabalhos de geração de carregamento em segundo plano, uma para cada locatário.

Depois que a tarefa de primeiro plano é iniciado, ele permanecerá em um estado de chamada de trabalho. A tarefa iniciar trabalhos de plano de fundo adicional para quaisquer novos locatários que são provisionados subsequentemente.

Fechando a sessão do PowerShell interrompe todos os trabalhos.

Deseja reiniciar a sessão do gerador de carga para usar valores de parâmetros diferentes. Nesse caso, feche o PowerShell sessão geração e, em seguida, execute novamente o *LoadGenerator.ps1 demonstração*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Provisionar um novo locatário para o banco de dados fragmentado

A implantação inicial inclui três locatários de exemplo no *Tenants1* banco de dados. Vamos criar outro locatário para ver como isso afeta o aplicativo implantado. Nesta etapa, você cria rapidamente um novo locatário.

1. Abra ...\\Módulos de aprendizado\Provisionar e catalogar\\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell*.
2. Pressione **F5** para executar o script (mantenha os valores padrão por enquanto).

   > [!NOTE]
   > Muitos scripts Wingtip tíquetes SaaS usam *$PSScriptRoot* para permitir a navegação de pastas, ou chamar outros scripts ou para importar os módulos. Essa variável é avaliada apenas quando o script é executado, pressionando **F5**.  Realçar e executar uma seleção (**F8**) pode resultar em erros, então pressione **F5** ao executar scripts.

O novo locatário vermelho bordo corrida é adicionado ao *Tenants1* banco de dados e registrados no catálogo. O novo locatário do tíquete de venda *eventos* site abre no navegador:

![Novo locatário](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de Eventos* e o novo locatário agora está na lista.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisionar um novo locatário em seu próprio banco de dados

O modelo multilocatário fragmentado permite que você escolha se deseja provisionar um novo locatário em um banco de dados multilocatário junto com outros locatários ou provisionar o locatário em um banco de dados separado. Um locatário em seu próprio beneficia-se de ter seus dados isolados dos dados de outros locatários. O isolamento permite que você gerencie o desempenho do locatário independentemente de outros locatários. Além disso, é mais fácil restaurar os dados para um momento anterior para o locatário isolado. Por exemplo, você pode colocar clientes regulares ou de avaliação gratuita em um banco de dados multilocatário e clientes premium em bancos de dados individuais. Se você criar muitos bancos de dados que contêm somente um locatário, você pode gerenciá-los todos coletivamente em um pool Elástico para otimizar os custos do recurso.  

Agora, vamos provisionar outro locatário, dessa vez em seu próprio banco de dados.

1. Em... \\Módulos de aprendizado\\provisionar e catálogo\*ProvisionTenants.ps1* de demonstração, modificar *$TenantName* para **Salix Salsa**, *$VenueType*  para **dança** e *$Scenario* para **2**.

2. Pressione **F5** para executar o script.
    - Este pressione F5 provisiona ao novo locatário em um banco de dados separado. O banco de dados e de locatário são registrados no catálogo. Em seguida, o navegador é aberto para a página de eventos do locatário.

   ![Página de eventos Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Role até o final do painel à direita. Existe na faixa, verá o nome do banco de dados no qual os dados de locatário são armazenados.

3. Atualize o Hub de Eventos e o novo locatário agora está na lista.



## <a name="explore-the-servers-and-tenant-databases"></a>Explorar os servidores e bancos de dados de locatários

Vamos examinar alguns dos recursos que foram implantados:

1. No [portal do Azure](http://portal.azure.com), procure a lista de grupos de recursos. Abra o grupo de recursos criados quando você implantou o aplicativo.

   ![grupo de recursos](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Clique em **catálogo mt&lt;usuário&gt;**  server. O servidor de catálogo contém dois bancos de dados denominados *tenantcatalog* e *basetenantdb*. O *basetenantdb* banco de dados é um modelo vazio. Ele é copiado para criar um novo banco de dados de locatário, se usado para vários locatários ou apenas um locatário.

   ![servidor catalog](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Volte para o grupo de recursos e selecione o *tenants1 mt* servidor que hospeda os bancos de dados de locatário.
    - O banco de dados tenants1 é um banco de dados multilocatário em que os três locatários originais, além do primeiro locatário que você adicionou, é armazenado. Ele é configurado como um banco de dados Standard de DTU 50.
    - O **salixsalsa** banco de dados contém o local de dança Salix Salsa como seu único locatário. Ele é configurado como um banco de dados de edição Standard com 50 DTUs por padrão.

   ![servidor de locatários](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>O nível de desempenho do banco de dados

Se o gerador de carga estiver sendo executado por vários minutos, dados suficientes deverão estar disponíveis para começar a examinar alguns dos recursos de monitoramento incorporados em pools e bancos de dados.

1. Navegue até o **tenants1 mt&lt;usuário&gt;**  servidor e clique em **tenants1** para exibir a utilização de recursos para o banco de dados que tem quatro locatários nele. Cada locatário está sujeito a uma carga pesada esporádica do gerador de carga:

   ![Monitor tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   O gráfico de utilização de DTU bem ilustra como um banco de dados multilocatário pode dar suporte a uma carga de trabalho imprevisível entre vários locatários. Nesse caso, o gerador de carga está aplicando uma carga esporádica de aproximadamente 30 DTUs para cada locatário. Essa carga é igual a 60% da utilização de um banco de dados DTU 50. Picos que excedem 60% são o resultado de carga que está sendo aplicada a mais de um locatário ao mesmo tempo.

2. Navegue até o **tenants1 mt&lt;usuário&gt;**  servidor e clique no **salixsalsa** banco de dados para exibir a utilização de recursos nesse banco de dados que contém somente um locatário.

   ![banco de dados salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

O gerador de carga está aplicando uma carga semelhante para cada locatário, independentemente de qual banco de dados a cada locatário está em. Com apenas um locatário no **salixsalsa** banco de dados, você pode ver que o banco de dados pode sustentar uma carga muito maior que o banco de dados com vários locatários. 

> [!NOTE]
> As cargas criadas pelo gerador de carga são apenas para fins ilustrativos.  Os recursos alocados para bancos de dados multilocatários e de locatário único e os números de locatários, que você pode hospedar em um banco de dados multilocatário dependem os padrões de carga de trabalho real em seu aplicativo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]

> - O aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets foi implantado
> - Sobre os servidores e bancos de dados que constituem o aplicativo
> - Que os locatários são mapeados para seus dados com o *catálogo*
> - Como provisionar novos locatários, em um banco de dados de vários locatários e o banco de dados único locatário.
> - Como exibir a utilização do pool para monitorar a atividade do locatário
> - Como excluir recursos de exemplo para interromper a cobrança relacionada

Agora, tente fazer o [Tutorial para provisionar e catalogar](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)








<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/





<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."

-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png


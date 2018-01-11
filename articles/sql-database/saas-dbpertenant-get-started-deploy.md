---
title: "Tutorial de SaaS Database-per-tenant – Banco de Dados SQL do Azure | Microsoft Docs"
description: "Implante e explore o aplicativo Wingtip Tickets SaaS multi-tenant, que demonstra o Database per Tenant e outros padrões de SaaS usando o Banco de Dados SQL do Azure."
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implantar e explorar um aplicativo SaaS multilocatário que usa o padrão por locatário do banco de dados com o Banco de Dados SQL do Azure

Neste tutorial, você implanta e explora o aplicativo Wingtip Tickets SaaS *Database per Tenant* (Wingtip). O aplicativo usa um padrão de banco de dados por locatário para armazenar os dados de vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

Cinco minutos depois de clicar no botão azul rotulado **Implantar no Azure**, você tem um aplicativo de SaaS multilocatário. O aplicativo inclui um Banco de Dados SQL do Azure em execução na nuvem do Microsoft Azure. O aplicativo é implantado com três locatários de exemplo, cada qual com seu próprio banco de dados. Todos os bancos de dados são implantados em um *pool elástico* do SQL. O aplicativo é implantado em sua assinatura do Azure. Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo. O código-fonte C# do aplicativo e os scripts de gerenciamento estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant do GitHub][github-wingtip-dpt].

Neste tutorial, você aprende:

> [!div class="checklist"]
> - Como implantar o aplicativo SaaS do Wingtip.
> - Onde obter o código-fonte do aplicativo e scripts de gerenciamento.
> - Sobre os servidores, pools e bancos de dados que constituem o aplicativo.
> - Quantos locatários são mapeados para seus dados com o *catálogo*.
> - Como provisionar um novo locatário.
> - Como monitorar a atividade de locatário no aplicativo.

Uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferece a exploração de vários padrões de design e gerenciamento de SaaS. Os tutoriais de compilação além desta implantação inicial.
Quando você usa os tutoriais, pode ver como os diferentes padrões de SaaS são implementados examinando os scripts fornecidos.
Os scripts demonstram como recursos de Banco de Dados SQL do Microsoft Azure simplificam o desenvolvimento de aplicativos SaaS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implantar o aplicativo Wingtip Tickets SaaS

Implante o aplicativo:

1. Escolha e lembre-se de valores que serão necessários para os seguintes parâmetros:

    - **Usuário**: escolha um valor curto, como suas iniciais seguidas por um dígito. Por exemplo: *af1*. O parâmetro Usuário pode conter apenas letras, dígitos e hifens (sem espaços). O primeiro e o último caractere devem ser uma letra ou um dígito. É recomendável que todas as letras estejam em minúsculas.
    - **Grupo de recursos**: cada vez que implantar o aplicativo Wingtip, você deve escolher um nome exclusivo diferente para o novo grupo de recursos. É recomendável que você acrescente o nome de usuário para um nome de base para o grupo de recursos. Um exemplo de nome de grupo de recursos pode ser *wingtip-af1*. Novamente, é recomendável que todas as letras estejam em minúsculas.

2. Abra o modelo de implantação Wingtip Tickets SaaS Database per Tenant no portal do Azure clicando no botão azul **Implantar no Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. No modelo, insira os valores dos parâmetros necessários:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. É recomendável que você *crie um novo grupo de recursos*. Não use grupos de recursos, servidores ou pools existentes. Não use esse aplicativo, scripts ou qualquer recurso implantado para produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    - **Grupo de recursos** – selecione **Criar novo** e forneça o **Nome** exclusivo que você escolheu anteriormente para o grupo de recursos. 
    - **Localização** - selecione uma **Localização** na lista suspensa.
    - **Usuário** – use o valor Nome de usuário que você escolheu anteriormente.

4. Implante o aplicativo.

    - Clique se você concordar com os termos e condições.
    - Clique em **Comprar**.

5. Monitore o status da implantação clicando em **Notificações**, que é o ícone de sino à direita da caixa de pesquisa. A implantação do aplicativo Wingtip Tickets SaaS leva aproximadamente cinco minutos.

   ![implantação bem-sucedida](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Baixar e desbloquear os scripts de gerenciamento de Wingtip Tickets

Enquanto o aplicativo for implantado, baixe os scripts de gerenciamento e de código-fonte.

> [!IMPORTANT]
> Conteúdos executáveis (scripts, DLLs) podem ser bloqueados pelo Windows quando arquivos .zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, siga as etapas abaixo para desbloquear o arquivo. zip antes da extração. O desbloqueio garante que os scripts possam ser executados.

1. Navegue até o [repositório WingtipTicketsSaaS-DbPerTenant do GitHub][github-wingtip-dpt].
2. Clique em **Clonar ou baixar**.
3. Clique em **Fazer o download do ZIP** e salve o arquivo.
4. Clique com o botão direito do mouse no arquivo **WingtipTicketsSaaS-DbPerTenant-master.zip** e selecione **Propriedades**.
5. Na guia **Geral**, selecione **Desbloquear** e clique em **Aplicar**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *.\\WingtipTicketsSaaS-DbPerTenant-master\\Módulos de Aprendizado*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualizar o arquivo de configuração para esta implantação

Antes de executar os scripts, atualize os valores *grupo de recursos* e *usuário* em **UserConfig.psm1**. Defina essas variáveis como os valores definidos durante a implantação.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizado\\*UserConfig.psm1* 
2. Atualize *ResourceGroupName* e *Nomeie* com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Salve as alterações!

Esses valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo apresenta locais que organizam eventos. Os tipos de local são casas de shows, clubes de jazz e de esportes. No Wingtip Tickets, os locais estão registrados como locatários. Ser um locatário oferece ao local uma maneira fácil de listar eventos e vender ingressos para os clientes. Cada local obtém um site personalizado para listar seus eventos e vender ingressos.

Internamente no aplicativo, cada locatário obtém um banco de dados SQL implantado em um pool elástico do SQL.

Uma página central do **Hub de Eventos** fornece uma lista de links para os locatários em sua implantação.

1. Abra o *Hub de Eventos* em seu navegador da Web: http://events.wingtip-dpt.&lt;USUÁRIO&gt;.trafficmanager.net (substitua &lt;USUÁRIO&gt; pelo seu nome de usuário da implantação):

    ![hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Gerenciador de Tráfego do Azure

O aplicativo Wingtip usa o [*Gerenciador de Tráfego do Azure*](../traffic-manager/traffic-manager-overview.md) para controlar a distribuição de solicitações de entrada. A URL para acessar o hub de eventos para um locatário deve obedecer o formato a seguir:

- http://events.wingtip-dpt.&lt;USUÁRIO&gt;.trafficmanager.net/fabrikamjazzclub

As partes do formato anterior são explicadas na tabela a seguir.

| Parte da URL | Descrição |
| :------- | :---------- |
| http://events.wingtip-dpt | As partes de eventos do aplicativo Wingtip.<br /><br />A porção ***-dpt*** distingue a implementação de Wingtip *Database Per Tenant* de outras implementações ligeiramente diferentes. Por exemplo, outros artigos de documentação oferecem Wingtip para *Standalong* (*-sa*), ou para *Banco de dados multilocatário*. |
| .*&lt;USER&gt;* | *af1* em nosso exemplo. |
| .trafficmanager.net/ | Gerenciador de Tráfego do Azure, URL base. |
| fabrikamjazzclub | Para o locatário nomeado *Fabrikam Jazz Club*. |
| &nbsp; | &nbsp; |

1. O nome do locatário é analisado a partir da URL, pelo aplicativo de eventos.
2. O nome do locatário é usado para criar uma chave.
3. A chave é usada para acessar o catálogo, para obter o local do banco de dados do locatário.
    - O catálogo é implementado usando *gerenciamento de mapas de fragmentos*.
4. O *Hub de eventos* usa metadados estendidos no catálogo para obter uma lista de URLs de evento.

Em um ambiente de produção, normalmente, um registro DNS CNAME é criado para [*apontar um domínio da Internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para o perfil do gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que o aplicativo está implantado, vamos colocá-lo em funcionamento!
O script do PowerShell *Demo-LoadGenerator* começa uma execução de carga de trabalho em todos os bancos de dados de locatário.
A carga real em um aplicativo SaaS é esporádica e imprevisível.
Para simular esse tipo de carga, o gerador produz uma carga com picos aleatórias ou de intermitências de atividade em cada locatário.
As intermitências ocorrem em intervalos aleatórios.
Levará vários minutos para que o padrão de carga surja. Portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Utilitários\\*Demo-LoadGenerator.ps1*.
2. Pressione **F5** para executar o script e iniciar o gerador de carga. (Deixe os valores de parâmetro padrão por ora.)

Não reutilize a mesma instância do ISE do PowerShell para qualquer coisa, a não ser talvez por uma nova execução de *Demo-LoadGenerator.ps1*. Se você precisar executar outros scripts do PowerShell, inicie um ISE do PowerShell.

#### <a name="rerun-with-different-parameters"></a>Executar novamente com parâmetros diferentes

Siga estas etapas se você desejar executar novamente o teste de carga de trabalho com parâmetros diferentes:

1. Pare o *LoadGenerator.ps1*.
    - Use **Ctrl + C** ou clique no botão **Parar**.
    - Essa interrupção não interrompe ou afeta todos os trabalhos incompletos em segundo plano que ainda estão em execução.

2. Execute novamente o *Demo-LoadGenerator.ps1*.
    - Este nova execução primeiro interrompe qualquer um dos trabalhos em segundo plano que podem ainda estar executando o *sp_CpuLoadGenerator*.

Ou você pode encerrar a instância do ISE do PowerShell, que interrompe qualquer trabalho em segundo plano. Em seguida, inicie uma nova instância do ISE do PowerShell e execute novamente o *Demo-LoadGenerator.ps1*.

#### <a name="monitor-the-background-jobs"></a>Monitorar trabalhos em segundo plano

Se deseja controlar e monitorar os trabalhos em segundo plano, você pode usar os cmdlets a seguir:

- Get-Job
- Receive-Job
- Stop-Job

#### <a name="demo-loadgeneratorps1-actions"></a>Ações Demo-LoadGenerator.ps1

O *Demo-LoadGenerator.ps1* imita uma carga de trabalho ativa de transações de cliente. As etapas a seguir descrevem a sequência de ações que o *Demo-LoadGenerator.ps1* inicia:

1. O *Demo-LoadGenerator.ps1* inicia o *LoadGenerator.ps1* em primeiro plano.
    - Ambos os arquivos. ps1 são armazenados nas pastas *Módulos de aprendizado\\Utilitários\\*.

2. O *LoadGenerator.ps1* é executado repetidamente em todos os bancos de dados do locatário que estão registrados no catálogo.

3. Para cada banco de dados de locatário, o *LoadGenerator.ps1* inicia a execução do procedimento armazenado do Transact-SQL chamado *sp_CpuLoadGenerator*.
    - As execuções são iniciadas em segundo plano, chamando o cmdlet *Invoke-SqlAzureWithRetry*.
    - O *sp_CpuLoadGenerator* é executado repetidamente em torno de uma instrução SELECT do SQL para uma duração padrão de 60 segundos. O intervalo de tempo entre problemas do SELECT varia de acordo com os valores do parâmetro.
    - Esse arquivo .sql é armazenado em *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Para cada banco de dados de locatário, o *LoadGenerator.ps1* também inicia o cmdlet *Start-Job*.
    - O *Start-Job* imita uma carga de trabalho de vendas de tíquete.

5. O *LoadGenerator.ps1* continua em execução, monitorando para quaisquer novos locatários que sejam provisionados.

&nbsp;

Antes de continuar na próxima seção, deixe o gerador de carga em execução no estado de invocação de trabalho.

## <a name="provision-a-new-tenant"></a>Provisionar um novo locatário

A implantação inicial cria três locatários de amostra. Agora você cria outro locatário para ver o impacto no aplicativo implantado. No aplicativo Wingtip, o fluxo de trabalho para provisionar novos locatários é explicado no [Tutorial de provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md). Nesta fase, você cria um novo locatário, o que leva menos de um minuto.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizado\Provisionamento e Catálogo\\*Demo-ProvisionAndCatalog.ps1*.
2. Pressione **F5** para executar o script. (Deixe os valores padrão por ora.)

   > [!NOTE]
   > Vários scripts Wingtip SaaS usam *$PSScriptRoot* para navegar em pastas para chamar funções em outros scripts. Essa variável é avaliada apenas quando o script completo é executado ao pressionar **F5**.  Destacar e executar uma seleção com **F8** pode resultar em erros. Portanto, execute os scripts pressionando **F5**.

O novo banco de dados de locatário é:

- Criado em um pool Elástico do SQL.
- Inicializado.
- Registrado no catálogo.

Após o provisionamento bem-sucedido, o site *Eventos* do novo locatário aparecerá no seu navegador:

![Novo locatário](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de Eventos* para fazer com que o novo locatário apareça na lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os pools e os bancos de dados de locatário

Agora que você começou a executar uma carga na coleção de locatários, vamos analisar alguns dos recursos que foram implantados:

1. No [Portal do Azure](http://portal.azure.com), navegue até sua lista de servidores SQL e abra o servidor **catalog-dpt-&lt;USER&gt;**.
    - O servidor de catálogo contém dois bancos de dados, o **tenantcatalog** e o **basetenantdb** (um banco de dados modelo que é copiado para criar novos locatários).

   ![databases](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Volte à lista de servidores SQL.

3. Abra o servidor**tenants1-dpt-&lt;USER&gt;** que contém os bancos de dados de locatário.

4. Confira os itens a seguir:
    - Cada banco de dados de locatário é um banco de dados *elástico padrão* em um pool padrão de 50 eDTU.
    - O banco de dados *Red Maple Racing*, que é o banco de dados de locatário que você provisionou anteriormente.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorar o pool

Depois de *LoadGenerator.ps1* ser executado por vários minutos, dados suficientes devem estar disponíveis para começar a olhar para alguns recursos de monitoramento. Esses recursos são criados em pools e bancos de dados.

Navegue até o servidor **tenants1-dpt-&lt;USER&gt;** e clique em **Pool1** para exibir a utilização de recursos do pool. Nos gráficos a seguir, o gerador de carga foi executado em uma hora.

   ![monitorar pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- O primeiro gráfico, rotulado **Utilização de recursos**, mostra a utilização de eDTU do pool.
- O segundo gráfico mostra a utilização de eDTU dos cinco principais bancos de dados no pool.

Os dois gráficos ilustram que os pools elásticos e o Banco de Dados SQL são bem adequados para cargas de trabalho de aplicativos SaaS.
Os gráficos mostram que 4 bancos de dados são cada um intermitências para até 40 eDTUs e ainda oferecem suporte confortavelmente a todos os bancos de dados para um pool de 50 eDTU. O pool de 50 eDTU pode dar suporte a cargas de trabalho ainda mais pesadas.
Se eles foram provisionados como bancos de dados independentes, cada um deles precisa ser um S2 (50 DTU) para dar suporte a intermitências.
O custo de 4 bancos de dados independentes S2 seria quase 3 vezes o preço do pool.
Em situações reais, os clientes do Banco de dados SQL estão executando atualmente até 500 bancos de dados em 200 pools de eDTU.
Para obter mais informações, consulte o [tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Para saber mais sobre pools elásticos, consulte [*O que é um pool elástico do SQL do Azure*](sql-database-elastic-pool.md)
- Para saber mais sobre os trabalhos elásticos, consulte [*Gerenciando bancos de dados de nuvem com escalonamento horizontal*](sql-database-elastic-jobs-overview.md)
- Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> - Como implantar o aplicativo Wingtip Tickets SaaS
> - Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> - Que os locatários são mapeados para seus dados com o *catálogo*
> - Como provisionar novos locatários
> - Como exibir a utilização do pool para monitorar a atividade do locatário
> - Como excluir recursos de exemplo para interromper a cobrança relacionada

Em seguida, tente fazer o [Tutorial para provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 


<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure Websites Web Hosting Plans In-Depth Overview - Windows Azure feature guide" description="Learn how Web Hosting Plans for Azure Websites work, and how they benefit your management experience." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron="" Tardif="" and="" Yochay="" Kiryaty" />
</br>

# Visão geral detalhada de planos de hospedagem na Web de sites do Azure

</br>
Os planos de hospedagem na Web (WHPs) representam um conjunto de recursos e capacidades que são possíveis de compartilhar entre seus sites. Os planos de hospedagem na Web oferecem suporte às 4 faixas de preço dos sites do Azure (Grátis, Compartilhado, Básica e Padrão), sendo que cada faixa tem seus próprios recursos e capacidade. Sites na mesma assinatura, grupo de recursos e localização geográfica podem compartilhar um plano de hospedagem na Web. Todos os sites compartilham um plano de hospedagem na Web que pode alavancar todos os recursos e capacidades definidos pela faixa de plano de hospedagem na Web. Todos os sites associados com um dado plano de hospedagem de web executam nos recursos definidos pelo plano de hospedagem na Web. Por exemplo, se seu plano de hospedagem na Web está configurado para duas máquinas virtuais “pequenas”, todos os sites associados com esse plano de hospedagem na Web irão executar nas duas máquinas virtuais. Como sempre com as máquinas virtuais dos sites Azure, seus sites que estão em execução são totalmente gerenciados e altamente disponíveis.
</br>
Nesse artigo exploraremos as principais características, como as faixas e escalas, de planos de hospedagem de web e como elas funcionam enquanto gerenciam seus sites.
</br>

## Sites, planos de hospedagem na Web e grupos de recursos

</br>
Um site pode ser associado com apenas um plano de hospedagem na Web em qualquer dado momento. Um plano de hospedagem na Web é associado com um grupo de recursos. Um grupo de recursos é um novo conceito no Azure que serve como o limite do clico da vida para todos os recursos contidos nele. Grupos de recursos habilitam o gerenciamento de todos os pedaços de um aplicativo juntos.
</br>
É possível ter múltiplos planos de hospedagem de web em um grupo de recursos e cada plano de hospedagem terá seu próprio conjunto de recursos e capacidades que são utilizadas pelos seus sites associados. A imagem a seguir ilustra essa relação:
</br>
![Planos de hospedagem na Web e grupos de recursos][Planos de hospedagem na Web e grupos de recursos]
</br>
A capacidade para ter múltiplos planos de hospedagem na Web em um único grupo de recursos permite alocar sites diferentes para recursos diferentes, primeiramente máquinas virtuais executando seus sites. Por exemplo, essa capacidade permite a separação de recursos entre sites de teste e desenvolvimento e produção, onde você pode querer atribuir um plano de hospedagem de web com seu próprio conjunto específico de recursos para os seus locais de produção e um segundo plano de hospedagem na Web para seus sites desenvolvimento e teste.
</br>
Ter múltiplos planos de hospedagem na Web em um único grupo de recursos também permite definir um aplicativo que se expande por regiões. Por exemplo, um site altamente disponível executando em duas regiões incluirá dois sites, um para cada região e um site associado com cada plano de hospedagem na Web. Em tal situação, todos os sites serão associados com um único grupo de recursos, que define um aplicativo. Tendo uma visão única de um grupo de recursos com múltiplos planos de hospedagem na Web e vários sites faz com que seja fácil de gerenciar, controlar e exibir a integridade dos sites. No topo do gerenciamento dos recursos de sites e respectivos sites para um determinado aplicativo, é possível associar qualquer recurso do Azure relacionado, como o banco de dados SQL e Projetos de equipe.
</br>

## Quando devo criar um novo grupo de recursos e quando devo criar um novo plano de hospedagem na Web?

</br>
Ao criar um novo site, deve-se considerar a criação de um novo grupo de recursos quando o site que você está prestes a criar representa um novo aplicativo Web. Nesse caso, criar um novo grupo de recursos, um plano de hospedagem na Web associado e um site é a escolha certa. Ao criar um novo site através da nova visualização de portal do Azure, usando a galeria ou a nova opção de site + SQL, o portal será o padrão para criar um novo plano de grupo de recursos e de hospedagem para o seu novo site. Se você precisar, no entanto, pode substituir esses padrões.
</br>
![Criando um novo plano de hospedagem na Web][Criando um novo plano de hospedagem na Web]
</br>
É possível adicionar sempre um novo site ou quaisquer outros recursos para um grupo de recursos existente. Ao criar um novo site do contexto de um grupo de recursos existente, o novo assistente do site padroniza para o plano de hospedagem na Web e recurso existente. Aqui também você pode substituir esses padrões conforme for necessário. Ao adicionar um novo site a um grupo de recursos existente, é possível escolher adicionar o site a um plano de hospedagem na Web (essa é a opção padrão na nova visualização do portal do Azure), ou você pode criar um novo plano de hospedagem na Web para adicionar ao site.
</br>
Criar um novo plano de hospedagem permite que você atribua um novo conjunto de recursos aos seus sites e possibilita maior controle sobre a alocação, já que cada plano de hospedagem na Web tem o seu próprio conjunto de máquinas virtuais. Como é possível mover sites entre planos de hospedagem na Web, supondo que os planos de hospedagem na Web estão na mesma região, a decisão de se criar um novo plano de hospedagem na Web ou não tem pouca importância. Se um determinado site começa a consumir recursos em demasiado ou se vocês precisa apenas separar alguns sites, é possível criar um novo plano de hospedagem na Web e mover seus sites para ele.
</br>
Se deseja criar um novo site em uma região diferente e essa região não tem um plano de hospedagem na Web existente, você terá de criar um plano de hospedagem na Web nessa região para poder ter um site associado a ela.
</br>
Uma coisa importante a se lembrar é que não é possível mover planos de hospedagem na Web ou sites entre grupos de recursos. Também não é possível mover um site entre dois planos de hospedagem na Web que estão em duas regiões separadas.
</br>

## Grupos de recursos existentes no portal de visualização do Azure

</br>
Se você já possui sites existentes nos sites do Azure, notará que todos os seus sites aparecem no portal de visualização do Azure. É possível ver todos os seus sites como uma lista simples clicando no botão do **procurar** no painel de navegação esquerdo e selecionando **Sites**:
</br>
![Consulte todos os seus sites em uma lista simples][Consulte todos os seus sites em uma lista simples]
</br>
Também é possível consultar todos os grupos de recursos que foram criados por você ao clicar no painel de navegação esquerdo, selecionar o botão do **navegador** no painel de navegação esquerdo e selecionar **Grupos de recursos**:
</br>
![Consulte todos os grupos de recursos que foram criados][Consulte todos os grupos de recursos que foram criados]
</br>
Você também notará que tem um grupo de recursos padrão gerado automaticamente em cada região em que já possui sites. O nome do grupo de recursos gerado automaticamente para sites é *Default-Web-<location name>* onde o nome do local representa uma região do Azure (por exemplo *Default-Web-WestUS*). Em cada grupo de recursos você encontrará todos os seus sites existentes para a região do grupo. Cada site que você criou no passado ou criará no futuro estará tanto no portal do Azure Completo quanto no Portal de visualização do Azure.
</br>
Como todo o site tem sido associado a um plano de hospedagem na Web, criamos planos de hospedagem na Web padrão para seus sites existentes de acordo com a seguinte convenção, em cada região:
</br>

-   Todos os seus sites **Gratuitos** são associados a um plano de hospedagem na Web **Padrão** e sua faixa de preço é definida como **Gratuito**.
    </br>
-   Todos os seus sites **Compartilhados** são associados a um plano de hospedagem na Web **Padrão** e sua faixa de preço é definida como **Compartilhado**
    </br>
-   Todos os seus sites **Padrão** são associados a um plano de hospedagem na Web padrão e sua faixa de preço é definida como **Compartilhado**.
    </br>
    O nome desse plano de hospedagem na Web é **DefaultServerFarm**. Esse nome foi escolhido para dar suporte a uma API herdada. O nome **ServerFarm** pode ser um pouco enganador, uma vez que ele se refere a um **Plano de hospedagem na Web**, mas é importante notar que ele é um nome de plano de hospedagem na Web e não uma entidade própria.
    </br>

    ## Perguntas frequentes sobre plano de hospedagem na Web

    </br>
    **Questão**: Como fazer para criar um plano de hospedagem na Web?
    </br>
    **Resposta**: Um plano de hospedagem na Web é um contêiner e, como tal, não é possível criar um plano de hospedagem na Web vazio. No entanto, um novo plano de hospedagem de web é explicitamente criado durante a criação do site.
    </br>
    Para fazer isso usando a interface de usuário no novo **portal de visualização do Azure** clique em **NOVO** e selecione **Site**, que irá abrir a lâmina para criação de site. Na primeira imagem abaixo você poderá ver o ícone **NOVO** na parte inferior esquerda e na segunda imagem você verá a lâmina de criação de **Site**, a lâmina do **Plano de hospedagem na Web** e a lâmina da **Faixa de preço**:
    </br>
    ![Criar um novo site][Criar um novo site]
    </br>
    ![Site, lâminas de plano de hospedagem na Web e faixa de preço][Site, lâminas de plano de hospedagem na Web e faixa de preço]
    </br>
    Nesse exemplo escolhemos criar um novo site chamado **contosomarketing** e colocá-lo no novo plano de hospedagem na Web chamado**contoso**. A faixa de preço selecionada para esse plano de hospedagem na Web é **Padrão pequeno**. Para obter mais detalhes sobre as faixas de preço do plano de hospedagem na Web bem como recursos, opções de escala e preço fornecidos em cada um, visite as [Especificações de planos de hospedagem na Web][Especificações de planos de hospedagem na Web].
    </br>
    Observe também que um plano de hospedagem também pode ser criado no portal do Azure existente. Isso é feito como parte do assistente de **criação rápida** selecionando **Criar novo plano de hospedagem na Web** da lista suspensa **PLANO DE HOSPEDAGEM DE WEB**:
    </br>
    ![Criar novo plano de hospedagem de web no portal existente][Criar novo plano de hospedagem de web no portal existente]
    </br>
    Para esse exemplo criamos um site chamado **northwind** e escolhemos criar um novo plano de hospedagem na Web. O resultado dessa operação será um novo plano de hospedagem na Web chamado **default0** que contém o site **northwind**. Todos os planos de hospedagem na Web criados através dessa experiência seguem essa convenção de nomenclatura e os planos de hospedagem na Web não podem ser renomeados depois de criados. Além disso, os planos de hospedagem na Web criados através desse processo serão criados na faixa de preço **Gratuito**.
    </br>
    **Questão**: Como fazer para atribuir um site a um**Plano de hospedagem na Web**?
    </br>
    **Resposta**: Os sites são atribuídos a um plano de hospedagem na Web como parte do processo de criação de sites. Para fazer isso usando a interface de usuário na nova **Visualização de portal do Azure**, clique em **NOVO** e selecione **Site**:
    </br>
    ![Criar um novo site][1]
    </br>
    Depois, na lâmina de criação de site, selecione o plano de hospedagem:
    </br>
    ![Selecionar um plano de hospedagem][Selecionar um plano de hospedagem]
    </br>
    Um site pode também ser criado em um plano de hospedagem na Web usando o portal do Azure existente. Isso é feito como parte do assistente de **criação rápida**. Depois de digitar a URL do site, utilize a lista suspensa do **PLANO DE HOSPEDAGEM NA WEB** para selecionar:
    </br>
    ![Selecione um plano de hospedagem no portal existente][Selecione um plano de hospedagem no portal existente]
    </br>
    **Questão**: Como eu posso mover um site para um plano de hospedagem diferente na Web?
    </br>
    **Resposta**: Você pode mover um site para um plano diferente de hospedagem na Web usando o portal de visualização do Azure. Os sites podem ser movidos entre planos de hospedagem na Web na mesma região geográfica que pertence ao mesmo grupo de recursos.
    </br>
    Para mover um site para outro plano, navegue até a lâmina de site do site que você deseja mover. Em seguida, clique em **Plano de hospedagem na Web**:
    </br>
    ![Escolha um plano de hospedagem na Web novo ou um existente][Escolha um plano de hospedagem na Web novo ou um existente]
    </br>
    Isso abrirá a lâmina do plano de hospedagem na Web. Nesse ponto, você poderá pegar um plano de hospedagem na Web existente ou criar um novo. Planos em uma localização geográfica ou grupo de recursos diferente são desabilitados e não podem ser selecionados
    </br>
    Note que cada plano de hospedagem na Web tem sua própria faixa de preço. Quando você move um site de um plano de hospedagem na Web de faixa **Gratuito** para um plano de hospedagem na Web **Padrão**, seu site estará habilitado para aproveitar todos os recursos da faixa Padrão.
    </br>
    </br>
    **Questão**: Como eu posso dimensionar um plano de hospedagem na Web?
    </br>
    **Resposta**: Existem duas maneiras de dimensionar um plano de hospedagem na Web. Uma maneira é aumentar seu plano de hospedagem na Web e todos os sites associados a ele. Ao alterar a faixa de preço de um plano de hospedagem na Web, todos os sites desse plano de hospedagem na Web estarão sujeitos aos recursos definidos pela faixa de preço.
    </br>
    Na imagem abaixo você pode visualizar a lâmina do **Plano de hospedagem na Web** e também a lâmina da **Faixa de preço**. Clicar na parte da **Faixa de preço** na lâmina do **Plano de hospedagem na Web** expande a lâmina da **Faixa de preço** onde você pode alterar a faixa de preço do plano de hospedagem na Web:
    </br>
    ![O plano de hospedagem na Web e a faixa de preço][O plano de hospedagem na Web e a faixa de preço]
    </br>
    A segunda maneira de dimensionar um plano é aumentá-lo aumentando o número de instâncias em seu plano de hospedagem na Web. Na imagem abaixo é possível visualizar a lâmina do **Plano de hospedagem na Web**, bem como a lâmina de **Escala**. Clicar na área de escala na lâmina do **Plano de hospedagem na Web** o expande e permite alterar a conta de instância do plano:
    </br>
    ![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
    </br>
    Uma vez que o plano de hospedagem na Web na imagem acima está configurado para usar a faixa de preço **Padrão** a opção de **Autoescala** é habilitada.
    </br>
    A execução da alteração no portal completo do Azure pode ser feita na guia **Escala**, como visto a seguir:
    </br>
   ![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
    </br>
    **Questão**: Como eu posso excluir um plano de hospedagem na Web?
    </br>
    **Resposta**: Para excluir um plano de hospedagem na Web você deve primeiro excluir todos os sites associados a ele. Uma vez excluídos todos os sites, um plano de hospedagem pode ser excluído da lâmina do plano de hospedagem na Web:
    </br>
   ![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
    </br>
    No portal completo do Azure, excluir o último site em um plano de hospedagem na Web excluirá automaticamente o plano de hospedagem na Web associado.
    </br>
    **Questão**: Como posso monitorar um plano de hospedagem na Web?
    </br>
    **Resposta**: Os planos de hospedagem na Web podem ser monitorados usando as partes de monitoramento na lâmina do plano de hospedagem:
    </br>
    ![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
    </br>
    Os controles de monitoramento podem ser personalizados clicando com o botão direito do mouse no controle e selecionando **Editar Consulta**:
    </br>
   ![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
    </br>
    As métricas expostas são:
    </br>
-   Porcentagem de CPU
    </br>
-  Porcentagem de Memória
    </br>
-   Tamanho da fila do disco
    </br>
-   Tamanho da fila HTTP.
    </br>
    Essas métricas representam a média de uso entre instâncias pertencentes a um plano de hospedagem na Web. Todas essas métricas podem ser usadas para configurar alertas bem como regras de autoescala.
    </br>

    ## Impressões e conclusões

    </br>
    Os planos de hospedagem na Web representam um conjunto de recursos e habilidades que você pode compartilhar através de seus sites. Um plano de hospedagem na Web oferece a flexibilidade para alocar sites específicos a um determinado conjunto de recursos, como máquinas virtuais, e otimiza ainda mais a alocação de seus recursos do Azure e o uso de sites.

  [Planos de hospedagem na Web e grupos de recursos]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png
  [Criando um novo plano de hospedagem na Web]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png
  [Consulte todos os seus sites em uma lista simples]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png
  [Consulte todos os grupos de recursos que foram criados]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png
  [Criar um novo site]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png
  [Site, lâminas de plano de hospedagem na Web e faixa de preço]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png
  [Especificações de planos de hospedagem na Web]: http://go.microsoft.com/?linkid=9845586
  [Criar novo plano de hospedagem de web no portal existente]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png
  [1]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png
  [Selecionar um plano de hospedagem]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png
  [Selecione um plano de hospedagem no portal existente]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png
  [Escolha um plano de hospedagem na Web novo ou um existente]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png
  [O plano de hospedagem na Web e a faixa de preço]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png

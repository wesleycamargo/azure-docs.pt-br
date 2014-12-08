<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Visão geral detalhada de planos de hospedagem na Web de sites do Azure - guia de recursos do Microsoft Azure" description="Learn how Web Hosting Plans for Azure Websites work, and how they benefit your management experience." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron Tardif and Yochay Kiryaty" />
</br>
#Visão geral detalhada de planos de hospedagem na Web de sites do Azure#
</br>
Planos de hospedagem na Web (WHPs) representam um conjunto de recursos e uma capacidade que pode ser compartilhada entre seus sites.  Planos de hospedagem na Web suportam as 4 faixas de preço para sites do Azure (gratuito, compartilhado, básico e padrão), onde cada camada tem seus próprios recursos e capacidade.   Sites pertencentes à mesma assinatura, grupo de recursos e localização geográfica podem compartilhar uma plano de hospedagem na Web. Todos os sites compartilham um plano de hospedagem na Web que pode alavancar todos os recursos e capacidades definidos pela faixa de plano de hospedagem na Web. Todos os sites associados com um dado plano de hospedagem de web executam nos recursos definidos pelo plano de hospedagem na Web. Por exemplo, se seu plano de hospedagem na Web está configurado para duas máquinas virtuais "pequenas", todos os sites associados com esse plano de hospedagem na Web irão executar nas duas máquinas virtuais. Como sempre, com sites do Azure, as máquinas virtuais nas quais seus sites estão sendo executados são totalmente gerenciadas e altamente disponíveis.
</br>
Neste artigo, exploraremos as principais características como camada e escala de um plano na Web de hospedagem e como eles entram em jogo ao gerenciar seus sites. 
</br>
##Sites, planos de hospedagem na Web e grupos de recursos##
</br>
Um site pode ser associado com apenas um plano de hospedagem na Web em qualquer dado momento. Um plano de hospedagem na Web é associado com um grupo de recursos. Um grupo de recursos é um novo conceito no Azure que serve como o limite do clico da vida para todos os recursos contidos nele. Os grupos de recursos permitem que você gerencie todos os seus recursos em um aplicativo, coletivamente. 
</br>
Você pode ter vários planos de hospedagem na Web em um grupo de recursos e cada plano de hospedagem terá seu próprio conjunto de recursos e funções, que são utilizados pelos sites associados a esse plano.  A imagem a seguir ilustra essa relação:
</br>
</br>
![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)
</br>
</br>
A capacidade de ter vários planos de hospedagem na Web em um único grupo de recursos permite que você aloque sites diferentes para recursos diferentes, principalmente as máquinas virtuais executando seus sites. Por exemplo, essa capacidade permite a separação de recursos entre os sites de desenvolvimento, teste e produção, onde é conveniente para você alocar um plano de hospedagem Web com um conjunto próprio dedicado de recursos para seus sites de produção, além de um segundo plano de hospedagem na Web para seus sites de desenvolvimento e teste. 
</br>
Ter vários planos de hospedagem na Web em um único grupo de recursos também permite que você defina um aplicativo que abrange várias regiões. Por exemplo, um site altamente disponível executando em duas regiões incluirá dois planos de hospedagem na Web, um para cada região e um site associado a cada plano de hospedagem na Web. Em tal situação, todos os sites serão associados com um único grupo de recursos, que define um aplicativo.  Tendo uma visão única de um grupo de recursos com múltiplos planos de hospedagem na Web e vários sites faz com que seja fácil de gerenciar, controlar e exibir a integridade dos sites. Além do gerenciamento dos recursos de sites e respectivos sites para um determinado aplicativo, é possível associar qualquer recurso do Azure relacionado, como bancos de dados SQL e Projetos de equipe. 
</br>
##Quando devo criar um novo grupo de recursos e quando devo criar um novo plano de hospedagem na Web?##
</br>
Ao criar um novo site, deve-se considerar a criação de um novo grupo de recursos quando o site que você está prestes a criar representa um novo aplicativo Web. Nesse caso, criar um novo grupo de recursos, um plano de hospedagem na Web associado e um site é a escolha certa.  Ao criar um novo site através da nova visualização de portal do Azure, usando a galeria ou a nova opção de site + SQL, o portal será o padrão para criar um novo plano de grupo de recursos e de hospedagem para o seu novo site. No entanto, se for necessário, você pode substituir esses padrões.
</br>
</br>
![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
</br>
</br>
Você sempre pode adicionar um novo site ou outros recursos a um grupo de recursos existente. Ao criar um novo site do contexto de um grupo de recursos existente, o novo assistente do site padroniza para o plano de hospedagem na Web e recurso existente. Aqui também você pode substituir esses padrões conforme for necessário. Ao adicionar um novo site a um grupo de recursos existente, você pode optar por adicionar o site a uma plano de hospedagem na Web existente (essa é a opção padrão na nova visualização do portal do Azure), ou criar um novo plano de hospedagem na Web ao qual adicionar o site.
</br>
Criar um novo plano de hospedagem permite a você alocar um novo conjunto de recursos para seus sites e oferece maior controle sobre a alocação de recursos, já que cada plano de hospedagem na Web é criado com seu próprio conjunto de máquinas virtuais. Como é possível mover sites entre planos de hospedagem na Web, supondo que os planos de hospedagem na Web estão na mesma região, a decisão de se criar um novo plano de hospedagem na Web ou não tem pouca importância. Se um determinado site começa a consumir muitos recursos ou se você precisar separar alguns sites, poderá criar um nov plano de hospedagem na Web e mover seus sites para ele.
</br>
Se você deseja criar um novo site em uma região diferente e essa região não tem uma plano de hospedagem na Web existente, você terá que criar um novo plano de hospedagem na Web nessa região para poder ter um site associado a ele. 
</br>
Uma coisa importante para ter em mente é que você não pode mover planos de hospedagem na Web ou sites entre grupos de recursos. Também não é possível mover um site entre dois planos de hospedagem na Web que estão em duas regiões separadas. 
</br>
##Grupos de recursos existentes no portal de visualização do Azure##
</br>
Se você já possui sites existentes nos sites do Azure, notará que todos os seus sites aparecem no portal de visualização do Azure. Você pode ver todo o seu site como uma lista plana, clicando no botão **Procurar** no painel de navegação esquerdo e selecionando **Sites**:
</br>
</br>
![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)
</br>
</br>
Você também pode ver todos os grupos de recursos que foram criados para você clicando no botão **Procurar** no painel de navegação esquerdo e selecionando **Grupos de recursos**:
</br>
</br>
![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)
</br>
</br>
Você também observará que tem um grupo de recursos padrão gerado automaticamente em cada região onde você já tem sites. O nome do grupo de recursos gerado automaticamente para os sites é *Default-Web-<NOME DO LOCAL>*, onde nome do local representa uma região do Azure (por exemplo, *Default-Web-WestUS*). Em cada grupo de recursos você encontrará todos os seus sites existentes para a região do grupo. Cada site que você criou anteriormente e criará no futuro, seja no portal do Azure completo ou no Portal de visualização do Azure, estará disponível em ambos os portais. 
</br>
Uma vez que cada site deve ser associado a uma plano de hospedagem na Web, criamos os planos de hospedagem na Web padrão para os sites existentes de acordo com a seguinte convenção, em cada região:
</br>
* Todos os seus sites **Gratuitos** são associados a um plano de hospedagem na Web **Padrão** e sua faixa de preço é definida como **Gratuito**. 
</br>
* Todos os seus sites **Compartilhados** são associados a um plano de hospedagem na Web **Padrão** e sua faixa de preço é definida como **Compartilhado.**
</br>
* Todos os seus sites **Padrão** são associados a um plano de hospedagem na Web padrão e sua faixa de preço é definida como **Padrão**. 
</br>
O nome deste plano de hospedagem na Web é **DefaultServerFarm**. Esse nome foi escolhido para dar suporte a uma API herdada. O nome **ServerFarm** pode ser um pouco enganador, uma vez que ele se refere a um **Plano de hospedagem na Web**, mas é importante notar que ele é um nome de plano de hospedagem na Web e não uma entidade própria. 
</br>
##Perguntas frequentes sobre plano de hospedagem na Web##
</br>
**Questão**: Como crio um plano de hospedagem na Web?
</br>
**Resposta**: Um plano de hospedagem na Web é um contêiner e, como tal, não é possível criar um plano de hospedagem na Web vazio. No entanto, um novo plano de hospedagem na Web é criado explicitamente durante a criação do site.
</br>
Para fazer isso usando a interface do usuário na nova **visualização do Portal do Azure** clique em **NOVO** e selecione **Site**, o que abrirá a lâmina de criação de sites. Na primeira imagem abaixo, você pode ver o ícone **NOVO** na parte inferior esquerda, enquanto na segunda imagem você pode ver a lâmina de criação de **Site**, a lâmina de **Plano de Hospedagem na Web** blade e a lâmina de **Faixa de Preços**:
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)
</br>
</br>
![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)
</br>
</br>
Para este exemplo, estamos criando um novo site chamado **contosomarketing** e optando por colocá-lo no novo plano de hospedagem na Web chamado **contoso**. A faixa de preço selecionada para esse plano de hospedagem na Web é **Padrão pequeno**. Para obter mais detalhes sobre as faixas de preço de planos de hospedagem na Web, bem como os recursos, valores e opções de escala oferecidos em cada um, visite as [Especificações de planos de hospedagem na Web do Azure](http://go.microsoft.com/?linkid=9845586). 
</br>
Deve-se observar também que um plano de hospedagem na Web também pode ser criado no Portal do Azure existente. Isso é feito como parte do assistente de **criação rápida**, selecionando **Criar um novo plano de hospedagem na Web** a partir da lista suspensa **PLANO DE HOSPEDAGEM NA WEB**:
</br>
</br>
![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)
</br>
</br>
Para este exemplo estamos criando um novo site chamado **northwind** e optando por criar um novo plano de hospedagem na Web. O resultado dessa operação será um novo plano de hospedagem na Web chamado **default0** que contém o site **northwind**. Todos os planos de hospedagem na Web criados através dessa experiência seguem essa convenção de nomenclatura e os planos de hospedagem na Web não podem ser renomeados depois de criados. Além disso, planos de hospedagem na Web criados por meio desse processo serão criados na faixa de preço **Gratuito**.
</br>
**Questão**: Como atribuo um site a um **Plano de Hospedagem na Web**?
</br>
**Resposta**: Os sites são atribuídos a um plano de hospedagem na Web como parte do processo de criação de sites. Para fazer isso usando a interface do usuário na nova **visualização do Portal do Azure**, clique em **NOVO** e selecione **Site**:
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)
</br>
</br>
Em seguida, na lâmina de criação do site, selecione o plano de hospedagem:
</br>
</br>
![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)
</br>
</br>
Um site também pode ser criado em um plano específico de hospedagem na Web usando o Portal do Azure existente. Isso é feito como parte do assistente de **criação rápida**. Depois de digitar a URL do site, use a lista suspensa **PLANO DE HOSPEDAGEM NA WEB** para selecionar um plano ao qual adicionar o site:
</br>
</br>
![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)
</br>
</br>
**Questão**: Como movo um site para um Plano de Hospedagem na Web diferente? 
</br>
**Resposta**: Você pode mover um site para um plano diferente de hospedagem na Web usando o portal de visualização do Azure. Sites podem ser movidos entre planos de hospedagem na Web na mesma região geográfica, que pertencem ao mesmo grupo de recursos.
</br>
Para mover um site para outro plano, navegue até a lâmina do site que você deseja mover.  Em seguida, clique em **Plano de Hospedagem na Web**:
</br>
</br>
![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)
</br>
</br>
Isso abrirá a lâmina do plano de hospedagem na Web. Nesse ponto, você poderá pegar um plano de hospedagem na Web existente ou criar um novo. Planos em uma localização geográfica ou grupo de recursos diferente permanecem esmaecidos e não podem ser selecionados.
</br>
Observe que cada plano de hospedagem na Web tem sua própria faixa de preço. Se você mover um site de um plano de hospedagem na Web com faixa de preço **Gratuito** para outro plano com faixa de preço **Padrão**, seu site poderá aproveitar todos os recursos e funções da camada Padrão. 
</br>
</br>
**Questão**: Como dimensiono um plano de hospedagem na Web?
</br>
**Resposta**: Existem duas maneiras de dimensionar um plano de hospedagem na Web. Uma maneira é aumentar seu plano de hospedagem na Web e todos os sites associados a ele. Ao alterar a faixa de preço de um plano de hospedagem na Web, todos os sites desse plano estarão sujeitos aos recursos e funções definidos por essa faixa de preço. 
</br>
Na imagem abaixo é possível visualizar a lâmina **Plano de Hospedagem na Web**, bem como a lâmina **Faixa de Preço**. Clicar na parte da **Faixa de Preço** na lâmina de **Plano de Hospedagem na Web** expande a lâmina **Faixa de Preço**, na qual você pode alterar a faixa de preço para o plano de hospedagem na Web:
</br>
</br>
![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)
</br>
</br>
O segundo modo de dimensionar um plano é aumentando o número de instâncias no seu plano de hospedagem na Web. Na imagem abaixo é possível visualizar a lâmina do **Plano de hospedagem na Web**, bem como a lâmina de **Escala**. Clicar na área Escala, da lâmina **Plano de Hospedagem na Web**, expande essa área e permite a alteração da contagem de instâncias do plano:
</br>
</br>
![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
</br>
</br>
Já que o plano de hospedagem na Web na imagem acima está configurado para usar a faixa de preço **Padrão**, a opção **Escala automática** está habilitada. 
</br>
Executar isso no Portal do Azure completo pode ser feito na guia **Escala**, conforme mostrado abaixo:
</br>
</br>
![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
</br>
</br>
**Questão**: Como excluo um plano de hospedagem na Web?
</br>
**Resposta**: Para excluir um plano de hospedagem na Web você deve primeiro excluir todos os sites associados a ele. Depois que todos os sites em um plano de hospedagem na Web tiverem sido excluídos, esse plano de hospedagem na Web pode ser excluído da lâmina Plano de Hospedagem na Web:
</br>
</br>
![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
</br>
</br>
No Portal do Azure completo, excluir o último site em uma plano de hospedagem na Web excluirá automaticamente o plano de hospedagem na Web associado.
</br>
**Questão**: Como faço o monitoramento de um plano de hospedagem na Web?
</br>
**Resposta**: Planos de hospedagem na Web pode ser monitorados usando as partes de Monitoramento na lâmina Plano de Hospedagem na Web:
</br>
</br>
![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
</br>
</br>
Os controles de monitoramento podem ser personalizados clicando com botão direito no controle e selecionando **editar consulta**:
</br>
</br>
![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
</br>
</br>
As métricas expostas são:
</br>
* Porcentagem de CPU
</br>
* Porcentagem de Memória
</br>
* Tamanho da fila do disco 
</br>
* Tamanho da fila de HTTP. 
</br>
Essas métricas representam a média de uso entre instâncias pertencentes a um plano de hospedagem na Web. Todas essas métricas podem ser usadas para configurar alertas bem como regras de autoescala.
</br>
##Impressões e conclusões##
</br>
Os planos de hospedagem na Web representam um conjunto de recursos e habilidades que você pode compartilhar através de seus sites.  Um plano de hospedagem na Web oferece a flexibilidade para alocar sites específicos a um determinado conjunto de recursos, como máquinas virtuais, e otimiza ainda mais a alocação de seus recursos do Azure e o uso de sites. 

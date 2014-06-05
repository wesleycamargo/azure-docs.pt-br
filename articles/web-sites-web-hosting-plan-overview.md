<properties pageTitle="O que é um plano de hospedagem na Web?" description="Visão geral do plano de hospedagem na Web" title="O que é um plano de hospedagem na Web?" authors="adamab" />
# O que é um plano de hospedagem na Web? #

Os planos de hospedagem na Web representam um conjunto de recursos e a capacidade de compartilhamento entre os sites.  Os planos de hospedagem na Web dão suporte a algumas faixas de preço (por exemplo, Grátis, Compartilhado, Básico e Padrão) em que cada faixa tem seus próprios recursos.  Sites na mesma assinatura, grupo de recursos e localização geográfica podem compartilhar um plano de hospedagem na Web.

## Recursos em planos de hospedagem na Web ##

Cada faixa de preço (por exemplo, Grátis, Compartilhado, Básico e Padrão) tem seu próprio conjunto de recursos.  [Acesse aqui](http://go.microsoft.com/fwlink/?LinkID=394421) para obter as informações mais recentes de recursos e preços.

Aqui estão algumas dicas úteis sobre planos e recursos de hospedagem na Web:

- É possível alterar a faixa de preço do plano de hospedagem na Web a qualquer momento com tempo de inatividade zero.
- Sites na mesma assinatura, local e grupo de recursos podem todos compartilhar um plano de hospedagem na Web. 
- Recursos como a escala automática funcionam segmentando-se um plano de hospedagem na Web.  Se quiser usar a autoescala em sites individuais, você deverá dedicar um plano de hospedagem na Web para esse site.

## Planos de hospedagem na Web e capacidade ##

Os planos de hospedagem na Web das faixas Grátis e Compartilhado fornecem sites com uma infraestrutura compartilhada, o que significa que os sites compartilham recursos com sites de outros clientes.  

Os planos de hospedagem na Web das faixas Básico e Padrão fornecem sites com uma infraestrutura dedicada, o que significa que apenas o site ou os sites escolhidos por você para associação a esse plano serão executados nesses recursos.  Nessa faixa, é possível configurar o plano de hospedagem na Web para usar uma ou mais instâncias de máquinas virtuais.  Há suporte para instâncias pequenas, médias e grandes.  Essas máquinas virtuais são gerenciadas em seu nome, o que significa que você jamais precisará se preocupar com atualizações do sistema operacional ou algo parecido.  

Uma observação sobre a faixa Compartilhado (visualização).  Em todas as faixas, exceto "Compartilhado", você paga um preço pelo plano com base na faixa e na capacidade escolhida, não havendo cobrança adicional para cada site que usa o plano. Os planos de hospedagem na Web compartilhados são diferentes.  Devido à natureza da infraestrutura compartilhada, você é cobrado separadamente pelo site no plano.  

### Os planos de hospedagem na Web e o novo Portal de Visualização do Azure ###

O novo Portal de Visualização do Azure permite associar sites existentes ou novos a planos de hospedagem na Web.  Na verdade, todos os sites existentes foram atribuídos automaticamente ao planos de hospedagem na Web com base em sua assinatura, localização geográfica e faixa de preço atual.  

Durante a criação de um novo site, o portal perguntará a qual plano de hospedagem na Web associá-lo.  É possível criar um novo plano de hospedagem na Web ou selecione um plano existente.  Para usar um plano existente o novo site deve estar na mesma assinatura, no mesmo local geográfico e no mesmo grupo de recursos do plano existente.  Durante a criação de um novo site vazio, o Azure assumirá como padrão o último plano de hospedagem na Web para ajudar a usar melhor os planos já criados por você.  Durante a criação de um site com um banco de dados, a opção de reutilizar um plano existente não estará disponível.

É possível ver todos os planos de hospedagem na Web em todas as assinaturas usando-se o botão **Procurar** na barra de menus à esquerda e, em seguida, clique em **Tudo** na parte superior direita do painel de atividade exibido na tela.

![][BrowseEverythingScreenshot]
![][BrowseWebHostingPlansScreenshot]

Também é possível ver a qual plano de hospedagem na Web cada site está associado observando-se a representação gráfica do grupo de recursos exibido na parte superior do blade do site.

![][WebHostingPlanResourceMapScreenshot]

O clique no plano iniciará um blade que permite gerenciar o plano de hospedagem na Web.  [Saiba mais sobre como gerenciar planos de hospedagem na Web](http://go.microsoft.com/fwlink/?LinkID=394411)

![][WebHostingPlanBladeScreenshot]

<!-- Images. -->
[BrowseEverythingScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-everything.png
[WebHostingPlanResourceMapScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png
[WebHostingPlanBladeScreenshot]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-blade.png
[BrowseWebHostingPlansScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-web-hosting-plans.png


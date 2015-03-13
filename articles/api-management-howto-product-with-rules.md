<properties 
	pageTitle="Como criar e definir configurações avançadas no Gerenciamento de API do Azure" 
	description="Saiba como configurar um produto com a cota e as políticas de limite de taxa." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Como criar e definir configurações avançadas no Gerenciamento de API do Azure

No Gerenciamento de API do Azure (Visualização), os produtos são altamente configuráveis para atender aos resquisitos dos publicadores de API. Este tópico demonstra como definir algumas das configurações avançadas, incluindo limite de taxa e políticas de cota.

Neste tutorial você irá criar um produto de avaliação gratuita que permite fazer até 10 chamadas por minuto e até um máximo de 200 chamadas por semana, publicar e testar a política de limite de taxa.

## Neste tópico

-   [Criar um produto][Criar um produto]
-   [Adicionar uma API ao produto][Adicionar uma API ao produto]
-   [Configurar o limite de taxa de chamada e as políticas de cota][Configurar o limite de taxa de chamada e as políticas de cota]
-   [Publicar o produto][Publicar o produto]
-   [Inscrever uma conta de desenvolvedor no produto][Inscrever uma conta de desenvolvedor no produto]
-   [Chamar uma operação e testar a taxa de limite][Chamar uma operação e testar a taxa de limite]
-   [Próximas etapas][Próximas etapas]

## <a name="create-product"> </a>Criar um produto

Nesta etapa você criará um produto de avaliação gratuita que não requer aprovação de assinatura.

Para começar, clique em **Console de gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de Gerenciamento de API][Console de Gerenciamento de API]

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda para exibir a página **Produtos**.

![Adicionar produto][Adicionar produto]

Clique em **Adicionar Produto** para exibir a janela pop-uo **Adicionar novo produto**.

![Adicionar novo produto][Adicionar novo produto]

Digite **Avaliação Gratuita** na caixa de texto **Título**.

Digite **Assinantes poderão fazer 10 chamadas/minuto, no máximo 200 chamadas/semana e após isso o acesso será negado.** na caixa de texto **Descrição**.

Se deseja que um administrador revise e aceite ou rejeite as tentativas de assinatura para este produto, marque **Requerer aprovação de assinatura**. Se a caixa não estiver marcada, as tentativas de assinatura serão aprovadas automaticamente. Neste exemplo, as assinaturas são aprovadas automaticamente; por isso, não marque a caixa.

Após inserir todos os valores, clique em **Salvar** para criar o produto.

![Produto adicionado][Produto adicionado]

Por padrão, os novos produtos ficam visíveis para os usários no grupo **Administradores**. Vamos adicionar o grupo **Desenvolvedores**. **Clique em Avaliação Gratuita** e selecione a guia **Visibilidade**.

> No Gerenciamento de API, os grupos são usados para gerenciar a visibilidade dos produtos para desenvolvedores. Os produtos dão visibilidade aos grupos e os desenvolvedores podem exibir e assinar os produtos visíveis para os grupos aos quais pertencem. Para obter mais informações, consulte [Como criar e usar grupos no Gerenciamento de API do Azure][Como criar e usar grupos no Gerenciamento de API do Azure].

![Adicionar um grupo de desenvolvedores][Adicionar um grupo de desenvolvedores]

Marque o grupo **Desenvolvedores** e clique em **Salvar**.

## <a name="add-api"> </a>Adicionar uma API ao produto

Nesta etapa do tutorial você adicionará a API de ECO a uma produto de avaliação gratuita.

> Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de ECO que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. Para obter mais informações, consulte [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda, depois clique em **Avaliação Gratuita** para configurar o produto.

![Configurar produto][Configurar produto]

Clique em **Adicionar API ao produto**.

![Adicionar API ao produto][Adicionar API ao produto]

Marque a caixa ao lado de **API de Eco** e clique em **Salvar**.

![Adicionar API de Eco][Adicionar API de Eco]

## <a name="policies"> </a>Configurar o limite de taxa de chamada e as políticas de cota

Os limites de taxa e as cotas são configurados no editor de políticas. Clique em **Políticas** no menu **Gerenciamento de API** à esquerda e selecione **Avaliação Gratuita** na lista suspensa **Produto de escopo da política**.

![Política de produtos][Política de produtos]

Clique em **Adicionar política** para importar o modelo de política e começar a criar o limite de taxa e a política de cota.

![Adicionar política][Adicionar política]

Para inserir políticas, posicione o cursor na seção **entrada** ou **saída** do modelo da política. A taxa de limite e as políticas de cota são políticas de entrada; portanto, posicione o cursor no elemento de entrada.

![Editor de políticas][Editor de políticas]

As duas políticas que estamos adicionando neste tutorial são as políticas **Limitar taxa de chamada** e **Definir cota de uso**.

![Declarações de políticas][Declarações de políticas]

Após posicionar o cursor no elemento de política de **entrada**, clique na seta ao lado de **Limitar taxa de chamada** para inserir o modelo de política.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

A opção **Limitar taxa de chamada** pode ser usada no nível do produto e também nos níveis de nome de operação individual e da API. Neste tutorial, somente as políticas de nível de produto são usadas, por isso, exclua os elementos **api** e **operação** do elemento **limite de taxa**, conforme mostrado no exemplo a seguir.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

No produto de **Avaliação Gratuita**, a taxa máxima permitida é de 10 chamadas por minuto, por isso digite **10** como o valor do atributo chamadas e **60** para o atributo **período de renovação**.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Para configurar a política **Definir cota de uso**, posicione seu cursor imediatamente abaixo do novo elemento **limite de taxa** que acabou de adicionar dentro do elemento **entrada** e clique na seta à esquerda de **Definir cota de uso**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Como essa política também tem a finalidade de ficar no nível do produto, exclua os elementos de nome **api** e **operação**, conforme mostrado no exemplo a seguir.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

As cotas podem ser baseadas no número de chamadas por intervalo, largura de banda ou ambos. Neste tutorial, não estamos limitando com base na largura de banda, por isso, exclua o atributo **largura de banda**.

    <quota calls="number" renewal-period="seconds">
    </quota>

No produto de **Avaliação Gratuita**, a cota é de 200 chamadas por semana. Especifique **200** como o valor para o atributo chamadas e especifique **604800** como o valor do período de renovação.

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> Os intervalos de política são especificados em segundos. Para calcular o intervalo para uma semana, você pode multiplicar o número de dias (7) pelo número de horas em um dia (24), pelo número de minutos em uma hora (60), pelo número de segundos em um minuto (60). 7 \* 24 \* 60 \* 60 = 604800.

Ao finalizar a configuração da política, ela deve corresponder ao exemplo a seguir.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
        
    </inbound>
    <outbound>
        
        <base />
        
        </outbound>
    </policies>

Após configurar as políticas desejadas, clique em **Salvar**.

![Salvar política][Salvar política]

## <a name="publish-product"> </a> Publicar o produto

Agora que as APIs foram adicionadas e as políticas configuradas, o produto está pronto para ser usado pelos desenvolvedores. Antes que o produto possa ser usado pelos desenvolvedores, ele deve ser publicado. Clique em **Produtos** no menu **Gerenciamento de API** à esquerda, depois clique em **Avaliação Gratuita** para configurar o produto.

![Configurar produto][Configurar produto]

Clique em **Publicar** e depois clique em **Sim, publicar** para confirmar.

![Publicar produto][Publicar produto]

## <a name="subscribe-account"> </a>Inscrever uma conta de desenvolvedor no produto

Agora que o produto foi publicado, ele está disponível para inscrição e para ser usado pelos desenvolvedores.

> Os administradores de uma instância de Gerenciamento de API são inscritos automaticamente em cada produto. Nesta etapa do tutorial, inscreveremos um das contas de desenvolvedor não administrador no produto de avaliação gratuita. Se sua conta de desenvolvedor fizer parte da função Administradores, você pode prosseguir com esta etapa, embora já esteja inscrito.

Clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda e clique no nome de sua conta de desenvolvedor. Neste exemplo, estamos usando a conta de desenvolvedor **Clayton Gragg**.

![Configurar desenvolvedor][Configurar desenvolvedor]

Clique em **Adicionar assinatura**.

![Adicionar assinatura][Adicionar assinatura]

Marque a caixa ao lado de **Avaliação Gratuita** e clique em **Assinar**.

![Adicionar assinatura][1]

## <a name="test-rate-limit"> </a>Chamar uma operação e testar a taxa de limite

Agora que o produto de Avaliação Gratuita está configurado e publicado, podemos chamar algumas operações e testar a política da taxa de limite. Alterne para o portal do desenvolvedor clicando em **Portal do desenvolvedor** no menu superior à direita.

![Portal do desenvolvedor][Portal do desenvolvedor]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![Portal do desenvolvedor][2]

> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.

Selecione a operação **Recurso GET** e clique em **Abri Console**.

![Abrir console][Abrir console]

Mantenha os valores de parâmetros padrão e selecione a chave de assinatura para o produto de **Avaliação Gratuita**.

![Chave de assinatura][Chave de assinatura]

> Se você tem várias inscrições certifique-se de selecionar a chave de **Avaliação Gratuita** ou as políticas que foram configuradas nas etapas anteriores não entrarão em vigor.

Clique em **HTTP Get** e visualize a resposta. Observe o **Status de resposta** de **200 OK**.

![Resultados da operação][Resultados da operação]

Clique em **HTTP Get** a uma taxa maior que a política de limite de taxa de 10 chamadas por minuto. Uma vez excedida a política de limite de taxa, um status de resposta de **429 muitas solicitações** será retornado.

![Resultados da operação][3]

Os **Cabeçalhos de resposta** e o **Conteúdo da resposta** indicam o intervalo restante antes que as recuperações obtenham êxito.

Quando a política de limite de taxa de 10 chamadas por minuto estiver em vigor, as chamadas subsequentes falharão até que 60 segundos tenham se passado a partir da primeira das 10 chamadas com êxito para o produto, antes que o limite de taxa tenha sido excedido. Neste exemplo, o intervalo restante é de 43 segundos.

## <a name="next-steps"> </a>Próximas etapas

-   Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][Introdução à configuração avançada de API].

  [Criar um produto]: #create-product
  [Adicionar uma API ao produto]: #add-api
  [Configurar o limite de taxa de chamada e as políticas de cota]: #policies
  [Publicar o produto]: #publish-product
  [Inscrever uma conta de desenvolvedor no produto]: #subscribe-account
  [Chamar uma operação e testar a taxa de limite]: #test-rate-limit
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de Gerenciamento de API]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Adicionar produto]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Adicionar novo produto]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Produto adicionado]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [Como criar e usar grupos no Gerenciamento de API do Azure]: ../api-management-howto-create-groups
  [Adicionar um grupo de desenvolvedores]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Configurar produto]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [Adicionar API ao produto]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Adicionar API de Eco]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Política de produtos]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Adicionar política]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Editor de políticas]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Declarações de políticas]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Salvar política]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [Publicar produto]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Configurar desenvolvedor]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Adicionar assinatura]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [Portal do desenvolvedor]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Abrir console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Chave de assinatura]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Resultados da operação]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [Introdução à configuração avançada de API]: ../api-management-get-started-advanced

<!--HONumber=46--> 

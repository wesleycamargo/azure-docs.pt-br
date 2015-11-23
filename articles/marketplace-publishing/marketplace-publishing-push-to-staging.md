<properties
   pageTitle="Preparar e testar sua oferta para implantação no Azure Marketplace | Microsoft Azure"
   description="Instruções detalhadas sobre como fornecer conteúdo de marketing, configurar planos de preços e testar sua oferta antes de implantar o Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Concluir a criação de oferta com conteúdo de marketing
Nesta etapa do processo de publicação, será necessário fornecer determinados conteúdos de marketing e detalhes sobre a oferta e/ou SKUs no Azure Marketplace, como a descrição do seu produto, logotipos da empresa, planos de preços, detalhes de plantas e outras informações necessárias para enviar sua oferta e/ou SKU para o preparo. Essas informações são usadas como conteúdo de marketing em nosso Portal do Azure. Você começará esse processo no [Portal de Publicação][link-pubportal].

## Etapa 1. Fornecer conteúdo de marketing do Marketplace
**Inglês é o padrão e o único idioma com suporte;** certifique-se de que todas as informações fornecidas nos campos estejam em inglês. Todas as informações podem ser editadas em qualquer momento até você enviar por push para o preparo.

  1. Acesse o Portal de Publicação, [https://publish.windowsazure.com](https://publish.windowsazure.com)
  2. No menu à esquerda, clique na guia **Marketing**.
  3. No painel principal, clique no botão **Inglês (EUA)**.

### Detalhes
1. Insira o resumo, o resumo longo e uma descrição da oferta.
2.	Carregue imagens das especificações necessárias (mencionadas no Portal de Publicação) em formato PNG, uma para cada tamanho.

  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *Título, descrição e logotipos da oferta*

### Links
Na guia de links à esquerda da barra, insira quaisquer links com informações que possam ajudar os clientes. Insira um nome e uma URL para cada link.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Imagens de exemplo (opcionais)
> [AZURE.NOTE]Incluir uma imagem de exemplo é uma etapa opcional. É possível preencher o conteúdo de marketing para atender aos requisitos de envio por push para o preparo.

Na guia **Imagens de Exemplo** no menu à esquerda, carregue uma nova imagem clicando em **Carregar uma nova imagem**. Se você tiver uma imagem existente e deseja substituí-la, clique em **Substituir imagem**.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### Planos
![desenho](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Legal
Na guia Legal, forneça um link para as políticas/termos de uso. Insira ou cole os termos na caixa grande Termos de Uso.

> [AZURE.IMPORTANT]Todos os campos devem ter entradas, incluindo as imagens, a fim de poder mover para o preparo.


## Etapa 2: Definir seus preços
### Modelos de preços
|Modelo de preços |Descrição |
|---------------|------------------------------------------|
|Base| Taxa mensal fixa paga no momento da compra, por exemplo, US$10/mês|
|Consumo (também conhecido como Uso, Medidor) | Pagamento por uso, que é definido pelo editor da oferta. O excedente não pode ser definido por estação, por usuário, etc., pois não há nenhuma definição que estabelece uma fração de um usuário ou capacidade de cálculo pro rata. O uso é relatado pelo parceiro, por hora. O cliente paga no final do ciclo de cobrança mensal em vez de antecipadamente como é normalmente em planos mensais. |
|Avaliação gratuita | O cliente pode usar gratuitamente por um período limitado e, então, paga taxas normais posteriormente |
|Camada gratuita | O plano é sempre gratuito |
| Migração (também conhecida como conversão ou upgrade/downgrade) do plano | Definição que estabelece um usuário mudando de seu plano atual para outro plano aceitável; definido pelo parceiro |

**Modelos de preços disponíveis por tipo de oferta**

> [AZURE.IMPORTANT]A disponibilidade de alguns modelos de preços varia por tipo de oferta. Consulte a tabela abaixo.

| | Apenas base | Apenas consumo | Base + Consumo |
|---|---|---|---|
| Imagem de Máquina Virtual | Não | Sim | Não|
| Serviço do desenvolvedor | Sim | Sim | Sim |
| Serviço de dados | Sim | Não | Não |

### 2\.1. Definir seus preços da VM
> [AZURE.NOTE]Traga sua própria licença (BYOL) só tem suporte para máquinas virtuais.

1.	Na guia **Preços**, você verá todos os mercados com suporte. Escolha o mais adequado para abrir os campos de preços.
2.	O link fornecido no Portal de Publicação mostrará informações sobre preços para ajudá-lo na determinação dos preços de suas SKUs.
3.	Se a SKU é BYOL, marque a caixa de seleção para a disponibilidade "SKU licenciada externamente (BYOL)".
4.	Se a SKU for por hora, insira o preço do seu software. As SKUs sem preços não estarão disponíveis para compra ou uso.

  >[AZURE.NOTE]Se você tiver BYOL e SKUs por hora, então certifique-se de que os seguintes requisitos sejam atendidos: caixa de seleção marcada como BYOL e valores de preços selecionados como Por hora.

5.	Um assistente de preços será aberto; continue para preencher os preços, incluindo preços de outros países, se você optar por permitir compras de fora do seu mercado específico.
6.	Alguns países são países de remessa de ISV. Para vender em um país de remessa de ISV, você deve ser capaz de cobrar e recolher imposto sobre suas SKUs, e calcular e pagar imposto ao governo do país. A Microsoft não está em posição de prestar orientação jurídica ou fiscal. Consulte a seção ""Países de destino de venda" da Oferta" na introdução deste documento para obter mais informações sobre "Países de destino de venda".

### 2\.2. Definir seus preços de serviço do desenvolvedor
Planos podem ser qualquer combinação de Base + consumo, em que BASE é o preço mensal e Excedente é o preço de pagamento por uso (consulte abaixo para obter mais detalhes)

**Exemplo:** Oferta de serviço do desenvolvedor da Contoso

| Plano | Preço | Inclui | Caminho de migração |
|-------|------|-------|-------|
|Grátis|US$0/mês|Funcionalidade básica|Pode migrar para qualquer outro plano|
|Bronze|US $10/ mês|Funcionalidade básica e uma cota de 1.000 do recurso X|Pode migrar para os planos Bronze Plus, Silver e Gold|
|Bronze Plus|Período de avaliação gratuita: US$0/mês + US$0/meter01 |Funcionalidade básica e uma cota de 10.000 do recurso X. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01.|Pode migrar para os planos Silver Plus e Gold|
|Bronze Plus| Período pago (também conhecido como avaliação gratuita expirada): US$10/mês + US$ 0,05/meter01|Funcionalidade básica e uma cota de 10.000 do recurso X. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01.|Pode migrar para os planos Silver Plus e Gold|
|Silver|US$ 0,15/meter01|O cliente pode pagar por uso via meter01, que é o recurso X|Pode migrar para os planos Bronze e Gold|
|Silver Plus|US$20/mês + US$ 0,15/meter01 + US$ 0,01/meter02|Funcionalidade básica e uma cota de 10.000 do recurso X e 100 do recurso Y. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01. Depois que a cota do recurso Y for usada, o cliente pode pagar por uso via meter02|Pode migrar para os planos Bronze Plus e Gold|
|Gold|US$1.000/mês|Cota de 10.000 de recurso X, 1.000 do recurso Y e ilimitada do recurso Z|Pode migrar para todos os planos exceto livre|

## Etapa 3: Fornecer informações de suporte
Algumas dessas informações já estarão preenchidas durante a etapa de certificação. Você pode adicionar ou editar informações como nas etapas abaixo. Os detalhes de contato são usados apenas para comunicações internas entre o parceiro e Microsoft. O URL de suporte estará disponível para os clientes finais.

1.	Vá para o cabeçalho de Suporte no lado esquerdo do Portal de Publicação.
2.	Insira as informações em Contato de Engenharia.
3.	Insira as informações em Suporte ao Cliente. Se você só oferece suporte por email, digite um número de telefone fictício, e seu email fornecido será utilizado no lugar.
4.	Insira a URL de suporte

## Etapa 4: Escolher categorias do Azure Marketplace
Na guia **Categorias**, haverá uma série de seleções fornecidas. Sua oferta pode estar entre elas e você pode selecionar até cinco (5) categorias.

## Como seu marketing aparecerá
Abaixo está a exibição detalhada de como os detalhes de marketing de ofertas do Portal do Editor são usados no [site do Azure Marketplace](http://azure.microsoft.com/marketplace) e [na galeria do Portal de visualização do Azure Marketplace](https://ms.portal.azure.com).

### No site Azure.com Marketplace
![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Listagem de ofertas no site do Azure.com Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Detalhes da descrição da oferta no site Azure.com Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Detalhes de preços da descrição da oferta no site Azure.com Marketplace*

### Na galeria do Portal de Visualização do Azure Marketplace
![desenho](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Listagem de ofertas na galeria do Portal de Visualização do Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

![desenho](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Detalhes da descrição de ofertas na galeria do Portal de Visualização do Azure Marketplace*

## Próximas etapas
Agora que o conteúdo do Marketplace está carregado, seguiremos para a **Etapa 3: Testar sua oferta no Preparo**. No entanto, você deve selecionar o tipo de oferta apropriado na lista abaixo já que as etapas variam por tipo de oferta.

|| Imagem de máquina virtual | Serviço do desenvolvedor | Serviço de dados | Modelo de solução | |---|---|---|---|---| | **Etapa 3. Envie sua oferta para Preparo**| [Teste sua oferta de VM no Preparo](marketplace-publishing-vm-image-test-in-staging.md) | Teste sua oferta de serviço do desenvolvedor no Preparo| [Teste seu modelo de solução no Preparo](marketplace-publishing-solution-template-test-in-staging.md) |

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=Nov15_HO3-->
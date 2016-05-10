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
   ms.date="04/25/2016"
   ms.author="hascipio"/>

# Concluir a criação de oferta com conteúdo de marketing
Nesta etapa do processo de publicação, você precisará de fornecer determinados conteúdos de marketing e detalhes sobre a sua oferta e/ou SKUs no Azure Marketplace. Por exemplo, você irá fornecer uma descrição do seu produto, logotipos da empresa, planos de preços, detalhes de planos e outras informações necessárias para enviar sua oferta de e/ou SKU para preparação. Essas informações são usadas como conteúdo de marketing no portal do Azure. Você começará esse processo no [portal de publicação][link-pubportal].

## Passo 1: fornecer conteúdo de marketing do Marketplace
**O inglês é o idioma padrão e o único suportado.** Verifique se todas as informações fornecidas nos campos se encontram em inglês. Todas as informações podem ser editadas em qualquer momento até você enviar por push para o preparo.

  1. Acesse o portal de publicação, [https://publish.windowsazure.com](https://publish.windowsazure.com).
  2. No menu à esquerda, clique na guia **Marketing**.
  3. No painel principal, clique no botão **Inglês (EUA)**.

  > [AZURE.IMPORTANT] Todos os campos devem ter entradas, incluindo as imagens, a fim de poder mover para o preparo.

### Detalhes
1. Insira o título da oferta (máximo de 50 caracteres), resumo da oferta (máximo de 100 caracteres), resumo longo da oferta (máximo de 256 caracteres), descrição da oferta (máximo de 1300 caracteres) e logotipos na guia **Detalhes**
2. Insira o título da SKU (máximo de 50 caracteres), resumo da SKU (máximo de 100 caracteres) e descrição da SKU (máximo de 2000 caracteres) na guia **Planos**
3. Não insira texto duplicado nas descrições da oferta e da SKU.
4. Não insira texto duplicado no título da SKU e resumo longo da oferta.
5. Não insira texto duplicado no título da SKU e resumo da oferta.
6. Carregue imagens com as especificações necessárias (mencionadas no Portal de Publicação) no formato PNG e uma para cada tamanho.
7. Verifique se os logotipos seguem as diretrizes de logotipo do Azure Marketplace mencionadas a seguir.

  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Diretrizes de Logotipo do Azure Marketplace**

Todos os logotipos carregados no Portal de Publicação devem seguir as diretrizes abaixo:

- O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores principais e secundárias em logotipos.
- Logotipos não devem ser colocados em uma tela de fundo branca. Recomendamos usar cores primárias simples ou planos de fundo transparentes.
- Não use uma tela de fundo gradiente no logotipo.
- Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo.
- A aparência do seu logotipo deve ser “simples” e deve evitar usar gradientes.
- O logotipo não deve ser estendido.
- O logotipo pequeno deve ter o tamanho de 40 X 40 px
- O logotipo médio deve ter o tamanho de 90 X 90 px
- O logotipo grande deve ter o tamanho de 115 X 115 px
- O logotipo largo deve ter o tamanho de 255 X 115 px
- O logotipo Hero deve ter o tamanho de 815 X 290 px

  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Diretrizes adicionais para a faixa de logotipo Hero:**

- O logotipo Hero é opcional. Você pode optar por não carregar um logotipo Hero.
- O nome de exibição do editor, título da SKU, resumo longo da oferta e o botão de criação são inseridos automaticamente no logotipo Hero depois que a oferta fica ativa. Por isso não é necessário inseri-los ao desenhar o logotipo Hero.
- Como o Nome de Exibição do Editor, título da SKU e o resumo longo da oferta são exibidos na cor branca, você deve evitar manter a tela de fundo do ícone Hero em branco ou qualquer cor clara.
- Você deve deixar espaço para o texto acima na parte superior do ícone Hero. O espaço para o texto é 415 x 100 e ele é desviado em 370 px da esquerda.

  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### Links
Na guia de **Links** à esquerda da barra, insira quaisquer links com informações que possam ajudar os clientes. Insira um nome e uma URL para cada link.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### Imagens de exemplo (opcionais)
> [AZURE.NOTE] Incluir uma imagem de exemplo é uma etapa opcional. É possível preencher o conteúdo de marketing para atender aos requisitos de envio por push para o preparo.

Na guia **Imagens de Exemplo** no menu à esquerda, carregue uma nova imagem clicando em **Carregar uma nova imagem**. Se você tiver uma imagem existente e deseja substituí-la, clique em **Substituir imagem**.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### Legal
Na guia **Legal**, forneça um link para as políticas/termos de uso. Insira ou cole os termos na caixa grande **Termos de Uso**. O limite de caracteres para os termos legais de uso é 1.000.000 caracteres.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

> [AZURE.NOTE] Para máquinas virtuais, não é possível alterar o seguinte após um SKU ser preparado ou ficar ativo: **Identificador da oferta**, **Identificador de editor** e **Identificador de SKU**.

## Etapa 2: Definir seus preços
### Modelos de preços
|Modelo de preços |Descrição |
|---------------|------------------------------------------|
|Base| Taxa mensal fixa paga no momento da compra, por exemplo, US$10/mês.|
|Consumo (também conhecido como uso, medidor) | Pagamento por uso, que é definido pelo editor da oferta. O excedente não pode ser definido por estação, por usuário, etc., pois não há nenhuma definição que estabelece uma fração de um usuário ou capacidade de cálculo pro rata. O uso é relatado pelo parceiro, por hora. O cliente paga no final do ciclo de cobrança mensal em vez de antecipadamente como é normalmente em planos mensais. |
|Avaliação gratuita | O cliente pode usar gratuitamente por um período limitado e, então, paga taxas normais posteriormente. |
|Camada gratuita | O plano é sempre gratuito. |
| Migração (também conhecida como conversão ou upgrade/downgrade) do plano | Definição que estabelece um usuário mudando de seu plano atual para outro plano aceitável; definido pelo parceiro. |

**Modelos de preços disponíveis por tipo de oferta**

> [AZURE.IMPORTANT] A disponibilidade de alguns modelos de preços varia por tipo de oferta. Consulte a tabela abaixo.

| | Apenas base | Apenas consumo | Base + Consumo |
|---|---|---|---|
| Imagem de máquina virtual | Não | Sim | Não|
| Serviço do desenvolvedor | Sim | Sim | Sim |
| Serviço de dados | Sim | Não | Não |

### 2\.1. Definir seus preços da VM
> [AZURE.NOTE] Traga sua própria licença (BYOL) só tem suporte para máquinas virtuais.

1.	Na guia **Preços**, você verá todos os mercados com suporte. Escolha o mais adequado para abrir os campos de preços.
2.	O link fornecido no portal de publicação mostrará informações sobre preços para ajudá-lo na determinação dos preços de suas SKUs.
3.	Se a SKU é BYOL, marque a caixa de seleção para a disponibilidade SKU licenciada externamente (BYOL).
4.	Se a SKU for por hora, insira o preço do seu software. As SKUs sem preços não estarão disponíveis para compra ou uso.

  > [AZURE.NOTE] Se você tiver BYOL e SKUs por hora, então certifique-se de que os seguintes requisitos sejam atendidos: caixa de seleção marcada como BYOL e valores de preços selecionados como por hora.

5.	Será aberto um assistente de preço. Continue para preencher os preços, incluindo preços de outros países, se você optar por permitir compras de fora do seu mercado específico.
6.	Alguns países são países de remessa de ISV. Para vender em um país de remessa de ISV, você deve ser capaz de cobrar e recolher imposto sobre suas SKUs, e calcular e pagar imposto ao governo do país. A Microsoft não está em posição de prestar orientação jurídica ou fiscal. Consulte a seção "Países de destino de venda da oferta" na introdução deste documento para obter mais informações sobre os países de destino de venda.

  > [AZURE.NOTE] Para as máquinas virtuais, não é possível alterar o seguinte após um SKU ficar ativo, pois isso afeta a cobrança de clientes existentes: **Mudança de preço**, **Mudança no modelo de cobrança** e **Remoção de regiões de cobrança**.

### 2\.2. Definir seus Preços de serviço do desenvolvedor
Os planos podem ser qualquer combinação de base + consumo, em que a base é o preço mensal e o excedente é o preço de pagamento por uso. (Consulte a seguir para obter mais detalhes.)

**Exemplo:** oferta de serviço do desenvolvedor da Contoso

| Plano | Preço | Inclui | Caminho de migração |
|-------|------|-------|-------|
|Grátis|US$0/mês|Funcionalidade básica.|Pode migrar para qualquer outro plano|
|Bronze|US $10/ mês|Funcionalidade básica e uma cota de 1.000 do recurso X.|Pode migrar para os planos Bronze Plus, Silver e Gold|
|Bronze Plus|Período de avaliação gratuita: US$0/mês + US$0/meter01 |Funcionalidade básica e uma cota de 10.000 do recurso X. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01.|Pode migrar para os planos Silver Plus e Gold|
|Bronze Plus| Período pago (também conhecido como avaliação gratuita expirada): US$10/mês + US$ 0,05/meter01|Funcionalidade básica e uma cota de 10.000 do recurso X. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01.|Pode migrar para os planos Silver Plus e Gold|
|Silver|US$ 0,15/meter01|O cliente pode pagar por uso via meter01, que é o recurso X.|Pode migrar para os planos Bronze e Gold|
|Silver Plus|US$20/mês + US$ 0,15/meter01 + US$ 0,01/meter02|Funcionalidade básica e uma cota de 10.000 do recurso X e 100 do recurso Y. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01. Depois que a cota do recurso Y for usada, o cliente pode pagar por uso via meter02.|Pode migrar para os planos Bronze Plus e Gold|
|Gold|US $ 1.000/mês|Cota de 10.000 de recurso X, 1.000 do recurso Y e ilimitada do recurso Z.|Pode migrar para todos os planos exceto livre|

## Etapa 3: Fornecer informações de suporte
Os detalhes de contato são usados apenas para comunicações internas entre o parceiro e Microsoft. O URL de suporte estará disponível para os clientes finais.

1.	Vá para o cabeçalho de **Suporte** no lado esquerdo do portal de publicação.
2.	Insira as informações em **Contato de Engenharia**.
3.	Insira as informações em **Suporte ao Cliente**. Se você só oferece suporte por email, digite um número de telefone fictício, e seu email fornecido será utilizado no lugar.
4.	Insira a URL de suporte.

## Etapa 4: Escolher categorias do Azure Marketplace
A guia **Categorias** fornece uma matriz de seleções. Sua oferta pode estar entre elas e você pode selecionar até cinco categorias.

## Como seu marketing aparecerá
Abaixo está uma exibição detalhada de como a oferta de informações de marketing é usada no [site do Azure Marketplace](https://azure.microsoft.com/marketplace/) e no [Portal do Azure](https://portal.azure.com).

### Site do Azure Marketplace
![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Listagem de ofertas no site do Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Detalhes da descrição da oferta no site Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Detalhes de preços da descrição da oferta no site Azure Marketplace*

### Portal do Azure
![desenho](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Listagem de ofertas no Portal do Azure*

![desenho](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Detalhes da descrição da oferta no Portal do Azure*

## Próximas etapas
Agora que o conteúdo do Marketplace está carregado, seguiremos para a testar sua oferta no preparo. No entanto, você deve selecionar o tipo de oferta apropriado na lista abaixo já que as etapas variam por tipo de oferta.

||Imagem de máquina virtual |Serviço do desenvolvedor | Serviço de dados | Modelo de solução |
|----|----|----|----|----|
| **Etapa 3. Enviar por push sua oferta para preparo** | [Testar sua oferta de VM em preparo](marketplace-publishing-vm-image-test-in-staging.md) | Visualização particular | [Testar sua oferta de serviço de dados em preparo](marketplace-publishing-data-service-test-in-staging.md) | [Testar o modelo de solução em preparo](marketplace-publishing-solution-template-test-in-staging.md) |

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0427_2016-->
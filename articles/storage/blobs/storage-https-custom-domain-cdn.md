---
title: Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS
description: Aprenda a integrar a CDN do Azure com o armazenamento de Blobs para acessar blobs com domínios personalizados por HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 110d2d6db4d87294b56b940a02a6633af0d6f482
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219232"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS

A CDN (Rede de Distribuição de Conteúdo) do Azure agora dá suporte a HTTPS para nomes de domínio personalizados. Com a CDN do Azure, você pode acessar blobs usando seu nome de domínio personalizado por HTTPS. Para fazer isso, habilite a CDN do Azure em seu ponto de extremidade de blob ou web e mapeie a CDN do Azure para um nome de domínio personalizado. Após terminar, o Azure simplifica a habilitação de HTTPS para seu domínio personalizado por meio de acesso com um clique e gerenciamento de certificado completo. Não há nenhum aumento nos preços normais da CDN do Azure.

A CDN do Azure ajuda a garantir a privacidade e a integridade dos dados de aplicativos Web em trânsito. Ao usar o protocolo SSL para assegurar o tráfego via HTTPS, a CDN do Azure mantém seus dados criptografados quando enviados pela Internet. O uso de HTTPS com a CDN do Azure ajuda a proteger seus aplicativos web contra ataques.

> [!NOTE]  
> Além de dar suporte a SSL para nomes de domínio personalizado, a CDN do Azure pode ajudá-lo a dimensionar seu aplicativo para fornecer conteúdo de alta largura de banda em todo o mundo. Para saber mais, confira [Visão geral da CDN do Azure](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Início rápido

Para habilitar HTTPS do ponto de extremidade do armazenamento de Blobs personalizado, faça o seguinte:

1.  [Integrar uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Este artigo guia você pela criação de uma conta de armazenamento no portal do Azure, se você ainda não fez isso.

    > [!NOTE]  
    > Para adicionar seu ponto de extremidade Web de armazenamento durante a visualização do suporte a sites estáticos no Armazenamento do Microsoft Azure, selecione **Origem personalizada** no menu suspenso **Tipo de origem**. No portal do Azure, você precisa fazer isso em seu perfil da CDN do Azure em vez de diretamente na sua conta de armazenamento.

2.  [Mapeie conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Habilite HTTPS em um domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado

Por padrão, os pontos de extremidade de armazenamento de Blobs não permitem acesso de leitura anônimo. Se o ponto de extremidade do armazenamento de Blobs estiver configurado para não permitir o acesso anônimo de leitura, forneça um token de [assinatura de acesso compartilhado](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) em cada solicitação que fizer ao domínio personalizado. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](storage-manage-access-to-resources.md).

A CDN do Azure não respeita nenhuma restrição adicionada ao token de assinatura de acesso compartilhado. Por exemplo, todos os tokens de assinatura de acesso compartilhado expiram. Você ainda pode acessar o conteúdo com uma assinatura de acesso compartilhado expirada até que esse conteúdo seja limpo dos nós de borda da CDN do Azure. Você pode controlar por quanto tempo os dados são armazenados em cache na CDN do Azure definindo o cabeçalho de resposta do cache. Para saber como, consulte [Gerenciar a expiração dos blobs de Armazenamento do Microsoft Azure na CDN do Azure](../../cdn/cdn-manage-expiration-of-blob-content.md).

Se você criar duas ou mais URLs de assinatura de acesso compartilhado para o mesmo ponto de extremidade de blob, é recomendável ativar o caching de cadeias de consulta da CDN do Azure. Essa ação garante que o Azure trate cada URL como entidade exclusiva. Para obter mais informações, confira [controle do comportamento do armazenamento em cache do CDN do Azure com cadeias de caracteres de consulta](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirecionamento de HTTP para HTTPS

Você pode redirecionar o tráfego de HTTP para HTTPS. Isso requer o uso da oferta do Azure CDN Premium da Verizon. [Substitua o comportamento HTTP com o mecanismo de regras da CDN do Azure](../../cdn/cdn-rules-engine.md) aplicando a seguinte regra:

![Regra de redirecionamento de HTTP para HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Cdn-endpoint-name*, selecionado na lista suspensa, refere-se ao nome que você configurou para o ponto de extremidade da CDN do Azure. *Origin-path* refere-se ao caminho dentro de sua conta de armazenamento de origem em que o conteúdo estático é armazenado. Se você estiver hospedando todo o conteúdo estático em um único contêiner, substitua *origin-path* pelo nome do contêiner.

Para informações mais aprofundadas sobre regras, consulte os [Recursos do mecanismo de regras da CDN do Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Preços e cobrança

Quando você acessa blobs por meio de CDN do Azure, você paga [preços de Armazenamento de Blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) para o tráfego entre os nós de borda e a origem (Armazenamento de Blobs). Você paga [preços de CDN](https://azure.microsoft.com/pricing/details/cdn/) para dados acessados por meio dos nós de borda.

Por exemplo, digamos que você tenha uma conta de armazenamento no Oeste dos EUA que acessa usando uma CDN do Azure. Quando alguém no Reino Unido tenta acessar um blob na conta de armazenamento por meio da CDN do Azure, o Azure verifica primeiro o blob no nó de borda mais próximo do Reino Unido. Se o Azure encontra o blob, ele acessa uma cópia e usa o preço de CDN do Azure, porque a CDN do Azure está acessando-o. Se o Azure não encontra o blob, ele copia o blob para o nó de borda. Essa ação resulta em encargos de saída e de transação, conforme especificado no preço do armazenamento de Blob. O Azure, em seguida, acessa o arquivo no nó de borda, o que resulta na cobrança da CDN do Azure.

Na [página de preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/), o suporte ao protocolo HTTPS para nomes de domínio personalizado está disponível para a CDN do Azure apenas dos produtos Verizon Standard e Premium.

## <a name="next-steps"></a>Próximas etapas

* [Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs](storage-custom-domain-name.md)
* [Hospedagem de site estático no Armazenamento do Microsoft Azure (versão prévia)](storage-blob-static-website.md)

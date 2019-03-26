---
title: Hospedagem de site estático no Armazenamento do Microsoft Azure
description: O Armazenamento do Microsoft Azure agora oferece um site estático hospedand, fornecendo uma solução econômica e dimensionável para hospedar aplicativos web modernos.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a72182091721dd6a1104cb8e3495aee1a3b25eb8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439372"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hospedagem de site estático no Armazenamento do Microsoft Azure
As contas de GPv2 de Armazenamento do Microsoft Azure permitem que você forneça conteúdo estático (HTML, CSS, JavaScript e arquivos de imagem) diretamente de um contêiner de armazenamento denominado *$web*. Aproveitar a hospedagem no Armazenamento do Microsoft Azure permite que você use arquiteturas sem servidor incluindo [Azure Functions](/azure/azure-functions/functions-overview) e outros serviços de PaaS.

Em contraste com a hospedagem de site estático, sites dinâmicos que dependem do código do lado do servidor que melhor são hospedados usando o [Serviço de Aplicativo do Azure](/azure/app-service/overview).

## <a name="how-does-it-work"></a>Como ele funciona?
Quando você habilita o site estático hospedando em sua conta de armazenamento, selecione o nome do arquivo padrão e, opcionalmente, forneça um caminho para uma página 404 personalizada. Conforme o recurso é habilitado, um contêiner denominado *$web* será criado se ele ainda não existir.

Os arquivos no contêiner *$web* são:

- atendidos por meio de solicitações de acesso anônimo
- disponível somente por meio de operações de leitura do objeto
- diferencia maiúsculas de minúsculas
- disponível na web pública seguindo esse padrão:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- disponível por meio de um ponto de extremidade de Armazenamento de Blobs seguindo esse padrão:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Você pode usar o ponto de extremidade de Armazenamento de Blobs para carregar arquivos. Por exemplo, o arquivo carregado nesse local:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

está disponível no navegador em um local como este:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

O nome do arquivo padrão selecionado é usado na raiz e todos os subdiretórios, quando não for fornecido um nome de arquivo. Se o servidor retornar um erro 404 e você não fornecer um caminho de documento de erro, uma página 404 padrão é retornada ao usuário.

> [!NOTE]
> O nível de acesso público padrão para arquivos é privado. Porque os arquivos são atendidos por meio de solicitações de acesso anônimo, essa configuração será ignorada. Não há acesso público a todos os arquivos e permissões de RBAC serão ignoradas.

## <a name="cdn-and-ssl-support"></a>Suporte CDN e SSL

Para acessar seu site hospedado em um nome de domínio personalizado por HTTPS, consulte [ Usando a Rede de Distribuição de Conteúdo do Microsoft Azure com domínios personalizados por HTTPS ](storage-https-custom-domain-cdn.md). Como parte desse processo, você precisará *apontar seu CDN para o ponto de extremidade da web* em vez do ponto de extremidade do Blobs. Talvez você precise aguardar alguns minutos antes que seu conteúdo fique visível, uma vez que a configuração de CDN não é executada imediatamente.

Quando você atualiza seu site estático, certifique-se de limpar o conteúdo armazenado em cache nos servidores de borda da CDN com uma limpeza de ponto de extremidade CDN. Para obter mais informações, confira [Como limpar um ponto de extremidade do CDN do Azure](../../cdn/cdn-purge-endpoint.md).

## <a name="custom-domain-names"></a>Nomes de domínio personalizados

Você pode [configurar um nome de domínio personalizado para sua conta de Armazenamento do Microsoft Azure](storage-custom-domain-name.md) para disponibilizar seu site estático por meio de um domínio personalizado. Para uma visão detalhada na hospedagem do seu domínio no [Azure, consulte Hospeder seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Preços
Hospedagem de website estático é fornecida sem nenhum custo adicional. Para obter mais detalhes sobre preços para Armazenamento de Blob do Azure, confira [Página de Preços do Armazenamento de Blob do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Início rápido

### <a name="azure-portal"></a>Portal do Azure
Comece abrindo o portal do Azure em https://portal.azure.com e execute as etapas a seguir em sua conta de armazenamento de GPv2:

1. Clique em **Configurações**
2. Clique em **Website estático**
3. Digite um *nome do documento de índice*. (Um valor comum é *index. HTML)*
4. Opcionalmente, insira um *caminho de documento de erro* para uma página 404 personalizada. (Um valor comum é *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Em seguida, carregue seus ativos para o contêiner *$web* por meio do portal do Azure ou com o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar diretórios inteiros. Certifique-se de incluir um arquivo que corresponde ao *nome do documento de índice* que você selecionou durante a habilitação do recurso.

Finalmente, navegue até seu ponto de extremidade de web para testar seu website.

### <a name="azure-cli"></a>CLI do Azure
Instale a extensão de versão prévia de armazenamento:

```azurecli-interactive
az extension add --name storage-preview
```
No caso de várias assinaturas, defina sua CLI para a assinatura da conta de armazenamento de GPv2 que deseja habilitar:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Habilitar o recurso. Certifique-se de substituir todos os valores de espaço reservado, incluindo os colchetes, com seus próprios valores:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Consulta para a URL do ponto de extremidade da Web:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Carregar objetos para o contêiner *$web* de um diretório de origem. Ignore adequadamente a referência para o contêiner *$web* no comando. Por exemplo, se você estiver usando a CLI do Azure do CloudShell no portal do Azure, ignore o contêiner *$web* conforme mostrado:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Implantação

Métodos disponíveis para a implantação de conteúdo em um contêiner de armazenamento incluem o seguinte:

- [AzCopy](../common/storage-use-azcopy.md)
- [Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
- [Extensão do Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

Em todos os casos, certifique-se de copiar os arquivos para o contêiner *$web*.

## <a name="metrics"></a>Métricas

Para habilitar as métricas em suas páginas do site estático, clique em **Configurações** > **Monitoramento** > **Métricas**.

Dados de métricas são gerados pendurando em diferentes APIs de métrica. O portal exibe apenas membros da API usados em um determinado período de tempo para se concentrar apenas em membros que retornam dados. Para garantir que você possa selecionar o membro de API necessário, a primeira etapa é expandir o intervalo de tempo.

Clique no botão de período de tempo e selecione **Útimas 24 horas** e, em seguida, clique em **Aplicar**

![Intervalo de tempo de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Em seguida, selecione o **Blob** da lista suspensa *Namespace*.

![Namespace de métricas de sites estático do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Em seguida, selecione a métrica **Egresso**.

![Métrica de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Selecione **Somar** do seletor *Agregação*.

![Agregação de métricas de sites estáticos de Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Em seguida, clique no botão **Adicionar filtro** e escolha **Nome da API** do seletor *Propriedade*.

![Nome da API de métricas de sies estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Por fim, a caixa de seleção ao lado de **GetWebContent** no seletor *Valores* para preencher o relatório de métricas.

![O GetWebContent de métricas de websites estáticos do Armazenamento do Microsoft Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Depois da habilitação, as estatísticas de tráfego nos arquivos no contêiner *$web* são relatadas no painel de métricas.

## <a name="faq"></a>Perguntas frequentes

**O recurso de sites estáticos está disponível para todos os tipos de conta de armazenamento?**  
Não, hospedagem de websites estáticos está apenas disponível em contas de armazenamento padrão GPv2.

**As regras VNET e de firewall do Storage são suportadas no novo ponto de extremidade da Web?**  
Sim, o novo endpoint da web obedece às regras de VNET e firewall configuradas para a conta de armazenamento.

**O ponto de extremidade da web diferencia maiusculas de minúsculas?**  
Sim, o ponto de extremidade da Web diferencia maiúsculas e minúsculas exatamente como o ponto de extremidade do Blob. 

## <a name="next-steps"></a>Próximas etapas
* [Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para seu ponto de extremidade de Blobs ou web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Serviço de Aplicativo do Azure](/azure/app-service/overview)
* [Compile seu primeiro aplicativo web sem servidor](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Tutorial: Hospede seu domínio no DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)

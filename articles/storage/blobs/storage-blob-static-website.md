---
title: Hospedagem de site estático no armazenamento do Azure (visualização) | Microsoft Docs
description: Armazenamento do Azure agora oferece um site estático hospedando (visualização), fornecendo uma solução econômica e dimensionável para hospedar aplicativos web modernos.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 08/17/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 65a1cd85baf18ac1f0d193e7e6d6c3139919fb59
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617390"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Hospedagem de site estático no armazenamento do Azure (visualização)
Armazenamento do Azure agora oferece um site estático hospedando (visualização), fornecendo uma solução econômica e dimensionável para hospedar aplicativos web modernos. Em um site estático, páginas da Web contém conteúdo estático e o JavaScript ou outro código do lado do cliente. Por outro lado, os sites dinâmicos dependem de código do lado do servidor e podem ser hospedados usando [aplicativos Web do Azure](/azure/app-service/app-service-web-overview).

À medida que implantações desloca para modelos Elásticos, econômicos, a capacidade de fornecer conteúdo da web sem a necessidade de gerenciamento de servidor é essencial. A introdução de hospedagem de site estática no Armazenamento do Azure torna isso possível, permitindo recursos avançados de back-end com arquiteturas sem servidor, aproveitando [ Funções do Azure ](/azure/azure-functions/functions-overview) e outros serviços de PaaS.

## <a name="how-does-it-work"></a>Como ele funciona?
Quando você ativa sites estáticos em sua conta de armazenamento, um novo ponto de extremidade de serviço da Web é criado no formulário `<account-name>.<zone-name>.web.core.windows.net`.

O ponto de extremidade do serviço da Web sempre permite acesso anônimo de leitura, retorna páginas HTML formatadas em resposta a erros de serviço e permite somente operações de leitura de objetos. O ponto de extremidade de serviço da web retorna o documento de índice no diretório solicitado para a raiz e todos os subdiretórios. Quando o serviço de armazenamento retorna um erro 404, o ponto de extremidade da Web retorna um documento de erro personalizado, se você o configurou.

O conteúdo do seu site estático está hospedado em um contêiner especial chamado "$web". Como parte do processo de ativação, "$ web" é criado para você, se ainda não existir. O conteúdo em "$ web" pode ser acessado na raiz da conta usando o ponto de extremidade da web. Por exemplo, `https://contoso.z4.web.core.windows.net/` retorna o documento de índice que você configurou para o seu site, caso exista um documento com esse nome no diretório raiz de $ web.

Ao carregar conteúdo ao seu website, use o ponto de extremidade de armazenamento de blob. Para fazer upload de um blob chamado 'image.jpg' que pode ser acessado na raiz da conta, use o seguinte URL `https://contoso.blob.core.windows.net/$web/image.jpg`. A imagem carregada pode ser vista em um navegador de web em um ponto de extremidade de web `https://contoso.z4.web.core.windows.net/image.jpg` correspondente.


## <a name="custom-domain-names"></a>Nomes de domínio personalizados
Você pode usar um domínio personalizado para hospedar seu conteúdo web. Para fazer isso, siga o guia em [Configurar um nome de domínio personalizado para conta de Armazenamento do Azure](storage-custom-domain-name.md). Para acessar seu site hospedado em um nome de domínio personalizado por HTTPS, consulte [ Usando o CDN do Azure para acessar blobs com domínios personalizados por HTTPS ](storage-https-custom-domain-cdn.md). Aponte seu CDN para o ponto de extremidade da web em vez do ponto de extremidade do blob e lembre-se de que a configuração de CDN não acontece instantaneamente, portanto, talvez seja necessário aguardar alguns minutos antes que seu conteúdo fique visível.

## <a name="pricing-and-billing"></a>Preços e cobrança
Hospedagem de website estático é fornecida sem nenhum custo adicional. Para obter mais detalhes sobre preços para Armazenamento de Blob do Azure, confira [Página de Preços do Armazenamento de Blob do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Início rápido
### <a name="azure-portal"></a>Portal do Azure
[Crie uma conta de armazenamento GPv2](../common/storage-quickstart-create-account.md), caso não tenha feito isso ainda Para começar a hospedar seu aplicativo Web, você pode configurar o recurso usando o Portal do Azure e clicar em "Site estático (versão prévia)" em "Configurações" na barra de navegação à esquerda. Clique em "Habilitado" e insira o nome do documento de índice e (opcionalmente) o caminho do documento de erro personalizado.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Faça o upload de seus recursos da web para o contêiner "$web" que foi criado como parte da ativação estática do site. Você pode fazer isso diretamente no Portal do Azure ou pode aproveitar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para carregar estruturas de diretórios inteiras. Tenha certeza de incluir um documento de índice com o nome que você configurou. Neste exemplo, o nome do documento é "index.html".

> [!NOTE]
> O nome do documento diferencia maiúsculas de minúsculas e, portanto, precisa corresponder exatamente ao nome do arquivo no armazenamento.

Finalmente, navegue até seu ponto de extremidade de web para testar seu website.

### <a name="azure-cli"></a>CLI do Azure
Instale a extensão de versão prévia de armazenamento:

```azurecli-interactive
az extension add --name storage-preview
```
Habilitar o recurso:

```azurecli-interactive
az storage blob service-properties update --account-name <account-name> --static-website --404-document <error-doc-name> --index-document <index-doc-name>
```
Consulta para a URL do ponto de extremidade da Web:

```azurecli-interactive
az storage account show -n <account-name> -g <resource-group> --query "primaryEndpoints.web" --output tsv
```

Carregar objetos no contêiner $web:

```azurecli-interactive
az storage blob upload-batch -s deploy -d $web --account-name <account-name>
```

## <a name="faq"></a>Perguntas frequentes
**Os websites estáticos estão disponíveis para todos os tipos de conta de armazenamento?**  
Não, hospedagem de websites estáticos está apenas disponível em contas de armazenamento padrão GPv2.

**As regras VNET e de firewall do Storage são suportadas no novo ponto de extremidade da Web?**  
Sim, o novo endpoint da web obedece às regras de VNET e firewall configuradas para a conta de armazenamento.

**O ponto de extremidade da Web diferencia maiúsculas de minúsculas?**  
Sim, o ponto de extremidade da Web diferencia maiúsculas e minúsculas exatamente como o ponto de extremidade do blob. 

## <a name="next-steps"></a>Próximas etapas
* [Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md)
* [Configurar um nome de domínio personalizado para seu ponto de extremidade de Blobs ou web](storage-custom-domain-name.md)
* [Funções do Azure](/azure/azure-functions/functions-overview)
* [Aplicativos Web do Azure](/azure/app-service/app-service-web-overview)
* [Compile seu primeiro aplicativo web sem servidor](https://aka.ms/static-serverless-webapp)

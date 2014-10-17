<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Como garantir serviços de back-end usando autenticação de certificado mútuo no Gerenciamento de API do Azure

O Gerenciamento de API fornece a capacidade para garantir acesso ao serviço back-end de uma API usando certificados mútuos. Este guia mostra como gerenciar certificados no console de Gerenciamento de API e como configurar uma API para usar um certificado para acessar seu serviço back-end.

> Para obter mais informações sobre gerenciamento de certificados usando API REST de Gerenciamento de API, consulte [entidade de Certificado da API REST de Gerenciamento de API do Azure][entidade de Certificado da API REST de Gerenciamento de API do Azure].

## Neste tópico

-   [Pré-requisitos][Pré-requisitos]
-   [Carregar um certificado do cliente][Carregar um certificado do cliente]
-   [Excluir um certificado do cliente][Excluir um certificado do cliente]
-   [Configurar uma API para usar um certificado mútuo para autenticação de proxy][Configurar uma API para usar um certificado mútuo para autenticação de proxy]

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autenticação de certificado mútuo para acessar o serviço back-end para uma API. Antes de seguir as etapas neste tópico, você deve ter seu serviço back-end configurado para autenticação de certificado mútuo e ter acesso ao certificado e a senha para o certificado para carregamento no console de Gerenciamento de API.

## <a name="step1"> </a>Carregar um certificado do cliente

Para começar, clique em **Console de gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

![Console de Gerenciamento de API][Console de Gerenciamento de API]

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

Clique em **Segurança** no menu **Gerenciamento de API** à esquerda e clique em **Certificados do cliente**.

![Certificados do cliente][Certificados do cliente]

Para carregar um novo certificado, clique em **Carregar um certificado**.

![Carregar um certificado][Carregar um certificado]

Navegue para o seu certificado e digite a senha para o certificado.

> O certificado deve estar no formato **.pfx**. Os certificados autoassinados são permitidos.

![Carregar um certificado][1]

Clique em **Carregar** para carregar o certificado.

> A senha do certificado é validada neste momento. Se ela estiver incorreta, é exibida uma mensagem de erro.

![Certificado carregado][Certificado carregado]

Uma vez que o certificado é carregado, ele aparece na guia **Certificados do cliente**. Se você tiver certificados múltiplos, tome nota do assunto, ou dos últimos quatro caracteres da impressão digital, que são usados para selecionar o certificado ao configurar uma API para usar certificados, conforme cobertos na seguinte seção [Configurar uma API para usar um certificado mútuo para autenticação de proxy][Configurar uma API para usar um certificado mútuo para autenticação de proxy].

## <a name="step1a"> </a>Excluir um certificado do cliente

Para excluir um certificado, clique em **Excluir** ao lado do certificado desejado.

![Excluir Certificado][Excluir Certificado]

Clique em **Sim, excluir** para confirmar.

![Confirmar exclusão][Confirmar exclusão]

Se o certificado está em uso por uma API, então uma tela de aviso é exibida. Para excluir o certificado, você primeiro deve remover o certificado de quaisquer APIs que são configuradas para usá-la.

![Confirmar exclusão][2]

## <a name="step2"> </a>Configurar uma API para usar um certificado mútuo para autenticação de proxy

Clique nas **APIs** no menu **Gerenciamento de API** à esquerda, clique no nome da API desejada e clique na guia **Segurança**.

![Segurança da API][Segurança da API]

Selecione **Certificados mútuos** da lista suspensa **Com credenciais**.

![Certificados mútuos][Certificados mútuos]

Selecione o certificado desejado da lista suspensa **Certificado do cliente**. Se houver certificados múltiplos, você pode olhar no assunto ou nos últimos quatro caracteres da impressão digital como observado na seção anterior para determinar o certificado correto.

![Selecionar certificado][Selecionar certificado]

Clique em **Salvar** para salvar as alterações de configuração para a API.

> Essa alteração tem efeito imediato e chama para operações desta API, que usará o certificado para autenticar no servidor back-end.

![Salvar alterações da API][Salvar alterações da API]

> Quando um certificado é especificado na autenticação de proxy para o serviço back-end de uma API, se torna parte da política para essa API e pode ser exibido no editor de política.

![Política do certificado][Política do certificado]

  [entidade de Certificado da API REST de Gerenciamento de API do Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [Pré-requisitos]: #prerequisites
  [Carregar um certificado do cliente]: #step1
  [Excluir um certificado do cliente]: #step1a
  [Configurar uma API para usar um certificado mútuo para autenticação de proxy]: #step2
  [Console de Gerenciamento de API]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Certificados do cliente]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [Carregar um certificado]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [1]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [Certificado carregado]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [Excluir Certificado]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [Confirmar exclusão]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [2]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [Segurança da API]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [Certificados mútuos]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [Selecionar certificado]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [Salvar alterações da API]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [Política do certificado]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png

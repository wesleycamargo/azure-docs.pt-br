---
title: "Configurar um nome de domínio para seu ponto de extremidade de Armazenamento de Blobs | Microsoft Docs"
description: "Saiba como mapear um domínio de usuário personalizado para o ponto de extremidade do Armazenamento de Blobs para uma conta de armazenamento do Azure no Portal Clássico do Azure."
services: storage
documentationcenter: 
author: tamram
manager: carmonm
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e81c98e0da32e0ac432a1a4bae0fb771e35cff7


---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs
## <a name="overview"></a>Visão geral
Você pode configurar um domínio personalizado para acessar os dados de blob em sua conta de armazenamento do Azure. O ponto de extremidade para o Armazenamento de Blobs é `<storage-account-name>.blob.core.windows.net`. Se você mapear um domínio personalizado e um subdomínio como **www.contoso.com** para o ponto de extremidade do blob para sua conta de armazenamento, logo, os usuários também poderão acessar dados do blob em sua conta de armazenamento usando esse domínio.

> [!IMPORTANT]
> O Armazenamento do Azure ainda não suporta HTTPS com domínios personalizados. Estamos cientes de que os clientes estão interessados nesse recurso, e ele estará disponível em uma versão futura.
> 
> 

Existem duas maneiras de apontar seu domínio personalizado para o ponto de extremidade do blob de sua conta de armazenamento. A maneira mais simples é criar um Registro CNAME que mapeia seu domínio e subdomínio personalizados ao ponto de extremidade do blob. Um Registro CNAME é um recurso do DNS que mapeia um domínio de origem a um domínio de destino. Nesse caso, o domínio de origem é o seu domínio e subdomínio personalizados – observe que o subdomínio é sempre necessário. O domínio de destino é o ponto de extremidade do serviço de blobs.

O processo de mapeamento de seu domínio personalizado ao ponto de extremidade do blob pode, no entanto, resultar em um breve período de inatividade para o domínio, enquanto você estiver registrando o domínio no [Portal Clássico do Azure](https://manage.windowsazure.com). Se atualmente o seu domínio personalizado oferecer suporte a um aplicativo com um contrato de nível de serviço (SLA) que não permita um tempo de inatividade, então você pode usar o subdomínio **asverify** do Azure para fornecer uma etapa intermediária de registro para que os usuários possam acessar seu domínio durante o mapeamento do DNS.

A tabela a seguir mostra exemplos de URLs para acessar os dados do blob em uma conta de armazenamento denominada **mystorageaccount**. O domínio personalizado registrado para a conta de armazenamento é **www.contoso.com**.

| Tipo de recurso | Formatos de URL |
| --- | --- |
| Conta de armazenamento |**URL padrão:** http://mystorageaccount.blob.core.windows.net<p>**URL do domínio personalizado:** http://www.contoso.com</td> |
| Blob |**URL padrão:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**URL do domínio personalizado:** http://www.contoso.com/mycontainer/myblob |
| Contêiner raiz |**URL padrão:** http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**URL do domínio personalizado:** http://www.contoso.com/myblob ou http://www.contoso.com/$root/myblob |

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registrar um domínio personalizado para sua conta de armazenamento
Use este procedimento para registrar seu domínio personalizado se você não se importar com a indisponibilidade temporária do domínio para os usuários ou se seu domínio personalizado não estiver atualmente hospedando um aplicativo.

Se o seu domínio personalizado no momento estiver oferecendo suporte a um aplicativo que não pode ter nenhum tempo de inatividade, use o procedimento descrito em <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">Registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio asverify intermediário</a>.

Para configurar um nome de domínio personalizado, você deve criar um novo Registro CNAME com seu registrador de domínio. O Registro CNAME especifica um alias para um nome de domínio; nesse caso, ele mapeia o endereço do seu domínio personalizado ao ponto de extremidade de Armazenamento de Blobs para sua conta de armazenamento.

Cada registrador tem um método semelhante, mas ligeiramente diferente, para especificar um registro CNAME, mas o conceito é o mesmo. Observe que muitos pacotes de registro de domínio básico não oferecem a configuração do DNS, portanto, talvez seja necessário atualizar o pacote de registro de domínio antes de criar o Registro CNAME.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), navegue até a guia **Armazenamento** .
2. Na guia **Armazenamento** , clique no nome da conta de armazenamento à qual você deseja mapear o domínio personalizado.
3. Clique na guia **Configurar** .
4. Na parte inferior da tela, clique em **Gerenciar Domínio** para exibir o diálogo **Gerenciar Domínio Personalizado**. No texto na parte superior da caixa de diálogo, você visualizará informações sobre como criar o Registro CNAME. Para este procedimento, ignore o texto que se refere ao subdomínio **asverify** .
5. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
7. Crie um novo registro CNAME e forneça um alias de subdomínio, como **www** ou **photos**. Em seguida, forneça um nome do host, que é o ponto de extremidade de serviço Blob, no formato **mystorageaccount.blob.core.windows.net** (em que **mystorageaccount** é o nome da sua conta de armazenamento). O nome do host que será usado é fornecido no texto da caixa de diálogo **Gerenciar Domínio personalizado** .
8. Depois de criar o registro CNAME, volte ao diálogo **Gerenciar Domínio Personalizado** e insira o nome do seu domínio personalizado, incluindo o subdomínio, no campo **Nome de Domínio Personalizado**. Por exemplo, se o domínio for **contoso.com** e seu subdomínio for **www**, digite **www.contoso.com**; se o seu subdomínio for **photos**, digite **photos.contoso.com**. Observe que o subdomínio é obrigatório.
9. Clique no botão **Registrar** para registrar seu domínio personalizado.
   
    Se o registro for bem-sucedido, a mensagem **Seu domínio personalizado está ativo**será exibida. Os usuários podem agora exibir os dados do blob em seu domínio personalizado, contanto que tenham as permissões adequadas.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registrar um domínio personalizado para sua conta de armazenamento usando o subdomínio asverify intermediário
Use este procedimento para registrar seu personalizado domínio se o seu domínio personalizado atualmente der suporte a um aplicativo com um SLA que não permita nenhum tempo de inatividade. Ao criar um CNAME que aponta de asverify.&lt;subdomain&gt;.&lt;customdomain&gt; to asverify.&lt;storageaccount&gt;.blob.core.windows.net, você pode pré-registrar seu domínio com o Azure. Depois, é possível criar um segundo CNAME que aponta de &lt;subdomain&gt;.&lt;customdomain&gt; to &lt;storageaccount&gt;.blob.core.windows.net, no qual o tráfego de ponto para seu domínio personalizado será direcionado para o ponto de extremidade do blob.

O subdomínio asverify é um subdomínio especial reconhecido pelo Azure. Ao anexar **asverify** ao seu próprio subdomínio, você permite que o Azure reconheça seu domínio personalizado sem modificar o registro DNS do domínio. Depois que você modificar o registro DNS do domínio, ele será mapeado ao ponto de extremidade do blob sem nenhum tempo de inatividade.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), navegue até a guia **Armazenamento** .
2. Na guia **Armazenamento** , clique no nome da conta de armazenamento à qual você deseja mapear o domínio personalizado.
3. Clique na guia **Configurar** .
4. Na parte inferior da tela, clique em **Gerenciar Domínio** para exibir o diálogo **Gerenciar Domínio Personalizado**. No texto na parte superior da caixa de diálogo, você visualizará informações sobre como criar o Registro CNAME usando o subdomínio **asverify** .
5. Faça logon no site do registrador de DNS e acesse a página de gerenciamento de DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
6. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
7. Crie um novo registro CNAME e forneça um alias de subdomínio que inclui o subdomínio asverify. Por exemplo, o subdomínio especificado estará no formato **asverify.www** ou **asverify.photos**. Em seguida, forneça um nome do host, que é o ponto de extremidade de serviço Blob, no formato **asverify.mystorageaccount.blob.core.windows.net** (em que **mystorageaccount** é o nome da sua conta de armazenamento). O nome do host que será usado é fornecido no texto da caixa de diálogo **Gerenciar Domínio personalizado** .
8. Depois de criar o registro CNAME, volte ao diálogo **Gerenciar Domínio Personalizado** e insira o nome do seu domínio personalizado no campo **Nome de Domínio Personalizado**. Por exemplo, se o domínio for **contoso.com** e seu subdomínio for **www**, digite **www.contoso.com**; se o seu subdomínio for **photos**, digite **photos.contoso.com**. Observe que o subdomínio é obrigatório.
9. Clique na caixa de seleção **Avançado: Usar o subdomínio 'asverify' para pré-registrar meu domínio personalizado**.
10. Clique no botão **Registrar** para pré-registrar seu domínio personalizado.
    
    Se o seu pré-registro for bem-sucedido, a mensagem **Seu domínio personalizado está ativo**será exibida.
11. Nesse ponto, seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento. Para concluir o processo, volte ao site do registrador do DNS e crie outro registro CNAME que mapeie o subdomínio para o ponto de extremidade do serviço Blob. Por exemplo, especifique o subdomínio como **www** ou **photos** e o nome do host como **mystorageaccount.blob.core.windows.net** (em que **mystorageaccount** é o nome da sua conta de armazenamento). Com essa etapa, o registro do seu domínio personalizado está concluído.
12. Por fim, você pode excluir o registro CNAME criado usando **asverify**, já que ele era necessário apenas como uma etapa intermediária.

Os usuários podem agora exibir os dados do blob em seu domínio personalizado, contanto que tenham as permissões adequadas.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Verificar se o subdomínio personalizado faz referência ao ponto de extremidade do serviço de blobs
Para verificar se seu domínio personalizado está de fato mapeado ao ponto de extremidade do serviço de blobs, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, use um URI no seguinte formato para acessar o blob:

* http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Por exemplo, você pode usar o seguinte URI para acessar um formulário da Web por meio de um subdomínio personalizado **photos.contoso.com**, que é mapeado para um blob no contêiner **myforms**:

* http://photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Cancelar o registro de um domínio personalizado para sua conta de armazenamento
Para cancelar o registro de um domínio personalizado, siga estas etapas: 

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com). 
2. No painel de navegação, clique em **Armazenamento**. 
3. Na página **Armazenamento** , clique no nome da conta de armazenamento para exibir o painel. 
4. Na faixa de opções, clique em **Gerenciar Domínio**. 
5. Na caixa de diálogo **Gerenciar Domínio Personalizado**, clique em **Cancelar Registro**. 

## <a name="additional-resources"></a>Recursos adicionais
* [Como mapear o domínio personalizado para o ponto de extremidade da CDN (Rede de Distribuição de Conteúdo)](../cdn/cdn-map-content-to-custom-domain.md)




<!--HONumber=Nov16_HO3-->



---
title: Tutorial - Hospedar seu domínio e subdomínio no DNS do Azure
description: Este tutorial mostra como configurar o DNS do Azure para hospedar suas zonas DNS.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 44f5bf9a28d56e85bae1d50136c50868ec96eb4e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205434"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Tutorial: Hospedar seu domínio no DNS do Azure

Você pode usar o DNS do Azure para hospedar seu domínio DNS e gerenciar seus registros DNS. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. 

Suponha que você compre o domínio 'contoso.net' de um registrador de nome de domínio e, em seguida, crie uma zona chamada contoso.net no DNS do Azure. Como o proprietário do domínio, seu registrador oferece a opção de configurar os registros de servidor de nomes (NS) para seu domínio. O registrador armazena os registros NS na zona pai .net. Usuários da internet em todo o mundo são direcionados para seu domínio na zona DNS do Azure quando eles estiverem tentando resolver os registros DNS em contoso.net.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma zona DNS
> * Recuperar lista de servidores de nome
> * Delegar o domínio
> * Verificar se a delegação está funcionando


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Entre no Portal do Azure.
1. No canto superior esquerdo, selecione **Criar um recurso** > **Networking** > **zona DNS** para abrir a página **Criar zona DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Na página **Criar zona DNS**, insira os seguintes valores e clique em **Criar**:

   | **Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|[seu nome de domínio] |O nome de domínio que você comprou. Este tutorial usa contoso.net como um exemplo.|
   |**Assinatura**|[Sua assinatura]|Selecione uma assinatura na qual a zona será criada.|
   |**Grupo de recursos**|**Criar um novo:** contosoRG|Crie um grupos de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. |
   |**Localidade**|Leste dos EUA||

> [!NOTE]
> O local do grupo de recursos não tem impacto sobre o local da zona DNS. O local da zona DNS sempre é “global” e não é exibido.

## <a name="retrieve-name-servers"></a>Recuperar servidores de nome

Antes que você possa delegar a zona DNS ao DNS do Azure, você precisará saber os nomes do servidor para sua zona. O Azure DNS aloca os servidores de nomes de um pool sempre que uma zona é criada.

1. Com a zona DNS criada, no painel **Favoritos** do portal do Azure, selecione **Todos os recursos**. Na página **Todos os recursos**, selecione sua zona DNS. Se a assinatura que você selecionou já contém vários recursos, você pode digitar seu nome de domínio na caixa **Filtrar pelo nome** na caixa para acessar facilmente o gateway de aplicativo. 

1. Recupere os servidores de nomes da página da zona DNS. Neste exemplo, os servidores de nomes *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* e *ns4-01.azure-dns.info*:

   ![Lista de servidores de nome](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

O DNS do Azure cria automaticamente os registros NS autoritativos na zona que contém os servidores de nomes atribuídos.


## <a name="delegate-the-domain"></a>Delegar o domínio

Agora que a zona DNS foi criada e você tem os servidores de nomes, você precisa atualizar o domínio pai com os servidores de nome do DNS do Azure. Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. Na página de gerenciamento do DNS do registrador, edite os registros NS e substitua-os por aqueles criados pelos servidores de nomes de domínio do Azure.

Ao delegar um domínio ao DNS do Azure, você deve usar os servidores de nomes fornecidos pelo DNS do Azure. É recomendável usar todos os quatro servidores de nomes, independentemente do nome do seu domínio. A delegação de domínio não requer que o servidor de nomes use o mesmo domínio de nível superior do seu domínio.

Atualmente, o Azure DNS não dá suporte às delegações que usam nomes de servidores em sua própria zona, às vezes chamados de *servidores de nome intuitivos*.

## <a name="verify-that-the-delegation-is-working"></a>Verificar se a delegação está funcionando

Após concluir a delegação, você poderá verificar se ela está funcionando, usando uma ferramenta como *nslookup* para consultar o registro de início de autoridade (SOA) da zona. O registro SOA é criado automaticamente quando a zona é criada. Você pode precisar aguardar 10 minutos ou mais depois de concluir a delegação antes que você possa verificar com êxito se ele está funcionando. Pode levar algum tempo para que as alterações se propaguem por meio do sistema DNS.

Você não precisa especificar os servidores de nome DNS do Azure. Se a delegação estiver configurada corretamente, o processo normal de resolução DNS localiza automaticamente os servidores de nomes.

Em um prompt de comando, digite um comando nslookup semelhante ao seguinte:

```
nslookup -type=SOA contoso.net
```

Aqui está um exemplo de resposta do comando anterior:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter o grupo de recursos **contosoRG** se você pretender fazer o próximo tutorial. Caso contrário, exclua o grupo de recursos **contosoRG** para excluir os recursos criados neste tutorial. Para fazer isso, clique no grupo de recursos **contosoRG** e, em seguida, em **Excluir grupo de recursos**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma zona DNS para seu domínio e delegou-a ao DNS do Azure. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)

---
title: Como criar e gerenciar uma zona DNS no Portal do Azure | Microsoft Docs
description: Saiba como criar zonas DNS para o DNS do Azure. Este é uma guia passo a passo para criar e gerenciar seu primeiro DNS e começar a hospedar o domínio DNS usando o Portal do Azure.
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Criar uma zona DNS no portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI do Azure](dns-getstarted-create-dnszone-cli.md)
> 
> 

Este artigo explicará as etapas para criar uma zona DNS usando o Portal do Azure. Você também pode criar uma zona DNS usando o PowerShell ou a CLI.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

### Sobre as marcas do DNS do Azure
As marcas consistem em uma lista de pares de nome/valor e são usadas pelo Azure Resource Manager na rotulagem de recursos para fins de cobrança ou agrupamento. Para saber mais sobre marcas, confira o artigo [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

Você pode adicionar marcas no Portal do Azure usando a folha **Configurações** da sua zona DNS.

## Criar uma zona DNS
1. Entrar no Portal do Azure
2. No menu Hub, clique em **Novo > Rede >** e em **Zona DNS** para abrir a folha de zona DNS.
   
    ![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)
3. Na folha **Zona DNS**, clique em **Criar** na parte inferior. Essa ação abrirá a folha **Criar zona DNS**.
   
    ![Criar zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)
4. Na folha **Criar zona DNS**, dê um nome para sua zona DNS. Por exemplo, *contoso.com*. [Confira Sobre nomes de zona DNS](#names) na seção acima.
5. Em seguida, especifique o grupo de recursos que você quer usar. Você pode criar um novo grupo de recursos ou selecionar um que já existe.
6. No menu suspenso **Local**, especifique o local do grupo de recursos. Observe que essa configuração se refere ao local do grupo de recursos, não ao local da zona DNS. O recurso de zona DNS real é automaticamente "global" e não é algo que você possa (ou precise) especificar no portal.
7. É possível deixar a caixa de seleção **Fixar no painel** marcada se você quiser localizar facilmente a nova zona no painel. Em seguida, clique em **Criar**.
   
    ![Fixar no painel](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)
8. Depois de clicar em Criar, você verá a nova zona sendo configurada no painel.
   
    ![Criando](./media/dns-getstarted-create-dnszone-portal/creating150.png)
9. Quando a nova zona tiver sido criada, a folha dela será aberta no painel.

## Exibir registros
Criar uma zona DNS também cria os seguintes registros:

* O registro SOA (Início de Autoridade). O SOA está presente na raiz de cada zona DNS.
* Os registros NS (name server, servidor de nomes) autoritativos. Eles mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Você pode exibir os registros no Portal do Azure

1. Na folha **Zona DNS**, clique em **Todas as configurações** para abrir a folha **Configurações** da zona DNS.
   
    ![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)
2. Na parte inferior do painel Essentials, é possível ver os conjuntos de registros para a zona DNS.

    ![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## Teste
Você pode testar a zona do DNS usando ferramentas do DNS como nslookup, dig ou o [cmdlet do PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisará direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme listado em `Get-AzureRmDnsRecordSet` acima. Certifique-se de substituir os valores corretos para sua zona para o comando a seguir.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)



## Excluir uma zona DNS
Você pode excluir a zona DNS diretamente no portal. Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto os registros NS e SOA na raiz da zona, que foram criados automaticamente quando a zona foi criada.

1. Localize a folha **Zona DNS** da zona que você quer excluir e clique em **Excluir** na parte superior da folha.
2. Será exibida uma mensagem informando que você deve excluir todos os conjuntos de registros, exceto os registros NS e SOA que foram criados automaticamente. Se você tiver excluído os conjuntos de registros, clique em **Sim**. Observe que ao excluir uma zona DNS no portal, o Grupo de Recursos ao qual a zona DNS está associada não será excluído.

## Próximas etapas
Após criar uma zona DNS, crie [conjuntos de registros e registros](dns-getstarted-create-recordset-portal.md) para iniciar a resolução de nomes do seu domínio da Internet.

<!---HONumber=AcomDC_0817_2016-->
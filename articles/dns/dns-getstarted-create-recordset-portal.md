<properties
   pageTitle="Criar um conjunto de registros e registros para uma zona DNS usando o portal do Azure | Microsoft Azure"
   description="Como criar registros de host para o DNS do Azure e criar conjuntos de registros e registros usando o portal do Azure"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Criar registros e conjuntos de registros DNS usando o Portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI do Azure](dns-getstarted-create-recordset-cli.md)


Este artigo o guiará durante a criação de registros e conjuntos de registros usando o Portal do Azure. Depois de criar a zona DNS, você precisa adicionar os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e os conjuntos de registros.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## Criar registro e um conjunto de registros

O exemplo a seguir explica como criar um conjunto de registros e registros usando o portal do Azure. Usaremos o tipo de registro de DNS 'A'.

1. Faça logon no Portal do Azure.

2. Navegue até a folha da **zona do DNS** na qual você deseja criar um conjunto de registros.

3. Na folha da zona do DNS, na parte superior da folha, clique em **Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.
 
	![novo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Na folha **Adicionar conjunto de registros**, nomeie o conjunto de registros. Por exemplo, você poderia nomear o conjunto de registros como "**www**".
  
	![adicionar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Selecione o tipo de registro que deseja criar. Por exemplo, **A**.

6. Defina o **TTL**. O padrão no portal é 1 hora.

7. Adicione os endereços IP, um endereço IP por linha. O uso do nome de conjunto de registros e do tipo de registro sugeridos acima adiciona os endereços IP IPv4 ao registro para o conjunto de registros www.

8. Ao terminar de adicionar endereços IP, clique em **OK** na parte inferior da folha. O conjunto de registros DNS será criado.


## Próximas etapas

Para gerenciar seus registros e conjunto de registros, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md).

Para saber mais sobre o Azure DNS, confira [Visão geral do Azure DNS](dns-overview.md).

<!-----HONumber=AcomDC_0518_2016-->
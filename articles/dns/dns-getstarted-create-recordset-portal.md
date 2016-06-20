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



# Criar registros e conjuntos de registros DNS usando o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI do Azure](dns-getstarted-create-recordset-cli.md)


Este artigo o guiará durante o processo de criação de registros e conjuntos de registros usando o portal do Azure. Depois de criar sua zona DNS, você adiciona os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## Criar registro e um conjunto de registros

O exemplo a seguir explica o processo de criação de um registro e conjunto de registros usando o portal do Azure. Usaremos o tipo de registro DNS “A”.

1. Entre no portal.

2. Vá para a folha da **zona DNS** na qual você deseja criar um conjunto de registros.

3. Na parte superior da folha **zona DNS**, selecione **Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.

	![Novo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Na folha **Adicionar conjunto de registros**, nomeie o conjunto de registros. Por exemplo, você pode nomear o conjunto de registros “**www**”.

	![Adicionar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Selecione o tipo de registro que você deseja criar. Por exemplo, selecione **A**.

6. Defina o **TTL**. A vida útil padrão no portal é de uma hora.

7. Adicione os endereços IP, um endereço IP por linha. Quando você usa o nome do conjunto de registros e o tipo de registro sugeridos descritos anteriormente, você adiciona os endereços IP IPv4 ao registro **A** ao conjunto de registros www.

8. Depois de concluir a inclusão de endereços IP, selecione **OK** na parte inferior da folha. O conjunto de registros DNS será criado.


## Próximas etapas

Para gerenciar seus registros e seu conjunto de registros, confira [Gerenciar registros DNS e conjuntos de registros usando o portal do Azure](dns-operations-recordsets-portal.md).

Para obter mais informações sobre o DNS do Azure, confira [Visão geral do DNS do Azure](dns-overview.md).

<!---HONumber=AcomDC_0608_2016-->
---
title: "Cadeia de conexão do MongoDB para conta do DocumentDB | Microsoft Docs"
description: "Saiba como conectar seu aplicativo MongoDB a uma conta do Azure DocumentDB usando uma cadeia de conexão do MongoDB."
keywords: "cadeia de conexão do mongodb"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 91474f61ab724d3fd7a70c51dcd097fade8953dd
ms.openlocfilehash: 3789079412932d56be1c9697fc23c56de5afba6f


---

# <a name="connect-a-mongodb-app-to-an-documentdb-account-using-a-mongodb-connection-string"></a>Conectar um aplicativo MongoDB a uma conta do DocumentDB usando a cadeia de conexão do MongoDB
Saiba como conectar seu aplicativo MongoDB a uma conta do Azure DocumentDB usando uma cadeia de conexão do MongoDB. Ao conectar seu aplicativo MongoDB a um banco de dados do Azure DocumentDB, você pode usar um banco de dados do DocumentDB como o repositório de dados do seu aplicativo MongoDB. 

Este tutorial fornece duas maneiras de recuperar informações da cadeia de conexão:

- [O método de Início rápido](#QuickStartConnection), para uso com os drivers do .NET, Node.js, Shell do MongoDB, Java e Python.
- [O método de cadeia de conexão personalizada](#GetCustomConnection), para uso com outros drivers.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/). 
- Uma conta do DocumentDB. Para obter instruções, veja [Criar uma conta do DocumentDB para uso com aplicativos MongoDB](documentdb-create-mongodb-account.md).

## <a name="a-idquickstartconnectionaget-the-mongodb-connection-string-using-the-quick-start"></a><a id="QuickStartConnection"></a>Obter a cadeia de conexão do MongoDB usando o Início rápido
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **NoSQL (DocumentDB)**, selecione a conta do DocumentDB com suporte ao protocolo do MongoDB. 
3. Na barra **Navegação Esquerda** da folha da conta, clique em **Início rápido**. 
4. Escolha sua plataforma (*driver do .NET*, *driver do Node.js*, *Shell do MongoDB*, *driver do Java*, *driver do Python*). Caso não veja seu driver ou ferramenta na lista, não se preocupe, pois documentamos continuamente mais trechos de código de conexão. Comente abaixo o que você gostaria de ver e leia [Obter informações da cadeia de conexão da conta](#GetCustomConnection) para saber como criar sua própria conexão.
5. Copie e cole o trecho de código no seu aplicativo MongoDB e você estará pronto para seguir adiante.

    ![Captura de tela da folha de início rápido](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a name="a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize"></a><a id="GetCustomConnection"></a> Obter a cadeia de conexão do MongoDB para personalização
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **NoSQL (DocumentDB)**, selecione a conta do DocumentDB com suporte ao protocolo do MongoDB. 
3. Na barra **Navegação Esquerda** da folha da conta, clique em **Cadeia de Conexão**. 
4. A folha **Informações da Cadeia de Conexões** é aberta e tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão pré-construída.

    ![Captura de tela da folha de cadeia de conexão](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos da cadeia de conexão
> [!Important]
> O DocumentDB tem padrões e requisitos de segurança rígidos. As contas do DocumentDB exigem autenticação e comunicação segura via **SSL**.
>
>

É importante observar que o Banco de Dados de Documentos é compatível com o formato de URI da cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: contas do Banco de Dados de Documentos exigem autenticação e comunicação segura via SSL.  Sendo assim, o formato da cadeia de conexão é:

    mongodb://username:password@host:port/[database]?ssl=true

Onde os valores dessa cadeia de caracteres estão disponíveis na folha Cadeia de Conexão mostrada acima.

* Nome de usuário (obrigatório)
  * Nome da conta do Banco de Dados de Documentos
* Senha (obrigatória)
  * Senha da conta do Banco de Dados de Documentos
* Host (obrigatório)
  * FQDN da conta do Banco de Dados de Documentos
* Porta (obrigatória)
  * 10250
* Banco de dados (opcional)
  * O banco de dados padrão usado pela conexão (se nenhum banco de dados for fornecido, o banco de dados padrão será "test")
* ssl=true (obrigatório)

Por exemplo, considere a conta mostrada nas Informações da Cadeia de Conexão acima.  Uma cadeia de conexão válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o MongoChef](documentdb-mongodb-mongochef.md) com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
* Explore [amostras](documentdb-mongodb-samples.md)do Banco de Dados de Documentos com suporte de protocolo para MongoDB.



<!--HONumber=Dec16_HO2-->



---
title: Usar o mongoimport e mongorestor com o Azure DocumentDB | Microsoft Docs
description: "Saiba como usar mongoimport e mongorestore para importar dados para uma conta do DocumentDB com suporte de protocolo para o MongoDB, agora disponível para versão de visualização."
keywords: mongoimport, mongorestore
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: df43600a11aa829ba7fc6b858d64b5f8e98d22fb


---
# <a name="migrate-data-to-documentdb-by-using-mongoimport-and-mongorestore"></a>Migrar dados para o DocumentDB usando o mongoimport e o mongorestore
Para migrar dados para uma conta do Azure DocumentDB com suporte de protocolo para o MongoDB, você deve:

* Baixar o *mongoimport.exe* ou o *mongorestore.exe* do [Centro de Download do MongoDB](https://www.mongodb.com/download-center).
* Obter o [suporte do DocumentDB para a cadeia de conexão do MongoDB](documentdb-connect-mongodb-account.md).

## <a name="before-you-begin"></a>Antes de começar

* Aumentar a taxa de transferência: a duração da sua migração de dados depende da taxa de transferência que você configurar para suas coleções. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar a taxa de transferência no [Portal do Azure](https://portal.azure.com), consulte [Níveis de desempenho e tipos de preço no DocumentDB](documentdb-performance-levels.md).

* Habilitar SSL: o DocumentDB tem padrões e requisitos de segurança rígidos. Certifique-se de habilitar o SSL ao interagir com sua conta. Os procedimentos no restante do artigo incluem como habilitar o SSL para o *mongoimport* e o *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Localizar informações de cadeia de conexão (host, porta, nome de usuário e senha)

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, clique na entrada **NoSQL (DocumentDB)**.
2. No painel **Assinaturas**, selecione o nome da sua conta.
3. No folha **Cadeia de Conexão**, clique em **Cadeia de Conexão**.  
O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.

    ![A folha de "Cadeia de Conexão"](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongoimport"></a>Importar dados para o DocumentDB com suporte de protocolo para o MongoDB com o mongoimport

Use o modelo a seguir para executar a importação de dados para a sua conta do DocumentDB. Preencha *host*, *nome de usuário* e *senha* com os valores que são específicos para a sua conta.  

Modelo:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongorestore"></a>Importar dados para o DocumentDB com suporte de protocolo para o MongoDB com o mongorestore

Para restaurar dados para sua conta do DocumentDB, use o modelo a seguir para executar a importação. Preencha *host*, *nome de usuário* e *senha* com os valores específicos para a sua conta.

Modelo:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações, explore [Suporte de protocolo do DocumentDB para exemplos do MongoDB](documentdb-mongodb-samples.md).



<!--HONumber=Jan17_HO4-->



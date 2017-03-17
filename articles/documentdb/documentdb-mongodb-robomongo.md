---
title: Usar o Robomongo para MongoDB com o Azure DocumentDB | Microsoft Docs
description: Saiba como usar Robomongo com uma conta da API para MongoDB do DocumentDB
keywords: robomongo
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
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: a8db7fbde5d6cd01b558ec351719bae361710efd
ms.lasthandoff: 03/08/2017


---
# <a name="use-robomongo-with-a-documentdb-api-for-mongodb-account"></a>Usar Robomongo com uma conta da API para MongoDB do DocumentDB
Para se conectar a uma conta da API para MongoDB do Azure DocumentDB usando o Robomongo, você deve:

* Baixar e instalar o [Robomongo](https://robomongo.org/)
* Ter suas informações de [cadeia de conexão](documentdb-connect-mongodb-account.md) da sua conta da API para MongoDB do DocumentDB

## <a name="connect-using-robomongo"></a>Conectar usando o Robomongo
Para adicionar sua conta da API para MongoDB do DocumentDB às conexões entre o Robomongo e o MongoDB, execute as etapas a seguir.

1. Recupere as informações da conexão da conta da API para MongoDB do DocumentDB usando as instruções encontradas [aqui](documentdb-connect-mongodb-account.md).

    ![Captura de tela da folha de cadeia de conexão](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. Execute *Robomongo.exe*

3. Clique no botão de conexão em **Arquivo** para gerenciar suas conexões. Em seguida, clique em **Criar** na janela **Conexões do MongoDB**, o que abrirá a janela **Configurações de Conexão**.

4. No janela **Configurações de Conexão**, escolha um nome. Em seguida, localize o **Host** e **Porta** de suas informações de conexão na Etapa 1 e insira-os em **Endereço** e **Porta**, respectivamente.

    ![Captura de tela do Robomongo Manage Connections](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. Na guia **Autenticação**, clique em **Executar autenticação**. Em seguida, insira seu Banco de dados (o padrão é *Admin*), **Nome de Usuário** e **Senha**.
Tanto o **Nome de Usuário** quanto a **Senha** podem ser encontrados em suas informações de conexão na Etapa 1.

    ![Captura de tela da guia de Autenticação do Robomongo](./media/documentdb-mongodb-robomongo/authentication.png)
6. Na guia **SSL**, marque **Usar protocolo SSL** e altere o **Método de Autenticação** para **Certificado Autoassinado**.

    ![Captura de tela da guia Robomongo SSL](./media/documentdb-mongodb-robomongo/SSL.png)
7. Por fim, clique em **Teste** para verificar se você é capaz de se conectar e, em seguida, **Salve**.

## <a name="next-steps"></a>Próximas etapas
* Explore [amostras](documentdb-mongodb-samples.md) da API para MongoDB do DocumentDB.


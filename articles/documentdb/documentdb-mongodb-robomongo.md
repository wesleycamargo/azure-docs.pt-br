---
title: Usar o Robomongo para MongoDB com o Azure DocumentDB | Microsoft Docs
description: "Saiba como usar o Robomongo com uma conta do DocumentDB com suporte a protocolo para MongoDB, agora disponível na visualização."
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
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 141154f9e8236e595f77bd4880c4f63d480da445


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Usar o Robomongo com uma conta do DocumentDB com suporte de protocolo para MongoDB
Para se conectar a uma conta do Azure DocumentDB com suporte de protocolo para MongoDB usando o Robomongo, você deve:

* Baixar e instalar o [Robomongo](https://robomongo.org/)
* Ter as informações de [cadeia de conexão](documentdb-connect-mongodb-account.md) da sua conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB

## <a name="connect-using-robomongo"></a>Conectar usando o Robomongo
Para adicionar a conta do DocumentDB com suporte de protocolo para MongoDB às Conexões entre o Robomongo e o MongoDB, execute as etapas a seguir.

1. Recuperar as informações do Banco de Dados de Documentos com o suporte de protocolo para as informações de conexão do MongoDB usando as instruções que constam [aqui](documentdb-connect-mongodb-account.md).

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
* Explore [amostras](documentdb-mongodb-samples.md)do Banco de Dados de Documentos com suporte de protocolo para MongoDB.



<!--HONumber=Jan17_HO4-->



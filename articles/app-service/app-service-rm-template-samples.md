---
title: Exemplos de modelo do Azure Resource Manager – Serviço de Aplicativo | Microsoft Docs
description: Exemplos de modelo do Azure Resource Manager para o recurso Aplicativos Web do Serviço de Aplicativo
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Modelos do Azure Resource Manager para Aplicativos Web

A tabela a seguir inclui links para modelos do Azure Resource Manager para o recurso Aplicativos Web do Serviço de Aplicativo do Azure. Para obter recomendações sobre como evitar erros comuns ao criar modelos de aplicativo Web, consulte [Orientações sobre a implantação de aplicativos Web com modelos do Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Implantar o aplicativo Web**||
| [Implantar aplicativo Web vinculado a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implanta um aplicativo Web do Azure efetua pull do código no GitHub. |
| [Aplicativo Web com slots de implantação personalizados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implanta um aplicativo Web do Azure com ambientes/slots de implantação personalizados. |
|**Configurar um aplicativo Web**||
| [Certificado de aplicativo Web do Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implanta um certificado de aplicativo Web de um segredo do Azure Key Vault e o usa para uma associação SSL. |
| [Aplicativo Web com um domínio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implanta um aplicativo Web do Azure com um nome do host personalizado. |
| [Aplicativo Web com domínio personalizado e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implanta um aplicativo Web do Azure com um nome de host personalizado e obtém o certificado do aplicativo Web do Key Vault para uma associação SSL. |
| [Aplicativo Web com uma extensão GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implanta um aplicativo Web do Azure com a extensão de site GoLang. Assim, você pode executar aplicativos Web desenvolvidos em GoLang no Azure. |
| [Aplicativo Web com o Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implanta um aplicativo Web do Azure com Java 8 e Tomcat 8 habilitados. Assim, você pode executar aplicativos Java no Azure. |
|**Aplicativo Web do Linux**||
| [Aplicativo Web no Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implanta um aplicativo Web do Azure no Linux com o Banco de Dados do Azure para MySQL. |
| [Aplicativo Web no Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implanta um aplicativo Web do Azure no Linux com o Banco de Dados do Azure para PostgreSQL. |
|**Aplicativo Web com recursos conectados**||
| [Aplicativo Web com o MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implanta um aplicativo Web do Azure no Windows com o Banco de Dados do Azure para MySQL. |
| [Aplicativo Web com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implanta um aplicativo Web do Azure no Windows com o Banco de Dados do Azure para PostgreSQL. |
| [Aplicativo Web com um banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implanta um aplicativo Web do Azure e o Banco de Dados SQL no nível de serviço "Básico". |
| [Aplicativo Web com conexão do armazenamento de Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implanta um aplicativo Web do Azure com uma cadeia de caracteres de conexão de armazenamento de Blobs do Azure. Em seguida, você pode usar o armazenamento de Blobs no aplicativo Web. |
| [Aplicativo Web com o cache Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implanta um aplicativo Web do Azure com um cache Redis. |
|**Ambiente do Serviço de Aplicativo para PowerApps**||
| [Criar um ambiente do Serviço de Aplicativo v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente do Serviço de Aplicativo v2 em sua rede virtual. |
| [Criar um ambiente do Serviço de Aplicativo v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente do Serviço de Aplicativo v2 em sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configurar o certificado SSL padrão para um ambiente de Serviço de Aplicativo ILB ou um ambiente de Serviço de Aplicativo v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado SSL padrão para um ambiente de Serviço de Aplicativo ILB ou um ambiente de Serviço de Aplicativo v2 ILB. |
| | |

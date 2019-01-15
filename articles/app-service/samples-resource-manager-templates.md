---
title: Exemplos de modelo do Azure Resource Manager – Serviço de Aplicativo | Microsoft Docs
description: Amostras de modelo do Azure Resource Manager para o Serviço de Aplicativo
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 842ec98245522095334b9f17e8c12292b7c1dda8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035433"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelos do Azure Resource Manager para o Serviço de Aplicativo

A tabela a seguir inclui links para modelos do Azure Resource Manager para o Serviço de Aplicativo do Azure. Para obter recomendações sobre como evitar erros comuns ao criar modelos de aplicativo, confira [Diretrizes de implantação de aplicativos com modelos do Azure Resource Manager](deploy-resource-manager-template.md).

Para saber mais sobre a sintaxe JSON e as propriedades dos recursos de Serviços de Aplicativos, confira [Tipos de recurso de Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Implantando um aplicativo**||
| [Plano do Serviço de Aplicativo e aplicativo básico do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Implanta um aplicativo do Serviço de Aplicativo configurado para o Linux. |
| [Plano do Serviço de Aplicativo e aplicativo básico do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Implanta um aplicativo do Serviço de Aplicativo configurado para o Windows. |
| [Aplicativo vinculado a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implanta um aplicativo do Serviço de Aplicativo que efetua pull do código por meio do GitHub. |
| [Aplicativo com slots de implantação personalizados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implanta um aplicativo do Serviço de Aplicativo com ambientes/slots de implantação personalizados. |
|**Configurando um aplicativo**||
| [Certificado de aplicativo do Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implanta um certificado de aplicativo do Serviço de Aplicativo de um segredo do Azure Key Vault e usa-o para uma associação SSL. |
| [Aplicativo com um domínio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implanta um aplicativo do Serviço de Aplicativo com um nome do host personalizado. |
| [Aplicativo com um domínio personalizado e o SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implanta um aplicativo do Serviço de Aplicativo com um nome do host personalizado e obtém o certificado de aplicativo do Key Vault para uma associação SSL. |
| [Aplicativo com uma extensão do GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implanta um aplicativo do Serviço de Aplicativo com a extensão de site do Golang. Assim, você pode executar aplicativos Web desenvolvidos em GoLang no Azure. |
| [Aplicativo com Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implanta um aplicativo do Serviço de Aplicativo com o Java 8 e o Tomcat 8 habilitados. Assim, você pode executar aplicativos Java no Azure. |
|**Aplicativo do Linux com recursos conectados**||
| [Aplicativo no Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implanta um aplicativo do Serviço de Aplicativo no Linux com o Banco de Dados do Azure para MySQL. |
| [Aplicativo no Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implanta um aplicativo do Serviço de Aplicativo no Linux com o Banco de Dados do Azure para PostgreSQL. |
|**Aplicativo com recursos conectados**||
| [Aplicativo com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implanta um aplicativo do Serviço de Aplicativo no Windows com o Banco de Dados do Azure para MySQL. |
| [Aplicativo com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implanta um aplicativo do Serviço de Aplicativo no Windows com o Banco de Dados do Azure para PostgreSQL. |
| [Aplicativo com um Banco de Dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implanta um aplicativo do Serviço de Aplicativo e um Banco de Dados SQL no nível de serviço Básico. |
| [Aplicativo com uma conexão do Armazenamento de Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implanta um aplicativo do Serviço de Aplicativo com uma cadeia de conexão do Armazenamento de Blobs do Azure. Em seguida, você poderá usar o Armazenamento de Blobs no aplicativo. |
| [Aplicativo com um Cache Redis do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implanta um aplicativo do Serviço de Aplicativo com um Cache Redis do Azure. |
|**Ambiente do Serviço de Aplicativo para PowerApps**||
| [Criar um ambiente do Serviço de Aplicativo v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente do Serviço de Aplicativo v2 em sua rede virtual. |
| [Criar um ambiente do Serviço de Aplicativo v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente do Serviço de Aplicativo v2 em sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configurar o certificado SSL padrão para um ambiente de Serviço de Aplicativo ILB ou um ambiente de Serviço de Aplicativo v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado SSL padrão para um ambiente de Serviço de Aplicativo ILB ou um ambiente de Serviço de Aplicativo v2 ILB. |
| | |

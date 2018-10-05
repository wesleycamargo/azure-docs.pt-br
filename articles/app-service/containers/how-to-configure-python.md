---
title: Configurar uma imagem interna do Python no Serviço de Aplicativo do Azure
description: Este tutorial descreve opções para criação e configuração de um aplicativo do Python no Serviço de Aplicativo do Azure, com a imagem interna do Python.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228538"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Configurar uma imagem interna do Python no Serviço de Aplicativo do Azure (versão prévia)

Este artigo mostra como configurar a imagem interna do Python no [Serviço de Aplicativo no Linux](app-service-linux-intro.md) para executar seus aplicativos do Python.

## <a name="python-version"></a>Versão do Python

O tempo de execução do Python no Serviço de Aplicativo no Linux usa a versão `python-3.7.0`.

## <a name="supported-frameworks"></a>Estruturas com suporte

Há suporte para todas as versões de estruturas da Web em conformidade com a WSGI (Interface de Gateway do Servidor Web) que são compatíveis com o tempo de execução `python-3.7`.

## <a name="package-management"></a>Gerenciamento de pacote

Durante a publicação do Git, o mecanismo do Kudu procura por [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do repositório e instala automaticamente os pacotes no Azure usando `pip`.

Para gerar esse arquivo antes da publicação, navegue até a raiz do repositório e execute o comando a seguir no ambiente do Python:

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Configurar seu aplicativo do Python

A imagem interna do Python no Serviço de Aplicativo usa o servidor [Gunicorn](http://gunicorn.org/) para executar o aplicativo do Python. O Gunicorn é um servidor de HTTP da WSGI do Python para UNIX. O Serviço de Aplicativo configura o Gunicorn automaticamente para projetos Django e Flask.

### <a name="django-app"></a>Aplicativo do Django

Se você publicar um projeto do Django que contenha um módulo `wsgi.py`, o Azure chamará automaticamente o Gunicorn usando o comando a seguir:

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Aplicativo do Flask

Se você estiver publicando um aplicativo do Flask e o ponto de entrada for em um módulo `application.py` ou `app.py`, o Azure chamará automaticamente o Gunicorn usando um dos comandos a seguir, respectivamente:

```bash
gunicorn application:app
```

Ou 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Personalizar a inicialização

Para definir um ponto de entrada personalizado para seu aplicativo, primeiramente crie um arquivo _.txt_ com um comando do Gunicorn personalizado e coloque-o na raiz do seu projeto. Por exemplo, para iniciar o servidor com o módulo _helloworld.py_ e a variável `app`, crie um _startup.txt_ com o conteúdo a seguir:

```bash
gunicorn helloworld:app
```

Na página [Configurações de aplicativo](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), escolha **Python|3.7** como a **Pilha de tempo de execução** e forneça o nome do seu **Arquivo de inicialização** da etapa anterior. Por exemplo, _startup.txt_.

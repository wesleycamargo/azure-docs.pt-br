---
title: Configurar aplicativos Python para o Serviço de Aplicativo do Azure no Linux
description: Este tutorial descreve as opções para criar e configurar aplicativos Python do Serviço de Aplicativo do Azure no Linux.
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
ms.date: 10/09/2018
ms.author: astay;cephalin;kraigb
ms.custom: mvc
ms.openlocfilehash: 71cbf0bb31a72e3b257f25c159d9d9eea31dbfbb
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901611"
---
# <a name="configure-your-python-app-for-the-azure-app-service-on-linux"></a>Configurar seu aplicativo do Python para o Serviço de Aplicativo do Azure no Linux

Este artigo descreve como o [Serviço de Aplicativo do Azure no Linux](app-service-linux-intro.md) executa aplicativos Python e como você pode personalizar o comportamento do Serviço de Aplicativo quando necessário.

## <a name="container-characteristics"></a>Características do contêiner

Aplicativos Python implantados no Serviço de Aplicativo no Linux são executados dentro de um contêiner do Docker definido no repositório do GitHub, [contêiner do Azure-App-Service/Python](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Esse contêiner tem as seguintes características:

- A imagem de contêiner base é `python-3.7.0-slim-stretch`, o que significa que os aplicativos são executados com Python 3.7. Se você precisar de uma versão diferente do Python, deverá criar e implantar sua própria imagem de contêiner em vez disso. Para obter mais informações, veja [Usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres](tutorial-custom-docker-image.md).

- Aplicativos são executados usando o [Gunicorn WSGI HTTP Server](http://gunicorn.org/) com os argumentos adicionais `--bind=0.0.0.0 --timeout 600`.

- Por padrão, a imagem base inclui a estrutura Web do Flask, mas o contêiner dá suporte a outras estruturas em conformidade com WSGI e compatíveis com Python 3.7, como Django.

- Para instalar pacotes adicionais, como o Django, crie um arquivo [*Requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) na raiz do seu projeto usando `pip freeze > requirements.txt`. Em seguida, publique seu projeto de Serviço de Aplicativo usando a implantação do Git, que executa automaticamente `pip install -r requirements.txt` no contêiner para instalar as dependências do seu aplicativo.

## <a name="container-startup-process-and-customizations"></a>Personalizações e o processo de inicialização do contêiner

Durante a inicialização, o Serviço de Aplicativo no contêiner do Linux executa as seguintes etapas:

1. Verificar e aplicar um comando de inicialização personalizados se fornecido.
1. Verificar a existência de um arquivo *wsgi.py* do aplicativo Django e, se houver, inicializar Gunicorn usando o arquivo.
1. Verificar se há um arquivo chamado *application.py* e, se houver, inicialize Gunicorn usando `application:app` supondo um aplicativo Flask.
1. Se nenhum outro aplicativo for encontrado, inicie um aplicativo padrão criado no contêiner.

As seções a seguir fornecem detalhes adicionais para cada opção.

### <a name="django-app"></a>Aplicativo do Django

Para aplicativos Django, o Serviço de Aplicativo procura um arquivo chamado `wsgi.py` dentro de seu código do aplicativo e, em seguida, executa Gunicorn usando o seguinte comando:

```bash
# <module> is the path to the folder containing wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Se você quiser um controle mais específico sobre o comando de inicialização, use um [comando de inicialização personalizado](#custom-startup-command) e substitua `<module>` pelo nome do módulo que contém *wsgi.py*.

### <a name="flask-app"></a>Aplicativo do Flask

Para Flask, o Serviço de Aplicativo procura um arquivo chamado *application.py* e inicia Gunicorn da seguinte maneira:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 application:app
```

Se o módulo principal do aplicativo estiver contido em um arquivo diferente, use um nome diferente para o objeto de aplicativo. Caso você queira fornecer argumentos adicionais para Gunicorn, use um [comando de inicialização personalizado](#custom-startup-command). Esta seção fornece um exemplo de Flask usando o código de entrada em *hello.py* e um objeto de aplicativo Flask chamado `myapp`.

### <a name="custom-startup-command"></a>Comando de inicialização personalizado

Você pode controlar o comportamento de inicialização do contêiner fornecendo um comando de inicialização Gunicorn personalizado. Por exemplo, se você tiver um aplicativo Flask cujo módulo principal seja *hello.py* e o objeto de aplicativo Flask tenha o nome `myapp`, o comando será assim:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Você também pode adicionar quaisquer argumentos adicionais para o Gunicorn ao comando, como `--workers=4`. Para obter mais informações, veja [Como executar o Gunicorn](http://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Para fornecer um comando personalizado, siga estas etapas:

1. Navegue até a página [Configurações do aplicativo](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) no portal do Azure.

1. Nas configurações de **Tempo de execução**, defina a opção **Pilha** como **Python 3.7** e digite o comando diretamente no campo **Arquivo de Inicialização**.

    Como alternativa, você pode salvar o comando em um arquivo de texto na raiz do seu projeto usando um nome como *startup.txt* (ou qualquer nome que desejar). Em seguida, implante esse arquivo para o Serviço de Aplicativo e especifique o nome do arquivo no campo **Arquivo de Inicialização** em vez disso. Essa opção permite que você gerencie o comando em seu repositório de código-fonte, em vez de por meio do portal do Azure.

1. Clique em **Salvar**. O Serviço de Aplicativo é reiniciado automaticamente e, depois de alguns segundos, você deverá ver o comando de inicialização personalizado aplicado.

> [!Note]
> O Serviço de Aplicativo ignora todos os erros que ocorrem ao processar um arquivo de comando personalizado, então ele continua seu processo de inicialização procurando aplicativos Django e Flask. Se você não vir o comportamento esperado, verifique se o arquivo de inicialização está implantado no Serviço de Aplicativo e se ele não contém erros.

### <a name="default-behavior"></a>Comportamento padrão

Se o Serviço de Aplicativo não encontrar um aplicativo Flask, um aplicativo Django ou um comando personalizado, ele executará um aplicativo padrão somente leitura, localizado na pasta _opt/defaultsite_. O aplicativo padrão será exibido da seguinte maneira:

![Serviço de Aplicativo padrão na página da Web do Linux](media/how-to-configure-python/default-python-app.png)

## <a name="troubleshooting"></a>solução de problemas

- **Você pode ver o aplicativo padrão depois de implantar seu próprio código de aplicativo.**  O aplicativo padrão é exibido porque você não tem implantou de fato o código do aplicativo no Serviço de Aplicativo ou o Serviço de Aplicativo falhou ao localizar o código do aplicativo e executou o aplicativo padrão em vez disso.
  - Reinicie o Serviço de Aplicativo, aguarde de 15 a 20 segundos e verifique o aplicativo novamente.
  - Use SSH ou o console do Kudu para se conectar diretamente ao Serviço de Aplicativo e verificar se há arquivos em *site/wwwroot*. Se os arquivos não existirem, examine seu processo de implantação e reimplante o aplicativo.
  - Se os arquivos existem, o Serviço de Aplicativo não pôde identificar o arquivo de inicialização específica. Verifique se seu aplicativo está estruturado como o Serviço de Aplicativo espera para [Django](#django-app) ou [Flask](#flask-app), ou use um [comando de inicialização personalizados](#custom-startup-command).

- **Você verá a mensagem "Serviço Não Disponível" no navegador.** O navegador atingiu o tempo limite aguardando uma resposta do Serviço de Aplicativo, o que indica que o Serviço de Aplicativo iniciou o servidor Gunicorn, mas os argumentos que especificam o código do aplicativo estão incorretos.
  - Atualize o navegador, especialmente se você estiver usando os tipos de preço mais baixos no seu plano de Serviço de Aplicativo. O aplicativo pode levar mais tempo para ser iniciado ao usar camadas gratuitas, por exemplo, e se tornar responsivo novamente depois que você atualizar o navegador.
  - Verifique se seu aplicativo está estruturado como o Serviço de Aplicativo espera para [Django](#django-app) ou [Flask](#flask-app), ou use um [comando de inicialização personalizados](#custom-startup-command).
  - Use SSH ou o Console do Kudu para se conectar ao Serviço de Aplicativo, então examine os logs de diagnóstico armazenados na pasta *LogFiles*. Para obter mais informações sobre registro em log, veja [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](../web-sites-enable-diagnostic-log.md).

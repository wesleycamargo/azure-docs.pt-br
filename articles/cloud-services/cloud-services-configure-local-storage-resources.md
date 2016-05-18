<properties
pageTitle="Configurar Recursos de Armazenamento Local nos Serviços de Nuvem do Azure | Microsoft Azure"
description="Aprender sobre como configurar recursos de armazenamento local nos Serviços de Nuvem do Azure"
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# Configurar recursos de armazenamento local

Um recurso de armazenamento local é um diretório reservado no sistema de arquivos da máquina virtual no qual uma instância de uma função está em execução. Você pode armazenar informações em sua instância de máquina virtual para que o código em execução na instância possa acessar o recurso de armazenamento local ao precisar gravar ou ler um arquivo. Por exemplo, um recurso de armazenamento local pode ser usado para armazenar em cache os dados que talvez precisem ser acessados novamente enquanto o serviço está em execução no Azure. Você também pode configurar o recurso de armazenamento local para armazenar arquivos durante a inicialização. Para obter mais informações sobre como configurar recursos de armazenamento local para inicialização, consulte [Use Local Storage to Store Files During Startup (Usar armazenamento local para armazenar arquivos durante inicialização)](https://msdn.microsoft.com/library/azure/hh974419.aspx)

Um recurso de armazenamento local é declarado no arquivo de definição de serviço. Você pode declarar qualquer número de recursos de armazenamento local para uma função. Cada recurso de armazenamento local é reservado para cada instância dessa função. A quantidade mínima de espaço em disco que você pode alocar para um recurso de armazenamento local é 1 MB. A quantidade máxima que você pode alocar para qualquer recurso local determinado depende do tamanho da máquina virtual especificada para a função. O tamanho de cada máquina virtual tem uma alocação de armazenamento total correspondente. Além disso, o espaço total alocado para todos os recursos de armazenamento local declarados para uma função não pode exceder o tamanho máximo alocado para o tamanho da máquina virtual. Para obter mais informações sobre a quantidade máxima de espaço em disco alocado para cada tamanho de máquina virtual, consulte [Configurar tamanhos para serviços de nuvem](https://msdn.microsoft.com/library/azure/ee814754.aspx).

> [AZURE.NOTE]
>
-   Lembre-se de que é responsabilidade do desenvolvedor verificar se a quantidade de espaço em disco solicitada para um recurso de armazenamento local não excede a quantidade máxima alocada para uma máquina virtual. Se você configurar um recurso de armazenamento local para ser maior que o máximo permitido, um erro não ocorrerá até você tentar uma operação de gravação que excede o máximo permitido. Nesse caso, a operação de gravação falhará e será exibido uma mensagem de erro de espaço em disco insuficiente. O modelo de processamento do Azure é tentativa/falha. Se você receber um erro de espaço em disco insuficiente, é possível tratar do erro e liberar espaço em disco. Em seguida, você pode tentar a operação de gravação novamente.
-   Você pode especificar que um recurso de armazenamento local seja preservado quando uma instância é reciclada. No entanto, não há garantia de que os dados salvos no sistema de arquivos local da máquina virtual sejam duráveis. Se sua função exige dados duráveis, é recomendável usar uma unidade do Azure para armazenar dados de arquivos. Como as unidades do Azure têm suporte do serviço Blob do Azure, elas têm garantia de durabilidade.  
>


## Adicionando um recurso de armazenamento local

Para declarar um recurso de armazenamento local no arquivo de definição de serviço, adicione o elemento **LocalResources** como filho de um elemento **WebRole** ou do elemento **WorkerRole**. Em seguida, adicione um elemento **LocalStorage** para representar o recurso. O elemento **LocalStorage** usa três atributos:

-   *name*
-   *sizeInMB*: especifica o tamanho desejado do recurso de armazenamento local
-   *cleanOnRoleRecycle*: especifica se o recurso de armazenamento local deve ser apagado quando uma instância de função é reciclada ou se deve persistir durante todo o ciclo de vida da função. O valor padrão é **true**.

O arquivo de definição de serviço a seguir mostra dois recursos de armazenamento local declarados para uma função web:

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

Para obter mais informações sobre o arquivo de definição de serviço, consulte [Esquema de definição de serviço do Azure (arquivo .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx).

> [AZURE.NOTE] Se estiver usando as Ferramentas do Azure para Microsoft Visual Studio, você poderá definir um recurso de armazenamento local nas páginas **Propriedades** da função. Para obter mais informações, consulte [Configuring the Azure Application with Visual Studio (Configurando o aplicativo do Azure com o Visual Studio)](https://msdn.microsoft.com/library/ee405486.aspx).

## Acessando um recurso de armazenamento local de forma programática

Para acessar o recurso de armazenamento local, o aplicativo deve recuperar o caminho por meio do método [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Você pode usar as operações padrão de leitura e gravação do arquivo para ler e gravar o conteúdo do recurso de armazenamento local. Por exemplo, o exemplo a seguir mostra como ler o conteúdo de um arquivo chamado **MyTest. txt** por meio do recurso de armazenamento local e exibi-lo na home page de um aplicativo MVC 3:

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## Acessando um recurso de armazenamento local no tempo de execução

A Biblioteca Gerenciada do Azure fornece classes para acessar o recurso de armazenamento local de dentro do código em execução em uma instância de função. O método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) retorna uma referência a um objeto chamado [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx).

Uma vez que o objeto **LocalResource** representa um diretório, você poderá ler e gravar usando as classes padrão de E/S de arquivo do .NET. Para determinar o caminho para o diretório do recurso de armazenamento local, use a propriedade [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx). Essa propriedade retorna o caminho completo para o recurso de armazenamento local, incluindo o diretório do recurso nomeado. Por exemplo, se o seu serviço estiver em execução no ambiente de desenvolvimento, o recurso de armazenamento local será definido em seu sistema de arquivos local e a propriedade **RootPath** retornará um valor semelhante ao seguinte:


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

Quando o serviço é implantado no Azure, o caminho para o recurso de armazenamento local inclui a ID da implantação e a propriedade **RootPath** retorna um valor semelhante ao seguinte:


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

O código em execução em uma instância de função pode acessar um recurso de armazenamento local definido para essa função desde o momento em que a instância fica online até ser colocada offline.

## Próximas etapas

- [Configurar um serviço de nuvem para o Azure](https://msdn.microsoft.com/library/azure/hh124108.aspx)

<!---HONumber=AcomDC_0420_2016-->
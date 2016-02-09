<properties
   pageTitle="Solucionando problemas de funções com falha na inicialização | Microsoft Azure"
   description="Veja algumas razões comuns pelas quais uma função do Serviço de Nuvem pode falhar ao ser iniciada. Soluções para esses problemas também são fornecidas."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Etapas comuns na solução de problemas de funções do Serviço de Nuvem com falha de inicialização

Veja alguns problemas comuns e soluções relacionadas às funções do Serviço de Nuvem do Azure com falha na inicialização.

## Contate o Suporte ao Cliente do Azure

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/).

Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para obter informações sobre como usar o Suporte do Azure, leia as [Perguntas Frequentes de Suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).


## DLLs ou dependências ausentes
Funções sem resposta e funções que alternam entre os estados **inicializando**, **ocupado** e **parando** podem ser causadas por DLLs ou assemblies ausentes.

**Sintoma:** sintomas de DLLs ou assemblies ausentes podem ser:

- A instância de função está alternando entre **inicializando**/**ocupado**/**parando**
- A instância de função foi movida para **pronto**, mas ao navegar até o seu aplicativo Web, a página não aparece

Resolução: há três métodos recomendados para investigar esses problemas.

## Diagnosticando problemas de DLL ausente em uma função web

Quando você navega para um site que é implantado em uma função web e o navegador exibe um erro de servidor semelhante ao mostrado abaixo:

![Erro de servidor no aplicativo '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## Diagnosticar problemas desativando erros personalizados

Erros mais completos podem ser exibidos pela configuração do web.config da função web para definir o modo de erro personalizado como desativado e pela nova implantação do serviço.

Para exibir erros mais completos sem usar a Área de Trabalho Remota:

1. Abra a solução no Visual Studio.

2. No **Gerenciador de Soluções**, localize o arquivo web.config e abri-lo.

3. No arquivo web.config, localize a seção system.web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```

4. Salve o arquivo.

5. Empacote e implante novamente o serviço.

Depois que o serviço for implantado novamente, você verá o erro abaixo com o nome do assembly ou DLL ausente.

## Diagnosticar problemas exibindo o erro remotamente

É possível usar a área de trabalho remota para acessar a função e exibir erros mais completos remotamente. Use as seguintes etapas para exibir os erros usando a Área de Trabalho Remota:

1. Certifique-se de que o Azure SDK 1.3 ou superior está instalado.

2. Durante a implantação da solução com o Visual Studio, escolha "Configurar conexões de Área de Trabalho Remota..." Para obter mais informações sobre como configurar a conexão de Área de Trabalho Remota, confira [Usando a Área de Trabalho Remota com as funções do Azure](https://msdn.microsoft.com/library/gg443832.aspx).

3. No portal clássico do Microsoft Azure, depois que a instância mostrar um status de **Pronto**, clique em uma das instâncias de função.

4. Clique no ícone **Conectar** na área **Acesso Remoto** da faixa de opções

5. Faça logon na máquina virtual usando as credenciais especificadas durante a configuração da Área de Trabalho Remota.

6. Abra um prompt de comando.

7. Digite `IPconfig`.

8. Observe o valor do Endereço IPV4.

9. Abra o Internet Explorer.

10. Digite o endereço e o nome do aplicativo Web. Por exemplo: `http://<IPV4 Address>/default.aspx`.

Navegar até o site retornará mensagens de erro mais explícitas.

* Erro de servidor no aplicativo '/'

* Descrição: ocorreu uma exceção sem tratamento durante a execução da solicitação da Web atual. Examine o rastreamento de pilha para obter mais informações sobre o erro e em que ponto ele ocorreu no código.

* Detalhes da exceção: System.IO.FIleNotFoundException: não foi possível carregar o arquivo ou assembly ‘Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’ ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado.

Por exemplo:

![Erro de servidor explícito no aplicativo '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## Diagnosticar problemas usando o Emulador de Computação

Você pode usar o emulador de computação do Microsoft Azure para diagnosticar e solucionar problemas de dependências ausentes e erros de web.config.

Para obter melhores resultados ao usar esse método de diagnóstico, você deve usar um computador ou uma máquina virtual com uma instalação limpa do Windows. Para simular melhor o ambiente do Azure, você deve usar o Windows Server 2008 R2 x64.

1. Instalar a versão autônoma do [SDK do Azure](https://azure.microsoft.com/downloads/)

2. No computador de desenvolvimento, compile o projeto do serviço de nuvem.

3. No Windows Explorer, navegue até a pasta bin\\debug do projeto do Serviço de Nuvem.

4. Copie a pasta .csx e o arquivo .cscfg no computador que você está usando para depurar os problemas.

5. No computador limpo, abra um Prompt de Comando do SDK do Azure e digite `csrun.exe /devstore:start`.

6. No Prompt de Comando, digite `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Quando a função for iniciada, você verá as informações de erro detalhadas no Internet Explorer. Você também pode usar ferramentas de solução de problemas padrão do Windows para ajudar a diagnosticar o problema.

## Diagnosticar problemas usando o IntelliTrace

Para as funções web e de trabalho que usam o .NET Framework 4, é possível usar o [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), que está disponível no [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Siga estas etapas para implantar o serviço com o IntelliTrace habilitado:

1. Confirme que o Azure SDK 1.3 ou superior está instalado.

2. Implante a solução usando o Visual Studio. Durante a implantação, verifique a caixa de seleção **Habilitar IntelliTrace para funções do .NET 4**.

3. Quando a instância for iniciada, abra o **Gerenciador de Servidores**.

4. Expanda o nó **Azure\\Serviços de Nuvem** e localize a implantação.

5. Expanda a implantação até ver as instâncias de função. Clique com o botão direito do mouse em uma das instâncias.

6. Escolha **Exibir logs do IntelliTrace**. O **Resumo do IntelliTrace** será aberto.

7. Localize a seção de exceções do resumo. Se houver exceções, elas serão rotuladas **Dados de Exceção**.

8. Expanda os **Dados de Exceção** e procure erros **System.IO.FileNotFoundException** semelhantes ao seguinte:

![Dados de exceção, arquivo ou assembly ausente](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## Lidando com DLLs e assemblies ausentes

Para resolver erros de DLL e assembly ausente, siga estas etapas:

1. Abra a solução no Visual Studio.

2. No **Gerenciador de Soluções**, abra a pasta **Referências**.

3. Clique no assembly identificado no erro.

4. No painel **Propriedades** e na propriedade Locate e Copy Local e defina o valor como **True**.

5. Implante novamente o serviço hospedado.

Depois de verificar que todos os erros foram corrigidos, o serviço pode ser implantado sem a configuração **Habilitar IntelliTrace para funções do .NET 4** marcada.

## Próximas etapas

Confira mais [artigos sobre solução de problemas](..\?tag=top-support-issue&service=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas das funções do Serviço de Nuvem usando os dados de diagnóstico do computador Azure PaaS, veja a [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0128_2016-->
<properties 
 pageTitle="Tamanhos dos serviços de nuvem" 
 description="Lista os diferentes tamanhos para funções web e de trabalho." 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="05/20/2015" 
 ms.author="adegeo"/>
 
# Tamanhos dos serviços de nuvem

## Visão geral 

Este tópico descreve os tamanhos disponíveis e as opções para instâncias de função de Serviço de Nuvem (funções web e funções de trabalho). Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos.

As Máquinas Virtuais e os Serviços de Nuvem do Azure são dois dos vários tipos de recursos de computação oferecidos pelo Azure. Para obter explicações, consulte [Opções de hospedagem de computação fornecidas pelo Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Para ver os limites relacionados do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

###Tamanhos de instâncias de função web e de trabalho

As considerações a seguir podem ajudá-lo a escolher um tamanho:

*   As instâncias agora podem ser configuradas para usar uma VM da série D. Elas são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, consulte o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

*   Funções web e funções de trabalho exigem mais espaço em disco temporário que as máquinas virtuais do Azure devido aos requisitos de sistema. Os arquivos do sistema reservam 4 GB de espaço para o arquivo de paginação do Windows e 2 GB de espaço para o arquivo de despejo de memória do Windows.

*   O disco do sistema operacional contém o SO Windows convidado e inclui a pasta Arquivos de Programas (incluindo as instalações feitas por meio de tarefas de inicialização, a menos que você especifique outro disco), as alterações no Registro, a pasta System32 e o .NET Framework.

*   O disco de recursos local contém logs e arquivos de configuração do Azure, diagnóstico do Azure (que inclui os logs do IIS) e quaisquer recursos de armazenamento local que você definir.

*   O disco de aplicativos é onde o .cspkg é extraído e inclui seu site, binários, processo de host de função, tarefas de inicialização, web.config e assim por diante.

*   Os tamanhos de máquina virtual A8/A10 e A9/A11 têm as mesmas capacidades. As instâncias de máquina virtual A8 e A9 incluem um adaptador de rede adicional que é conectado a uma rede de Acesso Remoto Direto à Memória (RDMA) para uma comunicação rápida entre máquinas virtuais. As instâncias A8 e A9 destinam-se a aplicativos de computação de alto desempenho que exigem comunicação constante e de baixa latência entre os nós durante a execução como, por exemplo, os aplicativos que usam MPI (Message Passing Interface). As instâncias de máquina virtual A10 e A11 não incluem o adaptador de rede adicional. As instâncias A10 e A11 destinam-se a aplicativos de computação de alto desempenho que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.

|Tamanho|CPU<br>núcleos|Memória|Tamanhos do disco|
|---|---|---|---|
|ExtraSmall|1|768 MB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 19 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Pequena|1|1,75 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 224 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Média|2|3,5 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 489 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Grande|4|7 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 999 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|ExtraLarge|8|14 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 2.039 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|A5|2|14 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 489 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|A6|4|28 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 999 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|A7|8|56 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 2.039 GB</p><p>Aplicativos = aprox. 1,5 GB</p>
|A8|8|56 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 1,77 TB</p><p>Aplicativos = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obter mais informações e considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|
|A9|16|<p>112 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 1,77 TB</p><p>Aplicativos = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obter mais informações e considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|
|A10|8|<p>56 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 1,77 TB</p><p>Aplicativos = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obter mais informações e considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|
|A11|16|<p>112 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 1,77 TB</p><p>Aplicativos = aprox. 1,5 GB</p><blockquote><p>[AZURE.NOTE]Para obter mais informações e considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|
|Standard_D1|1|3,5 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 50 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D2|2|7 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 100 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D3|4|14 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 200 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D4|8|28 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 400 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D11|2|14 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 100 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D12|4|28 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 200 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D13|8|56 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 400 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|
|Standard_D14|16|112 GB|<p>SO = tamanho do SO convidado</p><p>Recurso local = 800 GB</p><p>Aplicativos = aprox. 1,5 GB</p>|

### Consulte também

#### Conceitos

[Configurar um serviço de nuvem para o Azure](https://msdn.microsoft.com/library/hh124108)

[Configurar os tamanhos dos serviços de nuvem](https://msdn.microsoft.com/library/ee814754)

#### Outros recursos

[Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)

 [Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação](http://go.microsoft.com/fwlink/p/?linkid=328042)

 [Tamanhos das máquinas virtuais](../virtual-machines-size-specs.md)

<!---HONumber=58--> 
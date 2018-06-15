---
title: Personalizar a interface do usuário da solução Monitoramento Remoto – Azure | Microsoft Docs
description: Este artigo fornece informações sobre como acessar o código-fonte para a interface do usuário do acelerador da solução Monitoramento Remoto e fazer algumas personalizações.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: be20d45b380f66208884f15f4644f36f2a403837
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774043"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalizar o acelerador da solução Monitoramento Remoto

Este artigo fornece informações sobre como acessar o código-fonte e personalizar a interface do usuário do acelerador da solução Monitoramento Remoto. O artigo descreve:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do usuário

O código da interface do usuário do acelerador de solução Monitoramento Remoto é implementado usando a estrutura React.js. Você pode encontrar o código-fonte no repositório [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) do GitHub.

Para fazer alterações na interface do usuário, você pode executar uma cópia dela localmente. A cópia local se conecta a uma instância implantada de solução para executar ações como a recuperação de telemetria.

As etapas a seguir descrevem o processo de configurar um ambiente local para o desenvolvimento da interface do usuário:

1. Implante uma instância **básica** do acelerador de solução usando a CLI **pcs**. Anote o nome da sua implantação e as credenciais fornecidas para a máquina virtual. Para saber mais, veja [Implantar usando a CLI](iot-suite-remote-monitoring-deploy-cli.md).

1. Use o portal do Azure ou a [CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para habilitar o acesso SSH à máquina virtual que hospeda o microsserviços em sua solução. Por exemplo: 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Use o portal do Azure ou a [CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para localizar o nome e o endereço IP público de sua máquina virtual. Por exemplo: 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Use o SSH para se conectar à sua máquina virtual usando o endereço IP da etapa anterior e as credenciais fornecidas ao executar **pcs** para implantar a solução.

1. Para permitir que o UX local se conecta, execute os seguintes comandos no shell bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver que o comando foi concluído e o site da Web for iniciado, você poderá se desconectar da máquina virtual.

1. Em sua cópia local do repositório [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui), edite o arquivo **.env** para adicionar a URL da sua solução implantada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. No prompt de comando em sua cópia local da pasta `azure-iot-pcs-remote-monitoring-webui`, execute os seguintes comandos para instalar as bibliotecas necessárias e execute a interface do usuário localmente:

    ```cmd/sh
    npm install
    npm start
    ```

1. O comando anterior é executado localmente na interface do usuário em http://localhost:3000/dashboard. Você pode editar o código enquanto o site está em execução e vê-lo ser atualizado dinamicamente.

## <a name="customize-the-layout"></a>Personalizar o layout

Cada página na solução Monitoramento Remoto é composta de um conjunto de controles, conhecido como *painéis* no código-fonte. Por exemplo, a página **Painel** é composta por cinco painéis: Visão geral, Mapa, Alarmes, Telemetria e KPIs. Você pode encontrar o código-fonte que define cada página e seus painéis no repositório [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) do GitHub. Por exemplo, o código que define a página **Painel**, seu layout e os painéis na página está localizado na pasta [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Como os painéis gerenciam seus próprios layout e dimensionamento, você pode modificar facilmente o layout de uma página. Por exemplo, as alterações a seguir feitas no elemento **PageContent** no arquivo `src/components/pages/dashboard/dashboard.js` trocam as posições dos painéis de mapa e de telemetria, e alteram as larguras relativas dos painéis de mapa e de KPI:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Alterar layout do painel](media/iot-suite-remote-monitoring-customize/layout.png)

> [!NOTE]
> O mapa não está configurado na implantação local.

Você também pode adicionar várias instâncias do mesmo painel, ou diversas versões, se você [duplicar e personalizar um painel](#duplicate-and-customize-an-existing-control). O exemplo a seguir mostra como adicionar duas instâncias do painel de telemetria editando o arquivo `src/components/pages/dashboard/dashboard.js`:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Você pode exibir telemetria diferente em cada painel:

![Vários painéis de telemetria](media/iot-suite-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> O mapa não está configurado na implantação local.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicar e personalizar um controle existente

As etapas a seguir descrevem como usar o painel **alarmes** como um exemplo de como duplicar um painel existente, modificá-lo e usar a versão modificada:

1. Em sua cópia local do repositório, faça uma cópia da pasta **alarmes** na pasta `src/components/pages/dashboard/panels`. Chame a nova cópia de **cust_alarms**.

1. No arquivo **alarmsPanel.js** na pasta **cust_alarms**, edite o nome da classe como **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Adicione a seguinte linha ao arquivo `src/components/pages/dashboard/panels/index.js` :

    ```nodejs
    export * from './cust_alarms';
    ```

1. Substituir `AlarmsPanel` por `CustAlarmsPanel` no arquivo `src/components/pages/dashboard/dashboard.js`:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Você substituiu o painel **Alarmes** original por uma cópia chamada **CustAlarms**. Essa cópia é idêntica ao original. Agora você pode modificar a cópia. Por exemplo, para alterar a ordem das colunas no painel **Alarmes**:

1. Abra o arquivo `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` .

1. Modifique as definições de coluna conforme mostrado no seguinte trecho de código:

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

A captura de tela a seguir mostra a nova versão do painel **Alarmes**:

![Painel de alarmes atualizado](media/iot-suite-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalizar o gráfico de telemetria

O gráfico de telemetria na página **Painel** é definido pelos arquivos na pasta `src/components/pages/dashboard/panels/telemtry`. A interface do usuário recupera a telemetria de back-end da solução no arquivo `src/services/telemetryService.js`. As etapas a seguir mostram como alterar o período de tempo exibido no gráfico de telemetria de 15 minutos para 5 minutos:

1. No arquivo `src/services/telemetryService.js`, localize a função chamada **getTelemetryByDeviceIdP15M**. Faça uma cópia dessa função e modifique a cópia da seguinte maneira:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Para usar essa nova função para popular o gráfico de telemetria, abra o arquivo `src/components/pages/dashboard/dashboard.js`. Localize a linha que inicia o fluxo de telemetria e modifique-o da seguinte maneira:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

O gráfico de telemetria agora mostra os cinco minutos de dados de telemetria:

![Gráfico de telemetria mostrando um dia](media/iot-suite-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adicionar um novo KPI

A página **Painel** exibe os KPIs no painel **KPIs do Sistema KPIs**. Esses KPIs são calculados no arquivo `src/components/pages/dashboard/dashboard.js`. Os KPIs são renderizados pelo arquivo `src/components/pages/dashboard/panels/kpis/kpisPanel.js`. As etapas a seguir descrevem como calcular e processar um novo valor de KPI na página **Painel**. O exemplo mostrado é para adicionar uma nova alteração percentual ao KPI de alarmes de aviso:

1. Abra o arquivo `src/components/pages/dashboard/dashboard.js` . Modifique o objeto **initialState** para incluir uma propriedade **warningAlarmsChange** da seguinte maneira:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Modifique o objeto **currentAlarmsStats** para incluir **totalWarningCount** como uma propriedade:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Calcule o novo KPI. Localize o cálculo da contagem de alarmes críticos. Duplique o código e modifique a cópia da seguinte maneira:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Inclua o novo KPI **warningAlarmsChange** no fluxo de KPI:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Atualize os dados passados para o painel de KPIs:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Agora você concluiu as alterações no arquivo `src/components/pages/dashboard/dashboard.js`. As etapas a seguir descrevem as alterações a fazer no arquivo `src/components/pages/dashboard/panels/kpis/kpisPanel.js` para exibir o novo KPI:

1. Modifique a linha de código a seguir para recuperar o novo valor de KPI da seguinte maneira:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Modifique a marcação para exibir o novo valor do KPI da seguinte maneira:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

A página **Painel** agora exibe o novo valor de KPI:

![KPI de aviso](media/iot-suite-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalizar o mapa

Consulte a página [Personalizar mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) no GitHub para obter detalhes sobre os componentes de mapa na solução.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e de visualizações na solução Monitoramento Remoto, você pode editar o código. Os repositórios do GitHub relevantes são:

* [O microsserviço de configuração para soluções do Azure IoT (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [O microsserviço de configuração para soluções do Azure IoT (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Interface do usuário da Web do Monitoramento Remoto de PCS do Azure IoT](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a interface do usuário da Web no acelerador de solução Monitoramento Remoto.

Para obter mais informações conceituais sobre o acelerador da solução Monitoramento Remoto, veja [Arquitetura de Monitoramento Remoto](iot-suite-remote-monitoring-sample-walkthrough.md)

Para saber mais sobre como personalizar a solução Monitoramento Remoto, veja [Personalizar e reimplantar um microsserviço](iot-suite-microservices-example.md)
<!-- Next tutorials in the sequence -->
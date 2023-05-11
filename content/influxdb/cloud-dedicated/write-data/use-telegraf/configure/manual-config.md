---
title: Configure Telegraf for InfluxDB
seotitle: Configure Telegraf to write to InfluxDB
description: >
  Telegraf is a plugin-based agent with plugins that are enabled and configured in
  your Telegraf configuration file (`telegraf.conf`).
  Update existing or create new Telegraf configurations to use the `influxdb_v2`
  output plugin to write to InfluxDB.
  Start Telegraf using the custom configuration.
menu:
  influxdb_cloud_dedicated:
    name: Configure Telegraf
    parent: Use Telegraf
weight: 101
influxdb/cloud-dedicated/tags: [telegraf]
related:
  - /{{< latest "telegraf" >}}/plugins/
alt_engine: /influxdb/cloud/write-data/no-code/use-telegraf/manual-config/
aliases:
  - /influxdb/cloud-dedicated/write-data/use-telegraf/manual-config/
---

Use the Telegraf `influxdb_v2` output plugin to collect and write metrics into
an InfluxDB {{< current-version >}} database.
Learn how to enable the `influxdb_v2` output plugin in new and
existing Telegraf configurations,
and then start Telegraf using the custom configuration file.

{{% note %}}
_View the [requirements](/influxdb/cloud-dedicated/write-data/use-telegraf#requirements)
for using Telegraf with InfluxDB {{< current-version >}}._
{{% /note %}}

<!-- TOC -->

- [Configure Telegraf input and output plugins](#configure-telegraf-input-and-output-plugins)
  - [Add Telegraf plugins](#add-telegraf-plugins)
  - [Enable and configure the InfluxDB v2 output plugin](#enable-and-configure-the-influxdb-v2-output-plugin)
      - [urls](#urls)
      - [token](#token)
        - [Avoid storing tokens in telegraf.conf](#avoid-storing-tokens-in-telegrafconf)
      - [organization](#organization)
      - [bucket](#bucket)
      - [Write to InfluxDB v1.x and InfluxDB Cloud Dedicated](#write-to-influxdb-v1x-and-influxdb-cloud-dedicated)
- [Start Telegraf](#start-telegraf)

<!-- /TOC -->

## Configure Telegraf input and output plugins

Configure Telegraf input and output plugins in the Telegraf configuration file (typically named `telegraf.conf`).
Input plugins collect metrics.
Output plugins define destinations where metrics are sent.

_See [Telegraf plugins](/{{< latest "telegraf" >}}/plugins/) for a complete list of available plugins._

### Add Telegraf plugins

To add any of the available [Telegraf plugins](/{{< latest "telegraf" >}}/plugins/), follow the steps below.

1.  Find the plugin you want to enable from the complete list of available
    [Telegraf plugins](/{{< latest "telegraf" >}}/plugins/).
2.  Click **View** to the right of the plugin name to open the plugin page on GitHub.
    For example, view the [MQTT plugin GitHub page](https://github.com/influxdata/telegraf/blob/master/plugins/inputs/mqtt_consumer/README.md).
3.  Copy and paste the example configuration into your Telegraf configuration file
    (typically named `telegraf.conf`).

### Enable and configure the InfluxDB v2 output plugin

To send data to an InfluxDB {{< current-version >}} instance, enable the
[`influxdb_v2` output plugin](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/influxdb_v2/README.md)
in the `telegraf.conf`.

```toml
[[outputs.influxdb_v2]]
  urls = ["https://cluster-id.influxdb.io"]
  token = "${INFLUX_TOKEN}"
  organization = ""
  bucket = "${INFLUX_DATABASE}"
```

The InfluxDB output plugin configuration contains the following options:

##### urls

An array of URLs for your InfluxDB {{< current-version >}} instances.
To write to InfluxDB Cloud Dedicated, include your InfluxDB Cloud Dedicated cluster URL using the HTTPS protocol:

      ```toml
      ["https://cluster-id.influxdb.io"]
      ```

##### token

Your InfluxDB Cloud Dedicated [database token](/influxdb/cloud-dedicated/admin/tokens/) with _write_ permission to the database.
For information about viewing tokens, see [List tokens](/influxdb/cloud-dedicated/admin/tokens/list/).

In the example, **`INFLUX_TOKEN`** is an environment variable assigned to a [database token](/influxdb/cloud-dedicated/admin/tokens/) that has _write_ permission to the database.

{{% note %}}
###### Avoid storing tokens in `telegraf.conf`

We recommend storing your tokens in a secure secret store or by setting the `INFLUX_TOKEN` environment
variable and including the variable in your configuration file.

{{< tabs-wrapper >}}
{{% tabs %}}
[macOS or Linux](#)
[Windows](#)
{{% /tabs %}}

{{% tab-content %}}
```sh
export INFLUX_TOKEN=YourAuthenticationToken
```
{{% /tab-content %}}

{{% tab-content %}}

{{< code-tabs-wrapper >}}
{{% code-tabs %}}
[PowerShell](#)
[CMD](#)
{{% /code-tabs %}}

{{% code-tab-content %}}
```sh
$env:INFLUX_TOKEN = "YourAuthenticationToken"
```
{{% /code-tab-content %}}

{{% code-tab-content %}}
```sh
set INFLUX_TOKEN=YourAuthenticationToken
# Make sure to include a space character at the end of this command.
```
{{% /code-tab-content %}}
{{< /code-tabs-wrapper >}}

{{% /tab-content %}}
{{< /tabs-wrapper >}}

_See the [example `telegraf.conf` below](#example-influxdb_v2-configuration)._
{{% /note %}}

##### organization

For InfluxDB Cloud Dedicated, set this to an empty string (`""`).

##### bucket

The name of the InfluxDB Cloud Dedicated database to write data to.

In the example, **`INFLUX_DATABASE`** is an environment variable assigned to the [database](/influxdb/cloud-dedicated/admin/databases/) name.

{{% note %}}
##### Write to InfluxDB v1.x and InfluxDB Cloud Dedicated

If a Telegraf agent is already writing to an InfluxDB v1.x database,
enabling the InfluxDB v2 output plugin will write data to both v1.x and your {{< cloud-name >}} cluster.
{{% /note %}}

## Start Telegraf

Start the Telegraf service using the `--config` flag to specify the location of your `telegraf.conf`.

```sh
telegraf --config /path/to/custom/telegraf.conf
```

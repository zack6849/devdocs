---
group: cloud
title: Deploy variables
version: 2.2
github_link: cloud/env/variables-deploy.md
functional_areas:
  - Cloud
  - Configuration
---
The following _deploy_ variables control actions in the deploy phase and can inherit and override values from the [Global stage]({{page.baseurl}}/cloud/env/variables-intro.html#global-variables). Also, you can override the [ADMIN variables]({{page.baseurl}}/cloud/env/environment-vars_magento.html). Insert these variables in the `deploy` stage of the `.magento.env.yaml` file:

```yaml
stage:
  deploy:
    DEPLOY_VARIABLE_NAME: value
```

For more information about customizing the build and deploy process:

-  [Manage build and deploy actions]({{page.baseurl}}/cloud/project/magento-env-yaml.html)
-  [Deployment process]({{page.baseurl}}/cloud/reference/discover-deploy.html)

### `CACHE_CONFIGURATION`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

Configure Redis page and default caching. When setting the `cm_cache_backend_redis` parameter, you must specify the `server`, `port`, and `database` options.

```yaml
stage:
  deploy:
    CACHE_CONFIGURATION:
      frontend:
        default:
          backend: file
        page_cache:
          backend: file
```

By default, the deployment process overwrites all settings in the `env.php` file.

### `CLEAN_STATIC_FILES`

-  **Default**—`true`
-  **Version**—Magento 2.1.4 and later

Cleans the [generated static view files]({{page.baseurl}}/config-guide/cli/config-cli-subcommands-static-view.html#config-cli-static-overview) when you perform an action such as enabling or disabling a component. We recommend the default value _true_ in development. The supported values are `true` and `false`.

```yaml
stage:
  deploy:
    CLEAN_STATIC_FILES: false
```

Failure to clear static view files might result in issues if there are multiple files with the same name and you do not clear all of them. Because of [static file fallback]({{page.baseurl}}/howdoi/clean_static_cache.html) rules, if you do not clear static files and there is more than one file named `logo.gif` that are different, fallback might cause the wrong file to display.

### `CRON_CONSUMERS_RUNNER`

-  **Default**—`cron_run = false`, `max_messages = 1000`
-  **Version**—Magento 2.2.0 and later

Use this environment variable to make sure message queues are running after a deployment. 

-   `cron_run`—A boolean value that enables or disables the `consumers_runner` cron job (default = `false`).
-   `max_messages`—A number specifying the maximum number of messages each consumer must process before terminating (default = `1000`). Although we do not recommend it, you can use `0` to prevent the consumer from terminating.
-   `consumers`—An array of strings specifying which consumer(s) to run. An empty array runs _all_ consumers. Refer to [List consumers]({{page.baseurl}}/config-guide/mq/manage-mysql.html#list-consumers) for more information.

```yaml
stage: 
  deploy:
    CRON_CONSUMERS_RUNNER:
      cron_run: true,
      max_messages: 1000,
      consumers:
        - consumer1
        - consumer2
```

By default, the deployment process overwrites all settings in the `env.php` file. Refer to [Manage message queues]({{page.baseurl}}/config-guide/mq/manage-mysql.html) for more information about how this works in {{site.data.var.ce}} and {{site.data.var.ee}}.

### `CRYPT_KEY`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

Use the Project Web UI to set this value. When you move the database from one environment to another without an installation process, you need the corresponding cryptographic information. Magento uses the encryption key value set in the Web UI as the `crypt/key` value in the `env.php` file. This does not overwrite an existing encryption key value in the `env.php` file.

### `MYSQL_USE_SLAVE_CONNECTION`

-  **Default**—`false`
-  **Version**—Magento 2.1.4 and later

Magento can read multiple databases asynchronously. Set to `true` to automatically use a _read-only_ connection to the database to receive read-only traffic on a non-master node. This improves performance through load balancing, because only one node needs to handle read-write traffic. Set to `false` to remove any existing read-only connection array from the `env.php` file.

```yaml
stage:
  deploy:
    MYSQL_USE_SLAVE_CONNECTION: true
```

### `QUEUE_CONFIGURATION`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

Use this environment variable to retain customized AMQP service settings between deployments. For example, if you prefer using an existing message queue service, like RabbitMQ, instead of relying on {{site.data.var.ece}} to create it for you, use the `QUEUE_CONFIGURATION` environment variable to connect it to your site:

```yaml
stage:
  deploy:
    QUEUE_CONFIGURATION:
      amqp:
        host: test.host
        port: 1234
      amqp2:
        host: test.host2
        port: 12345
      mq:
        host: mq.host
        port: 1234
```

By default, the deployment process overwrites all settings in the `env.php` file.

### `REDIS_USE_SLAVE_CONNECTION`

-  **Default**—`false`
-  **Version**—Magento 2.1.4 and later

Magento can read multiple Redis instances asynchronously. Set to `true` to automatically use a _read-only_ connection to a Redis instance to receive read-only traffic on a non-master node. This improves performance through load balancing, because only one node needs to handle read-write traffic. Set to `false` to remove any existing read-only connection array from the `env.php` file.

```yaml
stage:
  deploy:
    REDIS_USE_SLAVE_CONNECTION: true
```

You must have a Redis service configured in the `.magento.app.yaml` file and in the `services.yaml` file.

The read-only connection is not available for use in the Integration environment or if you use the [`CACHE_CONFIGURATION` variable](#cache_configuration).

### `SCD_COMPRESSION_LEVEL`

-  **Default**—`6`
-  **Version**—Magento 2.1.4 and later

Specifies which [gzip](https://www.gnu.org/software/gzip){:target="\_blank} compression level (`0` to `9`) to use when compressing static content; `0` disables compression.

```yaml
stage:
  deploy:
    SCD_COMPRESSION_LEVEL: 4
```

### `SCD_EXCLUDE_THEMES`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

Themes can include numerous files. Set this variable to `true` if you want to skip copying over theme files during deployment. For example, the Luma theme is included with {{site.data.var.ece}}. You may not need to constantly deploy this theme with your code updates and deployments. To exclude the `magento/luma` theme:

```yaml
stage:
  deploy:
    SCD_EXCLUDE_THEMES: "magento/luma, magento/my-theme" 
```

### `SCD_STRATEGY`

-  **Default**—`quick`
-  **Version**—Magento 2.2.0 and later

Allows you to customize the [deployment strategy](http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-static-deploy-strategies.html) for static content. See [Deploy static view files](http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-static-view.html).

Use these options _only_ if you have more than one locale:

-  `standard`—deploys all static view files for all packages.
-  `quick`—minimizes deployment time. This is the default command option, if not specified.
-  `compact`—conserves disk space on the server and overrides the value for `scd_threads` with a value of `1`. This strategy does not work with multi-threads.

```yaml
stage:
  deploy:
    SCD_STRATEGY: "compact"
```

### `SCD_THREADS`

-  **Default**: 
    -  `1`—Starter environments and Pro Integration environments
    -  `3`—Pro Staging and Production environments
-  **Version**—Magento 2.1.4 and later

Sets the number of threads for static content deployment. Increasing the number of threads speeds up static content deployment; decreasing the number of threads slows it down.

```yaml
stage:
  deploy:
    SCD_THREADS: 2
```

To further reduce deployment time, we recommend using [Configuration Management]({{page.baseurl}}/cloud/live/sens-data-over.html) with the `scd-dump` command to move static deployment into the build phase.

### `SEARCH_CONFIGURATION`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

Use this environment variable to retain customized search service settings between deployments. For example:

```yaml
stage:
  deploy:
   SEARCH_CONFIGURATION:
     engine: elasticsearch
     elasticsearch_server_hostname: hostname
     elasticsearch_server_port: '123456'
     elasticsearch_index_prefix: magento
     elasticsearch_server_timeout: '15'
```

By default, the deployment process overwrites all settings in the `env.php` file. 

### `SESSION_CONFIGURATION`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

Configure Redis session storage. You must specify the `save`, `redis`, `host`, `port`, and `database` options for the session storage variable. For example:

```yaml
stage: 
  deploy:
    SESSION_CONFIGURATION: 
      redis: 
        bot_first_lifetime: 100
        bot_lifetime: 10001
        database: 0
        disable_locking: 1
        host: redis.internal
        max_concurrency: 10
        max_lifetime: 10001
        min_lifetime: 100
        port: 6379
      save: redis
```

By default, the deployment process overwrites all settings in the `env.php` file. 

### `SKIP_SCD`

-  **Default**— _Not set_
-  **Version**—Magento 2.1.4 and later

Set to `true` to skip static content deployment during the deploy phase.

We recommend using this option, because running static content deployment during the deployment phase can greatly increase deployment times and downtime for your live site.

```yaml
stage:
  deploy:
    SKIP_SCD: true
```

### `STATIC_CONTENT_SYMLINK`

-  **Default**—`true`
-  **Version**—Magento 2.1.4 and later

Generates symlinks for static content. This setting is vital in the Pro Production environment for the three-node cluster. When this variable is set to `false`, it must copy every file during the deployment, which increases deployment time. Setting `SCD_ON_DEMAND` to `true` disables this variable.

```yaml
stage:
  deploy:
    STATIC_CONTENT_SYMLINK: false
```

### `UPDATE_URLS`

-  **Default**—`true`
-  **Version**—Magento 2.1.4 and later

On deployment, replace Magento base URLs in the database with project URLs. This is useful for local development, where base URLs are set up for your local environment. When you deploy to a Cloud environment, we change the URLs so you can access your storefront and Magento Admin using project URLs.

```yaml
stage:
  deploy:
    UPDATE_URLS: false
```

You should set this variable to `false` _only_ in Staging or Production environments, where the base URLs cannot change. For Pro, we already set this to `false` for you.

### `VERBOSE_COMMANDS`

-  **Default**—_Not set_
-  **Version**—Magento 2.1.4 and later

 Enables or disables the [Symfony](https://symfony.com/doc/current/console/verbosity.html){:target="\_blank"} debug verbosity level for your logs. Choose the level of detail provided in the logs: `-v`, `-vv`, or `-vvv`.

```yaml
stage:
  deploy:
    VERBOSE_COMMANDS: "-vv"
```
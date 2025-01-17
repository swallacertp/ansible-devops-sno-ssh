suite_app_config
================

This role is used to configure specific components of the application workspace after the application has been installed in the Maximo Application Suite.

Role Variables
--------------

### mas_instance_id
Defines the instance id that was used for the MAS installation

### mas_app_id
Defines the kind of application that is intended for installation such as `assist`, `health`, `iot`, `manage`, `monitor`, `mso`, `predict`, or `safety`

### mas_workspace_id
MAS application workspace to use to configure app components

### mas_config_dir
Optional. Local directory where generated resource definitions are saved into. It is used by current role to retrieve the id of the analytics project eventually created by [cp4d_service](cp4d_service.md) role and then configure it into Health & Predict - Utilities resource.

- Optional, only supported when `mas_app_id` = `hputilities` and `cpd_ws_project_name` is informed.
- Environment Variable: `MAS_CONFIG_DIR`
- Default Value: None

### mas_appws_components
Defines the app components and versions to configure in the application workspace. Takes the form of key=value pairs seperated by a comma i.e. To install health within Manage set `base=latest,health=latest`

- Environment Variable: `MAS_APPWS_COMPONENTS`
- Default:
  For Manage the default is:
    `base=latest`

  For Health (standalone) the default is:
    `health=latest`

### mas_app_ws_spec
Optional.  The application workspace deployment spec used to configure various aspects of the application workspace configuration. Note that use of this will override anything set in `mas_appws_components`

- Environment Variable: `MAS_APP_WS_SPEC`
- Default: defaults are specified in `vars/defaultspecs/{{mas_app_id}}.yml`

Role Variables - Health & Predict Utilities
---------------------------------------------

### cpd_ws_project_id
Optional. It is the id of the analytics project created in Watson Studio and used to configure `hputilities` application.

- Only supported when `mas_app_id` = `hputilities`
- Environment Variable: `CPD_WS_PROJECT_ID`
- Default: None

### cpd_ws_project_name
Optional. It specifies the name of the file in `mas_config_dir` where the id of the analytics project is saved.

- Only supported when `mas_app_id` = `hputilities` and `mas_config_dir` is informed.
- Environment Variable: `CPD_WS_PROJECT_NAME`
- Default Value: `wsl-mas-${mas_instance_id}-hputilities`

Role Variables - Manage
---------------------------------------------

### mas_app_settings_aio_flag
Optional. Flag indicating if Asset Investment Optimization (AIO) resource must be loaded or not. It can be loaded only when Optimizer application is installed.

- Only supported when Optimizer application is installed.
- Environment Variable: `MAS_APP_SETTINGS_AIO_FLAG`
- Default: `true`

### mas_app_settings_db2_schema
Optional. Name of the schema where Manage database lives in.

- Environment Variable: MAS_APP_SETTINGS_DB2_SCHEMA') | 
- Default: `maximo`

### mas_app_settings_demodata
Optional. Flag indicating if manage demodata should be loaded or not.

- Environment Variable: `MAS_APP_SETTINGS_DEMODATA`
- Default: `false` (do not load demodata)

### mas_app_settings_persistent_volumes_flag
Optional. Flag indicating if persistent volumes should be configured by default during Manage Workspace activation.
There are two defaulted File Storage Persistent Volumes Claim resources that will be created out of the box for Manage if this flag is set to `true`:

- `/DOCLINKS`: Persistent volume used to store attachments and JMS queues related artifacts (`/DOCLINKS/jmsstore`).
- `/bim`: Persistent volume used to store Building Information Models related artifacts (models, docs and import)

- Environment Variable: `MAS_APP_SETTINGS_PERSISTENT_VOLUMES_FLAG`
- Default: `false`

### mas_app_settings_base_language
Optional. Provide the base language for Manage application.
For a full list of supported languages for Manage application and its corresponding language codes, please refer to [Language Support](https://www.ibm.com/docs/en/maximo-manage/continuous-delivery?topic=deploy-language-support) documentation.

- Environment Variable: `MAS_APP_SETTINGS_BASE_LANG`
- Default: `EN` (English)

### mas_app_settings_secondary_languages
Optional. Provide a list of additional secondary languages for Manage application. 

Note: The more languages you add, the longer Manage will take to install and activate.

Export the `MAS_APP_SETTINGS_SECONDARY_LANGS` variable with the language codes as comma-separated values.
For a full list of supported languages for Manage application and its corresponding language codes, please refer to [Language Support](https://www.ibm.com/docs/en/maximo-manage/continuous-delivery?topic=deploy-language-support) documentation.

- Environment Variable: `MAS_APP_SETTINGS_SECONDARY_LANGS`
- Default: None

 For example, use the following to enable Manage application with Arabic, Deutsch and Japanese as secondary languages:
`export MAS_APP_SETTINGS_SECONDARY_LANGS='AR,DE,JA'`

### mas_app_settings_server_bundles_size
Optional. Provides different flavors of server bundle configuration to handle workload for Manage application.
For more details about Manage application server bundle configuration, refer to [Setting the server bundles for Manage application](https://www.ibm.com/docs/en/maximo-manage/continuous-delivery?topic=manage-setting-server-bundles).

Currently supported server bundle sizes are:

- `dev` - Deploys Manage with the default server bundle configuration.
  - i.e just 1 bundle pod handling `all` Manage application workload.
- `small` - Deploys Manage with the most common deployment configuration. 
  - i.e 4 bundle pods, each one handling workload for each main capabilities: `mea`, `cron`, `report` and `ui`
- `jms` - Can be used for Manage 8.4 and above. Same server bundle configuration as `small` and includes `jms` bundle pod.
  - Enabling JMS pod workload will also configure Manage to use default JMS messaging queues to be stored in `DOCLINKS/jmsstore` persistent volume (Requires `mas_app_settings_persistent_volumes_flag` to be set to `true`)

- Environment Variable: `MAS_APP_SETTINGS_SERVER_BUNDLES_SIZE`
- Default: `dev`

Role Variables - Predict
---------------------------------------------

### mas_app_settings_wml_instance_id
Optional. Identifier of wml instance to be configured in Predict

- Environment Variable: `WML_INSTANCE_ID`
- Default: `openshift`

### mas_app_settings_wml_url
Optional. Url to access WML service (same as Cloud Pak for Data url)

- Environment Variable: `WML_URL`
- Default: `https://internal-nginx-svc.ibm-cpd.svc:12443` (it assumes cpd wml is installed in a namespace called `ibm-cpd`)

### cpd_product_version
Required when `mas_app_settings_wml_version`is not informed. It is the version of cloud pak for data installed in the cluster. Based on that wml version will be identified. Recommend use of this variable instead of forcing wml version by setting `mas_app_settings_wml_version`

Versions supported are `4.0` and `4.5`

- Environment Variable: `CPD_PRODUCT_VERSION`
- Default: None

### mas_app_settings_wml_version
Required when `cpd_product_version`is not informed. Is the version of WML to use.

- Environment Variable: `WML_VERSION`
- Default: None

### mas_app_settings_predict_deployment_size
Optional. Controls the workload size of predict containers. Avaliable options are `developer`, `small`, `medium` and `small`

    | Deployment_size        | Replica |
    | ---------------------- | :--: | 
    | developer              |  1 | 
    | small                  |  2 |
    | medium                 |  3 |

- Environment Variable: `PREDICT_DEPLOYMENT_SIZE`
- Default: `small`

### custom_labels
Optional. List of comma separated key=value pairs for setting custom labels on instance specific resources.

- Environment Variable: `CUSTOM_LABELS`
- Default: None


Example Playbook
----------------

```yaml
- hosts: localhost
  any_errors_fatal: true
  vars:
    # MAS configuration
    mas_instance_id: "{{ lookup('env', 'MAS_INSTANCE_ID') }}"

    # MAS workspace configuration
    mas_workspace_id: "{{ lookup('env', 'MAS_WORKSPACE_ID') }}"

    # MAS application configuration
    mas_app_id: "{{ lookup('env', 'MAS_APP_ID') }}"

    mas_app_ws_spec:
      bindings:
        jdbc: "{{ mas_appws_jdbc_binding | default( 'system' , true) }}"

  roles:
    - ibm.mas_devops.suite_app_config
```

License
-------

EPL-2.0

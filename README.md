# Symfony bundle for integration with Vault by HashiCorp 
### Badges
[![Build Develop Status](https://travis-ci.org/4xxi/vault-bundle.svg?branch=develop)](https://travis-ci.org/4xxi/vault-bundle)

[![SensioLabsInsight](https://insight.sensiolabs.com/projects/d004e03d-ee45-448f-a897-a55fc1522fde/big.png)](https://insight.sensiolabs.com/projects/d004e03d-ee45-448f-a897-a55fc1522fde)

### Full configuration
```yaml
fourxxi_vault:
    enabled: true
    auth:
        token: "571f9ef0-583b-cf7a-81af-191c43515c8e"
    connection:
        schema: http
        host: 127.0.0.1
        port: 8200
        api_version: 'v1'
```

### Examples
```yaml
    app.vault.mapper.yaml:
        class: Fourxxi\Bundle\VaultBundle\ParameterMapper\YamlParameterMapper
        arguments: ["value"]

    app.vault.mapper.simple:
        class: Fourxxi\Bundle\VaultBundle\ParameterMapper\SimpleParameterMapper

    # for cached parameters
    app.vault.parameter_provider.elasticsearch:
        class: Fourxxi\Bundle\VaultBundle\ParameterProvider\VaultParameterProvider
        factory: "fourxxi_vault.parameter_provider.vault_factory:create"
        arguments: ["secret/elasticsearch", "@app.vault.mapper.yaml"]
        tags:
            - { name: fourxxi_vault.cached_parameters }

    app.vault.parameter_provider.mysql:
        class: Fourxxi\Bundle\VaultBundle\ParameterProvider\VaultParameterProvider
        factory: "fourxxi_vault.parameter_provider.vault_factory:create"
        arguments: ["secret/mysql", "@app.vault.mapper.simple"]
        tags:
            - { name: fourxxi_vault.cached_parameters }
    
    # For dynamic parameters
    app.vault.enabled_parameter_provider.elasticsearch:
        class: Fourxxi\Bundle\VaultBundle\ParameterProvider\VaultParameterProvider
        factory: "fourxxi_vault.parameter_provider.vault_factory:create"
        arguments: ["secret/elasticsearch", "@app.vault.mapper.yaml"]
        tags:
            - { name: fourxxi_vault.enabled_parameter_provider, provider_name: 'el' }

    app.vault.disabled_parameters_provider.elasticsearch:
        class: Fourxxi\Bundle\VaultBundle\ParameterProvider\SimpleParameterProvider
        arguments: ["@=service('service_container').getParameterBag()"]
        tags:
            - { name: fourxxi_vault.disabled_parameter_provider, provider_name: 'el' }

    app.vault.enabled_parameter_provider.mysql:
        class: Fourxxi\Bundle\VaultBundle\ParameterProvider\VaultParameterProvider
        factory: "fourxxi_vault.parameter_provider.vault_factory:create"
        arguments: ["secret/mysql", "@app.vault.mapper.simple"]
        tags:
            - { name: fourxxi_vault.enabled_parameter_provider, provider_name: 'mysql' }

    app.vault.disabled_parameters_provider.mysql:
        class: Fourxxi\Bundle\VaultBundle\ParameterProvider\SimpleParameterProvider
        arguments: ["@=service('service_container').getParameterBag()"]
        tags:
            - { name: fourxxi_vault.disabled_parameter_provider, provider_name: 'mysql' }

    app.test.elasticsearch:
        class: App\Test
        public: true
        arguments:
            - "@=v('el','elasticsearch_host')"
            - "@=vault('el','elasticsearch_password')"
                        
    app.test.mysql:
        class: App\Test
        public: true
        arguments:
            - "@=fourxxi_vault('mysql', 'database_pass')"
            - "@=service('fourxxi_vault.parameter_getter').get('mysql','database_host')"
```

### Running unit tests:
```bash
./vendor/bin/phpunit --testsuite unit
```

### Running functional tests:

Run vault:
```bash
docker run -d -p 8200:8200 --cap-add=IPC_LOCK -e 'VAULT_DEV_ROOT_TOKEN_ID=f29e2a2f-26ac-a182-b7a9-05be2381e200' vault
```

Run tests for Symfony 2.8
```bash
./vendor/bin/simple-phpunit --testsuite functional
```

Run tests for Symfony >= 3
```bash
./vendor/bin/phpunit --testsuite functional
```

### @todo

1. Adding more tests
2. Writing documentation

# Strange config behavior reproducer

## Install dependencies
```bash
> composer install
```

## Debug `dev` configuration:
```bash
> bin/console debug:config security --env dev

Current configuration for extension with alias "security"
=========================================================

security:
    providers:
        users_in_memory:
            memory:
                users: {  }
    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
            methods: {  }
            user_checker: security.user_checker
            stateless: false
            lazy: false
        main:
            anonymous:
                lazy: false
                secret: null
            lazy: true
            provider: users_in_memory
            methods: {  }
            security: true
            user_checker: security.user_checker
            stateless: false
    access_decision_manager:
        strategy: affirmative
        allow_if_all_abstain: false
        allow_if_equal_granted_denied: true
    access_control:
        -
            path: ^/admin
            roles:
                - ROLE_ADMIN
            requires_channel: null
            host: null
            port: null
            ips: {  }
            methods: {  }
            allow_if: null
    access_denied_url: null
    session_fixation_strategy: migrate
    hide_user_not_found: true
    always_authenticate_before_granting: false
    erase_credentials: true
    enable_authenticator_manager: false
    encoders: {  }
    role_hierarchy: {  }
```

## Debug `prod` configuration:
```bash
> bin/console debug:config security --env prod

Current configuration for extension with alias "security"
=========================================================

security:
    providers:
        users_in_memory:
            memory:
                users: {  }
    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
            methods: {  }
            user_checker: security.user_checker
            stateless: false
            lazy: false
        main:
            anonymous:
                lazy: false
                secret: null
            lazy: true
            provider: users_in_memory
            methods: {  }
            security: true
            user_checker: security.user_checker
            stateless: false
    access_decision_manager:
        strategy: unanimous
        allow_if_all_abstain: false
        allow_if_equal_granted_denied: true
    access_denied_url: null
    session_fixation_strategy: migrate
    hide_user_not_found: true
    always_authenticate_before_granting: false
    erase_credentials: true
    enable_authenticator_manager: false
    encoders: {  }
    access_control: {  }
    role_hierarchy: {  }
```

## Expected behavior
The `security` component is configured by the following files:
- [/config/packages/security.yaml](/config/packages/security.yaml)
- [/config/packages/dev/security.yaml](/config/packages/dev/security.yaml)

The `access_decision_manager.strategy` configuration is set by the general configuration file so I expect both environments to have the `unanimous` value:
```yaml
# ...
    access_decision_manager:
        strategy: unanimous
# ...
```

But `dev` env has the default `affirmative` set (it's probably caused by [the default configuration](https://github.com/symfony/symfony/blob/5.x/src/Symfony/Bundle/SecurityBundle/DependencyInjection/MainConfiguration.php#L63) being merged between the multiple files).
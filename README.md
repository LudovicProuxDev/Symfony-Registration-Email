﻿# Symfony-Registration-Email

This is a Symfony project with registration, email verification and reset password.

The follow instructions build the project with the main commands.

1. Create database in MySQL
```bash
symfony console doctrine:database:create
```

2. Create user for authentication and security
```bash
symfony console make:user
```

We confirm all instructions by default to create the User.

3. Add fields dor the User
```bash
symfony console make:entity User
```

We add **surname** and **firstname**.

4. Make the first migration for the database 
```bash
symfony console make:migration
symfony console doctrine:migrations:migrate
```

5. Make a controller to show the user profile
```bash
symfony console make:controller Profile
```

6. Create the form login
```bash
symfony console make:security:form-login
```

Change the route from *login* function in *SecurityController*

Before
```php
// src/Controller/SecurityController.php
<?php

namespace App\Controller;

// ...

class SecurityController extends AbstractController
{
    #[Route(path: '/login', name: 'app_login')]
    public function login(AuthenticationUtils $authenticationUtils): Response
    {
        // ...
    }
    // ...
}
```

After
```php
// src/Controller/SecurityController.php
<?php

namespace App\Controller;

// ...

class SecurityController extends AbstractController
{
    #[Route(path: '/', name: 'app_login')]
    public function login(AuthenticationUtils $authenticationUtils): Response
    {
        // ...
    }
    // ...
}
```

7. Make the registration form with the email verification
```bash
composer require symfonycasts/verify-email-bundle
symfony console make:registration-form
```

We confirm all instructions by default until the adress to send registration.

- Give your email adress
```bash
What email address will be used to send registration confirmations? (e.g. mailer@your-domain.com):
```

- Give a name associated to the email adress
```bash
What "name" should be associated with that email address? (e.g. Acme Mail Bot):
```

- Disable the authentication after registration
```bash
Do you want to automatically authenticate the user after registration? (yes/no) [yes]: > no
```

- Select the **app_profile** route for the redirection after registration
```bash
What route should the user be redirected to after registration?
```

8. Make a new migration for the database 
```bash
symfony console make:migration
symfony console doctrine:migrations:migrate
```

9. Add the Google Mailer for Symfony Mailer
```bash
composer require symfony/google-mailer
```

Change the **symfony/google-mailer** section into the *.env* file.

We need to create a [Google App Password](https://myaccount.google.com/apppasswords?continue=https://myaccount.google.com/security) to send emails. Create the Google app and get the password to complete the config.

```bash
###> symfony/google-mailer ###
# Gmail SHOULD NOT be used on production, use it in development only.
MAILER_DSN=gmail+smtp://youraddress@gmail.com:apppassword@smtp.gmail.com:587
###< symfony/google-mailer ###
```

10. Add access control for the Security of the project
```yaml
# config/packages/security.yaml
# ...
    access_control:
            # - { path: ^/admin, roles: ROLE_ADMIN }
            # - { path: ^/profile, roles: ROLE_USER }
            - { path: ^/profile, roles: IS_AUTHENTICATED_FULLY }
# ...
```

11. Add **surname** and **firstname** fields from the user
- update *RegistrationFormType.php*
- update *register.html.twig*

12. Run the app and run the messenger queue in an other console
```bash
symfony server:start
```
```bash
symfony console messenger:consume async
```

13. Add an account with **/register** route, check your mailbox with the email verification and click on the link to confirm the email.

14. Add the option to reset the password
```bash
composer require symfonycasts/reset-password-bundle
symfony console make:reset-password
```

- Make a new migration for the database 
```bash
symfony console make:migration
symfony console doctrine:migrations:migrate
```

- Add the forgot password link on the login form in the *login.html.twig*
```twig
# templates/security/login.html.twig
# ...
<a href="{{ path('app_forgot_password_request') }}">Reset password</a>
# ...
```

- Test **/reset-password** route, submit the form to reset the password, check your mailbox and click on the link to reset the password with a strong password.

15. Run messenger queue if the email is not in the mailbox
```bash
symfony console messenger:consume async
```

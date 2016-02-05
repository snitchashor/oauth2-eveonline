# Eve Online Provider for OAuth 2.0 Client

This package provides Eve Online OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require evelabs/oauth2-eveonline
```

## Docs

[Eve Online CREST&SSO 3rd party documentation](http://eveonline-third-party-documentation.readthedocs.org/en/latest/crest/authentication/)

## Usage

Usage is the same as The League's OAuth client, using `\Evelabs\OAuth2\Client\Provider\EveOnline` as the provider.

See `example/example.php` for more insight.

### Authorization Code Flow

```php
$provider = new Evelabs\OAuth2\Client\Provider\EveOnline([
    'clientId'          => '{eveonline-client-id}',
    'clientSecret'      => '{eveonline-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url',
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getCharacterName());

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

### Managing Scopes

When creating your EveOnline authorization URL, you can specify the scopes your application may authorize.

```php
$options = [
    'scope' => ['publicData','characterLocationRead'] // array or string
];

$authorizationUrl = $provider->getAuthorizationUrl($options);
```

## Testing

``` bash
$ ./vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](https://github.com/thephpleague/oauth2-linkedin/blob/master/CONTRIBUTING.md) for details.


## Credits

- [Oleg Krasavin](https://github.com/okwinza)
- [All Contributors](https://github.com/evelabs/oauth2-eveonline/contributors)


## License

The MIT License (MIT). Please see [License File](https://github.com/evelabs/oauth2-eveonline/blob/master/LICENSE) for more information.

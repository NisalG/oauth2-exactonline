# Exact Online Provider for OAuth 2.0 Client
[![Build Status](https://travis-ci.org/PBXg33k/oauth2-exactonline.svg)](https://travis-ci.org/PBXg33k/oauth2-exactonline)

This package provides support for authenticating with Exact Online's OAuth2 provider using PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Demo
Click [here](http://oauth2-exactonline.demos.oguzhanuysal.eu/ "OAuth2 for Exact Online demo") to see an online demonstration which allows you to authenticate using an Exact account.

## Installation

To install, use composer:

```
composer require pbxg33k/oauth2-exact-provider
```

## Usage

Usage is the same as The League's OAuth client, using `Pbxg33k\OAuth2\Client\Provider\Exactonline` as the provider.

### Authorization Code Flow

```php
$provider = new Pbxg33k\OAuth2\Client\Provider\Exactonline([
    'clientId'          => '{exact-client-id}',
    'clientSecret'      => '{exact-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url'
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->state;
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
        $userDetails = $provider->getUserDetails($token);

        // Use these details to create a new profile
        printf('Hello %s!', $userDetails->firstName);

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->accessToken;
}
```

## Refreshing a Token

```php
$provider = new Pbxg33k\OAuth2\Client\Provider\Exactonline([
    'clientId'          => '{zenpayroll-client-id}',
    'clientSecret'      => '{zenpayroll-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url'
]);

$grant = new \League\OAuth2\Client\Grant\RefreshToken();
$token = $provider->getAccessToken($grant, ['refresh_token' => $refreshToken]);
```

# Laravel Social Caster

![Laravel Social Caster publishing network](art/banner.png)

Publish content to supported social platforms through one Laravel API. The
package validates content and account compatibility before making platform
requests.

[![Tests](https://github.com/eloquage/laravel-social-caster/actions/workflows/run-tests.yml/badge.svg)](https://github.com/eloquage/laravel-social-caster/actions/workflows/run-tests.yml)
[![Latest Version](https://img.shields.io/packagist/v/illuma-law/laravel-social-caster)](https://packagist.org/packages/illuma-law/laravel-social-caster)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Requires PHP 8.3+ and Laravel 11, 12, or 13. Supported platforms are X,
LinkedIn, Facebook, Instagram, Threads, and TikTok. Each platform requires its
own developer application, access scopes, and credentials.

## Install

```bash
composer require illuma-law/laravel-social-caster
php artisan vendor:publish --tag=social-caster-config
```

## Implement the contracts

Your content object implements `PublishableContent`; your account model
implements `SocialCredentials`. Keep access tokens in protected storage and
return the token and provider metadata from the credential methods.

```php
use Illuma\SocialCaster\Facades\SocialCaster;

// $post and $account are your application objects implementing the contracts.
$errors = SocialCaster::validate($post, $account);

if ($errors !== []) {
    throw new InvalidArgumentException(implode(' ', $errors));
}

$result = SocialCaster::publish($post, $account);

// Store the platform response details returned by the API.
$externalId = $result->externalId;
$rawResponse = $result->rawResponse;
```

`PublishableContent` supplies a platform, body, title, optional image/video,
and metadata. `SocialCredentials` supplies the platform, access tokens,
provider user ID, and provider-specific metadata. Publishing errors are thrown
as exceptions; handle them in your queue or application boundary.

## Configure limits

The published `config/social-caster.php` file controls preflight character
limits and LinkedIn's default visibility. Use it to tune content validation
for your application's publishing rules.

## Test

```bash
composer test
composer analyse
vendor/bin/pint --test
```

MIT licensed. See [LICENSE](LICENSE).

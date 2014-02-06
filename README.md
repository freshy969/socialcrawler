# SocialCrawler

SocialCrawler is a PHP library for retrieving images and videos posts from the most popular social networks if they contains specific hashtags. It currently supports the following social networks:

- Facebook
- Instagram
- Twitter


## Installation

The recommended way to install SocialCrawler is through [Composer](http://getcomposer.org).

```bash
# Install Composer
curl -sS https://getcomposer.org/installer | php
```

Then add SocialCrawler as a dependency in your `composer.json` file.
```javascript
"repositories": [
    {
        "type": "vcs",
        "url": "git@github.com:heavenconseil/socialcrawler.git"
    }
],
"require": {
    "heavenconseil/socialcrawler": "dev-master"
}
```

After installing, you need to require Composer's autoloader:

```php
require __DIR__ . '/vendor/autoload.php';
```

## Example usage

```php
<?php

require __DIR__ . '/vendor/autoload.php';

use SocialCrawler\Crawler;
use SocialCrawler\Channel\Channel;

$crawler = new Crawler(array(
    'channels'  => array(
        'FacebookChannel'   => array(
            'id'        => 'YOUR_FACEBOOK_APP_ID',
            'secret'    => 'YOUR_FACEBOOK_APP_SECRET',
            'token'     => 'YOUR_FACEBOOK_ACCESS_TOKEN', // Optional
            'media'     => Channel::MEDIA_IMAGES_VIDEOS,
            'since'     => '1391425325',
        ),
        'InstagramChannel'  => array(
            'id'        => 'YOUR_INSTAGRAM_CLIENT_ID',
            'media'     => Channel::MEDIA_VIDEOS,
        ),
        'TwitterChannel'    => array(
            'id'        => 'YOUR_TWITTER_CONSUMER_KEY',
            'secret'    => 'YOUR_TWITTER_CONSUMER_SECRET',
            'media'     => Channel::MEDIA_IMAGES,
        ),
    ),
    'log'       => array(
        'path'  => __DIR__,
        'level' => Crawler::LOG_VERBOSE,
    )
));
$data = $crawler->fetch('#hashtag');
```

SocialCrawler can be initialized with an Array of `channels`, each item containing at least an `id` property and identified by the name of the class that will handle the operations.
The `log` options are optional. By default a *socialcrawler.log* file will be created in the SocialCrawler directory.

The output generated by SocialCrawler will look like this:

```javascript
{
    'FacebookChannel': {
        'new_since': '...',
        'data': [
            {
                'id': '...',
                'author': {
                    'id': '...',
                    'avatar': '...',
                    'fullname': '...',
                    'username': '...' // Will return fullname if username is not set
                },
                'description': '...',
                'link': '...',
                'source': '...',
                'thumbnail': '...',
                'type': 'image|video',
                'raw': { } // The raw data returned by the social network API
            }
        ]
    },
    'InstagramChannel': { },
    'TwitterChannel': { }
}
```

## Support for more social networks

You can easily support more services (say, [Tumblr](http://www.tumblr.com/docs/en/api/v2) for example) by adding a new `TumblrChannel` that extends the abstract class `Channel`. It should have at least these public methods with the following signatures:

```php
 __construct($applicationId, $applicationSecret = null, $applicationToken = null)
 fetch($query, $type, $since = null)
```

You can benefit from the [Guzzle](https://github.com/guzzle/guzzle) HTTP framework for your Channel as it's already used by SocialCrawler.

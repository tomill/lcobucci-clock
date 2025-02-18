# Clock

[![Total Downloads](https://img.shields.io/packagist/dt/lcobucci/clock.svg?style=flat-square)](https://packagist.org/packages/lcobucci/clock)
[![Latest Stable Version](https://img.shields.io/packagist/v/lcobucci/clock.svg?style=flat-square)](https://packagist.org/packages/lcobucci/clock)
[![Unstable Version](https://img.shields.io/packagist/vpre/lcobucci/clock.svg?style=flat-square)](https://packagist.org/packages/lcobucci/clock)


[![Build Status](https://img.shields.io/travis/lcobucci/clock/2.0.x.svg?style=flat-square)](http://travis-ci.org/lcobucci/clock)
[![Scrutinizer Code Quality](https://img.shields.io/scrutinizer/g/lcobucci/clock/2.0.x.svg?style=flat-square)](https://scrutinizer-ci.com/g/lcobucci/clock/?branch=2.0.x)
[![Code Coverage](https://img.shields.io/scrutinizer/coverage/g/lcobucci/clock/2.0.x.svg?style=flat-square)](https://scrutinizer-ci.com/g/lcobucci/clock/?branch=2.0.x)

Yet another clock abstraction...

The purpose is to decouple projects from `DateTimeImmutable` instantiation so that we can test things properly.

## Installation

Package is available on [Packagist](http://packagist.org/packages/lcobucci/clock), you can install it using [Composer](http://getcomposer.org).

```shell
composer require lcobucci/clock
```

## Usage

Make your objects depend on the `Lcobucci\Clock\Clock` interface and use `SystemClock` or `FrozenClock` to retrieve the current time or a specific time (for testing), respectively:

```php
<?php

use Lcobucci\Clock\Clock;
use Lcobucci\Clock\SystemClock;
use Lcobucci\Clock\FrozenClock;

function filterData(Clock $clock, array $objects): array
{
    return array_filter(
        $objects,
        static function (stdClass $object) use ($clock): bool {
            return $object->expiresAt > $clock->now();
        }
    );
}

// Object that will return the current time based on the given timezone
// $clock = SystemClock::fromSystemTimezone();
// $clock = SystemClock::fromUTC();
$clock = new SystemClock(new DateTimeZone('America/Sao_Paulo'));

// Test object that always returns a fixed time object
$clock = new FrozenClock(
    new DateTimeImmutable('2017-05-07 18:49:30')
);

// Or creating a frozen clock from the current time on UTC
// $clock = FrozenClock::fromUTC();

$objects = [
    (object) ['expiresAt' => new DateTimeImmutable('2017-12-31 23:59:59')],
    (object) ['expiresAt' => new DateTimeImmutable('2017-06-30 23:59:59')],
    (object) ['expiresAt' => new DateTimeImmutable('2017-01-30 23:59:59')],
];

var_dump(filterData($clock, $objects)); // last item will be filtered
```

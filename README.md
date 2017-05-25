# Money Openexchangerates Bank

[![Gem Version](https://badge.fury.io/rb/money-openexchangerates-bank.svg)](https://rubygems.org/gems/money-openexchangerates-bank)
[![Gem](https://img.shields.io/gem/dt/money-openexchangerates-bank.svg?maxAge=2592000)](https://rubygems.org/gems/money-openexchangerates-bank)
[![Build Status](https://secure.travis-ci.org/phlegx/money-openexchangerates-bank.svg?branch=master)](https://travis-ci.org/phlegx/money-openexchangerates-bank)
[![Code Climate](http://img.shields.io/codeclimate/github/phlegx/money-openexchangerates-bank.svg)](https://codeclimate.com/github/phlegx/money-openexchangerates-bank)
[![Inline Docs](http://inch-ci.org/github/phlegx/money-openexchangerates-bank.svg?branch=master)](http://inch-ci.org/github/phlegx/money-openexchangerates-bank)
[![Dependency Status](https://gemnasium.com/phlegx/money-openexchangerates-bank.svg)](https://gemnasium.com/phlegx/money-openexchangerates-bank)
[![License](https://img.shields.io/github/license/phlegx/money-openexchangerates-bank.svg)](http://opensource.org/licenses/MIT)

A gem that calculates the exchange rate using published rates from
[openexchangerates.org](https://openexchangerates.org/)

## Openexchangerates API

~~~ json
{
  "timestamp": 1441101909,
  "base": "USD",
  "rates": {
      /* 190 currencies */
      "AUD": 1.413637,
      "CAD": 1.316495,
      "CHF": 0.96355,
      "EUR": 0.888466,
      "BTC": 0.004322, /* Includes cryptocurrencies! */
      ...
      }
}
~~~

See more about Openexchangerates product plans on https://openexchangerates.org/signup.

## Features

* supports 190 currencies
* includes cryptocurrencies
* precision of rates up to 6 digits after point
* uses fast and reliable json api
* average response time < 20ms
* supports caching currency rates
* calculates every pair rate calculating inverse rate or using base currency rate
* supports multi threading

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'money-openexchangerates-bank'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install money-openexchangerates-bank

## Usage

~~~ ruby
# Minimal requirements
require 'money/bank/openexchangerates_bank'
moxb = Money::Bank::OpenexchangeratesBank.new
moxb.access_key = 'your access_key from https://openexchangerates.com/signup'

# Update rates (get new rates from remote if expired or access rates from cache)
moxb.update_rates

# Force update rates from remote and store in cache
# moxb.update_rates(true)

# (optional)
# Set the base currency for all rates. By default, USD is used.
# OpenexchangeratesBank only allows USD as base currency for the free plan users.
moxb.source = 'EUR'

# (optional)
# Set the seconds after than the current rates are automatically expired
# by default, they never expire, in this example 1 day.
moxb.ttl_in_seconds = 86400

# (optional)
# Use https to fetch rates from OpenexchangeratesBank
# OpenexchangeratesBank only allows http as connection for the free plan users.
moxb.secure_connection = true

# Define cache (string or pathname)
moxb.cache = 'path/to/file/cache'

# Set money default bank to Openexchangerates bank
Money.default_bank = moxb
~~~

### More methods

~~~ ruby
moxb = Money::Bank::OpenexchangeratesBank.new

# Returns the base currency set for all rates.
moxb.source

# Include also alternative rates.
moxb.alternatives = :all

# Get only alternative rates.
moxb.alternatives = :only

# Default is nil to get only regular rates.
# moxb.alternatives = nil

# Expires rates if the expiration time is reached.
moxb.expire_rates!

# Returns true if the expiration time is reached.
moxb.expired?

# Get the API source url.
moxb.source_url

# Get the rates timestamp of the last API request.
moxb.rates_timestamp

# Get the rates timestamp of loaded rates in memory.
moxb.rates_mem_timestamp
~~~

### How to exchange

~~~ ruby
# Exchange 1000 cents (10.0 USD) to EUR
Money.new(1000, 'USD').exchange_to('EUR')        # => #<Money fractional:89 currency:EUR>
Money.new(1000, 'USD').exchange_to('EUR').to_f   # => 8.9

# Format
Money.new(1000, 'USD').exchange_to('EUR').format # => €8.90

# Get the rate
Money.default_bank.get_rate('USD', 'CAD')        # => 0.9
~~~

See more on https://github.com/RubyMoney/money.

### Using gem money-rails

You can also use it in Rails with the gem [money-rails](https://github.com/RubyMoney/money-rails).

~~~ ruby
require 'money/bank/openexchangerates_bank'

MoneyRails.configure do |config|
  moxb = Money::Bank::OpenexchangeratesBank.new
  moxb.access_key = 'your access_key from https://openexchangerates.com/signup'
  moxb.update_rates

  config.default_bank = moxb
end
~~~

### Cache

You can also provide a Proc as a cache to provide your own caching mechanism
perhaps with Redis or just a thread safe `Hash` (global). For example:

~~~ ruby
moxb.cache = Proc.new do |v|
  key = 'money:openexchangerates_bank'
  if v
    Thread.current[key] = v
  else
    Thread.current[key]
  end
end
~~~

## Process

The gem fetches all rates in a cache with USD as base currency. It's possible to compute the rate between any of the currencies by calculating a pair rate using base USD rate.

## Tests

You can place your own key on a file or environment
variable named TEST_ACCESS_KEY and then run:

~~~
bundle exec rake
~~~

## Refs

* Gem [money](https://github.com/RubyMoney/money)
* Gem [money-currencylayer-bank](https://github.com/phlegx/money-currencylayer-bank)
* Gem [money-open-exchange-rates](https://github.com/spk/money-open-exchange-rates)
* Gem [money-historical-bank](https://github.com/atwam/money-historical-bank)

## Other Implementations

* Gem [money-currencylayer-bank](https://github.com/phlegx/money-currencylayer-bank)
* Gem [money-open-exchange-rates](https://github.com/spk/money-open-exchange-rates)
* Gem [money-historical-bank](https://github.com/atwam/money-historical-bank)
* Gem [currencylayer](https://github.com/askuratovsky/currencylayer)
* Gem [eu_central_bank](https://github.com/RubyMoney/eu_central_bank)
* Gem [nordea](https://github.com/matiaskorhonen/nordea)
* Gem [google_currency](https://github.com/RubyMoney/google_currency)

## Contributors

* See [github.com/phlegx/money-openexchangerates-bank](https://github.com/phlegx/money-openexchangerates-bank/graphs/contributors).
* Inspired by [github.com/phlegx/money-currencylayer-bank](https://github.com/phlegx/money-currencylayer-bank/graphs/contributors).

## Contributing

1. Fork it ( https://github.com/[your-username]/money-openexchangerates-bank/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

## License

The MIT License

Copyright (c) 2017 Phlegx Systems OG

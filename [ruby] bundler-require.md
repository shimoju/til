# BundlerでGemfileを元に自動でrequireする

RailsではGemfileにgemを書くと自動でrequireされてアプリ内で使えるようになるが、これは`Bundler.require`を使っている。

例えばSinatraで使うには以下のようにする。

```ruby
source 'https://rubygems.org'

gem 'sinatra', require: 'sinatra/base'
gem 'sinatra-contrib', require: false
gem 'sinatra-asset-pipeline', require: 'sinatra/asset_pipeline'
gem 'uglifier'

gem 'foreman', require: false
gem 'puma', require: false

gem 'slim'
gem 'compass'
gem 'omniauth-twitter'
gem 'twitter'
```

```ruby
ENV['RACK_ENV'] ||= 'development'
require 'bundler/setup'
Bundler.require(:default, ENV['RACK_ENV'])

class App < Sinatra::Base
  get '/' do
    ...
  end
end
```

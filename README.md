Solidus Rich Data
===============

Designed for [Solidus 2.0+][1].

This extension adds the rich data that search engines need to help enhance search results:

* Open Graph: ([Docs Here][2]) Provide structured data about the page.
* Twitter Cards: ([Docs Here][3]) Using the Product Card.
* Pinterest Rich Pins ([Docs Here][4]) Using the Product Card.
* Entity Markup: Using JSON-LD to tell search engines what the related entity social media profiles are, i.e. Facebook, Twitter, Pinterest, LinkedIn.

```html
<!-- Inserted before closing </head> -->
<meta property="og:url" content="https://example.com" />
<meta property="og:locale" content="en" />
<meta property="og:type" content="website" />
<meta property="og:title" content="Buy Bags, Shoes &amp; Dresses at Example Store" />
<meta property="og:description" content="Get 10% off your first order and free world-wide delivery." />
<meta property="og:image" content="https://example.com/assets/example-store-social.jpg" />
<meta property="twitter:domain" content="https://example.com" />
<meta property="twitter:title" content="Buy Bags, Shoes &amp; Dresses at Example Store" />
<meta property="twitter:description" content="Get 10% off your first order and free world-wide delivery." />
<meta property="twitter:account_id" content="12345678" />
<meta property="twitter:site" content="@ExampleStore" />
<meta property="fb:app_id" content="87654321" />
```

```html
<!-- Inserted before closing </body> -->
<script type="application/ld+json">
{
  "@context": "http://schema.org",
  "@type": "Organization",
  "name": "Example Store",
  "url" : "https://example.com",
  "logo": "https://example.com/assets/logo.jpg",
  "sameAs": [
    "https://twitter.com/ExampleStore",
    "https://www.facebook.com/ExampleStore",
    "https://www.linkedin.com/company/ExampleStore",
    "https://www.pinterest.com/ExampleStore"]
}
</script>
```

Installation
------------

Add solidus_rich_data to your `Gemfile`:

```ruby
gem 'solidus_rich_data'
```

Bundle your dependencies and run the installation generator:

```shell
bundle
bundle exec rails g solidus_rich_data:install
```

You can find all Rich Data settings under Admin > Store > General Settings.
By default, Entity Markup is switched on. Basic URL validation is used for social profiles, however it assumes the Admin knows how to write a full URL, i.e. https://facebook.com/ExampleStore.

You need to configure the `store_uses_https` in an initalizer if the application uses SSL. By default, it's `false`. It helps to generate the correct protocol and domain.

```ruby
Spree::RichData::Config.configure do |config|
  config.store_uses_https = true
end
```

Overriding & Extension
----------------------

The `social_title` and `social_description` are two defaults to help provide social friendly titles for the rich data. You don't always want the SEO title to be the same as a social title.

SEO Title: "Buy Hand Bags - Shop Bags & Accessories | Brand Name"

Social Title: "Buy Hand Bags at Brand Name"

You can add these to other modules such as the [StaticPages gem][5].

``` ruby
# spree/static_content_controller_decorator.rb
Spree::StaticContentController.class_eval do
  def social_title
    if @page
      Spree.t('rich_data.social_title.page', page: @page.title, site: current_store.name)
    else
      super
    end
  end

  def social_description
    if @page
      Spree.t('rich_data.social_description.page', page: @page.title, site: current_store.name)
    else
      super
    end
  end
end
```

``` yaml
# config/locales/en.yml
---
en:
  spree:
    rich_data:
      social_title:
        page: "%{page} at %{site}"
      social_description:
        page: "Use the %{page} at %{site}."
```

You can extend or override existing rich data in your Controller:

```ruby
# spree/products_controller_decorator.rb
before_action :set_rich_data, :show

def set_rich_data
  set_meta og: {
    description: 'Hello world.' # Overrides existing description.
  },
  custom: {
    extra: 'New custom meta data' # Adds brand new meta data.
  }
end
```

Testing
-------

First bundle your dependencies, then run `rake`. `rake` will default to building the dummy app if it does not exist, then it will run specs, and [Rubocop][6] static code analysis. The dummy app can be regenerated by using `rake test_app`.

```shell
bundle
bundle exec rake
```

When testing your applications integration with this extension you may use it's factories.
Simply add this require statement to your spec_helper:

```ruby
require 'solidus_rich_data/factories'
```

Contributions
-------------

If you have found this extension useful, please consider contributing back. Here are some ways:

* Using pre-release versions
* Reporting [bugs][7]
* Suggesting new features
* Writing translations
* Writing or editing documentation
* Writing specifications
* Writing code (no patch is too small: fix typos, add comments, clean up inconsistent whitespace)
* Refactoring code
* Resolving issues
* Reviewing patches

Starting point:

* Fork the repo
* Create your feature branch `git checkout -b my-new-feature`
* Run `bundle install`
* Run `bundle exec rake test_app` to create the test application in `spec/test_app`
* Ensure specs pass by running `bundle exec rspec spec`
* Make your changes `git commit -am 'Add some feature'`
* Push to the branch `git push origin my-new-feature`
* Submit your pull request

TODO
-------
* Write test coverage.
* Find a better way to identify `force_ssl`.
* Add other social profiles.

Copyright (c) 2016 [Ryan Siddle][8] and [contributors][9], released under the [New BSD License][10]

[1]: https://github.com/solidusio/solidus/tree/v2.0
[2]: http://ogp.me/
[3]: https://dev.twitter.com/cards/overview
[4]: https://developers.pinterest.com/docs/rich-pins/overview/
[5]: https://github.com/solidusio-contrib/solidus_static_content
[6]: https://github.com/bbatsov/rubocop
[7]: https://github.com/rsiddle/solidus_rich_data/issues
[8]: https://github.com/rsiddle
[9]: https://github.com/rsiddle/solidus_rich_data/graphs/contributors
[10]: ./LICENSE

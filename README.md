# Intel

[not ready for prime time]

Search analytics made easy

- track searches and conversions week over week
- watch users search in real-time
- monitor the performance of top searches

:cupid: An amazing companion to [Searchkick](https://github.com/ankane/searchkick)

Works with Rails 3.1 or greater

## Get Started

Add this line to your application’s Gemfile:

```ruby
gem "intel"
```

And run the generator. This creates a migration for a table to keep track of searches.

```sh
rails generate intel:install
rake db:migrate
```

## Track Searches

With Searchkick, use the `track` option.

```ruby
Item.search "apple", track: true
```

Want to track more attributes? Use migrations to add them to the `searches` table. Then, pass the values to the `track` option.

```ruby
Item.search "apple", track: {user_id: 1, source: "web"}
```

It’s that easy!

Without Searchkick, create searches manually.

```ruby
Intel::Search.create(
  search_type: "Item", # typically the model name
  query: "apple",
  results_count: 12
)
```

## Track Conversions

Tracking conversions is super important.

First, define your conversion metric. This is specific to your application.

Next, when a user searches, keep track of the search id.

```ruby
@items = Item.search "apple", track: true
@items.search.id # returns search id
```

When a user converts, mark it.

```ruby
search = Intel::Search.find params[:search_id]
convertable = Item.find params[:convertable_id]
if !search.converted?
  search.converted_at = Time.now
  search.convertable = item
  search.save
end
```

## View the Data

Add the dashboards to your `config/routes.rb`.

```ruby
mount Intel::Engine, at: "admin/intel"
```

Be sure to protect the endpoint in production.

#### Basic Authentication

Set the following variables in your environment or an initializer.

```ruby
ENV["INTEL_USERNAME"] = "andrew"
ENV["INTEL_PASSWORD"] = "secret"
```

#### Devise

```ruby
authenticate :user, lambda{|user| user.admin? } do
  mount Intel::Engine, at: "admin/intel"
end
```

## Customize

#### Live Stream

What is “Item 123”? Add the `intel_name` method in your model.

```ruby
def intel_name
  title # use the title method
end
```

#### Time Zone [coming soon]

By default, Intel uses `Time.zone`. To set a specific zone, create an initializer `config/initializers/intel.rb` with:

```ruby
Intel.default_time_zone = "Pacific Time (US & Canada)"
```

#### Top Searches [coming soon]

By default, Intel uses the top 100 searches for low conversions.

```ruby
Intel.top_count = 200
```

#### Views [coming soon]

Add the controllers and views to your app and customize away.

```ruby
rails generate intel:engine
```

## Contributing

Everyone is encouraged to help improve this project. Here are a few ways you can help:

- [Report bugs](https://github.com/ankane/intel/issues)
- Fix bugs and [submit pull requests](https://github.com/ankane/intel/pulls)
- Write, clarify, or fix documentation
- Suggest or add new feature

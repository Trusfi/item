[![Build Status](https://secure.travis-ci.org/lassebunk/item.png)](http://travis-ci.org/lassebunk/item)

# Item

Item is a Ruby on Rails plugin for adding semantic markup (microdata) to your views without cluttering the code.

**What is Microdata?** [Microdata](http://schema.org) helps search engines show additional information about your content, for example product reviews.
This can help attract visitors to your site:

![Microdata in SERPs](http://i.imgur.com/bCi0GHF.png)

**The problem with Microdata:** Normally, implementing these Microdata can make your views quite messy because you have a lot of "extra" content other than your regular view content.


**Item:** Item solves the problem by adding two helpers, `scope` and `prop`, that you can use in your views.
This makes it easy to add microdata scopes and properties without cluttering the view. Item is made to maximize ease of writing (and joy, of course – it *is* Ruby), while minimizing the code needed to add microdata to your views.

## Installation

Add this line to your application's *Gemfile*:

```ruby
gem 'item'
```

Then run:

```bash
$ bundle
```

## Usage

### Scopes

To define an `itemscope`:

```erb
<% scope :product do %>
  ...
<% end %>
```

You don't need `<%=` before scopes, because we see it as just a definition, and not something that is inserted into the view, even though this is done behind the scenes.

The above will generate the following HTML:

```html
<div itemscope itemtype="http://schema.org/Product">
  ...
</div>
```

### Properties

To define an `itemprop` span:

```erb
<%= prop :name, "My Product Name" %>
```

This will generate the following HTML:

```html
<span itemprop="name">My Product Name</span>
```

#### Scoped properties

To define a property that is also a scope:

```erb
<% prop :review do %>
  <%= prop :name, "Pete Anderson" %>
<% end %>
```

You don't need `<%=` before scoped properties, because we see this as a definition of a scope. The HTML is inserted automatically.

The above will generate the following HTML:

```html
<div itemprop="review" itemscope itemtype="http://schema.org/Review">
  <span itemprop="name">Pete Anderson</span>
</div>
```

##### Defining types on scoped properties

Sometimes you need to define a type on scoped properties when this cannot be inferred from the property name. To set a type:

```erb
<% prop :review_rating, type: :review do %>
  ...
<% end %>
```

This will generate the following HTML:

```html
<div itemprop="reviewRating" itemscope itemtype="http://schema.org/Review">
  ...
</div>
```


#### Meta properties

To define hidden `itemprop`s via `<meta>` tags, pass a hash to the `prop` helper:

```erb
<%= prop date_published: Date.today %>
```

This will generate the following HTML:

```html
<meta itemprop="datePublished" content="2014-07-26" />
```

#### Link properties

To define a link to an enumeration member, e.g. [ItemAvailability](http://schema.org/ItemAvailability):

```erb
<%= prop :availability, :in_stock %>
```

This will generate the following HTML:

```html
<link itemprop="availability" href="http://schema.org/InStock" />
```

## Example

The following is based on the [Product example](http://schema.org/Product) on [Schema.org](http://schema.org), rewritten with the Item gem. In your view:

```erb
<% scope :product do %>
  <%= itemprop :name, "Kenmore White 17\" Microwave" %>
  <%= image_tag "kenmore-microwave-17in.jpg" %>

  <% prop :aggregate_rating do %>
    Rated <%= prop :rating_value, 3.5 %>/5
    based on <%= prop :review_count, 11 %> custom reviews
  <% end %>

  <% prop :offers, type: :offer do %>
    <%= prop :price, "$55.00" %>
    <%= prop :availability, :in_stock %>
    In Stock
  <% end %>

  Product description:
  <%= prop :description, "0.7 cubic feet countertop microwave.
    Has six preset cooking categories and convenience features like
    Add-A-Minute and Child Lock." %>

  Customer reviews:
  <% prop :review do %>
    <%= prop :name, "Not a happy camper" %> -
    by <%= prop :author, "Ellie" %>,
    <%= prop date_published: "2011-04-01" %>
    April 1, 2011
    <% prop :review_rating, type: :rating do %>
      <%= prop worst_rating: 1 %>
      <%= prop :rating_value, 1 %> /
      <%= prop :best_rating, 5 %> stars
      <%= prop :description, "The lamp burned out and now I have to replace it." %>
    <% end %>
  <% end %>
<% end %>
```

## Contributing

Changes and improvements are to Item are very welcome and greatly appreciated.

1. Fork the project
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Add your changes, including tests so we don't break it unintentionally.
4. Run `rake` to make sure the tests pass
5. Commit your changes (`git commit -am 'Add feature'`)
6. Push to the branch (`git push origin my-new-feature`)
7. Create a new pull request
# Effective Resources

Make your controller an effective resource controller.

Implements the 7 RESTful actions as a one-liner on any controller.

## Getting Started

```ruby
gem 'effective_resources'
```

Run the bundle command to install it:

```console
bundle install
```

Install the configuration file:

```console
rails generate effective_resources:install
```

The generator will install an initializer which describes all configuration options.

## Usage

Add to your contoller:

```ruby
class PostsController < ApplicationController
  include Effective::CrudController

  # Sets the @page_title in a before_filter
  page_title 'My Posts', only: [:index]

  # All queries and objects will be built with this scope
  resource_scope -> { current_user.posts }

  # Similar to above, with block syntax
  resource_scope do
    Post.active.where(user: current_user)
  end

  # When GET request, will render the approve page
  # When POST|PATCH|PUT request, will call @post.approve! and do the right thing
  member_action :approve

  # When GET request, will render an index page scoped to this method (if it's a scope on the model i.e. Post.approved)
  collection_action :approved

  # When POST|PATCH|PUT request, will call @post.approve! on each post as per params[:ids]
  # Created with effective_datatables bulk actions in mind
  collection_action :bulk_approve

  protected

  # The permitted parameters for this post.  Other recognized method names are posts_params and permitted_params
  def post_params
    params.require(:post).permit(:id, :title, :body)
  end

end
```

## What it does

Implements the 7 RESTful actions: `index`, `new`, `create`, `show`, `edit`, `update`, `destroy`.

- Loads an appropriate `@posts` or `@post` type instance variable.
- Sets a `@page_title` (effective_pages).
- Calls authorize as per the configured `EffectiveResources.authorization_method` (flow through to CanCan or Pundit)
- Does the create/update save
- Sets a `flash[:success]` and redirects on success, or sets a `flash.now[:danger]` and renders on error.
- Does the right thing with member and collection actions
- Intelligently redirects based on commit message

## Helpers

### simple_form_submit

Call `simple_form_submit(f)` like follows:

```haml
= simple_form_for(post) do |f|
  = f.input :title
  = f.input :body

  = simple_form_submit(f)
```

to render 3 submit buttons: `Save`, `Save and Continue`, and `Save and Add New`.

### simple_form_save

Call `simple_form_save(f)` like follows:

```haml
= simple_form_for(post) do |f|
  ...
  = simple_form_save(f)
```

to render just the `Save` button, with appropriate data-disable, title, etc.

## License

MIT License.  Copyright [Code and Effect Inc.](http://www.codeandeffect.com/)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Bonus points for test coverage
6. Create new Pull Request


---
layout: post
comments: true
title: "How to Authenticate a Rails 5 API with Knock and JSON Web Tokens"
date: 2017-12-17
categories:
---

In this tutorial, we'll step through creating a Rails 5 (api-only) application that
authenticates users with [Knock](https://github.com/nsarno/knock). Knock authenticates
users through JSON Web Tokens (JWT) in a lightweight and stateless manner.

## First, a bit About JWT

At its core, JWT is a method for securely sending information between two parties--namely,
our client and server. Since we'll be using JWT in it's most common use case, which is
authenticating a user, the JWT will primarily consist of:

1. An expiration date for the token
1. The user's email address
1. ~~The user's password~~ This will only be used to initially create a valid token.

Of course, this data will be encoded using our choice of the [supported JWT algorithms](https://github.com/jwt/ruby-jwt#algorithms-and-usage)
and will take the form of `xxxxxx.yyyyyyy.zzzzzz`.

## Creating our Rails 5 API Project

Run the following command to create a Rails API project. The `--api` flag tells rails to
use a subset of the default Rails middleware and will keep our project a bit lighter.

```bash
rails new knock-demo --api
```

We know we'll need to install knock at some point, so let's install the gem now. Add this
to our `Gemfile`
```ruby
gem 'knock'
```
Run
```
bundle install
```

Next, let's create a user model. The quick way to do it is to run
```bash
rails g model user email password_digest
```

After running this, our migration should look like the following code snippet. If you
already have a user model in your application, you might need to create a new migration.
You just need to make sure you're keeping track of the user by `email` and `password_digest`.

```ruby
# db/migrations/20171216015531_create_users.rb
class CreateUsers < ActiveRecord::Migration[5.1]
  def change
    create_table :users do |t|
      t.string :email
      t.string :password_digest

      t.timestamps
    end
  end
end
```

Now migrate our database with:
```bash
rails db:migrate
```

## Configuring Knock

To configure knock, we'll run knock's generator. We'll use the version of the generator that
__doesn't__ make use of Auth0.  This generator will create a controller,
`controllers/user_token_controller.rb` that can be used as a login route.
```bash
rails generate knock:token_controller user
```

We also need to add `has_secure_password` to our user model and `include Knock::Authenticable`
to our application controller. The two files should look like this:
```ruby
# models/user.rb
class User < ApplicationRecord
  has_secure_password
end
```
```ruby
# controllers/application_controller.rb
class ApplicationController < ActionController::API
  include Knock::Authenticable
end
```

Lastly, let's create a controller to the application that will help to determine whether
or not someone is successfully logged in. We can use the Rails generator to create this
controller.
```bash
rails g controller status
```

One of the great conveniences of knock, is that once we have everything set up, we can
almost forget about how users are authenticated. We can continue to use `current_user`
in all of our controllers to interact with our current user model. It's also worth noting
that when a user is not logged in, knock will return an `unauthorized` status.
```ruby
# controllers/status_controller.rb
class StatusController < ApplicationController
  before_action :authenticate_user, except: :user

  def index
    render json: { message: 'logged in' }
  end

  def user
    render json: { user: current_user }
  end
end
```

We'll also want to add routes to match the controller we just created:
```ruby
# config/routes.rb
Rails.application.routes.draw do
  post 'user_token' => 'user_token#create'

  resources :status, only: [:index] do
    collection do
      get "user"
    end
  end
end
```

## Knock Initializer
The knock initializer allows you to configure some details about how knock uses
JWT. Take note that the `token_lifetime` is set to `1.day`. This means that each
token will expire after 24 hours.
```ruby
Knock.setup do |config|
  config.token_signature_algorithm = 'HS256'
  config.token_secret_signature_key = -> { Rails.application.secrets.secret_key_base }
  config.token_public_key = nil
  config.token_audience = nil
  config.token_lifetime = 1.day

  config.not_found_exception_class_name = 'ActiveRecord::RecordNotFound'
end
```
Yes, this means a user will be automatically logged out after a day. What if we
want our active users to not have to log in all the time? It turns out we can
refresh the JWT on every request so the user will only have to log in again
after 24 hours of inactivity.

There's just one issue--I'm a lazy developer who doesn't want to have to
return a new JWT for every request. So how do we make sure we're sending a new
token for every authenticated request? Easy, let's override the `render` method
in our application controller to return a new token for each JSON request an
authenticated user makes.

```ruby
# controllers/application_controller.rb
class ApplicationController < ActionController::API
  include Knock::Authenticable

  # Refresh the JWT to a new 24h token
  def new_jwt
    Knock::AuthToken.new(payload: { sub: current_user.id }).token
  end

  def render(options=nil, extra_options={}, &block)
    options ||= {}
    # If the user is logged in and we're returning a json object,
    # send a new JWT with it
    if json_response?(options) && logged_in?
      options[:json].merge!({ jwt: new_jwt })
    end
    super(options, extra_options, &block)
  end

  private

  def json_response?(options)
    options[:json].is_a?(Hash)
  end

  def logged_in?
    current_user.present?
  end
end
```

## Testing our Application

Now that our application is set up and configured with knock, let's do some testing
to verify we can correctly identify a user and authenticate them.

First, let's write some tests to verify we're getting a non-nil response when
trying to log in a new user. The purpose of this test is not really to validate
the correctness of our application, but to verify we understand how knock is working
and how we should be interacting with it--knock itself should test whether or not this
works properly. The following test creates a user, sends a
post request to the login route with the user's credentials, and asserts two things:

1. We get a `:success` response
1. We get a non-nil `jwt`.

```ruby
require 'test_helper'

class UserTokenControllerTest < ActionDispatch::IntegrationTest

  def parse_jwt
    JSON.parse(@response.body)["jwt"]
  end

  setup do
    @user = User.create(email: "1@user.com", password: "password")

    post user_token_url({
      auth: {
        email: @user.email,
        password: @user.password
      }
    })
  end

  test "the response is successful" do
    assert_response :success
  end

  test 'it returns a non-nil jwt' do
    assert_not_nil parse_jwt
  end

end
```

Next, we'll write about four tests to see how knock handles unauthorized and logged in
user requests. The first two tests describe the index route and how knock handles
authorized and unauthorized requests. When using the `authenticate_user` `before_action`,
knock will return an unauthorized response when a user is not logged in, and will complete
the action successfully when a user is logged in (a valid JWT is passed in the
Authorization header. More on the browser side of this later).

```ruby
test "#index unauthorized requests return :unauthorized" do
  get status_index_url
  assert_response :unauthorized
end

test "#index should return a logged in message when a user is logged in" do
  get status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
  assert_response :success
  assert_equal 'logged in', parse_response(:message)
end
```

The next two tests describe how knock will handle the `current_user` helper. If a user
is not logged in, the value of `current_user` will be `nil`--otherwise, the value of
`current_user`, will be the user object of the currently logged in user. If we override
the `as_json` method of the user model, we can limit the user data that is available
to the client.
```ruby
test "#user non logged-in user should be nil" do
  get user_status_index_url
  assert_nil parse_response(:user)
end

test '#user returns the logged in user' do
  get user_status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
  assert_equal @user.email, parse_response(:user)['email']
end
```

Lastly, we want to test that we're getting a new JWT every time we make an authenticated
JSON request to our server. This way, we can keep using the new tokens to keep our
users logged in while they remain active. Since all of their past tokens will expire
within 24h, we don't really have to worry too much about generating excess tokens.

```ruby
test '#user returns a refreshed jwt' do
  get user_status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
  jwt = parse_response(:jwt)
  assert_not_nil jwt

  sleep 1

  get user_status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
  assert_not_nil parse_response(:jwt)
  assert_not_equal jwt, parse_response(:jwt)
end
```

The whole file, with comments, looks like this:
```ruby
# test/controllers/status_controller_test.rb
require 'test_helper'

class StatusControllerTest < ActionDispatch::IntegrationTest

  # Parse the response and return a specific key
  def parse_response(key)
    JSON.parse(@response.body)[key.to_s]
  end

  # Log a user in and return the valid JWT
  def log_in
    post user_token_url({
      auth: {
        email: @user.email,
        password: @user.password
      }
    })

    parse_response(:jwt)
  end

  setup do
    @user = User.create(email: "1@user.com", password: "password")
  end

  # A non-logged in user should get an unauthorized request response
  test "#index unauthorized requests return :unauthorized" do
    get status_index_url
    assert_response :unauthorized
  end

  test "#index should return a logged in message when a user is logged in" do
    # Users are authorized by sending the JWT in the Authorization header
    get status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
    assert_response :success
    assert_equal 'logged in', parse_response(:message)
  end

  # The #user action should return a nil user when no one is logged in
  test "#user non logged-in user should be nil" do
    get user_status_index_url
    assert_nil parse_response(:user)
  end

  # The #user action should return a user (email, check the as_json
  # method in the user model # of the linked github repository for
  # how to make sure you're not returning too much user data)
  test '#user returns the logged in user' do
    get user_status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
    assert_equal @user.email, parse_response(:user)['email']
  end

  test '#user returns a refreshed jwt' do
    get user_status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
    jwt = parse_response(:jwt)
    # Assert the first token is not nil
    assert_not_nil jwt

    # Generating an auth token seems to use a timestamp, so wait a second
    # before generating another one
    sleep 1

    get user_status_index_url, headers: { "Authorization" => "Bearer #{log_in}"}
    # Assert the second token is not nil
    assert_not_nil parse_response(:jwt)

    # Assert the second token does not equal the first
    assert_not_equal jwt, parse_response(:jwt)
  end
end
```

## How Does This Look Client Side?

First, let's create a user in the development database so we have a user to make
login calls with. Use `rails console` or the `db/seeds.rb` file to create a user
with a basic email/password.
```ruby
User.create(email: '1@user.com', password: 'password')
```

Now that we have a user in the database, we can start to think about making client
side requests to our server. But, before we get ahead of ourselves, we need to make
sure those requests can be made. Unless the requests are coming from the same domain
of the server, we'll need to add a CORS policy. The simplest way to do that is to
use the [rack-cors gem](https://github.com/cyu/rack-cors).

To use it, add the following to your `Gemfile`:
```ruby
gem 'rack-cors', :require => 'rack/cors'
```

Then add the configuration to your `config/application.rb`:
```ruby
config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*', :headers => :any, :methods => [:get, :post, :options]
  end
end
```

In a production environment, you would most likely want to limit the domains requests
can be made from. To do that, replace the `*` with a regex that matches the allowed
domains.

Now that we can make successful requests to our server, let's construct some
requests. In this example we'll use the [fetch api](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch),
although these requests are fairly easy to replicate in any AJAX library.

First, we'll look at an example login request. The key thing here is that we're
making a post request with an `auth` object. If the credentials are correct,
we'll get a JWT in return.
```javascript
var loginRequest new Request('http://localhost:3000/user_token', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    auth: {
      email: "1@user.com",
      password: "password"
    }
  })
})

fetch(request)
  .then((resp) => resp.json())
  .then((data) => localStorage.setItem('jwt', data.jwt))
```

When we get a JWT back, we'll need to store that off in `localStorage` or some
equivalent. Once we have a JWT, we can make authenticated requests.

```javascript
var request = new Request('http://localhost:3000/status/user', {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${localStorage.get('jwt')}`
  },
})
fetch(request)
  .then((resp) => resp.json())
  .then((data) => localStorage.setItem('jwt', data.jwt)
```

As we saw from our rails tests earlier, knock looks for a JWT in the `Authorization`
header. As long as we pass up a valid JWT, the user will be authenticated. If the
JWT is expired or invalidated in some way, the request will fail and the user will
need to log in again.

This is also where we'll need to keep track of our JWTs. Remember that we overrode the
`render` method to return a new JWT on every successful, authenticated, request. So, in
the repsonse of the request to `/status/user`, we'll have some data about the user as
well as a new JWT. To update our user's session, we'll forget entirely about the JWT
that is currently stored and store the new JWT that was returned in the request.

If you'd like to see all of this in action, check out the [knock-demo](https://github.com/callahanrts/knock-demo)
repository, where there's a more [detailed client-side implementation](https://github.com/callahanrts/knock-demo/tree/master/client)

---
author: "Ben Schembri"
date: "15/10/2024"
---

# Rails Authentication Without Devise

How to build your own authentication in Ruby on Rails.

<div class="info-box">
  <strong>Ruby on Rails</strong>
  <p>
    This article assumes you have a basic understanding of Ruby on Rails and the MVC Framework.
  </p>
</div>

Contents

## What is authentication?

Authentication in web development is the process of confirming the identity of a user. This usually involves checking credentials like usernames and passwords.

Think of your web app like a hotel. When a guest arrives, the receptionist checks their ID to authenticate their identity, ensuring they are who they claim to be. Once verified, the guest receives a key (similar to a session token or cookie) which allows them access to their room and to areas like the lobby and pool for the duration of their stay (session).

None of this is unique to Ruby on Rails either - the challenges and requirements around authentication are universal, regardless of the framework or technology you choose to build your web app.

Whether you opt for a third-party authentication tool or decide to create your own solution, understanding authentication is essential for any web application.

## Why build your own Authentication?

Building your own authentication system in Ruby on Rails will help you to fully understand key concepts like web security, user lifecycle, and session management. It reduces your app's reliance on third-party dependencies, keeping it lightweight and less complex. If your needs are basic - like signup, login, logout, password reset, and user roles - the DIY approach is easier to maintain and avoids unnecessary overhead.

## What exactly do we need to build?

Let's figure out the Minimum Viable Product (MVP). An MVP is the most basic version of a product with only the essential features.

The essential features of our DIY auth system are:

- Users should be able to signup to create an account
- Users should be able to login and logout
- Users should be able to reset their password if they forget it
- Developers should be able to control what different users can access within the application

Broken down like that, it starts to look straightforward. But there are some other things that we need to consider if we're actually going to use this in the wild.

## Security

This is the big one. Security concerns are what keeps most people using Devise or another auth gem. Keeping your user data safe is critical. You don't want your app or business to be known for a data leak or a data breach.

<div class="warning-box">
  <strong>Security basics</strong>
  <p>
    Facebook famously stored hundreds of millions of users' passwords in plain text on internal company servers, accessible by thousands of employees, for years. Don't be like Facebook. Encrypt your users' passwords.
  </p>
</div>

Let's list all the features that would be necessary to actually use our DIY auth in the wild:

- Require users to create strong passwords
- Encrypt user passwords (hash and salt) and don't store them in plain-text
- Redact sensitive information like user passwords from logs
- Verify emails after signup to ensure the user owns the email address
- Rate-limit login attempts to protect against brute force attacks
- Temporarily lock accounts after repeated failed login attempts
- Use HTTPS to encrypt all communication between users and server
- Use Secure Cookies for session management to prevent cross-site scripting (XSS) attacks and cookie theft
- Include tokens in any forms to prevent Cross-Site Request Forgery (CSRF)

Now it's starting to look a bit harder! It's about this point you might be wondering if you should just reach for a gem like Devise.

## Why it's a bad idea to build your own authentication

Building your own authentication can be risky and time-consuming. Implementing security features correctly requires an investment of time and effort, and an oversight could leave your app vulnerable to serious attacks.

Pre-built solutions like Devise have been tested and vetted by the community, saving you from reinventing the wheel and reducing the likelihood of introducing security flaws. For junior developers or projects with tight deadlines, building from scratch can become a maintenance burden.

<div class="warning-box">
  <h4>It's probably not a good idea to build your own authentication</h4>
  <p>
    Building your own authentication can be a good exercise if you’re looking for something lightweight for a small project or you're trying to understand the fundamentals. However, in most cases, using a gem like Devise is the better choice. It will save you a lot of development time and you can trust that all of its features are well-tested for security.
  </p>
</div>

## Should I just use Devise in my Rails app?

If you've built more than a hello world app with Ruby on Rails, there's a good chance that you've used the Devise gem for authentication. Devise has always been the go-to auth solution in the Rails community, but there are many other options such as Sorecery, Rodauth, or Clearance.

<div class="info-box">
  <h4>Devise</h4>
  <p>
    If you did the Le Wagon bootcamp like me, or you followed The Odin Project, then you're probably most familiar with the Devise gem. I'm going to refer to Devise a lot throughout this post because that's what I'm most familiar with. If you aren't familiar with it don't worry - you should still be able to follow along.
  </p>
</div>

Devise provides a comprehensive set of authentication features out of the box, offering pretty much everything you could want.

With sensible defaults that align with Rails' convention over configuration approach, Devise lets you get a working authorisation system set up quickly without reinventing the wheel. The extensive documentation and active community support also provide a strong foundation for developers, making it easier to implement and customise as needed.

However, there are some drawbacks to consider. Devise's complexity can be daunting - especially when a lot of its features might be unnecessary for smaller projects.

The way Devise abstracts various functionalities can make it hard to understand what's happening behind the scenes. This lack of clarity can be frustrating when trying to customise features or troubleshoot problems, and the source code is often complex and not always straightforward.

That said, you should probably keep using Devise. Devise has been widely adopted and vetted by thousands of developers, meaning it’s been tested extensively for security and scalability.

While a well-built custom authentication system can be just as good or even better than Devise, it takes a lot more effort to implement when Devise offers a quicker, safer route to achieving standard authentication functionality for most apps.

Before diving into something as comprehensive as Devise though, it can help to have a solid foundation in authentication concepts to understand exactly what Devise does for you.

## Let's go ahead and build our own DIY authentication anyway

I've tried to come up with a comprehensive list of every authentcation feature that we could possibly want to implement in our app. Most of these, Devise and other gems will provide out of the box. This is what I came up with - please let me know if I missed anything.

_Basic MVP features:_

- Users should be able to signup to create an account
- Users should be able to login and logout
- Users should be able to reset their password if they forget it
- Developers should be able to control what different users can access within the application

_Essential security features:_

- Require users to create strong passwords
- Encrypt user passwords (hash and salt) and don't store them in plain-text
- Redact sensitive information like user passwords from logs
- Verify emails after signup to ensure the user owns the email address
- Rate-limit login attempts to protect against brute force attacks
- Temporarily lock accounts after repeated failed login attempts
- Use HTTPS to encrypt all communication between users and server
- Use Secure Cookies for session management to prevent cross-site scripting (XSS) attacks and cookie theft
- Include tokens in forms to prevent Cross-Site Request Forgery (CSRF)

_Nice to have features:_

- A "Remember me" checkbox so users can stay logged in without having to re-enter credentials
- Automatically sign out users after a specified period of inactivity
- Keep track of user sign-in counts, timestamps, and IP addresses
- Letting users log in with their social media accounts
- Password expiration policies that require users to change their passwords after a specified period
- Users can enable two-factor authentication for an added layer of security
- Support JSON Web Tokens (JWT) for API-based applications
- Allow users to see their active sessions and log other devices out
- Allow users to mark devices as trusted, so they don’t have to enter credentials on those devices repeatedly

We're not going to implement any of the nice to have features for now, but we do want something we can safely use, so we'll implement the essential security features as well as the basic features.

<div class="info-box">
  <h4>Legal compliance</h4>
  <p>
    Depending on which country you're in, there may be specific legal requirements regarding privacy and the handling and storage of user data, such as the General Data Protection Regulation (GDPR) in Europe. If your site doesn't comply, you may face significant fines and legal action.
  </p>
</div>

## Users

### Start in the Gemfile

Our DIY auth relies a lot on ActiveModel's `has_secure_password` feature, provided by the `bcrypt` gem.

[https://guides.rubyonrails.org/active_model_basics.html#securepassword]

Uncomment this line in your Gemfile

`gem "bcrypt", "~> 3.1.7"`

Run `bundle install`

### Then the User model

Let's start with the data - the ruby models and database tables.

`rails g model User name email password_digest password_confirmation`

You don't have to call your model `User`, you could call it `Employee`, `Admin`, or anything else depending on what it represents in your app.

Note that `password_digest` and `password_confirmation` are special columns.

`password_digest` is where the hashed version of the user's password is stored (not the plain text password).

Rails uses `bcrypt` to securely hash passwords, and the `password_digest` column holds the result of that process. So, while in the database the column is a string, it is expected to contain a hashed password, not plain text.

`password_confirmation` is not stored in the database at all. It's used as a virtual attribute to verify that the user entered the same password twice during registration or password updates. It's part of Rails’ form validation when you're using `has_secure_password` to ensure the user didn’t mistype the password.

Let's add the `has_secure_password` feature and some validations to our model:

```
# ./app/models/user.rb
class User < ApplicationRecord
  has_secure_password

  validates :name, presence: true, uniqueness: true
  validates :email, presence: true, uniqueness: true
  validates_format_of :email, with: URI::MailTo::EMAIL_REGEXP
end
```

`rails db:migrate`

### Routes

```
Rails.application.routes.draw do
  get "/signup", to: "users#new", as: :signup
  get "/login", to: "sessions#new", as: :login
  post "/login", to: "sessions#create"
  delete "/logout", to: "sessions#destroy", as: :logout
  resources :users, only: [ :index, :show, :create, :destroy ], param: :username
end
```

### Controller

`rails g controller users`

```
class UsersController < ApplicationController
  before_action :authenticate_user, only: [ :index, :show, :update, :destroy ]
  before_action :authorise_user, only: [ :update, :destroy ]

  def index
    @users = User.all
  end

  # get /users/:username
  def show
  end

  def update
    @user = User.update(user_params)
    if @user.save
      redirect_to users_path(@user.name), notice: "User updated successfully."
    else
      flash.now[:alert] = "User not updated"
      render :new, status: :unprocessable_entity
    end
  end

  def new
    if user_signed_in?
      flash.now[:alert] = "You are already signed in as #{current_user.name}"
    end
    @user = User.new
  end

  def create
    @user = User.new(user_params)

    if @user.save
      reset_session
      session[:user_id] = @user.id
      redirect_to user_path(@user.name), notice: "User created successfully."
    else
      flash.now[:alert] = "User not created"
      render :new, status: :unprocessable_entity
    end
  end

  def destroy
    raise
    if @user.destroy
      redirect_to users_path, notice: "User deleted successfully"
    else
      redirect_to users_path, alert: "Failed to delete user", status: :unprocessable_entity
    end
  end

  private

  def user_params
    params.require(:user).permit(:name, :email, :password, :password_confirmation)
  end
end

```

### Views

## Session management

After a user logs in, the app has to remember who they are while they browse. This can be done by creating and managing session _tokens_ or _cookies_. We also need to think about how long a session lasts before it expires, and how to enforce expiry. Sessions should be secured with HTTPS and cookies need to be secured to prevent session hijacking.

### Routes

```
get "/login", to: "sessions#new", as: :login
post "/login", to: "sessions#create"
delete "/logout", to: "sessions#destroy", as: :logout
```

### Controller

`rails g controller sessions new create destroy`

```
# ./app/controllers/sessions_controller.rb
class SessionsController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.find_by(name: params[:user][:name])&.authenticate(params[:user][:password])

    if @user
      reset_session
      session[:user_id] = @user.id
      redirect_to user_path(@user.name), notice: "Logged in successfully"
    else
      flash.now[:alert] = "Login failed"
      render :new, status: :unprocessable_entity
    end
  end

  def destroy
    reset_session
    redirect_to root_path, notice: "Logged out successfully"
  end
end
```

### Authentication

Why did I move it to a concern?

```
# ./app/controllers/concerns/authentication.rb
module Authentication
  extend ActiveSupport::Concern

  included do
    helper_method :current_user
    helper_method :user_signed_in?
  end

  def current_user
    # If the user has been checked this request, return @current_user
    # Otherwise, if session[:user_id] is nil, set @current_user to nil
    # Otherwise set @current_id to the correct user instance
    @current_user ||= session[:user_id] && User.find_by(id: session[:user_id])
  end

  def authenticate_user
    # Ensure the user is authorised based on the username in params
    if current_user.nil? || current_user.name != params[:username]
      redirect_to root_path, alert: "You need to be logged in."
    end
  end

  def authorise_user
    if current_user.name != params[:username]
      redirect_to root_path, alert: "Unauthorised access."
    end
  end

  def user_signed_in?
    current_user.present?
  end
end
```

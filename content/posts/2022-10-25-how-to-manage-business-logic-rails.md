---
author: Me
title: How to manage business logic in rails
date: 2022-10-25
description: A pattern to handle complexity with business logic in Ruby on Rails
math: true
categories: ["ruby"]
---

One of the main problems, when an application grows, is how to keep organized the business logic.

Following the “rails way” can conduce to a common result called obese models & obese controllers.

This means that your models and your controllers keep growing when you continuously add the business logic in the same place. Another inconvenience is that you are coupling your use cases or business logic to the infrastructure (the framework, the database, and the input interface).

Imagine a common feature like registering a user, easily you can have the following steps:
1. Save the user in the database
2. Generate a token to handle email confirmation
3. Send an email to confirm the user's email

You would usually see this logic in the model & controller:

A common approach to handle this is to encapsulate the business logic in a specific class, this pattern has several names like class services, handlers, interactors, etc.

Another important thing to leverage this pattern is to take advantage of solid principles like single responsibility. Having multiple specific purpose classes that our service classes can use like query objects, mailers, jobs, etc.

I take this one from [Sustainable Web Development with Ruby on Rails](https://sustainable-rails.com/) book:

```ruby
# app/services/user_creator.rb

class UserCreator
  def create_user(user)
    user.save

    if user.invalid?
      return Result.new(created: false, user: user)
    end

    UserEmailConfirmationJob.perform_async(user.id)

    Result.new(created: user.valid?, user: user)
  end

  class Result
    attr_reader :user

    def initialize(created:, user:)
      @created = created
      @user = user
    end

    def created?
      @created
    end
  end
end
```

Usage:

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController
  ...

  def create
    user_params = params.require(:user).permit(:email, :password)

    result = UserCreator.new.create_user(User.new(user_params))

    if result.created?
      redirect_to user_path(result.user)
    else
      @user = result.user
      render :new, status: :unprocessable_entity
    end
  end
end
```

Some considerations:
- This class performs business logic and expects some specific kind of data to make an action. Type casting, input sanitization, etc. These things are infrastructure concerns, not the business.
- Return rich result objects provide more useful details than a single boolean. This is very useful for tests and readability.
- Explicit method names like `create_user` provide more context than a generic method like `call` or `execute`.


Finally, you get some nice advantages: the possibility of testing your business logic independently of the input interface. If your SSR app change to an API REST (or you have both) this will be easy to handle.

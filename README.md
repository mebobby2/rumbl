# Rumbl

# Set up
* mix deps.get
* mix ecto.create
* mix phoenix.server

# Phoenix Notes

## Phoenix plugs

Web applications in Phoenix are pipelines of plugs. The basic flow of traditional applications is endpoint, router, pipeline, controller.

## Plugs

Plugs are just functions, whether it's a function plug or a module plug (modules is just a collection of functions). A module plug must have two functions, init and call. init will happen at compile time. Plug uses the result of init as the second argument to call. Because init is called at compilation time, it’s the perfect place to validate options and prepare some of the work. That way, call can be as fast as possible. Since call is the workhorse, we want it to do as little work as possible.

## Forms not backed by changesets
Usually, the first arguement to a form_for is a changeset.
```
form_for @changeset...
```
But if you have a form that is not backed by a changeset, such as a login or search form, you can pass in %Plug.Conn{} struct instead.
```
form_for @conn...
```

## Phoenix MVC
You already know a bit about the differences between traditional MVC and Phoenix’s tweak from the perspective of controllers. More explicitly, we’d like to keep functions with side effects—the ones that change the world around us—in the controller while the model and view layers remain side effect free. Since Ecto splits the responsibilities between the repository and its data API, it fits our world view perfectly.

## Prefer db references and indexes

Constraints allow us to use underlying relational database features to help us maintain database integrity. For example, let’s validate our categories. When we create a video, we need to make sure that our category exists. We might be tempted to solve this problem by simply performing a query, but such an approach would be unsafe due to race conditions. In most cases, we would expect it to work like this:

1. The user sends a category ID through the form.
2. We perform a query to check if the category ID exists in the database.
3. If the category ID does exist in the database, we add the video with the category ID to the database.

However, someone could delete the category between steps 2 and 3, allowing us to ultimately insert a video without an existing category in the database. In any sufficiently busy application, that approach will lead to inconsistent data over time. Ecto has relentlessly pushed us to define references and indexes in our database because sometimes, doing a query won’t be enough and we’ll need to rely on database constraints.

Ecto will throw Ecto.ConstraintError if any of the db contraints fail. You can use unique_constraint, assoc_constraint etc inside the changesets to convert Ecto.ConstraintError into changeset errors. That way, your application does not crash on Ecto.ContraintErrors.

the *_constraint changeset functions are useful when the constraint being mapped is triggered by external data, often as part of the user request. Using changeset constraints only makes sense if the error message can be something the user can take action on.

Example:

When we added a foreign_key_constraint to the video belongs_to :category relationship, we knew we wanted to allow the user to choose the video category later on. If a category is removed at some point between the user loading the page and submitting the request to publish the video, setting the changeset constraint allows us to show a nice error message telling the user to pick something else.

This isn’t so uncommon. Maybe you’ve started to publish a new video on Friday at 5:00 p.m. but decide to finish the process next Monday. Someone has the whole weekend to remove a category, making your form data outdated.

On the other hand, let’s take the user has_many :videos relationship. Our application is the one responsible for setting up the relationship between videos and users. If a constraint is violated, it can only be a bug in our application or a data-integrity issue.

In such cases, the user can do nothing to fix the error, so crashing is the best option. Something unexpected really happened. But that’s OK. We know Elixir was designed to handle failures, and Phoenix allows us to convert them into nice status pages. Furthermore, we also recommend setting up a notification system that aggregates and emails errors coming from your application, so you can discover and act on potential bugs when your software is running in production.

## Use correct isolation in testing

For example, in your page_controller_test, we called our controller with get conn, "/" rather than calling the index action on our controller directly. This practice gives us the right level of isolation because we’re using the controller the same way Phoenix does.

# Upto
page 360
Part 2 - Writing Interactive and Maintainable Applications
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


# Upto
page 242
Chapter 7
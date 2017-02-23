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

Excerpt From: McCord, Chris. “Programming Phoenix.” iBooks.

# Upto
page 190
The Anatomy of a Plug
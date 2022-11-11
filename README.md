# README

1. Run `bundle install` and `bin/rails db:migrate`
2. Run `bin/rails s`
3. Visit [http://localhost:3000/posts](http://localhost:3000/posts)
4. The server should stop at the breakpoint:

    ```
    Started GET "/posts" for 127.0.0.1 at 2022-11-11 18:54:50 +0000
      ActiveRecord::SchemaMigration Pluck (0.2ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" ORDER BY "schema_migrations"."version" ASC
    Processing by PostsController#index as HTML
    [1, 10] in ~/projects/debug-issue/app/controllers/posts_controller.rb
         1| class PostsController < ApplicationController
         2|   before_action :set_post, only: %i[ show edit update destroy ]
         3|
         4|   # GET /posts or /posts.json
         5|   def index
    =>   6|     binding.b
         7|     @posts = Post.all
         8|     a = 1
         9|     b = 2
        10|   end
    ```
5. Type `n` TWICE, and then the debugger would stop at `zeitwerk/kernel.rb` while it should stop at `a = 1`

    **1st `n`**
    ```
    (rdbg) n    # next command
    [2, 11] in ~/projects/debug-issue/app/controllers/posts_controller.rb
         2|   before_action :set_post, only: %i[ show edit update destroy ]
         3|
         4|   # GET /posts or /posts.json
         5|   def index
         6|     binding.b
    =>   7|     @posts = Post.all
         8|     a = 1
         9|     b = 2
        10|   end
        11|
    =>#0    PostsController#index at ~/projects/debug-issue/app/controllers/posts_controller.rb:7
      #1    ActionController::BasicImplicitRender#send_action(method="index", args=[]) at ~/.gem/ruby/3.1.2/gems/actionpack-7.0.4/lib/action_controller/metal/basic_implicit_render.rb:6
      # and 76 frames (use `bt' command for all frames)
    ```

    **2nd `n`**
    ```
    (rdbg) n    # next command
    [23, 32] in ~/.gem/ruby/3.1.2/gems/zeitwerk-2.6.6/lib/zeitwerk/kernel.rb
        23|     alias_method :zeitwerk_original_require, :require
        24|   end
        25|
        26|   # @sig (String) -> true | false
        27|   def require(path)
    =>  28|     if loader = Zeitwerk::Registry.loader_for(path)
        29|       if path.end_with?(".rb")
        30|         required = zeitwerk_original_require(path)
        31|         loader.on_file_autoloaded(path) if required
        32|         required
    =>#0    Kernel#require(path="/Users/hung-wulo/projects/debug-issue/ap...) at ~/.gem/ruby/3.1.2/gems/zeitwerk-2.6.6/lib/zeitwerk/kernel.rb:28
      #1    PostsController#index at ~/projects/debug-issue/app/controllers/posts_controller.rb:7
      # and 77 frames (use `bt' command for all frames)
    ```

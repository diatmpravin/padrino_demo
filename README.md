Preface

Padrino is a ruby framework built upon the Sinatra web library. Sinatra is a DSL for creating simple web applications in Ruby. Padrino was created to make it fun and easy to code more advanced web applications while still adhering to the spirit that makes Sinatra great!

Installation

In order to develop a Padrino application, we must have a few things installed. First, we must obviously have ruby and rubygems installed. Next, we must install the padrino framework gems:

$ gem install padrino

Sample Application

For sample application, we will use the ActiveRecord ORM AND the erb templating language.
With that in mind,let us generate our new project:

$ padrino g project cast - erb -d activerecord
      create  
      create  .gitignore
      create  config.ru
      create  config/apps.rb
      
=================================================================
cast is ready for development!
=================================================================
$ cd ./cast
$ bundle
=================================================================

This command will generate our basic Padrino project and the print out a nice report
of the files generated. we need to do now is cd into our brand new application.
$ cd sample_blog

Let’s start off by generating the model into our app directory.

$ padrino g model list  name:string price:integer
       apply  orms/activerecord
      create  models/list.rb
      create  db/migrate/001_create_lists.rb

Go ahead and migrate the database now.

$ padrino rake ar:migrate
=> Executing Rake ar:migrate ...
  DEBUG -   (0.0ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" 
   INFO -  Migrating to CreateLists (1)
  DEBUG -   (0.0ms)  select sqlite_version(*)
  DEBUG -   (0.0ms)  begin transaction
==  CreateLists: migrating ====================================================
-- create_table(:lists)
  DEBUG -   (0.3ms)  CREATE TABLE "lists" ("id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, 
  "name" varchar(255), "price" text, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL)
   -> 0.0038s
==  CreateLists: migrated (0.0038s) ===========================================

  DEBUG -   (0.1ms)  INSERT INTO "schema_migrations" ("version") VALUES ('1')
  DEBUG -   (230.7ms)  commit transaction
  DEBUG -   (0.1ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" 

This creates the post model. Next, let’s create the controller to allow the basic
viewing functionality.

$ padrino g controller list get:new get:show
      create  app/controllers/list.rb
      create  app/helpers/list_helper.rb
      create  app/views/list

Now it's time to create index.html.rb and new.html.rb file and add some
custom code to place order.

new.html.rb 

<h2>Pleae fill below order</h2>
<%- form_tag '/list', :method => 'get' do %>
 <p>
   Name:
   <%= text_field_tag "name" %>
 </p>
 <p>
   Price:
   <%= text_field_tag "price" %>
 </p>
 <p>
   <%= submit_tag 'Place Order' %>
 </p>
<% end %>

index.html.rb


<h1>Below are the orders</h1>
<table>
    <tr>
        <td>Sl. No.</td>
        <td>Name</td>
        <td>Price</td>
    </tr>
    <% sl = 0 %>
    <% @orders.each do |order| %>
        <tr>
            <td><%= sl+=1 %> </td>
            <td><%= order.name %></td>
            <td><%= order.price%></td>
        </tr>
    <%end%>
</table>
<%= link_to "Place new order", url_for(:list, :new) %>

Now let's add ActiveRecord create method to save new entry to database
and get all recored from db.
controller/list.rb
get :index do
    # raise params.inspect
    List.create(params)
    @orders = List.all
    render 'list/index'
  end

  get :new do
    render 'list/new'
  end

Now it's time to start Padrino server.

$ padrino start
=> Padrino/0.11.3 has taken the stage development at http://127.0.0.1:3000
[2013-08-03 01:12:36] INFO  WEBrick 1.3.1
[2013-08-03 01:12:36] INFO  ruby 2.0.0 (2013-05-14) [x86_64-linux]
[2013-08-03 01:12:36] INFO  WEBrick::HTTPServer#start: pid=7392 port=3000

Visit http://localhost:3000 in browser. we will get default sinatra error page.

error

As per ERROR, root of application is missing here. 
We can have the root of our site routed at "root(/)", Need to add root routes in
app.rb and respective view.

app/app.rb

get '/' do
  render '/index'
end
 
and app/view/index.html.rb

<h1>Want to place order?</h1>
<%= link_to "Place order from here", url_for(:list, :new) %>

That's it. 


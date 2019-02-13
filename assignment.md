We’re going to build our first Rails app! Let’s give ourselves some perspective right now. These past weeks you’ve learned about Ruby, HTML, CSS, among other things (Git and Unix). HTML and CSS let you create webpages. Ruby is a programming language and we learned about Ruby because Rails is built on Ruby (hence Ruby on Rails). Rails is a huge library of methods written in Ruby. In other words, the way you use these methods is with Ruby. That’s why we learned it.

Now that we’ve given ourselves some perspective, what we are about to do is make a Rails application. We’re going to recreate [Imgur](http://imgur.com/) to some extent, to the extent you can create a new picture, edit it, delete it, and see all of the pictures created. 

## Learning Goals:

In broad strokes, without using too many words you don’t know quite yet, by the end of this assignment you want to get an idea of the ‘architecture’ Rails is built on—MVC, which stands for Model-View-Controller. In other words you want to know how to save data to a database, know how to configure a Rails app so that given a URL, certain data is retrieved from the database, and how to display that data using HTML and CSS. For a beginner we can imagine the model is the database, the view is what you see, and the controller is responsible for connecting the database and the view. 

Everything else you want to learn in this assignment falls under this bigger idea of MVC. Let it be dynamic routes, parameters, form helpers, migrations, seeds, blah blah blah. The point is: MVC.

## Building This Application



### 1. Creating a New Rails App

Ruby on Rails is a web framework. What this means is it's a bunch of files, and in these files there are lines of code, and you can use these lines of code to build a web application. Otherwise if you built a web application you would have to write many of these lines of code on your own anyways.

So when we call someone a Rails developer what we're saying is they understand how to manipulate these lines of code to make the web application they want to build. This is exactly what we're going to learn how to do over the course of the next few weeks.

Lets start by creating a project . Open the command line and run the following  command 

**`rails new NAME_OF_YOUR_APP`**
The code you're starting with is an empty Rails project that we have generated for you.

If you open this directory in your text editor you can see all of these files and these files in directories on the left-hand side:

![Directory Structure of Rails](http://i.imgur.com/oOISUe4.png)

This is very overwhelming! We just went from zero files to A LOT. Luckily for you, for now, you can  ignore all of them except for the following:

**`db/`**

This is where all the stuff related to your database goes.

**`config/`**

Files that configure your app go here, for example, files that configure your routes or your database.

**`app/`**

You'll spend most of your time here. This is where the majority of the code you'll write lives, from the logic of the app (Ruby) to what you actually see (HTML, CSS, images).

You can read more about the directory structure of Rails on [railstutorial.org](https://www.railstutorial.org/book/beginning#_table-rails_directory_structure).


### 2. Getting http://localhost:3000/pictures to work


Now that we've created a Rails app, if we want to visit its webpages we have to run a server. The way we do this is with:

```bash
$ rails server
```

(Make sure you're inside the folder `photogur/` first.)

What this will do is run a server at the URL [http://localhost:3000](http://localhost:3000). Visit the URL and see what happens! 

We want to create a new webpage at [http://localhost:3000/pictures](http://localhost:3000/pictures). We want this webpage to show a list of pictures. If you visit the URL though you'll get a Routing error saying `No route matches [GET] "/pictures"`. The way you fix this error is by going into your Routes file and adding some code so that it looks like this:

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  get 'pictures' => 'pictures#index'

  # Ignore the comments below for now
  # They are just documentation
end
```

Now if you go to [http://localhost:3000/pictures](http://localhost:3000/pictures) you should see a new message. This is Rails telling you the next thing you need to do to get the URL to work. It should say `uninitialized constant PicturesController`. To fix this we need to create a file with a very specific name in a very specific location. Luckily Rails has "generators" to help you define and create special files.

```bash
$ rails generate controller pictures
```

You'll noticed that this ends up generating many files. Ignore the others for now, we'll focus on **`app/controllers/pictures_controller.rb`**, which should look like the following:

```ruby
class PicturesController < ApplicationController

end
```

`ApplicationController` is a class that is given to us by Rails. `PicturesController` is a new class we're making. If we visit [http://localhost:3000/pictures](http://localhost:3000/pictures) now, we should see yet another error.

Now it should be saying "Unknown Action" and below it "The action 'index' could not be found for PicturesController". To fix this we need to add some code to `pictures_controller.rb`:

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController
  def index
  end
end
```

If you visit the URL yet again, can you guess what will happen? Yep, another error.

We see a "Template is missing" error. Just like when we made our controller, we have to create another file with a very specific name in a very specific location (inside a folder called `app/views/pictures/`).

```bash
$ touch app/views/pictures/index.html.erb
```

Now if you go to [http://localhost:3000/pictures](http://localhost:3000/pictures), you won't see anything! Isn't that better than an error!

What's happening is the file you created, `index.html.erb`, doesn't have anything in it. Let's add `<h1>Pictures</h1>` to it and it should show up.

![Adding title to index.html.erb](http://i.imgur.com/3aNNzca.png?1)

Remember what we wanted at the beginning was to show a list of pictures at this URL. What we need now are the actual pictures.

But let's make a commit right now because we've done some good work.

```bash
$ git add --all
$ git commit -m "Create route,  controller and view for http://localhost:3000/pictures"
```

You can understand now what this line in `routes.rb` does now:

```ruby
get 'pictures' => 'pictures#index'
```

In English, this line of code says "match any HTTP `GET` Request for the URL `/pictures` to the `index` action in the `PicturesController`".

#### What we covered in this commit
* `$ rails server`
* [http://localhost:3000](http://localhost:3000) renders default page
* Creating a route, the controller, and an action for [http://localhost:3000/pictures](http://localhost:3000/pictures)


### 3. Creating Picture model in database


When you go to Imgur and you upload a picture, the picture is saved to their database. So when users come to our website and they make new pictures, we need to save them into the database. Let's forget about Rails and this whole tutorial for a second.

In real life, when you get new data what do you do if you want to record it and make it easy to read? Well, one of the things you could do is draw a table on a piece of paper. If you have data concerning pictures, you could have rows of pictures and columns of different attributes belonging to each picture.

<table>
  <th>
     <td>Title</td>
     <td>Artist</td>
     <td>URL</td>
  </th>
  <tr>
    <td>Picture #1</td>
    <td>Starry Night</td>
    <td>Vincent Van Gogh</td>
    <td>https://wisetoast.com/wp-content/uploads/2015/10/Starry-Night-by-Vincent-Van-Gogh-painting.jpg</td>
  </tr>
  <tr>
    <td>Picture #2</td>
    <td>Smile</td>
    <td>Unknown</td>
    <td>https://pizzabottle.com/wp-content/uploads/2018/02/3-13.jpg</td>
  </tr>
  <tr>
    <td>Picture #3</td>
    <td>Joy</td>
    <td>Life Magazine</td>
    <td>https://s4.scoopwhoop.com/anj/gfgergergrafsfsf/435502866.jpeg</td>
  </tr>
</table>

This is exactly what we do if we want to be able to save pictures we've created. In Rails, the table is called a model. You have a *model* of a Picture, and you can create *instances* of this model to create new pictures.

To generate a new model type this into your command line:

```bash
$ rails generate model Picture artist:string title:string url:string
```

After `rails generate model Picture` are the attributes you want to give to the model. In this case we're saying we want three attributes, `artist`, `title`, and `url`, and we want each of them to have the datatype string. If we had an attribute for `file_size`, the datatype would be an integer (i.e. `file_size:integer`).

When you run this command Rails will create many files, but for now let's focus on two. If you open this file in your editor you'll see:

**`app/models/picture.rb`**:

```ruby
class Picture < ApplicationRecord
end
```

Here we're creating a new class called Picture that inherits from `ActiveRecord::Base`.

The second file the command creates is `[timestamp]_create_pictures.rb` in `db/migrate`. [timestamp] will be a string of what seems random numbers but actually are the date and time when you ran the command. In this file you'll find:

```ruby
class CreatePictures < ActiveRecord::Migration[5.0]

  def change
    create_table :pictures do |t|
      t.string :artist
      t.string :title
      t.string :url

      t.timestamps
    end
  end
end
```

This file tells Rails how to change the database in the way we want. We actually need to run this file and apply these changes!

```bash
$ rails db:migrate
```

Whenever you create a new model, a new *migration* is automatically made for you. Migrations are instructions you write for how you want the database to be changed. By saving the instructions into files, as opposed to doing it, for example, on the command line, other people can clone these instructions and run them right away to get exactly the same database you have.

With that, we've created a new model for Picture and migrated the database. Let's commit:

```bash
$ git add --all
$ git commit -m "Creating Picture model in database"
```

### What we covered in this commit
* How to generate a new model: `$ rails generate model Picture artist:string title:string url:string`
* How to migrate changes to the database: `$ rails db:migrate`


### 4. Creating some default data
[Commit `f2a44c3`]



Now that we have a Picture model, we should be ready to list all our pictures. However, we don't have any data yet to display.

Let's add some default data to our database. We can do that with the 'seeds' file.

**`db/seeds.rb`**:

```ruby
Picture.create(
  title:  'Starry Night',
  artist: 'Vincent Van Gogh',
  url:    'https://wisetoast.com/wp-content/uploads/2015/10/Starry-Night-by-Vincent-Van-Gogh-painting.jpg'
)

Picture.create(
  title:  'Smile',
  artist: 'Unknown',
  url:    'https://pizzabottle.com/wp-content/uploads/2018/02/3-13.jpg'
)

Picture.create(
  title:  'Joy',
  artist: 'Life Magazine',
  url:    'https://s4.scoopwhoop.com/anj/gfgergergrafsfsf/435502866.jpeg'
)
```

`Picture` with a capital lets Rails know you are talking about the model. So `Picture.create` creates a new instance of the model `Picture`.

To run these seeds and populate the database with these pictures we type this on the command line:

```bash
$ rails db:seed
```

#### What we covered in this commit
* Creating pictures in `seeds.rb`
* Running `seeds.rb` with `$ rails db:seed`

### 5. Making pictures show at /pictures



We can now use the data we just created in our controller to display in our view.

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController

  def index
    @pictures = Picture.all
  end

end
```

We can use these pictures in the view, `index.html.erb`, through the variable `@pictures`, even though it is defined in the controller, `pictures_controller.rb`.

So we could render these pictures in the view:

**`app/views/pictures/index.html.erb`**:

```erb
<h1>Pictures</h1>

<% @pictures.each do |picture| %>
  <p><%= picture.title %>, <%= picture.artist %></p>
  <img src="<%= picture.url %>">
<% end %>
```

You may have noticed the format of the file is ERB. ERB stands for "embedded Ruby" and it allows you to embed Ruby into a text document. Anything between `<%=` and `%>` is Ruby code, and whatever the Ruby code returns is put into that spot in the document.

So `<%= picture.title %>` returns the title of the picture, and `<%= picture.artist %>` returns the artist of the picture, so the line:

`<p><%= picture.title %>, <%= picture.artist %></p>`

becomes, for example:

`<p>The old church on the coast of White sea, Sergey Ershov</p>`

As you know, we can use Ruby in other ways. We can be given an array of pictures, and get each picture from the array. This is exactly what the line `<% @pictures.each do |picture| %>` is doing.

One important note to make is <% %> (without the equals sign) does not put what the code returns into the document. So if you did:

`<p><% "My name is Neo" %></p>`

That string would not be returned into the document:

`<p></p>`

Visit [http://localhost:3000/pictures](http://localhost:3000/pictures) and you should see the three pictures with their titles and artists!

Let's make a commit.

```bash
$ git add --all
$ git commit -m "Making pictures show at /pictures"
```

#### What we covered in this commit
* Creating the view (`index.html.erb`)
* Creating `@pictures` in `pictures#index` (in other words in the `index` action in the `PicturesController`)
* Writing HTML and ERB code to render `@pictures`

### 6. Making each picture show at its own URL with dynamic routes



What we want to do now is be able to see each picture individually, at their own URLs. First things first, the routes!

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  get 'pictures' => 'pictures#index'
  get 'pictures/:id' => 'pictures#show', as: 'picture'
end
```

When we say `'pictures/:id'`, what we're saying is anything can take the place of `:id` – think of it as a wildcard. So this URL will route `pictures/1`, `pictures/2`, `pictures/10000`, even `pictures/hello`. (You might think this means the order you put the routes in matters, and you'd be right.)

When we say `as: "picture"`, what we're saying is let us refer to this route as `picture_path`: the String is prepended to `_path`. So if I said `as: "hello"`, the path would be `hello_path`. Moreover, because this route requires an ID (the wildcard, remember?), when we use this path we need to pass in an ID, so for example, `picture_path(1)`.

Now that we've created the routes we need to define the new action in the controller that we already have (`PicturesController`). Eventually in the view we want to be able to render the picture corresponding to the URL, so we need to find the right picture in the action.

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController

  def index
    @pictures = Picture.all
  end

  def show
    @picture = Picture.find(params[:id])
  end

end
```

Now let's create the view associated to this action:

```bash
$ touch app/views/pictures/show.html.erb
```

In this view we want the following:

**`app/views/pictures/show.html.erb`**:

```erb
<p><%= @picture.title %>, <%= @picture.artist %></p>
<img src="<%= @picture.url %>">
```

If you visit [http://localhost:3000/pictures/1](http://localhost:3000/pictures/1), [http://localhost:3000/pictures/2](http://localhost:3000/pictures/2), or [http://localhost:3000/pictures/3](http://localhost:3000/pictures/3), you'll see a different picture at each URL.

Great. Now you can make a paparazzi website. I'm totally enthusiastic about that.

Remember to commit:

```bash
$ git add --all
$ git commit -m "Making each picture show at its own URL with dynamic routes"
```

#### What we covered in this commit
* Using dynamic routes (`get 'pictures/:id' => 'pictures#show'`)
* Creating actions in the controller (`def show; end`)
* Creating views (`show.html.erb`)


### 7. Adding feature to create new picture using HTML form


What we want now is an ability to create new pictures by filling out a form. As always the first file we touch is routes.rb.

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  get 'pictures' => 'pictures#index'

  post 'pictures' => 'pictures#create' # this is a new line of code
  get 'pictures/new' => 'pictures#new' # this is also a new line of code

  get 'pictures/:id' => 'pictures#show', as: 'picture'
end
```

This time we created two new routes. You'll see why we need this. For now let's create the actions in the controller:

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController
  .
  .
  .
  def new
  end

  def create
  end
end
```

(Instead of copying and pasting the whole file, I'm going to start just showing the new lines of code to add to the file and I'll trust you to put them in the right place.)

We have to create the view now. Let's take a step back here for a moment. Have you started to notice a pattern? We work on the routes, then the controller, write some actions, and then make views? We call this RCAV, it stands for Routes, Controller, Actions, and Views.

We need to create a new view named `new.html.erb` and inside of it we are going to make a form. Let's use something Rails provides us that are called **Form helpers**. These Form helpers are methods you can invoke that write HTML code for us. This also generates special code that ensures that Rails is secure.

**`app/views/pictures/new.html.erb`**:

```HTML+ERB
<h1>Add a picture</h1>
<%= form_tag "/pictures" do %>
  <p>
    <%= label_tag :artist %><br>
    <%= text_field_tag :artist %>
  </p>
  <p>
    <%= label_tag :title %><br>
    <%= text_field_tag :title %>
  </p>
  <p>
    <%= label_tag :url %><br>
    <%= text_field_tag :url %>
  </p>
  <p>
    <%= submit_tag "Save" %>
  </p>
<% end %>
```

To use the methods Rails provides us you'll notice we have to do use ERB.

Now we can talk about why we needed a second route. When we click on the Submit button for this form it will go to `POST /pictures`. When we click on Submit we want the new page to have the Title, Artist, and URL we put in the form, so let's update our `create` action in our controller:

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController
  .
  .
  .
  def create
    render text: "Saving a picture. URL: #{params[:url]}, Title: #{params[:title]}, Artist: #{params[:artist]}"
  end
end
```

Just like how we can get the wildcard in the URL from the `params` hash, we can get data from a form too!

Now when I fill in this form:

![Screenshot of filling in the form](http://i.imgur.com/DdydvoJ.png)

And I submit it I get:

![Screenshot of form submission](http://i.imgur.com/M1dE17K.png)

You might notice the picture isn't saved at all in any real sense. If you go to `/pictures`, you certainly won't see it there. We'll fix this later, in the meantime, *ssshhhhh*.

Commit:

```bash
$ git add --all
$ git commit -m "Adding feature to create new picture using HTML form"
```

#### What we covered in this commit
* Creating [/pictures/new](/pictures/new), `pictures#new`, and `new.html.erb`
* Using Form helpers to generate a form

### 8. Refactoring to save new pictures in database


Now we can get new pictures to be saved into the database! We just have to refactor our controller:

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController
  .
  .
  .
  def new
    @picture = Picture.new
  end

  def create
    # make a new picture with what picture_params returns (which is a method we're calling)
    @picture = Picture.new(picture_params)
    if @picture.save
      # if the save for the picture was successful, go to index.html.erb
      redirect_to pictures_url
    else
      # otherwise render the view associated with the action :new (i.e. new.html.erb)
      render :new
    end
  end

  private
  def picture_params
    params.require(:picture).permit(:artist, :title, :url)
  end

end
```

You'll notice we had to define a new Picture in the new action. This is because we are going to update the view with this:

**`app/views/pictures/new.html.erb`**:

```erb
<h1>Add a picture</h1>
<%= form_for @picture do |f| %>
  <p>
    <%= f.label :artist %><br>
    <%= f.text_field :artist %>
  </p>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>
  <p>
    <%= f.label :url %><br>
    <%= f.text_field :url %>
  </p>
  <p>
    <%= f.submit "Save" %>
  </p>
<% end %>
```

Now the form is for `@picture`, which comes from the model in the controller. In other words the form is now setup to work with the model.

And there you have it! Now when you fill out the form and click the Submit button, you should see your new picture on the index page. We're well on our way to a MVP, a minimal viable product.

Commit:

```bash
$ git add --all
$ git commit -m "Refactoring to save new pictures in database"
```

#### What we covered in this commit
* Updating `new` and `create` actions in `PicturesController`
* Updating `new.html.erb` with `form_for` helper

### 9. Configuring root as pictures#index


Now we're going to look after a smaller detail in this commit. If you go to [http://localhost:3000](http://localhost:3000), the default view is still rendered. This is hardly professional! This needs fixing immediately.

We need to configure the routes so something shows up at that URL:

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  root 'pictures#index'
  get 'pictures' => 'pictures#index'
  .
  .
  .
end
```

Now [http://localhost:3000](http://localhost:3000) renders the `index.html.erb` page. Professionalism is never a bad thing.

Commit:

```bash
$ git add --all
$ git commit -m "Configuring root as pictures#index"
```

### 10. Refactoring views to use helpers and adding navigation


What we want to do now is connect all these webpages we've made. We want it so that when we're on the index page we can click a link to go to the page to make a new picture. Or we can click on a picture to go to its show page. And when we're on the new or show page we can go back to the index page.

**`app/views/pictures/new.html.erb`**:

```erb
<h1>Add a picture</h1>
<%= link_to "Back to Pictures", pictures_url %>

.
.
.
```

Notice we are using a helper, `link_to`, to make a link. The first parameter is the text and the second parameter is the URL clicking the text takes you to. In this case clicking on "Back to Pictures" takes you to the path `pictures_url`, which is `/pictures`.

**`app/views/pictures/index.html.erb`**:

```erb
<h1>Pictures (<%= @pictures.length %>)</h1>
<%= link_to "New Picture", "/pictures/new" %>

<% @pictures.each do |picture| %>
  <p><%= picture.title %>, <%= picture.artist %></p>
  <%= link_to image_tag(picture.url), "/pictures/#{picture.id}" %>
<% end %>
```

Notice we are using a helper now, `image_tag`, to render the picture, which we just have to pass in the picture's URL.

**`app/views/pictures/show.html.erb`**:

```erb
<%= link_to "Back to Pictures", pictures_url %>

<p><%= @picture.title %>, <%= @picture.artist %></p>
<%= image_tag @picture.url %>
```

Commit:

```bash
$ git add --all
$ git commit -m "Refactoring views to use helpers and adding navigation"
```

#### What we covered in this commit:
* Adding links in views to connect each other using `link_to` and `image_tag`

### 11. Adding feature to edit pictures


Now we want to be able to, when we're on the show page of a picture, click a link to edit it. First we have to make a route for the edit page:

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  .
  .
  .
  get 'pictures/:id/edit' => "pictures#edit", as: "edit_picture"
  patch 'pictures/:id' => "pictures#update"
  .
  .
  .
end
```

Then we have to make two new actions in the controller:

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController
  .
  .
  .
  def edit
    @picture = Picture.find(params[:id])
  end

  def update
    @picture = Picture.find(params[:id])

    if @picture.update_attributes(picture_params)
      redirect_to "/pictures/#{@picture.id}"
    else
      render :edit
    end
  end
end
```

Create a new view for edit:

**`app/views/pictures/edit.html.erb`**:

```erb
<h1>Edit Picture</h1>

<p>
  <%= link_to "Back", @picture %>
</p>

<%= form_for @picture do |f| %>
  <p>
    <%= f.label :artist %><br>
    <%= f.text_field :artist %>
  </p>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>
  <p>
    <%= f.label :url %><br>
    <%= f.text_field :url %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>
```

And last but not least make a link on the show page of each picture to go to the edit form:

**`app/views/pictures/show.html.erb`**:

```erb
<%= link_to "Back to Pictures", pictures_url %> | <%= link_to "Edit", edit_picture_path(@picture) %>
.
.
.
```

Commit:

```bash
$ git add --all
$ git commit -m "Adding feature to edit pictures"
```

#### What we covered in this commit
* New route `/pictures/:id/edit`
* `edit` and `update` actions in `PicturesController`
* `edit.html.erb`
* Adding link to `edit.html.erb` in `show.html.erb`

### 12. Adding feature to delete pictures


This is our last commit! We've come a long way. The last thing we want to do is be able to delete a picture. You know, sometimes we regret a picture we've uploaded and we want to delete it. For a feature that is so important there is actually surprisingly little code. Write the delete route in our routes.

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  .
  .
  .
  delete 'pictures/:id' => 'pictures#destroy', as: "delete_picture"

  root 'pictures#index'
end
```

There is an action to be made in the controller:

**`app/controllers/pictures_controller.rb`**:

```ruby
class PicturesController < ApplicationController
  .
  .
  .
  def destroy
    @picture = Picture.find(params[:id])
    @picture.destroy
    redirect_to pictures_url
  end
  .
  .
end
```

And a link to be made in the show page:

```erb
<%= link_to "Back to Pictures", pictures_url %> | <%= link_to "Edit", edit_picture_path(@picture) %> | <%= link_to "Delete", delete_picture_path(@picture), method: :delete, data: {confirm: "Are you sure you want to delete this picture?"}  %>
```

`data:confirm` is the way of telling Rails you want to popup a dialog box and confirm with the user. `method:` lets Rails know what HTTP request to use on the URL. So the URL is `/picture` (which is denoted in this code with `@picture`), and the HTTP request is DELETE, which is obviously a different route from `GET /pictures`.

And there you have it! You can delete a picture now.

Commit:

```bash
$ git add --all
$ git commit -m "Adding feature to delete pictures"
```

#### What we covered in this commit
* Destroy action in controller
* Link to delete picture in `show.html.erb`

### 13. Refactoring routes to use resources


Many applications follow the CRUD pattern—create, read, update, and delete. If you look at our routes now:

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  root 'pictures#index'

  get 'pictures' => 'pictures#index'
  get 'pictures/new' => 'pictures#new'
  get 'pictures/:id/edit' => 'pictures#edit', as: 'edit_picture'
  get 'pictures/:id' => 'pictures#show', as: 'picture'
  
  post 'pictures' => "pictures#create"
  patch 'pictures/:id' => "pictures#update”
  delete 'pictures/:id' => 'pictures#destroy', as: "delete_picture"

end
```

You'll see they follow this CRUD pattern. Because us web developers are way too busy, Rails offers us a nice way to make all of these routes in one line of code, in addition to another one for deleting a picture, which we'll get to in the next commit.

**`config/routes.rb`**:

```ruby
Rails.application.routes.draw do
  root 'pictures#index'
  
  resources :pictures
end
```

Let's refactor our routes to do this!

##### One more thing

Now we need to change the delete link.

Currently we have `<%= link_to "Delete", delete_picture_path(@picture), method: :delete ...`

We need to change that to `<%= link_to "Delete", @picture, method: :delete ...`

Now let's commit!

```bash
$ git add --all
$ git commit -m "Refactoring routes to use resources"
```

#### What we covered in this commit
* `resources :pictures`

***

Congratulations. You just made your first Rails app. How do you feel?

![](http://i891.photobucket.com/albums/ac118/ROBsessedBlog2/gifs/Non%20Rob%20Funnies/DONE.gif)

### Next Step: Do It Again!
The more you do this, the better you'll be able to understand it and remember all the steps! Familiarize yourself with this project by starting over from scratch at least one more time. Try renaming variables (ie. `images` instead of `pictures`) so that you're not just copy/pasting all the different code bits.

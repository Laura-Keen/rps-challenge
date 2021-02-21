# Instructions

## Initial Set-up

1) Check `gemfile`, check for:
    - `capybara`
    - `sinatra`
    - `rspec`

2) Run `bundle` to install gems

3) Create a new file called `app.rb` in the root of the project ready to set up an application called `Game`

3) In `app.rb`, `require 'sinatra/base'`

4) Create a `class` called `Game` and add `Sinatra::Base` to it:

    `class Game < Sinatra::Base`

5) At the bottom of the `class`, before the `end` statement add a line that will start the server if the ruby file is executed directly:

    `run! if app_file == $0`

6) In the root of the project, create a `config.ru` file - this will be used to run the app

7) Inside the `config.ru`, do:
  ```
    require_relative "./app"
    run Game
  ```

8) Run `rspec` and check that there are `0 examples` and `0 failures`

9) Run `rackup` to see the port number

10) In the browser, use the port number to enter the url:

    localhost:9292

11) In app.rb make first get request to add the title of the game to the homepage:
```
    get '/' do
      'Rock, Paper, Scissors`
    end
```

## Setting Up Tests

12) Set up `spec_helper` with what it needs to prepare Capybara and to get Capybara to test the app, starting with chaning the rack environment to test:

    `ENV['RACK_ENV'] = 'test'`

13) In `spec_helper`, require the sinatra app file:

    `require File.join(File.dirname(__FILE__), '..', 'app.rb')`

14) In `spec_helper`, tell Capybara about the app class:

    `Capybara.app = Game`

15) Inside the `spec` directory, create a new directory called `features`. This is to store feature tests.

16) Inside the `features` directory, add a new file for the first feature test to check if the testings set up works:

    `touch spec/features/testing_spec.rb`

17) Inside this file, write a simple test to check that the homepage can be visited and to check that it has some content:
```
    feature 'Testing setup' do
      scenario 'Run app and check homepage has content' do
        visit('/')
        expect(page).to have_content 'Rock, Paper, Scissors'
      end
    end
```

18) Run `rspec` and check there are `1 example, 0 failures`


## Building The Game

**User Story 1:**
*As a marketeer So that I can see my name in lights I would like to register my name before playing an online game*

1) In the features directory, make a new file for a feature test:

    `touch spec/features/enter_name_spec.rb`

2) Write a capybara feature test to check the user can enter their name:
```
    feature 'Enter player name' do
      scenario 'Submits players name' do
        visit('/')
        fill_in :player_name, with: 'Dave'
        click_button 'Submit'
        expect(page).to have_content 'Dave'
      end
    end
```

3) Run `rspec` and make sure there is a failing test with an `Unable to find field :player_name` error

4) To fix the error, in `app.rb`, add a route that gets a `homepage.erb` view:
```
    get '/' do
      erb :homepage
    end
```
*NB:* Make sure to delete or comment out the `'Rock, Paper, Scissors'` text and comment out the test for it.

5) Create a new directory called `views` to store all erb documents:

    `mkdir views`

6) Inside the `views` directory, create a file called `homepage.erb`

    `touch views/homepage.erb`

7) Inside the `homepage.erb` file, solve the error by writing the code:

    `<input type="text" name="player_name">`

8) The new error should say that it expected to find the text `'Dave'` on the page

9) Fix this error in `homepage.erb` by adding a form and a `submit` button:
```
    <form>
      <input type="text" name="player_name"
      <input type="submit" name="Submit"
    </form>

10) The test still fails because capybara tries to submit the form and fails. This can be fixed by pointing the form to a specific route ans using a post request. The post request makes the server 'remember' the names.
Inside the opening form tag:

    <form action="/names" method="post">

11) In app.rb add the `post` request route:
```
    post '/name' do
      erb :play
    end
```

12) For the `post` request to work, make a new erb view called `play`:

    `touch views/play.erb`

This document is where the players name will be displayed. Use erb syntax to add a `@player_name` instance variable.

13) To be able to pass the instance variables into the view, extract them from the `params` in the `post '/names'` request
In  `app.rb`:
```
    post '/names' do
      @player_name = params[:player_name]
      erb :play
    end
```

14) Run `rspec` and make sure all tests pass
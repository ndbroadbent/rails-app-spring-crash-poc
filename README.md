# README

Trying to reproduce an issue where Spring would freeze if Rails application crashes while loading.

## Steps

* `rails new rails-app --skip-test`
* Update `Gemfile`:
  * Add `gem "rspec-rails"` in `group :development, :test`.
  * Uncomment `gem "spring"`  
  * Add `gem "spring-commands-rspec"` under `gem "spring"`
* `bundle install`
* `rails generate rspec:install`
* `bundle exec spring binstub --all`
* Create file at `spec/example_spec.rb` with the following contents:

```ruby
RSpec.describe do
  it "should be true" do
    expect(true).to be true
  end
end
```

* Run `./bin/rspec` to make sure everything is working:

```
$ ./bin/rspec
DEBUGGER: Attaching after process 46800 fork to child process 46892
Running via Spring preloader in process 46892
.

Finished in 0.00293 seconds (files took 0.07238 seconds to load)
1 example, 0 failures
```

* Raise an exception in `config/application.rb`:
  * `echo "raise 'test'" >> config/application.rb`

* Run `./bin/rspec`:

```
$ ./bin/rspec
/Users/ndbroadbent/code/rails-app-spring-crash/config/application.rb:40:in `<top (required)>': test (RuntimeError)
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application.rb:92:in `require'
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application.rb:92:in `preload'
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application.rb:166:in `serve'
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application.rb:148:in `block in run'
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application.rb:142:in `loop'
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application.rb:142:in `run'
	from /Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/gems/3.1.0/gems/spring-4.1.0/lib/spring/application/boot.rb:19:in `<top (required)>'
	from <internal:/Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/3.1.0/rubygems/core_ext/kernel_require.rb>:85:in `require'
	from <internal:/Users/ndbroadbent/.rbenv/versions/3.1.2/lib/ruby/3.1.0/rubygems/core_ext/kernel_require.rb>:85:in `require'
	from -e:1:in `<main>'
```

If Spring is not configured correctly then it may freeze.

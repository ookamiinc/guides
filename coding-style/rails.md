# Rails

We follows a simple set of coding style conventions:

* Use American English. See [a list of American and British English spelling differences here](http://en.wikipedia.org/wiki/American_and_British_English_spelling_differences).
* Two spaces, no tabs (for indentation).
* No trailing whitespace. Blank lines should not have any spaces.
* Indent after private/protected
* Prefer { a: :b } over { :a => :b }
* Prefer &&/|| over and/or
* Use MyClass.my_method(my_arg) not my_method( my_arg ) or my_method my_arg
* Use a = b and not a=b
* Prefer method { do_stuff } instead of method{do_stuff} for single-line blocks.
* Follow the conventions in the source you see used already

The most of these list come from http://guides.rubyonrails.org/contributing_to_ruby_on_rails.html#follow-the-coding-conventions

## Check Your Code

Just Run

    rubocop -RD

## Configuration

* Put custom initialization code in `config/initializers`.
* Keep initialization code for each gem in separate file with the same name as its gem, for example `carrierwave.rb`, `devise.rb`, etc.
* Adjust accordingly the settings for development, test and production environment (in the corresponding files under `config/environments/`)
* Keep configuration that's applicable to all environments in the `config/application.rb` file.

## Controllers

* Keep the controllers skinny - they should only retrieve data for the view layer and shouldn't contain any business logic (all the business logic should naturally reside in the model).
* Each controller action should (ideally) invoke only one method other than an initial find or new.

## Models

* Name the models with meaningful (but short) name without abbreviations.

### ActiveRecord

* Avoid altering ActiveRecord defaults (table names, primary key, etc) unless you need a very good reason
* Group macro-style methods (`has_many`, `validates`, etc) in the beginning of the class definition.

  ```Ruby
  class User < ActiveRecord::Base
    # keep the default scope first (if any)
    default_scope { where(active: true) }

    # constants come up next
    GENDERS = %w(male female)

    # afterwards we put attr related macros
    attr_accessor :formatted_date_of_birth

    attr_accessible :login, :first_name, :last_name, :email, :password

    # followed by association macros
    belongs_to :country

    has_many :authentications, dependent: :destroy

    # and validation macros
    validates :email, presence: true
    validates :username, presence: true
    validates :username, uniqueness: { case_sensitive: false }
    validates :username, format: { with: /\A[A-Za-z][A-Za-z0-9._-]{2,19}\z/ }
    validates :password, format: { with: /\A\S{8,128}\z/, allow_nil: true}

    # next we have callbacks
    before_save :cook
    before_save :update_username_lower

    # other macros (like devise's) should be placed after the callbacks

    ...
  end
  ```

references
* https://github.com/bbatsov/rails-style-guide
* https://github.com/bbatsov/ruby-style-guide
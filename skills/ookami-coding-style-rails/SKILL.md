---
name: ookami-coding-style-rails
description: >
  ookami team Rails and Ruby coding style conventions. Use when writing,
  modifying, or reviewing Ruby or Rails code, including models, controllers,
  configurations, and migrations.
---

# Rails Coding Style

Follow these conventions when writing Ruby/Rails code.
Full reference: https://github.com/ookamiinc/guides/blob/master/coding_style/rails.md

## General Style

- Use American English.
- Use 2-space indentation, no tabs.
- No trailing whitespace. Blank lines should not have any spaces.
- Indent after `private`/`protected`.
- Prefer `{ a: :b }` over `{ :a => :b }` (new-style hash syntax).
- Prefer `&&`/`||` over `and`/`or`.
- Use `MyClass.my_method(my_arg)` — not `my_method( my_arg )` or `my_method my_arg`.
- Use `a = b` — not `a=b` (spaces around operators).
- Prefer `method { do_stuff }` over `method{do_stuff}` for single-line blocks.
- Follow the conventions already present in the source code.

## Check Your Code

Run rubocop before submitting:

```
rubocop -RD
```

## Configuration

- Put custom initialization code in `config/initializers`.
- Keep initialization code for each gem in a separate file named after the gem (e.g., `carrierwave.rb`, `devise.rb`).
- Adjust settings for development, test, and production in `config/environments/`.
- Keep configuration applicable to all environments in `config/application.rb`.

## Controllers

- Keep controllers skinny — they should only retrieve data for the view layer and contain no business logic.
- Each controller action should ideally invoke only one method other than an initial `find` or `new`.

## Models

- Use meaningful but short names without abbreviations.

### ActiveRecord Ordering

Avoid altering ActiveRecord defaults unless you have a very good reason. Group macro-style methods at the beginning of the class definition in this order:

1. `default_scope`
2. Constants
3. `attr_accessor` / `attr_accessible` macros
4. `enum` (prefer hash syntax)
5. Associations (`belongs_to`, `has_many`, etc.)
6. Validations (`validates`)
7. Callbacks (`before_save`, etc.)
8. Other macros (devise, etc.)

Example:

```ruby
class User < ActiveRecord::Base
  default_scope { where(active: true) }

  COLORS = %w(red green blue)

  attr_accessor :formatted_date_of_birth
  attr_accessible :login, :first_name, :last_name, :email, :password

  enum gender: { female: 0, male: 1 }

  belongs_to :country
  has_many :authentications, dependent: :destroy

  validates :email, presence: true
  validates :username, presence: true
  validates :username, uniqueness: { case_sensitive: false }

  before_save :cook
  before_save :update_username_lower
end
```

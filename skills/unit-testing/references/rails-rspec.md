# Rails / RSpec Reference

Based on [Better Specs](https://www.betterspecs.org/).

---

## 1. Structure

### describe / context / it

```ruby
RSpec.describe User do
  describe '.authenticate' do          # class method -> dot
    context 'when credentials are valid' do  # "when" / "with" / "without"
      it 'returns the user' do
      end
    end
  end

  describe '#admin?' do                # instance method -> hash
    context 'when user has admin role' do
      it { is_expected.to be_admin }
    end
  end
end
```

```ruby
# Bad
describe 'the authenticate method for User' do
describe 'if the user is an admin' do

# Good
describe '.authenticate' do
context 'when user is an admin' do
```

### Naming

Short (under 40 chars). Third-person present tense. No "should".

```ruby
# Bad
it 'should not change timings' do
it 'has 422 status code if an unexpected params will be added' do

# Good
it 'does not change timings' do
context 'when not valid' do
  it { is_expected.to respond_with 422 }
end
```

---

## 2. subject / let / before

### subject

```ruby
# Implicit
subject { described_class.new(params) }
it { is_expected.to be_valid }

# Named — for clarity in complex tests
subject(:hero) { Hero.first }
it 'carries a sword' do
  expect(hero.equipment).to include 'sword'
end
```

### let / let!

```ruby
# Bad — instance variables
before { @resource = FactoryBot.create :device }

it 'sets the type_id' do
  expect(@resource.type_id).to eq(@type.id)
end

# Good — let (lazy, cached per example)
let(:resource) { FactoryBot.create :device }
let(:type) { Type.find resource.type_id }

it 'sets the type_id' do
  expect(resource.type_id).to eq(type.id)
end
```

- `let` — lazy, evaluated on first reference
- `let!` — eager, use only when side effect must happen before the test
- `before` — for setup that isn't a variable (e.g., `sign_in`)

---

## 3. Expectations

### One behavior per test

```ruby
# Isolated — preferred for unit tests
it { is_expected.to respond_with_content_type(:json) }
it { is_expected.to have_http_status(:ok) }

# Grouped — acceptable for integration tests
it 'creates a resource' do
  aggregate_failures do
    expect(response).to have_http_status(:created)
    expect(parsed_body['name']).to eq('Test')
  end
end
```

### Cover all cases

```ruby
describe '#destroy' do
  context 'when resource is found' do
    it 'responds with 200'
    it 'shows the resource'
  end

  context 'when resource is not found' do
    it 'responds with 404'
  end

  context 'when resource is not owned' do
    it 'responds with 404'
  end
end
```

---

## 4. Test Data

### Factories over fixtures

```ruby
# Bad — manual creation
user = User.create(name: 'Test', email: 'test@example.com', active: true)

# Good — factory
user = create(:user)

# Better — build (no DB hit)
user = build(:user)

# Fastest — build_stubbed (read-only, no DB callbacks/associations)
user = build_stubbed(:user)
```

### Minimal data

```ruby
describe '.top' do
  before { create_list(:user, 3) }

  it 'returns the requested number' do
    expect(User.top(2)).to have(2).items
  end
end
```

- Use traits for common variations: `create(:article, :published)`
- Avoid deeply nested factory associations

---

## 5. Mocking & Stubbing

### Basics

```ruby
# Stub
allow(service).to receive(:call).and_return(result)

# Verify
expect(service).to have_received(:call).with(expected_args)
```

### instance_double (always prefer over double)

```ruby
let(:mailer) { instance_double(UserMailer) }

before do
  allow(UserMailer).to receive(:welcome).and_return(mailer)
  allow(mailer).to receive(:deliver_later)
end
```

### HTTP stubbing with WebMock

```ruby
context 'with unauthorized access' do
  before do
    stub_request(:get, 'https://api.example.com/types')
      .to_return(status: 401, body: fixture('401.json'))
  end

  it 'returns access denied' do
    page.driver.get uri
    expect(page).to have_content 'Access denied'
  end
end
```

---

## 6. Shared Examples

```ruby
# Bad — duplicated test logic across specs

# Good — extract shared behavior
describe 'GET /devices' do
  let!(:resource) { create(:device, created_from: user.id) }

  it_behaves_like 'a listable resource'
  it_behaves_like 'a paginable resource'
  it_behaves_like 'a searchable resource'
end
```

---

## 7. Testing Patterns

### Service Objects

Follow the project's tuple return pattern `[result, status]`.

```ruby
RSpec.describe PickFactory do
  describe '.create' do
    let(:user) { create(:user) }
    let(:stream) { create(:stream) }
    let(:team) { create(:team) }
    let(:params) { { stream_id: stream.id, team_id: team.id } }

    context 'with valid params' do
      it 'returns the pick and 201 status' do
        result, status = described_class.create(user, params)

        aggregate_failures do
          expect(status).to eq(201)
          expect(result).to be_a(Pick)
        end
      end
    end

    context 'with invalid params' do
      let(:params) { { stream_id: nil } }

      it 'returns error and 422 status' do
        _result, status = described_class.create(user, params)
        expect(status).to eq(422)
      end
    end
  end
end
```

### Models

```ruby
RSpec.describe User do
  describe 'validations' do
    it { is_expected.to validate_presence_of(:email) }
    it { is_expected.to validate_uniqueness_of(:email) }
  end

  describe 'associations' do
    it { is_expected.to have_many(:picks) }
    it { is_expected.to belong_to(:team).optional }
  end

  describe '#active?' do
    context 'when last login is within 30 days' do
      subject { build(:user, last_login_at: 1.day.ago) }

      it { is_expected.to be_active }
    end

    context 'when last login is over 30 days ago' do
      subject { build(:user, last_login_at: 31.days.ago) }

      it { is_expected.not_to be_active }
    end
  end
end
```

### Request Specs

```ruby
RSpec.describe 'GET /api/v1/users', type: :request do
  let(:user) { create(:user) }

  before do
    get '/api/v1/users', params: { auth_token: user.auth_token }
  end

  it { expect(response).to have_http_status(:ok) }

  it 'returns users as JSON' do
    expect(parsed_body).to be_an(Array)
  end
end
```

---

## 8. Matchers Quick Reference

```ruby
# Equality
expect(value).to eq(expected)       # value equality (most common)
expect(value).to eql(expected)      # type + value
expect(value).to equal(expected)    # object identity (same object)

# Truthiness
expect(value).to be true
expect(value).to be_truthy
expect(value).to be_nil
expect(value).to be_falsey

# Comparisons
expect(value).to be > 5
expect(value).to be_between(1, 10)

# Collections
expect(array).to include(item)
expect(array).to contain_exactly(a, b, c)  # order independent
expect(hash).to include(key: value)

# Changes
expect { action }.to change { Model.count }.by(1)
expect { action }.to change(user, :name).from('old').to('new')

# Errors
expect { action }.to raise_error(SomeError)
expect { action }.to raise_error(SomeError, 'message')
expect { action }.not_to raise_error
```

---

## 9. Performance

| Strategy | Speed | When to Use |
|---|---|---|
| `build_stubbed` | Fastest | Read-only, no DB callbacks/associations/persistence needed |
| `build` | Fast | In-memory, no persistence needed |
| `create` | Slow | Record must exist in DB |
| `create_list` | Slowest | Multiple records needed |

- Use `aggregate_failures` to avoid splitting slow integration tests
- Use database cleaner with transaction strategy
- Use Guard for automatic test execution during development

# Zaikio::Directory

Ruby API Client for Zaikio's Directory.

## Installation

### 1. Add this line to your application's Gemfile:

```ruby
gem 'zaikio-directory'
```

And then execute:
```bash
$ bundle
```

Or install it yourself as:
```bash
$ gem install zaikio-directory
```

### 2. Configure the gem:

```rb
# config/initializers/zaikio_directory.rb

Zaikio::Directory.configure do |config|
  config.host :production # sandbox or production
end
```


## Usage

The Directory Client has an ORM like design. However, we distinguish between the contexts of the organization and the person.

For the requests to work, a valid JSON Web token with the correct OAuth Scopes must always be provided. Please refer to [zakio-oauth_client](#todo).

If you want to know which actions are available and which scopes are required, please refer to the [Directory API Reference](https://docs.zaikio.com/api/directory/directory.html).

### As an organization

```rb
token = "..." # Your valid JWT for an organization
Zaikio::Directory.with_token(token) do
  organization = Zaikio::Directory::CurrentOrganization.new

  # Fetch Data
  organization.memberships.find('abc')
  organization.members.all
  organization.machines.all
  organization.software.all
  organization.sites.all
  organization.sites.find('123')
  organization.business_relationships.all
  organization.fetch
  organization.name

  # Create new resources
  organization.business_relationships.create(target_id: "abcd-efgh", kind: "printer", reference: "a-123")
  organization.machines.create(name: "Speedmaster 2020", manufacturer: "heidelberg", kind: "sheetfed_digital_press", serial_number: "HDB1337", site_id: "d6308910-f5ae-58c0-aba7-d099947845c6")
  organization.sites.create(name: "Mainz", address_attributes: {
    addressee: "Crispy Mountain GmbH",
    text: "Emmerich-Josef-Straße 1A, 55116 Mainz"
  })

  # Delete resources
  organization.machines.first.destroy
end
```

### As a person

```rb
token = "..." # Your valid JWT for a person
Zaikio::Directory.with_token(token) do
  person = Zaikio::Directory::CurrentPerson.new

  # Fetch Data
  person.organizations
  person.organization_memberships
  person.fetch
  person.full_name

  organization = person.admin_organizations.find(&:connected?)
  organization.machines.all
  organization.sites.all

  # Update details
  person.update(first_name: "Lisa", name: "Simpson", pronoun: "She/Her")
end
```

### Other Use Cases

```rb
Zaikio::Directory.with_basic_auth(client_id, client_secret) do
  connections = Zaikio::Directory::Connection.all
end

roles = Zaikio::Directory::Role.all
revoked_access_tokens = Zaikio::Directory::RevokedAccessToken.all

Zaikio::Directory.with_token(token) do
  Zaikio::Directory::RevokedAccessToken.create
end
```

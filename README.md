# Maglev

Faster, smoother iOS development. Pushing RubyMotion as hard as we can.

It's a Rails pun.

Still baking.

## Models

Connect your models to your backend insanely fast.

Imagine `http://thiswebsite.com/users/1.json` returns something like:

```javascript
{
    "id": "123",
    "name": "Clay",
    "posts": [{
        "id": "321",
        "name": "My First Post",
        "body": "Maglev is super neat"
    }]
}
```

We can define our models like this:

```ruby
class User < Maglev::Model
  remote_attributes :id, :name

  has_many :posts

  collection_path '/users'
  member_path '/users/:id'
end

class Post < Maglev::Model
  remote_attributes :id, :name, :body

  belongs_to :user
end
```
Hook up our API like this:

```ruby
Maglev::API.setup do
  root 'http://thiswebsite.com'
end
```

And grab all of our objects with nifty methods:

```
User.find(1) do |user|
  p users.posts.first.body
  # => Maglev is super neat
end
```

What about `/users.json`? It returns a structure like this:

```javascript
{
    "users": [{
        "id": "123",
        "name": "Clay",
        "posts": [...]
    }]
}
```

`User.find_all do |users|` works out of the box.

### Handeling Irregular Structure

Most JSON APIs return keys that don't always correspond to resource names. Maglev handles these flawlessly with the `json_path` property on most resources:

```ruby
class User < Maglev::Model
  remote_attribute :city, json_path: "address.city"

  has_many :posts, json_path: "data"

  collection_path '/users', json_path: "members"
end
```

Thus, our `/users` would return something like: 

```javascript
{
    "members": [{
        "id": "123",
        "name": "Clay",
        "address": {
            "city": "San Francisco",
            "state": "CA"
        }
        "data": [...]
    }]
}
```

Notice how `User#city` is a nested resource inside the JSON; you can dig through the JSON with the value-path syntax.
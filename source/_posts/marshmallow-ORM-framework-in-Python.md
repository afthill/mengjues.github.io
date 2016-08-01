title: ORM？不是数据库的专利
date: 2015-12-29 10:37:41
tags:
- ORM
categories:
- 程序语言

---

项目地址：<https://github.com/marshmallow-code/marshmallow/>

marshmallow提供的是一个轻量级的Python ORM框架，管理python里的复杂的数据。

```
from datetime import date
from marshmallow import Schema, fields, pprint

class ArtistSchema(Schema):
    name = fields.Str()

class AlbumSchema(Schema):
    title = fields.Str()
    release_date = fields.Date()
    artist = fields.Nested(ArtistSchema)


bowie = dict(name='David Bowie')
album = dict(artist=bowie, title='Hunky Dory', release_date=date(1971, 12, 17))

schema = AlbumSchema()
result = schema.dump(album)
pprint(result.data, indent=2)
# { 'artist': {'name': 'David Bowie'},
#   'release_date': '1971-12-17',
#   'title': 'Hunky Dory'}
```

这里的schema类似于数据库里的ORM里的model的概念，也可以设定constraint和foreign key，所以schema可以在这里有如下作用：

- 验证输入的数据完整性，通过fields的validator
- 反序列化（deserialize）input到应用级别的对象
- 序列化（serialize）应用级别的对象到python原生的对象，然后python原生的对象可以
进一步被渲染为其他所需的数据格式（比如JSON）

<!--more-->

### 建模

```
import datetime as dt

class User(object):
    def __init__(self, name, email):
        self.name = name
        self.email = email
        self.created_at = dt.datetime.now()

    def __repr__(self):
        return '<User(name={self.name!r})>'.format(self=self)
```

```
from marshmallow import Schema, fields

class UserSchema(Schema):
    name = fields.Str()
    email = fields.Email()
    created_at = fields.DateTime()
```

### 序列化对象（dumping）

```
from marshmallow import pprint

user = User(name="Monty", email="monty@python.org")
schema = UserSchema()
result = schema.dump(user)
pprint(result.data)
# {"name": "Monty",
#  "email": "monty@python.org",
#  "created_at": "2014-08-17T14:54:16.049594+00:00"}
```

```
json_result = schema.dumps(user)
pprint(json_result.data)
# '{"name": "Monty", "email": "monty@python.org", "created_at": "2014-08-17T14:54:16.049594+00:00"}'
```

### 应用筛选器到shema

```
summary_schema = UserSchema(only=('name', 'email'))
summary_schema.dump(user).data
# {"name": "Monty Python", "email": "monty@python.org"}
```

也可以使用exclude关键词

### 反序列化对象（loading）

默认返回的是python dict：

```
from pprint import pprint

user_data = {
    'created_at': '2014-08-11T05:26:03.869245',
    'email': u'ken@yahoo.com',
    'name': u'Ken'
}
schema = UserSchema()
result = schema.load(user_data)
pprint(result.data)
# {'name': 'Ken',
#  'email': 'ken@yahoo.com',
#  'created_at': datetime.datetime(2014, 8, 11, 5, 26, 3, 869245)},
```

返回object:

```
from marshmallow import Schema, fields, post_load

class UserSchema(Schema):
    name = fields.Str()
    email = fields.Email()
    created_at = fields.DateTime()

    @post_load
    def make_user(self, data):
        return User(**data)
```

```
user_data = {
    'name': 'Ronnie',
    'email': 'ronnie@stones.com'
}
schema = UserSchema()
result = schema.load(user_data)
result.data  # => <User(name='Ronnie')>
```

### 序列化a collection of对象

```
user1 = User(name="Mick", email="mick@stones.com")
user2 = User(name="Keith", email="keith@stones.com")
users = [user1, user2]
schema = UserSchema(many=True)
result = schema.dump(users)  # OR UserSchema().dump(users, many=True)
result.data
# [{'name': u'Mick',
#   'email': u'mick@stones.com',
#   'created_at': '2014-08-17T14:58:57.600623+00:00'}
#  {'name': u'Keith',
#   'email': u'keith@stones.com',
#   'created_at': '2014-08-17T14:58:57.600623+00:00'}]
```


### 验证数据完整性

所有的错误的信息都存储在Schema.errors里：

```
data, errors = UserSchema().load({'email': 'foo'})
errors  # => {'email': ['"foo" is not a valid email address.']}
# OR, equivalently
result = UserSchema().load({'email': 'foo'})
result.errors  # => {'email': ['"foo" is not a valid email address.']}
```

当验证collection的数据完整性的时候，错误信息将按照编号存储：

```
class BandMemberSchema(Schema):
    name = fields.String(required=True)
    email = fields.Email()

user_data = [
    {'email': 'mick@stones.com', 'name': 'Mick'},
    {'email': 'invalid', 'name': 'Invalid'},  # invalid email
    {'email': 'keith@stones.com', 'name': 'Keith'},
    {'email': 'charlie@stones.com'},  # missing "name"
]

result = BandMemberSchema(many=True).load(user_data)
result.errors
# {1: {'email': ['"invalid" is not a valid email address.']},
#  3: {'name': ['Missing data for required field.']}}
```

### 自定义validator

任意的callable都可以被定义为validator，比如lambda，function，`__call__`。

```
class ValidatedUserSchema(UserSchema):
    # NOTE: This is a contrived example.
    # You could use marshmallow.validate.Range instead of an anonymous function here
    age = fields.Number(validate=lambda n: 18 <= n <= 40)

in_data = {'name': 'Mick', 'email': 'mick@stones.com', 'age': 71}
result = ValidatedUserSchema().load(in_data)
result.errors  # => {'age': ['Validator <lambda>(71.0) is False']}
```

validator的callable可以返回boolean，或者ValidationError：

```
from marshmallow import Schema, fields, ValidationError

def validate_quantity(n):
    if n < 0:
        raise ValidationError('Quantity must be greater than 0.')
    if n > 30:
        raise ValidationError('Quantity must not be greater than 30.')

class ItemSchema(Schema):
    quantity = fields.Integer(validate=validate_quantity)

in_data = {'quantity': 31}
result, errors = ItemSchema().load(in_data)
errors  # => {'quantity': ['Quantity must not be greater than 30.']}
```

> 如果有多个validation针对于一个field，那么需要传入a collection of callable

> Schema.dump()也返回一个errors字典，这个字典里存储了所有的序列化过程中的
ValidationError。`required，allow_once，validate，@validates，@validates_schema`
将会只应用于反序列化。                     

### strict 模式

如果在Schema(strict=True)中使用strict，或者`class Meta`中使用，不合法的数据将会被放
入ValidationError.messages:

```
from marshmallow import ValidationError

try:
    UserSchema(strict=True).load({'email': 'foo'})
except ValidationError as err:
    print(err.messages)# => {'email': ['"foo" is not a valid email address.']}
```

#### 自定义的error handler？

继承Schema类，并重写handle_error方法。

#### Schema级别的validation？

继承Schema类。

#### required fields?

```
class UserSchema(Schema):
    name = fields.String(required=True)
    age = fields.Integer(required='Age is required.')
    city = fields.String(required={'message': 'City required', 'code': 400})
    email = fields.Email()

data, errors = UserSchema().load({'email': 'foo@bar.com'})
errors
# {'name': ['Missing data for required field.'],
#  'age': ['Age is required.'],
#  'city': {'message': 'City required', 'code': 400}}
```

#### Partial loading?我只想检查required的fields

```
class UserSchema(Schema):
    name = fields.String(required=True)
    age = fields.Integer(required=True)

data, errors = UserSchema().load({'age': 42}, partial=True)
# OR UserSchema(partial=True).load({'age': 42})
data, errors  # => ({'age': 42}, {})
```

#### 只想validate，不loading

```
errors = UserSchema().validate({'name': 'Ronnie', 'email': 'invalid-email'})
errors  # {'email': ['"invalid-email" is not a valid email address.']}
```

#### 想指定attribute的key的名字？

```
class UserSchema(Schema):
    name = fields.String()
    email_addr = fields.String(attribute="email")
    date_created = fields.DateTime(attribute="created_at")

user = User('Keith', email='keith@stones.com')
ser = UserSchema()
result, errors = ser.dump(user)
pprint(result)
# {'name': 'Keith',
#  'email_addr': 'keith@stones.com',
#  'date_created': '2014-08-17T14:58:57.600623+00:00'}
```

```
class UserSchema(Schema):
    name = fields.String()
    email = fields.Email(load_from='emailAddress')

data = {
    'name': 'Mike',
    'emailAddress': 'foo@bar.com'
}
s = UserSchema()
result, errors = s.load(data)
#{'name': u'Mike',
# 'email': 'foo@bar.com'}
```

```
class UserSchema(Schema):
    name = fields.String(dump_to='TheName')
    email = fields.Email(load_from='CamelCasedEmail', dump_to='CamelCasedEmail')

data = {
    'name': 'Mike',
    'CamelCasedEmail': 'foo@bar.com'
}
s = UserSchema()
result, errors = s.dump(data)
#{'TheName': u'Mike',
# 'CamelCasedEmail': 'foo@bar.com'}
```

#### 重构schema？隐式Field创建

不建议，不安全：

```
# Refactored schema
class UserSchema(Schema):
    uppername = fields.Function(lambda obj: obj.name.upper())
    class Meta:
        fields = ("name", "email", "created_at", "uppername")
```

```
class UserSchema(Schema):
    uppername = fields.Function(lambda obj: obj.name.upper())
    class Meta:
        # No need to include 'uppername'
        additional = ("name", "email", "created_at")
```

#### Output排序？

```
from collections import OrderedDict

class UserSchema(Schema):
    uppername = fields.Function(lambda obj: obj.name.upper())
    class Meta:
        fields = ("name", "email", "created_at", "uppername")
        ordered = True

u = User('Charlie', 'charlie@stones.com')
schema = UserSchema()
result = schema.dump(u)
assert isinstance(result.data, OrderedDict)
# marshmallow's pprint function maintains order
pprint(result.data, indent=2)
# {
#   "name": "Charlie",
#   "email": "charlie@stones.com",
#   "created_at": "2014-10-30T08:27:48.515735+00:00",
#   "uppername": "CHARLIE"
# }
```

#### Read-only and Write-only field?

```
class UserSchema(Schema):
    name = fields.Str()
    # password is "write-only"
    password = fields.Str(load_only=True)
    # created_at is "read-only"
    created_at = fields.DateTime(dump_only=True)
```

More: <http://marshmallow.readthedocs.org/en/latest/examples.html>                        

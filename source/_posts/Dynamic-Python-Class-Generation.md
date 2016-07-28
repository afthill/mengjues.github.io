title: Dynamic Python Class Generation
date: 2016-07-29 01:20:32
tags:
- Python
categories:
- Dynamic Programming
---

作者是Kyle Knapp，是Amazon的软件工程师。


### 什么是python的动态类生成？

- Generate classes at runtime
- data driven

### 简单示例

```
def hello_world(obj):
  print "Hello world from %s" % obj

class BaseClass(object):
  def __init__(self, instance_name):
    self.instance_name = instance_name

  def __repr__(self):
    return self.instance_name

my_class = type("MyClass", (BaseClass,), {"hello_world": hello_world})
my_class_inst = my_class("My Instance")
```

### Why dynamic class generation

- Improve workflow
- Improve visibility
- Reduce physical size
- Is production-level

### Downside

- traceback (类似于javascript中的匿名函数，难以调试)
- IDE support

### Production level demo: AWS SDK for Python

- dynamic and data driven
- efficient workflow

### When to conside dynamic class generation

- existing canonical data
  - web API
  - database

### An application demo

- client interface
- 1:1 mapping of methods of API
- model driven
- validate inputs
- parse response
- JSON RPC

#### Steps

- 1) make a simple JSON RPC client
- 2) integrate API models
- 3) add specific API methods
- 4) make the client extensible
- 5) add more APIs

##### JSON API overview

- reply on HTTP POST
- Signle URI
- JSON in body of request and response

```
import json
import requests

class Client(object):
  def __init__(self, end_point_url):
    self._end_point_url = end_point_url

  def make_api_call(self, method, params):
    headers = {'content-type': 'application/json'}
    payload = {
      'jsonrpc': '2.0',
      'method': 'method',
      'params': params,
      'id': 0
    }

    return requests.post(
      self._end_point_url, data=json.dumps(payload), headers=headers
      ).json()
```

##### expanded

- feels very general
- no input validation
- return the entire response

##### integrate model into client

```
class ModeledClient(Client):
  def __init__(self, model):
    self._model = model
    self._validator = RequestParamsValidator()
    self._parser = ResponseParamsParser()
    super(ModeledClient, self).__init__(model['endpoint_url'])

  def make_modeled_api_call(self, method, *args, **kwargs):
    # Validate the parameters provided for the particular method using the
    # provided model.
    validated_params = self._validate_method_params(
        method, *args, **kwargs)
    # Make the API call and get the response.
    api_response = self.make_api_call(method, validated_params)
    # Parse the response for the particular method using the provided
    # model.
    return self._parse_api_response(method, api_response)

  def _validate_method_params(self, method, *args, **kwargs):
    operation_input_model = self._get_operation_model(method)['input']
    return self._validator.validate(operation_input_model, *args, **kwargs)

  def _parse_api_response(self, method, api_response):
    operation_output_model = self._get_operation_model(method)['output']
    return self._parser.parse(
    operation_output_model, api_response)

  def _get_operation_model(self, method):
    operation_models = self._model['operations']
    if method not in operation_models:
      raise RuntimeError(
        'Unknown operation %s' % method)
    return operation_models[method]
```

##### More expanded

- API remains undocumented

##### Add specific method

create a class factory:

```
def create_client(model):
  cls_name = 'MyClient'
  cls_bases = (ModeledClient,)
  cls_props = {}

  for operation_name, operation_model in model['operations'].items():
    method = _get_client_method(operation_name)
    cls_props[operation_name] = method

  cls = type(cls_name, cls_bases, cls_props)
  return cls(model)

def _get_client_method(operation_name):
  def _api_call(self, *args, **kwargs):
    return self.make_modeled_api_call(
      operation_name, *args, **kwargs)
  return _api_call
```

##### Expand class factory for specific docstring

```
def create_client(model):
  cls_name = 'MyClient'
  cls_bases = (ModeledClient,)
  cls_props = {}

  for operation_name, operation_model in model['operations'].items():
    method = _get_client_method(operation_name)
    method.__name__ = str(operation_name)
    method.__doc__ = _get_docstring(operation_model)
    cls_props[operation_name] = method

  cls = type(cls_name, cls_bases, cls_props)
  return cls(model)
```

##### Expand to support custom class name and custom inheritance

```
def create_client(model, cls_name='MyClient', cls_bases=None):
  if not cls_bases:
    cls_bases = (ModeledClient,)
  cls_props = {}

  for operation_name, operation_model in model['operations'].items():
    method = _get_client_method(operation_name)
    method.__name__ = str(operation_name)
    method.__doc__ = _get_docstring(operation_model)
    cls_props[operation_name] = method

cls = type(cls_name, cls_bases, cls_props)
return cls(model)
```

```
class CachedClient(ModeledClient):
  def __init__(self, model):
    super(CachedClient, self).__init__(model)
    self._operation_cache = {}

  @property
  def history(self):
    return self._operation_cache

  def make_modeled_api_call(self, method, *args, **kwargs):
    cache_key = '%s(args=%s,kwargs=%s)' % (method, args, kwargs)
    if cache_key in self._operation_cache:
      print('Retrieving result from history')
      return self._operation_cache[cache_key]
    else:
      print('Retrieving result from server')
      result = super(CachedClient, self).make_modeled_api_call(
          method, *args, **kwargs)
      self._operation_cache[cache_key] = result
      return result
```

Presentation and Source Code at: <https://github.com/kyleknap/dynamic-web-api-clients>

题外话：类似的codegen技术，还有最近正火的swagger：<https://github.com/swagger-api>

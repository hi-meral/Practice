Modules
#
# Sooooo how does this work exactly?
#
This is an extention of our current service fabric framework. More in depth explanations of parts of that will explored as they come up.
#

#
# Let's begin with our classes
#
To begin here will require a very quick overview of why we use mashumaro, this package is used as the base for our dataclasses becase of the speed between serializing and deserializing of dataclasses. we keep our base config in our service fabric framework that we can then extend capability to our other repos. The base class that we use most often is `DataClassJSONMixin` since we do the majority of our work using jsons. From here we can create larger dataclass that can encapsulate the use cases we have. From there we create a message from a base class called `MessageContent` that lives in the service fabric. This is our highest level of dataclass since it will contain all other dataclases in an MQMessage for out broker, as well as, `status` and `params` objects.
#
# Example 
#
a base dataclass that extends from our config
    `@dataclass`
    `class Email(DataClassJSONMixin):`
    `    email: str `

#
Now lets see a dataclass that will contain a field for the `Email` class
    `@dataclass`
    `class AccountInfo(DataClassJSONMixin):`
    `    email: Email`

#
Lastly, lets see our highest level message
    `@dataclass`
    `class AccountMessage(MessageContent):`
    `   account_info: AccountInfo = field(metadata=field_options(alias="accountInfo"))`

Everything dealing with dataclasses will be within the `/domain/` directory of the project, to keep them all in one place for easy viewing and mainantence

#
What is the `field` information in our *AccountMessage*?
#
the `field` is used to identify dataclass fields, for instance: `default = None` would be to set a default to the field, `default_factory` would be for something like `list` type on the field, in this case we are going to set some metadata to the field.
Next is `field_options` is a part of mashumaro as a helper class that assist with serialization of dataclasses, the example provided will be used to show how that field is viewed when it is a json. This one is used when a field in a dataclass are multi_worded or anything separated by `_`

#
# Direectory Structure
#
Our best practive for structure is to consolidate servers into directories that match their use case. I'll use `src/svc_mesh` for this example:
svc_mesh/
|-------domain/
|------------account.py
|-------usls/
|------------get_data.py
|-------zuora/
|------------get_account_info.py

**should any of these directories need an aditional subdirectory, that can be added without issue. For instance, if usls required some kind [batch] processing**

# if we include tests, it would match the `svc_mesh` but with test in the names
tests/
|----test_usls/
|--------------test_get_data.py
|----test_zuora/
|--------------test_get_account_info.py
#
**test for nested directories don't need to follow the convention of the `svc_mesh` directoryu, in the case of [batch] the test can be names `test_usls_batch_` and whatever the inner python file is named**

*naming standards would include all names being `lower_case` and generic nameing so that they can be resued if they are deemed to be able to be utilized in that manner*


## Building a virtual environment (Mac and Windows):

# Mac
**Creating a .venv**
in your terminal
``` python.311 -m venv .venv ``` 

# Windows
**Creating a venv**
```  ```

## How to run:

**Activate the env:**
# Windows
.venv\Scripts\activate.bat

**Start the Server:**

``` python ./src/svc_mesh/EchoClient/echo_server.py --brokers "amqp://svcfabric:Fabric.admin@52.12.199.48:5672/" --queue EXAMPLE.ECHO.SERVER --log-file echo_server.log --log-level debug ```

 **Run client:**

``` python ./src/svc_mesh/EchoClient/echo_client.py --brokers "amqp://svcfabric:Fabric.admin@52.12.199.48:5672/" ```

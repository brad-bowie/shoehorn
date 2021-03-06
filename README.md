So, you're a web-mvc layer. You've got your express app, you're accepting request bodies, but you're writing out painful validation logic all over the place. Instead, levereage **shoehorn** as a generic way to do required/type validation as well as casting

chickity-check it out: https://www.npmjs.com/package/shoehorn/

#### Install
```
npm install shoehorn
```

#### Require
```
var shoehorn = require('shoehorn');
```

#### Usage

#### shoehorn.register(name, schema)
Declares a schema to use during the binding process

```
var loginFormSchema = {
    'userName': {
        type: String,
        required: true,
        requiredErrorMessage: 'Please input your userName'
    },
    'password': {
        type: String,
        required: true,
        requiredErrorMessage: 'Please input your password'
    },
    'pinNumber': {
        type: Number,
        typeErrorMessage: '%s is not a valid PIN' // %s will be replaced with the value
    }
};

shoehorn.register('LoginForm', loginFormSchema);
```

Schema supports 4 parameters:
* `type` - (Required) type of the form's variable, currently only `Date`, `String`, and `Number`
* `typeErrorMessage` - (Optional) A custom message should type conversion fail. If not included, an automated version will be generated
* `required` - (Optional) A boolean; if left `true`, then the `requiredErrorMessage` will be added to the output in the event there is no value to convert. Left false, undefined may be set in the event there is no value to convert.
* `requiredErrorMessage` - (Optional) A custom message should the value to convert be undefined. If not included, an automated version will be generated

#### shoehorn.bind(name, obj)
Binds an object to a desired schema type, enforcing the declared requirements

```
var app = express();

// config express application

// register your login schema (see above example)

app.post('/login', function(req, res) {
    var bindingResult = shoehorn.bind('LoginForm', req.body);

    if(bindingResult.errors.length > 0) {
        console.error('Form was submitted with errors!');
        res.send(bindingResult.errors);
    } else {
        // log out the form conents
        console.log(bindingResult.form);
    }
});
```

### class: BindingResult
Returned from the `shoehorn#bind(name, obj)` execution, wraps the form and any errors encountered

#### BindingResult.errors
Array of any errors encountered during the binding process

#### BindingResult.form
The form generated from the binding process

### Misc notes

##### Dates
* Going to a `Date` from a `Number`, assumes this is an epoch timestamp
* Going from a `Date` from a `String`, assumes this is a formatted `String`
* Going to a `Number` from a `Date`, creates an epoch timestamp (`Date.getTime()`)
* Going to a `String` from a `Date`, creates a formatted `String` (`Date.toString()`)

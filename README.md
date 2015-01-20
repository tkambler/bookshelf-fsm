# bookshelf-fsm

A Finite State Machine Plugin for Bookshelf.js ORM. See [fsm-as-promised](https://github.com/vstirbu/fsm-as-promised) for details regarding the underlying state machine implementation.

## Getting Started

```
var BookshelfFSM = require('bookshelf-fsm');

/**
 * Begin by passing your `bookshelf` instance to `BookshelfFSM`.
 * This method will return a new model from which you can then 
 * extend. Any model that subsequently inherits from this model
 * will be able to take advantage of finite state machine
 * functionality.
 */
var BaseMachine = BookshelfFSM(bookshelf);

var Car = BaseMachine.extend({

	'tableName': 'cars',
	
	'defaults': {
		'state': 'off'
	},
	
	/**
	 * If you don't need to perform any special setup for your
	 * model(s), you can omit this method entirely. If you do
	 * override `initialize`, however, be sure you maintain the
	 * prototype chain as shown here.
	 */
    'initialize': function() {
        Base.prototype.initialize.apply(this, arguments);
    },
    
	'_getFSMCallbacks': function() {
	
		return {
			'onstart': function() {
				return new Promise(function(resolve, reject) {
					// ...
					return resolve();
				});
			},
			'onstop': function() {
				return new Promise(function(resolve, reject) {
					// ...
					return resolve();
				});
			}
		};

	},
	
	'_getFSMEvents': function() {
	
		return [
			{
				'name': 'start',
				'from': 'off',
				'to': 'on'
			},
			{
				'name': 'stop',
				'from': 'on',
				'to': 'off'
			}
		];

	}

});

var car = new Car();

car.on('transitioned', function(to, from, event) {
    console.log('car transitioned', {
        'to': to,
        'from': from,
        'event': event
    });
});

car.start().then(function() {
	// The state transition succeeded. The model is automatically updated with the
	// appropriate value for `state` and saved before this link in the promise chain
	// is called.
}).catch(function(err) {
	// The state transition failed. The model's `state` attribute was not updated.
});
```

## Acknowledgements

Bookshelf-fsm is basically just intelligent superglue that binds Bookshelf to a state machine made possible by the following libraries:

- [javascript-state-machine](https://github.com/jakesgordon/javascript-state-machine)
- [fsm-as-promised](https://github.com/vstirbu/fsm-as-promised)
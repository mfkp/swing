# Swing

[![Build Status](https://travis-ci.org/gajus/swing.png?branch=master&decache2)](https://travis-ci.org/gajus/swing)
[![NPM version](https://badge.fury.io/js/swing.svg)](http://badge.fury.io/js/swing)
[![Bower version](https://badge.fury.io/bo/swing.svg)](http://badge.fury.io/bo/swing)

A swipeable cards interface. The swipe-left/swipe-right for yes/no input. As seen in apps like [Jelly](http://jelly.co/) and [Tinder](http://www.gotinder.com/), and [many others](http://www.saydaily.com/2014/09/tinder-swipe-and-media).

Give it a [swing](http://gajus.com/sandbox/swing/examples/card-stack/)! and please [tweet it](https://twitter.com/intent/retweet?tweet_id=527505589732405248) if you like it. : )

![Card stack example.](./.readme/card-stack.gif)

[toc 3]

## Usage Examples

* [Card stack](http://gajus.com/sandbox/swing/examples/card-stack/).
* [Card stack](http://gajus.com/sandbox/angular-swing/examples/card-stack/) using [angular-swing](https://github.com/gajus/angular-swing/) module.
* [Programmatically control](http://gajus.com/sandbox/swing/examples/card-state/) the state of the card.
* [Indicate the state of the drop](http://gajus.com/sandbox/swing/examples/throw-confidence/) using `throwConfidence` and `direction` [event object](#event-object) properties.

The code for all of the examples is in the [./examples/](https://github.com/gajus/swing/tree/master/examples/) folder.

[Raise an issue](https://github.com/gajus/swing/issues) if you are missing an example.

## Use Case

A collection of observations about the extended use case of the swipeable cards interface, that I found useful when considering the implementation.

### Single-Handed Navigation

> Mobile devices are frequently used on-the-go, which drastically increases the probability that you'll attempt to navigate apps using just one hand, with the key digit being the mighty thumb.
> Instead of browsing endless lists for the hidden perfect piece of data — be it the right music for the moment, what to do tonight, or your next potential hookup — card-swiping turns decision making into a highly engaging Choose-Your-Own-Adventure game.

– https://medium.com/@janel_az/small-data-why-tinder-like-apps-are-the-way-of-the-future-1a4d5703b4b

### Digestible Unit of Information

> [..] the "card" on a mobile device becomes more and more important as a digestible unit of information on a small screen for users who are on the go and mostly glancing through their apps before settling into the ones that truly engage them.

– http://techcrunch.com/2013/09/22/mobile-apps-card-interfaces-and-our-opposable-thumbs/

### Data

> More than a scroll and perhaps even more than discrete taps themselves, cards create repetitive, deliberate, discrete decision moments over and over. And as the user swipes, you can learn.
> The time they swipe, the speed they swipe, what they swiped, the geolocation where they swiped, and even how similar the results of that swipe are vs. a swipe earlier that session are all possibilities that are yielding smarter apps for you and me every day.

– http://www.itsmakeable.com/unconventional-wisdom/good-user-experience-design-ux-can-do-what-now/

## Quick Start

```html
<ul>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

```js
var stack,
    cards;

// Prepare the cards in the stack for iteration.
cards = [].slice.call(document.querySelectorAll('ul li'))

// An instance of the Stack is used to attach event listeners.
stack = Swing.Stack();

cards.forEach(function (targetElement) {
    // Add card element to the Stack.
    stack.createCard(targetElement);
});

// Add event listener for when a card is thrown out of the stack.
stack.on('throwout', function (e) {
    // e.target Reference to the element that has been thrown out of the stack.
    // e.throwDirection Direction in which the element has been thrown (Card.DIRECTION_LEFT, Card.DIRECTION_RIGHT).

    console.log('Card has been thrown out of the stack.');
    console.log('Throw direction: ' + (e.throwDirection == Card.DIRECTION_LEFT ? 'left' : 'right'));
});

// Add event listener for when a card is thrown in the stack, including the spring back into place effect.
stack.on('throwin', function (e) {
    console.log('Card has snapped back to the stack.');
});
```

## Configuration

```js
var stack,
    config;

config = {
    /**
     * Invoked in the event of dragmove.
     * Returns a value between 0 and 1 indicating the completeness of the throw out condition.
     * Ration of the absolute distance from the original card position and element width.
     * 
     * @param {Number} offset Distance from the dragStart.
     * @param {HTMLElement} element Element.
     * @return {Number}
     */
    throwOutConfidence: function (offset, element) {
        return Math.min(Math.abs(offset) / element.offsetWidth, 1);
    }
};

stack = stack = Swing.Stack(config);
```

| Name | Description | Default |
| --- | --- | --- |
| `isThrowOut` | Invoked in the event of `dragend`. Determines if element is being thrown out of the stack. | Element is considered to be thrown out when `throwOutConfidence` is equal to 1. |
| `throwOutConfidence` | Invoked in the event of `dragmove`. Returns a value between 0 and 1 indicating the completeness of the throw out condition. | Ration of the absolute distance from the original card position and element width. |
| `throwOutDistance` | Invoked when card is added to the stack. The card is thrown to this offset from the stack. | The value is a random number between `minThrowOutDistance` and `maxThrowOutDistance`. |
| `minThrowOutDistance` | In effect when `throwOutDistance` is not overwritten. | 450. |
| `maxThrowOutDistance` | In effect when `throwOutDistance` is not overwritten. | 500. |
| `rotation` | Invoked in the event of `dragmove`. Determine the rotation of the element. | Rotation is equal to the proportion of horizontal and vertical offset times the `maximumRotation` constant. |
| `maxRotation` | In effect when `rotation` is not overwritten. | 20. |
| `transform` | Invoked in the event of `dragmove` and every time the physics solver is triggered. | Uses CSS transform to translate element position and rotation. |

All of the configuration parameters are optional. Refer to the source code of the [card](https://github.com/gajus/swing/blob/master/src/card.js) module to learn the parameters associated with every callback.

## Methods

```js
var stack,
    card;

stack = stack = Swing.Stack();
card = stack.createCard(HTMLElement);
```

| Name | Description |
| --- | --- |
| `stack.createCard(element)` | Creates an instance of Card and associates it with the element. |
| `stack.getCard(element)` | Returns card associated with an element. |
| `stack.on(event, listener)` | Attaches an [event listener](#events). |
| `card.on(event, listener)` | Attaches an [event listener](#events). |
| `card.throwIn(x, y)` | Throws a card into the stack from an arbitrary position. `x, y` is the position at the start of the throw. |
| `card.throwOut(x, y)` | Throws a card out of the stack in the direction away from the original offset. `x, y` is the position at the start of the throw. |
| `card.destroy()` | Unbinds all Hammer.Manager events. Removes the listeners from the physics simulation. |

### Throwing Card Out of the Stack

Use the `card.throwOut(x, y)` method to throw the card out of the stack. Offset the position to whatever direction you want to throw the card, e.g.

```js
card.throwOut(Card.DIRECTION_LEFT, 0);
card.throwOut(Card.DIRECTION_RIGHT, 0);
```

To make the animation more diverse, use random value for the `y` parameter.

## Events

Event listener can be attached to an instance of `Swing.Stack` or `Swing.Card` using the `on` method:

```js
var stack,
    card;

stack = stack = Swing.Stack();

card = stack.createCard(HTMLElement);

card.on('throwout', function () {});
stack.on('throwout', function () {});
```

| Name | Description |
| --- | --- |
| `throwout` | When card has been thrown out of the stack. |
| `throwoutend` | When card has been thrown out of the stack and the animation has ended. |
| `throwoutleft` | Shorthand for `throwout` event in the `Card.DIRECTION_LEFT` direction. |
| `throwoutright` | Shorthand for `throwout` event in the `Card.DIRECTION_RIGHT` direction. |
| `throwin` | When card has been thrown into the stack. |
| `throwinend` | When card has been thrown into the stack and the animation has ended. |
| `dragstart` | Hammer [panstart](http://hammerjs.github.io/recognizer-pan/). |
| `dragmove` | Hammer [panmove](http://hammerjs.github.io/recognizer-pan/). |
| `dragend` | Hammer [panend](http://hammerjs.github.io/recognizer-pan/). |

### Event Object

Event listener is invoked with a single `eventObject` parameter:

```js
var stack;

stack = stack = Swing.Stack();

stack.on('throwout', function (eventObject) {});
```

| Name | Value |
| --- | --- |
| `target` | The element being dragged. |
| `direction` | The direction in which the element is being dragged: `Card.DIRECTION_LEFT` or `Card.DIRECTION_RIGHT`. |
| `throwOutConfidence` | A value between 0 and 1 indicating the completeness of the throw out condition. |

## Download

Using [Bower](http://bower.io/):

```sh
bower install swing
```

Using [NPM](https://www.npmjs.org/):

```sh
npm install swing
```

The old-fashioned way, download either of the following files:

* https://raw.githubusercontent.com/gajus/swing/master/dist/swing.js
* https://raw.githubusercontent.com/gajus/swing/master/dist/swing.min.js

### Browser Bundle

When using the `./dist/` version the Swing resides under `gajus` namespace:

```js
var stack = gajus.Swing.Stack();
```

## Dependencies

If you are using the `./dist/` version, you do not need to download the dependencies.

The list of the dependencies and description of their role is for your reference only.

* [Rebound](http://facebook.github.io/rebound-js/docs/rebound.html) to drive physical animations. Notice how if you drag too little and let go, the cards spring back into place.
* [Hammer](http://hammerjs.github.io/) to handle drag interactions.
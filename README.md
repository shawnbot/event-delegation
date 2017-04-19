# Event delegation

Event delegation is a [DOM] JavaScript pattern that replaces event listeners on individual elements with listeners further up the tree that can conditionally respond to [bubbling events].

## Why?
* Adding lots of direct event listeners can have serious performance implications (citation needed).
* Knowing when to add listeners can be tricky if your DOM is built or updated asynchronously (via AJAX or with React, Angular, or web components).
* Adding, removing, and otherwise managing direct listeners can be tricky when the DOM is constantly changing.

Some or all of these concerns may apply if you're building [web components] with technologies like [HTML custom elements]. In that case, it's safe to assume that you have no control over what's going on inside of your component — and really, you shouldn't _need to_ — unless you're using [mutation observers]... in which case, well, ugh.

### jQuery example
Consider this example of direct listeners to implement an accordion component in jQuery:

```js
$(function() {
  $('.accordion').each(function() {
    var accordion = this;
    $('button', this).on('click', function() {
      // this function is left as an exercise to the reader
      toggleAccordionButton(accordion, button);
    });
  });
});
```

There is one glaring issue here: **it assumes that the DOM as it exists when it's "ready" will never change.** If your page or application adds or removes either a `.accordion` or `.accordion button` at runtime, the necessary listeners won't be added or removed with them. 

Lucky for us, jQuery implements [its own form of event delegation](https://learn.jquery.com/events/event-delegation/), which makes it possible to rewrite the above as:

```js
$('body').on('click', '.accordion button' function(event) {
  var accordion = $(event.target).closest('.accordion').get();
  toggleAccordionButton(accordion, event.target);
});
```

This is much better for a couple of reasons:

1. It doesn't care when the DOM is initialized (as long as there's a `<body>`).
1. The `.accordion button` selector ensures that only buttons within an `.accordion` will trigger the behavior, and that our `.closest('.accordion')` query will always give us the right ancestor.
1. Also thanks to the use of `.closest()`, we can now nest accordion elements — as long as we're sure to prevent the event from bubbling up to another ancestor with `event.stopPropagation()`.

## Implementations
Here are a couple of different implementations of the event delegation pattern to check out:

* [dom-delegate] is the Financial Times' excellent implementation, with a simple API:

    ```js
    var delegate = new Delegate(document.body);
    delegate.on('click', '.accordion button', toggleAccordion);
    ```
    
* [receptor] offers a declarative API and introduces the concept of reusable [behaviors](https://github.com/shawnbot/receptor#behavior), which encapsulate collections of listeners that can be atomically added and removed:

    ```js
    var behavior = receptor.behavior({
      'click': {
        '.accordion button': toggleAccordion,
        '.menu button': toggleMenuButton,
        'a[href^="#"]': scrollToLink,
      },
    });
    behavior.add(document.body);
    ```
    
    **ProTip**: If you're having trouble with child elements (e.g., images in `<button>` elements) gobbling up your delegates, receptor might be the ticket: it uses [Element.closest()][closest] to match the delegate selector to the event target.

* [Gator.js](https://craig.is/riding/gators) does the job, too.

* You can find lots of [other delegation modules on npm](https://www.npmjs.com/search?q=delegate).

## Further reading
* [David Walsh on event delegation](https://davidwalsh.name/event-delegate)
* [jQuery: Understanding event delegation](https://learn.jquery.com/events/event-delegation/)
* [SitePoint: JavaScript event delegation is easier than you think](https://www.sitepoint.com/javascript-event-delegation-is-easier-than-you-think/)
* [An event delegation tutorial on javascript.info](https://javascript.info/event-delegation)
* [Video: JavaScript event delegation in 4 minutes](https://youtu.be/yQRiBGeygeA)

[DOM]: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction
[bubbling events]: https://en.wikipedia.org/wiki/Event_bubbling
[mutation observers]: https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver
[web components]: https://www.webcomponents.org/
[HTML custom elements]: https://github.com/shawnbot/custom-elements#readme
[receptor]: https://github.com/shawnbot/receptor
[dom-delegate]: https://github.com/ftlabs/ftdomdelegate
[closest]: https://developer.mozilla.org/en-US/docs/Web/API/Element/closest

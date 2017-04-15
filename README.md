# Event delegation

Event delegation is a [DOM] JavaScript pattern that replaces event listeners on individual elements with listeners further up the tree that can conditionally respond to bubbling events. Consider this example of direct listeners to implement an accordion component in jQuery:

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

There is one serious issue with this pattern: **it assumes that the DOM as it exists when it's "ready" will never change.** This means that if your page or application adds or removes either a `.accordion` or `.accordion button` at runtime, the corresponding event listeners won't be added or removed with them. 

Lucky for us, jQuery implements [its own form of event delegation](https://learn.jquery.com/events/event-delegation/), which makes it possible to rewrite the above as:

```js
$('body').on('click', '.accordion button' function(event) {
  var accordion = $(event.target).closest('.accordion').get();
  toggleAccordionButton(accordion, event.target);
});
```

This is much better!

[DOM]: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction

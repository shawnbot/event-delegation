# Event delegation

Event delegation is a [DOM] JavaScript pattern that replaces event listeners on individual elements with listeners further up the tree that can conditionally respond to bubbling events. Consider this example of direct listeners to implement an accordion component in jQuery:

```js
$(function() {
  $('.accordion'.each(function() {
    var accordion = this;
    $('button', this).on('click', function() {
      // implementation of this function is an exercise left to the reader
      toggleAccordion(accordion);
    });
  });
});
```

There is one glaring issue with this pattern: **it assumes that the DOM as it exists when it's "ready" will never change.** This means that if your page or application adds or removes either a `.accordion` or `.accordion button` at runtime, the event listeners won't be added.

Thankfully, jQuery implements [its own form of event delegation](https://learn.jquery.com/events/event-delegation/), which makes it possible to rewrite the above as:

```js
$('body').on('click', '.accordion button' function(event) {
  var accordion = $(event.target).closest('.accordion');
  toggleAccordion(accordion);
});
```

More soon!

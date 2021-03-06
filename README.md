active_query.dart
=================

`activeQuery()` allows you to easily watch the DOM for elements that match and
un-match CSS selectors. `activeQuery()` is much easier to use than Mutation
Observers for the specific case of watching which objects match a CSS selector.

## Example

```dart
import 'dart:html';
import 'package:active_query/active_query.dart';

main() {
  activeQuery('[important]')
    ..added.listen((element) {
      print("${element.classes.first} is now important");
    })
    ..removed.listen((element) {
      print("${element.classes.first} is no longer important");
    })
    ..elements.listen((elements) {
      print("Important elements: ${elements.map((e) => e.classes.first)}");
    });

  // We need to perform each mutation in a Future so that the browser doesn't
  // coalesce them and not trigger our observer.
  new Future(() {
    print("first mutation");
    querySelector('div.two').attributes['important'] = '';
  }).then((_) => new Future(() {
    print("second mutation");
    querySelector('div.one').attributes.remove('important');
  })).then((_) => new Future(() {
    print("third mutation");
    querySelector('div.two').attributes.remove('important');
  }));
}
```

```html
<html>
  <body>
    <div class="one" important></div>
    <div class="two"></div>
  </body>
</html>
```

Prints the following:

```
one is now important
Important elements: (one)
first mutation
two is now important
Important elements: (one, two)
second mutation
one is no longer important
Important elements: (two)
third mutation
two is no longer important
Important elements: ()

```
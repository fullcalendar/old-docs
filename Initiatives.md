
## Converting to TypeScript

I (@arshaw) am currently in-progress with converting the main codebase to TypeScript (everything in the `src/` directory), which is a superset of ES6. I'm converting all of the `Class.extend` declarations to true TypeScript classes, converting each individual file over to a module, connect dependant modules together, and various other cleanup. I'm making heavy use of the "implicit any", and not locking down types just yet. The biggest gain is that we get an ES6 module system and can pull in dependencies from NPM.

*update (Nov 5):* mostly complete
https://github.com/fullcalendar/fullcalendar/tree/typescript

TypeScript's compilation checks will greatly help us with the removal of jQuery.

The conversion should be done in the next few days, hopefully by Nov 6th or 7th. If you'd like to proceed with the Removal-of-jQuery project, it's probably best to start with a place that won't be affect by the TypeScript conversion, such as converting legacy tests (mentioned below). However, modifying JS files in the `src/` directory should also be fine. I am using some git magic to retain the relationship between `.js -> .ts` files, so that merges still work.


## Removing jQuery

The first milestone is to create:

- [object-oriented initialization API](https://github.com/fullcalendar/fullcalendar/issues/3703)
- everywhere the API exposes jQuery objects should now expose raw DOM nodes
- everywhere the API exposes jQuery Event objects should now expose native JavaScript Events.

This is considered a breaking API change and will warrant a bump to the next major version. Depending on how fast this project gets done, I'd like to bundle other breaking API changes into the next major release as well. In fact, I'd like to revamp a lot of settings and callbacks, in addition to revamping how timezones and ambiguous moments work.

Here are the major subprojects for the conversion:

- replacing use of jQuery's `$.ajax`
- removing lots of jQuery utility methods like `$.each` and `$.grep`
- and of course, replacing all jQuery DOM operations and event-handling

I envision the very *last* step being the removal of the `$().fullCalendar` initializer and the `$.fullCalendar` root namespace.


### Technical Approach Tidbits

Removal of jQuery's AJAX system begs the question: do we want to start introducing additional dependencies to do the heavy lifting of the vacant functionality?

I want to err on the side of keep dependencies *really light*, even rolling our own basic utilites if we need to. But providing hooks in the API if people want to do more advanced stuff. For example, instead of introducing [axios](https://github.com/axios/axios) to handle AJAX, we'll make use of a really light XHR wrapper instead (like [xhr](https://github.com/naugtur/xhr)) but give people the option to use axios by leveraging the [events callback](https://fullcalendar.io/docs/event_data/events_function/), which might need tweaking to improve ease of use for this scenario.

Many third party libs these days rely on polyfills, especially a Promise polyfill. I'd like to avoid using these until FullCalendar drops support for IE <= 11. We can just use callbacks instead of Promises for now.


### Tests Tests Tests!

Completing the first milestone will entail all tests passing! This will require modifications to a lot of the existing tests!

Tests will need to compensate for the API changes described above, but tests can **continue to use jQuery, [jasmine-jquery](https://github.com/velesin/jasmine-jquery), and [jquery simulate](https://github.com/jquery/jquery-simulate)!** These are considered *test utilities*. jQuery will be moved from a dependency to a `dev dependency`.

One exception is our use of [jquery-mockjax](https://github.com/jakerella/jquery-mockjax). This is tied into jQuery's `$.ajax` system, which will be removed, so we'll need to mock XMLHttpRequest instead (maybe with [this](https://github.com/jameslnewell/xhr-mock)).


#### How to rewrite legacy initialization calls in tests

First, please glance at the general article for [Writing Automated Tests](https://github.com/fullcalendar/fullcalendar/wiki/Automated-Tests).

Because the `$().fullCalendar()` initialization method will no longer be available, tests will need to compensate. This will be the first struggle in adapting the existing tests. Fortunately, there's already a system in place. Sadly, it will be a rather tedious process.

Look at this file:
[tests/legacy/background-events.js](https://github.com/fullcalendar/fullcalendar/blob/v3.6.2/tests/legacy/background-events.js)

See how there's an `affix()` to attach the DOM node, and there's an `options` object, and the `describe` and `it` statements accumulate values onto the `options` object? And then it gets passed into the `$().fullCalendar` constructor?

Well, here's an example of the more modern way to do it:
[tests/view-dates/dateAlignment.js](https://github.com/fullcalendar/fullcalendar/blob/v3.6.2/tests/view-dates/dateAlignment.js)

- `pushOptions(options)` - will cause all `initCalendar` calls within the current `describe` block to receive these options. CANNOT be used within an `it()` statement. For that, pass options into `initCalendar` instead...
- `initCalendar(options?)` - will initialize the calendar with the accumulated options. You can also optionally pass in additional options.

There are also some more advanced techniques. These methods let you iterate through parallel sets of options:

- `describeOptions(optionName, descriptionAndValueHash, callback)` - [example](https://github.com/fullcalendar/fullcalendar/blob/v3.6.2/tests/view-dates/visibleRange.js#L13)
- `describeOptions(descriptionAndOptionsHash, callback)` - [example](https://github.com/fullcalendar/fullcalendar/blob/v3.6.2/tests/view-dates/dayCount.js#L8)
- `describeTimezones(callback)` - like `describeOptions`, but preconfigured to do the 4 different types of timezones
- `describeValues(hash, callback)` - like `describeOptions`, but simply iterates over the given hash and makes implicit describe statements. does not accumulate options.

For methods calls, something like this...

```js
$('#cal').fullCalendar('updateEvent', eventObj);
```

Will become this...

```js
currentCalendar.updateEvent(eventObj);
```

As you can see, the `currentCalendar` object is available for you in any `it()` statement.

To access the current calendar's DOM nodes, take something that was previously like this:

```js
var slats = $('#cal .fc-slat');
```

And convert it to this:

```js
var slats = $(currentCalendar.el).find('.fc-slats');
// please wrap currentCalendar.el in $() as it won't always be a jQuery object
```

Here is an example of a conversion:
https://github.com/fullcalendar/fullcalendar/commit/1c673b658a849ddd082c634697d41d79abd3d48f

If you'd like to help, please email arshaw at fullcalendar.io and join the Google Group:
https://groups.google.com/forum/#!forum/fullcalendar

To keep track on who's working on what (in terms of converting `$().fullCalendar()` calls in legacy tests), use this spreadsheet (you'll get write access when you join the Google Group):

[**Spreadsheet**: Converting Constructors in Legacy Tests](https://docs.google.com/spreadsheets/d/1QEeFl2vdaNqBqjCzIdTENXe9lzRpbLujHHEWO9ZvxBs/edit#gid=0)

Please keep all these renovated test files in the `tests/legacy/` directory for now.

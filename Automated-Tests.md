The test suite leverages the [Karma Test Runner][Karma]. This is the thing that runs the tests in various browsers and environments. The actual tests themselves are written with Jasmine:

> [Jasmine Introduction]


# Development Tools

First, your JS/CSS files should be built (via `gulp dist` or `gulp watch`).
Then you can run the tests from a browser (this will output a URL that you can visit):

	gulp test

Alternatively, you can run the tests headlessly:

	# runs once...
	gulp test:single

	# OR, runs continuously, watching changes..
	gulp test:headless

Both commands will watch for any changes made to source files or test files, and then automatically rereun the tests.


# Workflow

1. Create one `.js` file per "thing" you are testing.

2. Write the tests. Look at other tests in the directory for inspiration on how a test file is laid out.

3. Make sure your tests pass. Run the `gulp test` commands mentioned above.


# What to test for

We are mostly doing "black box testing", which means we don't care about testing the internals, mainly just the input and output. For most options, this will entail inspecting the DOM to see if the option had the desired effect. This might involve checking an element for certain text, a certain className, or a certain pixel width. An example of this can be seen with [minTime].

To test callbacks (like [eventClick]), please simulate a real user mouse event using the jQuery `trigger` method. Then, to determine if the callback has been called, use [Jasmine's asynchronous support].

To test API methods (like [updateEvent]), call the method with a healthy variety of arguments and make sure the desired output occurs, whether by querying DOM or querying the internal state of the calendar with a different method. Also, make sure all the proper callbacks have been called (if applicable).


# Tips

When you are writing a new test, it is often annoying to have to run *all* tests every time while you are debugging. Jasmine allows you to write `fdescribe` and `fit` statements, which will only run the enclosed tests and ignore all others.

Also, when running tests in a real browsers via the plain `gulp test` command, it is very helpful to use Karma's "debug" mode (available via the big DEBUG button). Once you are in that mode, JS debugging tools such as console and breakpoints will become available.

The [jasmine-jquery] library is available to make it easier to query the DOM for certain things. It adds extra DOM-specific utility methods to the `expect()` object.

The [jasmine-fixture] library is available to make it easier to create and attach DOM elements through the `affix` method. They also automatically get cleaned up after each `it` statement runs.


# Code formatting of tests

Please make sure your test code follows the [Style Guide] and that you run `gulp check` before committing.


# Questions

If you have any questions on what to tests, or how to organize your tests, post a question on the [Google Group].

[Jasmine Introduction]: http://jasmine.github.io/2.0/introduction.html
[Karma]: http://karma-runner.github.io/
[FullCalendar Documentation]: http://arshaw.com/fullcalendar/docs2/
[Style Guide]: Contributing-Code#style-guide
[Google Group]: https://groups.google.com/forum/#!forum/fullcalendar
[How to create a PR]: https://help.github.com/articles/creating-a-pull-request
[minTime]: https://github.com/arshaw/fullcalendar/blob/master/tests/automated/minTime.js
[jasmine-jquery]: https://github.com/velesin/jasmine-jquery
[jasmine-fixture]: https://github.com/searls/jasmine-fixture
[Jasmine's asynchronous support]: http://jasmine.github.io/2.0/introduction.html#section-Asynchronous_Support
[eventClick]: http://arshaw.com/fullcalendar/docs2/mouse/eventClick/
[updateEvent]: http://arshaw.com/fullcalendar/docs2/event_data/updateEvent/
FullCalendar is moving from cumbersome manual tests (which reside in the `/tests/` folder) to fully automated tests (which reside in the `/tests/automated/` folder). The goal is to have a test file for each individual "thing" (option, callback, API method, rendering behavior, whatever) that tests the entirety of its functionality.

The test suite leverages the [Karma Test Runner][Karma]. This is the thing that runs the tests in various browswers and environments. The actual tests themselves are written with Jasmine:

> [Jasmine Introduction]


# Development Tools

First, your JS/CSS files should be built (via `grunt dev` or `build/watch.sh`).
Then you can run the tests from a browser (this will output a URL that you can visit):

	grunt karma:url

Alternatively, you can run the tests headlessly:

	grunt karma:headless

Both commands will watch for any changes made to source files or test files, and then automatically rereun the tests.


# Workflow

1. Create one `.js` file per "thing" you are testing. Put it in the top-level `/tests/automated/` directory. Please do not make nested folders.

2. Write the tests. Look at other tests in the directory for inspiration on how a test file is laid out.

3. Make sure your tests pass. Run the `grunt karma` commands mentioned above.


# Organizing your tests

In each tests file, you should write individual tests that isolate one specific behavior about the given "thing" (option, callback, method, rendering, whatever). Write a `describe` statement that wraps all the individual `it` statement tests:

	describe('updateEvent', function() {
		it('should change the event\'s start date', function() {
			// your test here
		});
		// more tests...
	});

Many settings have different behaviors based on *other* settings. For example, the `header` setting will render differently depending on whether `isRTL` is `true` or `false`. In this case, where a general global setting (like `isRTL`) affects the behaviors of more specific settings (like `header`), make nested groupings under the umbrella of the specific setting:

	describe('header', function() {
		describe('when isRTL is false', function() {
			it('should render the default buttons on the left', function() {
				// your test
			});
		});
		describe('when isRTL is true', function() {
			it ('should render the default buttons on the right', function() {
				// your test
			});
		});
	});

The cool thing is that when a test name is read outloud, with all of its ancestral `describe` statements, it sounds like a sentence, like "header when isRTL is false should render the default buttons on the left".

Similar to how `iRTL` often calls for nested groupings, the `lang` option will often influence the behavior of other settings. Please test for different `lang` values when necessary. It will probably only be necessary to test for the default `lang` as well as one non-default lang. The inner describe statement might look like `"when lang is fr"` (for French).

Also, similar to how `isRTL` often calls for nested groupings, please also make different groupings for each type of view. Usually, testing for one "basic" view (month/basicWeek/basicDay) is okay, as well as one "agenda" view (agendaWeek/agendaDay). This is very necessary, especially for settings that affect rendering, because often the code paths for each view are completely different. It might look something like this:

	describe('weekNumbers', function() {
		beforeEach(function() {
			affix('#calendar');
		});
		describe('when view is basic', function() {
			beforeEach(function() {
				$('#calendar').fullCalendar({
					defaultView: 'basicWeek',
					weekNumbers: true
				});
			});
			it('should render the week number along the side', function() {
				// your test here
			});
		});
		describe('when view is agenda', function() {
			beforeEach(function() {
				$('#calendar').fullCalendar({
					defaultView: 'agendaWeek',
					weekNumbers: true
				});
			});
			it('should render the week numbers along the side', function() {
				// your test here
			});
		});
	});



# What to test for

We are mostly doing "black box testing", which means we don't care about testing the internals, mainly just the input and output. For most options, this will entail inspecting the DOM to see if the option had the desired effect. This might involve checking an element for certain text, a certain className, or a certain pixel width. An example of this can be seen with [minTime].

To test callbacks (like [eventClick]), please simulate a real user mouse event using the jQuery `trigger` method. Then, to determine if the callback has been called, use [Jasmine's asynchronous support].

To test API methods (like [updateEvent]), call the method with a healthy variety of arguments and make sure the desired output occurs, whether by querying DOM or querying the internal state of the calendar with a different method. Also, make sure all the proper callbacks have been called (if applicable).


# Tips

When you are writing a new test, it is often annoying to have to run *all* tests every time while you are debugging. Jasmine allows you to write `ddescribe` and `iit` statements, which will only run the enclosed tests and ignore all others.

Also, when running tests in a real browers via the plain `grunt karma` command, it is very helpful to use Karma's "debug" mode (available via the big DEBUG button). Once you are in that mode, JS debugging tools such as console and breakpoints will become available.

The [jasmine-jquery] library is available to make it easier to query the DOM for certain things. It adds extra DOM-specific utility methods to the `expect()` object.

The [jasmine-fixture] library is available to make it easier to create and attach DOM elements through the `affix` method. They also automatically get cleaned up after each `it` statement runs.


# Code formatting of tests

Please make sure your test code follows the [Style Guide] and that you run `grunt check` before committing.


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
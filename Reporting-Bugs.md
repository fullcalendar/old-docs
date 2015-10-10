Do you think something is broken with FullCalendar's existing behavior? If so, please carefully follow these steps for reporting a bug:


## Step 1: Isolate the bug on your end

More often than not, the problem is with your code, not with FullCalendar. To determine this, remove as much of your code as possible:

- Remove all unrelated stylesheets and JS files
- Remove all unrelated 3rd party libraries
- Remove all server-side logic. Use static HTML and JSON files instead.

For more info on this technique, read [Reduced Test Cases](http://css-tricks.com/reduced-test-cases/).


## Step 2: Post a public demonstration of the bug

Use a service like [JSBin](http://jsbin.com/) or [JSFiddle](http://jsfiddle.net/) to demonstrate the bug online. Enter your JS, CSS, HTML, and JSON and wire up all the relevant dependencies (jQuery, Moment, etc). To help you get started, here are some **debugging templates:**

<div style='margin:2em 0 2em 2em'>
	<div style='font-weight:bold'>FullCalendar Standard</div>
	<div style='margin-top:.5em'>
		<a href='../playground/jsbin.php' target='_blank'>simple template</a> |
		<a href='../playground/jsbin.php?demo=json' target='_blank'>JSON feed template</a>
	</div>
</div>

<div style='margin:2em 0 2em 2em'>
	<div style='font-weight:bold'>Scheduler Add-on</div>
	<div style='margin-top:.5em'>
		<a href='../playground/jsbin.php?demo=scheduler' target='_blank'>simple template</a> |
		<a href='../playground/jsbin.php?demo=scheduler-json' target='_blank'>JSON feed template</a>
	</div>
</div>

*Instructions:* Make your changes, Click *Share* at the top, then get the *Link*.


## Step 3: Describe the bug

Please describe the correct desired behavior and how the buggy behavior differs from that. Please describe, in detail, the exact steps that are needed to reproduce the bug.

*Screenshots* are extremely helpful. For bugs that require complicated user interaction, please post a recorded video screencast if possible.


## Step 4: Use the issue tracker

At this point, anyone should be able to open up your JSFiddle demonstration, read your description, and **reproduce your bug in less than 1 minute!!!**

If this is true, please move on to the appropriate issue tracker:

<div style='margin:2em 0 2em 2em'>
	<div style='font-weight:bold'>FullCalendar Standard</div>
	<div style='margin-top:.5em'>
		<a href='https://github.com/fullcalendar/fullcalendar/issues'>Issue tracker &raquo;</a>
	</div>
</div>

<div style='margin:2em 0 2em 2em'>
	<div style='font-weight:bold'>Scheduler Add-on</div>
	<div style='margin-top:.5em'>
		<a href='https://github.com/fullcalendar/fullcalendar-scheduler/issues'>Issue tracker &raquo;</a>
	</div>
</div>

It is a good idea to search through the list to see if someone has already reported your bug. If so, simply star it to receive notifications about progress. Please enter only one bug per issue and do not combine issues.

## The Timezone Problem

FullCalendar was initially designed without much notion of timezones. By default, it ignores timezone offsets in the dates it receives.

The original assumption was that if you received a date from Brussels, say `"2013-09-01T12:00:00+02:00"`, which is noon, it would display as noon in *every* timezone.

However, FullCalendar shoehorns this value into a *local* date. With the same example, if you were in San Francisco, it internally stores the date as `"2013-09-01T12:00:00-08:00"`. This is bad for two reasons:

1. The underlying JavaScript Date's milliseconds-since-epoch value no longer accurately represents when the date is. As a result, the `getUTC*`/`setUTC*` methods are busted.

2. The date/time values, which can be accessed via the `set*`/`get*` methods, might not exist in the browser's timezone because of daylight savings!

Most importantly, all dates everywhere, regardless of the end-user's timezone, are displayed the same. This makes it impossible for people across the world to view dates that are adjusted to their particular local timezones.

The `ignoreTimezone` option was created to rememdy this (when set to `false`), but the problems related to shoehorning everything into local dates still exist.

FullCalendar needs a stronger notion of timezones.

FullCalendar also needs better default support for languages / internationalization. The [text/time customization options](http://arshaw.com/fullcalendar/docs/text/) are not enough.


## The Solution

[Moment.js](http://momentjs.com/) should be leveraged. 

All dates, everywhere in the API, will now be Moment objects.

Encourage use of ISO8601 dates everywhere, because they can contain timezone information (or lackthereof) and time information (or lackthereof).


### Change the way event dates are parsed

When a date string has no specified timezone designator, like `2013-09-01T12:00:00`, and the `utc` option is `false` (the default), the date will be parsed as local. If `utc` is `true`, the date will be parsed in UTC.

No more ignoring the timezone. We will remove the `ignoreTimezone` option.

Also, when `utc` is set to `true`, all date values in the API will be in [UTC-mode](http://momentjs.com/docs/#/parsing/utc/).


### Change the way dates are sent over AJAX

Event sources currently send unix timestamps. This should change to ISO8601 dates.

By default, no timezone indicator is sent:

```js
$('#calendar').fullCalendar({
	events: 'feed.php'
		// start = 2013-09-01T00:00:00
		//   end = 2013-10-06T00:00:00
});
```

Setting the `transmitTZD` options to `true` will send the current timezone indicator. If in local mode:

```js
$('#calendar').fullCalendar({
	transmitTZD: true
	events: 'feed.php'
		// In in San Francisco, will send:
		// start = 2013-09-01T00:00:00-07:00
		//   end = 2013-10-06T00:00:00-07:00
});
```

If in UTC mode, it will send `Z` as the timezone indicator.

```js
$('#calendar').fullCalendar({
	utc: true,
	transmitTZD: true
	events: 'feed.php'
		// In any timezone, will send:
		// start = 2013-09-01T00:00:00Z
		//   end = 2013-10-06T00:00:00Z
});
```


### Basic example timezone configurations

**Scenario:** Everyone accessing your calendar is in the same timezone. Dates are displayed in each browser's local timezone, with daylight savings applied:

```js
$('#calendar').fullCalendar({
	events: {
		url: 'feed.php'
	}
	// examples request:
	//   start = 2013-09-01T00:00:00
	//     end = 2013-10-06T00:00:00
	//
	// your feed's dates should NOT have timezone indicators.
	// example response:
	// [
	//   {
	//     "title": "my event",
	//     "start": "2013-09-01T12:00:00" // displayed as noon in all timezones
	//   }
	// ]
});
```

**Scenario:** All dates are in a "generic" timezone and you don't want the unpredictability of daylight savings. Use UTC instead:

```js
$('#calendar').fullCalendar({
	utc: true,
	events: {
		url: 'feed.php'
	}
	// examples request:
	//   start = 2013-09-01T00:00:00
	//     end = 2013-10-06T00:00:00
	//
	// your feed's dates should have a 'Z' timezone indicator, or none at all.
	// example response:
	// [
	//   {
	//     "title": "my event",
	//     "start": "2013-09-01T12:00:00" // always displayed as noon
	//   }
	// ]
});
```

**Scenario:** Your dates are stored in one timezone, but displayed in each browser's individual local timezone. They should appear different across the world:

```js
$('#calendar').fullCalendar({
	transmitTZD: true,
	events: {
		url: 'feed.php'
	}
	// examples request, from San Francisco:
	//   start = 2013-09-01T00:00:00-07:00
	//     end = 2013-10-06T00:00:00-07:00
	//
	// your feed SHOULD return dates with timezone indicators.
	// example response:
	// [
	//   {
	//     "title": "my event",
	//     "start": "2013-09-01T12:00:00Z" // different across the world, but noon in San Fran
	//   }
	// ]
});
```


### Other timezones?

What if you want the calendar to operate in timezones other than UTC or the browser's local timezone?

Say you are living in Brussels, with a browser in the "Europe/Brussels" timezone, but you want the calendar to appear as if it is in San Francisco?

In JavaScript, it's really hard and cumbersome to represent timezones other than the one the browser naturally has configured, so let's just mock the timezone using UTC.

You'll want to do three things:

1. Turn on UTC mode so you don't experience any daylight-savings quirks from the browser's local timezone

2. Make sure the feed script is aware of the timezone we want to operate in, via an additional GET parameter ("timezone" in this example)

3. Send the dates back to FullCalendar in "Europe/Brussels" time, but with no timezone designator, so FullCalendar interprets them as UTC dates

Example:

```js
$('#calendar').fullCalendar({
	utc: true,
	events: {
		url: 'feed.php',
		data: {
			timezone: 'Europe/Brussels'
		}
	}
	// examples request, from San Francisco:
	//   start = 2013-09-01T00:00:00
	//     end = 2013-10-06T00:00:00
	//
	// your feed's dates should NOT have timezone indicators.
	// example response:
	// [
	//   {
	//     "title": "my event",
	//     "start": "2013-09-01T12:00:00" // always displayed as noon
	//   }
	// ]
});
```


### Changing today's date

If we simulate another timezone in the browser, the *today* date might be different, at most off-by-one. Maybe add an option to set the current `todayDate`? ([issue 593](https://code.google.com/p/fullcalendar/issues/detail?id=593)).



### Implicit allDay value

When receiving and parsing incoming event data, the `allDay` can be encoded into the date string:

```js
{
	title: 'my event',
	start: '2013-09-01' // implies allDay=true
}

{
	title: 'my event',
	start: '2013-09-01T00:50:00' // implies allDay=false
}

{
	title: 'my event',
	start: '2013-09-01T00:50:00Z' // implies allDay=false
}
```


### While we're at it: event end dates inclusivity/exclusivity

The `end` property of the [Event Object](http://arshaw.com/fullcalendar/docs/event_data/Event_Object/) has a troubled past. Prior to version 1.3, it was always exclusive. Some people, especially ones that only ever wanted all-day events, found this confusing because it is counter to how one verbally communicates the ending of an event.

The other issue was that daylight-savings would sometimes push an event's exclusive end time, which is often `00:00:00` (midnight), into the next day! (`00:01:00`). This would cause events to appear one day longer, seemingly for no reason!

The solution at the time seemed to be to make `end` dates *inclusive*, **but only for all-day events**. This seemed to cause equal amounts of confusion, as programmers often think about end-indices as exclusive.

While we're redesigning all-things date, it might be worth considering this again. At this point, I personally prefer to have exclusive end times, always. It is more consistent. Also, the [iCalendar protocol](http://en.wikipedia.org/wiki/ICalendar) does it this way too.

As for the DST issue with dates flowing into the next day, I know there is a good solution. [Issue 1049](https://code.google.com/p/fullcalendar/issues/detail?id=1049) proposes that events should have a certain threshold they must cross before they are considered to be in the next day. Google calendar implements this, and events must end before 10am the next day. I think we should make this the default.



### Options that accept times

FullCalendar should accept ISO8601-style strings for times. Like `"03:00"` or `"3:00"` or `"3am"`. Similar to what the internal `parseTime` method already does in the current codebase.

We should try to leverage Moment for this.

	minTime
	maxTime
	scrollTime (formerly firstHour)


### Options that accept durations

Leverage Moment's [Duration](http://momentjs.com/docs/#/durations/) object for this.

	slotDuration (formerly slotMinutes)
	snapDuration (formerly snapMinutes)
	defaultEventDisplayDuration (formerly defaultEventMinutes)
	defaultEventDuration (use this to calulate event.end when not specified)



### Date formatting options

All formatting-related options, like `timeFormat` and `titleFormat`, should now use Moment's [formatting codes](http://momentjs.com/docs/#/displaying/format/).

Date *range* formatting will no longer be explicitly specified with formatting strings (like `h:mm{ - h:mm}`), but rather, a formatting string for a single date will be provided and FullCalendar will be smart about inserting a dash between the two dates, like `Sep 2 - 9 2013`.



###  i18n

FullCalendar will leverge Moment's existing translations, as well as jQuery UI datepickers translations. Neither single library alone has enough info, se we'll have to combine them.

There will be two options if you want to include translation files:

1. Include *both* Moment's and jQuery UI's pre-packaged translation files as-is

2. Include FullCalendar's translation files, which are a merge of the two. Both Moment and jQuery UI translations will also be initialized.

Here is a [proposed translation file sample](https://gist.github.com/arshaw/6420211).

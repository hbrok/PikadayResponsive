Pikaday Responsive
=================

A responsive datepicker built on top of Pikaday. It shows the native datepicker on mobile devices and a nice JS-picker on desktop. It is realised as a jQuery-Plugin.

Pikaday is a nice and lean datepicker. For more details, see here: https://github.com/dbushell/Pikaday

Try the demo: https://mydea.github.io/PikadayResponsive/

Try it on mobile, too:

![PikadayResponsive Demo](https://api.qrserver.com/v1/create-qr-code/?data=https%3A%2F%2Fmydea.github.io%2FPikadayResponsive%2F&size=220x220&margin=0)

## Why?
Pikaday is a great datepicker, and there are a lot of other datepickers out there that work really well. However, all of them fall short when used on a mobile device, where the native datepickers work best (because they have been specifically optimised for the mobile experience). Native Datepickers have some drawbacks, though:

* The output format cannot be customized
* While they work quite reliably on mobile devices, support on desktop devices is either non-existing (Firefox) or their UX is rather terrible (Chrome).

PikadayResponsive tries to solve this problem.

## How it works
Basically, PikadayResponsive tries to detect if you are on a mobile device. For this, Modernizr is used (altough you can choose to use a different feature detection library if you want). It checks for touch and HTML5 input-type date support, and if one of them is missing, it simply displays a Pikaday-datepicker.

If, however, touch AND HTML5-date support are detected, it will instead display a native input type="date". Over this native input field, another, readonly and click-trough input field is displayed, in which a formatted date is displayed.

## Dependencies
PikadayResponsive needs the following components to work:

* jQuery
* Moment.js (for date formatting)
* Pikaday (https://github.com/dbushell/Pikaday)
* Modernizr or other feature detection library

You can also use pikaday-package.js, which bundles Moment.js, Pikaday, and Pikaday Responsive. It does not, however, contain jQuery and Modernizr - you have to add them manually. The dist/ directory contains the package files, a directory package_components/ which contains the individual files the package bundles, and a directory dependencies/ which contains jQuery and an automatically generated copy of modernizr pared down to only the features PikayResponsive requires.

To build a fresh copy of the package, with the latest version of the dependencies, simply download or clone the directory, and run `npm install` followed by `grunt`.

## Usage
You can install PikadayResponsive via Bower:

```console
bower install pikaday-responsive --save
```

You will need to include the following scripts at the bottom of your site:

```html
<script src="bower_components/jquery/dist/jquery.min.js"></script>
<script src="bower_components/pikaday-responsive/dist/pikaday-package.js"></script>
```

or alternatively:

```html
<script src="bower_components/jquery/dist/jquery.min.js"></script>
<script src="bower_components/momentjs/moment.js"></script>
<script src="bower_components/pikaday/pikaday.js"></script>
<script src="bower_components/pikaday-responsive/dist/pikaday-responsive.js"></script>
```

And in the head-section

```html
<link rel="stylesheet" href="bower_components/pikaday-responsive/dist/pikaday-package.css">
<script src="bower_components/modernizr/modernizr.js"></script>
```

As mentioned above, you can also use head.js or another feature detection library instead of Moderizr.

The CSS-file contains the basic styles for the Pikaday-Datepicker. You can change/overwrite them as you wish.

To use it, call the following on an input field:

HTML:

```html
<input id="date" name="date" />
```

JS:

```js
var dateObject = pikadayResponsive(document.getElementById("date"));
```

The `dateObject` returned from `pikadayResponsive()` is a POJO (plain old JavaScript object) with the following properties:

```javascript
dateObject = {
    pikaday: null, // This is either null (if native date is used) or the Pikaday-instance
    element: input#date1, // The original element
    value: "2015-10-20", // The current value of the input. This is the date formatted with outputFormat
    date: Object, // The Moment.js-Date-Object of the current value
}
```


## Setting the date: instance.setDate(newDate)

In addition, the dateObject has a function: `setDate()`. This function takes either a Moment.js object, a native JS-Date or a string in the specified `outputFormat`. 
Optionally, you can also pass in a format to parse from as second parameter: `instance.setDate(newDate, "YYYY-MM-DD")`.
The input will be set to this value (no matter if it is a native date picker or a Pikaday picker) and a change-event will be triggered.
If you call `dateObject.setDate(null)`, the field will be cleared. If you want to set the picker to the current day, you can simply call `dateObject.setDate(moment())`.

Whenever the input changes, the value on the original input will be updated as well and a regular `change` event will be triggered.
Additonally, a `change-date` event will be triggered on the original input. This event receives the dateObject as its second parameter:

```js
var el = document.getElementById("date1");
var dateObject = pikadayResponsive(el);
$(el).on("change-date", function(e, dateObject) {
    // do something with the dateObject
});
```

Note that the date object is always the same object and that it will automatically update its value/date:

```js
var el = document.getElementById("date1");
var dateObject = pikadayResponsive(el);
console.log(dateObject.value); // null

// The user enters "2015-10-5" in the input field
console.log(dateObject.value); // 2015-10-5
```

Note that the fields will not be updated automatically if you change the value programmatically. 
You have to update the date with `setDate()` in order for it to take effect.

## Configuration
There are some options to configure PikadayResponsive. Following are the default-values:

```js
pikadayResponsive(document.getElementById("date1"), {
    format: "DD.MM.YYYY",
    outputFormat: "x",
    checkIfNativeDate: function() {
        // return true if native date field should be used
    },
    placeholder: "",
    classes: "",
    dayOffset: 0,
    pikadayOptions: {}
});
```

### format
Determines how a date will be displayed in the input-field. Has to be a Moment.js format-string, like `DD.MM.YYYY`. 
See [Moment.js Docs](http://momentjs.com/docs/#/displaying/) for all available options.

### outputFormat
Determines the output of the field. Basically, this is what you will get if you call `$("#date").val()` or if you submit the form. has to be a Moment.js format-string, like "YYYY-MM-DD".
See [Moment.js Docs](http://momentjs.com/docs/#/displaying/) for all available options.

### placeholder
The placeholder for the input-field.

### classes
A string with classes that should be added to the displayed input-fields

### dayOffset
A number which is added to the date in Pikaday-mode. This is especially useful when working with timezones. 
If you set a default timezone with moment-timezone, like `moment.tz.setDefault("America/Los_Angeles")`, your dates may turn out wrong if your browser's timezone is different. This is caused if the specified timezone's offset is lower than your browser's timezone offset.
In this case, the picked date, which might be "2016-04-12 00:00:00" in "America/Los_Angeles", is converted to your local time zone, e.g. to "2016-04-11 22:00:00". This can be very hard to work with.
The number of days specified here will simply be added to the selected date before it is processed. In the above case, the date that would actually be returned as value would be "2016-04-12 22:00:00". 

An example implementation would be:

```js
var defaultOffset = moment.tz("Europe/Vienna")._offset; // This is your client's timezone
var currentOffset = moment()._offset; // This is your specified timezone

var dayOffset = 0;
if(currentOffset < defaultOffset) {
    dayOffset = 1;
}

var $date1 = $("#date1");
var instance1 = pikadayResponsive($date1, {
    dayOffset: dayOffset
});
```

### checkIfNativeDate
You can overwrite this, for example if you don't want to use Modernizr. This has to be a function. 
If this function returns true, the native date picker will be used, otherwise Pikaday will be used.
By default, the following function is used:

```js
function () {
    return Modernizr.inputtypes.date && (Modernizr.touch && navigator.appVersion.indexOf("Win") === -1);
}
```

### pikadayOptions
An object with options that will be used to initialize Pikaday. Note that ```field``` and ```format``` will be overridden.

## Manually opening the date picker

Opening the date picker from somewhere else on the page can be a bit tricky.
The best way to do this is by simply using standard HTML labels.
Since 0.7.0, the generated inputs will receive an ID, if the original input had an ID. It will be the original ID + `-input`. 
For example:

```html
<input id='date1'>
```

will become:

```html
<input id='date1-input' class='...'>
```

This makes it possible to trigger the date picker from any place on your page with a simple label:

```html
<label for='date1-input'>Open Date Picker</label>
```

See an example for this on the bottom of the [Demo](https://mydea.github.io/PikadayResponsive/).

## Changelog

For changes, see the [Changelog](CHANGELOG.md)

Additionally, please note the breaking changes from 0.5 to 0.6: [Information about upgrading to >= 0.6.0](upgrading-to-v0-6.md)

## Author
PikadayResponsive has been created by Francesco Novy | http://www.fnovy.com | francesconovy@gmail.com | @_fnovy

## Credits
Credits go to David Bushell and Ramiro Rikkert for creating Pikaday.

* David Bushell http://dbushell.com @dbushell
* Ramiro Rikkert GitHub @RamRik

## Copyright
Copyright © 2016 Francesco Novy | MIT license

---
layout: post
title: "Grails g:select with remoteFunction"
date: 2013-02-12 18:51
comments: true
categories: [grails, javascript, ajax]
---
I linger on several mailing lists and forums looking to help folks with their Grails issues and one of the more common questions I see is about using the Grails select taglib along with remoteFunction.

I decided to create a sample project with a couple of different ways to select an option from a select element that uses ajax to update a 2nd select element, since this is what most questions I see are asking about.  And I'll use this blog post to walk through the code and hopefully I'll be able to just post to this example when answering questions in the future.

<!-- more -->

I'm using Grails 2.2.0 which uses the jQuery plugin by default and I've also installed the grails bootstrap plugin.  Generally, I'll pull down bootstrap's files and include them via the resource plugin myself but for this example it was faster to do it this way.

The first thing I did was clean up the main.gsp layout and remove a lot of the Grails specific stuff from executing create-app.  I also added the jquery library and the bootstrap module.

{% highlight html %}
<!DOCTYPE html>
<!--[if lt IE 7 ]> <html lang="en" class="no-js ie6"> <![endif]-->
<!--[if IE 7 ]>    <html lang="en" class="no-js ie7"> <![endif]-->
<!--[if IE 8 ]>    <html lang="en" class="no-js ie8"> <![endif]-->
<!--[if IE 9 ]>    <html lang="en" class="no-js ie9"> <![endif]-->
<!--[if (gt IE 9)|!(IE)]><!--> <html lang="en" class="no-js"><!--<![endif]-->
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <title><g:layoutTitle default="Grails"/></title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="shortcut icon" href="${resource(dir: 'images', file: 'favicon.ico')}" type="image/x-icon">
  <link rel="apple-touch-icon" href="${resource(dir: 'images', file: 'apple-touch-icon.png')}">
  <link rel="apple-touch-icon" sizes="114x114" href="${resource(dir: 'images', file: 'apple-touch-icon-retina.png')}">
  <link rel="stylesheet" href="${resource(dir: 'css', file: 'main.css')}" type="text/css">
  <g:layoutHead/>
  <g:javascript library='jquery'/>
  <r:require modules="bootstrap"/>
  <r:layoutResources/>
</head>

<body>
<div>
  <g:layoutBody/>
  <g:javascript library="application"/>
  <r:layoutResources/>
</div>
</body>
</html>
{% endhighlight %}

I also added my own JavaScript file called main.js and included it in the ApplicationResources.groovy.

{% highlight groovy %}
modules = {
  application {
    resource url: 'js/application.js'
    resource url: 'js/main.js'
  }
}
{% endhighlight %}

main.js only includes a single function that handles rendering JSON data into option elements and appending them to a select element.  We'll use this function from the remoteFunction call in the gsp.

{% highlight javascript %}
function populateCities(data) {
  var $element = $('#cities');
  $element.empty();
  $.each(data, function(key, value) {
    var option = $('<option/>').val(key).text(value);
    $element.append(option)
  });
  $element.removeAttr('disabled');
}
{% endhighlight %}

Next, I wanted to setup the controller for the example and I needed a good parent-child relationship but didn't want to mess with creating Domains so I created the SelectController and put some simple static maps of data to use which looks like this:

{% gist 4942438 %}

We have some states and their cities.  The idea is that we will select a state and then another select will populate with its cities.

The first example is going to populate a select with cities from JSON.  I created an index.gsp with the following code:

{% gist 4942447 %} 

The key parts are:

{% gist 4942452 %}

- Line 3 gets the initial map of states to populate the select list.
- Line 7 defines the remoteFunction which will call the citiesByState action.  I'll explain the .json in a bit
- Line 8 specifies the function to call when the response is successful and passes in the data from the response to that function.  In our case, JSON data.
- Line 9 defines the params that get passed through the request.
    * this.value is the value of the state select

The SelectController gets updated with the following code:

{% gist 4942469 %}

- Line 9 pushes the state map to the index view.
- Line 13 gets cities map based on the state abbreviation from the request
- Lines 14-18 pass the cities map back as JSON.  I use withFormat here because I want to use the same method for different response types.  In a moment I'll add the HTML response type to the same method.
If you run the code as is:

- select a state from the select list
- An ajax call is made to citiesByState
- The cities are returned as JSON
- remoteFunction calls populateCities() in main.js
- populateCities adds the options to the disabled cities select and enables it.

The next part of the example will make the same ajax call to the server but instead of using a JSON response and our populateCities() function to fill in the city options, we will render a template that gets returned as HTML and remoteFunction will update an HTML element on the page.

index.gsp now looks like this:

{% highlight html %}
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
  <title>Ajax Select</title>
  <meta name="layout" content="main"/>
</head>
<body>
<div class="container-fluid">
  <form class="form-horizontal">
    <fieldset>
      <legend>JSON Update</legend>

      <div class="control-group">
        <label class="control-label" for="state">State</label>

        <div class="controls">
          <g:select
              name="state"
              from="${states.entrySet()}"
              optionKey="key"
              optionValue="value"
              noSelection="['-1': '-Select State-']"
              onchange="${remoteFunction(action: 'citiesByState.json',
                  onSuccess: 'populateCities(data)',
                  params: '\'stateAbbrv=\' + this.value')}"/>
        </div>
      </div>

      <div class="control-group">
        <label class="control-label" for="cities">Cities</label>

        <div class="controls">
          <select name="cities" id="cities" disabled></select>
        </div>
      </div>
    </fieldset>
  </form>

  <form class="form-horizontal">
    <fieldset>
      <legend>HTML Update</legend>

      <div class="control-group">
        <label class="control-label" for="state">State</label>

        <div class="controls">
          <g:select
              name="stateHTML"
              from="${states.entrySet()}"
              optionKey="key"
              optionValue="value"
              noSelection="['-1': '-Select State-']"
              onchange="${remoteFunction(action: 'citiesByState.html',
                  update: [success: 'citiesContainer'],
                  params: '\'stateAbbrv=\' + this.value')}"/>
        </div>
      </div>

      <div class="control-group">
        <label class="control-label" for="cities">Cities</label>

        <div class="controls" id="citiesContainer">
        </div>
      </div>
    </fieldset>
  </form>
</div>
</body>
</html>
{% endhighlight %}

I've added another form section so both can execute on the same page.  The main difference is on line 56.  I tell the remoteFunction that on success, update the container with an ID of citiesContainer with the response.

SelectController now looks like this:

{% highlight groovy %}
package com.ajax.select

import grails.converters.JSON

class SelectController {

  static states = ['KS': 'Kansas', 'OK': 'Oklahoma']
  static cities = ['KS': ['1': 'Dodge City', '2': 'Topeka', '3': 'Wichita'], 'OK': ['4': 'Norman', '5': 'Stillwater', '6': 'Tulsa']]

  def index() {
    [states: states]
  }

  def citiesByState() {
    def stateCities = cities[params.stateAbbrv]
    withFormat {
      html {
        render template: 'cities', model: [cities: stateCities]
      }
      json {
        render stateCities as JSON
      }
    }

  }
}
{% endhighlight %}

I added an additional block in withFormat for HTML and told it to render the _cities.gsp template passing in the cities map.  That template looks like this:

{% highlight groovy %}
<g:select name="citiesHTML"
          from="${cities.entrySet()}"
          optionKey="key"
          optionValue="value"/>
{% endhighlight %}

And that's it.  You can see 2 different ways to render a child select list based on a parent select list's selection with minimal JavaScript required.  

The complete project can be accessed on github here:

[https://github.com/gdboling/ajax-select](https://github.com/gdboling/ajax-select "ajax select project")
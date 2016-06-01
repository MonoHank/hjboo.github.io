---
layout: post
title:  "jQuery Plugins as Angular Directives"
categories: code
author: Ben Centra
date: 2015-09-29 20:00:00
tags: jquery plugin angular directives javascript
---

So you've chosen [AngularJS][angular] as your framework of choice for you next big web project. Good for you! But during the course of development you realize you need to utilize some external libraries. Maybe you need a slider component, and decide to go with the one included with [jQuery UI][slider].

Using jQuery plugins - not just the ones included in jQuery UI - is easy on a normal web page. Throwing Angular into the mix makes it a little more complicated. Here are some tips for using jQuery plugins inside of AngularJS apps.

_Check out [this CodePen][codepen] for interactive examples of the code below!_

## Plain-ol' jQuery Plugin

On their own, jQuery plugins are easy to use: just initialize the plugin in an [IIFE][iife] or a `document.ready` event. 

{% highlight html %}
<div id="ng-slider"></div>
<p>The slider's value is: <span id="slider-value">0</span></p>
{% endhighlight %}

{% highlight javascript %}
$(document).ready(function() {
  // Initialize the slider
  $('#slider').slider({
    change: function(event, ui) {
      $('#slider-value').html(ui.value);
    }
  });
});
{% endhighlight %}

## jQuery Plugin in an Angular App

Trying to use a jQuery plugin in an Angular app is trickier. Unfortunately you can't just move the initialization into your controller. Not only is it bad practice, but Angular won't be aware of any changes to or interactions with the plugin. This means that data binding with the plugin will be broken. 

{% highlight html %}
<div ng-app="MyApp">
  <div ng-controller="AppCtrl">
    <div id="ng-slider"></div>
    <p>The slider's value is: {{siderValue}}</p>
  </div>
</div>
{% endhighlight %}

{% highlight javascript %}
var app = angular.module('MyApp', []);
app.controller('AppCtrl', ['$scope', function($scope) {
  $scope.sliderValue = 0;
  // This slider won't work. Something's missing...
  $('#ng-slider').slider({
    slide: function(event, ui) {
      $scope.sliderValue = ui.value;
    }
  });
}]);
{% endhighlight %}

Since you probably chose Angular because of its strong templating and data binding capabilities, this doesn't look good.

### The Fix: Use $scope.$apply()

You can restore Angular's data binding using [`$scope.$apply()`][apply]. This method hooks into Angular's digest cycle, making your app aware of the changes made by the jQuery plugin to any `$scope` properties. 

{% highlight javascript %}
var app = angular.module('MyApp', []);
app.controller('AppCtrl', ['$scope', function($scope) {
  $scope.sliderValue = 0;
  // This slider WILL work
  $('#ng-slider').slider({
    slide: function(event, ui) {
      // Tell Angular you're updating a value
      $scope.apply(function() {
        $scope.sliderValue = ui.value;
      });
    }
  });
}]);
{% endhighlight %}

## Creating a Direcitve

To make the jQuery plugin code more reusable, you can create a [directive][directives] for the plugin. Any plugin initialization code should go in the directive's `link` method. Include the directive in your HTML and you should be good to go.

{% highlight html %}
<div ng-app="MyApp">
  <div ng-controller="AppCtrl">
    <div jq-slider model="sliderValue"></div>
    <p>The slider's value is: <input ng-model="sliderValue"/></p>
  </div>
</div>
{% endhighlight %}

{% highlight javascript %}
var app = angular.module('MyApp', []);
app.controller('AppCtrl', ['$scope', function($scope) {
  $scope.sliderValue = 50;
}]);
app.directive('jqSlider', [function() {
  return {
    restrict: 'A',
    scope: {
      'model': '='
    },
    link: function(scope, elem, attrs) {
      $(elem).slider({
        value: +scope.model,
        slide: function(event, ui) {
          $scope.apply(function() {
            scope.model = ui.value;
          });
        }
      });
    }
  };
}]);
{% endhighlight %}

### Adding Two-way Data Binding

The above example only gives you one-way data binding, from the plugin to the model. What if you want to update the plugin when the model changes from another input? Using [`$scope.$watch()`][watch], you can have Angular watch for changes to a value and have your directive update to reflect those changes. 

{% highlight javascript %}
var app = angular.module('MyApp', []);
app.controller('AppCtrl', ['$scope', function($scope) {
  $scope.sliderValue = 50;
}]);
app.directive('jqSlider', [function() {
  return {
    restrict: 'A',
    scope: {
      'model': '='
    },
    link: function(scope, elem, attrs) {
      $(elem).slider({
        value: +scope.model,
        slide: function(event, ui) {
          $scope.apply(function() {
            scope.model = ui.value;
          });
        }
      });
      // Watch for changes to the model and update the slider
      scope.$watch('model', function(newVal) {
        $slider.slider('value', newVal);
      });
    }
  };
}]);
{% endhighlight %}

## Complete Example

Below is one final example of a jQuery slider directive. In addition to the techniques seen above, it also reads HTML attributes to configure the slider (available in the `link` method's `attrs` property). 

{% highlight html %}
<div ng-app="MyApp">
  <div ng-controller="AppCtrl">
    <div jq-slider model="sliderValue" min="25" max="75" step="5"></div>
    <p>The slider's value is: <input ng-model="sliderValue"/></p>
  </div>
</div>
{% endhighlight %}

{% highlight javascript %}
var app = angular.module('MyApp', []);
app.controller('AppCtrl', ['$scope', function($scope) {
  $scope.sliderValue = 50;
}]);
app.directive('jqSlider', [function() {
  return {
    restrict: 'A',
    scope: {
      'model': '='
    },
    link: function(scope, elem, attrs) {
      $(elem).slider({
        value: +scope.model,
        min: +attrs.min,
        max: +attrs.max,
        step: +attrs.step,
        slide: function(event, ui) {
          $scope.apply(function() {
            scope.model = ui.value;
          });
        }
      });
      scope.$watch('model', function(newVal) {
        $slider.slider('value', newVal);
      });
    }
  };
}]);
{% endhighlight %}

_Check out [this CodePen][codepen] for interactive examples of the code above!_

[angular]: https://angularjs.org/
[slider]: https://jqueryui.com/slider/
[iife]: http://benalman.com/news/2010/11/immediately-invoked-function-expression/
[directives]: https://docs.angularjs.org/guide/directive
[digest]: https://www.ng-book.com/p/The-Digest-Loop-and-apply/
[apply]: http://jimhoskins.com/2012/12/17/angularjs-and-apply.html
[watch]: https://egghead.io/lessons/angularjs-the-basics-of-scope-watch
[codepen]: http://codepen.io/bencentra/pen/wKWWLv

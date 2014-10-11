## About
What is Tractor, you ask? Why would you use it?

Micah introduced the project in [a session on Drupalcon Prague](https://prague2013.drupal.org/session/creating-responsive-drupal-prototypes-angularjs). You can [watch the session on youtube](http://www.youtube.com/watch?v=8kLXigbdRRU).

## Installation
After downloading Tractor, you need to make sure you have the appropriate Ruby gems. To do this, we use bundler.

```shell
$ sudo gem install bundler
$ bundle
```

Next you will either want to point your server at the app folder, or install Grunt (preferred method). To install Grunt you will first need [Node](http://nodejs.org/download/) and [NPM](https://npmjs.org/) installed. Then:

```shell
$ sudo npm install
```

This will download all of the appropriate javascript packages needing to create the server. Once this is complete you can run "grunt server" and Tractor will spin up a grunt server and open up the start page for you.

## Editing the Home Page
To edit the start page in Tractor simply add valid markup to the views/home.html file. All pages inherit the basic HTML structure of index.html which includes ```<html>```  ```<head>```  ```<body>``` and a ```<div class="page">``` page wrapper.

## Creating New Pages
Any html file placed into the views directory can be accessed by visiting ```<root_url>/#/file_name```. So to create an about-us or blog page simply create ```app/views/about-us.html``` or ```app/views/blog.html``` file.

# Glossary

Terms specific to the functionality of Tractor.

## Partials
To create a HTML snippet or "partial", just create a new html file in the views/partials directory. The partial may only contain a single parent element, but can have as many child elements as needed.

To include a partial into your homepage Tractor comes with a custom directive called <partial>. So if you have a partial called "navigation.html" in your partials folder this is the markup to insert into your home page:

```html
<partial file="navigation"></partial>
```

Note that you don't need to specify the view or partial folder as they are assumed, and you will also ommit the extension as it is assumed. If you decide to organize your partials into folders you simply include the path from the partials folder:

```html
<partial file="path/from/partial/folder/navigation"></partial>
```

Partials can also be called from within other partials. When doing so you will always specify their path from the partials folder, not relative to where the partial is (if the partial is inside a folder).

## Layout and Regions

One of the most powerful features of Tractor is template inheritance. With it you are able to create various static templates and  dynamically apply them to pages of content.

Template inheritance allows you to define all of your regions along with classes, ID's, aria roles in a single place, and use a set of regions for many pages.

### Create a Layout

Create an html file in the views/layouts folder. Best practice is to append the word "layout" to the end of any layout file name. A layout file will then contain all of the region markup for your page. Your homepage regions might look something like the following:

```html
<header id="page-header"> </header>

<div class="container">
  <div class="main-content" role="main"> </div>
  <aside class="sidebar" role="complementary"> </aside>
</div>

<footer id="page-footer"> </footer>
```

To convert this to a Tractor template file you will simply add a "region" attribute to each of the elements that you will be placing content into, and give it an appropriate name. Here is what your home_layout.html file will look once that is done:

```html
<header region="header" id="page-header"> </header>

<div class="container">
  <div region="main-content" class="main-content" role="main"> </div>
  <aside region="sidebar" class="sidebar" role="complementary"> </aside>
</div>

<footer region="footer" id="page-footer"> </footer>
```
### Using a Layout

Going back to our home.html page, there are 2 things we'll want to do in order to start using this template. First off we declare which template we want to inherit using Tractor's ```<layout>``` directive:

```html
<layout file="home_layout">
  <img src="logo.png">
  <partial file="navigation"></partial>
  <partial file="welcome-copy"></partial>
  <partial file="call-to-action"></partial>
  <partial file="blog-links"></partial>
  <partial file="footer-nav"></partial>
</layout>
```

Secondly we need to place all of our markup and partials into a region using Tractor's ```<region>``` directive:

```html
<layout file="home_layout">
  <region name="header">
    <img src="logo.png">
    <partial file="navigation"></partial>
  </region>
  <region name="main-content">
    <partial file="welcome-copy"></partial>
    <partial file="call-to-action"></partial>
  </region>
  <region name="sidebar">
    <partial file="blog-links"></partial>
  </region>
  <region name="footer">
    <partial file="footer-nav"></partial>
  </region>
</layout>
```

When you visit your homepage you will now get the following:

```html
<header region="header" id="page-header">
  <img src="logo.png">
  <partial file="navigation"></partial>
</header>

<div class="container">
  <div region="main-content" class="main-content" role="main">
    <partial file="welcome-copy"></partial>
    <partial file="call-to-action"></partial>
  </div>
  <aside region="sidebar" class="sidebar" role="complementary">
    <partial file="blog-links"></partial>
  </aside>
</div>

<footer region="footer" id="page-footer">
  <partial file="footer-nav"></partial>
</footer>
```

This might seem a lot of work when getting started, but for larger projects you will certainly start to see significant benefits. If you happen to have several pages based on a single template this method will allow you to add/remove/change classes, ID's or other attributes from regions without having to replicate those changes across all of the files. You can also reorder regions inside of your template (move sidebar before content) without having to change the order of the content in all of your pages.

## Additional features

### Placeholder Images

You can use the `ph-img` attribute to add placeholder images:

```html
<img ph-img="230x400" />
<img ph-img="230x400" text="asdf" bg-color="#aa0000" txt-color="#00f" />
```

It also works for background images:

```html
<div ph-img="450x600" style="width: 450px; height: 600px">asdfasdasdf</div>
```

(Note that the size of the background image is independent of the size of the element)

### Placeholder Text

The default is an element containing 3-7 paragraphs of 3-5 sentences each:

```html
<ph-txt>
```
You can also get smaller amounts, and add it to other elements.

```html
<div>
  My text here. Followed by Lorem Ipsum. <ph-txt words=30>
</div>
```

```html
<div ph-txt paragraphs=2 sentences=9>
</div>
```

### Repeating Directive

Use `repeat=` to repeat the contents of an element. The new elements are created with jQuery's `.clone()` method.

```html
<div repeat=3>
  <h3>Hey there!</h3>
  <div>This is my block content.</div>
</div>
```
This results in the following markup in-browser:

```html
<div repeat="3" class="ng-scope">
  <h3>Hey there!</h3>
  <div>This is my block content.</div>
  <h3>Hey there!</h3>
  <div>This is my block content.</div>
  <h3>Hey there!</h3>
  <div>This is my block content.</div>
</div>
```

Note that text content outside your inner elements is not cloned. This means
that if your contents consist entirely of inline elements, the cloned elements
will not have spaces between them.


### Pane directive

### Tab directive

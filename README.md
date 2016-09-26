# Task Lists

*NOTE:* _This repository is no longer supported or updated by GitHub. If you wish to continue to develop this code yourself, we recommend you fork it._</code>

## Let's Make This Clear:  This is the VANBOOM FORK with amazing new and unsupported features.

[![Gem Version](http://img.shields.io/gem/v/task_list.svg)][gem]
[![Build Status](http://img.shields.io/travis/github/task_list.svg)][travis]
[![Dependency Status](http://img.shields.io/gemnasium/github/task_list.svg)][gemnasium]
[![Inline docs](http://inch-ci.org/github/github/task_list.svg?branch=master)][inchpages]

[gem]: https://rubygems.org/gems/task_list
[travis]: https://travis-ci.org/github/task_list
[gemnasium]: https://gemnasium.com/github/task_list
[inchpages]: http://inch-ci.org/github/github/task_list

This package provides various components necessary for integrating
[Task Lists](https://github.com/blog/1375-task-lists-in-gfm-issues-pulls-comments)
into your GitHub-flavored-Markdown user content.

## Components

The Task List feature is made of several different components:

* GitHub-flavored-Markdown Ruby Filter
* Summary Ruby Model: summarizes task list items
* JavaScript: frontend task list update behavior
* CSS: styles Markdown task list items

## Usage & Integration

The backend components are designed for rendering the Task List item checkboxes, and the frontend components handle updating the Markdown source (embedded in the markup).

### Backend: Markdown pipeline filter

Rendering Task List item checkboxes from source Markdown depends on the `TaskList::Filter`, designed to integrate with the [`html-pipeline`](https://github.com/jch/html-pipeline) gem. For example:

``` ruby
require 'html/pipeline'
require 'task_list/filter'

pipeline = HTML::Pipeline.new [
  HTML::Pipeline::MarkdownFilter,
  TaskList::Filter
]

pipeline.call "- [ ] task list item"
```

### Frontend: Markdown Updates

Task List updates on the frontend require specific HTML markup structure, and must be enabled with JavaScript.

Rendered HTML (the `<ul>` element below) should be contained in a `js-task-list-container` container element and include a sibling `textarea.js-task-list-field` element that is updated when checkboxes are changed.

``` markdown
- [ ] text
```

``` html
<div class="js-task-list-container">
  <ul class="task-list">
    <li class="task-list-item">
      <input type="checkbox" class="task-list-item-checkbox" disabled />
      text
    </li>
  </ul>
  <form>
    <textarea class="js-task-list-field">- [ ] text</textarea>
  </form>
</div>
```

Enable Task List updates with:

``` javascript
$('.js-task-list-container').taskList('enable')
```

NOTE: Updates are not persisted to the server automatically. Persistence is the responsibility of the integrating application, accomplished by hooking into the `tasklist:change` JavaScript event. For instance, we use AJAX to submit a hidden form on update.

Read through the documented behaviors and samples [in the source][frontend_behaviors] for more detail, including documented events.

[frontend_behaviors]: https://github.com/github/task_list/blob/master/app/assets/javascripts/task_list.coffee

## Installation

Task Lists are packaged as both a RubyGem with both backend and frontend behavior, and a Bower package with just the frontend behavior.

### Backend: RubyGem

For the backend Ruby components, add this line to your application's Gemfile:

    gem 'task_list'

And then execute:

    $ bundle

### Frontend: Bower

For the frontend components, add `task_list` to your Bower dependencies config.

This is the preferred method for including the frontend assets in your application. Alternatively, for Rails methods using `Sprockets`, see below.

### Frontend: Rails 3+ Railtie method

``` ruby
# config/application.rb
require 'task_list/railtie'
```

### Frontend: Rails 2.3 Manual method

Wherever you have your Sprockets setup:

``` ruby
Sprockets::Environment.new(Rails.root) do |env|
  # Load TaskList assets
  require 'task_list/railtie'
  TaskList.asset_paths.each do |path|
    env.append_path path
  end
end
```

If you're not using Sprockets, you're on your own but it's pretty straight
forward. `task_list/railtie` defines `TaskList.asset_paths` which you can use
to manage building your asset bundles.

### Dependencies

At a high level, the Ruby components integrate with the [`html-pipeline`](https://github.com/jch/html-pipeline) library, and the frontend components depend on the jQuery library. The frontend components are written in CoffeeScript and need to be preprocessed for production use.

## Testing and Development

JavaScript unit tests can be run with `script/testsuite`.

Ruby unit tests can be run with `rake test`.

Functional tests are useful for manual testing in the browser. To run, install
the necessary components with `script/bootstrap` then run the server:

```
rackup -p 4011
```

Navigate to http://localhost:4011/test/functional/test_task_lists_behavior.html

## Community Integration
- [Waffle.io](http://waffle.io)
- [HuBoard](https://huboard.com/)

## Contributing

Read the [Contributing Guidelines](CONTRIBUTING.md) and open a Pull Request!

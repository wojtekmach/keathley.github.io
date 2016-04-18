---
layout: "post"
title: "concurrent-feature-testing-with-wallaby”
date: "2016-04-07 22:05"
---

Feature tests are one of the best ways to ensure reliability and consistency for web applications. But, [as we’ve discussed previously](http://blog.carbonfive.com/2016/03/01/concurrent-acceptance-testing-in-elixir/) feature tests can become a performance bottleneck for a large test suite.

With the fast approaching release of Ecto 2.0 Elixirists will be able to run feature tests for Phoenix applications concurrently. To take advantage of these performance benefits we wanted a testing tool that supported concurrent tests out of the box and provided a flexible api for querying and interacting with webpages.

Thats why we built [Wallaby](https://github.com/keathley/wallaby).

## Introducing Wallaby

Wallaby is a concurrent, feature testing library for Elixir and Phoenix applications. It provides a flexible api for querying and selecting visible DOM elements and their attributes. For instance if you wanted to query a list of users you could write a test like this:

```elixir
defmodule YourApp.UserListTest do
  use YourApp.AcceptanceCase, async: true

  test “users have names”, %{session: session} do
    first_employee =
      session
      |> visit(“/users”)
      |> find(“.dashboard”)
      |> all(“.user”)
      |> List.first
      |> find(“.user-name”)

    assert has_text?(first_employee, “Chris”)
  end
end
```

## Concurrent by default

We're big fans of PhantomJS for feature tests. Wallaby manages a pool of PhantomJS browsers to run each test case concurrently. This ensures that tests don't share session information or cookies.

## Interacting with forms

We like readable tests. Wallaby allows you to interact with form elements based on their id, name, or label text:

```elixir
fill_in(session, “#last_name_field”, with: “Grace”)
fill_in(session, “Last Name”, with: “Hopper”)
choose(session, “Radio Button 1”)
check(session, “Checkbox”)
uncheck(session, “Checkbox”)
select(session, “My Awesome Select”, option: “Option 1”)
click(session, “Some Button”)
click_link(session, “Page 1”)
```

If actions need to be scoped within a specific DOM node then they can be composed with a finder:

```elixir
defmodule YourApp.UserRegistrationTest do
  use YourApp.AcceptanceCase, async: true

  test “users can register”, %{session: session} do
    session
    |> visit(“/users/new”)
    |> find(“.registration_form”)
    |> fill_in(“Full Name”, with: “Grace Hopper”)
    |> fill_in(“Email”, with: “grace@hoppper.com”)
    |> fill_in(“Password”, with: “password”)
    |> click(“Register”)

    assert has_text?(session, “Welcome Grace Hopper”)
  end
end
```

## Asynchronous pages

It can be difficult to test pages that load data asynchronously or rely on javascript rendering. You may try to interact with an element that isn’t visible on the page yet. Wallaby’s finders try to help mitigate this problem by blocking until the element becomes visible. You can use this strategy by writing tests in this way:

```elixir
session
|> click(“Some Async Button”)
|> find(“.async-result”)
```

## Simultaneous browsers

Because Wallaby supports running tests concurrently its possible to simulate multiple browsers in a single test:

```elixir
defmodule YourApp.UsersChatTest do
  use YourApp.AcceptanceCase, async: true

  def checkout_session do
    {:ok, session} = Wallaby.start_session
    visit(session, Phoenix.Ecto.SQL.Sandbox.path_for(Allocations.Repo, self()))
    session
  end

  test “users can chat”, %{session: browser_one} do
    browser_one
    |> find(“.chat-messages”, count: 0)

    browser_two
    |> checkout_session
    |> find(“.chat-messages”, count: 0)

    browser_two
    |> find(“.new-message-form”)
    |> fill_in(“Chat Message”, with: “Hello There!”)
    |> click(“Send”)

    new_message =
      browser_one
      |> find(“.chat-messages”, count: 1)

    assert has_text?(new_message, “Hello There!”)
  end
end
```

We often use this technique to test our applications that rely on Phoenix Channels for communication.

## Get Started

For more examples and information on how to set up Wallaby in your application see the [hex docs](https://hexdocs.pm/wallaby/readme.html) and [the github repo](https://github.com/keathley/wallaby).

Give it a try and let us know what you think.

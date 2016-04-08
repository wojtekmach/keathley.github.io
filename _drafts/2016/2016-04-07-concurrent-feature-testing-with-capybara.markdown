---
layout: "post"
title: "concurrent-feature-testing-with-capybara"
date: "2016-04-07 22:05"
---

Feature tests are one of the best ways to ensure reliability and such.

For all our Rails applications we use a combination of Rspec and Capybara.

As we've been porting some of our internal applications to Phoenix, one of the things that we found we were missing was...

## Requirements for Wallaby

We had specific requirements for Wallaby.

1) Concurrent tests with Ecto 2.0

2) Flexible syntax ala. Capybara

3) Managing browsers (specifically phantomjs) for the user

4) Allow for simultaneous sessions in a test

## Writing our first test

```elixir
# Example test goes here
```

## Conclusion

So far we're thrilled with the results

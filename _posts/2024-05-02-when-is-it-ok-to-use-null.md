---
title: When is it ok to use null?
date: 2024-05-02 01:11:00 -0400
---

[Tony Hoare](https://en.wikipedia.org/wiki/Tony_Hoare) is famous for a lot of well-known contributions to computer science. One of those is the idea of null references, and he calls it his _billion dollar mistake_:

> I call it my billion-dollar mistake. It was the invention of the null reference in 1965. At that time, I was designing the first comprehensive type system for references in an object oriented language (ALGOL W). My goal was to ensure that all use of references should be absolutely safe, with checking performed automatically by the compiler. But I couldn't resist the temptation to put in a null reference, simply because it was so easy to implement. This has led to innumerable errors, vulnerabilities, and system crashes, which have probably caused a billion dollars of pain and damage in the last forty years

Today, most programming languages and many data languages support the concept,
so you can't really avoid it altogether.

If you're working in a language whose designers managed to avoid it, congrats!
You don't have to think about it, except to follow the idioms the language
designer(s) intended.

For example, in Rust::

```rust
let maybe_number: Option<i32> = Some(42);

// Check if the value is present and print it
if let Some(number) = maybe_number {
    println!("The number is: {}", number);
} else {
    println!("No number found.");
};
```

But what if you're an API designer, your language supports nullable fields,
and you're not sure where you should use them?

I think I can answer that with some guidelines:

## 🤮 Unacceptable uses

* To indicate "no values" for a multi-valued field or parameter.<br>
  The right way to represent no values is an empty collection (set, list, etc).
  I will die on this hill.
* To act as a placeholder for a field you think might be present someday.<br>
  If the field might be present someday, add it when the time comes.
  Don't burden your consumers with fields that are of zero use to them now.
* To convey that a field value could not be retrieved due to a system error.<br>
  If a system error occurs, throw an exception if you language allows it.
  If your language does not have such things, it might have an idiom
  that includes returning a wrapper object or a pair of values from functions
  that can result in an error. If one of those values in the idiom is nullable,
  then that's unavoidable and this doesn't apply. Also, consider switching
  languages to a sane one if that's in your control!
* To convey some meaning that isn't obvious or documented clearly in your API.<br>
  Please don't do this. You can avoid this in any language.
  Just put yourself in the shoes of your consumers.

## 👍 Acceptable uses

* To indicate "no value" for a single-valued (scalar) field.<br>
  Note: In certain security contexts, "no value" is indistinguishable from
  "you don't have access", and that's ok.
* To convey that a field or parameter is optional and/or should accept a default value
  when making a call or request.
* To convey some meaning that is extremely obvious or documented clearly and concisely.

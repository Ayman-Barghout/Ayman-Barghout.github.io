---
title: "Extension methods in Flutter"
date: 2019-12-18T10:33:41+09:00
description: "How to use extension methods to make your flutter code better"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tags:
- Beginner
- Dart
series:
-
categories:
- Dart
image: images/hello-world.png
---

{{< youtube id="24kyvlku7HI" >}}

## Introduction
While coding using Flutter you must have wrote that code to get the screen's height `MediaQuery.of(context).size.height` if you have wished it could be shorter like `context.height` or you have used a package that offers such code then this article about `extension methods` is for you.
## Why extension methods?
`Extension methods` were added to Dart 2.7 so you can add `methods` or `properties` to classes that you don't own the code for be it in Dart, Flutter or a third-part package.
Before `extensions` we had to use helper methods to have the same functionality, if we want to get the height from context we would write a function like this.
`getScreenHeight(context) {}`
The same goes for getting the space separated word count of a string
`getWordsCount(string) {}`
## The syntax
It's a lot easier writing `extension methods` all you get to write is `extension ExtensionName on ClassName {}` and you will get access to the class instance using `this` keyword

Example: 
```dart:example.dart
extension MyExtension on ClassName {
    ClassName get instance => this;
}
```
## Overview of our app
The app we are going to learn `extension methods` with can be found on [Github](https://github.com/Ayman-Barghout/flutter-guides/tree/main/extensions_guide) so you can clone or download it and try it out.
It's a fairly simple app that has a `TextField` with certain `maxLines` extracted into a varialbe of the same name, it is wrapped inside a `Column` that is inside a `SizedBox` with `height` equal to screen's height, below the `TextField` is a `Text` that shows how many space separated words are in the `TextField`

*Note* : Some code is replaced with `...` for brevity.

```dart:main.dart
  @override
  Widget build(BuildContext context) {
    final int maxLines = addTen(5);
    return ...
        child: SizedBox(
          height: getScreenHeight(context),
          child: Column(
          ...
            children: <Widget>[
              TextField(
                onChanged: (value) {
                  setState(() {
                    _text = value;
                  });
                },
                minLines: maxLines,
                maxLines: maxLines,
              ),
              Text(
                '${getWordsCount(_text)} words',
                style: Theme.of(context).textTheme.subtitle1,
              ),
            ],
          ),
        ),
      ...
  }
  ```

In the `helpers.dart` file we have three helper functions that use `Classes` we can't change their source code like `BuildContext`, `String` and `num`, and we will change those functions into `extension methods`.

```dart:helpers.dart
int getWordsCount(String string) {
  if (string.trim().isEmpty) {
    return 0;
  }
  return string.trim().split(RegExp('(s|\\W)+')).length;
}

double getScreenHeight(BuildContext context) {
  return MediaQuery.of(context).size.height;
}

num addTen(num number) {
  return number + 10;
}
```
## Converting to extension methods
We will add our `exxtension methods` into `extensions.dart` file and `helpers.dart` file will be right next to it to view changes done.
1. We will first apply extension on `BuildContext` using the aforementioned syntax ` Extension ContextExtensions on BuildContext` and import it from the `widget.dart` file in Flutter .. Then we will add a getter that returns `double` where it returns the `MediaQuery`'s `Size`'s `height` so we can use it in our code as `context.height`.
{{< codes extensions.dart helpers.dart >}}
  {{< code >}}
  ```dart:extensions.dart
import 'package:flutter/widgets.dart';
...
extension ContextExtensions on BuildContext{
double get height => MediaQuery.of(this).size.height;
}
  ```
  {{< /code >}}
  {{< code >}}
  ```dart:helpers.dart
import 'package:flutter/widgets.dart';
...
double getScreenHeight(BuildContext context) {
  return MediaQuery.of(context).size.height;
}  
```
  {{< /code >}}
{{< /codes >}}

2. As for the `getWordsCount` we will also use the same syntax, it can be either a `method` or `property` with a getter but I feel like a getter here would be more fitting.

{{< codes extensions helpers >}}
  {{< code >}}
  ```dart:extensions.dart
import 'package:flutter/widgets.dart';
...
extension StringExtensions on String {
    int get wordsCount => this.trim().isEmpty ? 0 : string.trim().split(RegExp('(s|\\W)+')).length;
}
  ```
  {{< /code >}}
  {{< code >}}
  ```dart:helpers.dart
int getWordsCount(String string) {
  if (string.trim().isEmpty) {
    return 0;
  }
  return string.trim().split(RegExp('(s|\\W)+')).length;
}
```
  {{< /code >}}
{{< /codes >}}


3. Finally for the `addTen` `extension` on `num`, here it is obvious that it should be a method but the issue here is related to `inheritance` since both `double` and `int` inherit from `num` we can run into runtime errors as the compiler won't complain.
`type 'double' is not a subtype of type 'int'`
as we will be passing a `double` value to an `int` variable if we use `int maxLines = 5.0.addTen()`.
But `Generics` come to the rescue in such situation, we will refactor our code to return a generic type that `extends` `num` and now our compiler will tell us there is an issue with such assignment.

{{< codes extensions helpers >}}
  {{< code >}}
  ```dart:extensions.dart
import 'package:flutter/widgets.dart';
...
extension NumExtensions<T extends num> on T {
    T  addTen() => this + 10;
}
  ```
  {{< /code >}}
  {{< code >}}
  ```dart:helpers.dart
T addTen<T extends num>(T number) {
  return number + 10;
}
```
  {{< /code >}}
{{< /codes >}}
*Note*
We could have definitely made the method or extension return an `int` but this is only an example to see how to apple one code to two classes that have the same parent without repeating our code using generics `generics`

And after applying our extension methods code `main.dart` will look like this: 

```dart:main.dart
  @override
  Widget build(BuildContext context) {
    final int maxLines = 5.addTen();
    return ...
        child: SizedBox(
          height: context.height,
          child: Column(
          ...
            children: <Widget>[
              TextField(
                onChanged: (value) {
                  setState(() {
                    _text = value;
                  });
                },
                minLines: maxLines,
                maxLines: maxLines,
              ),
              Text(
                '${_text.wordsCount} words',
                style: Theme.of(context).textTheme.subtitle1,
              ),
            ],
          ),
        ),
      ...
  }
  ```

## Wrapping up and important notes
Our app was simple so this article can be as short as it can be, but it showed us how using `extensions` can make our code shorter, cleaner and more concise in place of helper methods.
We shouldn't make an extension for every simple use case though lest we change the simple interface that Flutter is famous for, but it can be helpful for repetitive code like `String` validation for example or getting screen size from `MediaQuery` for responsiveness.

Some important notes about `extension methods`:

1. Our variables have to be the same type that our `extension` is on, if they are dynamic it will throw a `runtime error`. 
i.e: `String` extensions have to be called on `String` variables.

{{< codes error works >}}
  {{< code >}}
  ```dart
dynamic s = 'Hello world';
print(s.wordsCount.toString()); // Runtime exception: NoSuchMethodError
  ```
  {{< /code >}}
  {{< code >}}
  ```dart
String s = 'Hello world';
print(s.wordsCount.toString());
```
  {{< /code >}}
{{< /codes >}}

2. We can use `extensions` on `operators` as well such as `-` and `&` not only properties and methods, as such:
 
```dart:operator_example.dart
extension ListExtensions<T> on List<T> {
  List<T> operator -() => reversed.toList();
}
```
3. We can't `override` the original `class`'s properties and methods, `extension methods` is not the same as inheritance, it is only used to add functionality.

```dart:override_error.dart
extension StringExtensions on String{
  @override
  String toString () => this; // Extensions can't declare members with the same name as a member declared by 'Object'
}
```
4. If we have mutiple `extensions` with the same name we can either `hide` the one we don't need or use extensions like `Wrapper class` if we need to use both of them in the same file.

{{< codes hide wrapper >}}
  {{< code >}}
  ```dart
// Defines the String extension method parseInt().
import 'string_apis.dart';

// Also defines parseInt(), but hiding NumberParsing2
// hides that extension method.
import 'string_apis_2.dart' hide NumberParsing2;

// ···
// Uses the parseInt() defined in 'string_apis.dart'.
print('42'.parseInt());
  ```
  {{< /code >}}
  {{< code >}}
  ```dart
// Both libraries define extensions on String that contain parseInt(),
// and the extensions have different names.
import 'string_apis.dart'; // Contains NumberParsing extension.
import 'string_apis_2.dart'; // Contains NumberParsing2 extension.

// ···
// print('42'.parseInt()); // Doesn't work.
print(NumberParsing('42').parseInt());
print(NumberParsing2('42').parseInt());
```
  {{< /code >}}
{{< /codes >}}
I hope this article was insightful and taught you the power and limitations of `Dart extension methods` in Flutter.
Feel free to share the article if you want, leave feedback in the comments or through contacting me, ask me anything or suggest a topic for another article.

Thanks!
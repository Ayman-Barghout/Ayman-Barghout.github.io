---
title: "فلاتر و الـ extension methods"
date: 2019-12-18T10:33:41+09:00
description: "Extension methods فلاتر و كيفية تسهيل الكود بإستخدام"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tags:
- مبتدئ
- دارت
series:
-
categories:
- دارت
image: images/hello-world.png
---

## مقدمة 
اكيد وانت بتكتب كود فلاتر و بتجيب طول الشاشة قابلت السطر ده `MediaQuery.of(context).size.height` لو اتمنيت يكون أبسط و أوضح زي `context.height` أو استخدمت باكدج بتعمل كده و عايز تعرف ازاي فالحل عن طريق extension methods و ده موضوعنا النهاردة.
## ليه الـ Extension methods
الـ Extension methods اتضافت لدارت نسخة 2.7 عشان تقدر تضيف بيها خصائص و أداء مختلف على الـClasses اللي ملكش تحكم عليها سواء جاية من دارت، فلاتر أو مكتبات  تانية بتستخدمها في مشروعك
قبل ال extension methods كان معظمنا بيستخدم helper methods عشان نسهل الكود أو نضيف خصائص جديدة و هي عبارة عن Functions عادية زي دي عشان نجيب طول الشاشة عن طريق الكونتيكست. 
`{} getScreenHeight(context)`
أو دي عشان نعرف السترينج فيه كام كلمة مفصولة بمسافة
`{} getWordsCount(string)`
## طريقة كتابتها
لكن الموضوع أسهل بكتير مع الـ extension methods ، كل اللي علينا نكتب `extension` وبعدين أسم الإكستينشن و بعدين  `on` وبعدين اسم الكلاس و داخل الcurly brackets نضيف اسم الميثود أو ال property و بيبقى عندنا اكسيس على ال instance أو الاوبجكت بإستخدام `this`.
كمثال: 
```dart:example.dart
extension MyExtension on ClassName {
    ClassName get instance => this;
}
```
## نظرة عامة عن التطبيق
التطبيق اللي هنجرب عليه الـ extensions method و موجود على [جيت هاب](https://github.com/Ayman-Barghout/flutter-guides/tree/main/extensions_guide) عشان تحمله و تمشي مع التوتوريال, هيكون تطبيق بسيط فيه `TextField` له عدد معين من الـ `maxLines` محطوطة بمتغير, و `Column` جوا `SizedBox` واخد الطول الكامل بتاع السكرين و `Text` تحت في الـ `Column` بتظهر عدد الكلمات اللي في الـ `TextField`.

*ملحوظة*:  بعض الكود مستبدل بـ `...` للإختصار

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

و في فايل `helpers.dart` معرفين الـ helper methods و كلها على Classes ملناش Access عليها زي `String`, `num`, `BuildContext` .. و دي اللي هنحولها لـ Extension methods .. 
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
## إستخدام الـ Extension methods
هنفتح ملف `extensions.dart`  و هيكون مقابل له `helpers.dart` عشان تقدر تشوف الكود قبل التغيير
1. هننفذ الـ extension methods بإستخدام الـ syntax اللي ذكرناه فوق ` Extension ContextExtensions on BuildContext` و نعمله `import` من الـ `widgets.dart` فايل بتاع `flutter` .. بعدين جواه هنقول double get height كإننا بنكتب getter في كلاس عادي, و بيبقى عندنا access عالـ instance بتاعة الـ `BuildContext` بإستخدام `this`, فهنكتب `MediaQuery.of(this).size.height` و ده يخلينا نقدر نستخدمه عالكونتكست علطول في الكود بتاعنا.
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

2. بالنسبة للـ `getWordsCount` فا برضه هنكتب نفس الـ Syntax و ممكن يبقى `method` أو `property` بـ getter .. الأتنين ينفعوا و مفيهومش مشاكل أنا أفضل الـ `get` لإنها أقرب للـ `property` عن أنها `method`.
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


3. و أخيراً الـ `addTen` `num` `extension` و ده هيكون ميثود لإن كـ `property` مش هيبقى واضح لكن هنا في مشكلة لازم ناخد بالنا منها و هي لها علاقة بالـ `inheritance` .. للأسف أحنا بنرجع `num` و ده ممكن يعمل مشاكل أثناء الـ runtime لإن الـ `int` ممكن يقبل قيمة `double` و الـ compiler مش هيشتكي لإن الأتنين فعلاً بيعملوا `extend` لـ `num` .. لكن لما نعمل run للكود هتظهر معانا مشكلة لإن 
`'type 'double' is not a subtype of type 'int`
لو كتبنا كود زي كده `()int maxLines = 5.0.addTen` في الحالة دي الـ `compiler` مش هيعترض و لكن هنقابل المشكلة أثناء تشغيل التطبيق.
لحل الموضوع ده هنستخدم الـ `generics` .. هنستخدمها الأول عالـ helper method و بحيث أننا نعمل `return` لـ Generic Type بيعمل `extend` لـ `num` .. زي `int` او `double` لكن بيرجع النوع ده بس, عشان وقتها الـ compiler هو اللي هيطلع error إن في مشكلة هنا و مش هنقابلها واحنا بنشغل التطبيق بتاعنا.
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
*ملحوظة*
كان ممكن أكيد نخليه يرجع `int` بس بدل `num` لكن ده كمثال لو محتاجين نفس الـ `extension` لكلاسين inherited من كلاس واحد زي الـ `double` والـ `int`. بدل ما نكتب الكود مرتين نكتبه مرة واحدة على الـ parent class بإستخدام الـ `generics` منعاً للـ `runtime errors`. 

و الكود بتاع التطبيق في النهاية هيكون كالآتي:

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

## الخاتمة و ملاحظات مهمة
بكده نكون خلصنا المثال بتاعنا بالرغم من إنه بسيط عشان المقال يكون مش طويل, لكن الـ `extensions` مهمة جداً و مستخدمة في باكدجات كتير لإنها بتخلي الـ code أنضف و بتخلينا منعتمدش على Utils أو Helpers `class`, بس يفضل تستخدم في الحاجات المتكررة مش كل `Function` على `String` نعملها في `extension` و لكن لو متكررة في الكود بتاعنا زي `Validation` مثلاً, ده بنحتاجه في معظم المشاريع .. و كذلك الـ `mediaQuery` بنحتاجها في معظم المشاريع عشان الـ responsiveness .. و في باكدجات مستخدماها أكتر من اللازم بحيث أن لو حد جديد على Flutter هيلاقي `interface` كبيرة لكلاس زي الـ double أو الـ context و الموضوع ده هيكون مرهق فميفضلش في رأيي.
و في نقط مهمة بالنسبة للـ `extension methods` مهم ذكرها و لو إن إستخدامها قليل:

1. لو عندنا `extension` على `String` مثلاً لازم يكون الـ Variable بتاعنا من النوع ده لإنه لو `dynamic` هيدينا `runtime error`. 
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
2. ممكن نعمل `extension methods` عالـ `operators` زي `-` و `&` و خلافه عشان نضيف functionality معينة كمثال.
```dart:operator_example.dart
extension ListExtensions<T> on List<T> {
  List<T> operator -() => reversed.toList();
}
```
3. متقدرش تعمل `override` لأي `property` أو `method` للـ `class` من خلال الـ `extension methods` على عكس الـ `inheritance` لإن الفكرة منها مش تغيير وظيفة الكلاس و لكن إضافة خصائص له.
```dart:override_error.dart
extension StringExtensions on String{
  @override
  int codeUnitAt (int index) => ....; // The method doesn't override an inherited method
}
```
4. لو عندنا كذا `extension` بينفذوا نفس الـ `Method` ممكن نعمل `hide` للـ Extension اللي مش محتاجينه, أو نستخدم الـ Extension كإنه `Wrapper class` بنمرر فيه الـ object اللي عايزين نستخدم عليه الـ `method` زي المثال ده. 
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
بكده نكون عرفنا أكتر عن الـ `extension methods` في دارت و إزاي ممكن نستفاد منها في فلاتر وتسهل علينا الكود بتاعنا, اتمنى يكون المقال فادكم  لو في أي feedback او سؤال أو حاجة عايزها تتشرح أكتبها في كومنت و شاركه مع غيرك للإفادة ..

!شكراً
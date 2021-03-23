# Creating Responsive Apps With Flutter

- [Creating Responsive Apps With Flutter](#creating-responsive-apps-with-flutter)
  - [1.1. Box Contraints](#11-box-contraints)
  - [1.2. Responsive Apps On Various Mobile Screen Sizes](#12-responsive-apps-on-various-mobile-screen-sizes)
    - [1.2.1. Columns](#121-columns)
    - [1.2.2. Handling Orientation](#122-handling-orientation)
  - [1.3. Handling Wider Screens](#13-handling-wider-screens)
    - [1.3.1. MediaQuery](#131-mediaquery)
    - [1.3.2. Layout Builder](#132-layout-builder)

## 1.1. Box Contraints

Flutter framework is constraint based framework, i.e, a widget bounds is constrained by the parent widget. If for example, you container has a dimension of height 900px and width 500px but when you run the code on your simulator, you see that its actually smaller than what you have intended to, this is due to the box constraints coming in from the parent widget which passes contraints of maybe 800px 400px. If the parent of the container is a scaffold, then the box constraint coming in from the scaffold to the container would be the screen dimensions. Box contraints are passed automatically by the parent.

<details>
  <summary>Example 1</summary>

<p>

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

class Example1 extends StatelessWidget {
  const Example1({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    //Scaffold passes down screen dimensions as constraints  
    return Scaffold(
      appBar: AppBar(
        title: Text('Example 1'),
      ),
      body: Container(
        color: Colors.green,
        width: double.infinity,
        height: double.infinity,
      ),
    );
  }
}
```
</p>
</details>

<details>
  <summary>Example 1</summary>

<p>

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

class Example2 extends StatelessWidget {
  const Example1({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    //Scaffold passes down screen dimensions as constraints
    return Scaffold(
      appBar: AppBar(
        title: Text('Example 1'),
      ),
      body: Container(
        width: 200,
        height: 200,
        child: Container(
          color: Colors.green,
          width: double.infinity,
          height: double.infinity,
        ),
      ),
    );
  }
}
```
</p>
</details>

<br/>

*A widget can decide its own size only within the constraints given to it by its parent. This means a widget usually can’t have any size it wants.*

## 1.2. Responsive Apps On Various Mobile Screen Sizes
When creating an app across various mobile screen, we want to make sure they are responsive and expand or collapse based on the screen dimensions. So we will look at how to achieve that with Columns and Rows.

### 1.2.1. Columns

So, Columns, unlike Scaffold or Container don't pass constraint along the main axis and will build the height from the children. This is because Columns children are dynamic.

<details>
  <summary>Example 1</summary>

<p>

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

class ColumnExample1 extends StatelessWidget {
  const ColumnExample1({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Example 1'),
      ),
      //if the containers height are infite here, the app will //crash
      body: Column(
        children: [
          Container(
            color: Colors.green,
            height: 100,
          ),
          Container(
            color: Colors.blue,
            height: 300,
          ),
          Container(
            color: Colors.orange,
            height: 500,
          ),
        ],
      ),
    );
  }
}
```
</p>
</details>

As you saw with the example, the overflow error generally happens with Columns and Rows because they don't pass constraint to the children along the main axis.

So how do we solve the problem of widgets going out of bounds? We use a special widget called **Expanded** or **Flex**.

**Expanded** will fill the remaining space with the child widget, which in our case is the orange widget. Note that when using Expanded, it will completely ignore the child's height.

<details>
  <summary>Example 1</summary>

<p>

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

class ColumnExampleResponsive extends StatelessWidget {
  const ColumnExampleResponsive({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Example 1'),
      ),
      body: Column(
        children: [
          Container(
            color: Colors.green,
            height: 100,
          ),
          Container(
            color: Colors.blue,
            height: 300,
          ),
          Expanded(
            child: Container(
              color: Colors.orange,
              height: 500,
            ),
          ),
        ],
      ),
    );
  }
}
```
</p>
</details>

Additonal Notes:
- **Flexible** is similar to Expanded but with more options on the Columns on how children should take up space.
- Rows is pretty similar to Column, except that the main axis is controlled by width.

### 1.2.2. Handling Orientation

- Use OrientationBuilder to know what the current orientation and return the respective widget
- Disable particular orientation

## 1.3. Handling Wider Screens

So for handling different mobile device screen, using Columns/Rows with Expanded is sufficient but to 
expand the repsonsiveness to wider screens like desktop apps and desktop browsers, we have to rely on 
either MediaQuery or LayoutBuilder.

### 1.3.1. MediaQuery

Using MediaQuery, you can get information like screen dimensions, accessibilty information which you can use to handle various screen sizes.

<details>
  <summary>Media Query Example</summary>

<p>

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

import '../responsive_util.dart';

class MediaQueryResponsive extends StatelessWidget {
  const MediaQueryResponsive({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: ResponsiveUtil.isWideScreen(context)
            ? null
            : AppBar(
                title: Text('MediaQuery Responsive'),
              ),
        body: GridView.count(
          crossAxisCount: MediaQuery.of(context).size.width < 500 ? 2 : 4,
          children: List.generate(100, (index) {
            return Container(
              child: Center(
                child: Image.network(
                  'https://picsum.photos/id/${index + 100}/${MediaQuery.of(context).size.width < 500 ? (MediaQuery.of(context).size.width / 2).round() : (MediaQuery.of(context).size.width / 4).round()}',
                  loadingBuilder: (BuildContext context, Widget child,
                      ImageChunkEvent loadingProgress) {
                    if (loadingProgress == null) return child;
                    return Center(
                      child: CircularProgressIndicator(),
                    );
                  },
                ),
              ),
            );
          }),
        ));
  }
}

```
</p>
</details>

### 1.3.2. Layout Builder

Layout builder is similar to MediaQuery when it comes to screen sizes but it can used with any widget 
and get the parent constraints.

<details>
  <summary>Media Query Example</summary>

<p>

```dart
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

import '../responsive_util.dart';

class LayoutBuilderResponsive extends StatelessWidget {
  const LayoutBuilderResponsive({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: ResponsiveUtil.isWideScreen(context)
          ? null
          : AppBar(
              title: Text('LayoutBuilder Responsive'),
            ),
      body: LayoutBuilder(
        builder: (context, constraints) {
          return GridView.count(
            crossAxisCount: constraints.maxWidth < 500 ? 2 : 4,
            children: List.generate(100, (index) {
              return Container(
                child: Center(
                  child: Image.network(
                    'https://picsum.photos/id/${index + 400}/${constraints.maxWidth < 500 ? (constraints.maxWidth / 2).round() : (constraints.maxWidth / 4).round()}',
                    loadingBuilder: (BuildContext context, Widget child,
                        ImageChunkEvent loadingProgress) {
                      if (loadingProgress == null) return child;
                      return Center(
                        child: CircularProgressIndicator(),
                      );
                    },
                  ),
                ),
              );
            }),
          );
        },
      ),
    );
  }
}
```
</p>
</details>
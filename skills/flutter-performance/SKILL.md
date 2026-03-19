---
name: flutter-performance
description: "Flutter performance optimization: diagnose jank, reduce rebuild costs, optimize raster thread, profile with DevTools, and meet 16ms frame budgets. Use when debugging slow frames, excessive widget rebuilds, expensive rendering operations, or profiling app performance."
---

# Flutter Performance Optimization

> Adapted from [flutter/skills](https://github.com/flutter/skills) (BSD-3-Clause).
> Original content derived from [docs.flutter.dev/perf](https://docs.flutter.dev/perf).

## Goal
Analyze and optimize Flutter app performance — identify jank, excessive rebuilds, and expensive rendering operations. Ensure frames render within the 16ms budget.

## Decision Logic

1. **Need a performance baseline?**
   - Yes: implement an integration test with `traceAction` and `TimelineSummary`.
   - No: proceed to step 2.
2. **Web app?**
   - Yes: enable `debugProfileBuildsEnabled` and use Chrome DevTools Performance panel.
   - No: run on a physical device in `--profile` mode, launch Flutter DevTools.
3. **Which thread shows jank (red bars > 16ms)?**
   - **UI Thread:** optimize `build()` methods, localize `setState()`, use `const` constructors, replace string concatenation with `StringBuffer`.
   - **Raster Thread:** minimize `saveLayer()`, `Opacity`, `Clip`, `ImageFilter`. Use `RepaintBoundary` for complex subtrees.
   - **Both:** start with UI thread — expensive Dart code cascades into expensive rendering.

## Instructions

### 1. Establish a Performance Baseline

Create an integration test that records a performance timeline.

**ASK THE USER:** "Do you want to run a baseline integration test to capture timeline metrics before optimizing?"

*test_driver/perf_driver.dart*:
```dart
import 'package:flutter_driver/flutter_driver.dart' as driver;
import 'package:integration_test/integration_test_driver.dart';

Future<void> main() {
  return integrationDriver(
    responseDataCallback: (data) async {
      if (data != null) {
        final timeline = driver.Timeline.fromJson(
          data['scrolling_timeline'] as Map<String, dynamic>,
        );
        final summary = driver.TimelineSummary.summarize(timeline);
        await summary.writeTimelineToFile(
          'scrolling_timeline',
          pretty: true,
          includeSummary: true,
        );
      }
    },
  );
}
```

*integration_test/scrolling_test.dart*:
```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_test/integration_test.dart';
import 'package:your_package/main.dart';

void main() {
  final binding = IntegrationTestWidgetsFlutterBinding.ensureInitialized();

  testWidgets('Performance profiling test', (tester) async {
    await tester.pumpWidget(const MyApp());
    final listFinder = find.byType(Scrollable);
    final itemFinder = find.byKey(const ValueKey('target_item'));

    await binding.traceAction(() async {
      await tester.scrollUntilVisible(
        itemFinder,
        500.0,
        scrollable: listFinder,
      );
    }, reportKey: 'scrolling_timeline');
  });
}
```

Run: `flutter drive --driver=test_driver/perf_driver.dart --target=integration_test/scrolling_test.dart --profile --no-dds`

### 2. Optimize UI Thread (Build Costs)

If UI thread exceeds 8ms per frame:

- **Localize state:** move `setState` as low in the tree as possible.
- **Use `const`:** apply `const` constructors to short-circuit rebuild traversals.
- **StringBuffer:** replace `+` in loops with `StringBuffer`.

```dart
// BAD: rebuilds entire tree
setState(() { _counter++; });

// GOOD: encapsulated state in a leaf widget
class CounterWidget extends StatefulWidget { ... }
```

```dart
// GOOD: efficient string building
final buffer = StringBuffer();
for (int i = 0; i < 1000; i++) {
  buffer.write('Item $i');
}
final result = buffer.toString();
```

### 3. Optimize Raster Thread (Rendering Costs)

If raster thread exceeds 8ms per frame:

- Replace `Opacity` widgets with semitransparent colors where possible.
- Replace `Opacity` in animations with `AnimatedOpacity` or `FadeInImage`.
- Avoid `Clip.antiAliasWithSaveLayer`. Use `borderRadius` on containers instead.

```dart
// BAD
Opacity(opacity: 0.5, child: Container(color: Colors.red))

// GOOD
Container(color: Colors.red.withOpacity(0.5))
```

### 4. Fix Layout and Intrinsic Passes

- Use lazy builders (`ListView.builder`, `GridView.builder`) for long lists.
- Avoid `shrinkWrap: true` on scrollables unless absolutely necessary.

### 5. Handle Framework Breaking Changes

`LayoutBuilder` and `OverlayEntry` no longer rebuild implicitly. Wrap state modifications in explicit `setState`:

```dart
final newLabel = await Navigator.pushNamed(context, '/bar');
setState(() {
  buttonLabel = newLabel;
});
```

### 6. Web-Specific Profiling

Inject timeline events into Chrome DevTools:

```dart
import 'package:flutter/widgets.dart';
import 'package:flutter/rendering.dart';

void main() {
  debugProfileBuildsEnabled = true;
  debugProfileBuildsEnabledUserWidgets = true;
  debugProfileLayoutsEnabled = true;
  debugProfilePaintsEnabled = true;
  runApp(const MyApp());
}
```

## Constraints

- **NEVER** profile in Debug mode. Always use `--profile` on a physical device.
- **NEVER** override `operator ==` on `Widget` — causes O(N^2) behavior. Use `const` caching.
- **NEVER** put non-animated subtrees inside `AnimatedBuilder`. Pass static parts as `child`.
- **DO NOT** use concrete `List` constructors (`Column`, `ListView`) for off-screen children. Use `.builder` for lazy loading.
- **DO NOT** use `saveLayer()` unless absolutely necessary.

## Resources

- https://docs.flutter.dev/perf
- https://docs.flutter.dev/perf/best-practices
- https://docs.flutter.dev/perf/rendering-performance
- https://docs.flutter.dev/perf/ui-performance
- https://docs.flutter.dev/perf/web-performance
- https://docs.flutter.dev/perf/metrics
- https://docs.flutter.dev/perf/faq
- https://docs.flutter.dev/tools/devtools/performance
- https://docs.flutter.dev/tools/devtools/cpu-profiler
- https://docs.flutter.dev/cookbook/testing/integration/profiling

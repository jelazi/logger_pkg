# logger_pkg

A small logging package for Dart and Flutter. Levelled console output with call-site
information, optional rotating log files on IO platforms, and a no-op file layer on the web —
so the same code compiles for Flutter web without conditional imports in your app.

No dependencies beyond the Dart SDK.

## Install

```yaml
dependencies:
  logger_pkg:
    git:
      url: https://github.com/jelazi/logger_pkg.git
```

## Getting started

The package exposes a `logger` top-level variable. Assign it once, early in `main()`:

```dart
import 'package:logger_pkg/logger_pkg.dart';

void main() {
  logger = MyLogger(
    level: 2,
    debug: true,
    saveToFile: true,
    pathDirectory: '/path/to/logs',
  );

  runApp(const MyApp());
}
```

## Usage

```dart
logger.e('failed to open the file');   // error
logger.w('retrying');                   // warning
logger.i('connected');                  // info
logger.v('payload: $body');             // verbose
logger.d('only while debugging');       // debug
```

### Levels

`level` sets how much gets through. Errors are always logged.

| `level` | Emits |
|---|---|
| `0` | error |
| `1` | error, warning |
| `2` | error, warning, info |
| `3` | error, warning, info, verbose |

`d()` is independent of the level and is controlled by the `debug` flag.

### Suppressing repeats

Pass `repetitionCount` to stop a message that fires in a loop from filling the log:

```dart
logger.w('reconnecting', repetitionCount: 5);
```

The message is written once and then skipped until it has been seen that many times.

## Constructor

| Parameter | Default | Meaning |
|---|---|---|
| `level` | `3` | verbosity, see the table above |
| `debug` | `true` | whether `d()` emits anything |
| `saveToFile` | `true` | write to log files; forced to `false` on the web |
| `isDebugConsole` | `false` | route output through `dart:developer` `log()` instead of `print` |
| `pathDirectory` | `''` | directory for the log files |
| `numberRepitions` | `0` | default repeat threshold |

Everything can be changed afterwards with `changeLevel`, `changeDebug`, `changeSaveToFile`,
`changeIsDebugConsole` and `changePathDirectory`.

## Log files

When `saveToFile` is on, entries are appended to a file named after the current date. Files
rotate on size, and a new file is started when the date changes. `getAllDatesWithLogs()` returns
the dates that have logs, which is enough to build a log viewer in the host app.

Call `dispose()` on shutdown to flush and close the sink.

## Web

On the web `saveToFile` is forced off and every file operation is a no-op, so `logger.i(...)`
behaves the same and nothing throws. The split lives in `logger_io.dart` and `logger_web.dart`,
selected by a conditional import.

## License

Released under the [MIT License](LICENSE).

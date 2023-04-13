# RiverpodCourses
https://www.youtube.com/watch?v=vtGCteFYs4M

## Example 1

```dart
void main() {
  runApp(
    const ProviderScope(
      child: App(),
    ),
  );
}

class App extends StatelessWidget {
  const App({
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      darkTheme: ThemeData.dark(),
      themeMode: ThemeMode.dark,
      debugShowCheckedModeBanner: false,
      home: const HomePage(),
    );
  }
}

final currentDate = Provider<DateTime>(
  (ref) => DateTime.now(),
);

class HomePage extends ConsumerWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final date = ref.watch(currentDate);
    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Hooks Riverpod',
        ),
      ),
      body: Center(
        child: Text(
          date.toIso8601String(),
          style: Theme.of(context).textTheme.headlineMedium,
        ),
      ),
    );
  }
}

```

## [Example 2](https://github.com/YamamotoDesu/RiverpodCourses/commit/dc601cd96c1a8f47d90900516e60b3a070c41853)

<img width="300" alt="スクリーンショット 2023-04-13 9 58 49" src="https://user-images.githubusercontent.com/47273077/231618531-30ad0872-ea26-474b-9e2b-fe5e37467ff8.gif">

```dart
mport 'package:flutter/material.dart';
import 'package:hooks_riverpod/hooks_riverpod.dart';

void main() {
  runApp(
    const ProviderScope(
      child: App(),
    ),
  );
}

class App extends StatelessWidget {
  const App({
    Key? key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      darkTheme: ThemeData.dark(),
      themeMode: ThemeMode.dark,
      debugShowCheckedModeBanner: false,
      home: const HomePage(),
    );
  }
}

extension OptionalInfixAddition<T extends num> on T? {
  T? operator +(T? other) {
    final shadow = this;
    if (shadow != null) {
      return shadow + (other ?? 0) as T;
    } else {
      return null;
    }
  }
}

void testIt() {
  final int? int1 = 1;
  final int? int2 = null;
  final result = int1 + int2;
  print(result);
}

// void testIt() {
//   final int? int1 = 1;
//   final int int2 = 2;
//   final result = (int1 ?? 0) + int2;
// }

class Counter extends StateNotifier<int?> {
  Counter() : super(null);
  void increment() => state = state == null ? 1 : state + 1;
}

final counterProvider = StateNotifierProvider<Counter, int?>(
  (ref) => Counter(),
);

class HomePage extends ConsumerWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return Scaffold(
      appBar: AppBar(title: Consumer(
        builder: (context, ref, child) {
          final count = ref.watch(counterProvider);
          final text = count == null ? 'Press the button' : count.toString();
          return Text(text);
        },
      )),
      body: Column(crossAxisAlignment: CrossAxisAlignment.stretch, children: [
        TextButton(
            onPressed: ref.read(counterProvider.notifier).increment,
            child: const Text('Increment counter'))
      ]),
    );
  }
}
```

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
import 'package:flutter/material.dart';
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

## [Example 3](https://github.com/YamamotoDesu/RiverpodCourses/commit/2e84f43943c9b35c808cde729cd347b89f9a1a25)

<img width="300" alt="スクリーンショット 2023-04-13 9 58 49" src="https://user-images.githubusercontent.com/47273077/231669224-54b6be81-2914-4a90-ac19-40cfb21b3867.gif">

```dart
import 'package:flutter/material.dart';
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

typedef WeatherEmoji = String;

Future<WeatherEmoji> getWeather(City city) {
  return Future.delayed(
    const Duration(seconds: 1),
    () => {
      City.stockholm: '☀️',
      City.paris: '🌧️',
      City.tokyo: '🌬️',
    }[city]!,
  );
}

enum City {
  stockholm,
  paris,
  tokyo,
}

// final myProvider = Provider((_) => DateTime.now);

// UI writes this and reads from this
final currentCityProvider = StateProvider<City?>(
  (ref) => null,
);

const unknownWeatherEmoji = '🤷‍♂️';

// UI reads this
final weatherProvider = FutureProvider<WeatherEmoji>((ref) {
  final city = ref.watch(currentCityProvider);
  if (city != null) {
    return getWeather(city);
  } else {
    return unknownWeatherEmoji;
  }
});

class HomePage extends ConsumerWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final currentWeather = ref.watch(
      weatherProvider,
    );

    return Scaffold(
      appBar: AppBar(
        title: const Text('Weather'),
      ),
      body: Column(
        children: [
          currentWeather.when(
            data: (data) => Text(
              data,
              style: const TextStyle(fontSize: 40),
            ),
            error: (_, __) => const Text('Error 🥲'),
            loading: () => const Padding(
              padding: EdgeInsets.all(8.0),
              child: CircularProgressIndicator(),
            ),
          ),
          Expanded(
            child: ListView.builder(
              itemCount: City.values.length,
              itemBuilder: (context, index) {
                final city = City.values[index];
                final isSelected = city == ref.watch(currentCityProvider);
                return ListTile(
                  title: Text(
                    city.toString(),
                  ),
                  trailing: isSelected ? const Icon(Icons.check) : null,
                  onTap: () =>
                      ref.read(currentCityProvider.notifier).state = city,
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
```

## [Example 4](https://github.com/YamamotoDesu/RiverpodCourses/commit/feb6a0e6994b0419bc7206037da6fbb6a378c912)

<img width="300" alt="スクリーンショット 2023-04-13 9 58 49" src="https://user-images.githubusercontent.com/47273077/231951119-1a185914-4c20-40e6-bb47-a09e2d5390c5.gif">

```dart
import 'package:flutter/material.dart';
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

const names = [
  'Alice',
  'Bob',
  'Charlie',
  'David',
  'Eve',
  'Fred',
  'Ginny',
  'Harriet',
  'Ileana',
  'Joseph',
  'Kincaid',
  'Larry',
];

final tickerProvider = StreamProvider(
  (ref) => Stream.periodic(
    const Duration(
      seconds: 1,
    ),
    (i) => i + 1,
  ),
);

final namesProvider = StreamProvider(
  (ref) => ref.watch(tickerProvider.stream).map(
        (count) => names.getRange(
          0,
          count,
        ),
      ),
);

class HomePage extends ConsumerWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final names = ref.watch(namesProvider);
    return Scaffold(
      appBar: AppBar(
        title: const Text('StreamProvider'),
      ),
      body: names.when(
        data: (names) {
          return ListView.builder(
            itemCount: names.length,
            itemBuilder: (context, index) {
              return ListTile(
                title: Text(
                  names.elementAt(index),
                ),
              );
            },
          );
        },
        error: ((error, stackTrace) => const Text(
              'Reached the end of the list',
            )),
        loading: () => const Center(
          child: CircularProgressIndicator(),
        ),
      ),
    );
  }
}
```

## [Example 5](https://github.com/YamamotoDesu/RiverpodCourses/commit/90bade316ebda62544b0459c1b703b4c86a77b35)

<img width="300" alt="スクリーンショット 2023-04-13 9 58 49" src="https://user-images.githubusercontent.com/47273077/232018530-fea706df-12c9-43e7-9499-d6b5ca7064cf.gif">

```dart
// ignore_for_file: public_member_api_docs, sort_constructors_first
import 'dart:collection';

import 'package:flutter/material.dart';
import 'package:hooks_riverpod/hooks_riverpod.dart';
import 'package:uuid/uuid.dart';

void main() {
  runApp(
    const ProviderScope(
      child: App(),
    ),
  );
}

@immutable
class Person {
  final String name;
  final int age;
  final String uuid;

  Person({
    required this.name,
    required this.age,
    String? uuid,
  }) : uuid = uuid ?? const Uuid().v4();

  Person updated([
    String? name,
    int? age,
  ]) =>
      Person(
        name: name ?? this.name,
        age: age ?? this.age,
        uuid: uuid,
      );

  String get displayName => '$name ($age years old)';

  @override
  bool operator ==(covariant Person other) => uuid == other.uuid;

  @override
  int get hashCode => uuid.hashCode;

  @override
  String toString() => 'Person(name: $name, age: $age, uuid: $uuid)';
}

class DataModel extends ChangeNotifier {
  final List<Person> _people = [];

  int get count => _people.length;

  UnmodifiableListView<Person> get people => UnmodifiableListView(_people);

  void add(Person person) {
    _people.add(person);
    notifyListeners();
  }

  void removePerson(Person person) {
    _people.remove(person);
    notifyListeners();
  }

  void update(Person updatedPerson) {
    final index = _people.indexOf(updatedPerson);
    final oldPerson = _people[index];
    if (oldPerson.name != updatedPerson.name ||
        oldPerson.age != updatedPerson.age) {
      _people[index] = oldPerson.updated(updatedPerson.name, updatedPerson.age);
      notifyListeners();
    }
  }
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

final peopleProvider = ChangeNotifierProvider(
  (_) => DataModel(),
);

class HomePage extends ConsumerWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Home page'),
      ),
      body: Consumer(builder: (context, ref, child) {
        final dataModel = ref.watch(peopleProvider);
        return ListView.builder(
          itemCount: dataModel.count,
          itemBuilder: ((context, index) {
            final person = dataModel.people[index];
            return ListTile(
              title: GestureDetector(
                onTap: () async {
                  final updatedPerson = await createOrUpdatePersonDialog(
                    context,
                    person,
                  );
                  if (updatedPerson != null) {
                    dataModel.update(updatedPerson);
                  }
                },
                child: Text(
                  person.displayName,
                ),
              ),
            );
          }),
        );
      }),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          final person = await createOrUpdatePersonDialog(
            context,
          );
          if (person != null) {
            final dataModel = ref.read(peopleProvider);
            dataModel.add(person);
          }
        },
        child: const Icon(Icons.add),
      ),
    );
  }
}

final nameController = TextEditingController();
final ageController = TextEditingController();

Future<Person?> createOrUpdatePersonDialog(
  BuildContext context, [
  Person? existingPerson,
]) {
  String? name = existingPerson?.name;
  int? age = existingPerson?.age;

  nameController.text = name ?? '';
  ageController.text = age?.toString() ?? '';

  return showDialog<Person?>(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: const Text('Create a Person'),
          content: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              TextField(
                controller: nameController,
                decoration: const InputDecoration(
                  labelText: 'Enter name here...',
                ),
                onChanged: (value) => name = value,
              ),
              TextField(
                controller: ageController,
                decoration: const InputDecoration(
                  labelText: 'Enter age here...',
                ),
                onChanged: (value) => age = int.tryParse(value),
              ),
            ],
          ),
          actions: [
            TextButton(
              onPressed: () => Navigator.of(context).pop(),
              child: const Text('Cancel'),
            ),
            TextButton(
              onPressed: () {
                if (name != null && age != null) {
                  if (existingPerson != null) {
                    final newPerson = existingPerson.updated(
                      name,
                      age,
                    );
                    Navigator.of(context).pop(
                      newPerson,
                    );
                  } else {
                    Navigator.of(context).pop(
                      Person(
                        name: name!,
                        age: age!,
                      ),
                    );
                  }
                } else {
                  Navigator.of(context).pop();
                }
              },
              child: const Text('Save'),
            ),
          ],
        );
      });
}
```

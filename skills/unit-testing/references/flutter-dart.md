# Flutter / Dart Testing Reference

## Test DSL: describe / context

Dart only provides `group` for all grouping. Create aliases to distinguish test targets from conditions:

```dart
// test/helpers/test_dsl.dart
import 'package:flutter_test/flutter_test.dart';

void describe(String description, void Function() body) => group(description, body);
void context(String description, void Function() body) => group(description, body);
```

Import in every test file:

```dart
import '../../helpers/test_dsl.dart';
```

## Test Structure

```dart
import 'package:flutter_test/flutter_test.dart';
import '../../helpers/test_dsl.dart';

void main() {
  describe('ClassName', () {
    describe('methodName', () {
      context('when input is valid', () {
        test('returns expected value', () {
          // Arrange
          final sut = ClassName();

          // Act
          final result = sut.methodName(input);

          // Assert
          expect(result, equals(expected));
        });
      });

      context('when input is empty', () {
        test('returns null', () {
          final sut = ClassName();
          expect(sut.methodName(''), isNull);
        });
      });
    });
  });
}
```

## Naming

```dart
// Bad — condition in test name
test('returns empty list when no items match filter', () { ... });
test('throws FormatException for invalid date string', () { ... });

// Good — condition in context, test name is outcome only
context('when no items match filter', () {
  test('returns empty list', () { ... });
});
context('when date string is invalid', () {
  test('throws FormatException', () { ... });
});
```

## Common Matchers

```dart
// Equality
expect(value, equals(expected));
expect(value, isNot(equals(other)));

// Type
expect(value, isA<String>());
expect(value, isNull);
expect(value, isNotNull);

// Collections
expect(list, contains(item));
expect(list, hasLength(3));
expect(list, isEmpty);
expect(map, containsPair('key', 'value'));

// Errors
expect(() => doThing(), throwsA(isA<ArgumentError>()));
expect(() => doThing(), throwsFormatException);

// Numeric
expect(value, greaterThan(5));
expect(value, closeTo(3.14, 0.01));
```

## Mocking with Mockito

```dart
import 'package:mockito/annotations.dart';
import 'package:mockito/mockito.dart';

@GenerateNiceMocks([MockSpec<ApiClient>()])
void main() {
  late MockApiClient mockClient;

  setUp(() {
    mockClient = MockApiClient();
  });

  describe('DataService', () {
    context('when API returns data', () {
      test('returns parsed result', () async {
        when(mockClient.fetchData())
            .thenAnswer((_) async => Data(value: 42));

        final service = DataService(client: mockClient);
        final result = await service.getData();

        expect(result.value, equals(42));
        verify(mockClient.fetchData()).called(1);
      });
    });
  });
}
```

## Widget Testing

```dart
void main() {
  Widget createWidgetUnderTest({required String title}) {
    return ProviderScope(
      overrides: [ /* provider overrides */ ],
      child: MaterialApp(
        home: Scaffold(body: MyWidget(title: title)),
      ),
    );
  }

  describe('MyWidget', () {
    context('when title is provided', () {
      testWidgets('displays title text', (tester) async {
        await tester.pumpWidget(createWidgetUnderTest(title: 'Hello'));
        await tester.pumpAndSettle();

        expect(find.text('Hello'), findsOneWidget);
      });
    });

    context('when button is tapped', () {
      testWidgets('navigates to detail page', (tester) async {
        await tester.pumpWidget(createWidgetUnderTest(title: 'Hello'));
        await tester.tap(find.byType(ElevatedButton));
        await tester.pumpAndSettle();

        expect(find.byType(DetailPage), findsOneWidget);
      });
    });
  });
}
```

## Test Helpers

### Factory pattern

```dart
// test/factories/model_factory.dart
abstract class ModelFactory<T> {
  Faker get faker => Faker();
  T generateFake();
  List<T> generateFakeList({required int length}) {
    return List.generate(length, (i) => generateFake());
  }
}

// test/factories/user_factory.dart
class UserFactory extends ModelFactory<User> {
  @override
  User generateFake({
    int? id,
    String? name,
    String? email,
  }) {
    return User(
      id: id ?? faker.randomGenerator.integer(100000),
      name: name ?? faker.person.name(),
      email: email ?? faker.internet.email(),
    );
  }
}
```

### Stub notifier pattern

```dart
// test/mocks/my_notifier_mock.dart
class StubMyNotifier extends MyNotifier {
  StubMyNotifier(this._value);
  final MyState _value;

  @override
  FutureOr<MyState> build() => _value;
}
```

## setUp / tearDown

```dart
void main() {
  late Database db;

  setUp(() {
    db = Database.inMemory();
  });

  tearDown(() {
    db.close();
  });

  // setUpAll / tearDownAll for expensive one-time setup
  setUpAll(() async {
    await loadTestFixtures();
  });
}
```

## Async Testing

```dart
test('completes future with data', () async {
  final result = await fetchData();
  expect(result, isNotNull);
});

test('stream emits values in order', () {
  expect(
    myStream,
    emitsInOrder([1, 2, 3, emitsDone]),
  );
});
```

## Performance Tips

- Use `setUp` to share expensive setup across tests in a group
- Prefer unit tests over widget tests when testing logic
- Use `pumpWidget` only for UI behavior, not business logic
- Mock HTTP calls — never hit real APIs in tests
- Use `build_runner` to generate mocks ahead of time

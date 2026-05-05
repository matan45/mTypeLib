# mTypeLib

The standard library for the [mType](https://github.com/matan45/mType) programming language.

`mTypeLib` ships the core types, collections, streams, reflection, networking, math, the `mtest` testing framework, and engine bindings used by mType programs and `.mtproj` projects.

---

## Layout

```
lib/
├── core/        # core language types: primitives, collections, functional, reflect, stream, json, exceptions
├── engine/      # game-engine bindings: scene, entity, physics, input, audio, animation, UI, VFX
├── math/        # vectors, matrices, quaternions, RNG
├── mtest/       # unit-testing framework + lifecycle annotations
└── net/         # HTTP client, TCP sockets, JSON API, network exceptions
```

162 `.mt` modules in total.

---

## Modules

### `core/`

Foundational types every mType program needs.

| Folder | Contents |
|---|---|
| `primitives/` | `Int`, `Float`, `Bool`, `String`, `Box<T>` — value-class wrappers around the four primitives plus a generic single-slot container |
| `interfaces/` | `Comparable`, `List`, `Map`, `MapEntry`, `Set`, `Queue`, `Deque` |
| `collections/` | `ArrayList`, `LinkedList`, `HashMap`, `HashSet`, `Stack`, `ArrayQueue` |
| `iterators/` | `ListIterator`, `StackIterator`, `QueueIterator`, `LinkedListIterator`, `HashSetIterator`, `HashMap{Entry,Key,Value}Iterator`, `FlatMappingIterator`, `StreamFlatMappingIterator` |
| `functional/` | `Function`, `BiFunction`, `Consumer`, `Predicate`, `Comparator`, `BinaryOperator` |
| `stream/` | `Stream<T>`, `StreamImpl`, `Streams`, `StreamToIteratorAdapter` |
| `reflect/` | `Class`, `Method`, `Field`, `Constructor`, `Parameter`, `Annotation`, `AccessibleObject`, `Modifier`, `Library` |
| `exceptions/` | `Exception`, `RuntimeException`, `IllegalArgumentException`, `IndexOutOfBoundsException`, `NullPointerException`, `ClassNotFoundException` |
| `annotations/` | `Retention`, `Targets` (meta-annotations for user-defined annotations) |
| `json/` | `Json` (serialize / deserialize) |
| `utils/` | `SortUtils` |
| `internal/` | implementation helpers used by the rest of `core/` |
| (root) | `Iterable<T>`, `Iterator<T>`, `Collection<T>` |

### `math/`

Game-math types: `Vec2f`, `Vec3f`, `Vec4f`, `Matrix3f`, `Matrix4f`, `Quaternion`, `Random`.

### `net/`

HTTP and TCP networking, async-native backed.

- `Http` — static facade with `getAsync`, `postAsync`, `putAsync`, `deleteAsync` returning `Promise<HttpResponse>`, plus sync wrappers
- `HttpRequest`, `HttpResponse` — request/response value types
- `JsonApi` — typed JSON HTTP client
- `TcpSocket`, `TcpServer` — raw TCP
- `AsyncConsumer` — utility for streaming async results
- `exceptions/` — `NetworkException`, `ConnectionException`, `DnsException`, `HttpException`, `TimeoutException`

### `mtest/`

Unit-testing framework.

- `TestSuite`, `TestRunner`, `TestResult`, `TestSuiteResult`
- `Assertions`, `AssertionFailedException`, `ThrowingRunnable`, `ExceptionName`, `Mtest`
- `annotations/Test`, `annotations/Lifecycle` (`@BeforeAll`, `@AfterAll`, `@BeforeEach`, `@AfterEach`)

### `engine/`

Bindings for the host game engine — exposes scene, entity, physics, input, animation, audio, UI, and VFX subsystems as static `Class::method()` facades over native calls.

Highlights: `Entity`, `Scene`, `Camera`, `Physics`, `Navmesh`, `Audio`, `Input`, `InputAction`, `InputAxis`, `Animator`, `IK`, `FootIK`, `HandIK`, `UI`, `VFX`, `PostProcess`, `Weather`, `Atmosphere`, `Cloud`, `Ocean`, `DirectionalLight`, `PointLight`, `SpotLight`, `Decal`, `Destruction`, `Streaming`, `Save`, `Log`, `Timer`, `Coroutine`, `RenderTexture`, plus a full set of `I*Listener` event interfaces.

---

## Usage

In a `.mtproj`, add an import path that points at this repo's root, then import the modules you need:

```mtype
import * from "lib/core/collections/ArrayList.mt";
import * from "lib/core/primitives/Int.mt";
import * from "lib/core/stream/Streams.mt";
import * from "lib/math/Vec3f.mt";
import * from "lib/net/Http.mt";
import * from "lib/mtest/TestSuite.mt";
```

### Collections + Stream

```mtype
import * from "lib/core/collections/ArrayList.mt";
import * from "lib/core/primitives/Int.mt";

ArrayList<Int> numbers = new ArrayList<Int>();
numbers.add(new Int(1));
numbers.add(new Int(2));
numbers.add(new Int(3));

Int sum = numbers.stream()
    .filter(x -> x > 0)
    .map(x -> x * 10)
    .reduceWithIdentity(0, (a, b) -> a + b);
```

### HTTP

```mtype
import * from "lib/net/Http.mt";
import * from "lib/net/HttpResponse.mt";

function async fetch(): Promise<HttpResponse> {
    HttpResponse r = await Http::getAsync("https://example.com");
    return r;
}
```

### Reflection

```mtype
import * from "lib/core/reflect/Class.mt";
import * from "lib/core/reflect/Method.mt";

Class c = Class::forName("MyService");
Method m = c.getDeclaredMethod("ping", 0);
```

### `mtest` Test

```mtype
import * from "lib/mtest/TestSuite.mt";
import * from "lib/mtest/Assertions.mt";
import * from "lib/mtest/annotations/Test.mt";
import * from "lib/mtest/annotations/Lifecycle.mt";

class MathTests {
    @BeforeAll
    public static function setup(): void { }

    @Test
    public function addsTwoNumbers(): void {
        Assertions::assertEquals(4, 2 + 2);
    }

    @Test(expected = "IllegalArgumentException")
    public function rejectsBadInput(): void {
        throw new IllegalArgumentException("nope");
    }
}
```

### User-Defined Annotation

```mtype
import * from "lib/core/annotations/Retention.mt";
import * from "lib/core/annotations/Targets.mt";

@Retention(RUNTIME)
@Target([METHOD])
annotation Timeout {
    int ms = 5000;
}
```

---

## Compatibility

Targets the mType runtime at <https://github.com/matan45/mType>. Versioning tracks the mType language release line.

## License

MIT License.

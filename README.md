# Why C# Sucks
By a professional hater :)

### The middle-child dilemma

> C# suffers from **severe** identity crisis lol, it tries to be the answer to every programming paradigm but gets mogged by specialised tools every single time

- Need raw systems performance, low latency, or metal control? You pick **C++**. Fact. C++ gives you no-cost abstractions, RAII, deterministic destruction, and direct memory layout. C# tries to sell you `Span<T>`, `Memory<T>`, `ref struct`, and `unsafe` blocks which just end up feeling like low-level C++ shoehorned into a managed runtime and it's genuine slop. That alone stopped me from using it.
- Need a quick script done, any automation, or 10-line parser? You pick **Python**. Python gets tf out of your way. C# historically forces you into project files, namespaces, classes, and build targets which are just so, so unnecessary, holy. Even with top-level statements you're still dragging along `.csproj` files, `bin/obj` folders, and build outputs just to test an idea which just gets so annoying so fast. I don't see the appeal.

---

### Memory footprint & runtime overhead

- The .NET gc is high-throughput, but it legit demands a memory tax. If you care about low memory footprints—like running microservices with tight memory limits, embedded devices, or real-time systems, C# forces you to jump through hoops to avoid allocations. Of course there's ways around it but C++ just makes it way way easier, and if it's small enough to not need or have a memory footprint then there are so many other options. Maybe excusable there but hardly.
- NativeAOT has apparently improved, but most standard .NET applications carry hefty runtime baggage compared to a slim native binary or a small Go/Rust executable. Again, fixable, but jumping through hoops.
- Lamba captures, boxing/unboxing, auto-properties with backing fields, array slicing without `Span<T>`—the language constantly tries to sneak allocations past you unless you write ultra-defensive C#.

---

### Tacked-on null safety

If you're coming from Kotlin, C#'s NRTs feel like some static analysis slapped onto a non-null-safe runtime lol.

- In Kotlin: Null safety is enforced by the type system at the bytecode level. `String` and `String?` are strictly distinct types.
- In C#: `string?` is just a compiler hint of some kind. But then at runtime, `string?` and `string` are the exact same object? If reflection or unannotated legacy code hands you `null` inside a `string` variable, the runtime happily throws a `NullReferenceException` which makes no sense to me.
- The compiler gives you warnings instead of strict type errors by default, leading to `#nullable enable` annotations scattered across codebase configs like band-aids.

---

### Language bloat & feature creep

C# has added so many syntax features over 20+ years that there are now 5 different ways to write the exact same line of code. Don't believe me?

```csharp
// Traditional property
private string _name;
public string Name { get { return _name; } set { _name = value; } }

// Auto-property
public string Name { get; set; }

// 3. Init-only property
public string Name { get; init; }

// 4. Expression-bodied property
public string Name => _name;

// 5. Primary constructor property (C# 12)
public class Person(string name) { public string Name { get; } = name; }

```

Every major C# release adds more and more syntactical slop to fix verbosity introduced in previous versions. The result is a Frankenstein dialect where legacy codebases look like enterprise Java 8 and modern codebases look like obscure functional scripting. No overreaction.

---

### The "glorious" async/await ceremony

C# did pioneer `async/await`, but it left an absolutely horrible mess of edge cases behind:

- If you write library code, you have to spam `.ConfigureAwait(false)` on every single awaited task to avoid synchronization context deadlocks. Hooray!
- A method calling an async function must become async itself, which just turns call stacks into an all-or-nothing async hierarchy. I still don't understand this one lol.
- `Task` and `Task<T>` are reference types that allocate on the heap unless you use `ValueTask<T>`, which then introduces its _own_ list of usage caveats (like not being allowed to await it twice? Just odd).

---

### Build systems, XML, and tooling bloat

* Every time you build a C# project, enjoy nested directories full of `.dll`, `.pdb`, `.json`, and `.cache` files directly into your workspace. Yummy!
* `.csproj` files may be cleaner now than in the .NET Framework days, which, granted, I'm more used to from college, but MSBuild is still a complex, XML-driven build engine that is notoriously painful to customise compared to CMake or just simple script tooling.
* Managing project dependencies often requires wrapping literally everything in a top-level `.sln` file, which is just an artifact left over from VS's crappy legacy project structure.

---

### Enterprise culture & idioms

> Time for some good ol' enterprise over-engineering:

* What I like to call "**AbstractFactoryInterfaceManager Syndrome**". Endless layers of abstractions, `IServiceProvider` dependency injection magic, options patterns, and middleware pipelines for tasks that could be solved in 20 lines of plain code
* For some reason everything needs a class, an interface, a DTO, a mapper and an injected handler.

---

### Dishonorable mention

C# has genuine annoyances, but it is not the worst language in existence. **That title belongs to Swift.**

| Pain Point | C# | Swift |
| --- | --- | --- |
| **Ecosystem** | Cross-platform runtime works reasonably well. Not great though lol. | Apple-first class citizen; Windows/Linux support feels horrendous. |
| **Tooling** | Heavy, but stable, I guess. | Xcode crashes, indexing hangs, and sourcekit-lsp breaks routinely. |
| **Breaking Changes** | Obsessed with backwards compatibility at least! | Language and API stability churn historically broke codebases across major releases. |
| **Compilation** | `For what is is, fast C# compiler. | Swift compiler frequently hangs on complex type inference. |

_At least C# code compiles reliably and runs across operating systems without requiring a $2,000 Mac and Xcode updates._

---

## So, in conclusion,

C# is "fine" for building corporate APIs and line-of-business software, but much better options exist. If you appreciate:

- Kotlin's clean syntax and true type-system null safety,
-  C++'s much leaner memory usage and zero-cost performance,
-  Python's frictionless scripting,

...then C# can easily feel like a bureaucratic language that tries to do everything, carries 20 years of slop, and eats up far more RAM than necessary depending on the task.

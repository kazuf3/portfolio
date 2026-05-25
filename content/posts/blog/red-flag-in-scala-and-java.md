---
title: "Weaponizing the Compiler: Managing Tactical Debt with Java, Scala 3, and Emacs"
date: 2026-05-25T12:04:55-07:00
url: "content/posts/blog/red-flag-in-scala-and-java/"
categories: "Blog"
weight: 5
logical_breadcrumbs:
    - title: "Blog"
      url: "/categories/blog"
---
In *A Philosophy of Software Design*, John Ousterhout distinguishes between two
approaches to software development: **tactical programming** and **strategic
programming**.

* **Tactical programming** focuses on getting the next feature working as
  quickly as possible. It is short-sighted, leading to accumulated complexity
  and critical design flaws.
* **Strategic programming** focuses on great system design. It prioritizes
  long-term maintainability over immediate speed.

However, real-world development is messy. Sometimes, business constraints
*force* a tactical shortcut. The danger isn’t making a tactical concession; the
danger is forgetting you made it.

To bridge this gap safely, you can practice **Explicit Tactical Debt
Management**: intentionally taking a shortcut, wrapping it immediately in
failing or tight safety tests, and marking the implementation at the type-level
with a "Red Flag." This causes your compiler or your editor to persistently nag
you until you return to refactor strategically.

---

### The Type-Level Red Flag Taxonomy

Instead of untyped strings or closed enums, we define our red flags at the type level. This allows teams to create open-ended, modular, and refactor-safe categories of debt based on Ousterhout's principles.

```scala
// The base marker for a tactical shortcut
trait TacticalDebt

// Core Ousterhout-inspired design violations
trait ShallowAbstraction  extends TacticalDebt // High interface overhead, low internal depth
trait ConjoinedComplexity extends TacticalDebt // Two distinct concepts tangled in one module
trait LeakException       extends TacticalDebt // Forcing the consumer to handle internal failures
```

---

### 1. Java Implementation: Class-Based Compilation Flags

Java annotations cannot accept generic type parameters, but they can accept
Class objects bounded by our `TacticalDebt` marker type.

#### The Annotation and Processor
```java
package com.compiler.flags;

import java.lang.annotation.*;

@Retention(RetentionPolicy.SOURCE)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface RedFlag {
    Class<? extends TacticalDebt> aspect();
    String reminder();
}
```

```java
package com.compiler.flags;

import javax.annotation.processing.*;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.Element;
import javax.lang.model.element.TypeElement;
import javax.lang.model.type.MirroredTypeException;
import javax.tools.Diagnostic;
import java.util.Set;

@SupportedAnnotationTypes("com.compiler.flags.RedFlag")
@SupportedSourceVersion(SourceVersion.RELEASE_17)
public class RedFlagProcessor extends AbstractProcessor {
    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        for (Element element : roundEnv.getElementsAnnotatedWith(RedFlag.class)) {
            RedFlag flag = element.getAnnotation(RedFlag.class);
            String aspectName = getAspectClassName(flag);

            String message = String.format(
                "\n[TACTICAL DEBT ALERT] Type: %s\n[REMINDER] %s\n[LOCATION] %s",
                aspectName.substring(aspectName.lastIndexOf('.') + 1).toUpperCase(),
                flag.reminder(),
                element.getSimpleName()
            );

            processingEnv.getMessager().printMessage(Diagnostic.Kind.WARNING, message, element);
        }
        return true;
    }

    private String getAspectClassName(RedFlag flag) {
        try { return flag.aspect().getName(); }
        catch (MirroredTypeException mte) { return mte.getTypeMirror().toString(); }
    }
}
```

#### The Workflow: Red-Flagged Code with Guardrail Tests

You write your fast, messy implementation, tag it, and write a test to guarantee
its current behavior. When you return later to rewrite it strategically, your
test guarantees you don't break functionality.

```java
public class OrderService {
    @RedFlag(
        aspect = ShallowAbstraction.class,
        reminder = "Hacked a shallow wrapper over raw SQL. Rewrite using the repository layer later."
    )
    public void processOrderQuickly(String orderId) {
        // Quick, tactical implementation...
    }
}

// The Safety Net Test
public class OrderServiceTest {
    @Test
    public void testProcessOrderQuickly() {
        OrderService service = new OrderService();
        service.processOrderQuickly("ORD-123");
        // Assert state matches expected business outcome
    }
}
```

---

### 2. Scala 3 Implementation: Real Type-Level Generics

Scala 3 shines by supporting true generic macro annotations (`@redFlag[Aspect]`), keeping your code clean and enforcing strict type boundaries.

#### The Macro Definition
```scala
package com.compiler.flags

import scala.quoted.*
import scala.annotation.MacroAnnotation

class redFlag[A <: TacticalDebt](reminder: String) extends MacroAnnotation:
  def transform(using Quotes)(tree: quotes.reflect.Definition): List[quotes.reflect.Definition] =
    import quotes.reflect._

    val aspectType = TypeRepr.of[A]
    val aspectName = aspectType.show.split("\\.").last

    val message = s"\n[TACTICAL DEBT] [\${aspectName.toUpperCase}] \(reminder at\){tree.name}"

    report.warning(message, tree.pos)
    List(tree)
```

#### The Workflow
```scala
// The Tactical Shortcut
@redFlag[ConjoinedComplexity]("Combined tax calculation and invoice rendering here to move fast. Split them!")
def generateInvoice(orderId: String): String =
  // Complex, tangled code written in a hurry
  "Invoice-Data"

// The Safety Net Test
class InvoiceSpec extends munit.FunSuite:
  test("generateInvoice should return correct layout even with hacky internals") {
    assertEquals(generateInvoice("123"), "Invoice-Data")
  }
```

---

### 3. Build Tool Configurations

To prevent these warnings from blending silently into standard build log noise, configure your build tools to format, surface, or escalate them appropriately during your local development loops.

#### SBT Configuration (`build.sbt`)
For Scala projects, you want to surface verbose warning details in the console. You can also configure the compiler to treat these warnings as errors strictly inside your CI pipeline environments to block merges until debt is managed.

```scala
lazy val root = (project in file("."))
  .settings(
    name := "tactical-debt-manager",
    scalaVersion := "3.3.3",

    // Enable verbose compiler warning flags
    scalacOptions ++= Seq(
      "-deprecation",
      "-feature",
      "-unchecked"
    ) ++ (sys.env.get("CI") match {
      // If running inside CI, turn warnings into hard build-failing errors
      case Some("true") => Seq("-Werror")
      case _            => Seq.empty
    })
  )
```

#### Maven Configuration (`pom.xml`)
For Java projects using Maven, configure the `maven-compiler-plugin`. Ensure annotation processing is enabled and optionally enforce strict builds when checking for architectural drift.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <source>17</source>
                <target>17</target>
                <!-- Ensure custom annotation processors are discovered -->
                <annotationProcessors>
                    <annotationProcessor>com.compiler.flags.RedFlagProcessor</annotationProcessor>
                </annotationProcessors>
                <compilerArgs>
                    <!-- Show detailed warning locations -->
                    <arg>-Xlint:all</arg>
                    <!-- Optional: Fail build on warnings in strict environments -->
                    <!-- <arg>-Werror</arg> -->
                </compilerArgs>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### Gradle Configuration (`build.gradle`)
For Gradle projects, configure the compiler arguments on all `JavaCompile` tasks and ensure the annotation processing tool path points directly to your compiler module.

```groovy
tasks.withType(JavaCompile) {
    options.compilerArgs << "-Xlint:unchecked" << "-Xlint:deprecation"

    // Configure to treat compiler warnings as failures when needed
    if (hasProperty('failOnWarnings')) {
        options.compilerArgs << "-Werror"
    }
}

dependencies {
    // Register the processor module during compilation loops
    annotationProcessor project(':compiler-processor-module')
    implementation project(':compiler-processor-module')
}
```

---

### Workflow Comparison: Compile-Warning Cycle vs. Emacs Org-Capture

If you want to map out tactical shortcuts, you have two primary schools of
thought: **In-Code Compile-Time Warnings** or an **Out-of-Code Knowledge Base
(Emacs Org-Capture)**.

The approach you choose dictates how you interact with your reminders:


| Dimension | Compile-Warning Cycle (Java/Scala 3) | Emacs Org-Capture Approach |
| :--- | :--- | :--- |
| **Storage Location** | Directly inside the production source code file. | A centralized, external plaintext file (`todo.org`). |
| **Context Coupling** | **Absolute:** The warning is bound directly to the exact method or class syntax tree. | **Referential:** Stores a hyperlink pointer back to the file path and line number. |
| **Friction Level** | **Medium:** Requires adding imports, applying the annotation, and filling out properties. | **Ultra-Low:** A global hotkey captures the current buffer line instantly without stopping flow. |
| **Team Dynamics** | **Shared:** Everyone on the team sees the warning during a local build or on the CI pipeline. | **Private/Personal:** Perfect for individual developer task management and personal tracking. |
| **Lifecycle** | Cleaned up when the code block itself is modified and refactored. | Cleaned up by explicitly resolving or checking off an item inside your Org agenda. |

#### The Emacs Org-Capture Setup
For developers who prefer to keep their Git history pristine without "temporary" annotations, Org-capture lets you track the exact same Ousterhout debt aspects externally with an immediate global keyboard stroke:

```elisp
(setq org-capture-templates
      '(("t" "Tactical Debt Tracker" entry (file+headline "~/org/engineering.org" "Tactical Debt")
         "* TODO RESOLVE: %? \n  Context aspect: %^{Aspect|ShallowAbstraction|ConjoinedComplexity|LeakException}\n  File location: %F :: Line: %l\n  Snapshot: %i\n  Target Refactor Plan: ")))
```

---

### The Strategic Choice: Choosing Your Weapon

Ousterhout teaches us that **strategic programming is an investment**. The time spent planning a deep interface pays dividends down the line because it reduces cognitive load for every engineer who touches that module next.

When applying these tools, align them to your development reality:

1. **Use Type-Level Macro Warnings** when working on a shared project where
   tactical debt poses an immediate risk to team velocity. By surfacing these
   warnings directly in the compile loop, you ensure the shortcut remains
   visible to the entire team, serving as a shared reminder to prioritize
   refactoring during the next sprint cleanup.
2. **Use Emacs Org-Capture** when you are in a state of high-velocity deep flow,
   or working on a legacy codebase where you do not want to trigger noisy CI
   pipeline alerts for your peers. It allows you to document technical debt
   accurately and build an engineering backlog without modifying a single line
   of production source code.

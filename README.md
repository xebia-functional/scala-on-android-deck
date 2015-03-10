footer: (Scala on Android) ⇒ The current state of affairs
slidenumbers: true

![Scala on Android, The current state of affairs](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/01@2x.png)

---
#whoami

⇒ CTO & Co-Founder at 47 Degrees, a global consulting agency & Typesafe Consulting Partner.

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/47.png)

@raulraja
@47deg
http://47deg.com/blog

---

Build Tools

![sbt, gradle & maven](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/02@2x.png)

---

# [fit] SBT ⇒ Android SDK Plugin

https://github.com/pfn/android-sdk-plugin

---

# [fit] SBT ⇒ Android SDK Plugin

- Supports all Android SDK tasks
    - dex 
    - _**typedResourcesGenerator**_ 
    - _**proguard**_
    - buildConfigGenerator
    - (+ 20... more)
                    
---

# SBT ⇒ Android SDK Plugin ⇒ **`typedResourcesGenerator`**

```scala

object TR {
  val title = TypedResource[TextView](R.id.title)

  object layout {
    val abc_screen_toolbar = TypedLayout[ActionBarOverlayLayout](R.layout.abc_screen_toolbar)
  }
}

class MyActivity extends TypedActivity {

  val titleTextView = findView(title) //titleTextView inferred as TextView, no casting needed

}

```

---

### SBT ⇒ Android SDK Plugin ⇒ _**`proguard`**_

## [fit] Size Matters

## [fit] Scala byte code size reduced ~ (2.8M)

---

# SBT ⇒ Android SDK Plugin

- _**`https://github.com/pfn/android-sdk-plugin`**_
- Active
- Fast (incremental compilation and proguard caching)
- Proguard + MultiDexApplication integration 
  _(Circumvents 65K method limit)_
- Supports *AAR*, *JAR* and *APK* artifact types

---

# IDE Support

![IDEA & Scala IDE](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/03@2x.png)

---

# IntelliJ IDEA ⇒ _**`Syntax Highlighting`**_

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/idea.png)

---

# IntelliJ IDEA ⇒ _**`Android + Scala + SBT`**_

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/idea_module.png)

---

# IntelliJ IDEA

- Active
- Syntax Highlighting
- Code assistance
- _**`Android Studio`**_ : Based on IntelliJ
> Google says: If you have been using Eclipse with ADT, be aware that Android Studio is now the official IDE for Android

---

# Libraries

![Macroid & Scaloid](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/04@2x.png)

---

# Libraries ⇒ Scaloid ⇒ _**`Simplifying the Android SDK`**_

```scala
//plain vanilla scala
val button = new Button(context)
button.setText("Greet")
button.setOnClickListener(new OnClickListener() {
  def onClick(v: View) {
    Toast.makeText(context, "Hello!", Toast.LENGTH_SHORT).show()
  }
})
layout.addView(button)


```

---

# Libraries ⇒ Scaloid ⇒ _**`Simplifying the Android SDK`**_

```scala
//with Scaloid
SButton("Greet", toast("Hello!"))

```

---

# Libraries ⇒ Scaloid ⇒ _**`XML-less layouts`**_

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical" android:layout_width="match_parent"
        android:layout_height="wrap_content" android:padding="20dip">
    <TextView android:layout_width="match_parent"
            android:layout_height="wrap_content" android:text="Sign in"
            android:layout_marginBottom="25dip" android:textSize="24.5sp"/>
    <TextView android:layout_width="match_parent"
            android:layout_height="wrap_content" android:text="ID"/>
    <EditText android:layout_width="match_parent"
            android:layout_height="wrap_content" android:id="@+id/userId"/>
    <TextView android:layout_width="match_parent"
            android:layout_height="wrap_content" android:text="Password"/>
    <EditText android:layout_width="match_parent"
            android:layout_height="wrap_content" android:id="@+id/password"
            android:inputType="textPassword"/>
    <Button android:layout_width="match_parent"
            android:layout_height="wrap_content" android:id="@+id/signin"
            android:text="Sign in"/>
</LinearLayout>

```

---

# Libraries ⇒ Scaloid ⇒ _**`XML-less layouts`**_

```scala
//with scaloid
new SVerticalLayout {
  STextView("Sign in").textSize(24.5 sp).<<.marginBottom(25 dip).>>
  STextView("ID")
  SEditText()
  STextView("Password")
  SEditText() inputType TEXT_PASSWORD
  SButton("Sign in")
}.padding(20 dip)

```

---

# Libraries ⇒ Scaloid ⇒ _**`Futures & Async`**_


```scala
//plain scala
new AsyncTask[String, Void, String] {
  def doInBackground(params: Array[String]) = {
    doAJobTakeSomeTime(params)
  }

  override def onPostExecute(result: String) {
    alert("Done!", result)
  }
}.execute("param")
```

---

# Libraries ⇒ Scaloid ⇒ _**`Futures & Async`**_


```scala
//scaloid
Future {
  val result = doAJobTakeSomeTime(params)
  runOnUiThread(alert("Done!", result))
}
```

---

# Libraries ⇒ Macroid ⇒ _**`Functional UI with Macros`**_

```scala
import macroid._
import macroid.FullDsl._

class GreetingActivity extends Activity with Contexts[Activity] {
  override def onCreate(savedInstanceState: Bundle) = {
    super.onCreate(savedInstanceState)

    // the layout goes here
    setContentView {
      getUi {
        l[LinearLayout](
          w[Button],
          w[TextView]
        )
      }
    }
  }
}
```

---

# Libraries ⇒ Macroid ⇒ _**`UI composition`**_

```scala
// ActivityContext is an Android Context obtained from an Activity
import macroid.ActivityContext
  ...
  def layout1(implicit ctx: ActivityContext) =
    l[LinearLayout](
      w[TextView]
    )

  def layout2(implicit ctx: ActivityContext) =
    l[FrameLayout](
      w[ProgressBar]
    )

  def layout3(implicit ctx: ActivityContext) =
    l[FrameLayout](
      layout1,
      layout2
    )
  ...
```

---

# Libraries ⇒ Macroid ⇒ _**`Tweaks`**_

```scala
l[LinearLayout](
  // set button caption
  w[Button] <~ text("Click me"),

  // set text and hide for the time being
  w[TextView] <~ text("Hello!") <~ hide 

  // set layout orientation
) <~ vertical

```

---

# Libraries ⇒ Macroid ⇒ _**`Tweaks Composition`**_

```scala
// AppContext is an Android Context obtained from getApplicationContext
import macroid.AppContext
// More tweaks
import macroid.contrib.TextTweaks

def greeting(greeting: String)(implicit appCtx: AppContext) =
    TextTweaks.large +
    text(greeting) +
    hide


```

---

# Libraries ⇒ Macroid ⇒ _**`Events`**_

```scala
button <~ On.click {
  ...
}

```

---

# Libraries ⇒ Macroid ⇒ _**`Slots`**_

```scala
// create a slot
var greeting = slot[TextView]

l[LinearLayout](
  w[TextView] <~
    // wire the view to the slot
    wire(greeting) <~
    OurTweaks.greeting("Hello!"),
  w[Button] <~
    text("Click me") <~
    On.click {
      // use the slot elsewhere
      greeting <~ show
    }
) <~ vertical

```

---

# Libraries ⇒ Macroid ⇒ _**`Contexts`**_


```scala
class MyActivity extends Activity with Contexts[Activity] {
  // implicit access to AppContext & ActivityContext stored as a weak reference
}

class MyFragment extends Fragment with Contexts[Fragment] {
  // implicit access to AppContext & ActivityContext stored as a weak reference
}

```


---

# Libraries ⇒ Macroid ⇒ _**`Contexts`**_


```scala
class MyActivity extends Activity with Contexts[Activity] {
  // implicit access to AppContext & ActivityContext stored as a weak reference
}

class MyFragment extends Fragment with Contexts[Fragment] {
  // implicit access to AppContext & ActivityContext stored as a weak reference
}

```

# [fit] No need to explicitly pass the context around!

---

# Libraries ⇒ Macroid ⇒ _**`Snails`**_

```scala
val focusLoudly = Snail[View] { view ⇒
  view.setFocus()
  playSound
}

```

---

# Libraries ⇒ Macroid ⇒ _**`Snails composition`**_

```scala
val wink = fadeIn ++ fadeOut

```

---

# Libraries ⇒ Macroid ⇒ _**`Snails & Tweaks combined`**_

```scala
editText <~ text("foo") <~~ fadeIn <~ enable

```

---

# Libraries ⇒ Macroid ⇒ _**`Easy workflows`**_

```scala
(myProgressBar <~~ fadeOut(400)) ~~
(myTextView <~~ blink) ~~
(myOtherTextView <~ text("’Scala at the Sea!"))

```

---

# Libraries ⇒ Macroid ⇒ _**`Transformers`**_

```scala
linearLayout <~ Transformer {
  case t: TextView ⇒ t <~ text("foo")
  case i: ImageView ⇒ i <~ hide
}

```

---

# Libraries ⇒ Macroid ⇒ _**`Adaptive (Media queries)`**_

```scala
object OurTweaks {
  def orient(implicit appCtx: AppContext) =
    landscape ? horizontal | vertical
}
...
// in layout
l[LinearLayout](
  ... 
) <~ OurTweaks.orient

```

---

# Libraries ⇒ Macroid ⇒ _**`Viewable`**_

Brings the power of Typeclasses to UI composition. 
How to display _**`A`**_ using _**`W`**_. 

```scala
libraryDependencies += aar("org.macroid" %% "macroid-viewable" % "2.0.0-M3")
 
trait Viewable[A, +W <: View]

```

---

# Libraries ⇒ Macroid ⇒ _**`Viewable`**_

```scala
import macroid.viewable.Viewable

case class User(name: String)

def userViewable(
  implicit ctx: ActivityContext, 
  appCtx: AppContext): Viewable[User, TextView] =
    Viewable[User] { user ⇒
      w[TextView] <~ TextTweaks.large <~ text(user.name)
    }

```

---

# Libraries ⇒ Macroid ⇒ _**`Akka Fragments`**_

Handling Fragment events & messaging with Actors.

```scala
class MyActor extends FragmentActor[MyFragment] {
  def receive = receiveUi andThen {
    case MyMessage(x) ⇒ ...
    case MyOtherMessage ⇒ withUi(fragment ⇒ Ui {
      // do some cool ui stuff here
    })
    case FragmentActor.AttachUi(_) ⇒ ...
    case FragmentActor.DetachUi(_) ⇒ ...
  }
}

```

---

# OS Apps ⇒ _**`Translate Bubble`**_

### https://play.google.com/store/apps/details?id=com.fortysevendeg.translatebubble

### https://github.com/47deg/translate-bubble-android

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/translatebubble.png)

---

# OS Apps ⇒ _**`Scala Days Official App`**_

### https://play.google.com/store/apps/details?id=com.fortysevendeg.android.scaladays

### https://github.com/47deg/scala-days-android 
(source code available on March 15th)

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/scaladays.png)

---

# OS Apps ⇒ _**`Scala API Demos`**_

### https://play.google.com/store/apps/details?id=com.fortysevendeg.scala.android

### https://github.com/47deg/scala-android

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/scala-api-demos.png)

---

# Thank you

@raulraja
@47deg
raul at 47deg.com
http://47deg.com/blog

## https://speakerdeck.com/raulraja/scala-on-android-the-current-state-of-affairs

## https://github.com/47deg/scala-on-android-deck




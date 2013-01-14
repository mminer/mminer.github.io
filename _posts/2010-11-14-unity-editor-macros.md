---
title: "Unity Editor Macros"
---

A feature that appeared in a beta release of Unity 3.0, but which has since disappeared, is editor macros. The reason for the feature's withdrawal is unclear. Presumably it's unready for primetime, and will appear in a future version of Unity. Fortunately the ability to evaluate arbitrary code snippets in the editor is still available in the `UnityEditor.Macros` namespace, and taking advantage of it is easier than racing a sloth.

Macros are snippets of code that help speed up repetitive tasks in the editor. The same functionality can be achieved by writing an editor script, but for simple tasks --- particularly ones that you only intend to run once --- doing so can be a chore greater than the action itself. Perhaps you want to duplicate the selected game object a thousand times, or you want to perform a batch rename. For such tasks, and even for more complex ones, a few lines of code suffice.

I've written a [simple editor window script](https://gist.github.com/mminer/975335) (also available at the [Unify Community Wiki](http://wiki.unity3d.com/index.php/Macros)) that allows you to input any code and execute it. Place the file in the /Assets/Editor directory and navigate to the *Window → Macros* menu.

Here's a few basic examples of how it might use be used. Note that the `MacroEvaluator.Eval` method only accepts JavaScript and not C#.

```csharp
// Duplicate the selected game object 100 times
var duplications = 100;

for (var i = 0; i < duplications; i++) {
    GameObject.Instantiate(Selection.activeObject);
}
```

```csharp
// Create a grid of cubes
var rows = 9;
var cols = 9;
var spacing = 2;

for (var i = 0; i < rows; i++) {
    for (var j = 0; j < cols; j++) {
        var pos = new Vector3(i * spacing, 0, j * spacing);
        var go = GameObject.CreatePrimitive(PrimitiveType.Cube);
        // Name the cube with a letter and number ("A1")
        go.name += " " + char.ConvertFromUtf32(65 + i) + (j + 1);
        go.transform.position = pos;
    }
}
```

```csharp
// Batch rename selected rigidbodies that have gravity enabled
var bodies = Selection.GetFiltered(Rigidbody, SelectionMode.Editable);

for (var rb : Rigidbody in bodies) {
    if (rb.useGravity) {
        rb.name = rb.name + " Gravity";
    }
}
```

An obvious enhancement to make is the ability to save and reuse code snippets. I assume this is what Unity Technologies has in the labs, and I leave the implementation of such functionality as an exercise for the reader.

The usual warnings about using undocumented APIs apply. Just don't do anything too ridiculous.

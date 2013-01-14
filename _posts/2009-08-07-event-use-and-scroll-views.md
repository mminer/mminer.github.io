---
title: "Event.Use() and Scroll Views"
---

Today's Unity discovery: calling `Event.Use()` every time the `OnGUI()` function runs will cause scroll views to not work. At least, they won't respect any dimensions you set and will stretch to accommodate the elements contained within it.

In code form, I had something like this:

```csharp
Vector2 scroll;

void OnGUI () {
    scroll = EditorGUILayout.BeginScrollView(scroll, GUILayout.Width(50), GUILayout.Height(200));
    GUILayout.Label("I like big labels and I cannot lie. You other brothers can't deny.");
    EditorGUILayout.EndScrollView();

    if (Event.current.type == EventType.KeyDown) {
        Debug.Log("You've pressed a key. Righteous.");
    }

    Event.current.Use();
}
```

The scroll view is not 50 pixels wide. Moving `Event.current.Use()` inside the `if` statement fixes it.

```csharp
if (Event.current.type == EventType.KeyDown) {
    Debug.Log("You've pressed a key. Righteous.");
    Event.current.Use();
}
```

And all is well.

---
title: "Unity Scene View Bookmarks"
---

Unreal Engine has a [handy feature](https://www.youtube.com/watch?v=_i-d5OZmhWI) that allows you to bookmark the viewport camera's current position then return to it later. Press <kbd>control</kbd> and a number from 0 to 9 to create a bookmark, then later press the number you chose to move the viewport camera back to its previous position. It gets you no closer to releasing that MMORPG you overscoped, but it sure is useful.

Unity lacks this convenience, but it's straightforward to extend the editor and implement the feature ourselves. Rev up your code editor (or skip to [the finished script](https://github.com/mminer/scene-view-bookmarks/blob/master/SceneViewBookmarker.cs) if you just want the goods).

```csharp
using UnityEditor;
using UnityEngine;
using UnityEngine.SceneManagement;

class SceneViewBookmarker
{
    // Put code snippets below here
}
```

The interesting line here is `using UnityEngine.SceneManagement`. Unity's scripting reference makes no mention of this namespace, so the usual caveats about using undocumented APIs apply (i.e. just do it; laugh in the face of danger).

Before we get to the fun stuff, let's create a simple struct to store our scene view's `pivot`, `rotation`, and `size` values.

```csharp
struct Bookmark
{
    public Vector3 pivot;
    public Quaternion rotation;
    public float size;
}
```

Now it's a matter of getting those values, serializing the `Bookmark` struct to a JSON string, and saving the result to `EditorPrefs`.

```csharp
[MenuItem("Window/Scene View Bookmarks/Bookmark Scene View &1")]
static void BookmarkSceneView()
{
    var sceneView = SceneView.lastActiveSceneView;

    var bookmark = new Bookmark {
        pivot = sceneView.pivot,
        rotation = sceneView.rotation,
        size = sceneView.size,
    };

    var json = JsonUtility.ToJson(bookmark);
    EditorPrefs.SetString("sceneViewBookmark", json);
}
```

We're halfway there. Press <kbd>option / alt</kbd> <kbd>1</kbd> to bookmark our current scene view. Alternatively, if menus are more your style, navigate to *Window > Scene View Bookmarks > Bookmark Scene View*.

Now we need a hotkey and menu item to recall that bookmark.

```csharp
[MenuItem("Window/Scene View Bookmarks/Move Scene View To Bookmark #1")]
static void MoveSceneViewToBookmark()
{
    var json = EditorPrefs.GetString("sceneViewBookmark");
    var bookmark = JsonUtility.FromJson<Bookmark>(json);
    var sceneView = SceneView.lastActiveSceneView;
    sceneView.pivot = bookmark.pivot;
    sceneView.rotation = bookmark.rotation;
    sceneView.size = bookmark.size;
}
```

And that's it. After zooming and panning around, press <kbd>shift</kbd> <kbd>1</kbd> to return the scene view to the position you previously bookmarked. Now you're cooking with gas.

<video autoplay height="412" loop src="/videos/scene-view-bookmarks.mp4" width="660"></video>

## Refinements

Let's add polish to make this groovy new feature shine. Ideally our scene view bookmarking system also boasts these niceties:

- Supports more than one bookmark
- Offers ability to return to scene view prior to recalling bookmark (i.e. an undo of sorts)
- Validates menu item to prevent recalling bookmark that doesn't exist

Actually let's take the easy route and [download the finished script](https://github.com/mminer/scene-view-bookmarks/blob/master/SceneViewBookmarker.cs) instead. Drop it into your Unity project and you're set. Press <kbd>option / alt</kbd> and a number from 1 to 9 to bookmark the current scene view. When you want to return to the scene view that you bookmarked, press <kbd>shift</kbd> and the number you chose. After recalling a bookmark, return to the former scene view by pressing <kbd>shift</kbd> <kbd>0</kbd>.

Then get back to working on that MMORPG. It's not going to release itself.

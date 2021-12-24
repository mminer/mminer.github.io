---
title: "Scalable Icons in Unity with Icon Fonts"
---

Need scalable icons in your Unity GUI? Icon fonts might be for you.

As aficionados of Wingdings know well, fonts can contain not only letters but also shapes. Web developers picked up on this a decade ago and started using fonts to display icons. Pack symbols into a font file, assign each a character, then use the browser's text rendering to draw them crisply at any size and with any colour. This solves a few problems:

1. Browser support for vector image formats like SVG is spotty (or rather it was; today it's excellent).
1. A single file can contain thousands of symbols, minimizing HTTP requests which hamper page load time.
1. Vector images are small. A font with 1,000 glyphs might weigh in at 200 KB.

Some of these concerns are irrelevant to Unity projects, but icon fonts are still an elegant way to provide a library of icons that scale to any dimensions with no degradation.

Let's talk implementation. Here's a bare-bones element for UI Toolkit that creates a child `Label`, assigns a TTF file loaded from a *Resources* folder to its `unityFont` style, and sets its `text` property to the chosen symbol's Unicode character.

```csharp
class Icon : VisualElement
{
    public enum Symbol
    {
        CandyCane,
        Sleigh,
        Snowflake,
    }

    public new class UxmlFactory : UxmlFactory<Icon, UxmlTraits> {}

    public new class UxmlTraits : VisualElement.UxmlTraits
    {
        readonly UxmlEnumAttributeDescription<Symbol> _symbolAttribute = new() { name = "symbol" };

        public override void Init(VisualElement ve, IUxmlAttributes bag, CreationContext cc)
        {
            base.Init(ve, bag, cc);
            var icon = (Icon)ve;
            icon.symbol = _symbolAttribute.GetValueFromBag(bag, cc);
        }
    }

    static readonly Dictionary<Symbol, char> _symbolGlyphMap = new()
    {
        { Symbol.CandyCane, '\uf786' },
        { Symbol.Sleigh,    '\uf7cc' },
        { Symbol.Snowflake, '\uf2dc' },
    };

    public Symbol symbol
    {
        get => _symbol;
        set
        {
            _symbol = value;
            _symbolLabel.text = _symbolGlyphMap[value].ToString();
        }
    }

    Symbol _symbol;
    Label _symbolLabel;

    public Icon() : this(Symbol.CandyCane) {}

    public Icon(Symbol symbol)
    {
        _symbolLabel = new Label
        {
            style =
            {
                unityFont = Resources.Load<Font>("fontawesome-solid"),
                unityFontDefinition = StyleKeyword.None,
            },
        };

        Add(_symbolLabel);
        this.symbol = symbol;
    }
}

```

The secret sauce is `_symbolGlyphMap`. It translates `Symbol` members to the Unicode character that the glyph is assigned to. For this example I used popular icon library [Font Awesome](https://fontawesome.com) and copied the Unicode characters from its website.

![Font Awesome Unicode character](/images/font-awesome-unicode.png)

In UI Builder you can choose the symbol from a dropdown in the inspector then change its color and font size styles. Give it an outline or text shadow if you feel adventurous.

<video autoplay height="413" loop src="/videos/ui-builder-icon.mp4" width="660"></video>

There are oodles of great icon fonts like the aforementioned Font Awesome, but you can also create your own using online tools like [IcoMoon](https://icomoon.io). Upload your SVGs, choose which character corresponds to each icon, and download a TTF file to import into your Unity project.

![IcoMoon](/images/icomoon.png)

Adding entries to your `Symbol` enum and `_symbolGlyphMap` dictionary will be tedious if you do it by hand. You're gonna want to automate that one.

*[GUI]: Graphical User Interface
*[HTTP]: Hypertext Transfer Protocol
*[KB]: Kilobyte
*[SVG]: Scalable Vector Graphics
*[TTF]: TrueType Font

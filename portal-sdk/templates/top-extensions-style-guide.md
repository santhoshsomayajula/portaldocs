
# Styling an Extension

## Overview

The portal includes a built in list of CSS classes that may be used in templates.

Browse the following topics to learn about portal styling.

* [HTML and CSS sanitization](#html-and-css-sanitization)

* [Adding Custom CSS](#adding-custom-css)

* [Layout classes](#layout-classes)

* [Theming](#theming)

* [Typography](#typography)

* [Iconography](#iconography)

* [Utility classes](#utility-classes)

* [Color Palette](#color-palette)

## HTML and CSS sanitization 

Extension writers provide their own HTML templates and associated Cascading Style Sheets (CSS) to render and style their experiences. Those files are analyzed at runtime to filter out disallowed HTML tags, attribute names, attribute values, CSS properties, or CSS property values that are known to have potential negative impact on the Portal. This filtering ensures a consistent and sandboxed experience in the Portal. Similar sanitization is also applied to SVG markup provided from the extension.

The sanitization philosophy is to allow as much CSS as possible for the extension to use based on a [whitelist](portalfx-extensions-glossary-style-guide.md). As such, developers may encounter items filtered out that are not explicitly called out in this document. Should this occur, developers should report the issue by using the [ibiza](https://stackoverflow.microsoft.com/questions/tagged/ibiza) tag on Stack Overflow.

For more information, see [portalfx-stackoverflow.md](portalfx-stackoverflow.md).

The criteria determining negative impact are as follows.

1. Usage allows an element to escape the tile or blade container boundaries
1. Usage impacts the layout/repaint performance of the browser significantly
1. Usage can be exploited as a possible security threat
1. Usage is non-standard across IE11, Microsoft Edge, Chrome, Firefox, and Safari.
1. Usage resets defaults font-family branding that is enforced for typographic consistency.
1. Usage assigns or relies on unique identifiers. Identifier allocation is reserved in the Portal for accessibility purposes.

### HTML sanitization

HTML sanitization is divided between HTML tags and their attributes.

### HTML tags

The `iframe` tag is not allowed based on criterion 3.

### HTML attributes

The `id` attribute is not allowed based on criterion 6. The `data-bind` value is sanitized to allow simple binding and logics, but no complex JavaScript is allowed.

### CSS sanitization

All CSS properties are allowed, with a few exceptions based on the criteria listed previously. The following properties are sanitized as described.

| Property | Criterion | Reason |
| -------- | --------- | ------- |
| position | Criterion 1 |  Only a subset of the possible values are allowed: static, relative, absolute |
| font | Criterion 5 |  Disallowed. Instead use expanded properties, like font-size, font-style, etc. |
| font-family |Criterion 5 |  Disallowed. Instead, use the portal classes that assign font-family: msportalfx-font-regular, msportalfx-font-bold, msportalfx-font-semibold, msportalfx-font-light, msportalfx-font-monospace |
| list-style | Criterion 3 |  Disallowed. Use expanded properties instead of the shorthand (list-style-type, etc) |
| user-select | Criterion 4 |  Disallowed. Use Framework class `msportalfx-unselectable` to achieve the same result. |

CSS media queries are not supported, and are filtered out. The most common scenario for media queries is responding to container size. Extensions support this feature by subscribing to the container ViewModel tile size property as specified  in [portalfx-no-pdl-programming.md](portalfx-no-pdl-programming.md), [top-legacy-parts.md](top-legacy-parts.md), and [top-extensions-forms.md](top-extensions-forms.md).

### SVG sanitization

SVG sanitization follows the rules applied to HTML sanitization, as they are both DOM structures. Before using SVGs directly in the Portal, convert them first using the build tools that are available on the Internet that sanitize SVG. 

Some SVG functionality, like certain filters, and gradients, are known to cause significant browser rendering slowdowns, as specified in Criterion 2.  As such, they are sanitized out. The build tools should detect those early and mitigate them when possible. Extension writers should rely on  [StackOverflow](https://stackoverflow.microsoft.com/questions/tagged?tagnames=ibiza) if the sanitization of SVG elements causes a blocking issue.

For more information about SVG sanitization, see [#iconography](#iconography).

## Adding custom css

Extension developers can combine commonly used classes into a CSS file. CSS styles that are defined in stylesheets are [sanitized](portalfx-extensions-glossary-style-guide.md) using the same rules as the `style` attribute (see below). All custom class names begin with the `.ext-` prefix that identifies them as classes that are owned by the extension. 

All developers who install the Portal Framework SDK that is located at [http://aka.ms/portalfx/download](http://aka.ms/portalfx/download) also install the samples on their computers during the installation process. The source for the samples is located in the `Documents\PortalSDK\FrameworkPortal\Extensions\SamplesExtension` folder.

In this discussion, `<dir>` is the `SamplesExtension\Extension\` directory and  `<dirParent>` is the `SamplesExtension\` directory, based on where the samples were installed when the developer set up the SDK. Links to the Dogfood environment are working copies of the samples that were made available with the SDK.

 Add a new CSS file to your extension to specify custom styles, as in the sample located at `<dir>\Client\V1\Parts\Custom\Styles\ExampleStyles.css`. This code is also included in the following example.

```css
.ext-too-many-clicks-box {
    color: red;
    border: 2px dotted red;
    padding: 8px;
    text-align: center;
}
```

CSS files can then be referenced from any PDL file inside  the `Definition` element, as in the  sample located at `<dir>\Client\V1\Parts\Custom\CustomParts.pdl`. This code is also included in the following example.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Definition xmlns="http://schemas.microsoft.com/aux/2013/pdl" Area="Parts">
  <!--
    The following sample demonstrates the use of custom parts. Custom parts
    supply HTML templates and can be styled with custom style sheets.
  -->
  <StyleSheet Source="{Css Source='Styles\\ExampleStyles.css'}" />
  ...
</Definition>
```

The styles that are included in the CSS file can now be used inside HTML templates, as in the  sample located at `<dir>\Client\V1\Parts\Custom\Templates\ExampleCustomPart.html`.

```html
<div class="ext-too-many-clicks-box" data-bind="visible: !allowMoreClicks()">
    That's too many clicks!
    <button data-bind="click: resetClickCount">Reset</button>
</div>
```

## Layout classes

For the most part, you can lay out a blade the same way you would build an **html** page using [**Knockout**](http://knockoutjs.com/).  Layout should be handled by using CSS, as specified in [portalfx-style-guide-custom-css-file.md](portalfx-style-guide-custom-css-file.md).  The framework provides a number of [controls](top-extensions-controls.md) that you can use by using **Knockout** bindings.  In additional, the Framework provides a set of css classes you can add to elements to help with docking them to the top or bottom of the blade.

For more information about docking and controls, see [top-legacy-blades-template-pdl.md#adding-buttons](top-legacy-blades-template-pdl.md#adding-buttons).

### Controls that help out with layout

* Sections

    For layout of user input forms, as specified in [top-extensions-forms.md](top-extensions-forms.md), we provide a section control, which is just a container for other controls.  The section align its contained controls labels and padding in a consistent manner across the Portal.

* Tabs

    For tab layouts, we provide a tab control.  This control contains a collection of sections, each of which holds the contents of a tab.

* Custom Html

    We also provide a custom html control which allows you to combine a `ViewModel` and template with support for labels which behave in similar ways to those on form controls.  Custom html allows you to create your own controls to be used in sections or directly in your blade.

Sections, tabs and custom html can all be found in the playground located at [https://aka.ms/portalfx/playground](https://aka.ms/portalfx/playground).



## Typography 

Typography is associated with the formation of letters and words, and their impact on usability. The right  typography makes  extensions convey the  feeling  of accuracy, crispness, and polish.  Typography that is not as effective may be distracting and and commands more  attention than the content on which it was used.

Azure extensions have considered the factors of font selection and placement, in addition to other linotype techniques, to provide typography that can be used consistently across extensions to display content in a way that the content is more important than the designs that display it.
The typography guidelines are located at [http://uni/DesignDepot.FrontEnd/#/ProductNav/49046/0/qv?t=Azure%20Portal%7CFramework%7CStyles](http://uni/DesignDepot.FrontEnd/#/ProductNav/49046/0/qv?t=Azure%20Portal%7CFramework%7CStyles).

For more information, see [https://developer.microsoft.com/en-us/windows/projects/campaigns/windows-dev-essentials-design-principles](https://developer.microsoft.com/en-us/windows/projects/campaigns/windows-dev-essentials-design-principles).



## Iconography 

The portal has some special requirements on the types of icons that can be used in parts, commands, or blades.  All icons are required to be SVG files, which allows icons to scale on high-resolution devices, and prepares them for automatic theming.

The SDK framework includes a large library of icons that can be used off the shelf, but developers can also provide their own icons.

### Setting up the project

Perform the following steps to set up the project to use Custom SVG files.

1. Add the following content to the .csproj file.

    ```xml
    <SvgTypeScriptCompile Include="Client\_generated\Svg.ts">
    <Namespace>SamplesExtension</Namespace>
    <IsAmd>true</IsAmd>
    </SvgTypeScriptCompile>
    ```

1. Add the SVG's to the project, and assign their build action to 'Svg', as in the following example.

    ```xml
    <Svg Include="Content\SamplesExtension\Images\sample.svg" />
    ```

1. Import the **Svg** module from the previous step in the code that will use custom images.

    ```ts
    import Svg = require ("./../_generated/Svg");
    ...
    this.icon(Svg.Content.SamplesExtension.Images.robot);
    ```

The extension should now reference the SVG images.

**NOTE**: Do not check the generated `Svg.ts` file in `Source Control`, because it updates whenever a reference to an svg is added, removed, or changed.

Typically, extensions also use custom SVG's in the `Icon` attribute of the PDL file, as in the following example.

  ```xml
     <AssetType Name="Engine"
                Text="{Resource engineSearchProviderKey, Module=ClientResources}"
                Icon="{Resource Content.SamplesExtension.Images.engine, Module=./../Generated/SvgDefinitions}"
                BladeName="EngineBlade"
                PartName="EnginePart">
  ```

### Customizing the Extension

After the basics of the extension icons are working, developers may want to use the following techniques to make the extension be more impressive.

#### Color Palettes

Most of the icons that the Framework provides are located in the root namespace.  An extension can change the color of all of the icons in the root namespace by using the 
`MsPortalFx.Base.Images.Add()` method, but not by using the `MsPortalFx.Base.Images.Polychromatic.PowerUp()` method.

To change the color, add the code `{palette: MsPortalFx.Base.ImagePalette.*}`  inside the function, as in the following code.

```ts
import CustomSvgImages = require("./SvgDefinitions.js");
...
export class DeleteCommandViewModel implements MsPortalFx.ViewModels.CommandContract {
	public icon = ko.observable<MsPortalFx.Base.Image>();

	constructor(dataContext: WebsitesDataContext) {
		this.icon(MsPortalFx.Base.Images.Delete({palette: MsPortalFx.Base.ImagePalette.Blue}));
	}
}
```

#### Custom data binding 

An extension can use custom data binding for the command bar with the `image` tag. This allows it to switch between SVG's and normal images while using the same data binding, as in the following code.

```ts
export class DeleteCommandViewModel implements MsPortalFx.ViewModels.CommandContract {
	public icon = ko.observable<MsPortalFx.Base.Image>();

	constructor(dataContext: WebsitesDataContext) {
		//SVG version
		//this.icon(MsPortalFx.Base.Images.Start());

		//PNG Version
		this.icon(MsPortalFx.Base.Images.ImageUri(MsPortalFx.Base.Resources.getContentUri("Content/RemoteExtension/Images/Website_Commandbar_Play.png")));
	}
}
```

#### Preserve icon colors over theme changes

For built-in monochromatic icons, also known as flat icons, the color changes are relative to the Portal's background color as the user changes themes. 

For a working copy, you can experiment with monochromatic blades in the sample located at [https://df.onecloud.azure-test.net/#blade/SamplesExtension/IconsMonochromaticBlade](https://df.onecloud.azure-test.net/#blade/SamplesExtension/IconsMonochromaticBlade).

In light themes, monochromatic icons are displayed in shades of black, as in the following image.

![alt-text](../media/portalfx-icons/monochromatic-light-theme.png "Flat icons with light theme")

In dark themes, monochromatic icons are displayed in shades of white, as in the following image.

![alt-text](../media/portalfx-icons/monochromatic-dark-theme.png "Flat icons with dark theme")

If the color of the icon should not be included in theme changes, send the `{ isLogo: true }` option to the icon's factory method. In the following example, the Delete icon remains black in all Portal themes.

{"gitdown": "include-section", "file": "../Samples/SamplesExtension/Extension/Client/V1/UI/ViewModels/Blades/IconBladeViewModels.ts", "section": "icon#flatIconWithLogoFlat"}

An alternative is to add custom color to an icon, and then send  `{isLogo: true}` to preserve the new colors. In the following example, the Delete icon  remains  blue in all Portal themes.

{"gitdown": "include-section", "file": "../Samples/SamplesExtension/Extension/Client/V1/UI/ViewModels/Blades/IconBladeViewModels.ts", "section": "icon#flatIconWithPaletteAndLogoFlat"}

### MsPortalFx.Base.Images

The following are special calls for icons that use the `MsPortalFx.Base` API.

  * `MsPortalFx.Base.Images.Blank()`
  
    Overrides the default icon and does not render any SVG element.

  * `MsPortalFx.Base.Images.Custom`
   
    This functionality is built into the SVG bundle definition, so the extension should not need to call it.  It triggers loading custom SVG's from the extension project.

  * `MsPortalFx.Base.Images.ImageUri`

    Adds non-SVG images.
    
    **NOTE**: Non-SVG images should not be used.
    
### Using the built in SVG's 

The following  is an example of using a built-in SVG on the command bar.

```ts
export class DeleteCommandViewModel implements MsPortalFx.ViewModels.CommandContract {
    public icon = ko.observable<MsPortalFx.Base.Image>();

    constructor(dataContext: WebsitesDataContext) {
        this.icon(MsPortalFx.Base.Images.Delete());
    }
}
```

### Using the custom SVG's 

An extension can also use custom SVG's in the command bar, as in the following procedure.

1. Add SVGS's to your project  as specified in [#setting-up-the-project](#setting-up-the-project), except include  `<Svg Include="Content\Images\Commandbar_Trash.svg" />` in the project file.

1.  Assign their build action to 'Svg', as in the following AMD example.

```ts
import CustomSvgImages = require("./SvgDefinitions.js");
export class DeleteCommandViewModel implements MsPortalFx.ViewModels.CommandContract {
    public icon = ko.observable<MsPortalFx.Base.Image>();

    constructor(dataContext: WebsitesDataContext) {
        this.icon(CustomSvgImages.Content.MsPortalFx.Images.commandbar_Trash);
    }
}
```

### Creating icons

The rules to use when creating SVG icons are as follows.

1. Use paths instead of stroke color.

1. Use the colors that are in the following approved list.

  `#ffffff`
  `#e5e5e5`
  `#a0a1a2`
  `#7a7a7a`
  `#3e3e3e` - Updates in dark theme.
  `#1e1e1e`
  `#0f0f0f` - Updates in dark theme.
  `#ba141a`
  `#dd5900`
  `#ff8c00`
  `#fcd116`
  `#fee087`
  `#b8d432`
  `#7fba00`
  `#59b4d9`
  `#3999c6`
  `#804998`
  `#ec008c`
  `#0072c6`
  `#68217a`
  `#00188f`
  `#e81123`

  The names of the colors, if web-safe, are specified in [https://www.w3schools.com/colors/colors_names.asp](https://www.w3schools.com/colors/colors_names.asp).  For example,  `#ffffff` is white.

### SVG tags 

Some, but not all, SVG tags are allowed by the Portal. The following table specifies the tags that are allowed, and their attributes.

| Tag      | Attributes |
| -------- | ---------- |
| SVG      | viewBox    |
| CIRCLE   | cx, cy, r, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| DEFS     | | 
| DESC     | |
| ELLIPSE  | cx, cy, rx, ry, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| G        | opacity |
| LINE     | x1, x2, y1, y2, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| PATH     | d, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| POLYGON  | points, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| POLYLINE | points, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| RECT     | width, height, x, y, rx, ry, stroke-dasharray, stroke-width, fill-rule, transform, opacity |
| TEXT     | transform, font-size |
| TITLE    | |

### Using built-in icons

The Portal comes with a set of SVG icons that are available for use with blades, parts, and commands.

<!-- TODO: Determine whether there is a way to include this content from the SDK samples.  -->

<!--
  I used a div here so the markdown parser would stop
  doing things to my CSS
-->

```
<div>
<style type="text/css">
  #icon-container h6, #icon-container h2 {
    padding-top: 20px;
    clear:both;
  }
  .icons, .icons ul {
    list-style-type: none;
    clear: both;
  }
  .icons > li > ul > li {
    padding: 10px;
    margin-right: 10px;
    float: left;
    border: 1px solid gray;
  }
  .icons > li > ul > li:nth-child(3n+1) > div {
    width: 16px;
    height: 16px;
  }
  .icons > li > ul > li:nth-child(3n+2) > div {
    width: 24px;
    height: 24px;
  }
  .icons > li > ul > li:nth-child(3n+3) > div {
    width: 50px;
    height: 50px;
  }
  .icons > li > ul > li:nth-child(n+4) {
    background-color: #32383f
  }
  svg {
    width: 100%;
    height: 100%;
  }
</style>
</div>
```

#### Polychromatic Icons

<div id="icon-container">

<h2>Polychromatic Icons</h2><ul class="icons">
<li><h6>MsPortalFx.Base.Images.Polychromatic.ActiveDirectory()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M25.001,50.001c-1.232,0-2.392-0.48-3.261-1.352L1.351,28.261C0.492,27.402,0,26.215,0,25
	c0-1.214,0.492-2.402,1.351-3.26L21.74,1.352C22.611,0.48,23.769,0,25.001,0c1.231,0,2.39,0.48,3.261,1.352L48.648,21.74
	C49.521,22.608,50,23.767,50,25c0,1.233-0.479,2.392-1.353,3.263L28.262,48.649C27.392,49.521,26.232,50.001,25.001,50.001"/>
<path fill="#FFFFFF" d="M38.614,21.093c-2.16,0-3.91,1.75-3.91,3.909c0,0.792,0.239,1.527,0.645,2.143l-7.744,7.744
	c-0.206-0.144-0.427-0.264-0.656-0.373V14.759c1.167-0.676,1.961-1.924,1.961-3.37C28.91,9.23,27.16,7.48,25,7.48
	c-2.158,0-3.908,1.75-3.908,3.909c0,1.446,0.794,2.694,1.96,3.37v19.756c-0.219,0.104-0.434,0.216-0.632,0.353l-7.753-7.753
	c0.394-0.61,0.628-1.333,0.628-2.113c0-2.159-1.75-3.909-3.908-3.909c-2.16,0-3.91,1.75-3.91,3.909s1.75,3.909,3.91,3.909
	c0.448,0,0.872-0.091,1.274-0.23l8.15,8.15c-0.234,0.548-0.364,1.15-0.364,1.783c0,2.513,2.038,4.551,4.551,4.551
	c2.514,0,4.551-2.038,4.551-4.551c0-0.621-0.126-1.212-0.351-1.751l8.173-8.172c0.392,0.132,0.804,0.22,1.241,0.22
	c2.158,0,3.908-1.75,3.908-3.909S40.771,21.093,38.614,21.093z"/>
<rect x="31.006" y="8.226" transform="matrix(-0.707 0.7072 -0.7072 -0.707 68.2099 8.8718)" opacity="0.5" fill="#FFFFFF" width="2.523" height="20.676"/>
<rect x="16.487" y="8.242" transform="matrix(0.7071 0.7071 -0.7071 0.7071 18.3355 -7.1088)" opacity="0.5" fill="#FFFFFF" width="2.524" height="20.677"/>
<path fill="#B8D432" d="M27.665,38.614c0,1.496-1.214,2.709-2.71,2.709c-1.497,0-2.709-1.213-2.709-2.709
	c0-1.496,1.212-2.709,2.709-2.709C26.45,35.905,27.665,37.118,27.665,38.614"/>
<path fill="#B8D432" d="M27.174,11.389c0,1.201-0.973,2.174-2.174,2.174c-1.201,0-2.174-0.973-2.174-2.174
	c0-1.201,0.973-2.174,2.174-2.174C26.201,9.215,27.174,10.188,27.174,11.389"/>
<path fill="#B8D432" d="M13.563,25.001c0,1.201-0.975,2.174-2.174,2.174c-1.201,0-2.174-0.973-2.174-2.174
	c0-1.201,0.973-2.174,2.174-2.174C12.588,22.827,13.563,23.8,13.563,25.001"/>
<path fill="#B8D432" d="M40.788,25.001c0,1.201-0.975,2.174-2.175,2.174c-1.2,0-2.174-0.973-2.174-2.174
	c0-1.201,0.974-2.174,2.174-2.174C39.813,22.827,40.788,23.8,40.788,25.001"/>
<path opacity="0.1" fill="#FFFFFF" d="M28.262,1.352C27.391,0.48,26.233,0,25.001,0c-1.231,0-2.389,0.48-3.26,1.352L1.352,21.74
	C0.492,22.598,0,23.786,0,25c0,1.215,0.492,2.403,1.352,3.261l11.543,11.544L34.61,7.699L28.262,1.352z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.AppInsights()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="19.8" y="39.4" fill="#7A7A7A" width="10.6" height="3.4"/>
<polygon fill="#7A7A7A" points="23.1,50 27,50 30.3,46.5 19.8,46.5 "/>
<path fill="#68217A" d="M41.2,14.7L41.2,14.7v-0.3c0-7.7-6.6-14.1-14.7-14.2c-0.2-0.3-4.8,0.1-4.8,0.1l0,0c-7.3,0.9-13,7-13,14.1
	c0,0.2-0.8,5.8,4.9,10.5c2.6,2.3,5.3,8.5,5.7,10.3l0.3,0.6h10.6l0.3-0.6c0.4-1.8,3.2-8,5.7-10.2C41.9,20.2,41.2,14.9,41.2,14.7z"/>
<path fill="#FFFFFF" d="M30.4,18.1l-1.7,10.6h-2V18.2l0.1-0.2c3.8,0,3.3-3.5,3.3-3.5H19.8v0.3c0,0.8,0.3,3.3,3.5,3.3v10.6h-2
	l-0.5-2.5l-1.3-8.1c-2.3,0-3-1.5-3.3-2.6c0-0.4,0-0.9,0-1.4c0-2.8,3.2-3.1,3.2-3.1h11c0,0,3.5,0.4,3.5,3.5
	C33.8,14.5,33.9,18.1,30.4,18.1z"/>
<path opacity="0.15" fill="#FFFFFF" enable-background="new    " d="M41.2,16.4c0.1-1,0-1.7,0-1.8l0,0v-0.3
	c0-7.7-6.6-14.1-14.7-14.2c-0.2-0.3-4.8,0.1-4.8,0.1l0,0c-7.3,0.9-13,7-13,14.1c0,0.1-0.1,0.9,0,2.1H41.2z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Automation()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M46.281,23.344c0-0.375-0.031-0.734-0.063-1.109L50,20.141c-0.203-1.25-0.531-2.469-0.969-3.625
	l-4.313,0.078c-0.328-0.672-0.688-1.328-1.094-1.938l2.219-3.703c-0.797-0.969-1.688-1.859-2.641-2.641l-2.188,1.297l-0.922,1.375
	l-1.984,2.922c2.813,2.188,4.625,5.594,4.625,9.438c0,6.594-5.328,11.938-11.922,11.938c-6.578,0-11.922-5.344-11.922-11.938
	c0-5.906,4.281-10.781,9.906-11.75l0.906-3.672l-2.094-3.781C26.359,4.344,25.156,4.672,24,5.109l0.063,4.328
	c-0.656,0.313-1.313,0.672-1.922,1.094l-3.703-2.219c-0.969,0.781-1.844,1.672-2.641,2.641l2.219,3.703
	c-0.422,0.609-0.766,1.266-1.094,1.938l-4.328-0.078c-0.422,1.156-0.75,2.375-0.953,3.625l3.766,2.094
	c-0.031,0.375-0.047,0.734-0.047,1.109s0.016,0.734,0.047,1.109l-3.766,2.094c0.203,1.25,0.531,2.469,0.969,3.625l4.313-0.078
	c0.328,0.672,0.672,1.328,1.094,1.938l-2.219,3.703c0.797,0.969,1.672,1.859,2.641,2.656l3.703-2.234
	c0.609,0.422,1.266,0.781,1.922,1.109L24,41.578c1.156,0.438,2.359,0.766,3.609,0.969l2.109-3.781
	c0.359,0.031,0.719,0.063,1.094,0.063s0.75-0.031,1.109-0.063l2.094,3.781c1.25-0.203,2.469-0.531,3.625-0.969l-0.078-4.313
	c0.672-0.328,1.313-0.688,1.938-1.109l3.703,2.234c0.953-0.797,1.844-1.688,2.641-2.656l-2.219-3.703
	c0.406-0.609,0.766-1.266,1.094-1.938l4.313,0.078c0.438-1.156,0.766-2.375,0.969-3.625l-3.781-2.094
	C46.25,24.078,46.281,23.719,46.281,23.344z"/>
<path fill="#7A7A7A" d="M16,39.719v-1.859l-0.102-0.094l-1.902-0.641l-0.49-1.281l0.942-1.953l0.104-0.219l-0.589-0.594
	l-0.716-0.719l-0.249,0.125l-1.859,0.953l-1.281-0.359L9.047,31H7.203l-0.094,0.094l-0.641,1.898L5.172,33.48l-2.156-1.037
	l-1.313,1.302l0.125,0.245l0.953,1.857l-0.531,1.28L0,37.938v1.859l0.266,0.078l1.984,0.656l0.531,1.281l-1.016,2.156l1.313,1.328
	l0.25-0.125l1.859-0.953l1.281,0.531L7.281,47h1.859l0.078-0.266l0.656-1.984l1.266-0.531l2.172,1.016l1.313-1.313l-0.125-0.25
	l-0.953-1.859l0.367-1.297L16,39.719z M8.172,41.438c-1.438,0-2.609-1.172-2.609-2.609s1.172-2.609,2.609-2.609
	s2.594,1.172,2.594,2.609S9.609,41.438,8.172,41.438z"/>
<path fill="#FCD116" d="M33.297,1.688l-0.719,2.266l-1.234,3.938L30.75,9.781l-0.516,1.656l-3.594,11.422
	c-0.078,0.219,0.031,0.391,0.281,0.391h3.313c0.109,0,0.188,0.031,0.234,0.094c0.078,0.078,0.094,0.188,0.047,0.313l-3.422,8.297
	c-0.094,0.219-0.031,0.266,0.125,0.078L38.047,19.75c0.156-0.188,0.094-0.328-0.141-0.328l-3.547,0.016l-0.656,0.016
	c-0.234,0-0.344-0.172-0.219-0.375l3.375-6.016l0.797-1.438l0.922-1.656l1.969-3.484l1.125-2.031
	C39.156,3,36.328,2.031,33.297,1.688z"/>
<path opacity="0.3" fill="#FF8C00" d="M39.505,3.367l-7.844,16.07h1.953c-0.174-0.037-0.249-0.183-0.14-0.359l3.375-6.016
	l0.797-1.438l0.922-1.656l1.969-3.484l1.125-2.031C40.966,4.052,40.245,3.692,39.505,3.367z"/>
<path opacity="0.3" fill="#FF8C00" d="M37.92,19.422l-2.189,0.01l-8.093,12.14L38.061,19.75
	C38.217,19.563,38.155,19.422,37.92,19.422z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.AvailabilitySet()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M35.723,0H2.066C0.923,0,0.001,1.021,0.001,2.162v23.439c0,1.134,0.922,2.144,2.066,2.144h33.656
	c1.141,0,2.297-1.01,2.297-2.144V2.162C38.02,1.018,36.864,0,35.723,0"/>
<path opacity="0.2" fill="#FFFFFF" d="M35.746,0.002c-0.008,0-0.016-0.002-0.024-0.002H2.066C0.922,0.001,0,1.021,0,2.163v23.438
	c0,1.134,0.922,2.144,2.066,2.144h0.801L35.746,0.002z"/>
<polygon fill="#59B4D9" points="35.129,24.823 2.882,24.823 2.882,2.922 35.129,2.875 "/>
<path fill="#B8D432" d="M19.404,1.591c0,0.298-0.242,0.539-0.54,0.539c-0.299,0-0.539-0.241-0.539-0.539
	c0-0.298,0.24-0.539,0.539-0.539C19.162,1.052,19.404,1.293,19.404,1.591"/>
<path fill="#FFFFFF" d="M19.425,13.209c-0.035,0-0.069-0.011-0.102-0.029l-6.695-3.859c-0.061-0.036-0.1-0.104-0.1-0.175
	c0-0.072,0.039-0.139,0.1-0.175l6.654-3.834c0.062-0.035,0.138-0.035,0.2,0l6.697,3.861c0.062,0.036,0.1,0.103,0.1,0.175
	c0,0.073-0.037,0.139-0.1,0.175l-6.652,3.833C19.495,13.198,19.462,13.209,19.425,13.209"/>
<path fill="#A0A1A2" d="M41.671,7.456H8.015c-1.144,0-2.066,1.021-2.066,2.162v23.439c0,1.134,0.922,2.144,2.066,2.144h33.656
	c1.141,0,2.297-1.01,2.297-2.144V9.618C43.969,8.474,42.813,7.456,41.671,7.456"/>
<path opacity="0.2" fill="#FFFFFF" d="M41.695,7.458c-0.008,0-0.016-0.002-0.024-0.002H8.015c-1.144,0-2.066,1.021-2.066,2.162
	v23.438c0,1.134,0.922,2.144,2.066,2.144h0.801L41.695,7.458z"/>
<polygon fill="#59B4D9" points="41.078,32.279 8.83,32.279 8.83,10.378 41.078,10.331 "/>
<path fill="#B8D432" d="M25.352,9.047c0,0.298-0.242,0.539-0.54,0.539c-0.299,0-0.539-0.241-0.539-0.539
	c0-0.298,0.24-0.539,0.539-0.539C25.11,8.508,25.352,8.748,25.352,9.047"/>
<path fill="#FFFFFF" d="M25.374,20.664c-0.035,0-0.069-0.011-0.102-0.029l-6.695-3.859c-0.061-0.036-0.1-0.104-0.1-0.175
	c0-0.072,0.039-0.139,0.1-0.175l6.654-3.834c0.062-0.035,0.138-0.035,0.2,0l6.697,3.861c0.062,0.036,0.1,0.103,0.1,0.175
	c0,0.073-0.037,0.139-0.1,0.175l-6.652,3.833C25.443,20.654,25.411,20.664,25.374,20.664"/>
<path fill="#7A7A7A" d="M36.636,42.698h-0.906h-8.965h-0.468c1.242,4.38-0.427,5.008-7.737,5.008V50h9.297h6.788h8.773v-2.293
	C36.109,47.706,35.392,47.081,36.636,42.698"/>
<path fill="#A0A1A2" d="M47.703,14.955H14.047c-1.144,0-2.066,1.021-2.066,2.162v23.439c0,1.134,0.922,2.144,2.066,2.144h33.656
	c1.141,0,2.297-1.01,2.297-2.144V17.117C50,15.974,48.844,14.955,47.703,14.955"/>
<path opacity="0.2" fill="#FFFFFF" d="M47.727,14.958c-0.008,0-0.016-0.002-0.024-0.002H14.046c-1.144,0-2.066,1.021-2.066,2.162
	v23.438c0,1.134,0.922,2.144,2.066,2.144h0.801L47.727,14.958z"/>
<polygon fill="#59B4D9" points="47.109,39.779 14.862,39.779 14.862,17.878 47.109,17.83 "/>
<rect x="18.561" y="47.706" fill="#A0A1A2" width="24.858" height="2.294"/>
<path fill="#B8D432" d="M31.384,16.546c0,0.298-0.242,0.539-0.54,0.539c-0.299,0-0.539-0.241-0.539-0.539
	c0-0.298,0.24-0.539,0.539-0.539C31.142,16.007,31.384,16.248,31.384,16.546"/>
<path fill="#FFFFFF" d="M31.405,28.164c-0.035,0-0.069-0.011-0.102-0.029l-6.695-3.859c-0.061-0.036-0.1-0.104-0.1-0.175
	c0-0.072,0.039-0.139,0.1-0.175l6.654-3.834c0.062-0.035,0.138-0.035,0.2,0l6.697,3.861c0.062,0.036,0.1,0.103,0.1,0.175
	c0,0.073-0.037,0.139-0.1,0.175l-6.652,3.833C31.475,28.153,31.442,28.164,31.405,28.164"/>
<path opacity="0.7" fill="#FFFFFF" d="M30.443,37.544c-0.038,0-0.072-0.009-0.102-0.027l-6.675-3.847
	c-0.065-0.036-0.104-0.101-0.104-0.175v-7.72c0-0.073,0.039-0.139,0.104-0.175c0.062-0.037,0.137-0.037,0.204,0l6.674,3.846
	c0.059,0.038,0.099,0.104,0.099,0.177v7.72c0,0.074-0.039,0.139-0.099,0.175C30.511,37.535,30.476,37.544,30.443,37.544"/>
<path opacity="0.4" fill="#FFFFFF" d="M32.333,37.544c-0.037,0-0.071-0.009-0.105-0.027c-0.059-0.036-0.098-0.102-0.098-0.175V29.67
	c0-0.071,0.039-0.138,0.098-0.175l6.674-3.845c0.064-0.036,0.138-0.036,0.201,0c0.064,0.036,0.102,0.102,0.102,0.175v7.671
	c0,0.074-0.039,0.14-0.102,0.175l-6.672,3.847C32.403,37.535,32.367,37.544,32.333,37.544"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Backlog()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M48.083,28.179H17.287c-1.325,0-2.417-1.12-2.417-2.14c0-1.02,1.091-1.86,2.417-1.86h30.796
	c1.351,0,2.417,0.84,2.417,1.86C50.5,27.059,49.435,28.179,48.083,28.179z"/>
<path fill="#A0A1A2" d="M48.083,12.587H17.287c-1.325,0-2.417-1.12-2.417-2.14s1.091-1.86,2.417-1.86h30.796
	c1.351,0,2.417,0.84,2.417,1.86S49.435,12.587,48.083,12.587z"/>
<path fill="#A0A1A2" d="M48.109,43.772H17.287c-1.325,0-2.417-1.12-2.417-2.14c0-1.04,1.091-1.86,2.417-1.86h30.822
	c1.325,0,2.391,0.82,2.391,1.86C50.5,42.652,49.435,43.772,48.109,43.772z"/>
<polygon fill="#B8D432" points="0.5,9.907 0.5,9.907 2.248,8.157 2.248,8.157 2.248,8.157 5.749,11.658 12.752,4.655 12.752,4.655 
	12.752,4.655 14.5,6.406 14.5,6.406 14.5,6.406 5.749,15.16 "/>
<polygon fill="#B8D432" points="0.5,25.5 0.5,25.5 2.248,23.75 2.248,23.75 2.248,23.75 5.749,27.25 12.752,20.248 12.752,20.248 
	12.752,20.248 14.5,21.999 14.5,21.999 14.5,21.999 5.749,30.752 "/>
<polygon fill="#B8D432" points="0.5,41.092 0.5,41.092 2.248,39.342 2.248,39.342 2.248,39.342 5.749,42.843 12.752,35.84 
	12.752,35.84 12.752,35.84 14.5,37.591 14.5,37.591 14.5,37.591 5.749,46.345 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Backup()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#0072C6" d="M45.083,14.967c0.033-0.35,0.05-0.717,0.05-1.083c0-5.817-4.783-10.55-10.65-10.55
	C31,3.333,27.8,5,25.833,7.733C24.567,6.95,23.1,6.517,21.567,6.517c-4.183,0-7.633,3.183-8.017,7.233
	c0.65-0.083,1.3-0.15,1.967-0.15c3.083,0,6.05,1,8.467,2.767C25.8,12.183,30.417,9.5,35.533,9.35V5.767l6.25,5.35l-6.25,5.317v-3.5
	C32.6,13.05,29.75,14.15,28.1,16.817l0.333-0.033c5.967,0,10.9,4.467,11.6,10.183c1.417,1.067,2.533,2.35,3.333,3.817
	C47.233,29.75,50,26.417,50,22.333C50,19.05,48.233,16.433,45.083,14.967z"/>
<path fill="#59B4D9" d="M4.917,28.833c-0.033-0.367-0.05-0.733-0.05-1.1c0-5.817,4.783-10.533,10.65-10.533
	c3.483,0,6.683,1.667,8.65,4.383c1.267-0.783,2.733-1.2,4.267-1.2c4.433,0,8.05,3.583,8.05,7.983L36.467,29
	c3,1.517,4.617,4.033,4.617,7.183c0,4.95-4.033,8.817-9.183,8.817H9.183C4.033,45,0,41.133,0,36.183
	C0,32.917,1.767,30.3,4.917,28.833z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.BillingHub()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#0F0F0F" d="M28.431,27.562c0,0.811-0.254,1.462-0.764,1.955c-0.512,0.493-1.254,0.796-2.23,0.908v1.59h-1.07v-1.546
	c-1.006-0.01-1.928-0.234-2.768-0.674v-2.028c0.264,0.215,0.684,0.423,1.264,0.626c0.578,0.202,1.08,0.318,1.504,0.348v-2.666
	c-1.078-0.4-1.844-0.839-2.293-1.314c-0.449-0.477-0.674-1.103-0.674-1.879s0.277-1.432,0.828-1.967
	c0.553-0.534,1.266-0.846,2.139-0.934v-1.362h1.07v1.333c1.029,0.049,1.799,0.215,2.307,0.498v1.978
	c-0.68-0.41-1.447-0.664-2.307-0.762v2.775c1.078,0.392,1.848,0.831,2.307,1.318C28.203,26.249,28.431,26.849,28.431,27.562z
	 M24.367,24.017v-2.322c-0.678,0.122-1.018,0.477-1.018,1.063C23.349,23.269,23.689,23.69,24.367,24.017z M26.484,27.664
	c0-0.473-0.35-0.863-1.047-1.172v2.22C26.134,28.599,26.484,28.25,26.484,27.664z"/>
<path fill="#7FBA00" d="M25,38.81c-7.627,0-13.811-6.183-13.811-13.809s6.183-13.809,13.81-13.81l0,0
	c7.627,0,13.81,6.182,13.81,13.809h11.19c0-13.807-11.192-25-25-25v0.001C11.191,0.002,0,11.194,0,25.001s11.192,25,25,25
	c6.393,0,12.221-2.403,16.641-6.35l-7.457-8.358C31.741,37.474,28.53,38.81,25,38.81z"/>
<path opacity="0.1" fill="#FFFFFF" enable-background="new    " d="M25,38.81c-7.627,0-13.811-6.183-13.811-13.809
	s6.183-13.809,13.81-13.81l0,0c7.627,0,13.81,6.182,13.81,13.809h11.19c0-13.807-11.192-25-25-25v0.001
	C11.191,0.002,0,11.194,0,25.001s11.192,25,25,25c6.393,0,12.221-2.403,16.641-6.35l-7.457-8.358
	C31.741,37.474,28.53,38.81,25,38.81z"/>
<path fill="#FCD116" d="M50,25H38.809c0,4.096-1.793,7.764-4.625,10.292l7.457,8.359C46.768,39.072,50,32.415,50,25"/>
<path opacity="0.9" fill="#B8D432" enable-background="new    " d="M38.809,25h11.19c0-13.807-11.192-25-25-25v11.191
	C32.625,11.191,38.809,17.373,38.809,25"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.BlobBlock()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M0,44.8c0,1,0.8,1.9,1.8,1.9h46.3c1,0,1.9-0.8,1.9-1.9l0-33.1H0V44.8z"/>
<path fill="#7A7A7A" d="M48.1,4H1.8C0.8,4,0,4.9,0,5.9v5.7h50l0-5.7C50,4.9,49.2,4,48.1,4"/>
<rect x="3.7" y="15.1" fill="#0072C6" width="20.4" height="13"/>
<rect x="3.7" y="29.9" fill="#0072C6" width="20.4" height="13"/>
<rect x="25.9" y="15.1" fill="#FFFFFF" width="20.3" height="13"/>
<rect x="25.9" y="29.9" fill="#0072C6" width="20.3" height="13"/>
<path opacity="0.2" fill="#FFFFFF" d="M2,4C0.9,4,0,4.9,0,6v7.3v3.3v28c0,1.1,0.9,2,2,2h2.2L43.6,4H2z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.BlobPage()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M0,44.8c0,1,0.8,1.9,1.8,1.9h46.3c1,0,1.9-0.8,1.9-1.9l0-33.1H0V44.8z"/>
<path fill="#7A7A7A" d="M48.1,4H1.8C0.8,4,0,4.9,0,5.9v5.7h50l0-5.7C50,4.9,49.2,4,48.1,4"/>
<polygon fill="#B8D432" points="13.1,16 3.7,16 3.7,43 22.7,43 22.7,25.6 "/>
<polygon opacity="0.8" fill="#FFFFFF" points="12.4,17.1 4.8,17.1 4.8,42 21.6,42 21.6,26.3 12.4,26.3 "/>
<polygon fill="#B8D432" points="36.6,16 27.2,16 27.2,43 46.2,43 46.2,25.6 "/>
<polygon opacity="0.8" fill="#FFFFFF" points="35.9,17.1 28.3,17.1 28.3,42 45.1,42 45.1,26.3 35.9,26.3 "/>
<path opacity="0.2" fill="#FFFFFF" d="M2,4C0.9,4,0,4.9,0,6v7.3v3.3v28c0,1.1,0.9,2,2,2h2.2L43.6,4H2z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Branch()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M49,22.5c1.4,1.4,1.4,3.7,0,5L27.5,49c-1.4,1.4-3.7,1.4-5,0L1,27.5c-1.4-1.4-1.4-3.7,0-5L22.5,1
	c1.4-1.4,3.7-1.4,5,0L49,22.5z"/>
<polygon fill="#FFFFFF" points="16.2,30.1 23.2,39.9 30.2,30.1 "/>
<polygon fill="#FFFFFF" points="33.5,30.1 39.9,28.5 36.4,22.9 "/>
<rect x="21" y="11.1" fill="#FFFFFF" width="4.5" height="20.1"/>
<path fill="#FFFFFF" d="M37,29.1c-16.9-4.5-15.7-15.3-15.7-15.8l3.7,0.5l-1.9-0.2l1.9,0.2c0,0.3-0.7,8.1,12.9,11.7L37,29.1z"/>
<path opacity="0.2" fill="#FFFFFF" d="M27.5,1c-1.4-1.4-3.7-1.4-5,0L1,22.5c-1.4,1.4-1.4,3.7,0,5l14.1,14.1L33.7,7.2L27.5,1z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Browser()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M0.5,45.127c0,1.108,0.898,2.007,2.007,2.007h45.986c1.109,0,2.007-0.899,2.007-2.007V13.798h-50V45.127z"/>
<path fill="#A0A1A2" d="M48.493,4.5H2.507C1.398,4.5,0.5,5.398,0.5,6.507v10.627h50V6.507C50.5,5.398,49.601,4.5,48.493,4.5"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M2.514,4.5c-1.108,0-2.007,0.898-2.007,2.007v7.291v3.336v27.993
	c0,1.108,0.899,2.007,2.007,2.007h2.188L44.12,4.5H2.514z"/>
<rect x="13.357" y="9.279" fill="#FFFFFF" width="33.671" height="3.942"/>
<path fill="#59B4D9" d="M11.81,11.183c0,2.693-2.184,4.878-4.878,4.878s-4.878-2.185-4.878-4.878c0-2.694,2.184-4.879,4.878-4.879
	C9.625,6.304,11.81,8.489,11.81,11.183"/>
<polygon fill="#FFFFFF" points="6.416,11.732 8.629,14.068 7.428,14.068 4.469,11.25 7.417,8.432 8.615,8.432 6.416,10.754 
	11.809,10.754 11.809,11.732 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Bug()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M7.824,32.85c0-0.726,0.032-1.442,0.094-2.147H2.1c-1.159,0-2.1,0.93-2.1,2.076s0.941,2.076,2.1,2.076h5.808
	C7.852,34.196,7.824,33.527,7.824,32.85 M46.381,20.733c0.82-0.813,0.82-2.127,0-2.939c-0.82-0.811-2.152-0.811-2.971,0
	l-3.713,3.671c0.723,1.273,1.327,2.648,1.797,4.099L46.381,20.733z M3.619,45.415c-0.821,0.811-0.821,2.127,0,2.936
	c0.82,0.812,2.15,0.812,2.97,0l4.166-4.118c-0.721-1.272-1.327-2.646-1.794-4.098L3.619,45.415z M8.85,25.904
	c0.45-1.468,1.041-2.856,1.749-4.148l-4.008-3.962c-0.82-0.811-2.151-0.811-2.972,0c-0.821,0.812-0.821,2.126,0,2.939L8.85,25.904z
	 M41.379,40.472c-0.489,1.434-1.11,2.792-1.85,4.044l3.881,3.835c0.819,0.812,2.15,0.812,2.971,0c0.82-0.809,0.82-2.125,0-2.936
	L41.379,40.472z M47.898,30.703h-5.363c0.061,0.705,0.095,1.421,0.095,2.147c0,0.677-0.03,1.346-0.085,2.005h5.353
	c1.161,0,2.102-0.93,2.102-2.076C50,31.633,49.059,30.703,47.898,30.703"/>
<path fill="#804998" d="M23.736,36.485L23.736,36.485V17.199h-4.762c-4.681,2.849-7.905,8.745-7.905,15.543
	c0,0.551,0.022,1.095,0.063,1.633c0.02,0.267,0.06,0.527,0.091,0.791c0.03,0.263,0.052,0.529,0.092,0.787
	c0.059,0.383,0.139,0.757,0.218,1.131c0.028,0.129,0.047,0.262,0.077,0.39c0.103,0.442,0.225,0.874,0.356,1.301
	c0.016,0.053,0.028,0.109,0.045,0.162c0.148,0.47,0.314,0.93,0.494,1.38c0.003,0.006,0.005,0.013,0.007,0.02
	c0.549,1.369,1.243,2.633,2.054,3.772c0.005,0.007,0.009,0.015,0.014,0.022l0,0c1.137,1.59,2.504,2.927,4.042,3.93
	c0.011,0.007,0.021,0.016,0.033,0.023c0.353,0.228,0.715,0.436,1.084,0.628c0.039,0.02,0.076,0.045,0.114,0.065
	c0.347,0.176,0.703,0.33,1.063,0.473c0.063,0.025,0.124,0.058,0.188,0.082c0.355,0.134,0.718,0.244,1.084,0.344
	c0.072,0.02,0.14,0.048,0.212,0.066c0.438,0.111,0.883,0.2,1.335,0.261l0,0h0.001C23.736,50.003,23.736,36.485,23.736,36.485z"/>
<path fill="#804998" d="M39.561,32.742c0-0.394-0.013-0.784-0.036-1.171c-0.011-0.198-0.031-0.392-0.047-0.588
	c-0.014-0.16-0.025-0.32-0.043-0.479c-0.518-4.874-2.699-9.114-5.826-11.856c-0.027-0.024-0.053-0.048-0.08-0.072
	c-0.25-0.216-0.506-0.421-0.768-0.618c-0.056-0.042-0.111-0.088-0.167-0.129c-0.306-0.223-0.619-0.436-0.939-0.631h-4.762v16.65l0,0
	V50l0,0c3.964-0.536,7.439-3.064,9.742-6.739C38.468,40.341,39.561,36.696,39.561,32.742z"/>
<path fill="#7A7A7A" d="M13.372,1.803c3.442,0.024,5.003,1.288,5.917,2.697c0.753,1.182,0.967,2.557,1.014,3.374
	c-1.92,1.301-3.296,3.327-3.727,5.682h17.479c-0.431-2.355-1.809-4.381-3.729-5.682c0.049-0.819,0.262-2.192,1.014-3.374
	c0.913-1.407,2.474-2.671,5.92-2.697c0.504,0,0.912-0.404,0.912-0.901C38.173,0.405,37.765,0,37.261,0
	c-3.931-0.024-6.298,1.641-7.471,3.546c-0.749,1.2-1.068,2.444-1.203,3.412c-1.013-0.4-2.114-0.621-3.269-0.623
	c-1.157,0.002-2.262,0.225-3.274,0.623c-0.136-0.968-0.454-2.212-1.205-3.412c-1.172-1.905-3.54-3.57-7.467-3.546
	c-0.506,0-0.913,0.405-0.913,0.902S12.866,1.803,13.372,1.803"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M18.974,17.199c-4.681,2.849-7.905,8.745-7.905,15.543
	c0,4.356,1.33,8.335,3.511,11.387l9.156-7.644V17.199H18.974z"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M31.656,17.199h-4.762v16.65l10.994-9.177
	C36.493,21.462,34.326,18.824,31.656,17.199"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Builds()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<polygon fill="#804998" points="26.931,12.098 33.833,5.55 33.833,9.103 25.504,17.854 17.167,9.134 17.167,5.592 24.032,12.098 
	24.032,0.5 26.931,0.5 "/>
<path fill="#804998" d="M0.5,48.647c0,1.021,0.836,1.853,1.848,1.853h46.295c1.02,0,1.853-0.832,1.853-1.853L50.5,15.508h-3.768
	v15.796H4.201V15.508H0.5V48.647z"/>
<rect x="4.228" y="31.304" opacity="0.35" fill="#FFFFFF" enable-background="new    " width="42.5" height="15.206"/>
<rect x="21.146" y="19.979" fill="#B8D432" width="8.323" height="8.324"/>
<rect x="33.564" y="19.979" fill="#B8D432" width="8.32" height="8.324"/>
<rect x="8.729" y="19.979" fill="#B8D432" width="8.324" height="8.324"/>
<rect x="26.673" y="34.474" fill="#B8D432" width="8.323" height="8.324"/>
<rect x="14.256" y="34.474" fill="#B8D432" width="8.324" height="8.324"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Cache()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	width="50px" height="50px"  viewBox="0 0 300 250" enable-background="new 0 0 300 250" xml:space="preserve">
<path fill="#3999C6" d="M25,35.5V216c0,19,42,34,93.5,34V35.5H25z"/>
<path fill="#59B4D9" d="M117.5,250h1.5c52,0,93.5-15,93.5-34V35.5h-95V250z"/>
<path fill="#FFFFFF" d="M212.5,35.5c0,18.5-42,34-93.5,34S25,54,25,35.5s42-34,93.5-34S212.5,17,212.5,35.5"/>
<path fill="#7FBA00" d="M193.5,33.5C193.5,46,160,56,119,56S44,46,44,33.5S77.5,11,118.5,11S193.5,21,193.5,33.5"/>
<path fill="#B8D432" d="M177.5,47c10-4,15.5-8.5,15.5-13.5C193,21,159.5,11,118.5,11S44,21,44,33.5c0,5,6,10,15.5,13.5
	c13.5-5.5,35-8.5,59-8.5S164,42,177.5,47"/>
<path fill="#0072C6" d="M144,107v120.5c0,12.5,28,22.5,62.5,22.5V107H144z"/>
<path fill="#0072C6" d="M205.5,250h1c34.5,0,62.5-10,62.5-22.5V107h-63.5V250z"/>
<path opacity="0.15" fill="#FFFFFF" enable-background="new    " d="M205.5,250h1c34.5,0,62.5-10,62.5-22.5V107h-63.5V250z"/>
<path fill="#FFFFFF" d="M269,107c0,12.5-28,22.5-62.5,22.5S144,119.5,144,107s28-22.5,62.5-22.5S269,94.5,269,107"/>
<path fill="#7FBA00" d="M256,105.5c0,8-22.5,15-49.5,15s-49.5-6.5-49.5-15c0-8,22.5-15,49.5-15S256,97.5,256,105.5"/>
<path fill="#B8D432" d="M245.5,114.5c6.5-2.5,10.5-5.5,10.5-9c0-8-22.5-15-49.5-15c-27.5,0-49.5,6.5-49.5,15c0,3.5,4,6.5,10.5,9
	c9-3.5,23.5-6,39.5-6C222,108.5,236,111,245.5,114.5"/>
<polygon fill="#FFFFFF" points="240.5,180 171,237.5 198,193 174.5,193 244,136 217,180 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Certificate()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FCD116" d="M50,37.571c0,1.973-1.599,3.571-3.571,3.571H3.571C1.599,41.143,0,39.544,0,37.571v-30
	C0,5.599,1.599,4,3.571,4h42.857C48.401,4,50,5.599,50,7.571V37.571z"/>
<rect x="4.286" y="8.286" fill="#FFFFFF" width="41.429" height="28.571"/>
<circle fill="#DD5900" cx="12.143" cy="30.429" r="5.714"/>
<circle fill="#FF8C00" cx="12.143" cy="30.429" r="4.286"/>
<path fill="#DD5900" d="M12.143,34.714c-1.662,0-3.174-0.64-4.317-1.679L7.148,46.066l4.923-4.923l5,5l0.072-0.714L16.46,33.035
	C15.317,34.075,13.805,34.714,12.143,34.714z"/>
<path fill="#7A7A7A" d="M40.962,29.085c0,0.441-0.358,0.799-0.799,0.799H22.227c-0.441,0-0.799-0.358-0.799-0.799
	c0-0.441,0.358-0.799,0.799-0.799h17.936C40.604,28.286,40.962,28.644,40.962,29.085"/>
<path fill="#7A7A7A" d="M40.962,24.714c0,0.441-0.358,0.799-0.799,0.799H22.227c-0.441,0-0.799-0.358-0.799-0.799
	c0-0.441,0.358-0.799,0.799-0.799h17.936C40.604,23.915,40.962,24.273,40.962,24.714"/>
<path fill="#7A7A7A" d="M40.962,18.286c0,0.441-0.358,0.799-0.799,0.799H10.084c-0.441,0-0.799-0.358-0.799-0.799
	c0-0.441,0.358-0.799,0.799-0.799h30.078C40.604,17.486,40.962,17.844,40.962,18.286"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Chart()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect y="24.7" fill="#0072C6" width="8.8" height="25.3"/>
<rect x="13.7" y="17.8" fill="#59B4D9" width="8.8" height="32.2"/>
<rect x="27.5" y="28.8" fill="#3999C6" width="8.8" height="21.2"/>
<rect x="41.2" y="21.2" fill="#0072C6" width="8.8" height="28.8"/>
<polygon fill="#68217A" points="31.1,20.3 21.3,6.1 4.4,18.5 3.1,16.7 21.8,2.9 31.5,17 46.5,3.8 47.5,4.9 46.7,5.7 47.2,6.3 "/>
<circle fill="#804998" cx="3.3" cy="17.8" r="3.3"/>
<circle fill="#804998" cx="21.5" cy="4.9" r="3.3"/>
<circle fill="#804998" cx="31" cy="18.7" r="3.3"/>
<circle fill="#804998" cx="46.7" cy="5.3" r="3.3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ClearDBDatabase()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#0072C6" d="M6,6.831v36.338C6,46.941,14.507,50,25,50V6.831H6z"/>
<path fill="#0072C6" d="M24.74,49.999H25c10.493,0,19-3.057,19-6.831V6.831H24.74V49.999z"/>
<path opacity="0.15" fill="#FFFFFF" enable-background="new    " d="M24.74,49.999H25c10.493,0,19-3.057,19-6.831V6.831H24.74
	V49.999z"/>
<path fill="#FFFFFF" d="M44,6.831c0,3.773-8.507,6.831-19,6.831S6,10.603,6,6.831C6,3.058,14.507,0,25,0S44,3.058,44,6.831"/>
<path fill="#DD5900" d="M40.115,6.438c0,2.491-6.768,4.507-15.115,4.507S9.884,8.928,9.884,6.438S16.652,1.931,25,1.931
	S40.115,3.948,40.115,6.438"/>
<path fill="#FF8C00" d="M36.949,9.191c1.979-0.762,3.168-1.716,3.168-2.752c0-2.491-6.768-4.508-15.116-4.508
	S9.886,3.949,9.886,6.439c0,1.036,1.189,1.99,3.168,2.752C15.816,8.126,20.134,7.439,25,7.439
	C29.867,7.439,34.183,8.126,36.949,9.191"/>
<path fill="#FFFFFF" d="M9.947,33.582h2.403v-9.49l3.72,8.273c0.439,1.001,1.04,1.356,2.218,1.356c1.178,0,1.756-0.354,2.195-1.356
	l3.72-8.273v9.49h2.403v-9.474c0-0.924-0.37-1.371-1.132-1.602c-1.825-0.57-3.05-0.077-3.605,1.155l-3.651,8.165l-3.535-8.165
	c-0.531-1.232-1.779-1.725-3.605-1.155c-0.763,0.231-1.132,0.678-1.132,1.602V33.582L9.947,33.582z"/>
<path fill="#FFFFFF" d="M28.605,25.858h2.402v5.228c-0.022,0.284,0.091,0.951,1.407,0.971c0.672,0.011,5.184,0,5.226,0v-6.225h2.408
	c0.011,0-0.002,8.489-0.002,8.525c0.013,2.094-2.598,2.548-3.801,2.584h-7.588v-1.617c0.013,0,7.582,0.002,7.601,0
	c1.547-0.163,1.364-0.932,1.364-1.191v-0.63h-5.108c-2.376-0.022-3.89-1.059-3.908-2.252C28.605,31.14,28.658,25.91,28.605,25.858
	L28.605,25.858z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Clock()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FFFFFF" d="M22.83,4.372C17.124,4.974,12.221,7.82,8.877,11.951c-3.345,4.134-5.106,9.52-4.508,15.227
	c0.604,5.703,3.449,10.604,7.581,13.949c4.134,3.346,9.52,5.109,15.222,4.509c5.705-0.601,10.606-3.451,13.955-7.581
	c3.34-4.132,5.106-9.519,4.505-15.225c-0.608-5.751-3.528-10.669-7.724-14.064c-3.531-2.859-8.032-4.51-12.856-4.51
	C24.319,4.256,23.578,4.294,22.83,4.372"/>
<path fill="#3999C6" d="M49.859,22.385c-0.728-6.937-4.271-12.883-9.273-16.928c-0.971-0.786-2.018-1.472-3.1-2.1l-1.963,3.777
	c0.83,0.493,1.636,1.024,2.385,1.632c4.196,3.395,7.116,8.312,7.723,14.063c0.602,5.706-1.165,11.093-4.504,15.226
	c-3.349,4.131-8.25,6.98-13.955,7.581c-3.82,0.4-7.489-0.277-10.744-1.761l-1.961,3.774c3.976,1.851,8.479,2.708,13.151,2.214
	c6.862-0.719,12.796-4.164,16.814-9.133C48.453,35.767,50.587,29.243,49.859,22.385"/>
<path fill="#A0A1A2" d="M11.951,41.127c-4.133-3.346-6.978-8.247-7.581-13.949c-0.599-5.708,1.162-11.094,4.508-15.228
	c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776C33.07,0.793,27.824-0.43,22.381,0.143
	C15.522,0.865,9.588,4.309,5.569,9.274L5.562,9.283c-4.018,4.965-6.144,11.484-5.423,18.338c0.723,6.859,4.164,12.796,9.135,16.815
	c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774C14.826,43.144,13.315,42.231,11.951,41.127"/>
<path fill="#59B4D9" d="M11.951,41.127c-4.133-3.346-6.978-8.247-7.581-13.949c-0.599-5.708,1.162-11.094,4.508-15.228
	c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776C33.07,0.793,27.824-0.43,22.381,0.143
	C15.522,0.865,9.588,4.309,5.569,9.274L5.562,9.283c-4.018,4.965-6.144,11.484-5.423,18.338c0.723,6.859,4.164,12.796,9.135,16.815
	c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774C14.826,43.144,13.315,42.231,11.951,41.127"/>
<path fill="#7A7A7A" d="M35.497,31.558l-9.601-3.414c-1.041-0.37-1.584-1.514-1.214-2.554c0.37-1.041,1.514-1.584,2.554-1.214
	l9.601,3.414c1.041,0.37,1.584,1.514,1.214,2.554C37.682,31.384,36.538,31.928,35.497,31.558z"/>
<path fill="#7A7A7A" d="M24.801,27.773c-1.104,0-2-0.896-2-2V8.755c0-1.104,0.896-2,2-2s2,0.896,2,2v17.018
	C26.801,26.878,25.906,27.773,24.801,27.773z"/>
<circle fill="#1E1E1E" cx="24.801" cy="25.773" r="2.921"/>
<rect x="11.79" y="12.992" transform="matrix(0.7071 0.7071 -0.7071 0.7071 13.566 -5.6206)" fill="#7A7A7A" width="3.556" height="1.147"/>
<rect x="24.418" y="7.087" fill="#7A7A7A" width="1.146" height="3.556"/>
<rect x="35.725" y="11.902" transform="matrix(0.7071 0.7071 -0.7071 0.7071 20.3048 -21.6604)" fill="#7A7A7A" width="1.147" height="3.556"/>
<rect x="39.055" y="24.415" fill="#7A7A7A" width="3.556" height="1.147"/>
<rect x="34.358" y="35.56" transform="matrix(0.7071 0.7071 -0.7071 0.7071 36.134 -14.9688)" fill="#7A7A7A" width="3.556" height="1.147"/>
<rect x="34.358" y="35.56" transform="matrix(0.7071 0.7071 -0.7071 0.7071 36.134 -14.9688)" fill="#7A7A7A" width="3.556" height="1.147"/>
<rect x="24.418" y="38.996" fill="#7A7A7A" width="1.146" height="3.556"/>
<rect x="11.79" y="12.992" transform="matrix(0.7071 0.7071 -0.7071 0.7071 13.566 -5.6206)" fill="#7A7A7A" width="3.556" height="1.147"/>
<rect x="13.168" y="34.459" transform="matrix(0.7071 0.7071 -0.7071 0.7071 29.6485 0.8972)" fill="#7A7A7A" width="1.147" height="3.556"/>
<rect x="13.168" y="34.459" transform="matrix(0.7071 0.7071 -0.7071 0.7071 29.6485 0.8972)" fill="#7A7A7A" width="1.147" height="3.556"/>
<rect x="7.151" y="24.415" fill="#7A7A7A" width="3.557" height="1.147"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.CloudService()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M43.339,18.659c0.195-0.911,0.291-1.779,0.291-2.643C43.63,9.39,38.232,4,31.595,4
	c-4.631,0-8.8,2.622-10.81,6.736c-1.619-1.61-3.809-2.523-6.114-2.523c-4.763,0-8.638,3.875-8.638,8.637
	c0,0.646,0.072,1.298,0.218,1.968C2.328,20.416,0,23.731,0,27.763c0,5.437,4.443,9.696,10.113,9.696h0.646
	c0.11,4.968,4.18,8.975,9.164,8.975c3.355,0,6.393-1.809,7.996-4.681c1.206,1.045,2.748,1.628,4.36,1.628
	c3.42,0,6.249-2.591,6.65-5.922h0.961c5.67,0,10.11-4.259,10.11-9.696C50,23.564,47.456,20.109,43.339,18.659"/>
<path fill="#FFFFFF" d="M39.437,23.192l0.736-1.743l-0.213-0.184l-1.607-1.396l0.008-1.411l1.812-1.62l-0.713-1.753l-0.279,0.019
	l-2.125,0.152l-0.992-1.006l0.135-2.428l-1.742-0.735l-0.184,0.212l-1.397,1.609l-1.412-0.01l-1.621-1.81l-1.752,0.711l0.02,0.281
	l0.15,2.125l-1.004,0.99l-2.428-0.135l-0.736,1.743l0.213,0.184l1.608,1.397l-0.009,1.412l-1.81,1.621l0.711,1.752l0.281-0.021
	l2.123-0.149l0.992,1.005l-0.135,2.426l1.743,0.736l0.184-0.213l1.396-1.608l1.412,0.01l1.621,1.812l1.752-0.712l-0.02-0.282
	l-0.15-2.124l1.006-0.992L39.437,23.192z M30.628,22.693c-1.971-0.831-2.894-3.102-2.062-5.071c0.833-1.972,3.102-2.894,5.072-2.063
	c1.97,0.832,2.893,3.103,2.062,5.073C34.868,22.602,32.599,23.525,30.628,22.693"/>
<path fill="#FFFFFF" d="M28.357,31.948v-1.893l-0.266-0.086l-2.025-0.663l-0.539-1.304l1.04-2.198l-1.336-1.337l-0.251,0.127
	l-1.899,0.962l-1.305-0.541l-0.818-2.289l-1.891-0.002l-0.087,0.269l-0.663,2.023l-1.304,0.539l-2.198-1.039l-1.337,1.337
	l0.127,0.251l0.962,1.9l-0.54,1.302l-2.29,0.819l-0.001,1.891l0.267,0.088l2.025,0.661l0.539,1.306l-1.04,2.196l1.336,1.34
	l0.251-0.128l1.901-0.964l1.302,0.541l0.819,2.289l1.891,0.001l0.087-0.266l0.663-2.025l1.305-0.539l2.196,1.04l1.339-1.336
	l-0.127-0.251l-0.964-1.899l0.542-1.305L28.357,31.948z M20.044,34.908c-2.138-0.001-3.869-1.736-3.869-3.874
	c0.002-2.139,1.736-3.87,3.875-3.869c2.139,0.001,3.871,1.735,3.869,3.874C23.918,33.177,22.183,34.909,20.044,34.908"/>
<path fill="#B8D432" d="M34.449,20.103c-0.539,1.279-2.014,1.878-3.293,1.338c-1.279-0.54-1.878-2.014-1.338-3.293
	c0.54-1.277,2.014-1.878,3.293-1.338C34.389,17.35,34.988,18.824,34.449,20.103"/>
<path fill="#B8D432" d="M22.561,31.037c-0.001,1.389-1.128,2.514-2.515,2.513c-1.388-0.001-2.512-1.127-2.511-2.516
	c0.001-1.388,1.127-2.513,2.513-2.512C21.437,28.523,22.562,29.649,22.561,31.037"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Code()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M-0.5,45.1c0,1.1,0.9,2,2,2h46c1.1,0,2-0.9,2-2v-32h-50V45.1z"/>
<path fill="#A0A1A2" d="M47.5,4.5h-46c-1.1,0-2,0.9-2,2v7.3h50V6.5C49.5,5.4,48.6,4.5,47.5,4.5z"/>
<rect x="-0.5" y="13.8" opacity="0.15" fill="#FFFFFF" enable-background="new    " width="50" height="3.3"/>
<path opacity="0.1" fill="#FFFFFF" enable-background="new    " d="M1.5,4.5c-1.1,0-2,0.9-2,2v7.3v3.3v28c0,1.1,0.9,2,2,2h19.2
	L43.1,4.5H1.5z"/>
<polygon fill="#FFFFFF" points="30.6,22.2 23.1,40 20.4,40 27.8,22.2 "/>
<polygon fill="#FFFFFF" points="9.8,31.1 17.7,26.9 17.7,24.2 6.5,30.1 6.5,32.2 17.7,38.1 17.7,35.4 "/>
<polygon fill="#FFFFFF" points="40.4,31.1 32.4,26.9 32.4,24.2 43.6,30.1 43.6,32.2 32.4,38.1 32.4,35.4 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Commit()</h6><svg viewBox="0 0 24 24" >
<path fill-rule="evenodd" clip-rule="evenodd" d="M5,13v9h15v-9H5z M18,20H7v-5h11V20z"/>
<polygon points="11.121,6.251 7,9.936 7,6.934 12.509,2 18,6.911 18,9.912 13.905,6.251 13.905,12 11.121,12"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Commits()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<polygon fill="#804998" points="24.069,9.256 17.167,15.804 17.167,12.251 25.496,3.5 33.833,12.22 33.833,15.762 26.968,9.256 
	26.968,20.854 24.069,20.854 "/>
<path fill="#804998" d="M48.648,25.5H2.353c-1.02,0-1.853,0.832-1.853,1.853V31.5v13v4.147c0,1.021,0.836,1.853,1.848,1.853h46.295
	c1.02,0,1.853-0.832,1.853-1.853V44.5v-13v-4.147C50.496,26.332,49.66,25.5,48.648,25.5z"/>
<rect x="4.228" y="29.5" opacity="0.35" fill="#FFFFFF" enable-background="new    " width="42.5" height="17.01"/>
<rect x="21.146" y="34.176" fill="#B8D432" width="8.323" height="8.324"/>
<rect x="33.564" y="34.176" fill="#B8D432" width="8.32" height="8.324"/>
<rect x="8.729" y="34.176" fill="#B8D432" width="8.324" height="8.324"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Controls()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="4" fill="#E5E5E5" width="10.003" height="50"/>
<rect x="6" y="2" fill="#59B4D9" width="5.941" height="46"/>
<rect x="6" y="20" opacity="0.6" fill="#FFFFFF" width="6.003" height="28"/>
<rect x="4" y="18" fill="#7A7A7A" width="9.999" height="4"/>
<rect x="4.001" y="18" opacity="0.7" fill="#FFFFFF" width="10" height="2"/>
<rect x="20" fill="#E5E5E5" width="10.003" height="50"/>
<rect x="22" y="2" fill="#59B4D9" width="5.941" height="46"/>
<rect x="22.005" y="36" opacity="0.6" fill="#FFFFFF" width="6.003" height="12"/>
<rect x="20" y="34" fill="#7A7A7A" width="9.999" height="4"/>
<rect x="20.001" y="34" opacity="0.7" fill="#FFFFFF" width="10" height="2"/>
<rect x="36" fill="#E5E5E5" width="10.003" height="50"/>
<rect x="38" y="2" fill="#59B4D9" width="5.941" height="46"/>
<rect x="38" y="20" opacity="0.6" fill="#FFFFFF" width="6.003" height="28"/>
<rect x="36" y="18" fill="#7A7A7A" width="9.999" height="4"/>
<rect x="36.001" y="18" opacity="0.7" fill="#FFFFFF" width="10" height="2"/>
<rect x="4.001" y="22" opacity="0.2" fill="#1E1E1E" width="10" height="2"/>
<rect x="35.97" y="22" opacity="0.2" fill="#1E1E1E" width="10" height="2"/>
<rect x="20.007" y="38" opacity="0.2" fill="#1E1E1E" width="10" height="2"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ControlsHorizontal()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="0.001" y="35.998" fill="#E5E5E5" width="50" height="10.003"/>
<rect x="2.001" y="38.061" fill="#7FBA00" width="46" height="5.941"/>
<rect x="20.001" y="37.998" opacity="0.6" fill="#FFFFFF" enable-background="new    " width="28" height="6.003"/>
<rect x="18.001" y="36.002" fill="#7A7A7A" width="4" height="9.999"/>
<rect x="18.001" y="36" opacity="0.7" fill="#FFFFFF" enable-background="new    " width="2" height="10"/>
<rect x="0.001" y="19.999" fill="#E5E5E5" width="50" height="10.003"/>
<rect x="2.001" y="22.06" fill="#7FBA00" width="46" height="5.941"/>
<rect x="36.001" y="21.993" opacity="0.6" fill="#FFFFFF" enable-background="new    " width="12" height="6.003"/>
<rect x="34.001" y="20.002" fill="#7A7A7A" width="4" height="9.999"/>
<rect x="34.001" y="20" opacity="0.7" fill="#FFFFFF" enable-background="new    " width="2" height="10"/>
<rect x="0.001" y="3.999" fill="#E5E5E5" width="50" height="10.003"/>
<rect x="2.001" y="6.06" fill="#7FBA00" width="46" height="5.941"/>
<rect x="20.001" y="5.999" opacity="0.6" fill="#FFFFFF" enable-background="new    " width="28" height="6.003"/>
<rect x="18.001" y="4.002" fill="#7A7A7A" width="4" height="9.999"/>
<rect x="18.001" y="4" opacity="0.7" fill="#FFFFFF" enable-background="new    " width="2" height="10"/>
<rect x="22.001" y="36" opacity="0.2" fill="#1E1E1E" enable-background="new    " width="2" height="10"/>
<rect x="22.001" y="4.031" opacity="0.2" fill="#1E1E1E" enable-background="new    " width="2" height="10"/>
<rect x="38.001" y="19.994" opacity="0.2" fill="#1E1E1E" enable-background="new    " width="2" height="10"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Counter()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M44.5,45h-39c-3,0-5.5-2.5-5.5-5.5v-29C0,7.5,2.5,5,5.5,5h39c3,0,5.5,2.5,5.5,5.5v29
	C50,42.5,47.5,45,44.5,45z"/>
<rect x="2.7" y="13.1" fill="#0072C6" width="13" height="24"/>
<rect x="2.7" y="13.1" opacity="0.15" fill="#FFFFFF" width="13" height="12"/>
<path fill="#FFFFFF" d="M14.1,24.7c0,2.5-0.5,4.4-1.4,5.7s-2.2,2-3.8,2c-1.6,0-2.8-0.6-3.7-1.9C4.4,29.2,4,27.4,4,25
	c0-2.6,0.4-4.5,1.3-5.9s2.2-2,3.9-2C12.5,17.2,14.1,19.7,14.1,24.7z M11.7,24.8c0-2-0.2-3.4-0.6-4.3c-0.4-0.9-1.1-1.4-1.9-1.4
	c-1.8,0-2.7,2-2.7,5.9c0,1.8,0.2,3.2,0.7,4.1c0.5,0.9,1.1,1.4,2,1.4c0.9,0,1.5-0.5,1.9-1.4S11.6,26.7,11.7,24.8L11.7,24.8z"/>
<rect x="18.7" y="13.1" fill="#0072C6" width="13" height="24"/>
<rect x="18.7" y="13.1" opacity="0.15" fill="#FFFFFF" width="13" height="12"/>
<path fill="#FFFFFF" d="M30.1,24.7c0,2.5-0.5,4.4-1.4,5.7s-2.2,2-3.8,2c-1.6,0-2.8-0.6-3.7-1.9C20.4,29.2,20,27.4,20,25
	c0-2.6,0.4-4.5,1.3-5.9s2.2-2,3.9-2C28.5,17.2,30.1,19.7,30.1,24.7z M27.7,24.8c0-2-0.2-3.4-0.6-4.3c-0.4-0.9-1.1-1.4-1.9-1.4
	c-1.8,0-2.7,2-2.7,5.9c0,1.8,0.2,3.2,0.7,4.1c0.5,0.9,1.1,1.4,2,1.4c0.9,0,1.5-0.5,1.9-1.4S27.6,26.7,27.7,24.8L27.7,24.8z"/>
<rect x="34.7" y="13.1" fill="#0072C6" width="13" height="24"/>
<rect x="34.7" y="13.1" opacity="0.15" fill="#FFFFFF" width="13" height="12"/>
<path fill="#FFFFFF" d="M42.5,17.1v15h-2.4V20c-0.8,0.6-1.8,1-3.1,1.4v-2c0.9-0.3,1.7-0.6,2.4-1c0.7-0.4,1.4-0.8,2-1.2H42.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Cubes()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7FBA00" d="M11.923,34.94c-0.055,0-0.107-0.013-0.155-0.043L1.595,29.025c-0.099-0.056-0.158-0.158-0.158-0.267
	c0-0.111,0.059-0.212,0.158-0.266l10.109-5.834c0.093-0.056,0.211-0.056,0.306,0l10.172,5.874c0.099,0.054,0.159,0.156,0.159,0.268
	c0,0.11-0.06,0.211-0.159,0.265l-10.107,5.832C12.027,34.927,11.976,34.94,11.923,34.94"/>
<path fill="#B8D432" d="M10.454,49.217c-0.052,0-0.107-0.015-0.152-0.04L0.158,43.321C0.059,43.267,0,43.163,0,43.055V31.304
	c0-0.108,0.059-0.212,0.158-0.266c0.093-0.055,0.213-0.055,0.307,0l10.143,5.855c0.097,0.055,0.156,0.157,0.156,0.268v11.748
	c0,0.111-0.059,0.212-0.156,0.268C10.558,49.202,10.51,49.217,10.454,49.217"/>
<path fill="#B8D432" d="M13.326,49.217c-0.052,0-0.105-0.015-0.156-0.04c-0.094-0.056-0.151-0.157-0.151-0.268V37.235
	c0-0.109,0.057-0.211,0.151-0.268l10.142-5.853c0.098-0.054,0.213-0.054,0.31,0c0.094,0.056,0.151,0.158,0.151,0.268v11.673
	c0,0.108-0.057,0.212-0.151,0.266L13.48,49.177C13.434,49.202,13.381,49.217,13.326,49.217"/>
<path fill="#7FBA00" d="M38.146,34.941c-0.054,0-0.104-0.016-0.152-0.044l-10.176-5.873c-0.097-0.055-0.154-0.158-0.154-0.269
	c0-0.108,0.057-0.21,0.154-0.264l10.11-5.835c0.096-0.053,0.214-0.053,0.306,0l10.178,5.876c0.092,0.054,0.153,0.155,0.153,0.264
	c0,0.113-0.061,0.213-0.153,0.268l-10.112,5.833C38.251,34.925,38.2,34.941,38.146,34.941"/>
<path fill="#B8D432" d="M36.683,49.216c-0.055,0-0.109-0.013-0.156-0.041L26.383,43.32c-0.098-0.055-0.156-0.155-0.156-0.267V31.304
	c0-0.11,0.058-0.211,0.156-0.269c0.093-0.053,0.212-0.053,0.31,0l10.142,5.855c0.094,0.057,0.151,0.158,0.151,0.268v11.75
	c0,0.113-0.057,0.211-0.151,0.267C36.784,49.203,36.733,49.216,36.683,49.216"/>
<path fill="#B8D432" d="M39.553,49.216c-0.052,0-0.105-0.013-0.156-0.041c-0.094-0.056-0.152-0.154-0.152-0.267V37.234
	c0-0.11,0.058-0.211,0.152-0.268l10.141-5.853c0.098-0.054,0.213-0.054,0.31,0C49.941,31.168,50,31.27,50,31.379v11.674
	c0,0.112-0.059,0.212-0.152,0.267l-10.144,5.855C39.661,49.203,39.606,49.216,39.553,49.216"/>
<path fill="#7FBA00" d="M24.937,12.325c-0.052,0-0.105-0.016-0.155-0.044L14.606,6.408c-0.093-0.055-0.152-0.158-0.152-0.268
	c0-0.109,0.059-0.211,0.152-0.265L24.719,0.04c0.097-0.053,0.213-0.053,0.306,0l10.178,5.876c0.094,0.055,0.152,0.155,0.152,0.265
	c0,0.112-0.058,0.212-0.152,0.267l-10.111,5.833C25.043,12.309,24.992,12.325,24.937,12.325"/>
<path fill="#B8D432" d="M23.475,26.601c-0.057,0-0.11-0.014-0.155-0.041l-10.144-5.855c-0.099-0.056-0.156-0.156-0.156-0.267V8.689
	c0-0.111,0.057-0.211,0.156-0.27c0.094-0.053,0.208-0.053,0.31,0l10.142,5.856c0.091,0.056,0.151,0.158,0.151,0.267v11.75
	c0,0.113-0.06,0.211-0.151,0.268C23.577,26.587,23.525,26.601,23.475,26.601"/>
<path fill="#B8D432" d="M26.346,26.601c-0.056,0-0.107-0.014-0.157-0.041c-0.093-0.057-0.152-0.155-0.152-0.268V14.618
	c0-0.11,0.059-0.211,0.152-0.267l10.14-5.854c0.098-0.054,0.212-0.054,0.307,0c0.097,0.055,0.155,0.157,0.155,0.267v11.674
	c0,0.111-0.058,0.211-0.155,0.267L26.495,26.56C26.453,26.587,26.399,26.601,26.346,26.601"/>
<path opacity="0.5" fill="#FFFFFF" d="M13.326,49.217c-0.052,0-0.105-0.015-0.156-0.04c-0.094-0.056-0.151-0.157-0.151-0.268V37.235
	c0-0.109,0.057-0.211,0.151-0.268l10.142-5.853c0.098-0.054,0.213-0.054,0.31,0c0.094,0.056,0.151,0.158,0.151,0.268v11.673
	c0,0.108-0.057,0.212-0.151,0.266L13.48,49.177C13.434,49.202,13.381,49.217,13.326,49.217"/>
<path opacity="0.5" fill="#FFFFFF" d="M39.553,49.216c-0.052,0-0.105-0.013-0.156-0.041c-0.094-0.056-0.152-0.154-0.152-0.267
	V37.234c0-0.11,0.058-0.211,0.152-0.268l10.141-5.853c0.098-0.054,0.213-0.054,0.31,0C49.941,31.168,50,31.27,50,31.379v11.674
	c0,0.112-0.059,0.212-0.152,0.267l-10.144,5.855C39.661,49.203,39.606,49.216,39.553,49.216"/>
<path opacity="0.5" fill="#FFFFFF" d="M26.346,26.601c-0.056,0-0.107-0.014-0.157-0.041c-0.093-0.057-0.152-0.155-0.152-0.268
	V14.618c0-0.11,0.059-0.211,0.152-0.267l10.14-5.854c0.098-0.054,0.212-0.054,0.307,0c0.097,0.055,0.155,0.157,0.155,0.267v11.674
	c0,0.111-0.058,0.211-0.155,0.267L26.495,26.56C26.453,26.587,26.399,26.601,26.346,26.601"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.CustomDomain()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#0072C6" d="M0,44.474c0,1.108,0.898,2.007,2.007,2.007h45.986c1.109,0,2.007-0.899,2.007-2.007V13.145H0V44.474z"/>
<path fill="#A0A1A2" d="M47.993,3.847H2.007C0.898,3.847,0,4.745,0,5.854v10.627h50V5.854C50,4.745,49.101,3.847,47.993,3.847"/>
<path opacity="0.2" fill="#FFFFFF" d="M2.014,3.847c-1.108,0-2.007,0.898-2.007,2.007v7.291v3.336v27.993
	c0,1.108,0.899,2.007,2.007,2.007h2.188L43.62,3.847H2.014z"/>
<rect x="12.857" y="8.626" fill="#FFFFFF" width="33.671" height="3.942"/>
<path fill="#59B4D9" d="M11.31,10.53c0,2.693-2.184,4.878-4.878,4.878s-4.878-2.185-4.878-4.878c0-2.694,2.184-4.879,4.878-4.879
	C9.125,5.651,11.31,7.836,11.31,10.53"/>
<polygon fill="#FFFFFF" points="5.916,11.079 8.129,13.414 6.928,13.414 3.969,10.597 6.917,7.779 8.115,7.779 5.916,10.101 
	11.309,10.101 11.309,11.079 "/>
<path fill="#FFFFFF" d="M17.286,26.473l-2.644,8.816h-1.463l-1.817-6.311c-0.069-0.241-0.115-0.514-0.138-0.818H11.19
	c-0.017,0.207-0.077,0.474-0.181,0.801l-1.972,6.328H7.625l-2.669-8.816h1.481l1.825,6.629c0.058,0.201,0.098,0.465,0.121,0.792
	h0.069c0.017-0.252,0.069-0.522,0.155-0.809l2.032-6.612h1.292l1.825,6.646c0.057,0.213,0.101,0.477,0.129,0.792h0.069
	c0.011-0.224,0.06-0.488,0.146-0.792l1.791-6.646H17.286z"/>
<path fill="#FFFFFF" d="M30.91,26.473l-2.644,8.816h-1.463l-1.817-6.311c-0.069-0.241-0.115-0.514-0.138-0.818h-0.035
	c-0.017,0.207-0.077,0.474-0.181,0.801l-1.972,6.328h-1.41l-2.669-8.816h1.481l1.825,6.629c0.058,0.201,0.098,0.465,0.121,0.792
	h0.069c0.017-0.252,0.069-0.522,0.155-0.809l2.032-6.612h1.292l1.825,6.646c0.057,0.213,0.101,0.477,0.129,0.792h0.069
	c0.011-0.224,0.06-0.488,0.146-0.792l1.791-6.646H30.91z"/>
<path fill="#FFFFFF" d="M44.535,26.473l-2.644,8.816h-1.463l-1.817-6.311c-0.069-0.241-0.115-0.514-0.138-0.818h-0.035
	c-0.017,0.207-0.077,0.474-0.181,0.801l-1.972,6.328h-1.412l-2.669-8.816h1.481l1.825,6.629c0.058,0.201,0.098,0.465,0.121,0.792
	H35.7c0.017-0.252,0.069-0.522,0.155-0.809l2.032-6.612h1.292l1.825,6.646c0.057,0.213,0.101,0.477,0.129,0.792h0.069
	c0.011-0.224,0.06-0.488,0.146-0.792l1.791-6.646H44.535z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Database()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M6,6.788v36.111c0,3.749,8.392,6.789,18.743,6.789v-42.9C24.743,6.788,6,6.788,6,6.788z"/>
<path fill="#59B4D9" d="M24.486,49.687h0.257c10.351,0,18.743-3.038,18.743-6.788V6.788h-19V49.687z"/>
<path fill="#FFFFFF" d="M43.486,6.788c0,3.749-8.392,6.788-18.743,6.788S6,10.537,6,6.788S14.392,0,24.743,0
	S43.486,3.039,43.486,6.788"/>
<path fill="#7FBA00" d="M39.654,6.397c0,2.475-6.676,4.479-14.911,4.479S9.831,8.872,9.831,6.397c0-2.474,6.677-4.479,14.912-4.479
	S39.654,3.923,39.654,6.397"/>
<path fill="#B8D432" d="M36.53,9.134c1.952-0.757,3.125-1.705,3.125-2.735c0-2.475-6.676-4.48-14.912-4.48
	c-8.235,0-14.911,2.005-14.911,4.48c0,1.03,1.173,1.978,3.125,2.735c2.726-1.058,6.986-1.741,11.786-1.741
	C29.544,7.393,33.802,8.076,36.53,9.134"/>
<path fill="#FFFFFF" d="M36.857,36.068c-1.003,0.836-2.362,1.256-4.128,1.256h-5.89V21.961h5.587c1.759,0,3.107,0.321,4.034,0.986
	c0.868,0.624,1.303,1.497,1.303,2.612c0,0.889-0.317,1.645-0.964,2.31c-0.551,0.55-1.213,0.93-2.029,1.153v0.036
	c1.095,0.135,1.989,0.552,2.655,1.253c0.623,0.665,0.944,1.481,0.944,2.424C38.373,34.14,37.861,35.24,36.857,36.068 M22.502,35.163
	c-1.474,1.442-3.464,2.161-5.947,2.161h-5.438V21.961h5.438c5.455,0,8.183,2.483,8.183,7.48C24.738,31.83,24,33.742,22.502,35.163"
	/>
<path fill="#3999C6" d="M16.271,24.765h-1.704v9.754h1.724c1.515,0,2.689-0.474,3.558-1.383c0.832-0.909,1.253-2.121,1.253-3.654
	c0-1.439-0.421-2.573-1.234-3.413C19.015,25.201,17.822,24.765,16.271,24.765"/>
<path fill="#59B4D9" d="M33.485,27.628c0.416-0.362,0.624-0.836,0.624-1.424c0-1.139-0.832-1.706-2.52-1.706h-1.302v3.64h1.533
	C32.521,28.138,33.089,27.963,33.485,27.628"/>
<path fill="#59B4D9" d="M34.054,31.241c-0.456-0.339-1.076-0.53-1.877-0.53h-1.893v4.052h1.875c0.797,0,1.439-0.189,1.913-0.568
	c0.435-0.38,0.661-0.869,0.661-1.515C34.737,32.073,34.51,31.581,34.054,31.241"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.DevConsole()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M0,44.525c0,1.108,0.898,2.007,2.007,2.007h45.986c1.109,0,2.007-0.899,2.007-2.007V12.531H0V44.525z"/>
<path fill="#A0A1A2" d="M47.993,3.898H2.007C0.898,3.898,0,4.796,0,5.904v7.291h50V5.904C50,4.796,49.101,3.898,47.993,3.898z"/>
<rect y="13.195" opacity="0.15" fill="#FFFFFF" width="50" height="3.336"/>
<path opacity="0.1" fill="#FFFFFF" d="M2.014,3.898c-1.108,0-2.007,0.898-2.007,2.007v7.291v3.336v27.993
	c0,1.108,0.899,2.007,2.007,2.007h19.188L43.62,3.898H2.014z"/>
<polygon fill="#FFFFFF" points="7.363,37.231 7.363,35.96 16.343,31.535 16.343,31.481 7.363,26.46 7.363,25.215 18.397,31.403 
	18.397,31.641 "/>
<rect x="17.621" y="41.683" fill="#FFFFFF" width="11.262" height="1.576"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Discs()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7FBA00" d="M50,37.198c0,5.001-11.194,9.054-25,9.054S0,42.199,0,37.198v-4.88h50V37.198z"/>
<path fill="#B8D432" d="M50,32.318c0,5.001-11.194,9.054-25,9.054S0,37.319,0,32.318c0-5,11.193-9.054,25-9.054S50,27.318,50,32.318
	"/>
<path fill="#7FBA00" d="M33.013,31.797c0,1.33-3.588,2.407-8.014,2.407s-8.015-1.077-8.015-2.407s3.589-2.407,8.015-2.407
	S33.013,30.468,33.013,31.797"/>
<path opacity="0.25" fill="#FFFFFF" d="M43.071,26.115c-3.502-1.327-8.104-2.269-13.279-2.633l-3.244,6.004
	c1.596,0.094,3.023,0.329,4.127,0.662L43.071,26.115z"/>
<path opacity="0.25" fill="#FFFFFF" d="M5.902,38.208c3.601,1.543,8.598,2.643,14.288,3.045l3.793-7.02
	c-1.579-0.06-3.014-0.257-4.168-0.552L5.902,38.208z"/>
<path fill="#0072C6" d="M50,17.682c0,5.001-11.194,9.054-25,9.054S0,22.682,0,17.682v-4.88h50V17.682z"/>
<path fill="#59B4D9" d="M50,12.802c0,5.001-11.194,9.054-25,9.054S0,17.802,0,12.802s11.193-9.054,25-9.054S50,7.801,50,12.802"/>
<path fill="#0072C6" d="M33.013,12.281c0,1.33-3.588,2.407-8.014,2.407s-8.015-1.077-8.015-2.407s3.589-2.407,8.015-2.407
	S33.013,10.951,33.013,12.281"/>
<path opacity="0.25" fill="#FFFFFF" d="M43.071,6.549c-3.502-1.327-8.104-2.269-13.279-2.633L26.548,9.92
	c1.596,0.094,3.023,0.329,4.127,0.662L43.071,6.549z"/>
<path opacity="0.25" fill="#FFFFFF" d="M5.902,18.642c3.601,1.543,8.598,2.643,14.288,3.045l3.793-7.02
	c-1.579-0.06-3.014-0.257-4.168-0.552L5.902,18.642z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Download()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M32.426,38.534h-1.191h-11.79H18.83c1.634,5.768-0.561,6.595-10.175,6.595v3.02h12.227h8.927h11.538v-3.02
	C31.733,45.129,30.79,44.305,32.426,38.534"/>
<path fill="#A0A1A2" d="M46.98,2H2.718C1.214,2,0.001,3.345,0.001,4.847v30.866c0,1.493,1.213,2.823,2.717,2.823H46.98
	c1.501,0,3.021-1.33,3.021-2.823V4.847C50.001,3.341,48.481,2,46.98,2"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M47.011,2.003c-0.011,0-0.021-0.002-0.031-0.002H2.717
	C1.213,2.001,0,3.345,0,4.848v30.865c0,1.494,1.213,2.824,2.717,2.824H3.77L47.011,2.003z"/>
<polygon fill="#59B4D9" points="46.212,5.848 46.212,34.689 3.79,34.689 3.79,5.848 46.212,5.786 "/>
<polygon fill="#FFFFFF" points="26.43,20.575 33.333,14.027 33.333,17.58 25.003,26.331 16.666,17.611 16.666,14.069 23.532,20.575 
	23.532,6.977 26.43,6.977 "/>
<rect x="17.778" y="29.18" fill="#FFFFFF" width="14.445" height="3.333"/>
<rect x="8.655" y="45.128" fill="#A0A1A2" width="32.692" height="3.021"/>
<path fill="#B8D432" d="M25.518,4.095c0,0.392-0.318,0.71-0.71,0.71c-0.393,0-0.709-0.318-0.709-0.71c0-0.393,0.316-0.71,0.709-0.71
	C25.2,3.385,25.518,3.702,25.518,4.095"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ErrorIcon()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 18.0.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
   viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<g>
<path fill="#58595B" d="M47.1,22.5c0-2.9-2.3-5.2-5.1-5.2c-0.2,0-0.4,0-0.6,0c0.3-1.2,0.5-2.4,0.5-3.6C41.9,6.2,35.8,0,28.2,0
  c-6,0-11.1,3.9-13,9.4c-1-0.3-2-0.5-3-0.5c-5.2,0-9.3,4.2-9.3,9.4c0,5.2,4.2,9.4,9.3,9.4c0,0,0,0,0,0v0h30.2l0,0
  C45.1,27.5,47.1,25.3,47.1,22.5"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M17.2,27.7c-1.2-1.2-2.1-2.8-2.6-4.6c-1.2-5.1,2-10.1,7-11.3
  c1-0.2,2.1-0.3,3.1-0.2c0.5-4.7,3.3-9,7.7-11C31,0.2,29.7,0,28.2,0c-6,0-11.1,3.9-13,9.4c-1-0.3-2-0.5-3-0.5
  c-5.2,0-9.3,4.2-9.3,9.4c0,5.2,4.2,9.4,9.3,9.4c0,0,0,0,0,0v0H17.2z"/>
<path fill="#00ACED" d="M35.4,40.6l-3.9-11.3l-3.9,11.3c-0.9,2.4-2.1,5.6,0,7.8c2.1,2.1,5.6,2.1,7.8,0
  C37.6,46.2,36.4,43.8,35.4,40.6z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Extensions()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 100 100" enable-background="new 0 0 100 100" xml:space="preserve">
<rect x="30" y="42" fill="#59B4D9" width="28" height="28"/>
<polygon fill="#804998" points="56,0 56,22 78,22 78,44 100,44 100,0 "/>
<polygon fill="#A0A1A2" points="22,78 22,34 38,34 38,12 0,12 0,100 88,100 88,60 66.2,60 66.2,78 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.File()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#59B4D9" points="45,6.8 40.2,2 38.2,0 38,0 3,0 3,50 47,50 47,9 47,8.8 "/>
<polygon opacity="0.8" fill="#FFFFFF" points="38,2 5,2 5,48 45,48 45,9 38,9 "/>
<path fill="#59B4D9" d="M28.8,39.2c0,0.6-0.5,1.1-1.1,1.1H12.6c-0.6,0-1.1-0.5-1.1-1.1c0-0.6,0.5-1.1,1.1-1.1h15.1
	C28.3,38.1,28.8,38.6,28.8,39.2"/>
<path fill="#59B4D9" d="M38.9,24.2c0,0.6-0.5,1.1-1.1,1.1H12.6c-0.6,0-1.1-0.5-1.1-1.1c0-0.6,0.5-1.1,1.1-1.1h25.1
	C38.4,23.1,38.9,23.6,38.9,24.2"/>
<path fill="#59B4D9" d="M38.9,31.7c0,0.6-0.5,1.1-1.1,1.1H12.6c-0.6,0-1.1-0.5-1.1-1.1c0-0.6,0.5-1.1,1.1-1.1h25.1
	C38.4,30.6,38.9,31.1,38.9,31.7"/>
<path fill="#59B4D9" d="M38.9,16.7c0,0.6-0.5,1.1-1.1,1.1H12.6c-0.6,0-1.1-0.5-1.1-1.1c0-0.6,0.5-1.1,1.1-1.1h25.1
	C38.4,15.6,38.9,16.1,38.9,16.7"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Files()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#59B4D9" points="47.4,17.5 43.5,13.6 41.8,12 41.6,12 13,12 13,50 49,50 49,19.4 49,19.2 "/>
<polygon opacity="0.8" fill="#FFFFFF" points="41,14 15,14 15,48 47,48 47,20 41,20 "/>
<path fill="#59B4D9" d="M20,39.1c0-0.5,0.4-0.9,0.9-0.9h12.4c0.5,0,0.9,0.4,0.9,0.9c0,0.5-0.4,0.9-0.9,0.9H20.9
	C20.4,40,20,39.6,20,39.1"/>
<path fill="#59B4D9" d="M20,32.9c0-0.5,0.4-0.9,0.9-0.9h20.5c0.5,0,0.9,0.4,0.9,0.9c0,0.5-0.4,0.9-0.9,0.9H20.9
	C20.4,33.8,20,33.4,20,32.9"/>
<path fill="#59B4D9" d="M20,27.1c0-0.5,0.4-0.9,0.9-0.9h20.5c0.5,0,0.9,0.4,0.9,0.9c0,0.5-0.4,0.9-0.9,0.9H20.9
	C20.4,28,20,27.6,20,27.1"/>
<rect x="3" fill="#59B4D9" width="29" height="6"/>
<rect x="1" fill="#59B4D9" width="6" height="40"/>
<polygon opacity="0.8" fill="#FFFFFF" points="5,2 3,2 3,38 7,38 7,6 30,6 30,2 "/>
<rect x="9" y="6" fill="#59B4D9" width="29" height="6"/>
<rect x="7" y="6" fill="#59B4D9" width="6" height="38"/>
<polygon opacity="0.8" fill="#FFFFFF" points="11,8 9,8 9,42 13,42 13,12 36,12 36,8 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.FolderBlank()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FEE087" d="M48.065,11.912V7.935H21.897L15.963,2H0v43.708c0,1.31,1.06,2.371,2.37,2.372v0h45.258
	c1.309,0,2.371-1.062,2.371-2.372V11.912H48.065z"/>
<path opacity="0.2" fill="#1E1E1E" d="M4.742,11.912v33.796c0,1.31-1.062,2.372-2.371,2.372C1.06,48.08,0,47.018,0,45.708V2h15.963
	l5.934,5.935h26.168v3.977H4.742z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.FolderCube()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FEE087" d="M48.065,11.912V7.935H21.897L15.963,2H0v43.708c0,1.31,1.06,2.371,2.37,2.372l0,0h45.258
	c1.309,0,2.371-1.062,2.371-2.372V11.912H48.065z"/>
<path fill="#DD5900" d="M26.176,27.945c-0.052,0-0.105-0.016-0.155-0.044l-10.175-5.873c-0.093-0.055-0.152-0.158-0.152-0.267
	c0-0.11,0.059-0.212,0.152-0.266l10.112-5.835c0.097-0.053,0.213-0.053,0.306,0l10.178,5.876c0.094,0.054,0.152,0.155,0.152,0.265
	c0,0.112-0.058,0.212-0.152,0.267l-10.111,5.833C26.283,27.929,26.232,27.945,26.176,27.945"/>
<path fill="#FF8C00" d="M24.714,42.221c-0.056,0-0.11-0.013-0.155-0.041l-10.144-5.855c-0.099-0.056-0.156-0.155-0.156-0.267V24.309
	c0-0.11,0.057-0.211,0.156-0.269c0.093-0.053,0.208-0.053,0.31,0l10.143,5.855c0.09,0.057,0.15,0.158,0.15,0.268v11.75
	c0,0.113-0.06,0.211-0.15,0.267C24.817,42.208,24.765,42.221,24.714,42.221"/>
<path opacity="0.5" fill="#FF8C00" enable-background="new    " d="M27.585,42.221c-0.055,0-0.107-0.013-0.157-0.041
	c-0.093-0.056-0.152-0.154-0.152-0.267V30.239c0-0.11,0.059-0.211,0.152-0.268l10.141-5.853c0.097-0.054,0.212-0.054,0.306,0
	c0.097,0.055,0.156,0.157,0.156,0.266v11.674c0,0.112-0.059,0.211-0.156,0.267l-10.14,5.855
	C27.693,42.208,27.638,42.221,27.585,42.221"/>
<path opacity="0.2" fill="#1E1E1E" enable-background="new    " d="M4.742,11.912v33.796c0,1.31-1.062,2.372-2.371,2.372
	C1.06,48.08,0,47.018,0,45.708V2h15.963l5.934,5.935h26.168v3.977H4.742z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.FolderWebsite()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FEE087" d="M47.064,11.912V7.934H21.897L15.963,2H0v43.708c0,1.309,1.06,2.372,2.371,2.372l0,0h45.258
	c1.309,0,2.371-1.063,2.371-2.371V11.912H47.064z"/>
<path fill="#59B4D9" d="M32.59,38.052c-1.656,1.267-3.607,1.883-5.544,1.883c-2.749,0-5.467-1.234-7.263-3.584
	c-3.069-4.007-2.312-9.737,1.703-12.807c1.655-1.274,3.608-1.882,5.543-1.882c2.749,0,5.467,1.234,7.263,3.585
	C37.361,29.253,36.596,34.986,32.59,38.052"/>
<path fill="#FFFFFF" d="M30.911,32.399c0.664,0.866,1.895,1.023,2.757,0.367c0.045-0.034,0.079-0.075,0.12-0.112
	c0.88,0.62,1.565,1.08,1.909,1.314c0.104-0.265,0.17-0.515,0.244-0.78c-0.362-0.269-0.933-0.709-1.639-1.281
	c0.233-0.613,0.158-1.329-0.267-1.89c-0.609-0.787-1.688-0.988-2.532-0.505c-0.934-0.838-1.95-1.784-3.034-2.861
	c3.351-1.802,5.73-1.537,5.73-1.537c-0.398-0.508-0.843-0.952-1.318-1.352c-1.414-0.219-3.607-0.194-6.115,1.14v-0.001
	c-0.122,0.064-0.244,0.139-0.367,0.21c0.122-0.071,0.244-0.146,0.366-0.211c-0.835-0.875-1.687-1.812-2.552-2.819
	c-0.416,0.133-0.82,0.298-1.211,0.492c0.639,1.046,1.498,2.101,2.469,3.131c0,0,0,0,0.001,0c0.032,0.034,0.067,0.068,0.099,0.102
	c-0.033-0.034-0.07-0.065-0.101-0.099c-0.828,0.57-1.673,1.296-2.531,2.203c-0.109,0.117-0.205,0.236-0.308,0.354
	c-0.492-0.1-1.009-0.074-1.493,0.099c-0.83-1.789-0.773-3.229-0.642-3.968c-0.359,0.376-0.695,0.771-0.984,1.193
	c-0.217,0.885-0.276,2.162,0.365,3.699c-0.735,0.965-0.776,2.328-0.006,3.336c0.067,0.087,0.146,0.158,0.221,0.235
	c-0.336,1.146-0.504,2.255-0.55,3.204c0.086,0.118,0.086,0.212,0.173,0.327c0.438,0.562,0.992,1.094,1.524,1.523
	c-0.065-1.007,0.007-2.551,0.631-4.221c0.426,0.031,0.856-0.035,1.262-0.205c0.236,0.208,0.48,0.417,0.744,0.63
	c0.897,0.71,1.791,1.263,2.663,1.698c-0.045,0.444,0.066,0.904,0.354,1.286c0.616,0.795,1.757,0.944,2.552,0.336
	c0.166-0.127,0.297-0.28,0.405-0.445c1.422,0.317,2.664,0.372,3.585,0.372c0.141,0,0.795-0.889,1.17-1.441
	c-0.56,0.117-2.226,0.339-4.497-0.313c-0.055-0.253-0.16-0.5-0.328-0.719c-0.577-0.756-1.629-0.92-2.417-0.415
	c-0.791-0.429-1.618-0.963-2.472-1.639c-0.178-0.141-0.331-0.281-0.495-0.422c0.513-0.817,0.571-1.857,0.12-2.735
	c0.108-0.108,0.199-0.218,0.314-0.325c0.807-0.753,1.663-1.424,2.387-1.926c1.14,1.054,2.348,2.052,3.492,2.944
	C30.403,31.015,30.451,31.798,30.911,32.399z"/>
<path fill="#FFFFFF" d="M27.309,27.361c0.002-0.001,0.004-0.003,0.006-0.005c0.058-0.037,0.117-0.075,0.175-0.113
	C27.43,27.283,27.369,27.322,27.309,27.361z"/>
<path opacity="0.2" fill="#1E1E1E" d="M4.742,11.912v33.796c0,1.309-1.062,2.372-2.371,2.372C1.06,48.08,0,47.017,0,45.708V2h15.963
	l5.934,5.934h25.167v3.978H4.742z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Ftp()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M0,44.627c0,1.108,0.898,2.007,2.007,2.007h45.986c1.109,0,2.007-0.899,2.007-2.007V13.298H0V44.627z"/>
<path fill="#A0A1A2" d="M47.993,4H2.007C0.898,4,0,4.898,0,6.007v10.627h50V6.007C50,4.898,49.101,4,47.993,4"/>
<polygon fill="#FFFFFF" points="26.556,21.397 19.1,39.232 14.424,39.232 21.848,21.397 "/>
<polygon fill="#FFFFFF" points="36.123,21.397 28.667,39.232 23.991,39.232 31.415,21.397 "/>
<path opacity="0.2" fill="#FFFFFF" d="M2.014,4C0.906,4,0.007,4.898,0.007,6.007v7.291v3.336v27.993
	c0,1.108,0.899,2.007,2.007,2.007h2.188L43.62,4H2.014z"/>
<rect x="12.857" y="8.779" fill="#FFFFFF" width="33.671" height="3.942"/>
<path fill="#59B4D9" d="M11.31,10.683c0,2.693-2.184,4.878-4.878,4.878s-4.878-2.185-4.878-4.878c0-2.694,2.184-4.879,4.878-4.879
	C9.125,5.804,11.31,7.989,11.31,10.683"/>
<polygon fill="#FFFFFF" points="5.916,11.232 8.129,13.568 6.928,13.568 3.969,10.75 6.917,7.932 8.115,7.932 5.916,10.254 
	11.309,10.254 11.309,11.232 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Gear()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#7A7A7A" points="50,27.726 50,22.036 49.197,21.775 43.106,19.786 41.481,15.861 44.605,9.252 40.581,5.229 
	39.826,5.61 34.115,8.513 30.19,6.885 27.724,0 22.036,0 21.773,0.807 19.784,6.899 15.862,8.522 9.25,5.4 5.227,9.422 5.61,10.175 
	8.51,15.889 6.887,19.81 0,22.276 0,27.967 0.804,28.231 6.896,30.218 8.521,34.141 5.398,40.752 9.419,44.777 10.174,44.393 
	15.886,41.492 19.81,43.117 22.275,50 27.966,50 28.228,49.197 30.217,43.108 34.14,41.482 40.75,44.608 44.775,40.582 
	44.392,39.828 41.49,34.117 43.117,30.193 "/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M39.749,5.648l-5.634,2.865L30.19,6.886L27.725,0h-5.689
	l-0.262,0.807l-1.989,6.091l-3.922,1.624L9.249,5.398L5.227,9.422l0.383,0.753l2.9,5.715l-1.623,3.92L0,22.277v5.69l0.804,0.263
	l6.092,1.988l1.625,3.923l-3.123,6.611l4.021,4.025l0.755-0.384l2.433-1.236l3.452-4.77c0.006,0.004,0.012,0.008,0.018,0.012
	l19.047-26.32c-0.006-0.005-0.012-0.009-0.018-0.014L39.749,5.648z"/>
<path fill="#FFFFFF" d="M32.527,16.257l-2.369,2.391c2.137,1.429,3.554,3.879,3.554,6.632c0,4.331-3.472,7.769-7.781,7.699
	c-4.285-0.07-7.792-3.635-7.792-7.978c0-2.433,1.245-4.587,2.989-5.994L23,20.747V15h-5.809l1.421,1.522
	c-2.326,2.009-3.817,5.02-3.817,8.371c0,6.201,5.016,11.312,11.148,11.417c6.155,0.104,10.916-4.725,10.916-10.915
	C36.859,21.702,35.268,18.347,32.527,16.257z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Globe()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FF8C00" d="M23.987,40.351h-3.951c0.957,5.788-2.336,6.618-7.968,6.618V50h7.163h5.229h6.759v-3.031
	C25.588,46.969,23.029,46.142,23.987,40.351"/>
<rect x="12.07" y="46.968" fill="#FCD116" width="19.151" height="3.032"/>
<path fill="#59B4D9" stroke="#59B4D9" stroke-width="0.232" stroke-miterlimit="10" d="M23.968,30.928
	c0.904-0.904,2.37-0.904,3.274,0l-3.66-3.66c-0.904-0.904-0.904-2.37,0-3.274c0.904-0.904,2.37-0.904,3.274,0l0.971,0.971
	l0.756,0.686l1.79,1.79c0.904,0.904,2.37,0.904,3.274,0c0.904-0.904,0.904-2.37,0-3.274l-1.79-1.79l-1.133-1.133l-5.447-5.447
	c-0.904-0.904-0.904-2.37,0-3.274c0.904-0.904,2.37-0.904,3.274,0l2.387,2.387c-0.904-0.904-0.904-2.37,0-3.274
	c0.904-0.904,2.37-0.904,3.274,0l6.195,6.195C40.199,13.34,38.379,8.91,34.949,5.48c-3.17-3.17-7.195-4.963-11.332-5.383
	l2.778,2.778c1.141,1.141,1.141,2.99,0,4.131s-2.99,1.141-4.131,0l-1.9-1.9c-1.141-1.141-2.99-1.141-4.131,0s-1.141,2.99,0,4.131
	l3.743,3.743c1.141,1.141,1.141,2.99,0,4.131c-1.141,1.141-2.99,1.141-4.131,0l-4.106-4.106c0.489,1.076,0.294,2.388-0.591,3.273
	c-1.141,1.141-2.99,1.141-4.131,0l2.988,2.988c1.141,1.141,1.141,2.99,0,4.131c-1.141,1.141-2.99,1.141-4.131,0l-2.778-2.778
	c0.42,4.138,2.213,8.162,5.383,11.332c4.898,4.898,11.834,6.508,18.077,4.842l-2.589-2.589
	C23.063,33.298,23.063,31.832,23.968,30.928z"/>
<path fill="#B8D432" d="M40.409,17.831l-6.195-6.195c-0.904-0.904-2.37-0.904-3.274,0c-0.904,0.904-0.904,2.37,0,3.274l-2.387-2.387
	c-0.904-0.904-2.37-0.904-3.274,0c-0.904,0.904-0.904,2.37,0,3.274l5.447,5.447l1.133,1.133l1.79,1.79
	c0.904,0.904,0.904,2.37,0,3.274c-0.904,0.904-2.37,0.904-3.274,0l-1.79-1.79l-0.756-0.686l-0.971-0.971
	c-0.904-0.904-2.37-0.904-3.274,0c-0.904,0.904-0.904,2.37,0,3.274l3.66,3.66c-0.904-0.904-2.37-0.904-3.274,0
	c-0.904,0.904-0.904,2.37,0,3.274l2.589,2.589c3.074-0.82,5.981-2.431,8.392-4.842C38.828,28.069,40.648,22.911,40.409,17.831z"/>
<path fill="#B8D432" d="M10.005,23.394c1.141-1.141,1.141-2.99,0-4.131l-2.988-2.988c1.141,1.141,2.99,1.141,4.131,0
	c0.885-0.885,1.08-2.196,0.591-3.273l4.106,4.106c1.141,1.141,2.99,1.141,4.131,0c1.141-1.141,1.141-2.99,0-4.131l-3.743-3.743
	c-1.141-1.141-1.141-2.99,0-4.131s2.99-1.141,4.131,0l1.9,1.9c1.141,1.141,2.99,1.141,4.131,0s1.141-2.99,0-4.131l-2.778-2.778
	C18.214-0.452,12.619,1.341,8.48,5.48s-5.932,9.734-5.383,15.137l2.778,2.778C7.015,24.535,8.864,24.535,10.005,23.394z"/>
<path fill="#FCD116" d="M21.714,43.656c-6.662,0-12.926-2.594-17.637-7.305c-0.68-0.68-0.68-1.781,0-2.461
	c0.68-0.679,1.781-0.68,2.461,0c4.054,4.054,9.443,6.286,15.176,6.286c5.733,0,11.122-2.232,15.176-6.286
	c4.054-4.054,6.286-9.443,6.286-15.176S40.944,7.592,36.89,3.538c-0.68-0.68-0.68-1.781,0-2.461c0.68-0.68,1.782-0.679,2.461,0
	c4.711,4.711,7.305,10.974,7.305,17.637s-2.594,12.926-7.305,17.637S28.376,43.656,21.714,43.656z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.GlobeError()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M24.159,40.351h-3.951c0.957,5.788-2.336,6.618-7.968,6.618V50h7.163h5.229h6.759v-3.031
	C25.76,46.969,23.201,46.142,24.159,40.351"/>
<rect x="12.242" y="46.968" fill="#A0A1A2" width="19.151" height="3.032"/>
<path fill="#EC008C" d="M35.12,5.48c-3.17-3.17-7.195-4.963-11.332-5.383C18.386-0.452,12.791,1.341,8.652,5.48
	C4.513,9.619,2.72,15.214,3.268,20.616c0.42,4.138,2.213,8.162,5.383,11.332c4.898,4.898,11.833,6.508,18.077,4.842
	c3.074-0.82,5.981-2.431,8.392-4.842c3.879-3.879,5.699-9.038,5.461-14.117C40.37,13.34,38.551,8.91,35.12,5.48z"/>
<path fill="#A0A1A2" d="M21.886,43.656c-6.662,0-12.926-2.594-17.637-7.305c-0.68-0.68-0.68-1.781,0-2.461
	c0.68-0.679,1.781-0.68,2.461,0c4.054,4.054,9.443,6.286,15.176,6.286c5.733,0,11.122-2.232,15.176-6.286
	c4.054-4.054,6.286-9.443,6.286-15.176S41.116,7.592,37.062,3.538c-0.68-0.68-0.68-1.781,0-2.461c0.68-0.68,1.782-0.679,2.461,0
	c4.711,4.711,7.305,10.974,7.305,17.637s-2.594,12.926-7.305,17.637S28.548,43.656,21.886,43.656z"/>
<path opacity="0.2" fill="#1E1E1E" d="M31.612,28.442c-3.221,3.222-7.105,5.373-11.211,6.469c-2.025,0.54-4.105,0.818-6.187,0.842
	c3.95,1.775,8.379,2.142,12.515,1.038c3.074-0.82,5.981-2.431,8.392-4.842c3.879-3.879,5.699-9.038,5.461-14.117
	c-0.111-2.361-0.679-4.7-1.679-6.891C38.857,17.279,36.449,23.605,31.612,28.442z"/>
<circle fill="#FFFFFF" cx="21.817" cy="27.096" r="2.904"/>
<polygon fill="#FFFFFF" points="22.25,9.379 21.383,9.379 19.213,9.379 19.988,22.846 21.383,22.846 22.25,22.846 23.646,22.846 
	24.421,9.379 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.GlobeSuccess()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M24.159,40.351h-3.951c0.957,5.788-2.336,6.618-7.968,6.618V50h7.163h5.229h6.759v-3.031
	C25.76,46.969,23.201,46.142,24.159,40.351"/>
<rect x="12.242" y="46.968" fill="#A0A1A2" width="19.151" height="3.032"/>
<path fill="#7FBA00" d="M35.12,5.48c-3.17-3.17-7.195-4.963-11.332-5.383C18.386-0.452,12.791,1.341,8.652,5.48
	C4.513,9.619,2.72,15.214,3.268,20.616c0.42,4.138,2.213,8.162,5.383,11.332c4.898,4.898,11.833,6.508,18.077,4.842
	c3.074-0.82,5.981-2.431,8.392-4.842c3.879-3.879,5.699-9.038,5.461-14.117C40.37,13.34,38.551,8.91,35.12,5.48z"/>
<path fill="#A0A1A2" d="M21.886,43.656c-6.662,0-12.926-2.594-17.637-7.305c-0.68-0.68-0.68-1.781,0-2.461
	c0.68-0.679,1.781-0.68,2.461,0c4.054,4.054,9.443,6.286,15.176,6.286c5.733,0,11.122-2.232,15.176-6.286
	c4.054-4.054,6.286-9.443,6.286-15.176S41.116,7.592,37.062,3.538c-0.68-0.68-0.68-1.781,0-2.461c0.68-0.68,1.782-0.679,2.461,0
	c4.711,4.711,7.305,10.974,7.305,17.637s-2.594,12.926-7.305,17.637S28.548,43.656,21.886,43.656z"/>
<polygon fill="#FFFFFF" points="11.175,18.713 11.175,18.713 13.85,16.034 13.85,16.034 13.85,16.034 19.208,21.392 29.925,10.676 
	29.925,10.676 29.925,10.676 32.6,13.355 32.6,13.355 32.6,13.355 19.208,26.751 "/>
<path opacity="0.2" fill="#1E1E1E" d="M20.401,34.911c-2.025,0.54-4.105,0.818-6.187,0.842c3.95,1.775,8.379,2.142,12.515,1.038
	c3.074-0.82,5.981-2.431,8.392-4.842c3.879-3.879,5.699-9.038,5.461-14.117c-0.111-2.361-0.679-4.7-1.679-6.891
	c-0.045,6.339-2.454,12.665-7.29,17.501C28.391,31.663,24.507,33.815,20.401,34.911z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.GlobeWarning()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M24.159,40.351h-3.951c0.957,5.788-2.336,6.618-7.968,6.618V50h7.163h5.229h6.759v-3.031
	C25.76,46.969,23.201,46.142,24.159,40.351"/>
<rect x="12.242" y="46.968" fill="#A0A1A2" width="19.151" height="3.032"/>
<path fill="#FF8C00" d="M35.12,5.48c-3.17-3.17-7.195-4.963-11.332-5.383C18.386-0.452,12.791,1.341,8.652,5.48
	C4.513,9.619,2.72,15.214,3.268,20.616c0.42,4.138,2.213,8.162,5.383,11.332c4.898,4.898,11.833,6.508,18.077,4.842
	c3.074-0.82,5.981-2.431,8.392-4.842c3.879-3.879,5.699-9.038,5.461-14.117C40.37,13.34,38.551,8.91,35.12,5.48z"/>
<path opacity="0.2" fill="#1E1E1E" d="M31.612,28.442c-3.221,3.222-7.105,5.373-11.211,6.469c-2.025,0.54-4.105,0.818-6.187,0.842
	c3.95,1.775,8.378,2.142,12.515,1.038c3.074-0.82,5.981-2.431,8.392-4.842c3.879-3.879,5.699-9.038,5.461-14.117
	c-0.111-2.361-0.679-4.7-1.679-6.891C38.857,17.279,36.449,23.605,31.612,28.442z"/>
<path fill="#A0A1A2" d="M21.886,43.656c-6.662,0-12.926-2.594-17.637-7.305c-0.68-0.68-0.68-1.781,0-2.461
	c0.68-0.679,1.781-0.68,2.461,0c4.054,4.054,9.443,6.286,15.176,6.286c5.733,0,11.122-2.232,15.176-6.286
	c4.054-4.054,6.286-9.443,6.286-15.176S41.116,7.592,37.062,3.538c-0.68-0.68-0.68-1.781,0-2.461c0.68-0.68,1.782-0.679,2.461,0
	c4.711,4.711,7.305,10.974,7.305,17.637s-2.594,12.926-7.305,17.637S28.548,43.656,21.886,43.656z"/>
<path fill="#FFFFFF" d="M32.932,24.446l-4.743-8.137l-4.808-8.25c-0.741-1.275-2.255-1.257-3.016,0.02l-4.742,7.928l-4.791,8.419
	c-0.746,1.275,0.051,2.845,1.562,2.845h9.495h9.49C32.926,27.271,33.687,25.716,32.932,24.446z M21.733,13.229h0.464h1.159
	l-0.416,7.182h-0.743h-0.464h-0.743l-0.413-7.182H21.733z M21.886,24.229c-0.856,0-1.55-0.694-1.55-1.55
	c0-0.856,0.694-1.55,1.55-1.55c0.856,0,1.55,0.694,1.55,1.55C23.436,23.535,22.742,24.229,21.886,24.229z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Grid()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="5" y="5" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="15.377" y="5" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="25.728" y="5" fill="#B8D432" width="8.895" height="8.792"/>
<rect x="36.132" y="5" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="5" y="15.402" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="15.377" y="15.402" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="25.728" y="15.402" fill="#B8D432" width="8.895" height="8.792"/>
<rect x="36.132" y="15.402" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="5" y="25.804" fill="#B8D432" width="8.868" height="8.917"/>
<rect x="15.377" y="25.804" fill="#B8D432" width="8.868" height="8.917"/>
<rect x="25.728" y="25.804" fill="#B8D432" width="8.895" height="8.917"/>
<rect x="36.132" y="25.804" fill="#B8D432" width="8.868" height="8.917"/>
<rect x="5" y="36.208" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="15.377" y="36.208" fill="#B8D432" width="8.868" height="8.792"/>
<rect x="25.728" y="36.208" fill="#B8D432" width="8.895" height="8.792"/>
<rect x="36.132" y="36.208" fill="#B8D432" width="8.868" height="8.792"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Guide()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FFFFFF" d="M22.83,4.368C17.124,4.97,12.221,7.816,8.877,11.947c-3.345,4.134-5.106,9.52-4.508,15.227
	c0.604,5.703,3.449,10.604,7.581,13.949c4.134,3.346,9.52,5.109,15.222,4.509c5.705-0.601,10.606-3.451,13.955-7.581
	c3.34-4.132,5.106-9.519,4.505-15.225c-0.608-5.751-3.528-10.669-7.724-14.064c-3.531-2.859-8.032-4.51-12.856-4.51
	C24.319,4.252,23.578,4.29,22.83,4.368"/>
<path fill="#A0A1A2" d="M49.859,22.381c-0.728-6.937-4.271-12.883-9.273-16.928c-0.971-0.786-2.018-1.472-3.1-2.1L35.523,7.13
	c0.83,0.493,1.636,1.024,2.385,1.632c4.196,3.395,7.116,8.312,7.723,14.063c0.602,5.706-1.165,11.093-4.504,15.226
	c-3.349,4.131-8.25,6.98-13.955,7.581c-3.82,0.4-7.489-0.277-10.744-1.761l-1.961,3.774c3.976,1.851,8.479,2.708,13.151,2.214
	c6.862-0.719,12.796-4.164,16.814-9.133C48.453,35.763,50.587,29.239,49.859,22.381"/>
<path fill="#A0A1A2" d="M11.951,41.123c-4.133-3.346-6.978-8.247-7.581-13.949C3.771,21.466,5.532,16.08,8.878,11.946
	c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776c-4.416-2.564-9.661-3.787-15.105-3.214
	C15.522,0.861,9.588,4.305,5.569,9.27L5.562,9.279c-4.018,4.965-6.144,11.484-5.423,18.338c0.723,6.859,4.164,12.796,9.135,16.815
	c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774C14.826,43.14,13.315,42.227,11.951,41.123"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M11.951,41.123c-4.133-3.346-6.978-8.247-7.581-13.949
	C3.771,21.466,5.532,16.08,8.878,11.946c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776
	c-4.416-2.564-9.661-3.787-15.105-3.214C15.522,0.861,9.588,4.305,5.569,9.27L5.562,9.279c-4.018,4.965-6.144,11.484-5.423,18.338
	c0.723,6.859,4.164,12.796,9.135,16.815c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774C14.826,43.14,13.315,42.227,11.951,41.123
	"/>
<polygon fill="#BA141A" points="29.678,27.103 25,12.803 25,12.802 25,12.802 25,12.801 25,12.803 20.322,27.104 25,27.104 
	25,27.103 "/>
<polygon fill="#3E3E3E" points="25,27.103 25,27.103 20.322,27.103 25,41.406 25,41.406 29.678,27.103 "/>
<path d="M27.834,12.422h-1.433l-2.596-3.959c-0.154-0.231-0.258-0.406-0.318-0.523h-0.016c0.022,0.234,0.035,0.574,0.035,1.019
	v3.463h-1.341V6.12h1.529l2.5,3.837l0.318,0.514h0.016c-0.022-0.153-0.035-0.442-0.035-0.865V6.12h1.341
	C27.834,6.12,27.834,12.422,27.834,12.422z"/>
<polygon opacity="0.3" fill="#FFFFFF" enable-background="new    " points="29.678,27.103 29.678,27.103 29.678,27.103 25,12.801 
	25,27.103 25,27.103 25,41.406 29.678,27.103 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Heart()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#BA141A" d="M43.701,8.79c-0.8-0.872-1.755-1.513-2.807-1.95c-4.855-2.022-11.869,0.495-16.261,6.731
	C17.876,5.42,9.798,3.89,5.302,8.79c-8.986,9.784,2.267,22.64,11.143,29.938c3.498,2.877,6.627,4.891,8.003,5.514v0.064
	c0.012-0.002,0.038-0.023,0.047-0.025c0,0,0.049,0.023,0.066,0.025v-0.064C29.417,42.04,56.222,22.432,43.701,8.79"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M24.632,13.572C17.876,5.421,9.8,3.889,5.302,8.792
	C-3.684,18.575,7.57,31.43,16.445,38.729L40.894,6.841C36.038,4.819,29.025,7.336,24.632,13.572"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Image()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect y="5" fill="#7A7A7A" width="50" height="39"/>
<rect x="2.2" y="7.2" fill="#59B4D9" width="45.7" height="34.8"/>
<rect x="2.2" y="7.2" fill="#59B4D9" width="45.7" height="34.8"/>
<path fill="#B8D432" d="M47.8,27.3L44.5,24c-1.3-1.3-3.4-1.3-4.8,0L21.9,42h26V27.3z"/>
<path opacity="0.25" fill="#FFFFFF" d="M47.8,27.3L44.5,24c-1.3-1.3-3.4-1.3-4.8,0L21.9,42h26V27.3z"/>
<path fill="#7FBA00" d="M43.1,42l-14-14c-1-1-2.7-1-3.7,0l-14,14H43.1z"/>
<path opacity="0.25" fill="#FFFFFF" d="M43.1,42l-14-14c-1-1-2.7-1-3.7,0l-14,14H43.1z"/>
<path opacity="0.63" fill="#FFFFFF" d="M27.6,22.3c0-1.4-1.1-2.5-2.4-2.5c-0.1,0-0.2,0-0.3,0c0.1-0.6,0.2-1.1,0.2-1.7
	c0-3.6-2.9-6.6-6.5-6.6c-2.9,0-5.3,1.9-6.2,4.5c-0.5-0.2-0.9-0.3-1.5-0.3c-2.5,0-4.4,2-4.4,4.5c0,2.5,2,4.5,4.4,4.5c0,0,0,0,0,0v0
	h14.4l0,0C26.6,24.6,27.6,23.6,27.6,22.3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Info()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M47.247,25c0,12.287-9.96,22.246-22.247,22.246S2.753,37.287,2.753,25S12.713,2.753,25,2.753
	C37.286,2.753,47.247,12.714,47.247,25"/>
<path fill="#59B4D9" d="M25,2.753C12.713,2.753,2.753,12.714,2.753,25c0,5.266,1.84,10.096,4.897,13.906L40.42,8.986
	C36.42,5.135,30.992,2.753,25,2.753"/>
<path fill="#FFFFFF" d="M28.867,19.518v16.098c0,1.434,0.166,2.35,0.499,2.749c0.333,0.398,0.985,0.626,1.956,0.684v0.782H20.35
	v-0.782c0.898-0.029,1.564-0.29,1.999-0.782c0.289-0.333,0.434-1.217,0.434-2.651V23.754c0-1.433-0.166-2.349-0.498-2.748
	c-0.334-0.398-0.979-0.627-1.935-0.684v-0.804H28.867z"/>
<path fill="#FFFFFF" d="M25.826,9.677c0.94,0,1.737,0.33,2.39,0.989c0.651,0.659,0.976,1.451,0.976,2.378s-0.329,1.717-0.987,2.369
	c-0.659,0.651-1.453,0.977-2.379,0.977c-0.928,0-1.717-0.326-2.369-0.977c-0.652-0.652-0.978-1.442-0.978-2.369
	s0.326-1.719,0.978-2.378S24.898,9.677,25.826,9.677"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.InputOutput()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#804998" points="17.7,23.411 12.811,18.256 16.789,18.256 23.333,24.978 16.822,31.667 12.844,31.667 17.7,26.511 
	0,26.511 0,23.411 "/>
<polygon fill="#59B4D9" points="44.367,23.411 39.478,18.256 43.456,18.256 50,24.978 43.489,31.667 39.511,31.667 44.367,26.511 
	26.667,26.511 26.667,23.411 "/>
<polygon fill="#A0A1A2" points="36.667,40.889 5.556,40.889 5.556,30.556 10,30.556 10,36.444 32.222,36.444 32.222,30.556 
	36.667,30.556 "/>
<polygon fill="#A0A1A2" points="36.667,19.444 32.222,19.444 32.222,12.444 10,12.444 10,19.444 5.556,19.444 5.556,8 36.667,8 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.IPAddress()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M0.5,45.127c0,1.108,0.898,2.007,2.007,2.007h45.986c1.109,0,2.007-0.899,2.007-2.007V13.798h-50V45.127z"/>
<path fill="#A0A1A2" d="M48.493,4.5H2.507C1.398,4.5,0.5,5.398,0.5,6.507v10.627h50V6.507C50.5,5.398,49.601,4.5,48.493,4.5"/>
<path opacity="0.2" fill="#FFFFFF" d="M2.514,4.5c-1.108,0-2.007,0.898-2.007,2.007v7.291v3.336v27.993
	c0,1.108,0.899,2.007,2.007,2.007h2.188L44.12,4.5H2.514z"/>
<rect x="13.357" y="9.279" fill="#FFFFFF" width="33.671" height="3.942"/>
<path fill="#59B4D9" d="M11.81,11.183c0,2.693-2.184,4.878-4.878,4.878c-2.694,0-4.878-2.185-4.878-4.878
	c0-2.694,2.184-4.879,4.878-4.879C9.625,6.304,11.81,8.489,11.81,11.183"/>
<polygon fill="#FFFFFF" points="6.416,11.732 8.629,14.068 7.428,14.068 4.469,11.25 7.417,8.432 8.615,8.432 6.416,10.754 
	11.809,10.754 11.809,11.732 "/>
<path fill="#FFFFFF" d="M17.926,36.576c0,0.57-0.203,1.037-0.609,1.4s-0.941,0.545-1.605,0.545c-0.594,0-1.098-0.186-1.512-0.557
	s-0.621-0.834-0.621-1.389c0-0.563,0.207-1.023,0.621-1.383s0.938-0.539,1.57-0.539c0.609,0,1.121,0.184,1.535,0.551
	S17.926,36.029,17.926,36.576z"/>
<path fill="#FFFFFF" d="M26.83,36.576c0,0.57-0.203,1.037-0.609,1.4s-0.941,0.545-1.605,0.545c-0.594,0-1.098-0.186-1.512-0.557
	s-0.621-0.834-0.621-1.389c0-0.563,0.207-1.023,0.621-1.383s0.938-0.539,1.57-0.539c0.609,0,1.121,0.184,1.535,0.551
	S26.83,36.029,26.83,36.576z"/>
<path fill="#FFFFFF" d="M35.733,36.576c0,0.57-0.203,1.037-0.609,1.4s-0.941,0.545-1.605,0.545c-0.594,0-1.098-0.186-1.512-0.557
	s-0.621-0.834-0.621-1.389c0-0.563,0.207-1.023,0.621-1.383s0.938-0.539,1.57-0.539c0.609,0,1.121,0.184,1.535,0.551
	S35.733,36.029,35.733,36.576z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.JourneyHub()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="2" fill="#FCD116" width="14" height="50"/>
<rect x="18" fill="#B8D432" width="14" height="50"/>
<rect x="34" fill="#59B4D9" width="14" height="50"/>
<rect x="2" y="0" opacity="0.6" fill="#FF8C00" enable-background="new    " width="14" height="8.5"/>
<rect x="18" y="0" fill="#7FBA00" width="14" height="8.5"/>
<rect x="34" y="0" fill="#3999C6" width="14" height="8.5"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Key()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FCD116" d="M39.337,19.525c2.222-2.22,2.222-5.824,0-8.045l-9.812-9.813c-2.222-2.222-5.824-2.222-8.046,0l-9.813,9.813
	c-2.221,2.221-2.221,5.825,0,8.045l8.633,8.633v16.641l5.202,5.202l4.519-4.519v-0.033l2.652-2.653l-2.629-2.629l2.629-2.629
	l-2.629-2.629l2.629-2.629l-2.652-2.653v-0.784L39.337,19.525z M25.502,4.039c1.783,0,3.229,1.446,3.229,3.229
	c0,1.784-1.446,3.229-3.229,3.229c-1.783,0-3.229-1.445-3.229-3.229S23.719,4.039,25.502,4.039z"/>
<polygon opacity="0.4" fill="#FF8C00" enable-background="new    " points="22.728,43.961 24.758,45.961 24.758,30.008 
	22.728,29.008 "/>
<rect x="15.868" y="13.617" opacity="0.5" fill="#FFFFFF" enable-background="new    " width="18.64" height="2.679"/>
<rect x="15.868" y="17.976" opacity="0.5" fill="#FFFFFF" enable-background="new    " width="18.64" height="2.679"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.LaunchPortal()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" fill="#B8D432" d="M20.4,19.2v7.2H3.6V9.6h7.198V6H2.4C1.075,6,0,7.075,0,8.4v19.2
	C0,28.925,1.075,30,2.4,30h19.2c1.325,0,2.4-1.075,2.4-2.4v-8.4H20.4z"/>
<polygon fill="#FCD116" points="30,0 19.2,0 23.221,4.021 7.835,19.406 10.629,22.2 26.015,6.815 30,10.8 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.LoadBalancer()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7FBA00" d="M25.001,50c-1.232,0-2.392-0.48-3.261-1.352L1.351,28.26C0.492,27.401,0,26.214,0,24.999
	c0-1.214,0.492-2.402,1.351-3.26L21.74,1.351c0.871-0.872,2.029-1.352,3.261-1.352c1.231,0,2.39,0.48,3.261,1.352l20.386,20.388
	C49.521,22.607,50,23.766,50,24.999c0,1.233-0.479,2.392-1.353,3.263L28.262,48.648C27.392,49.52,26.232,50,25.001,50"/>
<path fill="#FFFFFF" d="M45.613,24.66L39,18.048v4.668l-7.016-0.006c-0.677-2.418-2.573-4.328-4.984-5.02V11h4.613L25,4.387
	L18.388,11H23v6.689c-2.407,0.692-4.301,2.596-4.981,5.008L11,22.691v-4.618l-6.613,6.613L11,31.298V26.63l7.022,0.006
	c0.683,2.407,2.574,4.305,4.978,4.996v4.636c-1,0.728-2.528,2.258-2.528,4.04c0,2.481,2.033,4.5,4.514,4.5s4.51-2.019,4.51-4.5
	c0-1.762-1.496-3.274-2.496-4.013v-4.663c2.399-0.689,4.289-2.583,4.975-4.983L39,26.655v4.618L45.613,24.66z"/>
<path fill="#59B4D9" d="M25,19.402c-2.899,0-5.258,2.359-5.258,5.258s2.359,5.258,5.258,5.258s5.258-2.358,5.258-5.258
	S27.899,19.402,25,19.402z"/>
<path opacity="0.15" fill="#FFFFFF" d="M28.262,1.351c-0.871-0.872-2.029-1.352-3.261-1.352c-1.231,0-2.389,0.48-3.26,1.352
	L1.352,21.739C0.492,22.597,0,23.785,0,24.999c0,1.215,0.492,2.403,1.352,3.261l11.543,11.544L34.61,7.698L28.262,1.351z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.LoadTest()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M48.288,44.623L32.72,17.659V6.743h0.281c1.862,0,3.372-1.51,3.372-3.372S34.863,0,33.001,0H16.016
	c-1.862,0-3.372,1.51-3.372,3.372s1.51,3.372,3.372,3.372h0.281v10.915L0.729,44.623C-0.979,47.58,0.418,50,3.833,50h41.351
	C48.598,50,49.995,47.58,48.288,44.623z"/>
<polygon fill="#B8D432" points="13.551,33.017 7.127,44.143 41.889,44.143 35.466,33.017 "/>
<path fill="#7FBA00" d="M25.334,37.532c1.735,0,3.141-1.406,3.141-3.141c0-0.493-0.117-0.958-0.32-1.374h-5.643
	c-0.203,0.415-0.32,0.88-0.32,1.374C22.193,36.126,23.599,37.532,25.334,37.532z"/>
<circle fill="#7FBA00" cx="29.232" cy="39.956" r="1.541"/>
<path opacity="0.25" fill="#FFFFFF" d="M0.729,44.623l15.568-26.965V6.743h-0.281c-1.862,0-3.372-1.51-3.372-3.372
	S14.153,0,16.016,0l7.319,0v17.572L15.13,50H3.833C0.418,50-0.979,47.58,0.729,44.623z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Location()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path opacity="0.8" fill="#3E3E3E" d="M21.666,38.421c0,2.161,1.752,3.914,3.913,3.914c2.162,0,3.914-1.753,3.914-3.914
	c0-1.43-0.775-2.666-1.92-3.35c-0.662,1.107-1.106,1.822-1.193,1.96l-0.801,1.286l-0.801-1.286c-0.086-0.138-0.531-0.853-1.193-1.96
	C22.439,35.755,21.666,36.991,21.666,38.421"/>
<path opacity="0.5" fill="#3E3E3E" d="M17.668,38.421c0,4.363,3.549,7.911,7.909,7.911c4.363,0,7.912-3.548,7.912-7.911
	c0-2.896-1.569-5.426-3.895-6.804c-0.238,0.416-0.471,0.82-0.693,1.2c1.913,1.138,3.203,3.221,3.203,5.604
	c0,3.599-2.928,6.525-6.527,6.525c-3.598,0-6.524-2.926-6.524-6.525c0-2.383,1.289-4.466,3.201-5.604
	c-0.221-0.38-0.453-0.784-0.69-1.2C19.236,32.995,17.668,35.525,17.668,38.421"/>
<path opacity="0.2" fill="#3E3E3E" d="M31.381,28.417c-0.227,0.413-0.448,0.816-0.668,1.214c3.02,1.77,5.058,5.044,5.058,8.79
	c0,5.62-4.574,10.192-10.193,10.192c-5.621,0-10.192-4.572-10.192-10.192c0-3.747,2.038-7.02,5.057-8.79
	c-0.219-0.398-0.443-0.801-0.667-1.214C16.328,30.424,14,34.151,14,38.421c0,6.385,5.193,11.58,11.578,11.58
	c6.385,0,11.578-5.195,11.578-11.58C37.156,34.151,34.828,30.425,31.381,28.417"/>
<path fill="#B8D432" d="M29.492,10.553c0,2.161-1.752,3.914-3.914,3.914c-2.161,0-3.914-1.753-3.914-3.914s1.753-3.914,3.914-3.914
	C27.74,6.639,29.492,8.392,29.492,10.553 M25.579,0c-6.393,0-11.573,5.183-11.573,11.574c0,6.393,11.573,24.958,11.573,24.958
	s11.574-18.565,11.574-24.958C37.152,5.183,31.97,0,25.579,0"/>
<path opacity="0.2" fill="#FFFFFF" d="M25.579,14.466c-2.161,0-3.913-1.752-3.913-3.913c0-2.162,1.752-3.914,3.913-3.914
	c1.484,0,2.761,0.837,3.425,2.056l2.358-7.135c-1.701-0.987-3.674-1.559-5.783-1.559c-6.393,0-11.574,5.181-11.574,11.572
	c0,3.98,4.484,12.673,7.87,18.672l5.342-16.148C26.716,14.328,26.166,14.466,25.579,14.466"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Log()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-10.5 -9.5 50 50" enable-background="new -10.5 -9.5 50 50" xml:space="preserve">
<path fill="#0072C6" d="M34.492,35.865h2V-9.5H-1.841h-2.667C-5.801-9.333-8.5-6.2-8.5-5.749c0,0.187,0.059,43.882,0.059,43.882
	c0,1.307,1.06,2.367,2.368,2.367h37.565v-0.729L34.492,35.865z"/>
<path fill="#E5E5E5" d="M-2.726-7.5c-1.105,0-1.58,0.185-2.507,1c-2.275,2,0.39,2,1.495,2h35.23v44.271l3-3.906V-7.5H-2.726z"/>
<polygon opacity="0.5" fill="#A0A1A2" enable-background="new    " points="31.492,39.771 34.492,35.865 34.492,-7.5 31.492,-4.5 
	"/>
<path fill="#FFFFFF" d="M22.492,10.5c0,1.105-0.895,2-2,2h-17c-1.105,0-2-0.895-2-2v-5c0-1.105,0.895-2,2-2h17c1.105,0,2,0.895,2,2
	V10.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.LogDiagnostics()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-10.5 -9.5 50 50" enable-background="new -10.5 -9.5 50 50" xml:space="preserve">
<path fill="#7FBA00" d="M34.492,35.865h2V-9.5H-1.841h-2.667C-5.801-9.333-8.5-6.2-8.5-5.749c0,0.187,0.059,43.882,0.059,43.882
	c0,1.307,1.06,2.367,2.368,2.367h37.565v-0.729L34.492,35.865z"/>
<path fill="#E5E5E5" d="M-2.726-7.5c-1.105,0-1.58,0.185-2.507,1c-2.275,2,0.39,2,1.495,2h35.23v44.271l3-3.906V-7.5H-2.726z"/>
<polygon opacity="0.5" fill="#A0A1A2" enable-background="new    " points="31.492,39.771 34.492,35.865 34.492,-7.5 31.492,-4.5 
	"/>
<g>
	<path fill="#FFFFFF" d="M15.417,29.203l-4.302-17.581l-2.241,6.076H0.492c-0.828,0-1.5-0.671-1.5-1.5s0.672-1.5,1.5-1.5h6.29
		l4.915-13.32l4.507,18.419l2.197-5.099h7.091c0.828,0,1.5,0.671,1.5,1.5s-0.672,1.5-1.5,1.5h-5.116L15.417,29.203z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.LogStreaming()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-10.5 -9.5 50 50" enable-background="new -10.5 -9.5 50 50" xml:space="preserve">
<path fill="#FF8C00" d="M34.5,35.865h2V-9.5H-1.833H-4.5c-1.293,0.167-3.992,3.3-3.992,3.751c0,0.187,0.059,43.882,0.059,43.882
	c0,1.307,1.06,2.367,2.368,2.367H31.5v-0.729L34.5,35.865z"/>
<path fill="#E5E5E5" d="M-2.718-7.5c-1.105,0-1.58,0.185-2.507,1c-2.275,2,0.39,2,1.495,2H31.5v44.271l3-3.906V-7.5H-2.718z"/>
<polygon opacity="0.5" fill="#A0A1A2" enable-background="new    " points="31.5,39.771 34.5,35.865 34.5,-7.5 31.5,-4.5 "/>
<g>
	<path fill="#FFFFFF" d="M7.048,17.125c0-1.332,0.533-2.542,1.396-3.428L6.548,11.8c-1.349,1.373-2.183,3.253-2.183,5.325
		c0,2.096,0.853,3.996,2.229,5.373l1.896-1.896C7.6,19.71,7.048,18.481,7.048,17.125z"/>
	<path fill="#FFFFFF" d="M16.886,17.125c0,1.356-0.552,2.586-1.442,3.477l1.896,1.896c1.377-1.377,2.229-3.277,2.229-5.373
		c0-2.072-0.834-3.952-2.183-5.325l-1.896,1.896C16.353,14.583,16.886,15.792,16.886,17.125z"/>
	<path fill="#FFFFFF" d="M21.134,17.125c0,2.527-1.028,4.819-2.688,6.479l1.896,1.896c2.146-2.146,3.475-5.108,3.475-8.375
		c0-3.243-1.311-6.185-3.429-8.327l-1.896,1.896C20.125,12.35,21.134,14.621,21.134,17.125z"/>
	<path fill="#FFFFFF" d="M2.8,17.125c0-2.503,1.01-4.775,2.642-6.431L3.546,8.797c-2.118,2.142-3.429,5.084-3.429,8.327
		c0,3.267,1.329,6.23,3.475,8.375l1.896-1.896C3.828,21.944,2.8,19.652,2.8,17.125z"/>
	<path fill="#FFFFFF" d="M14.225,14.961c-0.57-0.594-1.37-0.966-2.258-0.966c-0.888,0-1.688,0.372-2.258,0.966
		c-0.539,0.562-0.872,1.324-0.872,2.164c0,0.864,0.35,1.647,0.917,2.213c0.566,0.567,1.349,0.917,2.213,0.917
		c0.864,0,1.647-0.35,2.213-0.917c0.567-0.566,0.917-1.349,0.917-2.213C15.097,16.284,14.764,15.523,14.225,14.961z"/>
</g>
<path fill="#FF8C00" d="M34.5,35.865h2V-9.5H-1.833H-4.5c-1.293,0.167-3.992,3.3-3.992,3.751c0,0.187,0.059,43.882,0.059,43.882
	c0,1.307,1.06,2.367,2.368,2.367H31.5v-0.729L34.5,35.865z"/>
<path fill="#E5E5E5" d="M-2.718-7.5c-1.105,0-1.58,0.185-2.507,1c-2.275,2,0.39,2,1.495,2H31.5v44.271l3-3.906V-7.5H-2.718z"/>
<polygon opacity="0.5" fill="#A0A1A2" enable-background="new    " points="31.5,39.771 34.5,35.865 34.5,-7.5 31.5,-4.5 "/>
<g>
	<path fill="#FFFFFF" d="M7.048,17.125c0-1.332,0.533-2.542,1.396-3.428L6.548,11.8c-1.349,1.373-2.183,3.253-2.183,5.325
		c0,2.096,0.853,3.996,2.229,5.373l1.896-1.896C7.6,19.71,7.048,18.481,7.048,17.125z"/>
	<path fill="#FFFFFF" d="M16.886,17.125c0,1.356-0.552,2.586-1.442,3.477l1.896,1.896c1.377-1.377,2.229-3.277,2.229-5.373
		c0-2.072-0.834-3.952-2.183-5.325l-1.896,1.896C16.353,14.583,16.886,15.792,16.886,17.125z"/>
	<path fill="#FFFFFF" d="M21.134,17.125c0,2.527-1.028,4.819-2.688,6.479l1.896,1.896c2.146-2.146,3.475-5.108,3.475-8.375
		c0-3.243-1.311-6.185-3.429-8.327l-1.896,1.896C20.125,12.35,21.134,14.621,21.134,17.125z"/>
	<path fill="#FFFFFF" d="M2.8,17.125c0-2.503,1.01-4.775,2.642-6.431L3.546,8.797c-2.118,2.142-3.429,5.084-3.429,8.327
		c0,3.267,1.329,6.23,3.475,8.375l1.896-1.896C3.828,21.944,2.8,19.652,2.8,17.125z"/>
	<path fill="#FFFFFF" d="M14.225,14.961c-0.57-0.594-1.37-0.966-2.258-0.966c-0.888,0-1.688,0.372-2.258,0.966
		c-0.539,0.562-0.872,1.324-0.872,2.164c0,0.864,0.35,1.647,0.917,2.213c0.566,0.567,1.349,0.917,2.213,0.917
		c0.864,0,1.647-0.35,2.213-0.917c0.567-0.566,0.917-1.349,0.917-2.213C15.097,16.284,14.764,15.523,14.225,14.961z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Media()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#3E3E3E" points="24.65,50 3,37.5 3,12.5 24.65,0 46.301,12.5 46.301,37.5 "/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M24.651,50L46.3,37.5v-25C46.3,12.5,28.413,26.12,24.651,50"/>
<path fill="#FFFFFF" d="M34.172,14.958c-5.55-5.526-14.518-5.526-20.074,0c-5.549,5.549-5.549,14.535,0,20.084
	c5.549,5.525,14.524,5.525,20.074,0C39.715,29.497,39.715,20.511,34.172,14.958"/>
<path fill="#59B4D9" d="M32.227,16.903c-4.474-4.456-11.705-4.456-16.185,0c-4.473,4.474-4.473,11.719,0,16.192
	c4.475,4.456,11.711,4.456,16.185,0C36.697,28.626,36.697,21.38,32.227,16.903"/>
<polygon fill="#FFFFFF" points="31.029,24.986 20.127,17.816 20.127,25.015 30.986,25.015 "/>
<polygon opacity="0.8" fill="#FFFFFF" enable-background="new    " points="30.986,25.015 20.127,25.015 20.127,32.213 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.MediaFile()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#59B4D9" points="45,6.761 40.239,2 38.239,0 38,0 3,0 3,50 47,50 47,9 47,8.761 "/>
<polygon opacity="0.8" fill="#FFFFFF" points="38,2 5,2 5,48 45,48 45,9 38,9 "/>
<path fill="#59B4D9" d="M38.857,24.738c0,0.618-0.501,1.119-1.119,1.119H22.637c-0.618,0-1.119-0.501-1.119-1.119
	c0-0.618,0.501-1.119,1.119-1.119h15.101C38.356,23.619,38.857,24.12,38.857,24.738"/>
<polygon fill="#59B4D9" points="11.509,11.203 24.765,18.844 11.509,26.485 "/>
<path fill="#59B4D9" d="M38.857,29.89c0,0.618-0.501,1.118-1.119,1.118h-25.11c-0.617,0-1.118-0.5-1.118-1.118
	c0-0.618,0.501-1.119,1.118-1.119h25.11C38.356,28.771,38.857,29.272,38.857,29.89"/>
<path fill="#59B4D9" d="M38.857,35.042c0,0.618-0.501,1.119-1.119,1.119h-25.11c-0.617,0-1.118-0.501-1.118-1.119
	c0-0.617,0.501-1.119,1.118-1.119h25.11C38.356,33.923,38.857,34.425,38.857,35.042"/>
<path fill="#59B4D9" d="M38.857,40.194c0,0.618-0.501,1.119-1.119,1.119h-25.11c-0.617,0-1.118-0.501-1.118-1.119
	c0-0.618,0.501-1.119,1.118-1.119h25.11C38.356,39.075,38.857,39.576,38.857,40.194"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Mobile()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M42.445,47c0,1.657-1.343,3-3,3H11c-1.657,0-3-1.343-3-3V3c0-1.657,1.343-3,3-3h28.445c1.657,0,3,1.343,3,3
	V47z"/>
<rect x="10.224" y="5" fill="#59B4D9" width="30" height="35.222"/>
<path fill="#FFFFFF" d="M28.112,45.11c0,1.596-1.294,2.889-2.89,2.889c-1.594,0-2.888-1.293-2.888-2.889
	c0-1.595,1.294-2.889,2.888-2.889C26.818,42.221,28.112,43.515,28.112,45.11"/>
<path fill="#B8D432" d="M27.117,45.11c0,1.046-0.848,1.895-1.895,1.895c-1.045,0-1.893-0.849-1.893-1.895
	c0-1.046,0.848-1.894,1.893-1.894C26.269,43.216,27.117,44.064,27.117,45.11"/>
<path opacity="0.15" fill="#FFFFFF" d="M10.223,40.222V5H32.99l2.031-5H11C9.343,0,8,1.343,8,3v44c0,1.658,1.343,3,3,3h3.695
	l3.974-9.778H10.223z"/>
<path fill="#1E1E1E" d="M30.334,2.817c0,0.408-0.33,0.738-0.738,0.738h-8.744c-0.409,0-0.74-0.33-0.74-0.738
	c0-0.408,0.331-0.739,0.74-0.739h8.744C30.004,2.078,30.334,2.409,30.334,2.817"/>
<path fill="#FFFFFF" d="M30.334,2.817c0,0.408-0.33,0.738-0.738,0.738h-8.744c-0.409,0-0.74-0.33-0.74-0.738
	c0-0.408,0.331-0.739,0.74-0.739h8.744C30.004,2.078,30.334,2.409,30.334,2.817"/>
<path fill="#FFFFFF" d="M25.251,21.311c-0.045,0-0.091-0.014-0.134-0.038l-8.804-5.082c-0.081-0.048-0.132-0.137-0.132-0.231
	c0-0.095,0.051-0.183,0.132-0.23l8.751-5.049c0.082-0.046,0.182-0.046,0.263,0l8.807,5.084c0.082,0.047,0.131,0.135,0.131,0.23
	c0,0.096-0.049,0.183-0.131,0.23l-8.748,5.048C25.343,21.297,25.3,21.311,25.251,21.311"/>
<path opacity="0.7" fill="#FFFFFF" d="M23.987,33.663c-0.05,0-0.095-0.012-0.134-0.036l-8.778-5.066
	c-0.085-0.047-0.136-0.133-0.136-0.231V18.164c0-0.096,0.051-0.183,0.136-0.231c0.081-0.049,0.181-0.049,0.268,0l8.777,5.064
	c0.078,0.05,0.13,0.137,0.13,0.233v10.166c0,0.097-0.052,0.183-0.13,0.231C24.076,33.651,24.03,33.663,23.987,33.663"/>
<path opacity="0.4" fill="#FFFFFF" d="M26.471,33.663c-0.048,0-0.093-0.012-0.138-0.036c-0.078-0.048-0.129-0.134-0.129-0.231
	V23.294c0-0.094,0.051-0.182,0.129-0.231l8.777-5.064c0.084-0.048,0.182-0.048,0.264,0c0.084,0.047,0.135,0.135,0.135,0.23V28.33
	c0,0.098-0.051,0.184-0.135,0.231L26.6,33.627C26.564,33.651,26.517,33.663,26.471,33.663"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Module()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="3" y="3" fill="#804998" width="11.704" height="11.541"/>
<rect x="19.107" y="3" fill="#804998" width="11.746" height="11.541"/>
<rect x="35.296" y="3" fill="#804998" width="11.704" height="11.541"/>
<rect x="3" y="19.133" fill="#804998" width="27.853" height="11.541"/>
<rect x="35.296" y="19.133" fill="#804998" width="11.704" height="11.541"/>
<rect x="3" y="19.133" opacity="0.2" fill="#FFFFFF" width="27.853" height="11.541"/>
<rect x="35.296" y="19.133" opacity="0.2" fill="#FFFFFF" width="11.704" height="11.541"/>
<rect x="3" y="35.266" fill="#804998" width="44" height="11.734"/>
<rect x="3" y="35.266" opacity="0.4" fill="#FFFFFF" width="44" height="11.734"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Monitoring()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FFFFFF" d="M22.83,4.37c-5.706,0.602-10.609,3.448-13.953,7.579c-3.345,4.134-5.106,9.52-4.508,15.227
	c0.604,5.703,3.449,10.604,7.581,13.949c4.134,3.346,9.52,5.109,15.222,4.509c5.705-0.601,10.606-3.451,13.955-7.581
	c3.34-4.132,5.106-9.519,4.505-15.225c-0.608-5.751-3.528-10.669-7.724-14.064c-3.531-2.859-8.032-4.51-12.856-4.51
	C24.319,4.254,23.578,4.292,22.83,4.37"/>
<path opacity="0.4" fill="#7FBA00" enable-background="new    " d="M16.456,19.472l-5.048-4.086
	c-2.158,3.088-3.137,6.688-2.991,10.232l6.454-0.679C14.871,23.049,15.392,21.151,16.456,19.472"/>
<path opacity="0.8" fill="#7FBA00" enable-background="new    " d="M30.575,16.279l4.087-5.049c-3.09-2.156-6.693-3.13-10.238-2.982
	l0.679,6.453C26.994,14.699,28.894,15.217,30.575,16.279"/>
<path opacity="0.6" fill="#7FBA00" enable-background="new    " d="M22.853,14.956l-0.68-6.46c-3.49,0.623-6.795,2.35-9.348,5.123
	l5.044,4.084C19.274,16.294,21.012,15.371,22.853,14.956"/>
<path opacity="0.25" fill="#7FBA00" enable-background="new    " d="M15.121,27.189l-6.46,0.682c0.62,3.492,2.345,6.801,5.121,9.357
	l4.083-5.044C16.455,30.775,15.534,29.034,15.121,27.189"/>
<path fill="#A0A1A2" d="M49.859,22.383C49.131,15.446,45.588,9.5,40.586,5.455c-0.971-0.786-2.018-1.472-3.1-2.1l-1.963,3.777
	c0.83,0.493,1.636,1.024,2.385,1.632c4.196,3.395,7.116,8.312,7.723,14.063c0.602,5.706-1.165,11.093-4.504,15.226
	c-3.349,4.131-8.25,6.98-13.955,7.581c-3.82,0.4-7.489-0.277-10.744-1.761l-1.961,3.774c3.976,1.851,8.479,2.708,13.151,2.214
	c6.862-0.719,12.796-4.164,16.814-9.133C48.453,35.765,50.587,29.241,49.859,22.383"/>
<path fill="#A0A1A2" d="M11.951,41.125C7.818,37.78,4.973,32.879,4.37,27.177c-0.599-5.708,1.162-11.094,4.508-15.228
	c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776c-4.416-2.564-9.661-3.787-15.105-3.214
	C15.522,0.863,9.588,4.307,5.569,9.272L5.562,9.281c-4.018,4.965-6.144,11.484-5.423,18.338c0.723,6.859,4.164,12.796,9.135,16.815
	c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774C14.826,43.142,13.315,42.229,11.951,41.125"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M11.951,41.125C7.818,37.78,4.973,32.879,4.37,27.177
	c-0.599-5.708,1.162-11.094,4.508-15.228c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776
	c-4.416-2.564-9.661-3.787-15.105-3.214C15.522,0.863,9.588,4.307,5.569,9.272L5.562,9.281c-4.018,4.965-6.144,11.484-5.423,18.338
	c0.723,6.859,4.164,12.796,9.135,16.815c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774
	C14.826,43.142,13.315,42.229,11.951,41.125"/>
<path fill="#7FBA00" d="M36.43,12.647l-4.084,5.045c1.008,1.002,1.759,2.178,2.264,3.437h6.775
	C40.643,17.958,38.977,14.985,36.43,12.647z"/>
<path fill="#DD5900" d="M23.082,27.041c-1.147-1.136-1.154-2.986-0.018-4.131c0.573-0.578,1.327-0.866,2.079-0.866
	c0.743,0,1.487,0.248,2.057,0.813c0.007,0.007,0.01-0.008,0.019,0h7.601h6.685l0.621-0.005l0.004,0.005
	c1.173,0,2.125,0.943,2.133,2.113c0.003,1.178-0.944,2.133-2.121,2.137H27.057c-0.55,0.481-1.235,0.779-1.923,0.779
	C24.394,27.886,23.652,27.606,23.082,27.041"/>
<path fill="#FF8C00" d="M42.13,22.875l-0.004-0.005l-0.621,0.005H34.82h-7.601c-0.009-0.008-0.012,0.007-0.019,0
	c-0.57-0.565-1.314-0.813-2.057-0.813c-0.752,0-1.506,0.288-2.079,0.866c-0.564,0.569-0.845,1.312-0.846,2.055h22.044
	C44.251,23.815,43.301,22.875,42.13,22.875z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.NetworkInterfaceCard()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="3" fill="#7FBA00" width="40.205" height="50"/>
<rect x="19.19" y="4.915" width="18.921" height="11.888"/>
<rect x="43.205" y="3.846" fill="#FCD116" width="4.501" height="8.577"/>
<rect x="43.205" y="16.084" fill="#FCD116" width="4.501" height="22.825"/>
<rect x="20.809" y="31.718" width="3.243" height="13.391"/>
<rect x="15.565" y="31.718" width="3.243" height="13.391"/>
<polygon opacity="0.2" points="17.636,37.365 11.492,37.365 11.492,11.908 19.734,11.908 19.734,14.006 13.59,14.006 13.59,35.267 
	17.636,35.267 "/>
<polygon opacity="0.2" points="15.538,42.61 7.296,42.61 7.296,7.713 20.783,7.713 20.783,9.811 9.394,9.811 9.394,40.512 
	15.538,40.512 "/>
<polygon opacity="0.2" points="33.12,40.144 27.058,40.144 27.058,22.834 31.798,22.834 31.798,15.035 33.896,15.035 33.896,24.932 
	29.156,24.932 29.156,38.046 33.12,38.046 "/>
<path fill="#E5E5E5" d="M23.003,24.11c0,1.343-1.089,2.431-2.431,2.431l0,0c-1.343,0-2.431-1.089-2.431-2.431l0,0
	c0-1.343,1.089-2.431,2.431-2.431l0,0C21.915,21.678,23.003,22.767,23.003,24.11L23.003,24.11z"/>
<rect x="32.846" y="32.343" fill="#E5E5E5" width="4.322" height="12.365"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Notification()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#B8D432" d="M43.327,0H6.275C2.611-0.02,0,2.188,0,5.839v26.218c0,3.706,2.661,6.985,6.329,7.029l13.383,0.025
	L30.702,49.9l0.082-10.758h12.441c3.661,0.03,6.664-2.933,6.664-6.594l0.056-26.317C49.97,2.59,46.993,0,43.327,0"/>
<path fill="#FFFFFF" d="M28.611,27.92c0,2.025-1.642,3.666-3.666,3.666c-2.024,0-3.667-1.641-3.667-3.666
	c0-2.025,1.643-3.666,3.667-3.666C26.969,24.254,28.611,25.895,28.611,27.92"/>
<polygon fill="#FFFFFF" points="25.636,6.305 24.607,6.305 22.039,6.305 22.957,22.237 24.607,22.237 25.636,22.237 27.283,22.237 
	28.202,6.305 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Powershell()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M0,44.627c0,1.108,0.898,2.007,2.007,2.007h45.986c1.109,0,2.007-0.899,2.007-2.007V12.634H0V44.627z"/>
<path fill="#A0A1A2" d="M47.993,4H2.007C0.898,4,0,4.898,0,6.007v7.291h50V6.007C50,4.898,49.101,4,47.993,4z"/>
<rect y="13.298" opacity="0.15" fill="#FFFFFF" width="50" height="3.336"/>
<path opacity="0.1" fill="#FFFFFF" d="M2.014,4C0.906,4,0.007,4.898,0.007,6.007v7.291v3.336v27.993
	c0,1.108,0.899,2.007,2.007,2.007h19.188L43.62,4H2.014z"/>
<polygon fill="#FFFFFF" points="7.363,37.334 7.363,36.062 16.343,31.637 16.343,31.584 7.363,26.563 7.363,25.317 18.397,31.505 
	18.397,31.743 "/>
<rect x="17.621" y="41.785" fill="#FFFFFF" width="11.262" height="1.576"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.PowerUp()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FFFFFF" d="M23.844,21.87c0.132-0.091,0.264-0.182,0.395-0.263C24.107,21.688,23.976,21.779,23.844,21.87L23.844,21.87z
	"/>
<polygon fill="#FFFFFF" points="25.134,23.341 25.122,23.328 25.135,23.341 "/>
<path fill="#A0A1A2" d="M41.386,50c-6.829,0-16.33-5.585-26.069-15.323C1.768,21.125-3.448,8.134,2.338,2.348
	C3.875,0.811,6.039,0,8.599,0c6.829,0,16.331,5.585,26.069,15.325c6.637,6.638,11.379,13.177,13.715,18.916
	c2.365,5.811,2.104,10.572-0.736,13.413C46.111,49.189,43.946,50,41.386,50 M8.599,3.078c-1.75,0-3.124,0.486-4.083,1.445
	C0.791,8.249,4.052,19.056,17.495,32.498c9.031,9.032,17.962,14.424,23.892,14.424c1.75,0,3.123-0.486,4.082-1.446
	c1.908-1.908,1.932-5.486,0.063-10.074C43.35,30.04,38.84,23.85,32.49,17.502C23.459,8.471,14.528,3.078,8.599,3.078"/>
<path fill="#0F0F0F" d="M8.599,50c-2.56,0-4.724-0.811-6.261-2.346c-2.84-2.841-3.101-7.602-0.735-13.413
	c2.336-5.739,7.078-12.278,13.715-18.916C25.056,5.585,34.558,0,41.387,0c2.56,0,4.724,0.811,6.26,2.347
	c5.786,5.787,0.57,18.778-12.979,32.33C24.929,44.415,15.427,50,8.599,50 M41.387,3.078c-5.93,0-14.861,5.393-23.893,14.424
	c-6.348,6.348-10.86,12.538-13.041,17.9c-1.869,4.588-1.845,8.166,0.063,10.074c0.959,0.96,2.332,1.446,4.083,1.446
	c5.93,0,14.86-5.392,23.892-14.424C45.933,19.056,49.194,8.249,45.468,4.522C44.509,3.564,43.137,3.078,41.387,3.078"/>
<path fill="#BA141A" d="M32.273,20.801c-0.303-0.33-0.665-0.573-1.063-0.739c-1.839-0.766-4.496,0.188-6.16,2.55
	c-2.559-3.088-5.619-3.668-7.323-1.811c-3.404,3.706,0.859,8.576,4.221,11.341c1.325,1.09,2.51,1.853,3.032,2.089v0.024
	c0.005-0.001,0.014-0.009,0.018-0.009c0,0,0.018,0.009,0.025,0.009v-0.024C26.863,33.397,37.017,25.969,32.273,20.801"/>
<path opacity="0.2" fill="#FFFFFF" d="M25.05,22.613c-2.559-3.088-5.619-3.668-7.323-1.811c-3.404,3.706,0.859,8.576,4.221,11.341
	l9.262-12.08C29.371,19.297,26.714,20.25,25.05,22.613"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ProcessExplorer()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M32.4,38.4h-1.2H19.4h-0.6c1.6,5.7-0.6,6.6-10.2,6.6v3h12.2h8.9h11.5v-3C31.7,45,30.8,44.2,32.4,38.4"/>
<path fill="#A0A1A2" d="M47,2H2.7C1.2,2,0,3.3,0,4.8v30.8c0,1.5,1.2,2.8,2.7,2.8H47c1.5,0,3-1.3,3-2.8V4.8C50,3.3,48.5,2,47,2
	 M46.2,5.8v28.7H3.8V5.8l42.4-0.1L46.2,5.8z"/>
<polygon fill="#59B4D9" points="46.1,5.8 46.1,34.6 3.8,34.6 3.8,5.8 46.2,5.8 "/>
<path opacity="0.2" fill="#FFFFFF" d="M3.8,34.6L3.8,34.6L3.8,5.8l38.7-0.1L47,2c0,0,0,0,0,0H2.7C1.2,2,0,3.3,0,4.8v30.8
	c0,1.5,1.2,2.8,2.7,2.8h1.1l4.6-3.8H3.8z"/>
<polygon fill="#59B4D9" points="3.8,34.6 3.8,34.6 3.8,5.8 42.5,5.8 42.5,5.8 3.8,5.8 "/>
<rect x="8.7" y="45" fill="#A0A1A2" width="32.7" height="3"/>
<path fill="#B8D432" d="M25.5,4.1c0,0.4-0.3,0.7-0.7,0.7c-0.4,0-0.7-0.3-0.7-0.7c0-0.4,0.3-0.7,0.7-0.7C25.2,3.4,25.5,3.7,25.5,4.1"
	/>
<polygon fill="#0072C6" points="3.8,20.7 12.5,18.8 16.2,24.9 21.5,18.8 25.1,22.2 31.3,10.6 35,27.1 38.6,18.9 46.2,21.9 
	46.2,34.6 3.8,34.6 "/>
<g>
	<path fill="#B8D432" d="M38.1,17.4l-2.8,6.3L31.7,7.5l-6.8,12.8l-3.5-3.2L16.3,23L13,17.4l-9.2,2v2c0.1,0,0.1,0,0.2,0l8-1.8l4,6.7
		l5.6-6.4l3.8,3.6l5.5-10.4l3.7,16.8L39.1,20l6.7,2.6c0.1,0,0.2,0,0.3,0v-2.1L38.1,17.4z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ProductionReadyDB()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M6.139,6.831v36.338C6.139,46.941,14.584,50,25,50V6.831H6.139z"/>
<path fill="#59B4D9" d="M24.742,49.999H25c10.416,0,18.861-3.057,18.861-6.831V6.831H24.742V49.999z"/>
<path fill="#FFFFFF" d="M43.861,6.831c0,3.773-8.445,6.831-18.861,6.831S6.139,10.603,6.139,6.831C6.139,3.058,14.584,0,25,0
	S43.861,3.058,43.861,6.831"/>
<path fill="#7FBA00" d="M40.005,6.438c0,2.491-6.718,4.507-15.005,4.507S9.994,8.928,9.994,6.438c0-2.49,6.719-4.507,15.006-4.507
	S40.005,3.948,40.005,6.438"/>
<path fill="#B8D432" d="M36.861,9.191c1.964-0.762,3.145-1.716,3.145-2.752c0-2.491-6.718-4.508-15.006-4.508
	c-8.287,0-15.005,2.018-15.005,4.508c0,1.036,1.18,1.99,3.145,2.752C15.883,8.126,20.17,7.439,25,7.439
	C29.831,7.439,34.116,8.126,36.861,9.191"/>
<polygon fill="#FFFFFF" points="14.435,29.376 14.435,29.376 17.325,26.483 17.325,26.483 17.325,26.483 23.112,32.271 
	34.689,20.694 34.689,20.694 34.689,20.694 37.579,23.589 37.579,23.589 37.579,23.589 23.112,38.06 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.QuickStart()</h6><svg viewBox="-10.5 -9.5 50 50">
<path class="msportalfx-svg-c15" d="M34.5,35.865h2L36.405-9.5H-1.833H-4.5c-1.293,0.167-3.992,3.3-3.992,3.751 c0,0.187,0.059,43.882,0.059,43.882c0,1.307,1.06,2.367,2.368,2.367H31.5v-0.729L34.5,35.865z"/>
<path class="msportalfx-svg-c01" d="M22.673,15.737c0.14-0.548,0.217-1.115,0.217-1.688c0-2.263-1.108-4.258-2.82-5.465l-2.685,4.523 c-0.11,0.17-0.025,0.312,0.18,0.312l3.445-0.022c0.198-0.007,0.248,0.117,0.115,0.265l-8.892,10.142 c-0.132,0.152-0.185,0.127-0.105-0.058l2.807-6.853c0.065-0.182-0.028-0.332-0.23-0.332h-2.732c-0.195,0-0.302-0.142-0.225-0.337 l3.272-8.728c-2.21,0.417-4.027,1.937-4.873,3.962c-0.857-1.013-2.14-1.662-3.572-1.662c-2.582,0-4.685,2.112-4.685,4.72 c0,0.457,0.065,0.882,0.18,1.29c-2.11,0.783-3.62,2.828-3.62,5.223c0,3.068,2.47,5.563,5.522,5.563h17.037 c3.06,0,5.523-2.492,5.523-5.563C26.538,18.543,24.917,16.443,22.673,15.737z"/>
<path class="msportalfx-svg-c02" d="M-2.718-7.5c-1.105,0-1.58,0.185-2.507,1c-2.275,2,0.39,2,1.495,2H31.5v44.271l3-3.906V-7.5H-2.718z"/>
<polygon opacity="0.5" class="msportalfx-svg-c03" enable-background="new" points="31.5,39.771 34.5,35.865 34.5,-7.5 31.5,-4.5"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ResourceDefault()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M25.561,23.167c-0.103,0-0.197-0.03-0.288-0.083L6.011,12.045c-0.183-0.103-0.292-0.297-0.292-0.506
	c0-0.203,0.108-0.395,0.292-0.496L25.149,0.075c0.182-0.1,0.405-0.1,0.579,0L44.994,11.12c0.174,0.102,0.29,0.291,0.29,0.496
	c0,0.212-0.116,0.4-0.29,0.504L25.853,23.084C25.762,23.137,25.665,23.167,25.561,23.167"/>
<path fill="#59B4D9" d="M22.792,50c-0.104,0-0.207-0.024-0.295-0.077L3.295,38.917C3.11,38.814,3,38.626,3,38.416V16.331
	c0-0.207,0.11-0.397,0.295-0.506c0.176-0.1,0.401-0.1,0.586,0L23.08,26.831c0.178,0.107,0.286,0.297,0.286,0.504v22.086
	c0,0.212-0.108,0.397-0.286,0.502C22.985,49.976,22.888,50,22.792,50"/>
<path fill="#59B4D9" d="M28.225,50c-0.098,0-0.199-0.024-0.295-0.077c-0.178-0.105-0.288-0.289-0.288-0.502V27.478
	c0-0.207,0.11-0.397,0.288-0.504l19.196-11.002c0.185-0.102,0.403-0.102,0.587,0c0.176,0.103,0.287,0.295,0.287,0.5v21.943
	c0,0.211-0.111,0.398-0.287,0.502L28.511,49.923C28.429,49.976,28.325,50,28.225,50"/>
<path opacity="0.5" fill="#FFFFFF" d="M28.225,50c-0.098,0-0.199-0.024-0.295-0.077c-0.178-0.105-0.288-0.289-0.288-0.502V27.478
	c0-0.207,0.11-0.397,0.288-0.504l19.196-11.002c0.185-0.102,0.403-0.102,0.587,0c0.176,0.103,0.287,0.295,0.287,0.5v21.943
	c0,0.211-0.111,0.398-0.287,0.502L28.511,49.923C28.429,49.976,28.325,50,28.225,50"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ResourceGroup()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M25.047,22.647c-0.078,0-0.15-0.023-0.22-0.064l-14.711-8.49c-0.14-0.08-0.223-0.228-0.223-0.389
	c0-0.156,0.083-0.304,0.223-0.382l14.616-8.435c0.139-0.077,0.309-0.077,0.442,0l14.714,8.495c0.133,0.078,0.221,0.224,0.221,0.382
	c0,0.163-0.088,0.308-0.221,0.387l-14.619,8.433C25.2,22.624,25.126,22.647,25.047,22.647"/>
<path fill="#59B4D9" d="M22.932,43.284c-0.08,0-0.158-0.019-0.226-0.059L8.042,34.761c-0.142-0.079-0.226-0.224-0.226-0.386V17.39
	c0-0.159,0.084-0.305,0.226-0.389c0.134-0.077,0.306-0.077,0.448,0l14.662,8.464c0.136,0.082,0.218,0.228,0.218,0.387v16.987
	c0,0.163-0.083,0.305-0.218,0.386C23.079,43.266,23.006,43.284,22.932,43.284"/>
<path fill="#59B4D9" d="M27.081,43.284c-0.075,0-0.152-0.019-0.226-0.059c-0.136-0.081-0.22-0.223-0.22-0.386V25.962
	c0-0.159,0.084-0.305,0.22-0.387l14.66-8.461c0.142-0.078,0.308-0.078,0.448,0c0.134,0.08,0.22,0.227,0.22,0.385v16.877
	c0,0.162-0.085,0.306-0.22,0.386l-14.665,8.464C27.237,43.266,27.158,43.284,27.081,43.284"/>
<path opacity="0.5" fill="#FFFFFF" d="M27.081,43.284c-0.075,0-0.152-0.019-0.226-0.059c-0.136-0.081-0.22-0.223-0.22-0.386V25.962
	c0-0.159,0.084-0.305,0.22-0.387l14.66-8.461c0.142-0.078,0.308-0.078,0.448,0c0.134,0.08,0.22,0.227,0.22,0.385v16.877
	c0,0.162-0.085,0.306-0.22,0.386l-14.665,8.464C27.237,43.266,27.158,43.284,27.081,43.284"/>
<path fill="#7A7A7A" d="M9.558,45.171c-0.247,0-0.497-0.063-0.726-0.195l-6.845-3.952C0.835,40.358,0,38.911,0,37.582V12.418
	c0-1.329,0.835-2.777,1.987-3.441l6.845-3.952c0.697-0.401,1.586-0.163,1.987,0.532c0.402,0.696,0.163,1.585-0.532,1.987
	l-6.845,3.952c-0.243,0.141-0.532,0.641-0.532,0.922v25.164c0,0.281,0.289,0.782,0.532,0.922l6.845,3.952
	c0.696,0.402,0.934,1.291,0.532,1.987C10.55,44.91,10.061,45.171,9.558,45.171z"/>
<path fill="#7A7A7A" d="M40.442,4.829c0.247,0,0.497,0.063,0.726,0.195l6.845,3.952C49.165,9.642,50,11.089,50,12.418v25.164
	c0,1.329-0.835,2.777-1.987,3.441l-6.845,3.952c-0.697,0.401-1.586,0.163-1.987-0.532c-0.402-0.696-0.163-1.585,0.532-1.987
	l6.845-3.952c0.243-0.141,0.532-0.641,0.532-0.922V12.418c0-0.281-0.289-0.782-0.532-0.922l-6.845-3.952
	c-0.696-0.402-0.934-1.291-0.532-1.987C39.45,5.09,39.939,4.829,40.442,4.829z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ResourceLinked()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M25.001,50c-1.232,0-2.392-0.48-3.261-1.352L1.351,28.26C0.492,27.401,0,26.214,0,24.999
	c0-1.214,0.492-2.402,1.351-3.26L21.74,1.351c0.871-0.872,2.029-1.352,3.261-1.352c1.231,0,2.39,0.48,3.261,1.352l20.386,20.388
	C49.521,22.607,50,23.766,50,24.999c0,1.233-0.479,2.392-1.353,3.263L28.262,48.648C27.392,49.52,26.232,50,25.001,50"/>
<path opacity="0.15" fill="#FFFFFF" d="M28.262,1.351c-0.871-0.872-2.029-1.352-3.261-1.352c-1.231,0-2.389,0.48-3.26,1.352
	L1.352,21.739C0.492,22.597,0,23.785,0,24.999c0,1.215,0.492,2.403,1.352,3.261l11.543,11.544L34.61,7.698L28.262,1.351z"/>
<path fill="#FFFFFF" d="M18.269,27.734h1.893l0.086-0.266l0.663-2.025l1.304-0.539l2.198,1.04l1.337-1.336l-0.127-0.251
	l-0.962-1.899l0.541-1.305l2.289-0.818l0.002-1.891l-0.269-0.087l-2.023-0.663l-0.539-1.304l1.039-2.198l-1.337-1.337l-0.251,0.127
	l-1.9,0.962l-1.302-0.54l-0.819-2.29l-1.891-0.001l-0.088,0.267l-0.661,2.025l-1.306,0.539l-2.196-1.04l-1.34,1.336l0.128,0.251
	l0.964,1.901l-0.541,1.302l-2.289,0.819l-0.001,1.891l0.266,0.087l2.025,0.663l0.539,1.305l-1.04,2.196l1.336,1.339l0.251-0.127
	l1.899-0.964l1.305,0.542L18.269,27.734z M15.309,19.421c0.001-2.138,1.736-3.869,3.874-3.869c2.139,0.002,3.87,1.736,3.869,3.875
	c-0.001,2.139-1.735,3.871-3.874,3.869C17.04,23.294,15.308,21.559,15.309,19.421"/>
<path fill="#B8D432" d="M19.18,21.937c-1.389-0.001-2.514-1.128-2.513-2.515c0.001-1.388,1.127-2.512,2.516-2.511
	c1.388,0.001,2.513,1.127,2.512,2.513C21.694,20.813,20.568,21.938,19.18,21.937"/>
<path fill="#FFFFFF" d="M40.918,28.692l-2.788,2.889c-1.033,1.063-2.23,1.16-3.607,0.835l4.881-5.054
	c0.722-0.745,0.271-2.213-0.52-2.905c-0.782-0.692-2.279-0.945-3.01-0.19l-4.872,5.053c-0.453-1.278-0.444-2.431,0.58-3.484
	l2.788-2.889c1.49-1.549,4.41-1.469,6.022-0.049s2.064,4.271,0.565,5.82L40.918,28.692z"/>
<path fill="#FFFFFF" d="M28.252,38.919l4.881-5.054c0.444,1.278,0.444,2.431-0.59,3.485l-2.778,2.889
	c-1.499,1.549-4.37,1.286-5.992-0.134c-1.612-1.42-2.055-4.062-0.556-5.611l2.788-2.889c1.024-1.062,2.23-1.16,3.597-0.844
	l-4.881,5.054c-0.722,0.754-0.34,2.159,0.451,2.851c0.782,0.692,2.245,1.051,2.977,0.296L28.252,38.919z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#FFFFFF" d="M27.794,34.505l6.965-7.218c0.384-0.397,1.23-1.307,1.979-0.558
	c0.645,0.645-0.082,1.481-0.465,1.888l-6.975,7.218c-0.384,0.397-1.461,1.621-2.154,0.928C26.274,35.894,27.4,34.903,27.794,34.505z
	"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ResourceList()</h6><svg viewBox="0 0 50 50">
    <rect x="16.261" y="1" class="msportalfx-svg-c13" width="9.802" height="9.695"/>
    <rect x="27.702" y="1" class="msportalfx-svg-c13" width="9.831" height="9.695"/>
    <rect x="39.199" y="1" class="msportalfx-svg-c13" width="9.801" height="9.695"/>
    <rect x="39.199" y="12.469" class="msportalfx-svg-c13" width="9.801" height="9.695"/>
    <rect x="39.199" y="23.938" class="msportalfx-svg-c13" width="9.801" height="9.831"/>
    <path class="msportalfx-svg-c05" d="M4.121,48.367c-0.961,0-1.865-0.373-2.546-1.048c-0.686-0.679-1.068-1.587-1.072-2.553
        c-0.005-0.968,0.367-1.877,1.048-2.564l8.747-8.83c-1.479-2.758-1.888-5.921-1.145-8.952c1.399-5.652,6.431-9.599,12.236-9.599
        c1.01,0,2.026,0.124,3.022,0.368c3.269,0.805,6.029,2.836,7.771,5.718c1.742,2.882,2.258,6.272,1.45,9.541
        c-1.389,5.646-6.421,9.589-12.235,9.589c-1.017,0-2.031-0.124-3.018-0.367c-1.032-0.254-2.033-0.643-2.984-1.16l-8.701,8.784
        C6.007,47.987,5.094,48.367,4.121,48.367 M21.389,19.598c-3.604,0-6.728,2.452-7.598,5.965c-0.525,2.141-0.105,4.429,1.151,6.27
        c0.545,0.802,1.223,1.488,2.017,2.037c0.793,0.547,1.655,0.94,2.563,1.163c0.612,0.152,1.244,0.227,1.875,0.227
        c3.609,0,6.734-2.448,7.597-5.956c0.502-2.028,0.182-4.133-0.901-5.926c-1.081-1.79-2.795-3.051-4.823-3.551
        C22.651,19.674,22.018,19.598,21.389,19.598"/>
    <path class="msportalfx-svg-c13" d="M24.792,14.08c0.432,0.106,0.854,0.234,1.271,0.379v-1.991h-9.802v2.284c1.631-0.686,3.412-1.068,5.27-1.068
        C22.623,13.684,23.72,13.816,24.792,14.08"/>
    <path class="msportalfx-svg-c13" d="M33.175,20.249c0.372,0.617,0.679,1.26,0.946,1.915h3.41v-9.696h-9.83v2.704
        C29.946,16.309,31.84,18.041,33.175,20.249"/>
    <path class="msportalfx-svg-c13" d="M34.741,30.541c-0.282,1.146-0.704,2.227-1.243,3.227h4.034v-9.831h-2.807
        C35.271,26.079,35.286,28.335,34.741,30.541"/>
    <path class="msportalfx-svg-c13" d="M28.208,27.414c0,3.703-3.003,6.704-6.705,6.704c-3.703,0-6.705-3.001-6.705-6.704
        c0-3.703,3.002-6.704,6.705-6.704C25.204,20.71,28.208,23.711,28.208,27.414"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Scale()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-0.5 0.5 50 50" enable-background="new -0.5 0.5 50 50" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" fill="#A0A1A2" d="M36.167,27.167v10H12.833V13.833h9.997v-5H11.167
	c-1.841,0-3.333,1.493-3.333,3.333v26.667c0,1.841,1.493,3.333,3.333,3.333h26.667c1.841,0,3.333-1.493,3.333-3.333V27.167H36.167z"
	/>
<rect x="21.167" y="0.5" fill="#B8D432" width="6.667" height="5"/>
<rect x="11.167" y="0.5" fill="#B8D432" width="6.667" height="5"/>
<path fill="#B8D432" d="M44.5,45.5h-3.333v5h5c1.841,0,3.333-1.493,3.333-3.333v-5h-5V45.5z"/>
<rect x="-0.5" y="32.167" fill="#B8D432" width="5" height="6.667"/>
<rect x="21.167" y="45.5" fill="#B8D432" width="6.667" height="5"/>
<rect x="44.5" y="32.167" fill="#B8D432" width="5" height="6.667"/>
<rect x="31.167" y="45.5" fill="#B8D432" width="6.667" height="5"/>
<rect x="11.167" y="45.5" fill="#B8D432" width="6.667" height="5"/>
<path fill="#B8D432" d="M4.5,45.5v-3.333h-5v5c0,1.841,1.493,3.333,3.333,3.333h5v-5H4.5z"/>
<rect x="-0.5" y="12.167" fill="#B8D432" width="5" height="6.667"/>
<path fill="#B8D432" d="M4.5,5.5h3.333v-5h-5C0.993,0.5-0.5,1.993-0.5,3.833v5h5V5.5z"/>
<rect x="-0.5" y="22.167" fill="#B8D432" width="5" height="6.667"/>
<rect x="44.5" y="22.167" fill="#B8D432" width="5" height="6.667"/>
<polygon fill="#0F0F0F" points="49.5,0.5 34.5,0.5 40.084,6.084 18.715,27.453 22.597,31.333 43.965,9.965 49.5,15.5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Search()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M47.092,8.89c-2.544-4.209-6.572-7.174-11.343-8.349C34.298,0.183,32.812,0,31.329,0
	c-8.472,0-15.818,5.768-17.863,14.024c-1.123,4.584-0.389,9.441,1.974,13.547L1.372,41.751c-1.841,1.861-1.828,4.862,0.033,6.704
	c0.922,0.914,2.13,1.372,3.336,1.372c1.219,0,2.441-0.469,3.368-1.404l14.002-14.115c1.524,0.895,3.14,1.566,4.827,1.981
	c1.447,0.356,2.93,0.537,4.408,0.537c8.487,0,15.834-5.76,17.864-14.008C50.391,18.049,49.638,13.102,47.092,8.89z"/>
<path fill="#FFFFFF" d="M43.859,21.499c-1.423,5.775-6.566,9.81-12.513,9.81c-1.034,0-2.073-0.126-3.087-0.375
	c-1.558-0.385-2.97-1.051-4.22-1.916c-1.317-0.911-2.438-2.05-3.324-3.352c-1.99-2.922-2.795-6.636-1.889-10.328
	c1.43-5.782,6.576-9.819,12.51-9.819c1.035,0,2.076,0.126,3.094,0.377c3.341,0.823,6.162,2.899,7.945,5.848
	C44.158,14.691,44.685,18.156,43.859,21.499"/>
<path opacity="0.1" fill="#59B4D9" d="M43.859,21.499c-1.423,5.775-6.566,9.81-12.513,9.81c-1.034,0-2.073-0.126-3.087-0.375
	c-1.558-0.385-2.97-1.051-4.22-1.916c-1.317-0.911-2.438-2.05-3.324-3.352c-1.99-2.922-2.795-6.636-1.889-10.328
	c1.43-5.782,6.576-9.819,12.51-9.819c1.035,0,2.076,0.126,3.094,0.377c3.341,0.823,6.162,2.899,7.945,5.848
	C44.158,14.691,44.685,18.156,43.859,21.499"/>
<path opacity="0.3" fill="#59B4D9" d="M38.308,7.577C37.13,6.816,35.83,6.242,34.43,5.896c-1.018-0.252-2.059-0.378-3.095-0.378
	c-5.933,0-11.079,4.038-12.509,9.82c-0.906,3.692-0.1,7.406,1.889,10.329c0.349,0.513,0.746,0.993,1.166,1.452
	C24.615,18.668,30.572,11.672,38.308,7.577"/>
<path opacity="0.5" fill="#1E1E1E" d="M20.913,33.556c-1.865-1.291-3.463-2.902-4.746-4.786c-0.265-0.389-0.494-0.795-0.727-1.199
	l-1.323,1.333l-0.145,0.146c0.174,0.286,0.354,0.567,0.543,0.844c1.422,2.089,3.191,3.874,5.261,5.306
	c0.176,0.122,0.619,0.293,1.004,0.45l1.331-1.342C21.706,34.071,21.304,33.826,20.913,33.556z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.SearchGrid()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="16.261" y="1" fill="#B8D432" width="9.802" height="9.695"/>
<rect x="27.702" y="1" fill="#B8D432" width="9.831" height="9.695"/>
<rect x="39.199" y="1" fill="#B8D432" width="9.801" height="9.695"/>
<rect x="39.199" y="12.469" fill="#B8D432" width="9.801" height="9.695"/>
<rect x="39.199" y="23.938" fill="#B8D432" width="9.801" height="9.831"/>
<path fill="#3E3E3E" d="M4.121,48.367c-0.961,0-1.865-0.373-2.546-1.048c-0.686-0.679-1.068-1.587-1.072-2.553
	c-0.005-0.968,0.367-1.877,1.048-2.564l8.747-8.83c-1.479-2.758-1.888-5.921-1.145-8.952c1.399-5.652,6.431-9.599,12.236-9.599
	c1.01,0,2.026,0.124,3.022,0.368c3.269,0.805,6.029,2.836,7.771,5.718c1.742,2.882,2.258,6.272,1.45,9.541
	c-1.389,5.646-6.421,9.589-12.235,9.589c-1.017,0-2.031-0.124-3.018-0.367c-1.032-0.254-2.033-0.643-2.984-1.16l-8.701,8.784
	C6.007,47.987,5.094,48.367,4.121,48.367 M21.389,19.598c-3.604,0-6.728,2.452-7.598,5.965c-0.525,2.141-0.105,4.429,1.151,6.27
	c0.545,0.802,1.223,1.488,2.017,2.037c0.793,0.547,1.655,0.94,2.563,1.163c0.612,0.152,1.244,0.227,1.875,0.227
	c3.609,0,6.734-2.448,7.597-5.956c0.502-2.028,0.182-4.133-0.901-5.926c-1.081-1.79-2.795-3.051-4.823-3.551
	C22.651,19.674,22.018,19.598,21.389,19.598"/>
<path fill="#B8D432" d="M24.792,14.08c0.432,0.106,0.854,0.234,1.271,0.379v-1.991h-9.802v2.284c1.631-0.686,3.412-1.068,5.27-1.068
	C22.623,13.684,23.72,13.816,24.792,14.08"/>
<path fill="#B8D432" d="M33.175,20.249c0.372,0.617,0.679,1.26,0.946,1.915h3.41v-9.696h-9.83v2.704
	C29.946,16.309,31.84,18.041,33.175,20.249"/>
<path fill="#B8D432" d="M34.741,30.541c-0.282,1.146-0.704,2.227-1.243,3.227h4.034v-9.831h-2.807
	C35.271,26.079,35.286,28.335,34.741,30.541"/>
<path fill="#B8D432" d="M28.208,27.414c0,3.703-3.003,6.704-6.705,6.704c-3.703,0-6.705-3.001-6.705-6.704
	c0-3.703,3.002-6.704,6.705-6.704C25.204,20.71,28.208,23.711,28.208,27.414"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.ServerFarm()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M29.364,47.462c0,1.402-1.136,2.538-2.538,2.538H2.538C1.136,50,0,48.864,0,47.462V2.538
	C0,1.136,1.136,0,2.538,0h24.288c1.402,0,2.538,1.136,2.538,2.538V47.462z"/>
<path fill="#1E1E1E" d="M4.316,27.024c0-1.762,1.429-3.191,3.191-3.191h14.614c1.762,0,3.191,1.429,3.191,3.191l0,0
	c0,1.762-1.429,3.191-3.191,3.191H7.507C5.745,30.215,4.316,28.787,4.316,27.024L4.316,27.024z"/>
<circle fill="#B8D432" cx="7.643" cy="27.024" r="2.142"/>
<path fill="#1E1E1E" d="M4.316,17.545c0-1.762,1.429-3.191,3.191-3.191h14.614c1.762,0,3.191,1.429,3.191,3.191l0,0
	c0,1.762-1.429,3.191-3.191,3.191H7.507C5.745,20.736,4.316,19.307,4.316,17.545L4.316,17.545z"/>
<circle fill="#B8D432" cx="7.643" cy="17.545" r="2.142"/>
<path fill="#1E1E1E" d="M4.316,8.065c0-1.762,1.429-3.191,3.191-3.191h14.614c1.762,0,3.191,1.429,3.191,3.191l0,0
	c0,1.762-1.429,3.191-3.191,3.191H7.507C5.745,11.257,4.316,9.828,4.316,8.065L4.316,8.065z"/>
<circle fill="#B8D432" cx="7.643" cy="8.065" r="2.142"/>
<polygon fill="#BA141A" points="43.167,25.117 31.557,19.889 19.948,25.117 15.867,35.404 15.867,50 47.248,50 47.248,35.404 "/>
<polygon fill="#A0A1A2" points="47.111,37.362 42.247,26.523 31.557,21.709 20.868,26.523 16.004,37.362 13.784,36.365 
	19.027,24.684 31.557,19.042 44.088,24.684 49.331,36.365 "/>
<path fill="#FFFFFF" d="M39.369,50H23.561V37.812h15.808V50z M25.187,48.374h12.555v-8.935H25.187V48.374z"/>
<rect x="22.623" y="43.092" transform="matrix(-0.802 -0.5973 0.5973 -0.802 30.4733 97.9112)" fill="#FFFFFF" width="17.682" height="1.626"/>
<rect x="30.65" y="35.065" transform="matrix(-0.5972 -0.8021 0.8021 -0.5972 15.0365 95.3626)" fill="#FFFFFF" width="1.626" height="17.682"/>
<rect x="28.943" y="27.568" opacity="0.5" fill="#1E1E1E" enable-background="new    " width="5.229" height="4.187"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.SQLDatabase()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-0.5 0.5 50 50" enable-background="new -0.5 0.5 50 50" xml:space="preserve">
<path fill="#0072C6" d="M5.757,7.288v36.111c0,3.749,8.392,6.789,18.743,6.789v-42.9C24.5,7.288,5.757,7.288,5.757,7.288z"/>
<path fill="#0072C6" d="M24.243,50.187H24.5c10.351,0,18.743-3.038,18.743-6.788V7.288h-19V50.187z"/>
<path opacity="0.15" fill="#FFFFFF" d="M24.243,50.187H24.5c10.351,0,18.743-3.038,18.743-6.788V7.288h-19V50.187z"/>
<path fill="#FFFFFF" d="M43.243,7.288c0,3.749-8.392,6.788-18.743,6.788S5.757,11.037,5.757,7.288S14.149,0.5,24.5,0.5
	S43.243,3.539,43.243,7.288"/>
<path fill="#7FBA00" d="M39.411,6.897c0,2.475-6.676,4.479-14.911,4.479S9.588,9.372,9.588,6.897c0-2.474,6.677-4.479,14.912-4.479
	S39.411,4.423,39.411,6.897"/>
<path fill="#B8D432" d="M36.287,9.634c1.952-0.757,3.125-1.705,3.125-2.735c0-2.475-6.676-4.48-14.912-4.48
	c-8.235,0-14.911,2.005-14.911,4.48c0,1.03,1.173,1.978,3.125,2.735C15.44,8.576,19.7,7.893,24.5,7.893
	C29.301,7.893,33.559,8.576,36.287,9.634"/>
<path fill="#FFFFFF" d="M18.547,32.354c0,1.122-0.407,1.991-1.221,2.607c-0.814,0.616-1.938,0.924-3.373,0.924
	c-1.221,0-2.241-0.22-3.061-0.66v-2.64c0.946,0.803,1.988,1.205,3.126,1.205c0.55,0,0.975-0.11,1.275-0.33s0.45-0.511,0.45-0.875
	c0-0.357-0.144-0.668-0.433-0.932s-0.876-0.605-1.761-1.023c-1.804-0.846-2.706-2.002-2.706-3.464c0-1.061,0.393-1.912,1.18-2.553
	c0.786-0.64,1.831-0.961,3.134-0.961c1.155,0,2.111,0.152,2.871,0.454v2.466c-0.797-0.55-1.705-0.825-2.722-0.825
	c-0.511,0-0.915,0.108-1.212,0.325c-0.297,0.218-0.445,0.508-0.445,0.87c0,0.374,0.119,0.681,0.359,0.92
	c0.239,0.239,0.73,0.535,1.472,0.887c1.106,0.523,1.893,1.053,2.364,1.592C18.312,30.881,18.547,31.552,18.547,32.354z"/>
<path fill="#FFFFFF" d="M31.274,29.682c0,1.391-0.317,2.599-0.949,3.621c-0.633,1.023-1.523,1.74-2.672,2.153l3.431,3.176H27.62
	l-2.45-2.747c-1.05-0.038-1.998-0.316-2.842-0.833c-0.844-0.516-1.496-1.225-1.955-2.124s-0.689-1.902-0.689-3.007
	c0-1.226,0.249-2.319,0.746-3.279c0.498-0.96,1.197-1.698,2.099-2.215c0.902-0.516,1.935-0.775,3.102-0.775
	c1.088,0,2.063,0.25,2.924,0.751c0.86,0.5,1.528,1.212,2.004,2.136C31.036,27.463,31.274,28.511,31.274,29.682z M28.47,29.831
	c0-1.199-0.261-2.146-0.784-2.842s-1.237-1.044-2.145-1.044c-0.924,0-1.663,0.349-2.219,1.047c-0.555,0.699-0.833,1.628-0.833,2.788
	c0,1.155,0.272,2.077,0.816,2.767c0.545,0.69,1.267,1.035,2.169,1.035c0.919,0,1.647-0.334,2.186-1.002
	C28.2,31.913,28.47,30.996,28.47,29.831z"/>
<polygon fill="#FFFFFF" points="40.273,35.679 33.229,35.679 33.229,23.851 35.893,23.851 35.893,33.518 40.273,33.518 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.SQLDatabaseServer()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-0.5 0.5 50 50" enable-background="new -0.5 0.5 50 50" xml:space="preserve">
<path fill="#0072C6" d="M-0.5,7.288v36.111c0,3.749,8.392,6.789,18.743,6.789v-42.9C18.243,7.288-0.5,7.288-0.5,7.288z"/>
<path fill="#0072C6" d="M17.986,50.187h0.257c10.351,0,18.743-3.038,18.743-6.788V7.288h-19V50.187z"/>
<path opacity="0.15" fill="#FFFFFF" d="M17.986,50.187h0.257c10.351,0,18.743-3.038,18.743-6.788V7.288h-19V50.187z"/>
<path fill="#FFFFFF" d="M36.986,7.288c0,3.749-8.392,6.788-18.743,6.788S-0.5,11.037-0.5,7.288S7.892,0.5,18.243,0.5
	S36.986,3.539,36.986,7.288"/>
<path fill="#7FBA00" d="M33.154,6.897c0,2.475-6.676,4.479-14.911,4.479S3.331,9.372,3.331,6.897c0-2.474,6.677-4.479,14.912-4.479
	S33.154,4.423,33.154,6.897"/>
<path fill="#B8D432" d="M30.03,9.634c1.952-0.757,3.125-1.705,3.125-2.735c0-2.475-6.676-4.48-14.912-4.48
	c-8.235,0-14.911,2.005-14.911,4.48c0,1.03,1.173,1.978,3.125,2.735c2.726-1.058,6.986-1.741,11.786-1.741
	C23.044,7.893,27.302,8.576,30.03,9.634"/>
<path fill="#FFFFFF" d="M12.29,31.354c0,1.122-0.407,1.991-1.221,2.607c-0.814,0.616-1.938,0.924-3.373,0.924
	c-1.221,0-2.241-0.22-3.061-0.66v-2.64c0.946,0.803,1.988,1.205,3.126,1.205c0.55,0,0.975-0.11,1.275-0.33s0.45-0.511,0.45-0.875
	c0-0.357-0.144-0.668-0.433-0.932s-0.876-0.605-1.761-1.023c-1.804-0.846-2.706-2.002-2.706-3.464c0-1.061,0.393-1.912,1.18-2.553
	c0.786-0.64,1.831-0.961,3.134-0.961c1.155,0,2.111,0.152,2.871,0.454v2.466c-0.797-0.55-1.705-0.825-2.722-0.825
	c-0.511,0-0.915,0.108-1.212,0.325c-0.297,0.218-0.445,0.508-0.445,0.87c0,0.374,0.119,0.681,0.359,0.92
	c0.239,0.239,0.73,0.535,1.472,0.887c1.106,0.523,1.893,1.053,2.364,1.592C12.055,29.881,12.29,30.552,12.29,31.354z"/>
<path fill="#FFFFFF" d="M25.017,28.682c0,1.391-0.317,2.599-0.949,3.621c-0.633,1.023-1.523,1.74-2.672,2.153l3.431,3.176h-3.464
	l-2.45-2.747c-1.05-0.038-1.998-0.316-2.842-0.833c-0.844-0.516-1.496-1.225-1.955-2.124s-0.689-1.902-0.689-3.007
	c0-1.226,0.249-2.319,0.746-3.279c0.498-0.96,1.197-1.698,2.099-2.215c0.902-0.516,1.935-0.775,3.102-0.775
	c1.088,0,2.063,0.25,2.924,0.751c0.86,0.5,1.528,1.212,2.004,2.136C24.779,26.463,25.017,27.511,25.017,28.682z M22.213,28.831
	c0-1.199-0.261-2.146-0.784-2.842s-1.237-1.044-2.145-1.044c-0.924,0-1.663,0.349-2.219,1.047c-0.555,0.699-0.833,1.628-0.833,2.788
	c0,1.155,0.272,2.077,0.816,2.767c0.545,0.69,1.267,1.035,2.169,1.035c0.919,0,1.647-0.334,2.186-1.002
	C21.943,30.913,22.213,29.996,22.213,28.831z"/>
<polygon fill="#FFFFFF" points="34.016,34.679 26.972,34.679 26.972,22.851 29.636,22.851 29.636,32.518 34.016,32.518 "/>
<path fill="#A0A1A2" d="M34.755,49.584l2.123,0.916l0.224-0.265l1.7-1.999l1.719,0.01l1.973,2.254l2.135-0.887l-0.023-0.347
	l-0.185-2.643l1.225-1.234l2.957,0.168L49.5,43.39l-0.258-0.229l-1.96-1.738l0.012-1.756l2.205-2.016l-0.866-2.179l-0.342,0.025
	l-2.588,0.186l-1.206-1.249l0.164-3.02L42.537,30.5l-0.224,0.265l-1.702,2l-1.72-0.011l-1.975-2.251l-2.134,0.884l0.026,0.35
	l0.181,2.64l-1.224,1.234l-2.955-0.168l-0.896,2.167l0.259,0.229l1.959,1.736l-0.012,1.756l-2.207,2.016l0.867,2.179l0.343-0.025
	l2.587-0.186l1.208,1.251L34.755,49.584z M35.363,38.628c1.012-2.452,3.778-3.6,6.177-2.565c2.402,1.036,3.525,3.858,2.513,6.308
	c-1.013,2.451-3.78,3.598-6.179,2.565C35.474,43.902,34.35,41.079,35.363,38.628"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.SSLCustomDomains()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7FBA00" d="M38.185,5.96L38.185,5.96C27.169,4.745,25.326,0,25.326,0S22.847,6.242,7,6.242v25.611
	c0,3.097,1.721,5.997,4.104,8.532l0,0c5.402,5.748,14.223,9.616,14.223,9.616s18.327-8.029,18.327-18.149V6.242
	C41.616,6.242,39.803,6.139,38.185,5.96z"/>
<path opacity="0.4" fill="#B8D432" d="M29.86,16.543L38.185,5.96C27.169,4.745,25.326,0,25.326,0S22.847,6.242,7,6.242v25.611
	c0,3.097,1.721,5.997,4.104,8.532l6.154-7.822L29.86,16.543z"/>
<path fill="#FFFFFF" d="M32.595,24.46h-1.066v-3.552c0-1.709-0.629-3.276-1.669-4.45l0,0c-0.039-0.043-0.074-0.09-0.112-0.133
	c-1.107-1.186-2.683-1.948-4.422-1.947c-1.736-0.001-3.312,0.761-4.419,1.947c-1.11,1.187-1.783,2.811-1.783,4.582v3.553h-1.065
	c-0.443,0-0.801,0.359-0.801,0.801v7.217l0,0.001v2.174c0,0.442,0.359,0.801,0.801,0.801h14.536c0.442,0,0.801-0.359,0.801-0.801
	v-9.391C33.396,24.818,33.037,24.46,32.595,24.46z M28.584,24.461h-5.02l0.001-0.001H22.07v-3.552c0-1.022,0.386-1.927,0.988-2.57
	c0.605-0.643,1.395-1.013,2.268-1.013c0.874,0,1.665,0.37,2.27,1.013c0.143,0.153,0.271,0.323,0.389,0.504l-0.001,0.001
	c0.373,0.579,0.6,1.288,0.6,2.064V24.461z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Storage()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	  width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M0,45.1c0,1,0.8,1.9,1.8,1.9h46.3c1,0,1.9-0.8,1.9-1.9V12H0V45.1z"/>
<path fill="#7A7A7A" d="M48.1,4.1H1.8C0.8,4.1,0,5,0,6v6h50V6c0-1-0.8-1.6-1.9-1.6"/>
<rect x="4" y="25.5" fill="#B8D432" width="42" height="7"/>
<rect x="4" y="15" fill="#FFFFFF" width="42" height="7"/>
<rect x="4" y="36" fill="#B8D432" width="42" height="7"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M2,4C0.9,4,0,4.9,0,6v7.3v3.3v28c0,1.1,0.9,2,2,2h2.2L43.6,4H2z"
	/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.StorageContainer()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M0,44.8c0,1,0.8,1.9,1.8,1.9h46.3c1,0,1.9-0.8,1.9-1.9l0-33.1H0V44.8z"/>
<path fill="#7A7A7A" d="M48.1,4H1.8C0.8,4,0,4.9,0,5.9v5.7h50l0-5.7C50,4.9,49.2,4,48.1,4"/>
<path opacity="0.8" fill="#FFFFFF" d="M38,21.2v-2.4H22.8l-3.6-3.6H9.7v26.3c0,0.8,0.6,1.4,1.4,1.4l0,0h27.2c0.8,0,1.4-0.6,1.4-1.4
	V21.2H38z"/>
<path fill="#FF8C00" d="M12.5,21.2v20.3c0,0.8-0.6,1.4-1.4,1.4c-0.8,0-1.4-0.6-1.4-1.4V15.2h9.6l3.6,3.6H38v2.4H12.5z"/>
<path opacity="0.2" fill="#FFFFFF" d="M2,4C0.9,4,0,4.9,0,6v7.3v3.3v28c0,1.1,0.9,2,2,2h2.2L43.6,4H2z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.StorageQueue()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M0,44.8c0,1,0.8,1.9,1.8,1.9h46.3c1,0,1.9-0.8,1.9-1.9l0-33.1H0V44.8z"/>
<path fill="#7A7A7A" d="M48.1,4H1.8C0.8,4,0,4.9,0,5.9v5.7h50l0-5.7C50,4.9,49.2,4,48.1,4"/>
<rect x="3.7" y="15.1" fill="#804998" width="12" height="27.8"/>
<rect x="19" y="15.1" fill="#804998" width="12" height="27.8"/>
<rect x="34.3" y="15.1" fill="#804998" width="12" height="27.8"/>
<path opacity="0.2" fill="#FFFFFF" d="M2,4C0.9,4,0,4.9,0,6v7.3v3.3v28c0,1.1,0.9,2,2,2h2.2L43.6,4H2z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Store()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#3999C6" points="46.334,44.864 46.332,44.863 45.222,12.364 42.699,12.364 42.699,12.364 39.667,12.364 
	39.667,12.364 4.111,12.364 3,47.105 39.667,50 39.667,50 42.699,47.664 "/>
<polygon opacity="0.3" fill="#FFFFFF" points="42.699,12.364 39.667,12.364 39.667,50 42.699,44.345 "/>
<polygon opacity="0.2" fill="#1E1E1E" points="42.722,44.302 42.699,44.345 39.667,50 42.699,47.664 46.334,44.864 "/>
<path fill="#FFFFFF" d="M33.037,34.209c0-1.552-1.246-2.81-2.785-2.81c-0.116,0-0.23,0.009-0.34,0.024
	c0.17-0.631,0.264-1.295,0.264-1.981c0-4.137-3.326-7.491-7.428-7.491c-3.277,0-6.056,2.14-7.043,5.111
	c-0.52-0.183-1.076-0.286-1.658-0.286c-2.806,0-5.077,2.293-5.077,5.121c0,2.83,2.271,5.123,5.077,5.123
	c0,0,0.005-0.002,0.007-0.002v0.002h16.424l-0.003-0.014C31.908,36.893,33.037,35.686,33.037,34.209"/>
<path fill="#A0A1A2" d="M23.406,6.76c0-2.451,2.144-4.446,4.779-4.446c2.635,0,4.777,1.995,4.777,4.446v5.604h2.296V6.76
	c0-3.728-3.173-6.76-7.073-6.76c-3.901,0-7.073,3.032-7.073,6.76v5.604h2.294V6.76z"/>
<path fill="#7A7A7A" d="M16.369,6.76c0-2.451,2.144-4.446,4.779-4.446c2.635,0,4.779,1.995,4.779,4.446v5.604h2.294V6.76
	c0-3.728-3.173-6.76-7.073-6.76c-3.9,0-7.073,3.032-7.073,6.76v5.604h2.294V6.76z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Support()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M34.256,14.928c0,5.006-4.059,9.065-9.066,9.065c-5.007,0-9.065-4.059-9.065-9.065
	c0-5.006,4.058-9.065,9.065-9.065C30.197,5.863,34.256,9.922,34.256,14.928"/>
<polygon fill="#59B4D9" points="31.818,27.1 25.19,36.387 18.562,27.1 9.001,27.1 9.001,50 41.38,50 41.38,27.1 "/>
<path opacity="0.2" fill="#FFFFFF" d="M16.126,14.928c0,4.931,3.939,8.935,8.843,9.054l2.277-17.875
	c-0.661-0.154-1.346-0.243-2.055-0.243C20.183,5.863,16.126,9.922,16.126,14.928"/>
<polygon opacity="0.2" fill="#FFFFFF" points="18.564,27.1 9,27.1 9,50 21.696,50 23.698,34.297 "/>
<path fill="#3E3E3E" d="M39.966,24.14h-6.881v-3.33h3.552v-5.827c0-6.426-5.228-11.654-11.654-11.654S13.329,8.557,13.329,14.983
	v1.665h-3.33v-1.665C9.999,6.722,16.721,0,24.982,0s14.983,6.722,14.983,14.983V24.14z"/>
<path opacity="0.2" fill="#FFFFFF" d="M24.982,0C16.721,0,9.999,6.722,9.999,14.983v1.665h3.33v-1.665
	c0-6.426,5.228-11.654,11.654-11.654c0.964,0,1.896,0.131,2.793,0.352l0.416-3.327C27.156,0.128,26.085,0,24.982,0z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Table()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="50" width="50" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M0,44.4c0,1,0.7,1.9,1.9,1.9h46.2c1,0,1.9-0.7,1.9-1.9V11.3H0V44.4z"/>
<path fill="#7A7A7A" d="M48.1,3.7H1.9C0.7,3.7,0,4.6,0,5.6v5.7h50V5.6C50,4.6,49.3,3.7,48.1,3.7"/>
<rect x="18.8" y="14.6" fill="#FFFFFF" width="12.6" height="7.6"/>
<rect x="18.8" y="24.9" fill="#FCD116" width="12.6" height="7.6"/>
<rect x="33.8" y="24.9" fill="#FCD116" width="12.6" height="7.6"/>
<rect x="33.8" y="14.6" fill="#FFFFFF" width="12.6" height="7.6"/>
<rect x="3.8" y="14.6" fill="#FFFFFF" width="12.6" height="7.6"/>
<rect x="3.8" y="24.9" fill="#FFFFFF" width="12.6" height="7.6"/>
<rect x="3.8" y="35.1" fill="#FCD116" width="12.6" height="7.6"/>
<rect x="18.8" y="35.1" fill="#FCD116" width="12.6" height="7.6"/>
<rect x="33.8" y="35.1" fill="#FCD116" width="12.6" height="7.6"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M1.9,3.7C1,3.7,0,4.6,0,5.6V13v3.3v28.1c0,1,1,1.9,1.9,1.9H4
	L43.6,3.7H1.9z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.TeamProject()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#804998" d="M44.977,10.5c0,5.638-4.571,10.209-10.21,10.209S24.558,16.138,24.558,10.5s4.57-10.209,10.209-10.209
	C40.406,0.291,44.977,4.862,44.977,10.5"/>
<polygon fill="#804998" points="42.232,24.209 34.767,34.668 27.302,24.209 19.534,24.209 19.534,50 50,50 50,24.209 "/>
<path fill="#804998" d="M14.294,27.636c0,3.168-2.568,5.734-5.736,5.734s-5.736-2.566-5.736-5.734s2.568-5.735,5.736-5.735
	S14.294,24.468,14.294,27.636"/>
<polygon fill="#804998" points="12.751,35.512 8.558,41.388 4.364,35.512 0,35.512 0,50 17.115,50 17.115,35.512 "/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M24.558,10.5c0,5.554,4.437,10.063,9.959,10.197l2.564-20.132
	c-0.744-0.174-1.516-0.274-2.314-0.274C29.128,0.291,24.558,4.862,24.558,10.5"/>
<polygon opacity="0.2" fill="#FFFFFF" enable-background="new    " points="27.304,24.209 19.533,24.209 19.533,50 30.832,50 
	33.086,32.314 "/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M2.823,27.636c0,3.167,2.567,5.734,5.735,5.734
	c0.2,0,0.387-0.038,0.582-0.057l1.407-11.038c-0.622-0.23-1.286-0.374-1.989-0.374C5.39,21.901,2.823,24.469,2.823,27.636"/>
<polygon opacity="0.2" fill="#FFFFFF" enable-background="new    " points="4.365,35.512 0.001,35.512 0.001,50 7.063,50 
	8.221,40.916 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.TFSVCRepository()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<rect x="26.839" y="12.777" fill="#68217A" width="11.062" height="3.571"/>
<rect x="12.777" y="26.768" fill="#68217A" width="3.571" height="11.232"/>
<rect x="21.35" y="26.028" transform="matrix(0.7071 0.7071 -0.7071 0.7071 27.8135 -11.5206)" fill="#68217A" width="12.926" height="3.571"/>
<path fill="#804998" d="M14.562,28.625C6.808,28.625,0.5,22.317,0.5,14.563S6.808,0.5,14.562,0.5s14.062,6.308,14.062,14.062
	S22.316,28.625,14.562,28.625z M14.562,4.072c-5.784,0-10.491,4.706-10.491,10.491s4.707,10.491,10.491,10.491
	s10.491-4.706,10.491-10.491S20.347,4.072,14.562,4.072z"/>
<circle fill="#804998" cx="43.357" cy="14.563" r="7.143"/>
<circle fill="#804998" cx="14.563" cy="43.357" r="7.143"/>
<circle fill="#804998" cx="34.987" cy="34.987" r="7.143"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Toolbox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#BA141A" d="M2.001,45.578v-27.8c0-1.617,1.311-2.927,2.927-2.927h39.145c1.617,0,2.928,1.31,2.928,2.927v27.8H2.001z"/>
<path fill="#804998" d="M44.271,31.609c-2.872,0-5.2,2.327-5.2,5.199c0,2.872,2.328,5.2,5.2,5.2c1.005,0,1.934-0.298,2.729-0.791
	V32.4C46.205,31.907,45.275,31.609,44.271,31.609z"/>
<path fill="#1E1E1E" d="M37.111,14.851h-3.389V9.997c0-0.887-0.72-1.608-1.607-1.608h-15.23c-0.885,0-1.607,0.721-1.607,1.608v4.854
	H11.89V9.997C11.89,7.242,14.132,5,16.885,5h15.23c2.755,0,4.996,2.242,4.996,4.997L37.111,14.851z"/>
<polygon opacity="0.6" fill="#FEE087" enable-background="new    " points="19.077,35.804 2,31.731 2,38.339 17.587,42.056 "/>
<path opacity="0.8" fill="#FF8C00" enable-background="new    " d="M16.84,19.344c0.254,1.656-2.448,3.445-6.035,3.996
	c-3.588,0.551-6.702-0.344-6.956-2c-0.255-1.656,2.447-3.445,6.035-3.996C13.471,16.793,16.586,17.688,16.84,19.344"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M44.271,33.274c-1.951,0-3.533,1.582-3.533,3.534
	s1.582,3.534,3.533,3.534c1.106,0,2.082-0.519,2.729-1.314v-4.44C46.352,33.793,45.377,33.274,44.271,33.274z"/>
<rect x="2" y="27.127" opacity="0.3" fill="#1E1E1E" enable-background="new    " width="45" height="2.112"/>
<path opacity="0.3" fill="#1E1E1E" enable-background="new    " d="M28.187,25.966c0.727,0,1.314,0.588,1.314,1.314v3.988
	c0,0.726-0.587,1.314-1.314,1.314h-7.376c-0.726,0-1.313-0.588-1.313-1.314V27.28c0-0.726,0.587-1.314,1.313-1.314L28.187,25.966z"
	/>
<rect x="2.001" y="26.002" fill="#E5E5E5" width="45" height="2.111"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.TrafficManager()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#804998" points="50,35.5 50,14.588 35.368,0 14.662,0 0,15.029 0,35.426 14.632,50 35.368,50 "/>
<path opacity="0.8" fill="#FFFFFF" d="M34.538,2h-19.05L2,15.827v18.765L15.462,48h19.077L48,34.66V15.421L34.538,2z M33.403,45.24
	h-0.152l-11.28-11.446l2.382-2.662h-8.177v8.382l2.677-2.882l8.868,8.608H16.602L4.76,33.446V16.95l3.322-3.406l8.829,7.955
	l-5.029,5.221h16.059V10.765L22.691,16l-8.885-8.323l2.846-2.917h16.745L45.24,16.567v14.745l-5.622-5.298l4.118-3.706H32.353v10.75
	l3.72-3.691l6.368,6.915L33.403,45.24z"/>
<polygon opacity="0.2" fill="#FFFFFF" points="42.896,7.506 35.368,0 14.662,0 0,15.029 0,35.427 7.503,42.899 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.TrafficManagerDisabled()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#804998" points="50,35.5 50,14.588 35.368,0 14.662,0 0,15.029 0,35.426 14.632,50 35.368,50 "/>
<path opacity="0.8" fill="#FFFFFF" enable-background="new    " d="M34.538,2h-19.05L2,15.827v18.765L15.462,48h19.077L48,34.66
	V15.421L34.538,2z M33.403,45.24h-0.152l-11.28-11.446l2.382-2.662h-8.177v8.382l2.677-2.882l8.868,8.608H16.602L4.76,33.446V16.95
	l3.322-3.406l8.829,7.955l-5.029,5.221h16.059V10.765L22.691,16l-8.885-8.323l2.846-2.917h16.745L45.24,16.567v14.745l-5.622-5.298
	l4.118-3.706H32.353v10.75l3.72-3.691l6.368,6.915L33.403,45.24z"/>
<polygon opacity="0.2" fill="#FFFFFF" enable-background="new    " points="42.896,7.506 35.368,0 14.662,0 0,15.029 0,35.427 
	7.503,42.899 "/>
<circle fill="#7A7A7A" cx="8" cy="42" r="8"/>
<rect x="3" y="41" fill="#FFFFFF" width="10" height="2"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.TrafficManagerEnabled()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#804998" points="50,35.5 50,14.588 35.368,0 14.662,0 0,15.029 0,35.426 14.632,50 35.368,50 "/>
<path opacity="0.8" fill="#FFFFFF" enable-background="new    " d="M34.538,2h-19.05L2,15.827v18.765L15.462,48h19.077L48,34.66
	V15.421L34.538,2z M33.403,45.24h-0.152l-11.28-11.446l2.382-2.662h-8.177v8.382l2.677-2.882l8.868,8.608H16.602L4.76,33.446V16.95
	l3.322-3.406l8.829,7.955l-5.029,5.221h16.059V10.765L22.691,16l-8.885-8.323l2.846-2.917h16.745L45.24,16.567v14.745l-5.622-5.298
	l4.118-3.706H32.353v10.75l3.72-3.691l6.368,6.915L33.403,45.24z"/>
<polygon opacity="0.2" fill="#FFFFFF" enable-background="new    " points="42.896,7.506 35.368,0 14.662,0 0,15.029 0,35.427 
	7.503,42.899 "/>
<circle fill="#7FBA00" cx="8" cy="42" r="8"/>
<path fill="#FFFFFF" d="M3.989,42.469L3.7,42.156c-0.075-0.083-0.075-0.214,0.012-0.293l0.835-0.772
	c0.04-0.036,0.087-0.055,0.139-0.055c0.059,0,0.111,0.024,0.15,0.067l2.296,2.462l3.951-5.06c0.04-0.051,0.099-0.079,0.162-0.079
	c0.048,0,0.091,0.016,0.127,0.044l0.903,0.697c0.044,0.032,0.071,0.079,0.079,0.135c0.004,0.055-0.008,0.111-0.044,0.15
	l-5.075,6.497L3.989,42.469z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Versions()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M0,33.847c0,0.853,0.691,1.545,1.545,1.545h35.41c0.854,0,1.545-0.692,1.545-1.545V9.724H0V33.847z"/>
<path fill="#A0A1A2" d="M36.955,2.564H1.545C0.691,2.564,0,3.255,0,4.109v5.873h38.501V4.109
	C38.501,3.255,37.808,2.564,36.955,2.564"/>
<path opacity="0.2" fill="#FFFFFF" d="M1.551,2.564c-0.853,0-1.545,0.691-1.545,1.545v5.614v2.569v21.555
	c0,0.853,0.692,1.545,1.545,1.545h1.685L33.588,2.564H1.551z"/>
<path fill="#0072C6" d="M11.499,45.52c0,0.853,0.691,1.545,1.545,1.545h35.41c0.854,0,1.545-0.692,1.545-1.545V21.396H11.499V45.52z
	"/>
<path fill="#3E3E3E" d="M48.455,14.237h-35.41c-0.854,0-1.545,0.691-1.545,1.545v5.873H50v-5.873
	C50,14.928,49.308,14.237,48.455,14.237"/>
<path opacity="0.2" fill="#FFFFFF" d="M13.05,14.237c-0.853,0-1.545,0.691-1.545,1.545v5.614v2.569V45.52
	c0,0.853,0.692,1.545,1.545,1.545h1.685l30.352-32.829H13.05z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.VirtualMachine()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7A7A7A" d="M32.426,38.534h-1.191h-11.79H18.83c1.634,5.768-0.561,6.595-10.175,6.595v3.02h12.227h8.927h11.538v-3.02
	C31.733,45.129,30.79,44.305,32.426,38.534"/>
<path fill="#A0A1A2" d="M46.98,2H2.718C1.214,2,0.001,3.345,0.001,4.847v30.866c0,1.493,1.213,2.823,2.717,2.823H46.98
	c1.501,0,3.021-1.33,3.021-2.823V4.847C50.001,3.341,48.481,2,46.98,2"/>
<path opacity="0.2" fill="#FFFFFF" enable-background="new    " d="M47.011,2.003c-0.011,0-0.021-0.002-0.031-0.002H2.717
	C1.213,2.001,0,3.345,0,4.848v30.865c0,1.494,1.213,2.824,2.717,2.824H3.77L47.011,2.003z"/>
<polygon fill="#59B4D9" points="46.098,5.848 46.098,34.689 3.79,34.689 3.79,5.848 "/>
<polygon fill="#59B4D9" points="3.79,34.689 3.848,34.689 3.848,5.849 42.528,5.791 42.53,5.791 3.79,5.849 "/>
<rect x="8.655" y="45.128" fill="#A0A1A2" width="32.692" height="3.021"/>
<path fill="#B8D432" d="M25.518,4.095c0,0.392-0.318,0.71-0.71,0.71c-0.393,0-0.709-0.318-0.709-0.71c0-0.393,0.316-0.71,0.709-0.71
	C25.2,3.385,25.518,3.702,25.518,4.095"/>
<path fill="#FFFFFF" d="M25.546,19.394c-0.045,0-0.091-0.014-0.134-0.038l-8.804-5.082c-0.081-0.048-0.132-0.137-0.132-0.231
	c0-0.095,0.051-0.183,0.132-0.23l8.751-5.049c0.082-0.046,0.182-0.046,0.263,0l8.807,5.084c0.082,0.047,0.131,0.135,0.131,0.23
	c0,0.096-0.049,0.183-0.131,0.23l-8.748,5.048C25.638,19.38,25.595,19.394,25.546,19.394"/>
<path opacity="0.7" fill="#FFFFFF" enable-background="new    " d="M24.281,31.746c-0.05,0-0.095-0.012-0.134-0.036l-8.778-5.066
	c-0.085-0.047-0.136-0.133-0.136-0.231V16.247c0-0.096,0.051-0.183,0.136-0.231c0.081-0.049,0.181-0.049,0.268,0l8.777,5.064
	c0.078,0.05,0.13,0.137,0.13,0.233v10.166c0,0.097-0.052,0.183-0.13,0.231C24.37,31.734,24.324,31.746,24.281,31.746"/>
<path opacity="0.4" fill="#FFFFFF" enable-background="new    " d="M26.766,31.746c-0.048,0-0.093-0.012-0.138-0.036
	c-0.078-0.048-0.129-0.134-0.129-0.231V21.377c0-0.094,0.051-0.182,0.129-0.231l8.777-5.064c0.084-0.048,0.182-0.048,0.264,0
	c0.084,0.047,0.135,0.135,0.135,0.23v10.101c0,0.098-0.051,0.184-0.135,0.231l-8.774,5.066
	C26.859,31.734,26.812,31.746,26.766,31.746"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.VirtualNetwork()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3999C6" d="M49.7,25.7c0.5-0.5,0.4-1.3,0-1.8l-2.4-2.4L36.5,11c-0.5-0.5-1.2-0.5-1.7,0l0,0c-0.5,0.5-0.6,1.3,0,1.8
	l11.3,11.1c0.5,0.5,0.5,1.3,0,1.8L34.6,37.2c-0.5,0.5-0.5,1.3,0,1.8l0,0c0.5,0.5,1.3,0.4,1.7,0l10.7-10.6c0,0,0,0,0.1-0.1L49.7,25.7
	z"/>
<path fill="#3999C6" d="M0.3,25.7c-0.5-0.5-0.4-1.3,0-1.8l2.4-2.4L13.5,11c0.5-0.5,1.2-0.5,1.7,0l0,0c0.5,0.5,0.6,1.3,0,1.8
	L4.1,23.9c-0.5,0.5-0.5,1.3,0,1.8l11.3,11.5c0.5,0.5,0.5,1.3,0,1.8l0,0c-0.5,0.5-1.3,0.4-1.7,0L2.8,28.5c0,0,0,0-0.1-0.1L0.3,25.7z"
	/>
<path fill="#7FBA00" d="M18.2,24.8c0,1.9-1.6,3.3-3.3,3.3s-3.5-1.6-3.5-3.3s1.4-3.3,3.5-3.3C16.9,21.5,18.2,23.1,18.2,24.8z"/>
<path fill="#7FBA00" d="M28.3,24.8c0,1.9-1.6,3.3-3.3,3.3s-3.5-1.6-3.5-3.3s1.6-3.3,3.5-3.3S28.3,23.1,28.3,24.8z"/>
<circle fill="#7FBA00" cx="35.2" cy="24.8" r="3.3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebEnvironment()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#B8D432" points="50,43.012 0,43.012 6,32.012 44,32.012 "/>
<path fill="#59B4D9" d="M35.387,34.763c-3.069,2.347-6.684,3.489-10.274,3.489c-5.094,0-10.131-2.287-13.46-6.64
	C5.966,24.186,7.37,13.566,14.809,7.876c3.067-2.361,6.687-3.487,10.273-3.487c5.094,0,10.132,2.287,13.46,6.645
	C44.23,18.457,42.812,29.079,35.387,34.763"/>
<path fill="#FFFFFF" d="M32.276,24.289c1.23,1.604,3.513,1.894,5.108,0.68c0.083-0.063,0.148-0.141,0.224-0.209
	c1.632,1.149,2.901,2.001,3.54,2.436c0.189-0.49,0.315-0.954,0.449-1.445c-0.675-0.502-1.718-1.303-3.037-2.372
	c0.433-1.138,0.296-2.465-0.494-3.503c-1.13-1.463-3.133-1.835-4.698-0.933c-1.725-1.547-3.619-3.32-5.617-5.306
	c6.208-3.339,10.619-2.85,10.619-2.85c-0.736-0.939-1.562-1.761-2.443-2.504c-2.618-0.404-6.685-0.359-11.332,2.113l-0.002-0.002
	l-0.001,0c-1.549-1.621-3.125-3.358-4.73-5.224c-0.768,0.246-1.519,0.55-2.244,0.912c1.185,1.939,2.779,3.894,4.575,5.802h0
	c0.007,0.007,0.014,0.014,0.021,0.021c-1.491,1.046-3.188,2.452-4.713,4.065c-0.196,0.209-0.385,0.42-0.571,0.631
	c-0.919-0.192-1.886-0.136-2.789,0.191c-1.534-3.309-1.411-5.967-1.168-7.337c-0.666,0.698-1.288,1.431-1.824,2.213
	c-0.4,1.637-0.514,3.996,0.667,6.839c-1.368,1.79-1.432,4.328-0.004,6.2c0.119,0.155,0.246,0.299,0.379,0.437
	c-0.624,2.125-0.903,4.175-0.989,5.935c0.161,0.218,0.161,0.394,0.32,0.607c0.812,1.042,1.839,2.027,2.824,2.822
	c-0.122-1.862,0.003-4.717,1.155-7.81c0.795,0.06,1.603-0.065,2.359-0.383c0.434,0.382,0.887,0.767,1.372,1.159
	c1.661,1.315,3.319,2.339,4.935,3.145c-0.084,0.822,0.122,1.676,0.656,2.383c1.141,1.474,3.255,1.749,4.73,0.622
	c0.307-0.235,0.549-0.519,0.75-0.824c2.635,0.587,4.936,0.69,6.643,0.69c0.261,0,1.475-1.65,2.17-2.673
	c-1.039,0.218-4.121,0.641-8.332-0.569c-0.102-0.472-0.296-0.932-0.608-1.341c-1.07-1.402-3.026-1.705-4.483-0.767
	c-1.464-0.794-2.997-1.789-4.578-3.041c-0.319-0.252-0.625-0.505-0.92-0.757c0.966-1.522,1.069-3.473,0.216-5.114
	c0.194-0.194,0.384-0.388,0.59-0.581c1.565-1.462,3.038-2.633,4.416-3.573c-0.042-0.038-0.078-0.114-0.118-0.153
	c0.041,0.038,0.079,0.08,0.12,0.08h-0.001c2.114,2,4.356,3.843,6.479,5.498C31.334,21.688,31.422,23.173,32.276,24.289z"/>
<rect y="43" fill="#7A7A7A" width="50" height="3"/>
<rect y="43" opacity="0.5" fill="#DD5900" width="50" height="3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebHosting()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M29.364,47.462c0,1.402-1.136,2.538-2.538,2.538H2.538C1.136,50,0,48.864,0,47.462V2.538
	C0,1.136,1.136,0,2.538,0h24.288c1.402,0,2.538,1.136,2.538,2.538V47.462z"/>
<path fill="#1E1E1E" d="M4.316,27.024c0-1.762,1.429-3.191,3.191-3.191h14.614c1.762,0,3.191,1.429,3.191,3.191l0,0
	c0,1.762-1.429,3.191-3.191,3.191H7.507C5.745,30.215,4.316,28.787,4.316,27.024L4.316,27.024z"/>
<circle fill="#B8D432" cx="7.643" cy="27.024" r="2.142"/>
<path fill="#1E1E1E" d="M4.316,17.545c0-1.762,1.429-3.191,3.191-3.191h14.614c1.762,0,3.191,1.429,3.191,3.191l0,0
	c0,1.762-1.429,3.191-3.191,3.191H7.507C5.745,20.736,4.316,19.307,4.316,17.545L4.316,17.545z"/>
<circle fill="#B8D432" cx="7.643" cy="17.545" r="2.142"/>
<path fill="#1E1E1E" d="M4.316,8.065c0-1.762,1.429-3.191,3.191-3.191h14.614c1.762,0,3.191,1.429,3.191,3.191l0,0
	c0,1.762-1.429,3.191-3.191,3.191H7.507C5.745,11.257,4.316,9.828,4.316,8.065L4.316,8.065z"/>
<circle fill="#B8D432" cx="7.643" cy="8.065" r="2.142"/>
<path fill="#59B4D9" d="M49.949,45.373c0-2.555-2.051-4.626-4.585-4.626c-0.191,0-0.379,0.015-0.559,0.04
	c0.28-1.039,0.434-2.132,0.434-3.261c0-6.81-5.475-12.331-12.227-12.331c-5.395,0-9.969,3.523-11.594,8.413
	c-0.856-0.301-1.772-0.471-2.73-0.471c-4.619,0-8.357,3.775-8.357,8.43c0,4.658,3.738,8.433,8.357,8.433
	c0,0,0.008-0.003,0.012-0.003V50h27.036l-0.005-0.023C48.09,49.791,49.949,47.804,49.949,45.373"/>
<path opacity="0.2" fill="#FFFFFF" d="M23.128,50c-1.099-1.092-1.918-2.496-2.29-4.123c-1.036-4.538,1.768-9.051,6.271-10.079
	c0.934-0.213,1.864-0.252,2.766-0.149c0.409-4.24,2.995-8.011,6.848-9.872c-1.171-0.376-2.417-0.583-3.712-0.583
	c-5.395,0-9.969,3.523-11.594,8.413c-0.856-0.301-1.771-0.471-2.73-0.471c-4.619,0-8.357,3.775-8.357,8.43
	c0,4.658,3.738,8.433,8.357,8.433c0,0,0.008-0.003,0.012-0.003V50H23.128z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebJobs()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M33.143,38.974c-3.735,2.856-8.137,4.248-12.507,4.248c-6.201,0-12.333-2.784-16.385-8.084
	c-6.923-9.039-5.214-21.967,3.842-28.893C11.827,3.371,16.233,2,20.598,2c6.201,0,12.334,2.784,16.385,8.089
	C43.907,19.125,42.181,32.055,33.143,38.974"/>
<path fill="#FFFFFF" d="M29.356,26.225c1.497,1.953,4.276,2.305,6.218,0.828c0.101-0.077,0.18-0.171,0.272-0.255
	c1.987,1.399,3.532,2.436,4.309,2.965c0.23-0.596,0.383-1.161,0.547-1.759c-0.821-0.611-2.092-1.586-3.698-2.887
	c0.527-1.385,0.361-3.001-0.602-4.264c-1.376-1.781-3.814-2.234-5.719-1.136c-2.1-1.884-4.406-4.042-6.838-6.459
	c7.557-4.064,12.927-3.469,12.927-3.469c-0.896-1.143-1.901-2.144-2.974-3.049c-3.187-0.492-8.138-0.437-13.795,2.572l-0.002-0.003
	l-0.001,0c-1.885-1.973-3.804-4.088-5.758-6.359c-0.935,0.299-1.849,0.67-2.731,1.111c1.435,2.348,3.363,4.715,5.536,7.026
	c-1.817,1.277-3.811,3.036-5.679,5.011c-0.239,0.255-0.469,0.511-0.695,0.768C9.556,16.632,8.38,16.7,7.281,17.098
	C5.413,13.07,5.563,9.835,5.858,8.166c-0.811,0.849-1.568,1.742-2.22,2.694c-0.487,1.992-0.626,4.864,0.812,8.325
	c-1.665,2.179-1.743,5.269-0.004,7.548c0.145,0.189,0.3,0.364,0.461,0.532c-0.76,2.587-1.099,5.082-1.204,7.225
	c0.195,0.266,0.195,0.48,0.389,0.739c0.989,1.268,2.238,2.467,3.438,3.435c-0.149-2.267,0.003-5.742,1.406-9.507
	c0.967,0.073,1.951-0.079,2.872-0.467c0.528,0.465,1.08,0.934,1.67,1.411c2.022,1.601,4.041,2.847,6.007,3.829
	c-0.102,1.001,0.149,2.04,0.798,2.901c1.39,1.794,3.962,2.129,5.758,0.758c0.374-0.286,0.669-0.632,0.913-1.003
	c3.207,0.715,6.009,0.84,8.086,0.84c0.318,0,1.795-2.009,2.641-3.254c-1.265,0.265-5.016,0.78-10.143-0.693
	c-0.124-0.575-0.36-1.134-0.741-1.632c-1.302-1.707-3.683-2.076-5.457-0.934c-1.782-0.967-3.648-2.178-5.572-3.702
	c-0.388-0.307-0.761-0.615-1.12-0.922c1.176-1.853,1.301-4.228,0.262-6.225c0.236-0.236,0.468-0.473,0.719-0.707
	c1.906-1.78,3.698-3.205,5.376-4.349c-0.059-0.054-0.112-0.113-0.169-0.168c0.058,0.054,0.113,0.109,0.172,0.163l-0.002,0.001
	c2.573,2.38,5.302,4.635,7.887,6.65C28.209,23.101,28.316,24.866,29.356,26.225z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#804998" d="M44.958,45.015v-3.149c0-1.511-1.261-2.771-2.774-2.771h-3.655
	L35,35.569v-5.408h-3.782v6.037c0,0.505,0.252,1.008,0.505,1.386l4.537,4.408v2.897c0,1.511,1.261,2.77,2.774,2.77h3.151
	C43.698,47.786,44.958,46.527,44.958,45.015 M38.908,45.267c-0.126,0-0.253-0.126-0.253-0.252v-3.149
	c0-0.126,0.127-0.252,0.253-0.252h3.151c0.126,0,0.253,0.126,0.253,0.252v3.149c0,0.126-0.126,0.252-0.253,0.252H38.908z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#68217A" d="M28.697,30.172h0.263v-0.01h12.057v0.01h0.285
	c0.126,1.386,1.387,2.519,2.773,2.519h3.151c1.513,0,2.773-1.259,2.773-2.771v-3.149C50,25.259,48.739,24,47.227,24h-3.151
	c-1.512,0-2.647,1.134-2.773,2.519H28.697C28.571,25.134,27.311,24,25.925,24h-3.151C21.261,24,20,25.259,20,26.771v3.149
	c0,1.512,1.261,2.771,2.773,2.771h3.151C27.437,32.691,28.571,31.557,28.697,30.172 M44.075,26.393h3.151
	c0.126,0,0.252,0.126,0.252,0.252v3.149c0,0.126-0.126,0.252-0.252,0.252h-3.151c-0.126,0-0.251-0.126-0.251-0.252v-3.149
	C43.824,26.519,43.95,26.393,44.075,26.393 M22.521,30.172c-0.126,0-0.253-0.126-0.253-0.252v-3.149
	c0-0.126,0.127-0.252,0.253-0.252h3.151c0.126,0,0.253,0.126,0.253,0.252v3.149c0,0.126-0.126,0.252-0.253,0.252H22.521z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Website()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M40.193,44.839c-4.53,3.464-9.867,5.151-15.167,5.151c-7.52,0-14.956-3.376-19.87-9.803
	C-3.24,29.225-1.168,13.548,9.814,5.148C14.343,1.663,19.686,0,24.979,0c7.52,0,14.958,3.376,19.87,9.809
	C53.247,20.768,51.154,36.448,40.193,44.839"/>
<path fill="#FFFFFF" d="M35.6,29.378c1.816,2.368,5.186,2.796,7.541,1.004c0.123-0.094,0.218-0.208,0.33-0.309
	c2.409,1.697,4.082,2.817,5.025,3.459c0.279-0.723,0.472-1.417,0.67-2.142c-0.996-0.741-2.343-1.778-4.29-3.356
	c0.639-1.68,0.437-3.64-0.73-5.171c-1.669-2.16-4.626-2.709-6.936-1.377c-2.546-2.284-5.343-4.902-8.293-7.833
	c9.165-4.929,15.676-4.207,15.676-4.207c-1.087-1.386-2.305-2.6-3.606-3.697c-3.865-0.597-9.869-0.53-16.729,3.119l-0.002-0.003
	l-0.001,0c-2.286-2.393-4.613-4.958-6.983-7.712c-1.134,0.363-2.242,0.812-3.312,1.347c1.749,2.862,4.102,5.749,6.754,8.565h0
	c0.005,0.006,0.011,0.011,0.017,0.017c-2.211,1.546-4.673,3.613-6.944,6.015c-0.29,0.309-0.569,0.62-0.842,0.931
	c-1.357-0.284-2.784-0.201-4.117,0.282C6.564,13.425,6.746,9.501,7.104,7.478c-0.983,1.03-1.901,2.113-2.692,3.267
	C3.821,13.16,3.653,16.643,5.397,20.84c-2.019,2.642-2.114,6.389-0.005,9.153c0.176,0.229,0.364,0.442,0.559,0.645
	c-0.921,3.137-1.333,6.163-1.46,8.761c0.237,0.322,0.237,0.582,0.472,0.896c1.199,1.538,2.705,2.834,4.16,4.008
	c-0.18-2.75,0.014-6.806,1.714-11.372c1.173,0.089,2.366-0.096,3.483-0.566c0.64,0.563,1.31,1.132,2.025,1.711
	c2.453,1.942,4.9,3.453,7.285,4.643c-0.124,1.213,0.18,2.474,0.968,3.518c1.685,2.176,4.805,2.582,6.983,0.919
	c0.453-0.347,0.811-0.766,1.108-1.216c3.889,0.866,7.287,1.019,9.806,1.019c0.386,0,2.177-2.436,3.203-3.946
	c-1.534,0.321-6.083,0.946-12.3-0.84c-0.15-0.698-0.437-1.376-0.898-1.98c-1.579-2.07-4.466-2.518-6.618-1.133
	c-2.161-1.172-4.424-2.641-6.758-4.49c-0.471-0.373-0.923-0.745-1.359-1.118c1.426-2.247,1.578-5.127,0.318-7.55
	c0.286-0.286,0.567-0.573,0.871-0.857c2.311-2.159,4.485-3.887,6.519-5.274c-0.082-0.076-0.156-0.156-0.236-0.233
	c0.081,0.075,0.157,0.152,0.239,0.227c-0.001,0-0.001,0.001-0.002,0.001c3.121,2.886,6.43,5.621,9.564,8.065
	C34.209,25.589,34.339,27.73,35.6,29.378z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebsitePower()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M29.833,31.322c-1.443,1.104-3.144,1.642-4.832,1.642c-2.396,0-4.765-1.076-6.331-3.123
	c-2.676-3.494-2.014-8.488,1.484-11.165c1.443-1.11,3.145-1.64,4.832-1.64c2.396,0,4.765,1.076,6.33,3.125
	C33.992,23.653,33.324,28.649,29.833,31.322"/>
<path fill="#59B4D9" d="M29.833,31.322c-1.443,1.104-3.144,1.642-4.832,1.642c-2.396,0-4.765-1.076-6.331-3.123
	c-2.676-3.494-2.014-8.488,1.484-11.165c1.443-1.11,3.145-1.64,4.832-1.64c2.396,0,4.765,1.076,6.33,3.125
	C33.992,23.653,33.324,28.649,29.833,31.322"/>
<path fill="#FFFFFF" d="M28.37,26.396c0.578,0.754,1.651,0.891,2.401,0.321c0.04-0.03,0.07-0.067,0.106-0.1
	c0.768,0.541,1.365,0.941,1.665,1.146c0.088-0.231,0.148-0.449,0.211-0.68c-0.315-0.235-0.813-0.618-1.428-1.117
	c0.203-0.535,0.138-1.159-0.234-1.646c-0.531-0.688-1.47-0.863-2.206-0.44c-0.814-0.73-1.7-1.555-2.644-2.494
	c2.92-1.57,4.993-1.34,4.993-1.34c-0.345-0.442-0.734-0.828-1.148-1.178c-1.231-0.189-3.145-0.169-5.33,0.994l0,0c0,0,0,0,0,0
	c-0.729-0.762-1.47-1.58-2.226-2.457c-0.361,0.116-0.713,0.258-1.055,0.429c0.557,0.912,1.307,1.831,2.152,2.729
	c0.022-0.016,0.044-0.028,0.066-0.044c-0.003,0.002-0.007,0.005-0.01,0.008c-0.018,0.013-0.036,0.023-0.054,0.036
	c0.001,0.001,0.001,0.001,0.002,0.002c-0.697,0.493-1.503,1.171-2.21,1.919c-0.095,0.101-0.177,0.204-0.267,0.306
	c-0.431-0.091-0.879-0.063-1.303,0.088c-0.724-1.558-0.674-2.813-0.559-3.459c-0.314,0.329-0.607,0.673-0.858,1.04
	c-0.189,0.772-0.241,1.885,0.317,3.225c-0.641,0.842-0.676,2.03-0.004,2.909c0.058,0.075,0.126,0.136,0.191,0.203
	c-0.293,0.999-0.439,1.966-0.479,2.793c0.075,0.103,0.075,0.185,0.15,0.284c0.382,0.491,0.864,0.955,1.329,1.329
	c-0.058-0.877,0.005-2.223,0.55-3.68c0.371,0.027,0.746-0.031,1.099-0.179c0.206,0.181,0.418,0.364,0.648,0.55
	c0.782,0.619,1.561,1.097,2.322,1.476c-0.04,0.388,0.056,0.791,0.307,1.125c0.537,0.693,1.531,0.822,2.226,0.292
	c0.147-0.113,0.263-0.25,0.359-0.396c1.232,0.273,2.318,0.333,3.117,0.333c0.123,0,0.694-0.776,1.022-1.257
	c-0.489,0.102-1.941,0.295-3.92-0.272c-0.048-0.22-0.139-0.435-0.285-0.625c-0.503-0.659-1.422-0.802-2.106-0.363
	c-0.689-0.374-1.411-0.84-2.155-1.43c-0.155-0.123-0.289-0.245-0.432-0.367c0.446-0.711,0.496-1.615,0.102-2.382
	c0.094-0.095,0.174-0.191,0.275-0.285c0.737-0.688,1.429-1.24,2.079-1.681c0,0,0,0-0.001-0.001c0.994,0.92,2.049,1.791,3.047,2.569
	C27.927,25.189,27.968,25.871,28.37,26.396z"/>
<path fill="#FFFFFF" d="M23.755,20.477c0.142-0.098,0.284-0.196,0.425-0.283C24.039,20.281,23.897,20.379,23.755,20.477
	C23.755,20.477,23.755,20.477,23.755,20.477z"/>
<polygon fill="#FFFFFF" points="25.144,22.06 25.131,22.046 25.145,22.06 "/>
<path fill="#A0A1A2" d="M41.386,50c-6.829,0-16.33-5.585-26.069-15.323C1.768,21.125-3.448,8.134,2.338,2.348
	C3.875,0.811,6.039,0,8.599,0c6.829,0,16.331,5.585,26.069,15.325c6.637,6.638,11.379,13.177,13.715,18.916
	c2.365,5.811,2.104,10.572-0.736,13.413C46.111,49.189,43.946,50,41.386,50 M8.599,3.078c-1.75,0-3.124,0.486-4.083,1.445
	C0.791,8.249,4.052,19.056,17.495,32.498c9.031,9.032,17.962,14.424,23.892,14.424c1.75,0,3.123-0.486,4.082-1.446
	c1.908-1.908,1.932-5.486,0.063-10.074C43.35,30.04,38.84,23.85,32.49,17.502C23.459,8.471,14.528,3.078,8.599,3.078"/>
<path fill="#0F0F0F" d="M8.599,50c-2.56,0-4.724-0.811-6.261-2.346c-2.84-2.841-3.101-7.602-0.735-13.413
	c2.336-5.739,7.078-12.278,13.715-18.916C25.056,5.585,34.558,0,41.387,0c2.56,0,4.724,0.811,6.26,2.347
	c5.786,5.787,0.57,18.778-12.979,32.33C24.929,44.415,15.427,50,8.599,50 M41.387,3.078c-5.93,0-14.861,5.393-23.893,14.424
	C11.146,23.85,6.634,30.04,4.453,35.402c-1.869,4.588-1.845,8.166,0.063,10.074c0.959,0.96,2.332,1.446,4.083,1.446
	c5.93,0,14.86-5.392,23.892-14.424C45.933,19.056,49.194,8.249,45.468,4.522C44.509,3.564,43.137,3.078,41.387,3.078"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebsiteStaging()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M40.771,43.034c0,0.687-0.557,1.243-1.243,1.243H11.475c-0.686,0-1.243-0.557-1.243-1.243
	c0-0.687,0.557-1.243,1.243-1.243h28.053C40.214,41.791,40.771,42.348,40.771,43.034"/>
<path fill="#3E3E3E" d="M28.683,48.758c0,0.687-0.557,1.242-1.243,1.242H11.474c-0.686,0-1.242-0.555-1.242-1.242
	c0-0.687,0.557-1.243,1.242-1.243H27.44C28.126,47.515,28.683,48.071,28.683,48.758"/>
<path fill="#3E3E3E" d="M40.612,31.237c0,0.687-0.557,1.243-1.243,1.243H11.474c-0.686,0-1.242-0.557-1.242-1.243
	c0-0.685,0.557-1.243,1.242-1.243h27.895C40.055,29.994,40.612,30.552,40.612,31.237"/>
<path fill="#3E3E3E" d="M35.349,36.961c0,0.687-0.557,1.243-1.243,1.243H11.474c-0.686,0-1.242-0.557-1.242-1.243
	c0-0.687,0.557-1.243,1.242-1.243h22.631C34.792,35.718,35.349,36.275,35.349,36.961"/>
<path fill="#59B4D9" d="M46.4,22.105c0-2.799-2.247-5.067-5.022-5.067c-0.209,0-0.415,0.016-0.613,0.043
	c0.306-1.138,0.476-2.335,0.476-3.572C41.24,6.048,35.243,0,27.846,0c-5.91,0-10.921,3.859-12.7,9.217
	c-0.938-0.33-1.941-0.516-2.99-0.516C7.095,8.701,3,12.836,3,17.936c0,5.103,4.095,9.238,9.155,9.238c0,0,0.009-0.004,0.013-0.004
	v0.004h29.617l-0.005-0.025C44.364,26.945,46.4,24.768,46.4,22.105"/>
<path opacity="0.2" fill="#FFFFFF" d="M17.019,27.174c-1.204-1.196-2.101-2.734-2.508-4.517c-1.135-4.972,1.937-9.915,6.87-11.041
	c1.023-0.234,2.042-0.276,3.03-0.163c0.448-4.645,3.281-8.776,7.502-10.815C30.629,0.226,29.264,0,27.846,0
	c-5.91,0-10.921,3.859-12.7,9.217c-0.938-0.33-1.941-0.516-2.99-0.516C7.095,8.701,3,12.836,3,17.936
	c0,5.103,4.095,9.238,9.155,9.238c0,0,0.009-0.004,0.013-0.004v0.004H17.019z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebSlots()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#A0A1A2" d="M42.744,50H7.256c-3.27,0-5.93-2.66-5.93-5.93v-9.863c0-0.864,0.699-1.563,1.563-1.563
	s1.563,0.699,1.563,1.563v9.863c0,1.546,1.259,2.805,2.805,2.805h35.489c1.546,0,2.805-1.259,2.805-2.805v-9.863
	c0-0.864,0.699-1.563,1.563-1.563s1.563,0.699,1.563,1.563v9.863C48.674,47.34,46.014,50,42.744,50z"/>
<rect x="7.006" y="16.691" fill="#59B4D9" width="9.87" height="27.614"/>
<rect x="20.065" y="16.691" fill="#59B4D9" width="9.87" height="27.614"/>
<rect x="33.125" y="16.691" fill="#B8D432" width="9.869" height="27.614"/>
<g>
	<path fill="#B8D432" d="M16.908,11.426C16.908,5.125,22.035,0,28.336,0S39.89,4.784,39.89,11.084c0,0.864-0.699,1.563-1.563,1.563
		s-1.563-0.699-1.563-1.563c0-4.578-3.852-7.959-8.429-7.959s-8.302,3.723-8.302,8.301H16.908z"/>
</g>
<polygon fill="#B8D432" points="44.365,8.782 38.199,14.948 32.033,8.782 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.WebTest()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M33.143,38.974c-3.735,2.856-8.137,4.248-12.507,4.248c-6.201,0-12.333-2.784-16.385-8.084
	c-6.923-9.039-5.214-21.967,3.842-28.893C11.827,3.371,16.233,2,20.598,2c6.201,0,12.334,2.784,16.385,8.089
	C43.907,19.125,42.182,32.055,33.143,38.974"/>
<path fill="#59B4D9" d="M33.143,38.974c-3.735,2.856-8.137,4.248-12.507,4.248c-6.201,0-12.333-2.784-16.385-8.084
	c-6.923-9.039-5.214-21.967,3.842-28.893C11.827,3.371,16.233,2,20.598,2c6.201,0,12.334,2.784,16.385,8.089
	C43.907,19.125,42.182,32.055,33.143,38.974"/>
<path fill="#FFFFFF" d="M29.356,26.225c1.497,1.953,4.276,2.305,6.218,0.828c0.101-0.077,0.18-0.171,0.272-0.255
	c1.986,1.399,3.532,2.436,4.309,2.965c0.23-0.596,0.383-1.161,0.547-1.759c-0.821-0.611-2.092-1.586-3.698-2.887
	c0.527-1.385,0.361-3.001-0.602-4.264c-1.376-1.781-3.814-2.234-5.719-1.136c-2.1-1.884-4.406-4.042-6.838-6.459
	c7.557-4.064,12.927-3.469,12.927-3.469c-0.896-1.143-1.901-2.144-2.973-3.049c-3.187-0.492-8.138-0.437-13.795,2.572l-0.002-0.003
	l-0.001,0c-1.885-1.973-3.804-4.088-5.758-6.359c-0.935,0.299-1.849,0.67-2.731,1.111c1.442,2.36,3.383,4.741,5.57,7.063h0
	c0,0,0,0,0,0c-1.822,1.275-3.841,2.994-5.713,4.974c-0.239,0.255-0.469,0.511-0.695,0.768C9.556,16.632,8.38,16.7,7.281,17.098
	C5.413,13.07,5.563,9.835,5.858,8.166c-0.811,0.849-1.568,1.742-2.22,2.694c-0.487,1.992-0.626,4.864,0.812,8.325
	c-1.665,2.179-1.743,5.269-0.004,7.548c0.145,0.189,0.3,0.364,0.461,0.532c-0.76,2.587-1.099,5.082-1.204,7.225
	c0.195,0.266,0.195,0.48,0.389,0.739c0.989,1.268,2.238,2.467,3.438,3.435c-0.149-2.267,0.003-5.742,1.406-9.507
	c0.967,0.073,1.951-0.079,2.872-0.467c0.528,0.465,1.08,0.934,1.67,1.411c2.022,1.601,4.041,2.847,6.007,3.829
	c-0.102,1.001,0.149,2.04,0.798,2.901c1.39,1.794,3.962,2.129,5.758,0.758c0.374-0.286,0.669-0.632,0.913-1.003
	c3.207,0.714,6.009,0.84,8.086,0.84c0.318,0,1.795-2.009,2.641-3.254c-1.265,0.265-5.016,0.78-10.143-0.693
	c-0.124-0.575-0.36-1.134-0.741-1.633c-1.302-1.707-3.683-2.076-5.457-0.934c-1.782-0.967-3.648-2.178-5.572-3.702
	c-0.388-0.307-0.761-0.615-1.12-0.922c1.176-1.853,1.301-4.228,0.262-6.225c0.236-0.236,0.468-0.473,0.719-0.707
	c1.906-1.78,3.698-3.205,5.376-4.349c-0.066-0.062-0.126-0.127-0.192-0.189c0.066,0.061,0.128,0.123,0.194,0.184
	c0,0-0.001,0.001-0.002,0.001c2.573,2.38,5.302,4.635,7.887,6.65C28.209,23.101,28.316,24.866,29.356,26.225z"/>
<path fill="#A0A1A2" d="M49.625,45.229l-8.023-13.897v-5.625h0.145c0.96,0,1.738-0.778,1.738-1.738s-0.778-1.738-1.738-1.738h-8.754
	c-0.96,0-1.738,0.778-1.738,1.738s0.778,1.738,1.738,1.738h0.145v5.625l-8.023,13.897c-0.88,1.524-0.16,2.771,1.6,2.771h21.311
	C49.785,48,50.504,46.753,49.625,45.229z"/>
<path fill="#B8D432" d="M43.016,39.247h-3.767c0.104,0.214,0.165,0.454,0.165,0.708c0,0.894-0.725,1.619-1.619,1.619
	s-1.619-0.725-1.619-1.619c0-0.254,0.06-0.494,0.165-0.708h-4.619l-3.31,5.734h17.915L43.016,39.247z"/>
<path fill="#7FBA00" d="M37.795,41.574c0.894,0,1.619-0.725,1.619-1.619c0-0.254-0.06-0.494-0.165-0.708h-2.908
	c-0.104,0.214-0.165,0.454-0.165,0.708C36.176,40.85,36.901,41.574,37.795,41.574z"/>
<circle fill="#7FBA00" cx="39.804" cy="42.824" r="0.794"/>
<path opacity="0.25" fill="#FFFFFF" d="M25.114,45.229l8.023-13.897v-5.625h-0.145c-0.96,0-1.738-0.778-1.738-1.738
	s0.778-1.738,1.738-1.738h3.772v9.056L32.536,48h-5.822C24.954,48,24.234,46.753,25.114,45.229z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Polychromatic.Workflow()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" fill="#59B4D9" d="M37.676,26.891c-0.299-0.299-0.769-1.218-2.176-1.218h-7.67H15.5
	c-1.408,0-1.877,0.919-2.176,1.218L7.096,35.54l4.724,1.29l4.481-6.353h11.53h6.87l4.481,6.353l4.724-1.29L37.676,26.891z"/>
<rect x="23.111" y="14.465" fill-rule="evenodd" clip-rule="evenodd" fill="#59B4D9" width="4.778" height="24.488"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#59B4D9" d="M29.169,0.5h-7.338c-2.112,0-3.84,1.728-3.84,3.84v6.655
	c0,2.112,1.728,3.84,3.84,3.84h7.338c2.112,0,3.84-1.728,3.84-3.84V4.34C33.009,2.228,31.281,0.5,29.169,0.5z M29.596,10.739
	c0,0.375-0.307,0.683-0.683,0.683h-6.826c-0.375,0-0.683-0.307-0.683-0.683V4.596c0-0.375,0.307-0.683,0.683-0.683h6.826
	c0.375,0,0.683,0.307,0.683,0.683V10.739z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#0072C6" d="M29.169,0.5h-7.338c-2.112,0-3.84,1.728-3.84,3.84v6.655
	c0,2.112,1.728,3.84,3.84,3.84h7.338c2.112,0,3.84-1.728,3.84-3.84V4.34C33.009,2.228,31.281,0.5,29.169,0.5z M29.596,10.739
	c0,0.375-0.307,0.683-0.683,0.683h-6.826c-0.375,0-0.683-0.307-0.683-0.683V4.596c0-0.375,0.307-0.683,0.683-0.683h6.826
	c0.375,0,0.683,0.307,0.683,0.683V10.739z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#0072C6" d="M27.804,38.213h-4.608c-2.112,0-3.84,1.728-3.84,3.84v4.608
	c0,2.112,1.728,3.84,3.84,3.84h4.608c2.112,0,3.84-1.728,3.84-3.84v-4.608C31.643,39.941,29.916,38.213,27.804,38.213z
	 M28.23,46.404c0,0.375-0.307,0.683-0.683,0.683h-4.096c-0.375,0-0.683-0.307-0.683-0.683v-4.096c0-0.375,0.307-0.683,0.683-0.683
	h4.096c0.375,0,0.683,0.307,0.683,0.683V46.404z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#0072C6" d="M0.5,37.053v4.608c0,2.112,1.728,3.84,3.84,3.84h4.608
	c2.112,0,3.84-1.728,3.84-3.84v-4.608c0-2.112-1.728-3.84-3.84-3.84H4.34C2.228,33.213,0.5,34.941,0.5,37.053z M3.913,37.309
	c0-0.375,0.307-0.683,0.683-0.683h4.096c0.375,0,0.683,0.307,0.683,0.683v4.096c0,0.375-0.307,0.683-0.683,0.683H4.596
	c-0.375,0-0.683-0.307-0.683-0.683V37.309z"/>
<path fill-rule="evenodd" clip-rule="evenodd" fill="#0072C6" d="M46.66,33.213h-4.608c-2.112,0-3.84,1.728-3.84,3.84v4.608
	c0,2.112,1.728,3.84,3.84,3.84h4.608c2.112,0,3.84-1.728,3.84-3.84v-4.608C50.5,34.941,48.772,33.213,46.66,33.213z M47.087,41.404
	c0,0.375-0.307,0.683-0.683,0.683h-4.096c-0.375,0-0.683-0.307-0.683-0.683v-4.096c0-0.375,0.307-0.683,0.683-0.683h4.096
	c0.375,0,0.683,0.307,0.683,0.683V41.404z"/>
</svg>
</li>
</ul>
<h2>Flat Icons</h2><ul class="icons">
<li><h6>MsPortalFx.Base.Images.Add()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="16px" width="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<g>
	<polygon points="10,16 6,16 6,0 10,0 10,15.9 	"/>
</g>
<g>
	<polygon points="15.9,10 0,10 0,6 16,6 16,10 	"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.AddAlternate()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="21px" width="21px" viewBox="0 0 21 21" enable-background="new 0 0 21 21" xml:space="preserve">
<polygon points="21,9 12,9 12,0 9,0 9,9 0,9 0,12 9,12 9,21 12,21 12,12 21,12 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.AddTeamMember()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M42.959,15.338c0,4.947-4.011,8.959-8.96,8.959c-4.948,0-8.958-4.011-8.958-8.959
	c0-4.947,4.01-8.958,8.958-8.958C38.948,6.38,42.959,10.391,42.959,15.338"/>
<polygon fill="#59B4D9" points="40.55,27.368 34,36.546 27.449,27.368 18,27.368 18,50 50,50 50,27.368 "/>
<polygon fill="#59B4D9" points="8.211,23.289 8.211,18.5 4.789,18.5 4.789,23.289 0,23.289 0,26.711 4.789,26.711 4.789,31.5 
	8.211,31.5 8.211,31.411 8.211,26.711 12.911,26.711 13,26.711 13,23.289 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.ArrowDown()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<polygon points="13.8,15.6 19,10.9 19,14.7 12,21 5,14.8 5,10.9 10.2,15.6 10.2,3 13.8,3 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.ArrowLeft()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<polygon points="8.4,13.8 13.1,19 9.3,19 3,12 9.2,5 13.1,5 8.4,10.2 21,10.2 21,13.8 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.ArrowRight()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<polygon points="15.6,10.2 10.9,5 14.7,5 21,12 14.8,19 10.9,19 15.6,13.8 3,13.8 3,10.2 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.ArrowUp()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<polygon points="10.2,8.4 5,13.1 5,9.3 12,3 19,9.3 19,13.1 13.8,8.4 13.8,21 10.2,21 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Attachment()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M12,22c-3.196,0-6-2.57-6-5.5v-10C6,4.019,8.019,2,10.5,2S15,4.019,15,6.5V15c0,1.654-1.346,3-3,3s-3-1.346-3-3V8h2v7
	c0,0.552,0.449,1,1,1s1-0.448,1-1V6.5C13,5.122,11.878,4,10.5,4S8,5.122,8,6.5v10c0,1.971,2.15,3.5,4,3.5s4-1.529,4-3.5V8h2v8.5
	C18,19.43,15.196,22,12,22z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.AvatarDefault()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect fill="#A0A1A2" width="50" height="50"/>
<path fill="#E5E5E5" d="M33,18.2c0,4.5-3.7,8.2-8.2,8.2c-4.5,0-8.2-3.7-8.2-8.2c0-4.5,3.7-8.2,8.2-8.2C29.3,10,33,13.7,33,18.2"/>
<polygon fill="#E5E5E5" points="30.8,29.2 24.8,37.7 18.8,29.2 10.1,29.2 10.1,50 39.4,50 39.4,29.2 "/>
<path fill="#FFFFFF" d="M16.5,18.2c0,4.5,3.6,8.1,8,8.2l2.1-16.2C26,10.1,25.4,10,24.8,10C20.2,10,16.5,13.7,16.5,18.2"/>
<polygon fill="#FFFFFF" points="18.8,29.2 10.1,29.2 10.1,50 21.6,50 23.4,35.8 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.AvatarUnknown()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">
<rect fill="#A0A1A2" width="30" height="30"/>
<g>
	<path fill="#FFFFFF" d="M9.143,11.378c0-0.804,0.258-1.619,0.774-2.444s1.27-1.509,2.26-2.05s2.146-0.813,3.466-0.813
		c1.228,0,2.311,0.227,3.25,0.679s1.665,1.068,2.178,1.847c0.512,0.779,0.768,1.625,0.768,2.539c0,0.719-0.146,1.35-0.438,1.892
		c-0.293,0.542-0.64,1.009-1.041,1.403c-0.402,0.394-1.124,1.056-2.165,1.987c-0.288,0.262-0.519,0.493-0.692,0.691
		c-0.173,0.199-0.302,0.381-0.387,0.547c-0.085,0.164-0.15,0.33-0.197,0.494c-0.046,0.166-0.116,0.455-0.209,0.87
		c-0.161,0.88-0.664,1.32-1.511,1.32c-0.44,0-0.811-0.144-1.11-0.432c-0.301-0.288-0.451-0.716-0.451-1.282
		c0-0.711,0.11-1.326,0.33-1.848c0.22-0.52,0.512-0.977,0.876-1.371s0.854-0.861,1.473-1.403c0.542-0.474,0.933-0.832,1.175-1.073
		c0.24-0.241,0.443-0.51,0.609-0.806c0.164-0.296,0.247-0.618,0.247-0.965c0-0.677-0.252-1.249-0.755-1.714
		c-0.504-0.465-1.153-0.698-1.949-0.698c-0.931,0-1.616,0.235-2.057,0.705s-0.813,1.162-1.117,2.076
		c-0.288,0.957-0.834,1.435-1.638,1.435c-0.474,0-0.874-0.167-1.2-0.501C9.306,12.129,9.143,11.767,9.143,11.378z M15.338,25.292
		c-0.517,0-0.967-0.167-1.352-0.501c-0.386-0.335-0.578-0.803-0.578-1.403c0-0.533,0.187-0.981,0.559-1.346s0.829-0.546,1.371-0.546
		c0.533,0,0.981,0.182,1.346,0.546s0.546,0.813,0.546,1.346c0,0.593-0.19,1.058-0.571,1.396S15.837,25.292,15.338,25.292z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.AzureQuickstart()</h6><svg class="msportalfx-svg-placeholder" viewBox="-0.5 -0.5 50 50">
<path class="msportalfx-svg-c01" d="M49.5,32.663c0-3.224-2.589-5.837-5.787-5.837c-0.241,0-0.476,0.019-0.707,0.049 c0.355-1.311,0.55-2.69,0.55-4.115c0-5.003-2.35-9.442-5.986-12.289l-5.652,7.279h6.682L24.459,32.718l4.662-10.773H23.06 l5.471-14.729c-0.136-0.004-0.269-0.021-0.406-0.021c-6.808,0-12.581,4.447-14.63,10.617c-1.082-0.377-2.238-0.592-3.447-0.592 C4.221,17.22-0.5,21.984-0.5,27.86c0,5.877,4.721,10.64,10.548,10.64c0.005,0,0.01-0.001,0.014-0.001V38.5h34.124l-0.007-0.026 C47.157,38.236,49.5,35.728,49.5,32.663z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Backlog()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M21.07,13.5H8.45c-0.51,0-0.93-0.56-0.93-1.07c0-0.51,0.42-0.93,0.93-0.93h12.62c0.52,0,0.93,0.42,0.93,0.93
	C22,12.94,21.59,13.5,21.07,13.5z"/>
<path d="M21.07,7.5H8.45c-0.51,0-0.93-0.56-0.93-1.07S7.94,5.5,8.45,5.5h12.62C21.59,5.5,22,5.92,22,6.43S21.59,7.5,21.07,7.5z"/>
<path d="M21.08,19.5H8.45c-0.51,0-0.93-0.56-0.93-1.07c0-0.52,0.42-0.93,0.93-0.93h12.63c0.51,0,0.92,0.41,0.92,0.93
	C22,18.94,21.59,19.5,21.08,19.5z"/>
<polygon points="2,5.899 2,5.899 2.699,5.199 2.699,5.199 2.699,5.199 4.1,6.599 6.901,3.798 6.901,3.798 6.901,3.798 7.6,4.499 
	7.6,4.499 7.6,4.499 4.1,8 "/>
<polygon points="2,11.899 2,11.899 2.699,11.199 2.699,11.199 2.699,11.199 4.1,12.599 6.901,9.798 6.901,9.798 6.901,9.798 
	7.6,10.499 7.6,10.499 7.6,10.499 4.1,14 "/>
<polygon points="2,17.899 2,17.899 2.699,17.199 2.699,17.199 2.699,17.199 4.1,18.599 6.901,15.798 6.901,15.798 6.901,15.798 
	7.6,16.499 7.6,16.499 7.6,16.499 4.1,20 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Book()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M36.194,30.337c2.303,0,4.003,0.407,5.126,0.801c0.562,0.197,0.969,0.393,1.236,0.534l0.256,0.148v-3.206
	c-1.418-0.555-3.619-1.156-6.619-1.156c-2.991,0-5.15,0.597-6.545,1.154v3.214C29.831,31.713,31.966,30.337,36.194,30.337z"/>
<path fill="#59B4D9" d="M36.194,24.101c2.303,0,4.003,0.421,5.126,0.815c0.562,0.197,0.969,0.379,1.236,0.52l0.256,0.148v-3.206
	c-1.418-0.555-3.619-1.156-6.619-1.156c-2.991,0-5.15,0.602-6.545,1.159v3.223C29.747,25.548,31.882,24.115,36.194,24.101z"/>
<path fill="#59B4D9" d="M29.649,19.382c0.098-0.07,2.233-1.503,6.545-1.503c2.303,0,4.003,0.407,5.126,0.801
	c0.562,0.197,0.969,0.393,1.236,0.52l0.256,0.162v-3.205C41.394,15.601,39.194,15,36.194,15c-2.991,0-5.15,0.597-6.545,1.154v3.214
	V19.382z"/>
<path fill="#59B4D9" d="M13.806,30.337c-2.303,0-4.003,0.407-5.126,0.801c-0.562,0.197-0.969,0.393-1.236,0.534L7.188,31.82v-3.206
	c1.418-0.555,3.619-1.156,6.619-1.156c2.991,0,5.15,0.597,6.545,1.154v3.214C20.169,31.713,18.034,30.337,13.806,30.337z"/>
<path fill="#59B4D9" d="M13.806,24.101c-2.303,0-4.003,0.421-5.126,0.815c-0.562,0.197-0.969,0.379-1.236,0.52l-0.256,0.148v-3.206
	c1.418-0.555,3.619-1.156,6.619-1.156c2.991,0,5.15,0.602,6.545,1.159v3.223C20.253,25.548,18.118,24.115,13.806,24.101z"/>
<path fill="#59B4D9" d="M20.351,19.382c-0.098-0.07-2.233-1.503-6.545-1.503c-2.303,0-4.003,0.407-5.126,0.801
	c-0.562,0.197-0.969,0.393-1.236,0.52l-0.256,0.162v-3.205C8.606,15.601,10.806,15,13.806,15c2.991,0,5.15,0.597,6.545,1.154v3.214
	V19.382z"/>
<path fill="#59B4D9" d="M49.761,13.834c-0.07-0.098-1.32-1.98-3.483-3.905c-2.163-1.924-5.506-3.918-9.902-3.918
	c-4.41-0.014-7.711,2.008-9.832,3.932c-0.598,0.546-1.113,1.088-1.545,1.589c-0.431-0.502-0.947-1.043-1.545-1.589
	c-2.121-1.924-5.421-3.947-9.832-3.932c-4.396,0-7.739,1.994-9.902,3.918s-3.413,3.806-3.483,3.905L0,14.747V43.09l3.399,0.899
	h0.169v-0.014l0.014-0.028l0.112-0.169c0.112-0.155,0.267-0.393,0.492-0.674c0.435-0.576,1.095-1.362,1.952-2.121
	c1.756-1.545,4.228-3.006,7.486-3.006c3.16,0,5.534,1.362,7.219,2.837c0.71,0.626,0.979,1.286,1.067,1.844H25h3.09
	c0.089-0.558,0.357-1.218,1.067-1.844c1.685-1.475,4.059-2.837,7.219-2.837c3.258,0,5.73,1.461,7.486,3.006
	c0.857,0.758,1.517,1.545,1.952,2.121c0.225,0.281,0.379,0.52,0.492,0.674l0.112,0.169l0.014,0.028v0.014h0.169L50,43.09V14.747
	L49.761,13.834z M23.75,38.118c-2.121-1.868-5.421-3.778-9.691-3.764c-4.354,0-8.146,1.952-10.309,3.848V15.267l0.379-0.52
	c0.435-0.562,1.152-1.348,2.008-2.107c1.756-1.545,4.228-3.006,7.486-3.006c3.16,0,6.011,1.362,7.697,2.837
	c1.236,1.081,2.065,2.219,2.43,2.781V38.118z M46.25,38.202c-2.163-1.896-5.955-3.848-10.309-3.848
	c-4.27-0.014-7.57,1.896-9.691,3.764V15.253c0.365-0.562,1.194-1.699,2.43-2.781c1.685-1.475,4.536-2.837,7.697-2.837
	c3.258,0,5.73,1.461,7.486,3.006c0.857,0.758,1.573,1.545,2.008,2.107l0.379,0.52C46.25,15.268,46.25,38.202,46.25,38.202z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Calendar()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 18 18" enable-background="new 0 0 18 18" xml:space="preserve">
<g>
	<path fill="#A0A1A2" d="M17.1,0H0.9C0.4,0,0,0.4,0,0.9v16.1C0,17.6,0.4,18,0.9,18h16.1c0.5,0,0.9-0.4,0.9-0.9V0.9
		C18,0.4,17.6,0,17.1,0z M17,17H1V4h16V17z"/>
	<rect x="5" y="5" fill="#A0A1A2" width="2" height="2"/>
	<rect x="8" y="5" fill="#A0A1A2" width="2" height="2"/>
	<rect x="2" y="8" fill="#A0A1A2" width="2" height="2"/>
	<rect x="5" y="8" fill="#A0A1A2" width="2" height="2"/>
	<rect x="8" y="8" fill="#A0A1A2" width="2" height="2"/>
	<rect x="11" y="8" fill="#A0A1A2" width="2" height="2"/>
	<rect x="2" y="11" fill="#A0A1A2" width="2" height="2"/>
	<rect x="5" y="11" fill="#A0A1A2" width="2" height="2"/>
	<rect x="2" y="14" fill="#A0A1A2" width="2" height="2"/>
	<rect x="5" y="14" fill="#A0A1A2" width="2" height="2"/>
	<rect x="8" y="14" fill="#A0A1A2" width="2" height="2"/>
	<rect x="11" y="14" fill="#A0A1A2" width="2" height="2"/>
	<rect x="8" y="11" fill="#A0A1A2" width="2" height="2"/>
	<rect x="11" y="5" fill="#A0A1A2" width="2" height="2"/>
	<rect x="11" y="11" fill="#A0A1A2" width="2" height="2"/>
	<rect x="14" y="8" fill="#A0A1A2" width="2" height="2"/>
	<rect x="14" y="5" fill="#A0A1A2" width="2" height="2"/>
	<rect x="14" y="11" fill="#A0A1A2" width="2" height="2"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Canceled()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<path d="M8,0C3.582,0,0,3.582,0,8s3.582,8,8,8s8-3.582,8-8S12.418,0,8,0z M8,13.5c-3.038,0-5.5-2.462-5.5-5.5
	S4.962,2.5,8,2.5s5.5,2.462,5.5,5.5S11.038,13.5,8,13.5z"/>
<rect x="7" y="0.988" transform="matrix(0.7071 -0.7071 0.7071 0.7071 -3.3137 8)" width="2" height="14.024"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Check()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<path fill="#7FBA00" d="M0.632,8.853L0.101,8.278C-0.037,8.126-0.037,7.885,0.123,7.74l1.534-1.418
	c0.073-0.066,0.16-0.101,0.255-0.101c0.108,0,0.204,0.044,0.276,0.123l4.218,4.523l7.258-9.296c0.073-0.094,0.182-0.145,0.298-0.145
	c0.088,0,0.167,0.029,0.233,0.081l1.659,1.28c0.081,0.059,0.13,0.145,0.145,0.248c0.007,0.101-0.015,0.204-0.081,0.276L6.595,15.246
	L0.632,8.853z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Clock()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<path d="M8,0C3.582,0,0,3.582,0,8s3.582,8,8,8s8-3.582,8-8S12.418,0,8,0z M8,13.5c-3.038,0-5.5-2.462-5.5-5.5
	S4.962,2.5,8,2.5s5.5,2.462,5.5,5.5S11.038,13.5,8,13.5z"/>
<rect x="7" y="3" width="1" height="5"/>
<rect x="7" y="8" width="5" height="1"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Clone()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M12,11v8H6v-8H12 M15,8H3v14h12V8L15,8z"/>
<polyline points="18,5 18,13 16,13 16,16 21,16 21,2 21,2 9,2 9,7 12,7 12,5 18,5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Code()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 0.5 24 24" enable-background="new 0.5 0.5 24 24" xml:space="preserve">
<g>
	<polygon points="15.5,4.5 12,19.5 9.5,19.5 13,4.5 	"/>
</g>
<polygon points="8.5,5.553 2.5,10.981 2.5,13.118 8.5,18.545 8.5,15.109 5.118,12.049 8.5,8.99 "/>
<polygon points="16.5,5.553 16.5,8.99 19.882,12.049 16.5,15.109 16.5,18.545 22.5,13.118 22.5,10.981 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Columns()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<rect x="2" y="5" width="9" height="3"/>
<rect x="13" y="5" width="9" height="3"/>
<rect x="2" y="10" width="9" height="3"/>
<rect x="13" y="10" width="9" height="3"/>
<rect x="2" y="15" width="9" height="3"/>
<rect x="13" y="15" width="9" height="3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Commit()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" d="M5,13v9h15v-9H5z M18,20H7v-5h11V20z"/>
<polygon points="11.121,6.251 7,9.936 7,6.934 12.509,2 18,6.911 18,9.912 13.905,6.251 13.905,12 11.121,12 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Commits()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#59B4D9" points="24.069,9.256 17.167,15.804 17.167,12.251 25.496,3.5 33.833,12.22 33.833,15.762 26.968,9.256 
	26.968,20.854 24.069,20.854 "/>
<path fill="#59B4D9" d="M48.648,25.5H2.353c-1.02,0-1.853,0.832-1.853,1.853V31.5v13v4.147c0,1.021,0.836,1.853,1.848,1.853h46.295
	c1.02,0,1.853-0.832,1.853-1.853V44.5v-13v-4.147C50.496,26.332,49.66,25.5,48.648,25.5z M46.728,46.51h-42.5V29.5h42.5V46.51z"/>
<rect x="21.146" y="34.176" fill="#59B4D9" width="8.323" height="8.324"/>
<rect x="33.564" y="34.176" fill="#59B4D9" width="8.32" height="8.324"/>
<rect x="8.729" y="34.176" fill="#59B4D9" width="8.324" height="8.324"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Connect()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" d="M12.44,16.5h1.06v-4h-5v-4H6.16c-0.91,0-1.47,0.83-1.61,2H0.5v4h4.07
	c0.16,1.13,0.71,2,1.59,2H12.44z"/>
<path fill-rule="evenodd" clip-rule="evenodd" d="M9.5,8.5v3h5v5h2c0.17,0,0.34-0.03,0.49-0.09c0.65-0.24,1.13-0.98,1.28-1.91h4.03
	v-4h-4.04c-0.19-1.14-0.87-2-1.76-2H9.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Delete()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 0.5 24 24" enable-background="new 0.5 0.5 24 24" xml:space="preserve">
<path d="M19.846,6.5H5.154C4.601,6.5,4.5,6.27,4.5,6s0.101-1.5,0.654-1.5h14.692C20.399,4.5,20.5,5.73,20.5,6S20.399,6.5,19.846,6.5
	z"/>
<path d="M14.33,3.5h-0.011h-3.723c-0.402,0-0.597-0.13-0.597-0.5c0.011-0.36,0.149-0.5,0.597-0.5h3.686
	c0.402,0,0.717,0.14,0.717,0.5c0,0.37-0.256,0.5-0.646,0.5H14.33z"/>
<path d="M5.5,7.5v14c0.04,0.82,3.537,1,4.337,1h5.862c0.8,0,3.761-0.18,3.801-1v-14H5.5z M9.542,19.167
	c0.01,0.36-0.27,0.68-0.62,0.69h-0.73c-0.35,0-0.63-0.28-0.65-0.65v-9.37c-0.01-0.37,0.542-0.726,1.023-0.726
	c0.34,0,0.967,0.316,0.977,0.676V19.167z M13.5,19.156c-0.01,0.36-0.3,0.64-0.64,0.64h-0.74c-0.35-0.01-0.63-0.33-0.62-0.7v-9.37
	c0.02-0.37,0.642-0.655,1.002-0.635S13.51,9.416,13.5,9.786V19.156z M17.396,19.151c-0.01,0.36-0.3,0.64-0.64,0.64h-0.74
	c-0.35-0.01-0.63-0.33-0.62-0.7v-9.37c0.02-0.37,0.642-0.654,1.002-0.635c0.36,0.02,1.008,0.325,0.998,0.695V19.151z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Disable()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 24.5 24 24" enable-background="new 0.5 24.5 24 24" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" d="M18.446,40.87L8.133,30.557c1.252-0.926,2.768-1.442,4.367-1.442
	c1.978,0,3.831,0.768,5.22,2.168c1.4,1.389,2.168,3.252,2.168,5.22C19.888,38.102,19.382,39.607,18.446,40.87z M7.28,41.722
	c-2.526-2.515-2.841-6.409-0.958-9.271L16.552,42.68c-1.189,0.779-2.589,1.21-4.052,1.21C10.522,43.89,8.669,43.122,7.28,41.722z
	 M19.572,29.431c-1.884-1.884-4.399-2.926-7.072-2.926s-5.188,1.042-7.072,2.926c-3.904,3.904-3.904,10.239,0,14.144
	C7.312,45.458,9.827,46.5,12.5,46.5s5.188-1.042,7.072-2.926C23.476,39.67,23.476,33.335,19.572,29.431z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Disabled()</h6><svg class="msportalfx-svg-placeholder msportalfx-svg-palette-error" viewBox="0 0 30 30">
  <circle cx="15" cy="15" r="14"/>
  <rect fill="#FFFFFF" x="7.222" y="11.889" width="15.556" height="6.222"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Discard()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<polygon points="15,3.475 12.539,1.001 8.006,5.534 3.474,1.001 1,3.475 5.52,8.007 1,12.527 3.474,15.001 
	7.994,10.481 12.526,15.014 15,12.54 10.467,8.007 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Disconnect()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" d="M12.1,16.5h4.4c0.17,0,0.34-0.03,0.49-0.09c0.65-0.24,1.13-0.98,1.28-1.91h4.03v-4
	h-4.04c-0.19-1.14-1.47-1.99-2.36-1.99l0,0L12.1,16.5z M13.82,8.51h-1.23L8.72,16.5h1.3L13.82,8.51z M10.69,8.51L7.44,8.5H6.16
	c-0.91,0-1.47,0.83-1.61,2H0.5v4h4.06c0.17,1.13,0.72,2,1.6,2H6.9L10.69,8.51z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Download()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 0.5 24 24" enable-background="new 0.5 0.5 24 24" xml:space="preserve">
<polygon points="14.255,12.09 19.5,7.4 19.5,11.22 12.489,17.5 5.5,11.25 5.5,7.43 10.712,12.09 10.712,2.5 14.255,2.5 "/>
<rect x="5.5" y="19.5" width="14" height="3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Edit()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 24.5 24 24" enable-background="new 0.5 24.5 24 24" xml:space="preserve">
<g>
	<path fill-rule="evenodd" clip-rule="evenodd" d="M8.177,43.896c0.35-0.09,0.42-0.37,0.17-0.62L5.116,40.05
		c-0.26-0.26-0.54-0.18-0.62,0.17l-1.979,5.175c-0.08,0.35,0.13,0.57,0.48,0.48L8.177,43.896z M20.743,27.676
		c-2.388-2.388-3.45-0.4-3.45-0.4c-0.28,0.22-0.72,0.6-0.97,0.85s-0.24,0.66,0.01,0.92l3.04,3.04c0.25,0.25,0.67,0.26,0.92,0.01
		s0.63-0.69,0.85-0.97C21.143,31.126,23.104,30.038,20.743,27.676z M10.456,42.063c-0.26,0.26-0.67,0.26-0.92,0l-3.207-3.167
		c-0.25-0.25-0.25-0.67,0-0.92l8.064-7.92c0.25-0.25,0.67-0.25,0.92,0l3.04,3.04c0.26,0.26,0.26,0.68,0,0.93L10.456,42.063z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Ellipsis()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-12.5 37.5 24 24" enable-background="new -12.5 37.5 24 24" xml:space="preserve">
<circle fill="#3E3E3E" cx="6.544" cy="49.456" r="1.956"/>
<circle fill="#3E3E3E" cx="-1" cy="49.456" r="1.956"/>
<circle fill="#3E3E3E" cx="-8.544" cy="49.456" r="1.956"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Error()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<g>
	<g>
		<g>
		</g>
		<g>
		</g>
		<g>
			<g>
				<path fill-rule="evenodd" clip-rule="evenodd" d="M8.688,5.969L8.661,5.963L8.688,5.969z"/>
			</g>
		</g>
	</g>
	<g>
		<g>
			<path d="M18.569,15.078H5.431c-0.19,0-0.343-0.148-0.343-0.331V8.581C5.088,4.9,8.189,1.906,12,1.906
				c3.812,0,6.912,2.995,6.912,6.676v6.165C18.912,14.929,18.759,15.078,18.569,15.078z M5.774,14.415h12.453V8.581
				c0-3.315-2.793-6.013-6.226-6.013S5.774,5.266,5.774,8.581V14.415z"/>
		</g>
	</g>
	<g>
		<g>
			<g>
				<path d="M24,18.273c0,0-1.145-2.618-6.007-4.05H6.007C1.145,15.654,0,18.273,0,18.273l0.048,0.04h23.904L24,18.273z"/>
			</g>
		</g>
		<g>
			<path d="M22.773,18.969H1.227C1.666,19.632,5.362,22.144,12,22.144S22.334,19.632,22.773,18.969z"/>
		</g>
	</g>
	<g>
		<g>
			<g>
				<path d="M10.951,14.554c-0.13-0.027-3.23-0.616-3.909-3.162c-0.027-0.106,0.014-0.225,0.117-0.272
					c0.103-0.066,0.226-0.053,0.309,0.027c0.021,0.013,1.611,1.492,2.928,1.505c0.11,0,0.199,0.06,0.24,0.152l0.603,1.412
					c0.041,0.08,0.021,0.172-0.041,0.252C11.143,14.534,11.047,14.574,10.951,14.554z"/>
			</g>
		</g>
		<g>
			<g>
				<path d="M12.631,14.554c0.137-0.027,3.237-0.616,3.915-3.162c0.027-0.106-0.021-0.225-0.123-0.272
					c-0.096-0.066-0.226-0.053-0.309,0.027c-0.014,0.013-1.611,1.492-2.928,1.505c-0.103,0-0.199,0.06-0.24,0.152l-0.597,1.412
					c-0.041,0.08-0.027,0.172,0.034,0.252C12.439,14.534,12.542,14.574,12.631,14.554z"/>
			</g>
		</g>
		<g>
			<g>
				<path fill-rule="evenodd" clip-rule="evenodd" d="M11.904,11.014C10.656,10.988,9.593,9.781,9.6,9.582
					c0.007-0.192,4.505-0.099,4.505,0.099C14.098,9.874,13.145,11.034,11.904,11.014z M10.8,7.129
					c0.267,0.007,0.473,0.378,0.466,0.822c-0.014,0.464-0.233,0.822-0.501,0.815c-0.267-0.007-0.466-0.378-0.459-0.835
					C10.313,7.481,10.539,7.123,10.8,7.129z M13.193,7.249c0.261,0,0.466,0.378,0.459,0.829c-0.014,0.451-0.233,0.815-0.501,0.809
					c-0.267-0.007-0.466-0.371-0.453-0.829C12.706,7.607,12.926,7.242,13.193,7.249z M11.931,4.551
					C9.415,4.498,7.447,6.997,8.674,9.556c1.262,2.632,2.29,3.613,2.997,6.317c0.823-2.671,1.899-3.606,3.278-6.185
					C16.293,7.189,14.448,4.604,11.931,4.551z"/>
			</g>
		</g>
	</g>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Fallback()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 20 20">
  <path d="M8.69 5.97L8.66 5.96 8.69 5.97z"/>
  <path d="M18.57 15.08H5.43c-0.19 0-0.34-0.15-0.34-0.33V8.58C5.09 4.9 8.19 1.91 12 1.91c3.81 0 6.91 3 6.91 6.68v6.17C18.91 14.93 18.76 15.08 18.57 15.08zM5.77 14.42h12.45V8.58c0-3.31-2.79-6.01-6.23-6.01S5.77 5.27 5.77 8.58V14.42z"/>
  <path d="M24 18.27c0 0-1.14-2.62-6.01-4.05H6.01C1.15 15.65 0 18.27 0 18.27l0.05 0.04h23.9L24 18.27z"/>
  <path d="M22.77 18.97H1.23C1.67 19.63 5.36 22.14 12 22.14S22.33 19.63 22.77 18.97z"/>
  <path d="M10.95 14.55c-0.13-0.03-3.23-0.62-3.91-3.16 -0.03-0.11 0.01-0.22 0.12-0.27 0.1-0.07 0.23-0.05 0.31 0.03 0.02 0.01 1.61 1.49 2.93 1.51 0.11 0 0.2 0.06 0.24 0.15l0.6 1.41c0.04 0.08 0.02 0.17-0.04 0.25C11.14 14.53 11.05 14.57 10.95 14.55z"/>
  <path d="M12.63 14.55c0.14-0.03 3.24-0.62 3.92-3.16 0.03-0.11-0.02-0.22-0.12-0.27 -0.1-0.07-0.23-0.05-0.31 0.03 -0.01 0.01-1.61 1.49-2.93 1.51 -0.1 0-0.2 0.06-0.24 0.15l-0.6 1.41c-0.04 0.08-0.03 0.17 0.03 0.25C12.44 14.53 12.54 14.57 12.63 14.55z"/>
  <path d="M11.9 11.01C10.66 10.99 9.59 9.78 9.6 9.58c0.01-0.19 4.51-0.1 4.51 0.1C14.1 9.87 13.15 11.03 11.9 11.01zM10.8 7.13c0.27 0.01 0.47 0.38 0.47 0.82 -0.01 0.46-0.23 0.82-0.5 0.82 -0.27-0.01-0.47-0.38-0.46-0.83C10.31 7.48 10.54 7.12 10.8 7.13zM13.19 7.25c0.26 0 0.47 0.38 0.46 0.83 -0.01 0.45-0.23 0.82-0.5 0.81 -0.27-0.01-0.47-0.37-0.45-0.83C12.71 7.61 12.93 7.24 13.19 7.25zM11.93 4.55C9.41 4.5 7.45 7 8.67 9.56c1.26 2.63 2.29 3.61 3 6.32 0.82-2.67 1.9-3.61 3.28-6.18C16.29 7.19 14.45 4.6 11.93 4.55z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Favorite()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 0.5 24 24" enable-background="new 0.5 0.5 24 24" xml:space="preserve">
<path fill="#FCD116" d="M5.5,2.5v20l7-5l7,5v-20H5.5z M16.208,15.706l-3.708-2.58l-3.708,2.58l1.308-4.324L6.5,8.653l4.517-0.092
	L12.5,4.294l1.483,4.267L18.5,8.653l-3.6,2.729L16.208,15.706z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Feedback()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 15 15">
<path class="msportalfx-svg-c01" d="M13.903,2.152c-0.267-0.291-0.585-0.505-0.936-0.651c-1.62-0.674-3.959,0.165-5.425,2.246 c-2.253-2.719-4.948-3.23-6.448-1.595c-2.997,3.264,0.756,7.552,3.717,9.987c1.167,0.96,2.211,1.632,2.67,1.84v0.021 c0.004-0.001,0.013-0.007,0.016-0.008c0,0,0.016,0.007,0.022,0.008v-0.021C9.139,13.244,18.08,6.702,13.903,2.152z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.File()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="13px" height="13px" viewBox="0 0 13 13" enable-background="new 0 0 13 13" xml:space="preserve">
<rect x="2" y="1" fill="#FFFFFF" width="9" height="11"/>
<path fill="#59B4D9" d="M11,1v11H2V1H11 M12,0H1v13h11V0L12,0z"/>
<rect x="3" y="3" opacity="0.5" fill="#59B4D9" width="7" height="1"/>
<rect x="3" y="5" opacity="0.5" fill="#59B4D9" width="7" height="1"/>
<rect x="3" y="7" opacity="0.5" fill="#59B4D9" width="7" height="1"/>
<rect x="3" y="9" opacity="0.5" fill="#59B4D9" width="7" height="1"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Filter()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M22,5.477c0,0-2.462-1.4-10-1.4s-10,1.4-10,1.4l7.969,8.031l0.338,7c0,0,0.754,0.492,1.692,0.492
	c0.954,0,1.692-0.492,1.692-0.492l0.385-7L22,5.477z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Folder()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 12 12" enable-background="new 0 0 12 12" xml:space="preserve">
<path fill="#A0A1A2" d="M11.3,3H1v9h10.4c0.3,0,0.6-0.3,0.6-0.6V3H11.3z"/>
<path fill="#7A7A7A" d="M1,3v8.6C1,11.9,0.9,12,0.6,12C0.3,12,0,12.1,0,11.8V1h3.8l1.4,1H12v1H1z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.FolderAlt()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M22,8.1V5.4C22,4.6,21.6,4,20.8,4H14c-0.8,0-1,0-1.7,1.4L11,8.1L22,8.1z"/>
<path d="M20.7,7H3.2C2.4,7,2,7.6,2,8.4v11.2C2,20.4,2.4,21,3.2,21h17.5c0.8,0,1.2-0.6,1.2-1.4V8.4C21.9,7.6,21.5,7,20.7,7z M20,19H4
	V9h16V19z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.FolderAlternate()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M22,8.1V5.4C22,4.6,21.6,4,20.8,4H14c-0.8,0-1,0-1.7,1.4L11,8.1L22,8.1z"/>
<path d="M20.7,7H3.2C2.4,7,2,7.6,2,8.4v11.2C2,20.4,2.4,21,3.2,21h17.5c0.8,0,1.2-0.6,1.2-1.4V8.4C21.9,7.6,21.5,7,20.7,7z M20,19H4
	V9h16V19z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.ForPlacementOnly()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<circle fill="#EC008C" cx="25" cy="25" r="25"/>
<path fill="#FFFFFF" d="M10.055,19.625v4.507h5.294v2.179h-5.294v6.379h-2.53V17.446h8.25v2.179H10.055z"/>
<path fill="#FFFFFF" d="M28.669,22.177c0,0.688-0.128,1.338-0.383,1.951c-0.255,0.613-0.631,1.146-1.127,1.6
	c-0.496,0.453-1.105,0.813-1.828,1.079c-0.723,0.266-1.549,0.398-2.477,0.398h-1.807v5.485h-2.53V17.446h4.709
	c0.914,0,1.711,0.11,2.392,0.33c0.68,0.22,1.247,0.535,1.7,0.946c0.454,0.411,0.792,0.909,1.016,1.493
	C28.558,20.8,28.669,21.454,28.669,22.177z M26.011,22.262c0-0.411-0.065-0.781-0.196-1.111c-0.131-0.33-0.331-0.611-0.601-0.845
	c-0.27-0.233-0.604-0.413-1.004-0.537s-0.874-0.186-1.419-0.186h-1.743v5.485h1.626c1.049,0,1.867-0.237,2.456-0.712
	C25.717,23.881,26.011,23.183,26.011,22.262z"/>
<path fill="#FFFFFF" d="M44.796,24.919c0,1.226-0.176,2.333-0.526,3.322c-0.351,0.989-0.851,1.832-1.499,2.53
	s-1.426,1.237-2.333,1.616s-1.921,0.568-3.04,0.568c-1.085,0-2.071-0.188-2.961-0.563c-0.89-0.375-1.649-0.899-2.28-1.573
	c-0.631-0.673-1.12-1.482-1.467-2.429c-0.348-0.946-0.521-1.986-0.521-3.12c0-1.169,0.165-2.247,0.495-3.232
	c0.329-0.985,0.813-1.837,1.45-2.557c0.639-0.719,1.421-1.281,2.35-1.685c0.929-0.404,1.991-0.605,3.189-0.605
	c1.056,0,2.023,0.184,2.902,0.553c0.878,0.368,1.632,0.889,2.259,1.563s1.114,1.487,1.462,2.439
	C44.622,22.7,44.796,23.757,44.796,24.919z M42.117,25.153c0-0.942-0.108-1.77-0.324-2.482c-0.217-0.712-0.524-1.308-0.925-1.786
	s-0.881-0.84-1.44-1.084c-0.561-0.244-1.188-0.367-1.882-0.367c-0.723,0-1.375,0.138-1.956,0.415s-1.073,0.665-1.478,1.164
	c-0.404,0.5-0.716,1.093-0.936,1.781s-0.329,1.446-0.329,2.275c0,0.886,0.116,1.678,0.351,2.376c0.233,0.698,0.556,1.29,0.967,1.775
	s0.896,0.855,1.457,1.11c0.56,0.256,1.165,0.383,1.817,0.383c0.694,0,1.327-0.118,1.897-0.355s1.063-0.589,1.478-1.053
	c0.415-0.464,0.735-1.044,0.962-1.738C42.004,26.872,42.117,26.067,42.117,25.153z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Gear()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M50,27.726v-5.69l-0.803-0.261l-6.091-1.989l-1.625-3.925l3.124-6.609l-4.024-4.023L39.826,5.61
	l-5.711,2.903L30.19,6.885L27.724,0h-5.688l-0.263,0.807l-1.989,6.092l-3.922,1.623L9.25,5.4L5.227,9.422l0.383,0.753l2.9,5.714
	L6.887,19.81L0,22.276v5.691l0.804,0.264l6.092,1.987l1.625,3.923l-3.123,6.611l4.021,4.025l0.755-0.384l5.712-2.901l3.924,1.625
	L22.275,50h5.691l0.262-0.803l1.989-6.089l3.923-1.626l6.61,3.126l4.025-4.026l-0.383-0.754l-2.902-5.711l1.627-3.924L50,27.726z
	 M25.943,36.31c-6.132-0.105-11.148-5.216-11.148-11.417c0-3.351,1.491-6.362,3.817-8.371L16,14h8v8l-2.872-2.993
	c-1.744,1.407-2.989,3.561-2.989,5.994c0,4.343,3.507,7.908,7.792,7.978c4.309,0.07,7.781-3.368,7.781-7.699
	c0-2.753-1.417-5.203-3.554-6.632l2.369-2.391c2.741,2.09,4.332,5.445,4.332,9.138C36.859,31.585,32.098,36.414,25.943,36.31z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.GearAlternate()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M50,27.7V22l-0.8-0.3l-6.1-2l-1.6-3.9l3.1-6.6l-4-4l-0.8,0.4l-5.7,2.9l-3.9-1.6L27.7,0H22l-0.3,0.8l-2,6.1
	l-3.9,1.6L9.3,5.4l-4,4l0.4,0.8l2.9,5.7l-1.6,3.9L0,22.3V28l0.8,0.3l6.1,2l1.6,3.9l-3.1,6.6l4,4l0.8-0.4l5.7-2.9l3.9,1.6l2.5,6.9H28
	l0.3-0.8l2-6.1l3.9-1.6l6.6,3.1l4-4l-0.4-0.8l-2.9-5.7l1.6-3.9L50,27.7z M25,33.5c-4.7,0-8.5-3.8-8.5-8.5s3.8-8.5,8.5-8.5
	s8.5,3.8,8.5,8.5S29.7,33.5,25,33.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.GetMoreLicense()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<polygon fill="#59B4D9" points="29.946,22.868 20.19,12.584 25.484,12.584 38.523,24.994 25.53,37.416 20.252,37.416 29.946,27.187 
	0,27.187 0,22.868 "/>
<path fill="#59B4D9" d="M48.147,0H46L30.269,0.002L15,0h-3.143C10.836,0,10,0.837,10,1.857V17h5V5h30v40H15V33h-5v15.152
	C10,49.164,10.84,50,11.861,50h36.286C49.168,50,50,49.164,50,48.152V1.857C50,0.837,49.168,0,48.147,0z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.GetStarted()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<g>
	<polygon fill="#59B4D9" points="33.097,19.905 18.435,5.389 30.371,5.389 50,23.986 30.452,42.486 18.516,42.486 33.097,27.97 
		0,27.97 0,19.905 	"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Go()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path d="M25.004,0C38.809,0,50,11.197,50,25c0,13.808-11.191,25-24.996,25C11.194,50,0,38.808,0,25
	C0,11.197,11.194,0,25.004,0 M25.004,3.179C12.974,3.179,3.181,12.967,3.181,25c0,12.033,9.792,21.825,21.823,21.825
	c12.026,0,21.817-9.792,21.817-21.825C46.821,12.967,37.03,3.179,25.004,3.179"/>
<polygon points="29.576,22.574 21.893,15.329 28.14,15.329 38.415,25.021 28.187,34.677 21.938,34.677 
	29.576,27.475 12.259,27.475 12.259,22.574 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Guide()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M49.859,22.381c-0.728-6.937-4.271-12.883-9.273-16.928c-0.971-0.786-2.018-1.472-3.1-2.1L35.523,7.13
	c0.83,0.493,1.636,1.024,2.385,1.632c4.196,3.395,7.116,8.312,7.723,14.063c0.602,5.706-1.165,11.093-4.504,15.226
	c-3.349,4.131-8.25,6.98-13.955,7.581c-3.82,0.4-7.489-0.277-10.744-1.761l-1.961,3.774c3.976,1.851,8.479,2.708,13.151,2.214
	c6.862-0.719,12.796-4.164,16.814-9.133C48.453,35.763,50.587,29.239,49.859,22.381"/>
<path fill="#59B4D9" d="M11.951,41.123c-4.133-3.346-6.978-8.247-7.581-13.949C3.771,21.466,5.532,16.08,8.878,11.946
	c3.343-4.13,8.246-6.977,13.951-7.578c4.613-0.484,9.016,0.574,12.694,2.76l1.963-3.776c-4.416-2.564-9.661-3.787-15.105-3.214
	C15.522,0.861,9.588,4.305,5.569,9.27L5.562,9.279c-4.018,4.965-6.144,11.484-5.423,18.338c0.723,6.859,4.164,12.796,9.135,16.815
	c1.582,1.282,3.335,2.346,5.194,3.212l1.961-3.774C14.826,43.14,13.315,42.227,11.951,41.123"/>
<path fill="#59B4D9" d="M27.834,12.422h-1.433l-2.596-3.959c-0.154-0.231-0.258-0.406-0.318-0.523h-0.016
	c0.022,0.234,0.035,0.574,0.035,1.019v3.463h-1.341V6.12h1.529l2.5,3.837l0.318,0.514h0.016c-0.022-0.153-0.035-0.442-0.035-0.865
	V6.12h1.341C27.834,6.12,27.834,12.422,27.834,12.422z"/>
<polygon opacity="0.8" fill="#59B4D9" points="20.322,27.103 25,41.406 25,27.103 "/>
<polygon fill="#59B4D9" points="20.322,27.104 25,27.104 25,27.103 25,12.803 "/>
<polygon opacity="0.8" fill="#59B4D9" points="25,12.803 25,27.103 29.678,27.103 "/>
<polygon opacity="0.6" fill="#59B4D9" points="25,27.103 25,41.406 29.678,27.103 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.HeartPulse()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M45.4,7.3c-4.8-5.2-14.6-3-20.4,5.1C17.8,3.8,9.2,2.1,4.3,7.3c-5,5.4-3.7,11.7-0.7,16.9l5.1-0.1l4.9-0.2
	l3.7-9.8c0.2-0.6,0.6-1.7,2.1-1.7c1.6,0,1.7,1.2,1.7,1.8l0.1,16.3l2.6-5.5c0.2-0.5,0.7-0.8,1.2-0.8h15.6c0.7,0,1.3,0.7,1.3,1.4
	c0,0.7-0.6,1.7-1.3,1.7H26.6l-5,10c-0.3,1-0.8,1.3-1.9,1.2l0,0c-0.6-0.1-1.1-1-1.1-1.6l-0.2-15l-1.5,4.1c-0.1,0.5-0.6,1.3-1.1,1.4
	l-4.6,0l-6.2,0c6.1,8.9,16.6,16.4,19.6,17.7v0.1v0v0v-0.1C30.6,42.9,58.9,21.9,45.4,7.3z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.History()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<path d="M5.848,17.718c1.587,2.111,4.112,3.477,6.956,3.477c4.802,0,8.696-3.893,8.696-8.696
	s-3.893-8.696-8.696-8.696S4.109,7.698,4.109,12.5c0,0.294,0.015,0.584,0.043,0.87l2.737,0c-0.041-0.284-0.063-0.574-0.063-0.87
	c0-3.302,2.676-5.978,5.978-5.978s5.978,2.676,5.978,5.978s-2.676,5.978-5.978,5.978c-2.005,0-3.779-0.986-4.863-2.5L5.848,17.718z"
	/>
<rect x="11.717" y="7.065" width="1.087" height="5.435"/>
<rect x="11.717" y="12.5" width="5.435" height="1.087"/>
<polygon fill="#7A7A7A" points="9.326,12.5 5.413,16.413 1.5,12.5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Hyperlink()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<g>
	<polygon fill-rule="evenodd" clip-rule="evenodd" points="14.607,2.04 17.727,4.61 8.557,13.31 11.377,16.12 19.987,6.96 
		22.507,9.48 22.507,2.04 	"/>
	<path fill-rule="evenodd" clip-rule="evenodd" d="M19.507,19.04h-15v-14h7v-3h-7.92c-1.66,0-2.09,1.77-2.08,1.75
		c-0.02-0.74,0.01,11.45,0.02,16.17c0,1.09,2.05,2.08,2,2.08c0.68-0.05,12.88-0.02,17.36-0.01c1,0.01,1.6-1.46,1.62-1.4
		c-0.02,0.93,0-6.59,0-6.59h-3V19.04z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Inactive()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="-0.5 0.5 16 16" enable-background="new -0.5 0.5 16 16" xml:space="preserve">
<circle fill-rule="evenodd" clip-rule="evenodd" fill="#7A7A7A" cx="7.5" cy="8.5" r="8"/>
<rect x="3.5" y="7.5" fill-rule="evenodd" clip-rule="evenodd" fill="#FFFFFF" width="8" height="2"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Info()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M25,2.753C12.713,2.753,2.753,12.713,2.753,25S12.713,47.246,25,47.246S47.247,37.287,47.247,25
	C47.247,12.714,37.286,2.753,25,2.753z M23.457,10.666c0.652-0.659,1.441-0.989,2.369-0.989c0.94,0,1.737,0.33,2.39,0.989
	c0.651,0.659,0.976,1.451,0.976,2.378s-0.329,1.717-0.987,2.369c-0.659,0.651-1.453,0.977-2.379,0.977
	c-0.928,0-1.717-0.326-2.369-0.977c-0.652-0.652-0.978-1.442-0.978-2.369S22.805,11.325,23.457,10.666z M31.322,39.831H20.35v-0.782
	c0.898-0.029,1.564-0.29,1.999-0.782c0.289-0.333,0.434-1.217,0.434-2.651V23.754c0-1.433-0.166-2.349-0.498-2.748
	c-0.334-0.398-0.979-0.627-1.935-0.684v-0.804h8.517v16.098c0,1.434,0.166,2.35,0.499,2.749c0.333,0.398,0.985,0.626,1.956,0.684
	V39.831z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.InstallVisualStudio()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" fill="#59B4D9" d="M13.796,14.204l7.511-5.841v11.682L13.796,14.204z M2.841,18.466
	V9.943l4.261,4.261L2.841,18.466z M21.307,0L10.034,11.273L2.841,5.682L0,7.102v14.205l2.841,1.42l7.193-5.591l11.273,11.273
	l7.102-2.841V2.841L21.307,0z"/>
<path fill="#59B4D9" d="M45.694,25.261l-3.977-1.068l-1.057-3.977l4.057-4.057c-2.398-0.648-5.068-0.034-6.943,1.852
	c-1.898,1.886-2.5,4.568-1.841,6.977L24.989,35.932c-2.409-0.659-5.091-0.045-6.989,1.841c-1.875,1.875-2.489,4.546-1.841,6.943
	l4.057-4.057l3.977,1.057l1.057,3.977l-4.057,4.057c2.398,0.648,5.068,0.034,6.943-1.852c1.977-1.966,2.557-4.807,1.739-7.296
	l10.727-10.727c2.489,0.818,5.33,0.239,7.307-1.739c1.875-1.875,2.489-4.546,1.841-6.943L45.694,25.261z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Key()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M18,15c1.6,0,3-1.3,3-3V5c0-1.7-1.3-3-3-3H11C9.4,2,8,3.3,8,5v6.2l-6,6V21h3.3l0,0h1.9v-1.9h1.9v-1.9H11v-1.9l0.3-0.3H18z
	 M18.7,4.3c0.6,0.6,0.6,1.7,0,2.3s-1.7,0.6-2.3,0c-0.6-0.6-0.6-1.7,0-2.3C17,3.7,18,3.7,18.7,4.3z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.LaunchCurrent()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 15 15">
<path class="msportalfx-svg-c01" d="M8.883,0.942v1.586h3.118L6.314,8.214c-0.31,0.31-0.31,0.812,0,1.121C6.469,9.49,6.672,9.568,6.875,9.568 S7.281,9.49,7.435,9.336l5.545-5.545v2.833h1.586V0.942H8.883z"/>
<path class="msportalfx-svg-c01" d="M12.01,6.064c-0.117-0.049-0.253-0.022-0.344,0.068L10.72,7.078c-0.059,0.059-0.092,0.14-0.092,0.223 v3.746c0,0.601-0.489,1.09-1.09,1.09H3.272c-0.601,0-1.09-0.489-1.09-1.09V6.07c0-0.601,0.489-1.09,1.09-1.09h4.762 c0.084,0,0.164-0.033,0.223-0.092l0.946-0.946c0.09-0.09,0.117-0.226,0.068-0.344C9.223,3.48,9.108,3.403,8.98,3.403H3.272 c-1.471,0-2.667,1.196-2.667,2.667v4.977c0,1.47,1.196,2.667,2.667,2.667h6.266c1.471,0,2.667-1.196,2.667-2.667V6.355 C12.204,6.228,12.127,6.113,12.01,6.064z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Link()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<g>
	<g>
		<path d="M21.89,9.381l-3.05,2.91c-1.13,1.07-2.39,1.12-3.82,0.72l5.34-5.09c0.79-0.75,0.38-2.31-0.42-3.07
			c-0.79-0.76-2.35-1.09-3.15-0.33l-5.33,5.09c-0.42-1.36-0.36-2.57,0.76-3.63l3.05-2.91c1.63-1.56,4.69-1.35,6.32,0.21
			c1.63,1.56,1.98,4.57,0.34,6.13L21.89,9.381z"/>
		<path d="M8.16,19.561l5.34-5.09c0.41,1.36,0.36,2.57-0.77,3.63l-3.04,2.91c-1.64,1.56-4.64,1.16-6.28-0.4
			c-1.63-1.56-1.98-4.35-0.34-5.91l3.05-2.91c1.12-1.07,2.39-1.12,3.81-0.73l-5.34,5.09c-0.79,0.76-0.45,2.25,0.35,3.01
			c0.79,0.76,2.31,1.2,3.11,0.44L8.16,19.561z"/>
		<path d="M7.87,14.911l7.62-7.27c0.42-0.4,1.229-1.371,2.1-0.5c0.635,0.635,0.127,1.263-0.57,1.96l-7.63,7.27
			c-0.697,0.697-1.658,1.522-2.3,0.88C6.388,16.549,7.047,15.734,7.87,14.911z"/>
	</g>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Lock()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<g>
	<path d="M8.5,10.13c0-0.97,0.36-2.26,0.92-2.87S11.19,6.3,12,6.29c0.81,0.01,2.02,0.36,2.58,0.97
		S15.5,8.73,15.5,9.7v2.8h-7V10.13z M18.49,12.5h0.01V9.7c0-1.68-0.62-3.23-1.66-4.35C15.82,4.22,13.62,3.5,12,3.5
		S8.18,4.22,7.16,5.35C6.12,6.47,5.5,8.02,5.5,9.7v2.8h0.01c-1.479,0-2.01,0.875-2.01,2v6c0,1.422,0.609,2,1.74,2h13.52
		c1.443,0,1.74-0.906,1.74-2v-6C20.5,13.484,20.094,12.5,18.49,12.5z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Log()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" d="M4.5,2.5v2h-2v1h2v2h-2v1h2v2h-2v1h2v2h-2v1h2v2h-2v1h2v2h-2v1h2v2h15v-20H4.5z
	 M17.5,20.5h-11v-16h11V20.5z"/>
<rect x="8.5" y="7.5" fill-rule="evenodd" clip-rule="evenodd" width="7" height="2"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Mail()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<g>
	<path d="M11.9,14.2c-0.2,0-0.4-0.1-0.6-0.2L2.5,6.7l1.2-1.3l8.2,6.7l8.5-6.9l1.2,1.3L12.4,14C12.4,14.1,12.2,14.2,11.9,14.2z"/>
</g>
<path d="M21,4H3C2.4,4,2,4.5,2,5.1v13.8C2,19.5,2.4,20,3,20l18,0c0.6,0,1-0.5,1-1.1V5.1C22,4.5,21.6,4,21,4z M20,18H4V6h16V18z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Message()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<polygon points="5.8,15.7 12.7,15.2 13.8,21.8 "/>
<path d="M20.5,17H3.5C2.7,17,2,16.3,2,15.5V4.5C2,3.7,2.7,3,3.5,3h17.1C21.3,3,22,3.7,22,4.5v11.1C22,16.3,21.3,17,20.5,17z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Monitoring()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path opacity="0.4" fill="#59B4D9" d="M16.456,19.473l-5.048-4.086c-2.158,3.088-3.137,6.688-2.991,10.232l6.454-0.679
	C14.871,23.05,15.392,21.152,16.456,19.473"/>
<path opacity="0.8" fill="#59B4D9" d="M30.575,16.279l4.087-5.049c-3.09-2.156-6.693-3.13-10.238-2.982l0.679,6.453
	C26.994,14.699,28.894,15.217,30.575,16.279"/>
<path opacity="0.6" fill="#59B4D9" d="M22.853,14.956l-0.68-6.46c-3.49,0.623-6.795,2.35-9.348,5.123l5.044,4.084
	C19.274,16.294,21.012,15.371,22.853,14.956"/>
<path opacity="0.25" fill="#59B4D9" d="M15.121,27.189l-6.46,0.682c0.62,3.492,2.345,6.801,5.121,9.357l4.083-5.044
	C16.455,30.775,15.534,29.034,15.121,27.189"/>
<path fill="#59B4D9" d="M36.43,12.647l-4.084,5.045c1.008,1.002,1.759,2.178,2.264,3.437h6.775
	C40.643,17.958,38.977,14.985,36.43,12.647z"/>
<path fill="#59B4D9" d="M44.261,24.978c0-0.003,0.002-0.005,0.002-0.008c-0.008-1.17-0.96-2.113-2.133-2.113l-0.004-0.005
	l-0.621,0.005H34.82h-7.601c-0.009-0.008-0.012,0.007-0.019,0c-0.57-0.565-1.314-0.813-2.057-0.813
	c-0.752,0-1.506,0.288-2.079,0.866c-1.136,1.145-1.129,2.995,0.018,4.131c0.57,0.565,1.312,0.845,2.052,0.845
	c0.688,0,1.373-0.298,1.923-0.779h15.085c1.172-0.004,2.114-0.952,2.118-2.124h0.002C44.262,24.981,44.261,24.98,44.261,24.978z"/>
<path fill="#59B4D9" d="M25,4.2c11.469,0,20.8,9.331,20.8,20.8S36.469,45.8,25,45.8S4.2,36.469,4.2,25S13.531,4.2,25,4.2 M25,0
	C11.193,0,0,11.193,0,25s11.193,25,25,25s25-11.193,25-25S38.807,0,25,0L25,0z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Paused()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="-0.5 0.5 16 16" enable-background="new -0.5 0.5 16 16" xml:space="preserve">
<rect x="2.5" y="2.5" fill="#7A7A7A" width="4" height="12"/>
<rect x="8.5" y="2.5" fill="#7A7A7A" width="4" height="12"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Pending()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<path fill="#0072C6" d="M16,8.175c0-3.916-3.027-7.152-6.831-7.497v2.246c2.582,0.315,4.619,2.526,4.619,5.213
	c0,1.669-0.794,3.137-2.022,4.063L9.135,9.726V16h6.673l-2.406-2.262C14.983,12.41,16,10.426,16,8.175z"/>
<path fill="#0072C6" d="M2.212,7.864c0-1.669,0.794-3.137,2.023-4.063l2.631,2.474V0H0.192l2.406,2.262C1.017,3.59,0,5.574,0,7.825
	c0,3.928,3.046,7.171,6.865,7.499v-2.244C4.267,12.782,2.212,10.563,2.212,7.864z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Person()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M35.859,10.269c0,5.671-4.638,10.269-10.359,10.269c-5.721,0-10.358-4.598-10.358-10.269
	C15.142,4.598,19.779,0,25.5,0C31.221,0,35.859,4.598,35.859,10.269"/>
<polygon fill="#59B4D9" points="33.074,24.058 25.5,34.578 17.926,24.058 7.001,24.058 7.001,50 44,50 44,24.058 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.PersonWithFriend()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M44.977,10.5c0,5.638-4.571,10.209-10.21,10.209S24.558,16.138,24.558,10.5s4.57-10.209,10.209-10.209
	C40.406,0.291,44.977,4.862,44.977,10.5"/>
<polygon fill="#59B4D9" points="42.232,24.209 34.767,34.668 27.302,24.209 19.534,24.209 19.534,50 50,50 50,24.209 "/>
<path fill="#59B4D9" d="M14.294,27.636c0,3.168-2.568,5.734-5.736,5.734s-5.736-2.566-5.736-5.734s2.568-5.735,5.736-5.735
	S14.294,24.468,14.294,27.636"/>
<polygon fill="#59B4D9" points="12.751,35.512 8.558,41.388 4.364,35.512 0,35.512 0,50 17.115,50 17.115,35.512 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Pin()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#FFFFFF" d="M50,17.8L32.2,0c-0.8,2.6-0.9,5.2-0.5,7.7L18.6,20.7c-4-1.6-8.8-2.1-13-0.7l9.4,9.1L0,50l21-15l9,8.8
	c1.3-4.2,0.9-8.6-0.7-12.4l13-13C44.8,18.7,47.4,18.6,50,17.8z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Postpone()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0.5 0.5 24 24" enable-background="new 0.5 0.5 24 24" xml:space="preserve">
<path d="M12.5,2.5c-5.523,0-10,4.477-10,10s4.477,10,10,10s10-4.477,10-10S18.023,2.5,12.5,2.5z M12.5,19.375
	c-3.797,0-6.875-3.078-6.875-6.875S8.703,5.625,12.5,5.625s6.875,3.078,6.875,6.875S16.297,19.375,12.5,19.375z"/>
<rect x="11.25" y="6.25" width="1.25" height="6.25"/>
<rect x="11.25" y="12.5" width="6.25" height="1.25"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.PowerUp()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path opacity="0.5" fill="#59B4D9" d="M41.386,50c-6.829,0-16.33-5.585-26.069-15.323C1.768,21.125-3.448,8.134,2.338,2.348
	C3.875,0.811,6.039,0,8.599,0c6.829,0,16.331,5.585,26.069,15.325c6.637,6.638,11.379,13.177,13.715,18.916
	c2.365,5.811,2.104,10.572-0.736,13.413C46.111,49.189,43.946,50,41.386,50 M8.599,3.078c-1.75,0-3.124,0.486-4.083,1.445
	C0.791,8.249,4.052,19.056,17.495,32.498c9.031,9.032,17.962,14.424,23.892,14.424c1.75,0,3.123-0.486,4.082-1.446
	c1.908-1.908,1.932-5.486,0.063-10.074C43.35,30.04,38.84,23.85,32.49,17.502C23.459,8.471,14.528,3.078,8.599,3.078"/>
<path fill="#59B4D9" d="M8.599,50c-2.56,0-4.724-0.811-6.261-2.346c-2.84-2.841-3.101-7.602-0.735-13.413
	c2.336-5.739,7.078-12.278,13.715-18.916C25.056,5.585,34.558,0,41.387,0c2.56,0,4.724,0.811,6.26,2.347
	c5.786,5.787,0.57,18.778-12.979,32.33C24.929,44.415,15.427,50,8.599,50 M41.387,3.078c-5.93,0-14.861,5.393-23.893,14.424
	C11.146,23.85,6.634,30.04,4.453,35.402c-1.869,4.588-1.845,8.166,0.063,10.074c0.959,0.96,2.332,1.446,4.083,1.446
	c5.93,0,14.86-5.392,23.892-14.424C45.933,19.056,49.194,8.249,45.468,4.522C44.509,3.564,43.137,3.078,41.387,3.078"/>
<path fill="#59B4D9" d="M31.914,20.031c-0.291-0.318-0.639-0.551-1.022-0.71c-1.768-0.736-4.322,0.18-5.922,2.451
	c-2.46-2.968-5.402-3.526-7.039-1.741c-3.272,3.563,0.826,8.244,4.058,10.902c1.274,1.048,2.413,1.781,2.914,2.008v0.023
	c0.004-0.001,0.014-0.008,0.017-0.009c0,0,0.018,0.008,0.024,0.009v-0.023C26.713,32.139,36.473,24.998,31.914,20.031"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.PreviewRight()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="40px" width="40px" viewBox="0 0 40 40" enable-background="new 0 0 40 40" xml:space="preserve">
<polygon fill="#B8D432" points="40,20.4 40,17.5 22.5,0 19.6,0 1.8,0 40,38.1 "/>
<path fill="#7FBA00" d="M15.1,4.6l-1.3,1.3l-0.6-0.6l3.6-3.6l1.1,1.1c0.4,0.4,0.6,0.8,0.7,1.2c0,0.4-0.1,0.8-0.5,1.2
	c-0.3,0.3-0.8,0.5-1.2,0.5c-0.5,0-0.9-0.2-1.3-0.6L15.1,4.6z M16.9,2.8l-1.3,1.3L16,4.5c0.3,0.3,0.5,0.4,0.8,0.4
	c0.3,0,0.5-0.1,0.7-0.3c0.4-0.4,0.4-0.9-0.1-1.4L16.9,2.8z"/>
<path fill="#7FBA00" d="M19.1,11.2l-0.7-0.7l0.4-1.5c0-0.1,0.1-0.3,0.1-0.4c0-0.1,0-0.2,0-0.3c0-0.1,0-0.2-0.1-0.3
	c0-0.1-0.1-0.2-0.2-0.2l-0.2-0.2L16.9,9l-0.6-0.6l3.6-3.6L21.1,6c0.2,0.2,0.3,0.3,0.4,0.5c0.1,0.2,0.2,0.4,0.2,0.6
	c0,0.2,0,0.4-0.1,0.6c-0.1,0.2-0.2,0.4-0.3,0.5c-0.1,0.1-0.3,0.2-0.4,0.3c-0.1,0.1-0.3,0.1-0.4,0.1c-0.2,0-0.3,0-0.5,0
	c-0.2,0-0.3-0.1-0.5-0.2l0,0c0,0.1,0,0.2,0,0.3c0,0.1,0,0.2,0,0.3c0,0.1,0,0.2,0,0.3c0,0.1,0,0.2-0.1,0.3L19.1,11.2z M20,5.9
	l-1.2,1.2l0.5,0.5c0.1,0.1,0.2,0.2,0.3,0.2c0.1,0,0.2,0.1,0.3,0.1c0.1,0,0.2,0,0.3-0.1c0.1,0,0.2-0.1,0.3-0.2
	c0.2-0.2,0.3-0.4,0.2-0.6c0-0.2-0.1-0.4-0.3-0.6L20,5.9z"/>
<path fill="#7FBA00" d="M21.6,13.7l-2-2l3.6-3.6l1.9,1.9l-0.5,0.5l-1.3-1.3l-1,1l1.2,1.2L23,12l-1.2-1.2l-1.1,1.1l1.4,1.4L21.6,13.7
	z"/>
<path fill="#7FBA00" d="M28.8,13.7L23.9,16l-0.7-0.7l2.3-4.9l0.7,0.7l-1.8,3.6c-0.1,0.1-0.1,0.2-0.2,0.4l0,0
	c0.1-0.1,0.2-0.2,0.4-0.2l3.6-1.8L28.8,13.7z"/>
<path fill="#7FBA00" d="M26.4,18.5l-0.6-0.6l3.6-3.6l0.6,0.6L26.4,18.5z"/>
<path fill="#7FBA00" d="M29.4,21.5l-2-2l3.6-3.6l1.9,1.9l-0.5,0.5L31.1,17l-1,1l1.2,1.2l-0.5,0.5l-1.2-1.2l-1.1,1.1l1.4,1.4
	L29.4,21.5z"/>
<path fill="#7FBA00" d="M38.3,23.2l-4.6,2.6L33,25.1l1.8-3.2c0.1-0.1,0.2-0.3,0.3-0.4l0,0c-0.1,0.1-0.3,0.2-0.4,0.3l-3.2,1.8
	l-0.7-0.7l2.6-4.6l0.7,0.7l-2,3.3c-0.1,0.1-0.2,0.3-0.3,0.4l0,0c0.1-0.1,0.2-0.2,0.4-0.3l3.4-1.9l0.6,0.6l-2,3.3
	c-0.1,0.1-0.2,0.2-0.3,0.4l0,0c0.1-0.1,0.2-0.2,0.4-0.3l3.3-2L38.3,23.2z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M15.1,4.6l-1.3,1.3l-0.6-0.6l3.6-3.6l1.1,1.1
	c0.4,0.4,0.6,0.8,0.7,1.2c0,0.4-0.1,0.8-0.5,1.2c-0.3,0.3-0.8,0.5-1.2,0.5c-0.5,0-0.9-0.2-1.3-0.6L15.1,4.6z M16.9,2.8l-1.3,1.3
	L16,4.5c0.3,0.3,0.5,0.4,0.8,0.4c0.3,0,0.5-0.1,0.7-0.3c0.4-0.4,0.4-0.9-0.1-1.4L16.9,2.8z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M19.1,11.2l-0.7-0.7l0.4-1.5c0-0.1,0.1-0.3,0.1-0.4
	c0-0.1,0-0.2,0-0.3c0-0.1,0-0.2-0.1-0.3c0-0.1-0.1-0.2-0.2-0.2l-0.2-0.2L16.9,9l-0.6-0.6l3.6-3.6L21.1,6c0.2,0.2,0.3,0.3,0.4,0.5
	c0.1,0.2,0.2,0.4,0.2,0.6c0,0.2,0,0.4-0.1,0.6c-0.1,0.2-0.2,0.4-0.3,0.5c-0.1,0.1-0.3,0.2-0.4,0.3c-0.1,0.1-0.3,0.1-0.4,0.1
	c-0.2,0-0.3,0-0.5,0c-0.2,0-0.3-0.1-0.5-0.2l0,0c0,0.1,0,0.2,0,0.3c0,0.1,0,0.2,0,0.3c0,0.1,0,0.2,0,0.3c0,0.1,0,0.2-0.1,0.3
	L19.1,11.2z M20,5.9l-1.2,1.2l0.5,0.5c0.1,0.1,0.2,0.2,0.3,0.2c0.1,0,0.2,0.1,0.3,0.1c0.1,0,0.2,0,0.3-0.1c0.1,0,0.2-0.1,0.3-0.2
	c0.2-0.2,0.3-0.4,0.2-0.6c0-0.2-0.1-0.4-0.3-0.6L20,5.9z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M21.6,13.7l-2-2l3.6-3.6l1.9,1.9l-0.5,0.5l-1.3-1.3l-1,1
	l1.2,1.2L23,12l-1.2-1.2l-1.1,1.1l1.4,1.4L21.6,13.7z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M28.8,13.7L23.9,16l-0.7-0.7l2.3-4.9l0.7,0.7l-1.8,3.6
	c-0.1,0.1-0.1,0.2-0.2,0.4l0,0c0.1-0.1,0.2-0.2,0.4-0.2l3.6-1.8L28.8,13.7z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M26.4,18.5l-0.6-0.6l3.6-3.6l0.6,0.6L26.4,18.5z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M29.4,21.5l-2-2l3.6-3.6l1.9,1.9l-0.5,0.5L31.1,17l-1,1l1.2,1.2
	l-0.5,0.5l-1.2-1.2l-1.1,1.1l1.4,1.4L29.4,21.5z"/>
<path opacity="0.35" fill="#1E1E1E" enable-background="new    " d="M38.3,23.2l-4.6,2.6L33,25.1l1.8-3.2c0.1-0.1,0.2-0.3,0.3-0.4
	l0,0c-0.1,0.1-0.3,0.2-0.4,0.3l-3.2,1.8l-0.7-0.7l2.6-4.6l0.7,0.7l-2,3.3c-0.1,0.1-0.2,0.3-0.3,0.4l0,0c0.1-0.1,0.2-0.2,0.4-0.3
	l3.4-1.9l0.6,0.6l-2,3.3c-0.1,0.1-0.2,0.2-0.3,0.4l0,0c0.1-0.1,0.2-0.2,0.4-0.3l3.3-2L38.3,23.2z"/>
<polygon opacity="0.6" fill="#1E1E1E" enable-background="new    " points="40,38.1 1.8,0 0,0 40,40 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Properties()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M21.31,13.5H8.69c-0.51,0-0.93-0.56-0.93-1.07c0-0.51,0.42-0.93,0.93-0.93h12.62c0.52,0,0.93,0.42,0.93,0.93
	C22.24,12.94,21.83,13.5,21.31,13.5z"/>
<path d="M21.31,7.5H8.69c-0.51,0-0.93-0.56-0.93-1.07S8.18,5.5,8.69,5.5h12.62c0.52,0,0.93,0.42,0.93,0.93S21.83,7.5,21.31,7.5z"/>
<path d="M21.32,19.5H8.69c-0.51,0-0.93-0.56-0.93-1.07c0-0.52,0.42-0.93,0.93-0.93h12.63c0.51,0,0.92,0.41,0.92,0.93
	C22.24,18.94,21.83,19.5,21.32,19.5z"/>
<circle cx="4.5" cy="6.5" r="1.5"/>
<circle cx="4.5" cy="12.5" r="1.5"/>
<circle cx="4.5" cy="18.5" r="1.5"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Publish()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M45.636,25.068c0-0.326-0.007-0.65-0.021-0.973h-4.288c0.017,0.322,0.029,0.646,0.029,0.973
	c0,0.458-0.021,0.912-0.054,1.362c-0.283,3.821-1.744,7.319-4.023,10.131c-1.325-0.697-2.826-1.296-4.46-1.78
	c0.739-2.513,1.203-5.342,1.311-8.351c0.016-0.451,0.027-0.904,0.027-1.362c0-0.326-0.005-0.65-0.013-0.973
	c-0.077-3.011-0.507-5.85-1.217-8.381c1.682-0.504,3.22-1.132,4.57-1.863c1.364-0.739,2.534-1.582,3.471-2.505
	c-0.477-0.621-0.987-1.215-1.524-1.783C35.328,5.219,29.511,2.5,23.068,2.5S10.808,5.219,6.692,9.563
	c-0.538,0.568-1.047,1.162-1.524,1.783C2.244,15.152,0.5,19.909,0.5,25.068c0,5.261,1.814,10.103,4.843,13.945
	c0.483,0.612,0.995,1.199,1.537,1.758c4.104,4.23,9.842,6.866,16.187,6.866S35.151,45,39.255,40.77
	c0.542-0.559,1.055-1.145,1.537-1.758c2.766-3.508,4.514-7.85,4.798-12.583C45.618,25.979,45.636,25.526,45.636,25.068z
	 M10.507,38.341c1.094-0.53,2.29-0.983,3.563-1.355c0.838,2.143,1.893,3.98,3.11,5.389C14.665,41.517,12.396,40.13,10.507,38.341z
	 M4.834,26.43h7.172c0.108,3.01,0.571,5.838,1.311,8.351c-1.633,0.483-3.134,1.082-4.46,1.78C6.578,33.749,5.117,30.251,4.834,26.43
	z M8.639,13.852c1.35,0.731,2.888,1.359,4.57,1.863c-0.71,2.531-1.14,5.37-1.217,8.381H4.809
	C5.012,20.247,6.411,16.712,8.639,13.852z M17.182,7.758c-1.284,1.485-2.39,3.444-3.25,5.74c-1.326-0.396-2.567-0.879-3.694-1.447
	C12.18,10.137,14.548,8.657,17.182,7.758z M21.706,14.728c-1.91-0.072-3.76-0.293-5.5-0.656c0.32-0.824,0.676-1.607,1.072-2.335
	c1.278-2.354,2.839-3.923,4.428-4.506V14.728z M21.706,24.095h-7.38c0.074-2.77,0.466-5.417,1.14-7.801
	c1.95,0.425,4.048,0.69,6.24,0.769V24.095z M21.706,33.461c-2.153,0.077-4.214,0.335-6.133,0.747
	c-0.705-2.367-1.127-5.008-1.231-7.778h7.364V33.461z M21.706,42.905c-1.589-0.583-3.15-2.151-4.428-4.506
	c-0.338-0.622-0.648-1.283-0.93-1.976c1.698-0.346,3.5-0.556,5.358-0.627V42.905z M24.041,35.782
	c1.997,0.053,3.931,0.271,5.747,0.641c-0.283,0.693-0.593,1.354-0.93,1.976c-1.382,2.546-3.095,4.174-4.817,4.628V35.782z
	 M28.858,11.737c0.395,0.728,0.752,1.511,1.072,2.335c-1.858,0.388-3.84,0.616-5.889,0.671V7.109
	C25.762,7.563,27.475,9.191,28.858,11.737z M32.203,13.498c-0.859-2.296-1.965-4.254-3.25-5.74c2.634,0.899,5.002,2.379,6.944,4.293
	C34.77,12.619,33.529,13.102,32.203,13.498z M31.81,24.095h-7.769v-7.017c2.334-0.06,4.564-0.334,6.629-0.784
	C31.343,18.678,31.736,21.326,31.81,24.095z M31.794,26.43c-0.104,2.771-0.526,5.412-1.231,7.778
	c-2.035-0.437-4.228-0.703-6.522-0.761V26.43H31.794z M35.629,38.341c-1.891,1.79-4.162,3.181-6.677,4.039
	c1.219-1.41,2.275-3.249,3.114-5.395C33.34,37.357,34.536,37.81,35.629,38.341z"/>
<polygon fill="#59B4D9" points="50.5,26.43 43.642,14.552 36.785,26.43 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Query()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M15.8,22c0.8,0,1.2-0.6,1.2-1.4V9.4C17,8.6,16.6,8,15.8,8H3.2C2.4,8,2,8.6,2,9.4v11.2C2,21.4,2.4,22,3.2,22H15.8z M3.9,20.1
	V12h11.2v8.1H3.9z"/>
<path d="M20.8,16c0.8,0,1.2-0.6,1.2-1.4V3.4C22,2.6,21.6,2,20.8,2H8.3C7.5,2,7.1,2.6,7.1,3.4v11.2c0,0.8,0.4,1.4,1.2,1.4H20.8z
	 M9,14.1V6h11.1v8.1H9z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Question()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 30 30">
  <path d="M14.622,1c4.843,0,9.61,2.232,9.61,7.568c0,4.919-5.638,6.811-6.848,8.589 c-0.908,1.324-0.605,3.179-3.103,3.179c-1.627,0-2.422-1.324-2.422-2.535c0-4.503,6.622-5.524,6.622-9.232 c0-2.043-1.362-3.254-3.632-3.254c-4.843,0-2.951,4.995-6.622,4.995c-1.325,0-2.46-0.795-2.46-2.308C5.768,4.292,10.006,1,14.622,1 z M14.432,22.794c1.703,0,3.103,1.4,3.103,3.103c0,1.703-1.4,3.103-3.103,3.103s-3.103-1.4-3.103-3.103 C11.329,24.194,12.729,22.794,14.432,22.794z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Queued()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<rect y="2.5" width="4" height="12"/>
<rect x="6" y="2.5" width="4" height="12"/>
<rect x="12" y="2.5" width="4" height="12"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Quickstart()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="-0.5 0.5 50 50" enable-background="new -0.5 0.5 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M42.63,22.172c0.249-0.976,0.386-1.985,0.386-3.006c0-4.03-1.973-7.582-5.021-9.731l-4.781,8.054
	c-0.196,0.303-0.045,0.555,0.32,0.555l6.134-0.039c0.353-0.012,0.442,0.208,0.205,0.472L24.041,36.535
	c-0.234,0.27-0.329,0.226-0.187-0.104l4.997-12.202c0.116-0.323-0.05-0.591-0.41-0.591h-4.864c-0.347,0-0.537-0.252-0.401-0.599
	l5.825-15.541c-3.935,0.742-7.17,3.448-8.677,7.054c-1.525-1.804-3.81-2.959-6.359-2.959c-4.597,0-8.342,3.76-8.342,8.404
	c0,0.813,0.116,1.57,0.32,2.297C2.189,23.689-0.5,27.33-0.5,31.594c0,5.463,4.398,9.906,9.831,9.906h30.334
	c5.448,0,9.834-4.436,9.834-9.906C49.512,27.17,46.624,23.431,42.63,22.172z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Redo()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<path d="M10.147,3.491c1.708,0,3.364,0.609,4.66,1.713l2.72,2.252V2.5L20.5,5.653v3.875v1.442v1.442h-6.754l-3.159-2.974h4.956
	l-2.602-2.035c-0.777-0.663-1.772-1.028-2.795-1.028c-2.383,0-4.322,1.941-4.322,4.326c0,1.265,0.672,2.462,1.65,3.301l7.078,6.34
	l-1.91,2.157l-7.061-6.323c-1.601-1.372-2.638-3.369-2.638-5.475C2.943,6.726,6.175,3.491,10.147,3.491z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Refresh()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<g>
	<g>
		<polygon points="3.25,2 11.94,2 11.94,10.17 		"/>
		<path d="M12.6,22C7.32,21.91,3,17.53,3,12.19c0-3.12,1.49-5.86,3.78-7.58l2.08,2.08c-1.8,1.18-2.98,3.22-2.98,5.55
			c0,3.74,3.02,6.81,6.71,6.87c3.71,0.06,6.7-2.9,6.7-6.63c0-2.37-1.22-4.48-3.06-5.71l2.04-2.06c2.36,1.8,3.73,4.7,3.73,7.88
			C22,17.92,17.9,22.09,12.6,22z"/>
	</g>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Release()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="4.5 20.5 24 24" enable-background="new 4.5 20.5 24 24" xml:space="preserve">
<path d="M23.5,27.5v13h-15v-13h-2v15h19v-15H23.5z"/>
<polygon points="14.621,26.751 10.5,30.436 10.5,27.434 16.009,22.5 21.5,27.411 21.5,30.412 17.405,26.751 17.405,38.5 
	14.621,38.5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Request()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="-0.5 0.5 16 16" enable-background="new -0.5 0.5 16 16" xml:space="preserve">
<path d="M3.5,9.5h-2v-7h7v2h2v-4h-11v11h4V9.5z"/>
<path d="M4.5,5.5v11h11v-11H4.5z M6.5,7.5h2v2h-2V7.5z M13.5,14.5h-7v-3h2h1h1v-4h3V14.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Retain()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="4.5 20.5 24 24" enable-background="new 4.5 20.5 24 24" xml:space="preserve">
<path d="M23.5,27.5v13h-15v-13h-2v15h19v-15H23.5z"/>
<polygon points="17.379,34.249 21.5,30.564 21.5,33.566 15.991,38.5 10.5,33.589 10.5,30.588 14.595,34.249 14.595,22.5 
	17.379,22.5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Save()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="16px" width="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<g>
	<path d="M14.4,0H1.6C0.7,0,0,0.7,0,1.6v12.8C0,15.3,0.7,16,1.6,16L3,16v-5c0-0.6,0.5-1.1,1.1-1.1l7.9,0c0.6,0,1.1,0.5,1.1,1.1v5
		h1.3c0.9,0,1.6-0.7,1.6-1.6V1.6C16,0.7,15.3,0,14.4,0z M13.1,6.2c0,0.6-0.5,1-1.1,1l-7.9,0C3.5,7.2,3,6.7,3,6.1L2.9,2.9
		c0-0.6,0.5-1.1,1.1-1.1l7.9,0C12.5,1.8,13,2.4,13,3L13.1,6.2L13.1,6.2z"/>
	<polygon points="5,13 8,13 8,16 5,16 	"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.SaveAll()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M20.8,8H9.2C8.4,8,8,8.6,8,9.4v11.2C8,21.4,8.4,22,9.2,22h0.7v-4.1c0-0.5,0.4-0.9,0.9-0.9h8.4c0.5,0,0.9,0.4,0.9,0.9V22h0.7
	c0.8,0,1.2-0.6,1.2-1.4V9.4C22,8.6,21.6,8,20.8,8z M20.1,13.5c0,0.5-0.4,0.8-0.9,0.8h-8.4c-0.5,0-0.9-0.4-0.9-0.9v-2.7
	c0-0.5,0.4-0.9,0.9-0.9h8.4c0.5,0,0.9,0.5,0.9,1L20.1,13.5L20.1,13.5z"/>
<path d="M19,6.4C19,5.6,18.6,5,17.8,5H6.2C5.4,5,5,5.6,5,6.4v11.2C5,18.4,5.4,19,6.2,19h0.7V7.7c0-0.5,0.4-0.9,0.9-0.9h8.4H19V6.4z"
	/>
<path d="M16,3.4C16,2.6,15.6,2,14.8,2H3.2C2.4,2,2,2.6,2,3.4v11.2C2,15.4,2.4,16,3.2,16h0.7V4.7c0-0.5,0.4-0.9,0.9-0.9h8.4H16V3.4z"
	/>
<rect x="12.2" y="20.1" width="2.8" height="1.9"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Search()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M4.2,22c-0.6,0-1.1-0.2-1.5-0.6C2.2,21,2,20.4,2,19.9c0-0.6,0.2-1.1,0.6-1.5l5.2-5.3C7,11.4,6.7,9.5,7.2,7.7
	C8,4.4,11,2,14.5,2c0.6,0,1.2,0.1,1.8,0.2c2,0.5,3.6,1.7,4.6,3.4c1,1.7,1.3,3.7,0.9,5.7c-0.8,3.4-3.8,5.7-7.3,5.7
	c-0.6,0-1.2-0.1-1.8-0.2c-0.6-0.2-1.2-0.4-1.8-0.7l-5.2,5.2C5.3,21.8,4.7,22,4.2,22 M14.5,4.8c-2.2,0-4,1.5-4.5,3.6
	c-0.3,1.3-0.1,2.6,0.7,3.7c0.3,0.5,0.7,0.9,1.2,1.2c0.5,0.3,1,0.6,1.5,0.7c0.4,0.1,0.7,0.1,1.1,0.1c2.2,0,4-1.5,4.5-3.6
	c0.3-1.2,0.1-2.5-0.5-3.5C17.8,6,16.8,5.3,15.6,5C15.2,4.9,14.8,4.8,14.5,4.8"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Signout()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 15 15">
<path class="msportalfx-svg-c01" d="M12.133,3.033c-0.389-0.389-1.019-0.389-1.408,0c-0.389,0.389-0.389,1.019,0,1.408l0,0 c0.892,0.893,1.335,2.054,1.336,3.225c-0.001,1.17-0.444,2.332-1.336,3.225c-0.893,0.892-2.054,1.334-3.225,1.336 c-1.17-0.001-2.332-0.444-3.224-1.336C3.384,9.998,2.941,8.836,2.94,7.666c0.001-1.171,0.444-2.332,1.336-3.225 c0.389-0.389,0.389-1.019,0-1.408c-0.388-0.389-1.019-0.389-1.408,0C1.59,4.31,0.947,5.993,0.948,7.666 C0.947,9.34,1.59,11.022,2.868,12.299c1.277,1.277,2.959,1.92,4.632,1.919h0.004c1.672,0,3.353-0.642,4.628-1.919 c1.278-1.277,1.92-2.959,1.919-4.633C14.053,5.993,13.41,4.31,12.133,3.033z M7.5,8.662c0.55,0,0.996-0.446,0.996-0.996V1.655 c0-0.55-0.446-0.996-0.996-0.996c-0.55,0-0.996,0.446-0.996,0.996v6.011C6.504,8.216,6.95,8.662,7.5,8.662z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Start()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<g>
	<polygon points="20,11.95 6,22 6,2 	"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Stop()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<rect x="4" y="4" width="16" height="16"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Subtract()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 24 24">
    <polygon points="20.87,14 2,14 2,9 21,9 21,14"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Support()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M34.256,14.928c0,5.006-4.059,9.065-9.066,9.065s-9.065-4.059-9.065-9.065s4.058-9.065,9.065-9.065
	C30.197,5.863,34.256,9.922,34.256,14.928"/>
<polygon fill="#59B4D9" points="31.818,27.1 25.19,36.387 18.562,27.1 9.001,27.1 9.001,50 41.38,50 41.38,27.1 "/>
<path fill="#59B4D9" d="M39.966,24.14h-6.881v-3.33h3.552v-5.827c0-6.426-5.228-11.654-11.654-11.654S13.329,8.557,13.329,14.983
	v1.665h-3.33v-1.665C9.999,6.722,16.721,0,24.982,0s14.983,6.722,14.983,14.983L39.966,24.14L39.966,24.14z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.swap()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 24 24">
	<polygon points="14.047,4.976 8.075,9.769 12.968,10.134 10.7,11.963 4.142,11.488 5.011,4.958 7.279,3.129 6.63,8.004 12.657,2.855 13.663,4.336 "/>
	<polygon points="19.927,19.097 17.806,20.715 18.318,16.061 12.026,21.145 10.59,19.38 16.873,14.295 11.98,13.93 14.248,12.101 20.604,12.32 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Tag()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#804998" d="M42.3,0.3L28.6,0.8L0,29.4l20.3,20.3l28.6-28.6L50,8L42.3,0.3z M43.4,10.9c-1.1,1.1-2.9,1.1-4,0
	c-1.1-1.1-1.1-2.9,0-4c1.1-1.1,2.9-1.1,4,0C44.5,8,44.5,9.8,43.4,10.9z"/>
<polygon opacity="0.1" fill="#FFFFFF" points="0,29.4 20.3,49.7 24.9,45.2 24.9,4.5 "/>
<path opacity="0.3" fill="#1E1E1E" d="M45.1,5.2c-2-2-5.3-2-7.3,0c-2,2-2,5.3,0,7.3c2,2,5.3,2,7.3,0C47.1,10.5,47.1,7.2,45.1,5.2z
	 M43.4,10.9c-1.1,1.1-2.9,1.1-4,0c-1.1-1.1-1.1-2.9,0-4c1.1-1.1,2.9-1.1,4,0C44.5,8,44.5,9.8,43.4,10.9z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Tags()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#7FBA00" d="M34.5,0l-9.9,0.4L3.9,21.1l14.7,14.7l20.7-20.7l0.8-9.5L34.5,0z M35.3,7.7c-0.8,0.8-2.1,0.8-2.9,0
	c-0.8-0.8-0.8-2.1,0-2.9c0.8-0.8,2.1-0.8,2.9,0C36.1,5.6,36.1,6.9,35.3,7.7z"/>
<polygon opacity="0.1" fill="#FFFFFF" points="3.9,21.1 18.6,35.8 21.9,32.5 21.9,3.1 "/>
<path opacity="0.3" fill="#1E1E1E" d="M36.5,3.6c-1.5-1.5-3.8-1.5-5.3,0c-1.5,1.5-1.5,3.8,0,5.3c1.5,1.5,3.8,1.5,5.3,0
	C38,7.4,38,5,36.5,3.6z M35.3,7.7c-0.8,0.8-2.1,0.8-2.9,0c-0.8-0.8-0.8-2.1,0-2.9c0.8-0.8,2.1-0.8,2.9,0C36.1,5.6,36.1,6.9,35.3,7.7
	z"/>
<path fill="#3999C6" d="M37.5,7.1l-9.9,0.4L6.9,28.2l14.7,14.7l20.7-20.7l0.8-9.5L37.5,7.1z M38.3,14.8c-0.8,0.8-2.1,0.8-2.9,0
	c-0.8-0.8-0.8-2.1,0-2.9c0.8-0.8,2.1-0.8,2.9,0C39.1,12.7,39.1,14,38.3,14.8z"/>
<polygon opacity="0.1" fill="#FFFFFF" points="6.9,28.2 21.6,42.9 24.9,39.6 24.9,10.2 "/>
<path opacity="0.3" fill="#1E1E1E" d="M39.5,10.7c-1.5-1.5-3.8-1.5-5.3,0c-1.5,1.5-1.5,3.8,0,5.3c1.5,1.5,3.8,1.5,5.3,0
	C41,14.5,41,12.2,39.5,10.7z M38.3,14.8c-0.8,0.8-2.1,0.8-2.9,0c-0.8-0.8-0.8-2.1,0-2.9c0.8-0.8,2.1-0.8,2.9,0
	C39.1,12.7,39.1,14,38.3,14.8z"/>
<path fill="#804998" d="M40.5,14.2l-9.9,0.4L9.9,35.3L24.6,50l20.7-20.7l0.8-9.5L40.5,14.2z M41.3,21.9c-0.8,0.8-2.1,0.8-2.9,0
	c-0.8-0.8-0.8-2.1,0-2.9c0.8-0.8,2.1-0.8,2.9,0C42.1,19.8,42.1,21.1,41.3,21.9z"/>
<polygon opacity="0.1" fill="#FFFFFF" points="9.9,35.3 24.6,50 27.9,46.7 27.9,17.3 "/>
<path opacity="0.3" fill="#1E1E1E" d="M42.5,17.8c-1.5-1.5-3.8-1.5-5.3,0c-1.5,1.5-1.5,3.8,0,5.3c1.5,1.5,3.8,1.5,5.3,0
	C44,21.6,44,19.3,42.5,17.8z M41.3,21.9c-0.8,0.8-2.1,0.8-2.9,0c-0.8-0.8-0.8-2.1,0-2.9c0.8-0.8,2.1-0.8,2.9,0
	C42.1,19.8,42.1,21.1,41.3,21.9z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Tasks()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M47.794,10.559H13.919c-1.218,0-2.206,0.988-2.206,2.206s0.988,2.206,2.206,2.206h33.875
	c1.218,0,2.206-0.988,2.206-2.206S49.012,10.559,47.794,10.559z"/>
<path fill="#59B4D9" d="M47.794,23.735H13.919c-1.218,0-2.206,0.988-2.206,2.206s0.988,2.206,2.206,2.206h33.875
	c1.218,0,2.206-0.988,2.206-2.206S49.012,23.735,47.794,23.735z"/>
<path fill="#59B4D9" d="M47.794,36.912H13.919c-1.218,0-2.206,0.988-2.206,2.206c0,1.218,0.988,2.206,2.206,2.206h33.875
	c1.218,0,2.206-0.988,2.206-2.206C50,37.899,49.012,36.912,47.794,36.912z"/>
<path fill="#59B4D9" d="M2.153,17.176v-7.055c-0.319,0.187-0.649,0.329-0.991,0.427S0.433,10.719,0,10.764V8.577
	c0.615-0.105,1.188-0.27,1.719-0.496C2.25,7.855,2.794,7.547,3.35,7.155h1.716v10.021H2.153z"/>
<path fill="#59B4D9" d="M0.068,31.176V30.39c0-0.556,0.068-1.039,0.205-1.449s0.352-0.795,0.646-1.155
	c0.293-0.36,0.805-0.822,1.534-1.388c0.52-0.41,0.864-0.705,1.036-0.885c0.17-0.18,0.298-0.354,0.383-0.523
	c0.084-0.168,0.126-0.351,0.126-0.547c0-0.652-0.392-0.978-1.176-0.978c-0.798,0-1.552,0.29-2.263,0.868v-2.386
	c0.542-0.273,1.049-0.462,1.521-0.567c0.471-0.105,0.955-0.157,1.452-0.157c1.066,0,1.892,0.253,2.475,0.759s0.875,1.219,0.875,2.14
	c0,0.707-0.152,1.306-0.458,1.797c-0.306,0.492-0.873,1.005-1.702,1.538c-0.634,0.41-1.054,0.71-1.261,0.898
	c-0.208,0.189-0.313,0.351-0.318,0.482h3.828v2.338H0.068z"/>
<path fill="#59B4D9" d="M6.836,42.373c0,0.943-0.342,1.675-1.025,2.194s-1.661,0.779-2.933,0.779c-0.474,0-0.943-0.04-1.408-0.12
	c-0.465-0.079-0.863-0.186-1.196-0.317v-2.283c0.306,0.183,0.654,0.326,1.046,0.431s0.788,0.157,1.189,0.157
	c0.465,0,0.821-0.083,1.069-0.249c0.249-0.167,0.373-0.405,0.373-0.715c0-0.314-0.152-0.561-0.458-0.738s-0.731-0.267-1.278-0.267
	H1.271v-2.146h0.834c0.552,0,0.958-0.085,1.221-0.257c0.262-0.17,0.393-0.393,0.393-0.666c0-0.26-0.1-0.465-0.297-0.615
	c-0.199-0.15-0.503-0.226-0.913-0.226c-0.634,0-1.267,0.183-1.9,0.547v-2.181c0.793-0.328,1.627-0.492,2.502-0.492
	c1.085,0,1.935,0.221,2.55,0.663s0.923,1.055,0.923,1.839c0,0.61-0.165,1.123-0.495,1.538c-0.331,0.415-0.795,0.686-1.392,0.813
	v0.034c0.661,0.091,1.183,0.343,1.565,0.755C6.645,41.265,6.836,41.771,6.836,42.373z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.ThisWeek()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="21px" height="21px" viewBox="0 0 21 21" enable-background="new 0 0 21 21" xml:space="preserve">
<path fill="#1E1E1E" d="M2.832,9.563C2.654,9.661,2.42,9.71,2.129,9.71c-0.823,0-1.234-0.459-1.234-1.377V5.552H0.087V4.909h0.808
	V3.762l0.752-0.243v1.391h1.184v0.643H1.647V8.2c0,0.315,0.054,0.54,0.161,0.675s0.285,0.202,0.533,0.202
	c0.189,0,0.353-0.052,0.491-0.156V9.563z"/>
<path fill="#1E1E1E" d="M7.738,9.609H6.986V6.901c0-0.979-0.364-1.469-1.092-1.469c-0.367,0-0.676,0.142-0.927,0.425
	S4.59,6.504,4.59,6.947v2.662H3.837V2.651H4.59v3.038h0.019C4.969,5.096,5.483,4.799,6.15,4.799c1.059,0,1.588,0.639,1.588,1.914
	V9.609z"/>
<path fill="#1E1E1E" d="M9.542,3.716c-0.135,0-0.25-0.046-0.344-0.138S9.056,3.37,9.056,3.229s0.047-0.258,0.142-0.352
	c0.095-0.093,0.209-0.14,0.344-0.14c0.138,0,0.255,0.047,0.351,0.14c0.096,0.094,0.145,0.211,0.145,0.352
	c0,0.135-0.048,0.249-0.145,0.345C9.797,3.669,9.68,3.716,9.542,3.716z M9.909,9.609H9.156v-4.7h0.753V9.609z"/>
<path fill="#1E1E1E" d="M11.149,9.439V8.632c0.41,0.303,0.861,0.454,1.354,0.454c0.661,0,0.991-0.22,0.991-0.661
	c0-0.125-0.028-0.231-0.085-0.318c-0.057-0.088-0.133-0.165-0.229-0.232c-0.096-0.066-0.209-0.127-0.339-0.181
	c-0.13-0.054-0.271-0.109-0.42-0.168c-0.208-0.082-0.391-0.166-0.548-0.25s-0.289-0.179-0.395-0.284s-0.186-0.226-0.239-0.36
	c-0.054-0.135-0.081-0.292-0.081-0.473c0-0.221,0.051-0.416,0.152-0.586c0.101-0.17,0.235-0.312,0.404-0.427
	c0.168-0.114,0.36-0.201,0.576-0.259c0.216-0.059,0.438-0.088,0.668-0.088c0.407,0,0.771,0.071,1.092,0.212v0.762
	c-0.346-0.227-0.743-0.34-1.193-0.34c-0.141,0-0.268,0.016-0.381,0.048s-0.21,0.078-0.292,0.136
	c-0.081,0.059-0.144,0.128-0.188,0.209s-0.066,0.171-0.066,0.269c0,0.122,0.022,0.225,0.066,0.308
	c0.044,0.082,0.109,0.156,0.195,0.22c0.086,0.064,0.19,0.123,0.313,0.175c0.122,0.052,0.261,0.108,0.417,0.17
	c0.208,0.079,0.395,0.161,0.56,0.245c0.165,0.084,0.306,0.18,0.422,0.285c0.116,0.105,0.206,0.227,0.268,0.364
	c0.063,0.138,0.094,0.302,0.094,0.491c0,0.232-0.051,0.435-0.154,0.606c-0.103,0.171-0.239,0.313-0.411,0.427
	s-0.369,0.197-0.592,0.252c-0.223,0.056-0.458,0.083-0.702,0.083C11.923,9.72,11.503,9.626,11.149,9.439z"/>
<path fill="#1E1E1E" d="M6.572,16.189l-1.409,4.7h-0.78l-0.969-3.364c-0.037-0.129-0.061-0.273-0.073-0.437H3.323
	c-0.009,0.11-0.041,0.253-0.096,0.428L2.175,20.89H1.423L0,16.189h0.789l0.973,3.534c0.031,0.107,0.052,0.248,0.064,0.423h0.037
	c0.009-0.135,0.037-0.278,0.083-0.432l1.083-3.525h0.688l0.973,3.544c0.031,0.113,0.054,0.254,0.069,0.422h0.037
	c0.006-0.119,0.032-0.26,0.078-0.422l0.955-3.544H6.572z"/>
<path fill="#1E1E1E" d="M11.176,18.728H7.857c0.012,0.523,0.153,0.928,0.422,1.212c0.27,0.284,0.64,0.427,1.111,0.427
	c0.529,0,1.016-0.174,1.459-0.523v0.707C10.437,20.85,9.891,21,9.211,21c-0.664,0-1.186-0.214-1.565-0.641s-0.569-1.027-0.569-1.802
	c0-0.73,0.208-1.327,0.622-1.787c0.415-0.461,0.93-0.691,1.544-0.691c0.615,0,1.091,0.199,1.428,0.597
	c0.336,0.398,0.505,0.95,0.505,1.657V18.728z M10.405,18.09c-0.003-0.435-0.108-0.772-0.314-1.015
	c-0.207-0.241-0.493-0.362-0.86-0.362c-0.355,0-0.656,0.127-0.904,0.381c-0.248,0.254-0.401,0.586-0.459,0.996H10.405z"/>
<path fill="#1E1E1E" d="M16.092,18.728h-3.319c0.012,0.523,0.153,0.928,0.422,1.212c0.27,0.284,0.64,0.427,1.111,0.427
	c0.529,0,1.016-0.174,1.459-0.523v0.707c-0.413,0.3-0.959,0.45-1.639,0.45c-0.664,0-1.186-0.214-1.565-0.641
	s-0.569-1.027-0.569-1.802c0-0.73,0.208-1.327,0.622-1.787c0.415-0.461,0.93-0.691,1.544-0.691c0.615,0,1.091,0.199,1.428,0.597
	c0.336,0.398,0.505,0.95,0.505,1.657V18.728z M15.32,18.09c-0.003-0.435-0.108-0.772-0.314-1.015
	c-0.207-0.241-0.493-0.362-0.86-0.362c-0.355,0-0.656,0.127-0.904,0.381c-0.248,0.254-0.401,0.586-0.459,0.996H15.32z"/>
<path fill="#1E1E1E" d="M21.131,20.89h-1.056l-2.074-2.258h-0.019v2.258H17.23v-6.958h0.753v4.411h0.019l1.974-2.153h0.987
	l-2.18,2.268L21.131,20.89z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Tools()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0.5 0.5 50 50" enable-background="new 0.5 0.5 50 50" xml:space="preserve">
<path fill="#59B4D9" d="M25.19,28.092l2.75,2.672l3.125-3.032c-0.172-1.485,0.328-3.015,1.5-4.14c1.172-1.14,2.75-1.625,4.265-1.453
	L50.5,8.905L42.845,1.5L29.19,14.733c0.157,1.468-0.328,3.015-1.5,4.14c-1.172,1.125-2.75,1.61-4.265,1.437L20.3,23.342l2.75,2.672
	L9.55,39.075l-0.5-0.468l-2.593,2.015l-4.358,6.672l1.11,1.078l6.89-4.218l2.093-2.515l-0.5-0.485L25.19,28.092z"/>
<path fill="#59B4D9" d="M33.002,26.842c-0.11,0.422-0.125,0.86-0.078,1.297l0.062,0.562l-0.407,0.407l-3.125,3.015l-1.14,1.11
	l-2.75-2.657l-1.485,1.422L39.343,46.87c0.985,0.953,2.282,1.422,3.562,1.422c1.297,0,2.578-0.468,3.562-1.422
	c1.968-1.907,1.968-4.983,0-6.89L33.002,26.842z"/>
<path fill="#59B4D9" d="M14.487,22.78l0.032,0.047l4.562,4.407l1.485-1.422l-1.89-1.843l-0.86-0.828l4.672-4.532l0.578,0.062
	c0.157,0.015,0.297,0.015,0.453,0.015c0.297,0,0.61-0.032,0.89-0.093l-2.765-2.672l-0.203-0.172
	c0.985-3.468,0.078-7.343-2.733-10.077C15.908,2.968,11.94,2.078,8.378,3l6.03,5.843l-1.578,5.75l-5.92,1.53l-6.047-5.858
	c-0.953,3.453-0.032,7.297,2.765,10.015C6.567,23.123,10.785,23.952,14.487,22.78z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.TrendDown()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<polygon fill="#0F0F0F" points="0,1.5 5.25,6.75 1.5,6.75 3.608,9 9,9 9,3.75 6.75,1.5 6.75,5.25 1.5,0 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.TrendUp()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<polygon fill="#0F0F0F" points="0,7.5 5.25,2.25 1.5,2.25 3.608,0 9,0 9,5.25 6.75,7.5 6.75,3.75 1.5,9 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Triangle()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="10px" height="10px" viewBox="0 0 10 10" enable-background="new 0 0 10 10" xml:space="preserve">
<polygon points="3,0 8,5 3,10 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Undo()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 height="24px" width="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<path d="M20.8,10.2c0,2.1-1,4.1-2.6,5.5L11.1,22l-1.9-2.2l7.1-6.3c1-0.8,1.6-2,1.6-3.3c0-2.4-1.9-4.3-4.3-4.3c-1,0-2,0.4-2.8,1
	l-2.6,2h5l-3.2,3H3.2v-1.4V9V5.2l3-3.2v5l2.7-2.3c1.3-1.1,3-1.7,4.7-1.7C17.5,3,20.8,6.2,20.8,10.2z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Unlock()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="-0.5 0.5 24 24" enable-background="new -0.5 0.5 24 24" xml:space="preserve">
<g>
	<path d="M15.5,12.5h-7V9.13c0-0.97,0.36-2.26,0.92-2.87S11.19,5.3,12,5.29c0.81,0.01,2.02,0.36,2.58,0.97
		c0.56,0.61,0.92,1.47,0.92,2.44v1.738l3,0.062V8.7c0-1.68-0.62-3.23-1.66-4.35C15.82,3.22,13.62,2.5,12,2.5S8.18,3.22,7.16,4.35
		C6.12,5.47,5.5,7.02,5.5,8.7v3.8h0.01c-1.479,0-2.01,0.875-2.01,2v6c0,0.979,0.609,2,1.74,2h13.52c1.302,0,1.74-1.021,1.74-2v-6
		c0-1.016-0.406-2-2.01-2h0.01H15.5z"/>
</g>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Unpin()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<path fill="#FFFFFF" d="M14.8,9.2c-1.6-1.6-4.1-1.6-5.7,0c-1.6,1.6-1.6,4.1,0,5.7c1.6,1.6,4.1,1.6,5.7,0S16.4,10.7,14.8,9.2z M10,14
	c-1-1-1.1-2.5-0.4-3.6l4,4C12.5,15.1,11,15,10,14z M14.4,13.6l-4-4C11.5,8.9,13,9,14,10C15,11,15.1,12.5,14.4,13.6z"/>
<path fill="#FFFFFF" d="M12.9,6.5l0.7-0.7c0.8,0.1,1.6,0.1,2.4-0.2L10.3,0c-0.2,0.8-0.3,1.6-0.2,2.4L5.9,6.6C4.6,6.1,3.1,6,1.8,6.4
	l3,2.9L0,16l0,0l0,0l6.3-4.5C6.5,10.2,7,9,7.9,8.1C9.3,6.7,11.2,6.2,12.9,6.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Upload()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<polygon points="10.745,12.91 5.5,17.6 5.5,13.78 12.511,7.5 19.5,13.75 19.5,17.57 14.288,12.91 14.288,22.5 10.745,22.5 "/>
<rect x="5.5" y="2.5" width="14" height="3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Variables()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#3E3E3E" d="M22.972,4.679c1.417,1.504,2.488,3.009,3.211,4.512c0.521,1.041,1.374,3.775,2.56,8.2l3.818-5.727
	c1.012-1.388,2.242-2.712,3.688-3.97s2.719-2.119,3.818-2.582c0.694-0.289,1.461-0.434,2.3-0.434c1.244,0,2.235,0.333,2.972,0.998
	c0.738,0.666,1.106,1.475,1.106,2.43c0,1.1-0.217,1.851-0.651,2.256c-0.81,0.723-1.736,1.085-2.777,1.085
	c-0.607,0-1.258-0.13-1.952-0.39c-1.36-0.462-2.271-0.694-2.733-0.694c-0.694,0-1.519,0.406-2.473,1.215
	c-1.793,1.504-3.934,4.411-6.421,8.721l3.558,14.926c0.55,2.285,1.012,3.652,1.388,4.1c0.376,0.449,0.752,0.673,1.128,0.673
	c0.607,0,1.316-0.332,2.126-0.998c1.591-1.33,2.95-3.066,4.079-5.207l1.519,0.781c-1.822,3.413-4.136,6.248-6.942,8.504
	c-1.591,1.273-2.936,1.909-4.035,1.909c-1.62,0-2.907-0.911-3.862-2.733c-0.607-1.128-1.866-5.988-3.775-14.579
	c-4.512,7.839-8.128,12.886-10.847,15.143c-1.764,1.446-3.471,2.169-5.12,2.169c-1.157,0-2.213-0.419-3.167-1.258
	c-0.694-0.636-1.041-1.49-1.041-2.56c0-0.955,0.318-1.75,0.955-2.386c0.636-0.636,1.417-0.955,2.343-0.955s1.909,0.463,2.95,1.388
	c0.752,0.666,1.331,0.998,1.736,0.998c0.347,0,0.795-0.231,1.345-0.694c1.36-1.099,3.211-3.471,5.554-7.116s3.876-6.276,4.599-7.897
	c-1.793-7.029-2.762-10.745-2.907-11.151c-0.665-1.88-1.533-3.211-2.603-3.992s-2.647-1.171-4.729-1.171
	c-0.665,0-1.432,0.029-2.3,0.087V6.718L22.972,4.679z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.VisualStudio()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 21 21">
<path fill-rule="evenodd" clip-rule="evenodd" class="msportalfx-svg-c20" d="M9.714,10.007L15,5.893v8.229L9.714,10.007z M2,13.007v-6l3,3 L2,13.007z M15,0.007L7.064,7.943L2,4.007l-2,1v10l2,1l5.064-3.936L15,20.007l5-2v-16L15,0.007z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.VisualStudioLogoBox()</h6><svg class="msportalfx-svg-placeholder" viewBox="0 0 30 30">
<rect class="msportalfx-svg-c20" width="30" height="30"/>
<path class="msportalfx-svg-c01" d="M14.743,14.924l4.757-3.703v7.406L14.743,14.924z M7.8,17.624v-5.4l2.7,2.7L7.8,17.624z M19.5,5.924 l-7.142,7.142L7.8,9.524l-1.8,0.9v9l1.8,0.9l4.558-3.542l7.142,7.142l4.5-1.8v-14.4L19.5,5.924z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Warning()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<path fill="#FF8C00" d="M14.697,15H8.001H1.302c-1.067,0-1.629-1.108-1.102-2.007l3.38-5.94L6.926,1.46
	c0.537-0.901,1.605-0.914,2.128-0.014l3.392,5.821l3.346,5.741C16.325,13.903,15.788,15,14.697,15z"/>
<circle fill="#FFFFFF" cx="8" cy="11.761" r="1.093"/>
<polygon fill="#FFFFFF" points="8.218,5.094 7.891,5.094 7.075,5.094 7.367,10.161 7.891,10.161 8.218,10.161 8.743,10.161 
	9.036,5.094 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.WebHostingPlan()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="24px" height="24px" viewBox="0 0 24 24" enable-background="new 0 0 24 24" xml:space="preserve">
<circle cx="5.6" cy="5.4" r="1"/>
<circle cx="5.6" cy="9.4" r="1"/>
<circle cx="5.6" cy="13.4" r="1"/>
<path d="M4.9,18.7c0-1.5,0.7-2.8,1.8-3.7H4.5c-0.8,0-1.5-0.7-1.5-1.5s0.7-1.5,1.5-1.5h7c0,0,0.1,0,0.1,0c0.7-0.5,1.5-0.9,2.4-1.1V3
	c0-0.6-0.5-1-1-1H3C2.5,2,2,2.5,2,3v18c0,0.6,0.5,1,1,1h3.2C5.4,21.1,4.9,20,4.9,18.7z M4.5,3.9h7c0.8,0,1.5,0.7,1.5,1.5
	s-0.7,1.5-1.5,1.5h-7C3.7,6.9,3,6.3,3,5.4S3.7,3.9,4.5,3.9z M4.5,7.9h7c0.8,0,1.5,0.7,1.5,1.5s-0.7,1.5-1.5,1.5h-7
	C3.7,10.9,3,10.3,3,9.4S3.7,7.9,4.5,7.9z"/>
<path d="M22,20.2c0-1-0.8-1.8-1.8-1.8c-0.1,0-0.1,0-0.2,0c0.1-0.4,0.2-0.8,0.2-1.3c0-2.7-2.2-4.9-4.8-4.9c-2.1,0-3.9,1.4-4.6,3.3
	c-0.3-0.1-0.7-0.2-1.1-0.2c-1.8,0-3.3,1.5-3.3,3.3c0,1.8,1.5,3.3,3.3,3.3c0,0,0,0,0,0v0h10.7l0,0C21.3,21.9,22,21.1,22,20.2"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Wrench()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="-0.5 0.5 16 16" enable-background="new -0.5 0.5 16 16" xml:space="preserve">
<path fill="#FFFFFF" d="M13.478,4.879l-1.865-0.496l-0.496-1.865l1.905-1.905c-1.128-0.304-2.377-0.016-3.257,0.864
	C8.877,2.366,8.588,3.623,8.901,4.759L3.763,9.897C2.626,9.585,1.37,9.873,0.481,10.761c-0.88,0.88-1.168,2.129-0.864,3.257
	l1.905-1.905l1.865,0.496l0.496,1.865l-1.905,1.905c1.128,0.304,2.377,0.016,3.257-0.864c0.928-0.928,1.2-2.257,0.824-3.425
	l5.034-5.034c1.168,0.376,2.497,0.104,3.425-0.824c0.88-0.88,1.168-2.129,0.864-3.257L13.478,4.879z"/>
</svg>
</li>
<li><h6>MsPortalFx.Base.Images.StatusBadge.Canceled()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<circle fill="#A0A1A2" cx="8" cy="8" r="8"/>
<polygon fill="#FFFFFF" points="12.073,5.285 10.715,3.927 8,6.642 5.285,3.927 3.927,5.285 6.642,8 3.927,10.715 5.285,12.073 
	8,9.358 10.715,12.073 12.073,10.715 9.358,8 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Disabled()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0.5 16.5 16 16" enable-background="new 0.5 16.5 16 16" xml:space="preserve">
<circle fill="#EC008C" cx="8.5" cy="24.5" r="8"/>
<rect x="3.5" y="23.5" fill="#FFFFFF" width="10" height="2"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Error()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<circle fill="#EC008C" cx="4.5" cy="4.5" r="4.5"/>
<circle fill="#FFFFFF" cx="4.5" cy="6.438" r="0.697"/>
<polygon fill="#FFFFFF" points="4.604,2.186 4.396,2.186 3.875,2.186 4.061,5.418 4.396,5.418 4.604,5.418 4.939,5.418 5.125,2.186 
	"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.ErrorOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="12px" height="12px" viewBox="2.5 2.5 12 12" enable-background="new 2.5 2.5 12 12" xml:space="preserve">
<path fill="#EC008C" d="M8.5,14C5.467,14,3,11.532,3,8.5C3,5.467,5.467,3,8.5,3C11.532,3,14,5.467,14,8.5
	C14,11.532,11.532,14,8.5,14z"/>
<path fill="#FFFFFF" d="M8.5,3.5c2.757,0,5,2.243,5,5s-2.243,5-5,5s-5-2.243-5-5S5.743,3.5,8.5,3.5 M8.5,2.5c-3.314,0-6,2.686-6,6
	s2.686,6,6,6s6-2.686,6-6S11.814,2.5,8.5,2.5L8.5,2.5z"/>
<circle fill="#FFFFFF" cx="8.5" cy="11.084" r="0.929"/>
<polygon fill="#FFFFFF" points="8.639,5.415 8.361,5.415 7.667,5.415 7.915,9.723 8.361,9.723 8.639,9.723 9.085,9.723 9.333,5.415 
	"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Failed()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<circle fill="#EC008C" cx="4.5" cy="4.5" r="4.5"/>
<polygon fill="#FFFFFF" points="7,2.8 6.2,2 4.5,3.7 2.8,2 2,2.8 3.7,4.5 2,6.2 2.8,7 4.5,5.3 6.2,7 7,6.2 5.3,4.5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.FailedOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 12 12" enable-background="new 0 0 12 12" xml:space="preserve">
<path fill="#EC008C" d="M6,11.5C3,11.5,0.5,9,0.5,6C0.5,3,3,0.5,6,0.5c3,0,5.5,2.5,5.5,5.5C11.5,9,9,11.5,6,11.5z"/>
<path fill="#FFFFFF" d="M6,1c2.8,0,5,2.2,5,5s-2.2,5-5,5S1,8.8,1,6S3.2,1,6,1 M6,0C2.7,0,0,2.7,0,6s2.7,6,6,6s6-2.7,6-6S9.3,0,6,0
	L6,0z"/>
<polygon fill="#FFFFFF" points="9,4 8,3 6,5 4,3 3,4 5,6 3,8 4,9 6,7 8,9 9,8 7,6 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Info()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<circle fill="#0072C6" cx="4.5" cy="4.5" r="4.5"/>
<circle fill="#FFFFFF" cx="4.5" cy="2.315" r="0.815"/>
<polygon fill="#FFFFFF" points="4.378,7.5 4.622,7.5 5.231,7.5 5.231,3.93 4.622,3.93 4.378,3.93 3.776,3.93 3.769,7.5 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.InfoOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="12px" height="12px" viewBox="2.5 2.5 12 12" enable-background="new 2.5 2.5 12 12" xml:space="preserve">
<path fill="#0072C6" d="M8.5,14C5.467,14,3,11.532,3,8.5C3,5.467,5.467,3,8.5,3C11.532,3,14,5.467,14,8.5
	C14,11.532,11.532,14,8.5,14z"/>
<path fill="#FFFFFF" d="M8.5,3.5c2.757,0,5,2.243,5,5s-2.243,5-5,5s-5-2.243-5-5S5.743,3.5,8.5,3.5 M8.5,2.5c-3.314,0-6,2.686-6,6
	s2.686,6,6,6s6-2.686,6-6S11.814,2.5,8.5,2.5L8.5,2.5z"/>
<circle fill="#FFFFFF" cx="8.5" cy="5.951" r="0.951"/>
<polygon fill="#FFFFFF" points="8.357,12 8.642,12 9.353,12 9.353,7.836 8.642,7.836 8.357,7.836 7.656,7.836 7.647,12 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.None()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<circle fill="#A0A1A2" cx="8" cy="8" r="8"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Pending()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<circle fill="#0072C6" cx="8" cy="8" r="8"/>
<path fill="#FFFFFF" d="M7.747,3.18H7.693c-1.119,0-2.157,0.394-2.996,1.017L3,2.5v4.467h4.413h0.053L5.781,5.281
	C6.335,4.856,6.996,4.62,7.747,4.62c1.676,0,3.066,1.179,3.358,2.72h1.474C12.219,4.98,10.211,3.18,7.747,3.18z"/>
<path fill="#FFFFFF" d="M12.8,9.013H8.387l0.022,0.012H8.387l1.633,1.633c-0.554,0.388-1.215,0.595-1.966,0.595
	c-1.512,0-2.799-1.022-3.239-2.4H3.269c0.485,2.202,2.432,3.84,4.784,3.84h0.053c1.102,0,2.127-0.38,2.961-0.987l1.786,1.786v-0.119
	V9.025V9.013H12.8z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Stopped()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<circle fill="#A0A1A2" cx="4.5" cy="4.5" r="4.5"/>
<rect x="3" y="3" fill="#FFFFFF" width="3" height="3"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.StoppedOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="12px" height="12px" viewBox="2.5 2.5 12 12" enable-background="new 2.5 2.5 12 12" xml:space="preserve">
<path fill="#A0A1A2" d="M8.5,14C5.467,14,3,11.532,3,8.5C3,5.467,5.467,3,8.5,3C11.532,3,14,5.467,14,8.5
	C14,11.532,11.532,14,8.5,14z"/>
<path fill="#FFFFFF" d="M8.5,3.5c2.757,0,5,2.243,5,5s-2.243,5-5,5s-5-2.243-5-5S5.743,3.5,8.5,3.5 M8.5,2.5c-3.314,0-6,2.686-6,6
	s2.686,6,6,6s6-2.686,6-6S11.814,2.5,8.5,2.5L8.5,2.5z"/>
<rect x="6.5" y="6.5" fill="#FFFFFF" width="4" height="4"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Success()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="16px" height="16px" viewBox="0 0 16 16" enable-background="new 0 0 16 16" xml:space="preserve">
<circle fill="#7FBA00" cx="8" cy="8" r="8"/>
<path fill="#FFFFFF" d="M3.989,8.469L3.7,8.156C3.625,8.073,3.625,7.942,3.712,7.863l0.835-0.772
	c0.04-0.036,0.087-0.055,0.139-0.055c0.059,0,0.111,0.024,0.15,0.067l2.296,2.462l3.951-5.06c0.04-0.051,0.099-0.079,0.162-0.079
	c0.048,0,0.091,0.016,0.127,0.044l0.903,0.697c0.044,0.032,0.071,0.079,0.079,0.135c0.004,0.055-0.008,0.111-0.044,0.15
	l-5.075,6.497L3.989,8.469z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.SuccessOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="12px" height="12px" viewBox="2.5 2.5 12 12" enable-background="new 2.5 2.5 12 12" xml:space="preserve">
<path fill="#7FBA00" d="M8.5,14C5.364,14,3,11.636,3,8.5S5.364,3,8.5,3S14,5.364,14,8.5S11.636,14,8.5,14z"/>
<path fill="#FFFFFF" d="M8.5,3.5c2.897,0,5,2.103,5,5s-2.103,5-5,5s-5-2.103-5-5S5.603,3.5,8.5,3.5 M8.5,2.5c-3.429,0-6,2.571-6,6
	s2.571,6,6,6s6-2.571,6-6S11.929,2.5,8.5,2.5L8.5,2.5z"/>
<polygon fill="#FFFFFF" points="5.071,8.5 5.071,8.5 5.929,7.643 5.929,7.643 5.929,7.643 7.643,9.357 11.071,5.929 11.071,5.929 
	11.071,5.929 11.929,6.786 11.929,6.786 11.929,6.786 7.643,11.071 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Unknown()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="-0.5 0.5 9 9" enable-background="new -0.5 0.5 9 9" xml:space="preserve">
<circle fill="#A0A1A2" cx="4" cy="5" r="4.5"/>
<path fill="#FFFFFF" d="M5.5,3.67c0,0.45-0.24,0.87-0.69,1.26C4.63,5.08,4.48,5.2,4.42,5.32C4.36,5.41,4.33,5.56,4.33,5.71v0.18
	H3.49V5.62c0-0.39,0.15-0.72,0.48-0.99c0.21-0.15,0.33-0.3,0.39-0.39c0.06-0.09,0.12-0.24,0.12-0.39s-0.06-0.24-0.15-0.33
	c-0.12-0.12-0.24-0.18-0.42-0.18c-0.36,0-0.69,0.15-1.02,0.42V2.8c0.33-0.21,0.72-0.3,1.14-0.3c0.45,0,0.84,0.12,1.08,0.33
	C5.38,3.01,5.5,3.31,5.5,3.67z"/>
<path fill="#FFFFFF" d="M4.5,7.04c0,0.15-0.06,0.3-0.18,0.39S4.05,7.58,3.9,7.58c-0.18,0-0.3-0.06-0.42-0.15S3.3,7.19,3.3,7.04
	s0.06-0.3,0.18-0.39C3.599,6.56,3.75,6.5,3.93,6.5c0.18,0,0.33,0.06,0.42,0.15C4.44,6.74,4.5,6.89,4.5,7.04z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.UnknownOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="12px" height="12px" viewBox="0 0 12 12" enable-background="new 0 0 12 12" xml:space="preserve">
<path fill="#A0A1A2" d="M6,11.5c-3.033,0-5.5-2.468-5.5-5.5c0-3.033,2.467-5.5,5.5-5.5c3.032,0,5.5,2.467,5.5,5.5
	C11.5,9.032,9.032,11.5,6,11.5z"/>
<path fill="#FFFFFF" d="M6,1c2.757,0,5,2.243,5,5s-2.243,5-5,5S1,8.757,1,6S3.243,1,6,1 M6,0C2.686,0,0,2.686,0,6s2.686,6,6,6
	s6-2.686,6-6S9.314,0,6,0L6,0z"/>
<path fill="#FFFFFF" d="M8.193,4.135c0,0.655-0.349,1.265-1.003,1.832C6.929,6.186,6.711,6.36,6.623,6.534
	c-0.086,0.13-0.13,0.349-0.13,0.567v0.262H5.271V6.97c0-0.567,0.218-1.047,0.697-1.439c0.306-0.218,0.48-0.436,0.568-0.567
	s0.174-0.349,0.174-0.567c0-0.218-0.088-0.349-0.218-0.48C6.318,3.743,6.144,3.657,5.882,3.657c-0.523,0-1.003,0.218-1.482,0.611
	V2.872c0.479-0.306,1.046-0.436,1.656-0.436c0.655,0,1.221,0.174,1.57,0.48C8.019,3.176,8.193,3.613,8.193,4.135z"/>
<path fill="#FFFFFF" d="M6.74,9.034c0,0.218-0.088,0.436-0.262,0.567c-0.174,0.13-0.392,0.218-0.611,0.218
	c-0.262,0-0.436-0.088-0.611-0.218C5.083,9.47,4.995,9.252,4.995,9.034c0-0.218,0.088-0.436,0.262-0.567
	c0.173-0.13,0.392-0.218,0.655-0.218s0.48,0.088,0.611,0.218C6.652,8.597,6.74,8.816,6.74,9.034z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.UpdateOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 12 12" enable-background="new 0 0 12 12" xml:space="preserve">
<path fill="#0072C6" d="M6,11.5C3,11.5,0.5,9,0.5,6C0.5,3,3,0.5,6,0.5c3,0,5.5,2.5,5.5,5.5C11.5,9,9,11.5,6,11.5z"/>
<path fill="#FFFFFF" d="M6,1c2.8,0,5,2.2,5,5s-2.2,5-5,5S1,8.8,1,6S3.2,1,6,1 M6,0C2.7,0,0,2.7,0,6s2.7,6,6,6s6-2.7,6-6S9.3,0,6,0
	L6,0z"/>
<polygon fill="#FFFFFF" points="6,3 3,5.7 5.2,5.7 5.2,9 6.8,9 6.8,5.7 9,5.7 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.Warning()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="9px" height="9px" viewBox="0 0 9 9" enable-background="new 0 0 9 9" xml:space="preserve">
<path fill="#FF8C00" d="M8.267,8H4.501H0.733c-0.6,0-0.916-0.623-0.62-1.129L2.014,3.53l1.882-3.146
	C4.198-0.123,4.799-0.13,5.093,0.376L7.001,3.65l1.882,3.229C9.183,7.383,8.881,8,8.267,8z"/>
<circle fill="#FFFFFF" cx="4.5" cy="6.178" r="0.615"/>
<polygon fill="#FFFFFF" points="4.623,2.428 4.439,2.428 3.98,2.428 4.144,5.278 4.439,5.278 4.623,5.278 4.918,5.278 5.083,2.428 
	"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.StatusBadge.WarningOutline()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="12px" height="12px" viewBox="2.5 2.5 12 12" enable-background="new 2.5 2.5 12 12" xml:space="preserve">
<path fill="#FF8C00" d="M3.396,13.042c-0.23,0-0.337-0.129-0.381-0.205c-0.081-0.141-0.074-0.317,0.018-0.474l2.327-4.056
	l2.295-3.806c0.093-0.155,0.22-0.244,0.349-0.244c0.124,0,0.24,0.082,0.326,0.229l4.632,7.886c0.097,0.159,0.106,0.334,0.029,0.47
	c-0.072,0.128-0.21,0.199-0.387,0.199H3.396z"/>
<path fill="#FFFFFF" d="M7.995,4.823l2.271,3.867l2.282,3.894H8.001l-4.566-0.001l2.311-4.029L7.995,4.823 M8.003,3.8
	c-0.277,0-0.555,0.156-0.742,0.465l-2.3,3.815l-2.324,4.051C2.276,12.744,2.662,13.5,3.396,13.5h4.606h4.602
	c0.751,0,1.12-0.748,0.753-1.36l-2.3-3.915l-2.332-3.97C8.546,3.951,8.275,3.8,8.003,3.8L8.003,3.8z"/>
<circle fill="#FFFFFF" cx="7.888" cy="11.436" r="0.807"/>
<polygon fill="#FFFFFF" points="8.04,6.972 7.813,6.972 7.249,6.972 7.451,10.202 7.813,10.202 8.04,10.202 8.403,10.202 
	8.605,6.972 "/>
</svg>
</li>
<li><h6>MsPortalFx.Base.Images.Logos.Bitbucket()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<ellipse fill="#0072C6" cx="14.52" cy="15.01" rx="2.072" ry="2.072"/>
<path fill="#0072C6" d="M14.5,0.5C7.312,0.5,1.483,2.404,1.483,4.761l2.16,12.727c0,2.52,4.854,4.552,10.857,4.552
	c5.992,0,10.857-2.032,10.857-4.552l2.16-12.727C27.505,2.404,21.688,0.5,14.5,0.5z M14.523,19.149
	c-2.299,0-4.145-1.858-4.145-4.145s1.846-4.134,4.145-4.134c2.288,0,4.134,1.846,4.134,4.134S16.811,19.149,14.523,19.149z
	 M14.5,6.201c-4.598,0-8.314-0.801-8.314-1.788c0-0.987,3.716-1.788,8.314-1.788c4.587,0,8.303,0.801,8.303,1.788
	C22.803,5.4,19.087,6.201,14.5,6.201z"/>
<path fill="#0072C6" d="M5.761,21.988c-0.163-0.128-0.302-0.255-0.441-0.383c-0.035-0.012-0.081-0.012-0.128-0.012
	c-0.279,0-0.511,0.198-0.581,0.453l0.105,0.511l0.883,4.366c0,1.788,3.983,3.576,8.895,3.576c4.923,0,8.906-1.788,8.906-3.576
	l0.883-4.366l0.105-0.511c-0.07-0.255-0.302-0.453-0.592-0.453c-0.035,0-0.081,0-0.128,0.012c-0.128,0.128-0.267,0.255-0.43,0.383
	c-1.661,1.277-4.947,2.148-8.744,2.148C10.708,24.137,7.421,23.266,5.761,21.988z"/>
<ellipse opacity="0.5" fill="#1E1E1E" cx="14.52" cy="15.01" rx="2.072" ry="2.072"/>
<path opacity="0.5" fill="#1E1E1E" d="M14.5,0.5C7.312,0.5,1.483,2.404,1.483,4.761l2.16,12.727c0,2.52,4.854,4.552,10.857,4.552
	c5.992,0,10.857-2.032,10.857-4.552l2.16-12.727C27.505,2.404,21.688,0.5,14.5,0.5z M14.523,19.149
	c-2.299,0-4.145-1.858-4.145-4.145s1.846-4.134,4.145-4.134c2.288,0,4.134,1.846,4.134,4.134S16.811,19.149,14.523,19.149z
	 M14.5,6.201c-4.598,0-8.314-0.801-8.314-1.788c0-0.987,3.716-1.788,8.314-1.788c4.587,0,8.303,0.801,8.303,1.788
	C22.803,5.4,19.087,6.201,14.5,6.201z"/>
<path opacity="0.5" fill="#1E1E1E" d="M5.761,21.988c-0.163-0.128-0.302-0.255-0.441-0.383c-0.035-0.012-0.081-0.012-0.128-0.012
	c-0.279,0-0.511,0.198-0.581,0.453l0.105,0.511l0.883,4.366c0,1.788,3.983,3.576,8.895,3.576c4.923,0,8.906-1.788,8.906-3.576
	l0.883-4.366l0.105-0.511c-0.07-0.255-0.302-0.453-0.592-0.453c-0.035,0-0.081,0-0.128,0.012c-0.128,0.128-0.267,0.255-0.43,0.383
	c-1.661,1.277-4.947,2.148-8.744,2.148C10.708,24.137,7.421,23.266,5.761,21.988z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.BitbucketBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<rect x="-0.5" y="0.5" fill="#0072C6" width="30" height="30"/>
<rect x="-0.5" y="0.5" opacity="0.5" fill="#1E1E1E" width="30" height="30"/>
<ellipse fill="#FFFFFF" cx="14.9" cy="15.263" rx="1.373" ry="1.373"/>
<path fill="#FFFFFF" d="M14.887,5.65c-4.762,0-8.624,1.262-8.624,2.823l1.431,8.432c0,1.67,3.216,3.016,7.193,3.016
	c3.97,0,7.193-1.346,7.193-3.016l1.431-8.432C23.503,6.911,19.649,5.65,14.887,5.65z M14.902,18.005
	c-1.523,0-2.747-1.231-2.747-2.747s1.223-2.739,2.747-2.739c1.516,0,2.739,1.223,2.739,2.739S16.418,18.005,14.902,18.005z
	 M14.887,9.427c-3.047,0-5.508-0.531-5.508-1.185c0-0.654,2.462-1.185,5.508-1.185c3.039,0,5.501,0.531,5.501,1.185
	C20.387,8.896,17.925,9.427,14.887,9.427z"/>
<path fill="#FFFFFF" d="M9.097,19.886c-0.108-0.085-0.2-0.169-0.292-0.254c-0.023-0.008-0.054-0.008-0.085-0.008
	c-0.185,0-0.339,0.131-0.385,0.3l0.069,0.339l0.585,2.893c0,1.185,2.639,2.37,5.893,2.37c3.262,0,5.901-1.185,5.901-2.37
	l0.585-2.893l0.069-0.339c-0.046-0.169-0.2-0.3-0.392-0.3c-0.023,0-0.054,0-0.085,0.008c-0.085,0.085-0.177,0.169-0.285,0.254
	c-1.1,0.846-3.277,1.423-5.793,1.423C12.374,21.31,10.197,20.733,9.097,19.886z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.CodePlex()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<path fill="#804998" d="M29.5,26.5H16.441l7.491-7.444v-7.574l-7.491-7.527H29.5V26.5z"/>
<path opacity="0.4" fill="#FFFFFF" d="M29.5,26.5H16.441l7.491-7.444v-7.574l-7.491-7.527H29.5V26.5z"/>
<path fill="#3E3E3E" d="M10.754,19.021c-2.078,0-3.763-1.686-3.763-3.764v-0.107c0-2.078,1.686-3.763,3.763-3.763h3.763V3.908h-3.87
	C4.486,3.908-0.5,8.894-0.5,15.044v0.321c0,6.149,4.986,11.136,11.148,11.136h3.87v-7.479H10.754L10.754,19.021z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.CodePlexBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<rect x="-0.5" y="0.5" fill="#804998" width="30" height="30"/>
<rect x="-0.5" y="0.5" opacity="0.4" fill="#FFFFFF" width="30" height="30"/>
<path fill="#FFFFFF" d="M24.5,23.5h-9.249l5.305-5.272v-5.364l-5.305-5.33H24.5V23.5z"/>
<path fill="#FFFFFF" d="M11.224,18.203c-1.471,0-2.665-1.194-2.665-2.665v-0.076c0-1.471,1.194-2.665,2.665-2.665h2.665V7.5h-2.741
	c-4.364,0-7.895,3.531-7.895,7.886v0.227c0,4.355,3.531,7.887,7.895,7.887h2.741v-5.297H11.224L11.224,18.203z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.Dropbox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<path fill="#0072C6" d="M14.519,6.721l-8.957,5.465L-0.5,7.319L8.342,1.55L14.519,6.721z"/>
<path fill="#0072C6" d="M14.519,17.65l-8.957-5.464L-0.5,17.053l8.842,5.768L14.519,17.65z"/>
<path fill="#0072C6" d="M14.481,6.721l8.946,5.465L29.5,7.319L20.658,1.55L14.481,6.721z"/>
<path fill="#0072C6" d="M14.481,17.65l8.946-5.464l6.073,4.867l-8.842,5.768L14.481,17.65z"/>
<path fill="#0072C6" d="M20.658,23.87l-6.178-5.087L8.302,23.87L5.68,22.15v2.024l8.8,5.276l8.8-5.276V22.15L20.658,23.87z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.DropboxBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<rect x="-0.5" y="0.5" fill="#0072C6" width="30" height="30"/>
<path fill="#FFFFFF" d="M14.514,8.961l-6.672,4.07L3.327,9.406l6.586-4.297L14.514,8.961z"/>
<path fill="#FFFFFF" d="M14.514,17.102l-6.672-4.07l-4.516,3.625l6.586,4.297L14.514,17.102z"/>
<path fill="#FFFFFF" d="M14.486,8.961l6.664,4.07l4.523-3.625l-6.586-4.297L14.486,8.961z"/>
<path fill="#FFFFFF" d="M14.486,17.102l6.664-4.07l4.523,3.625l-6.586,4.297L14.486,17.102z"/>
<path fill="#FFFFFF" d="M19.087,21.735l-4.601-3.789l-4.601,3.789l-1.953-1.281v1.508l6.555,3.93l6.555-3.93v-1.508L19.087,21.735z"
	/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.ExternalRepositoryBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 500 500" enable-background="new 0 0 500 500" xml:space="preserve">
<rect fill="#7FBA00" width="500" height="500"/>
<path fill="#FFFFFF" d="M414.7,0h-29.5C349.5,121.8,194,216.8,0,233.2v28.6C210.7,244.6,379.5,137.1,414.7,0z"/>
<path fill="#FFFFFF" d="M393,46c25.4,0,46-20.6,46-46h-92C347.1,25.4,367.6,46,393,46z"/>
<polygon fill="#FFFFFF" points="339,373.3 260.9,446.6 236.6,342.3 "/>
<path fill="#FFFFFF" d="M288.2,370.8c-1.4,0-2.8-0.2-4.1-0.6c-7.5-2.3-11.8-10.3-9.5-17.8l6.2-20.5c2.3-7.5,10.3-11.8,17.8-9.5
	c7.5,2.3,11.8,10.3,9.5,17.8l-6.2,20.5C300,366.8,294.3,370.8,288.2,370.8z"/>
<path fill="#FFFFFF" d="M314.5,283.9c-1.4,0-2.8-0.2-4.1-0.6c-7.5-2.3-11.8-10.3-9.5-17.8l12.1-39.8c2.3-7.5,10.3-11.8,17.8-9.5
	c7.6,2.3,11.8,10.3,9.5,17.8l-12.1,39.8C326.3,279.9,320.7,283.9,314.5,283.9z"/>
<path fill="#FFFFFF" d="M346.7,177.7c-1.4,0-2.8-0.2-4.1-0.6c-7.6-2.3-11.8-10.3-9.5-17.8l12.1-39.8c2.3-7.6,10.2-11.8,17.8-9.5
	c7.6,2.3,11.8,10.3,9.5,17.8l-12.1,39.8C358.5,173.7,352.8,177.7,346.7,177.7z"/>
<path fill="#FFFFFF" d="M378.9,71.5c-1.4,0-2.8-0.2-4.1-0.6c-7.6-2.3-11.8-10.3-9.5-17.8l12.1-39.8c2.3-7.5,10.2-11.8,17.8-9.5
	c7.6,2.3,11.8,10.3,9.5,17.8l-12.1,39.8C390.7,67.5,385,71.5,378.9,71.5z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.Git()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">
<path fill="#DD5900" d="M29.5,13.7L16.3,0.5c-0.7-0.7-1.9-0.7-2.6,0l-2.8,2.8l3.4,3.4c0.3-0.1,0.5-0.2,0.8-0.2c1.3,0,2.3,1,2.3,2.3
	c0,0.3,0,0.6-0.1,0.8l3.3,3.3c0.3-0.1,0.5-0.2,0.8-0.2c1.3,0,2.3,1,2.3,2.3c0,1.3-1,2.3-2.3,2.3c-1.3,0-2.3-1-2.3-2.3
	c0-0.3,0.1-0.6,0.2-0.8l-3.1-3.1v8.2c0.8,0.4,1.3,1.2,1.3,2c0,1.3-1,2.3-2.3,2.3c-1.3,0-2.3-1-2.3-2.3c0-1,0.6-1.8,1.5-2.1V11
	c-0.9-0.3-1.5-1.2-1.5-2.1c0-0.3,0.1-0.6,0.2-0.8L9.6,4.7l-9,9c-0.7,0.7-0.7,1.9,0,2.6l13.1,13.1c0.7,0.7,1.9,0.7,2.6,0l13.1-13.1
	C30.2,15.6,30.2,14.4,29.5,13.7z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.GitBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">
<rect fill="#DD5900" width="30" height="30"/>
<path fill="#FFFFFF" d="M25.6,14L16,4.4c-0.5-0.5-1.4-0.5-1.9,0L12,6.5L14.5,9c0.2-0.1,0.4-0.1,0.6-0.1c0.9,0,1.7,0.7,1.7,1.7
	c0,0.2,0,0.4-0.1,0.6l2.4,2.4c0.2-0.1,0.4-0.1,0.6-0.1c0.9,0,1.7,0.8,1.7,1.7c0,0.9-0.7,1.7-1.7,1.7c-0.9,0-1.7-0.7-1.7-1.7
	c0-0.2,0-0.4,0.1-0.6l-2.3-2.3v6c0.6,0.3,0.9,0.8,0.9,1.5c0,0.9-0.7,1.7-1.7,1.7c-0.9,0-1.7-0.7-1.7-1.7c0-0.7,0.4-1.3,1.1-1.6v-6.1
	c-0.6-0.2-1.1-0.8-1.1-1.6c0-0.2,0-0.4,0.1-0.6L11,7.4L4.4,14c-0.5,0.5-0.5,1.4,0,1.9l9.6,9.6c0.5,0.5,1.4,0.5,1.9,0l9.6-9.6
	C26.1,15.4,26.1,14.6,25.6,14z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.GitHub()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<path fill="#1E1E1E" d="M14.5,0.5c-8.281,0-15,6.719-15,15c0,8.292,6.719,15,15,15s15-6.708,15-15C29.5,7.219,22.781,0.5,14.5,0.5z
	 M18.466,28.522v-2.562c0-0.989-0.539-1.865-1.371-2.416c0.056-0.011,0.124-0.022,0.191-0.022c1.427-0.18,2.719-0.629,2.719-0.629
	s1.854-0.573,2.854-1.742c1.056-1.236,1.326-3.067,1.326-3.067s0.348-2,0-3.551c-0.348-1.517-1.461-2.573-1.461-2.573
	s0.382-0.989,0.348-2.023c-0.034-0.989-0.494-2.022-0.494-2.022s-0.876-0.079-1.809,0.281c-1.135,0.438-2.371,1.326-2.371,1.326
	s-0.865-0.281-1.876-0.416C15.59,8.972,14.5,8.961,14.5,8.961s-1.09,0.011-2.022,0.146c-1.011,0.135-1.876,0.416-1.876,0.416
	S9.376,8.635,8.23,8.197c-0.921-0.36-1.809-0.281-1.809-0.281S5.972,8.949,5.938,9.938c-0.034,1.034,0.348,2.023,0.348,2.023
	s-1.124,1.056-1.461,2.573c-0.36,1.551,0,3.551,0,3.551s0.27,1.831,1.315,3.067c1,1.169,2.865,1.742,2.865,1.742
	s1.281,0.449,2.708,0.629c0.067,0,0.135,0.011,0.202,0.022c-0.831,0.551-1.382,1.427-1.382,2.416v2.562
	c-5.584-1.697-9.64-6.888-9.64-13.022c0-7.517,6.09-13.607,13.607-13.607S28.118,7.983,28.118,15.5
	C28.118,21.635,24.051,26.826,18.466,28.522z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.GitHubBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="-0.5 0.5 30 30" enable-background="new -0.5 0.5 30 30" xml:space="preserve">
<rect x="-0.5" y="0.5" fill="#1E1E1E" width="30" height="30"/>
<path fill="#FFFFFF" d="M14.5,4.5c-6.073,0-11,4.927-11,11c0,6.081,4.927,11,11,11s11-4.919,11-11C25.5,9.427,20.573,4.5,14.5,4.5z
	 M17.409,25.05v-1.879c0-0.725-0.396-1.368-1.005-1.772c0.041-0.008,0.091-0.016,0.14-0.016c1.046-0.132,1.994-0.461,1.994-0.461
	s1.36-0.42,2.093-1.277c0.775-0.906,0.972-2.249,0.972-2.249s0.255-1.467,0-2.604c-0.255-1.112-1.071-1.887-1.071-1.887
	s0.28-0.725,0.255-1.483c-0.025-0.725-0.363-1.483-0.363-1.483s-0.643-0.058-1.327,0.206c-0.832,0.321-1.739,0.972-1.739,0.972
	s-0.634-0.206-1.376-0.305c-0.684-0.099-1.483-0.107-1.483-0.107s-0.799,0.008-1.483,0.107c-0.742,0.099-1.376,0.305-1.376,0.305
	s-0.898-0.651-1.739-0.972C9.227,9.88,8.576,9.938,8.576,9.938s-0.33,0.758-0.354,1.483c-0.025,0.758,0.255,1.483,0.255,1.483
	s-0.824,0.774-1.071,1.887c-0.264,1.137,0,2.604,0,2.604s0.198,1.343,0.964,2.249c0.733,0.857,2.101,1.277,2.101,1.277
	s0.939,0.33,1.986,0.461c0.049,0,0.099,0.008,0.148,0.016c-0.61,0.404-1.013,1.046-1.013,1.772v1.879
	c-4.095-1.244-7.07-5.051-7.07-9.55c0-5.512,4.466-9.978,9.978-9.978s9.987,4.466,9.987,9.978
	C24.487,19.999,21.504,23.806,17.409,25.05z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.Microsoft()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="101px" height="37px" viewBox="0 0 101 37" enable-background="new 0 0 101 37" xml:space="preserve">
<path fill="#FFFFFF" d="M39.032,19.815l-0.476,1.332h-0.027c-0.085-0.312-0.228-0.755-0.451-1.317l-2.547-6.39h-2.49v10.159h1.642
	v-6.245c0-0.385-0.008-0.851-0.025-1.384c-0.008-0.27-0.039-0.486-0.047-0.65h0.036c0.083,0.383,0.17,0.674,0.233,0.869l3.054,7.41
	h1.149l3.031-7.477c0.069-0.17,0.142-0.503,0.209-0.802h0.036c-0.04,0.74-0.073,1.416-0.078,1.824v6.455h1.752V13.44h-2.392
	L39.032,19.815z"/>
<rect x="45.684" y="16.319" fill="#FFFFFF" width="1.713" height="7.28"/>
<path fill="#FFFFFF" d="M46.558,13.225c-0.282,0-0.528,0.096-0.73,0.286c-0.203,0.191-0.306,0.431-0.306,0.714
	c0,0.279,0.102,0.514,0.302,0.7c0.2,0.185,0.446,0.279,0.733,0.279c0.287,0,0.534-0.094,0.736-0.279
	c0.203-0.186,0.306-0.421,0.306-0.7c0-0.273-0.1-0.511-0.298-0.706C47.105,13.324,46.855,13.225,46.558,13.225z"/>
<path fill="#FFFFFF" d="M53.46,16.246c-0.329-0.068-0.651-0.103-0.957-0.103c-0.786,0-1.487,0.168-2.083,0.501
	c-0.597,0.333-1.059,0.808-1.373,1.412c-0.313,0.603-0.472,1.307-0.472,2.092c0,0.688,0.154,1.319,0.458,1.875
	c0.305,0.558,0.736,0.993,1.282,1.296c0.545,0.302,1.175,0.455,1.872,0.455c0.814,0,1.509-0.163,2.066-0.483l0.023-0.013v-1.569
	l-0.072,0.053c-0.253,0.184-0.535,0.331-0.838,0.436c-0.303,0.106-0.579,0.159-0.821,0.159c-0.672,0-1.211-0.21-1.603-0.625
	c-0.393-0.415-0.591-0.997-0.591-1.731c0-0.738,0.207-1.336,0.616-1.777c0.408-0.44,0.948-0.663,1.606-0.663
	c0.563,0,1.112,0.191,1.63,0.567l0.072,0.052v-1.653l-0.023-0.013C54.057,16.405,53.79,16.314,53.46,16.246z"/>
<path fill="#FFFFFF" d="M59.102,16.192c-0.43,0-0.815,0.138-1.145,0.411c-0.29,0.239-0.499,0.567-0.659,0.976H57.28v-1.261h-1.712
	v7.28h1.712v-3.724c0-0.633,0.144-1.153,0.427-1.546c0.28-0.388,0.652-0.585,1.108-0.585c0.154,0,0.328,0.025,0.515,0.076
	c0.185,0.05,0.32,0.104,0.399,0.161l0.072,0.052v-1.726l-0.028-0.012C59.613,16.226,59.388,16.192,59.102,16.192z"/>
<path fill="#FFFFFF" d="M63.753,16.143c-1.201,0-2.154,0.352-2.834,1.046c-0.68,0.694-1.024,1.654-1.024,2.854
	c0,1.14,0.336,2.056,0.999,2.724c0.663,0.668,1.566,1.007,2.683,1.007c1.163,0,2.098-0.357,2.778-1.06
	c0.679-0.703,1.024-1.654,1.024-2.826c0-1.158-0.323-2.082-0.961-2.745C65.781,16.48,64.884,16.143,63.753,16.143z M65.115,21.749
	c-0.322,0.404-0.806,0.608-1.439,0.608c-0.629,0-1.125-0.208-1.475-0.619c-0.352-0.413-0.53-1.002-0.53-1.751
	c0-0.772,0.178-1.376,0.53-1.796c0.35-0.418,0.841-0.63,1.461-0.63c0.601,0,1.08,0.202,1.422,0.602
	c0.345,0.401,0.519,1.001,0.519,1.782C65.603,20.735,65.439,21.342,65.115,21.749z"/>
<path fill="#FFFFFF" d="M71.177,19.327c-0.54-0.217-0.886-0.397-1.028-0.535c-0.138-0.133-0.207-0.322-0.207-0.561
	c0-0.212,0.086-0.382,0.263-0.519c0.178-0.138,0.426-0.208,0.737-0.208c0.289,0,0.585,0.045,0.878,0.135
	c0.293,0.089,0.551,0.209,0.767,0.356l0.071,0.048V16.46l-0.028-0.012c-0.198-0.085-0.46-0.158-0.778-0.217
	c-0.317-0.059-0.605-0.088-0.854-0.088c-0.817,0-1.492,0.209-2.008,0.621c-0.519,0.414-0.782,0.958-0.782,1.615
	c0,0.342,0.057,0.645,0.169,0.902c0.112,0.258,0.287,0.486,0.518,0.677c0.229,0.189,0.584,0.387,1.054,0.589
	c0.395,0.162,0.689,0.3,0.876,0.408c0.183,0.106,0.313,0.213,0.386,0.317c0.071,0.102,0.108,0.241,0.108,0.413
	c0,0.489-0.366,0.727-1.12,0.727c-0.28,0-0.598-0.058-0.948-0.173c-0.35-0.115-0.676-0.281-0.971-0.492l-0.072-0.052v1.669
	l0.026,0.012c0.245,0.113,0.555,0.209,0.919,0.284c0.364,0.075,0.695,0.113,0.981,0.113c0.886,0,1.6-0.21,2.12-0.624
	c0.524-0.417,0.789-0.973,0.789-1.654c0-0.491-0.143-0.911-0.425-1.251C72.34,19.909,71.855,19.6,71.177,19.327z"/>
<path fill="#FFFFFF" d="M77.649,16.143c-1.201,0-2.154,0.352-2.834,1.046c-0.679,0.694-1.024,1.654-1.024,2.854
	c0,1.14,0.336,2.056,1,2.724c0.663,0.668,1.566,1.007,2.683,1.007c1.163,0,2.098-0.357,2.778-1.06
	c0.679-0.703,1.024-1.654,1.024-2.826c0-1.158-0.323-2.082-0.961-2.745C79.677,16.48,78.78,16.143,77.649,16.143z M79.011,21.749
	c-0.322,0.404-0.806,0.608-1.439,0.608c-0.629,0-1.125-0.208-1.475-0.619c-0.352-0.413-0.53-1.002-0.53-1.751
	c0-0.772,0.178-1.376,0.53-1.796c0.35-0.418,0.841-0.63,1.461-0.63c0.601,0,1.08,0.202,1.422,0.602
	c0.345,0.401,0.519,1.001,0.519,1.782C79.5,20.735,79.335,21.342,79.011,21.749z"/>
<path fill="#FFFFFF" d="M90.406,17.715v-1.397h-1.734v-2.171l-0.058,0.018l-1.629,0.499l-0.032,0.01v1.645h-2.57v-0.916
	c0-0.427,0.095-0.753,0.284-0.971c0.187-0.216,0.454-0.325,0.794-0.325c0.245,0,0.499,0.058,0.754,0.172l0.064,0.028v-1.471
	l-0.03-0.011c-0.238-0.086-0.562-0.129-0.963-0.129c-0.506,0-0.966,0.111-1.366,0.328c-0.401,0.219-0.717,0.531-0.938,0.927
	c-0.22,0.396-0.332,0.853-0.332,1.36v1.008h-1.208v1.397h1.208v5.883h1.733v-5.883h2.57v3.739c0,1.54,0.726,2.32,2.159,2.32
	c0.235,0,0.483-0.028,0.736-0.082c0.258-0.056,0.433-0.111,0.536-0.17l0.023-0.013v-1.41l-0.07,0.047
	c-0.094,0.063-0.211,0.114-0.349,0.152c-0.138,0.039-0.253,0.058-0.342,0.058c-0.336,0-0.584-0.09-0.738-0.269
	c-0.156-0.18-0.235-0.495-0.235-0.936v-3.437H90.406z"/>
<rect x="11.006" y="10" fill="#FFFFFF" width="7.99" height="8"/>
<rect x="20" y="10" fill="#FFFFFF" width="8" height="8"/>
<rect x="11.006" y="19" fill="#FFFFFF" width="7.99" height="8"/>
<rect x="20" y="19" fill="#FFFFFF" width="8" height="8"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.MicrosoftSquares()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<rect x="0" y="0" fill="#F25022" width="23.8" height="23.8"/>
<rect x="26.2" fill="#7FBA00" width="23.8" height="23.8"/>
<rect x="0" y="26.2" fill="#00A4EF" width="23.8" height="23.8"/>
<rect x="26.2" y="26.2" fill="#FFB900" width="23.8" height="23.8"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.Redis()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.1.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="50px" height="50px" viewBox="0 0 50 50" enable-background="new 0 0 50 50" xml:space="preserve">
<path fill="#BA141A" d="M0,35.7c-0.1,0.5,0.4,1.1,1.4,1.5l20.3,8.3c1.8,0.7,4.8,0.7,6.6,0l20.3-8.3c1-0.4,1.4-1,1.4-1.5l0-4.8
	c0.1,0.5-0.4,1.1-1.4,1.5l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,32.4c-1.1-0.4-1.5-1-1.3-1.6L0,35.7z"/>
<path fill="#BA141A" d="M48.6,29.7c1.8,0.7,1.8,2,0,2.7l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,32.4c-1.8-0.7-1.8-2,0-2.7l20.3-8.3
	c1.8-0.7,4.8-0.7,6.6,0L48.6,29.7z"/>
<path fill="#BA141A" d="M0,27.3c-0.1,0.5,0.4,1.1,1.4,1.5l20.3,8.3c1.8,0.7,4.8,0.7,6.6,0l20.3-8.3c1-0.4,1.4-1,1.4-1.5l0-4.8
	c0.1,0.5-0.4,1.1-1.4,1.5l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,24c-1.1-0.4-1.5-1-1.3-1.6L0,27.3z"/>
<path fill="#BA141A" d="M48.6,21.2c1.8,0.7,1.8,2,0,2.7l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,24c-1.8-0.7-1.8-2,0-2.7l20.3-8.3
	c1.8-0.7,4.8-0.7,6.6,0L48.6,21.2z"/>
<path fill="#BA141A" d="M0,18.8c-0.1,0.5,0.4,1.1,1.4,1.5l20.3,8.3c1.8,0.7,4.8,0.7,6.6,0l20.3-8.3c1-0.4,1.4-1,1.4-1.5l0-4.8
	c0.1,0.5-0.4,1.1-1.4,1.5l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,15.5c-1.1-0.4-1.5-1-1.3-1.6L0,18.8z"/>
<path opacity="0.25" fill="#1E1E1E" d="M0,35.7c-0.1,0.5,0.4,1.1,1.4,1.5l20.3,8.3c1.8,0.7,4.8,0.7,6.6,0l20.3-8.3
	c1-0.4,1.4-1,1.4-1.5l0-4.8c0.1,0.5-0.4,1.1-1.4,1.5l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,32.4c-1.1-0.4-1.5-1-1.3-1.6L0,35.7z"/>
<path opacity="0.25" fill="#1E1E1E" d="M0,27.3c-0.1,0.5,0.4,1.1,1.4,1.5l20.3,8.3c1.8,0.7,4.8,0.7,6.6,0l20.3-8.3
	c1-0.4,1.4-1,1.4-1.5l0-4.8c0.1,0.5-0.4,1.1-1.4,1.5l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,24c-1.1-0.4-1.5-1-1.3-1.6L0,27.3z"/>
<path opacity="0.25" fill="#1E1E1E" d="M0,18.8c-0.1,0.5,0.4,1.1,1.4,1.5l20.3,8.3c1.8,0.7,4.8,0.7,6.6,0l20.3-8.3
	c1-0.4,1.4-1,1.4-1.5l0-4.8c0.1,0.5-0.4,1.1-1.4,1.5l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,15.5c-1.1-0.4-1.5-1-1.3-1.6L0,18.8z"/>
<path fill="#BA141A" d="M48.6,12.8c1.8,0.7,1.8,2,0,2.7l-20.3,8.3c-1.8,0.7-4.8,0.7-6.6,0L1.4,15.5c-1.8-0.7-1.8-2,0-2.7l20.3-8.3
	c1.8-0.7,4.8-0.7,6.6,0L48.6,12.8z"/>
<path fill="#FFFFFF" d="M18.7,15.7c-2.3,1-6.1,1-8.4,0S8,13,10.3,12s6.1-1,8.4,0S21.1,14.7,18.7,15.7z"/>
<polygon fill="#FFFFFF" points="17.2,18.4 29.1,17 25.9,22.3 "/>
<polygon opacity="0.25" fill="#1E1E1E" points="35.8,17.1 28.7,13.9 35.8,10.7 42.9,13.9 "/>
<polygon opacity="0.25" fill="#1E1E1E" points="35.8,17.1 28.7,13.9 35.8,10.7 "/>
<polygon fill="#FFFFFF" points="22.7,10 18.1,9.6 22.2,8.6 21.7,6.5 25.2,7.9 29.5,7 27.5,9 30.7,10.5 26,10.3 23.7,12.1 "/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.VisualStudio()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="21px" height="21px" viewBox="0 0 21 21" enable-background="new 0 0 21 21" xml:space="preserve">
<path fill-rule="evenodd" clip-rule="evenodd" fill="#68217A" d="M9.714,10.007L15,5.893v8.229L9.714,10.007z M2,13.007v-6l3,3
	L2,13.007z M15,0.007L7.064,7.943L2,4.007l-2,1v10l2,1l5.064-3.936L15,20.007l5-2v-16L15,0.007z"/>
</svg>
</li><li><h6>MsPortalFx.Base.Images.Logos.VisualStudioBox()</h6><?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 17.0.1, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 width="30px" height="30px" viewBox="0 0 30 30" enable-background="new 0 0 30 30" xml:space="preserve">
<rect fill="#68217A" width="30" height="30"/>
<path fill="#FFFFFF" d="M14.743,14.924l4.757-3.703v7.406L14.743,14.924z M7.8,17.624v-5.4l2.7,2.7L7.8,17.624z M19.5,5.924
	l-7.142,7.142L7.8,9.524l-1.8,0.9v9l1.8,0.9l4.558-3.542l7.142,7.142l4.5-1.8v-14.4L19.5,5.924z"/>
</svg>
</li>
</ul>



<!--
  |
  |
  | END GENERATED SVG DATA
  |
  |
-->
</div>
<div style="clear:both"></div>

The following script will append each icon to the list.

```
<script type="text/javascript">
  $(".icons li").each(function() {
    var $svg = $(this).children("svg");
    $svg.remove();
    $(this).append("<ul><li></li><li></li><li></li><li></li><li></li><li></li></ul>");
    $(this).children("ul").children("li").each(function() {
      $(this).append($svg.clone());
      $(this).children("svg").wrap("<div></div>");
    });
  });
</script>
```


## Theming 

Base colors within the Portal have been outfitted to change based on user-chosen themes. Because the actual hexadecimal values of these colors are determined by the theme definitions, acceptable levels of contrast between elements are maintained by the Framework. The following classes have been made available to extension developers, so that their extensions can react to theme changes and maintain readability.

### Text color classes
```css
// Suited for main text, will render with the highest contrast
msportalfx-text-default

// Suited for labels, subheaders, or any secondary text
msportalfx-text-muted-50

// Suited for links, or call to action text
msportalfx-link-primary

// Suited for highlighting searched text
msportalfx-highlight
```


## Utility classes

There are several built-in classes that make working with the Portal easier.

### Code Formatting

```html
<pre class="msportalfx-code"><code>// this is code</code></pre>
```

In addition to using the `msportalfx-code` class, text blocks may be set to use a monospace style font, as in the following example.

```html
<div class="msportalfx-font-monospace">msportalfx-font-monospace</div>
```

### Classes

The following utility classes standardize basic or initial page formatting.

**msportalfx-boxsizing-borderbox**: Changes layout to include padding and borders in its width and height.

**msportalfx-clearfix**: Applied to a container that contains floated elements. Ensures the container gets a size and that the DOM element following the container flows the document normally with no overlap.

**msportalfx-gridcolumn-asseticon**: Applied as the CSS class name for a grid column that displays an asset SVG icon.

**msportalfx-gridcolumn-statusicon**: Applied as the CSS class name for a grid column that displays a status SVG icon.

**msportalfx-lineheight-reset**: Reset the line height back to the default of the current font size.

**msportalfx-partdivider**: Sets up a horizontal side-to-side divider within the part.

**msportalfx-removeDefaultListStyle**: Remove bullets from a `ul` or `ol` element.

**msportalfx-removeTableBorders**: Removes all borders from a TABLE element.

**msportalfx-removepartpadding**: Remove default padding on a part template.

**msportalfx-removepartpaddingside**: Remove padding on the side only of a part template.


## Color palette

<!-- TODO:  Add a style sheet to this document so that the Framework class behaviors are displayed. -->
The Portal offers a built-in set of classes that are based on a core color palette. These classes ensure a consistent experience for all users. This is especially important when the color conveys meaning, or differentiates data. The purposes are discussed in the following list.

1. [Convey status](#convey-status)

1. [Differentiate data](#differentiate-data)

1. [Color SVG](#color-svg)

### Convey status

CSS classes can be applied to specific UI elements in an extension to convey status. These classes ensure any future changes to the status colors will automatically be applied to the content of the extension. The names of the class prefixes are as follows.

**msportalfx-bg-**: changes the background color.

**msportalfx-text-**: changes the foreground color. The foreground color will be the same for the text and for the border.

**msportalfx-br-**: changes the border color.

**msportalfx-fill-**: changes the SVG fill color.

The classes can be combined to update multiple aspects simultaneously.

In the following example, each class prefix is applied to a box.  The "info" box on the left presents data, and the "dirty" box on the right indicates that the data has been updated.

<div id="statuspalette">
<div class="statuscontainer">
Info
  <div class="msportalfx-bg-info">msportalfx-bg-info</div>
  <div class="msportalfx-text-info">msportalfx-text-info</div>
  <div class="msportalfx-br-info">msportalfx-br-info</div>
  <div class="msportalfx-fill-info">msportalfx-fill-info <svg><rect height="10" width="10"/></svg></div>
</div>
<div class="statuscontainer">
Dirty
  <div class="msportalfx-bg-dirty">msportalfx-bg-dirty</div>
  <div class="msportalfx-text-dirty">msportalfx-text-dirty</div>
  <div class="msportalfx-br-dirty">msportalfx-br-dirty</div>
  <div class="msportalfx-fill-dirty">msportalfx-fill-dirty <svg><rect height="10" width="10"/></svg></div>
</div>
<br>
<br>
<div class="statuscontainer">
Success
  <div class="msportalfx-bg-success">msportalfx-bg-success</div>
  <div class="msportalfx-text-success">msportalfx-text-success</div>
  <div class="msportalfx-br-success">msportalfx-br-success</div>
  <div class="msportalfx-fill-success">msportalfx-fill-success <svg><rect height="10" width="10"/></svg></div>
</div>
<div class="statuscontainer">
Warning
  <div class="msportalfx-bg-warning">msportalfx-bg-warning</div>
  <div class="msportalfx-text-warning">msportalfx-text-warning</div>
  <div class="msportalfx-br-warning">msportalfx-br-warning</div>
  <div class="msportalfx-fill-warning">msportalfx-fill-warning <svg><rect height="10" width="10"/></svg></div>
</div>
<div class="statuscontainer">
Error
  <div class="msportalfx-bg-error">msportalfx-bg-error</div>
  <div class="msportalfx-text-error">msportalfx-text-error</div>
  <div class="msportalfx-br-error">msportalfx-br-error</div>
  <div class="msportalfx-fill-error">msportalfx-fill-error <svg><rect height="10" width="10"/></svg></div>
</div>
</div>

### Differentiate data

Differentiating data with color is a common representation technique, for example, when drawing lines in a chart, or coloring pie chart sections. The following sets of classes are provided to specify background colors for elements. They also define a contrasted color for the text. They do not change appearance between themes.

<div id="bgcolorpalette">
<div class="bgcolorcontainer">
Base set
  <div class="msportalfx-bgcolor-a0">msportalfx-bgcolor-a0</div>
  <div class="msportalfx-bgcolor-b0">msportalfx-bgcolor-b0</div>
  <div class="msportalfx-bgcolor-c0">msportalfx-bgcolor-c0</div>
  <div class="msportalfx-bgcolor-d0">msportalfx-bgcolor-d0</div>
  <div class="msportalfx-bgcolor-e0">msportalfx-bgcolor-e0</div>
  <div class="msportalfx-bgcolor-f0">msportalfx-bgcolor-f0</div>
  <div class="msportalfx-bgcolor-g0">msportalfx-bgcolor-g0</div>
  <div class="msportalfx-bgcolor-h0">msportalfx-bgcolor-h0</div>
  <div class="msportalfx-bgcolor-i0">msportalfx-bgcolor-i0</div>
  <div class="msportalfx-bgcolor-j0">msportalfx-bgcolor-j0</div>
  <div class="msportalfx-bgcolor-k0">msportalfx-bgcolor-k0</div>
</div>
<br>
<br>
<div class="bgcolorcontainer">
Shade 1
  <div class="msportalfx-bgcolor-a1">msportalfx-bgcolor-a1</div>
  <div class="msportalfx-bgcolor-b1">msportalfx-bgcolor-b1</div>
  <div class="msportalfx-bgcolor-c1">msportalfx-bgcolor-c1</div>
  <div class="msportalfx-bgcolor-d1">msportalfx-bgcolor-d1</div>
  <div class="msportalfx-bgcolor-e1">msportalfx-bgcolor-e1</div>
  <div class="msportalfx-bgcolor-f1">msportalfx-bgcolor-f1</div>
  <div class="msportalfx-bgcolor-g1">msportalfx-bgcolor-g1</div>
  <div class="msportalfx-bgcolor-h1">msportalfx-bgcolor-h1</div>
  <div class="msportalfx-bgcolor-i1">msportalfx-bgcolor-i1</div>
  <div class="msportalfx-bgcolor-j1">msportalfx-bgcolor-j1</div>
  <div class="msportalfx-bgcolor-k1">msportalfx-bgcolor-k1</div>
</div>
<div class="bgcolorcontainer">
Shade 2
  <div class="msportalfx-bgcolor-a0s1">msportalfx-bgcolor-a0s1</div>
  <div class="msportalfx-bgcolor-b0s1">msportalfx-bgcolor-b0s1</div>
  <div class="msportalfx-bgcolor-c0s1">msportalfx-bgcolor-c0s1</div>
  <div class="msportalfx-bgcolor-d0s1">msportalfx-bgcolor-d0s1</div>
  <div class="msportalfx-bgcolor-e0s1">msportalfx-bgcolor-e0s1</div>
  <div class="msportalfx-bgcolor-f0s1">msportalfx-bgcolor-f0s1</div>
  <div class="msportalfx-bgcolor-g0s1">msportalfx-bgcolor-g0s1</div>
  <div class="msportalfx-bgcolor-h0s1">msportalfx-bgcolor-h0s1</div>
  <div class="msportalfx-bgcolor-i0s1">msportalfx-bgcolor-i0s1</div>
  <div class="msportalfx-bgcolor-j0s1">msportalfx-bgcolor-j0s1</div>
  <div class="msportalfx-bgcolor-k0s1">msportalfx-bgcolor-k0s1</div>
</div>
<div class="bgcolorcontainer">
Shade 3
  <div class="msportalfx-bgcolor-a0s2">msportalfx-bgcolor-a0s2</div>
  <div class="msportalfx-bgcolor-b0s2">msportalfx-bgcolor-b0s2</div>
  <div class="msportalfx-bgcolor-c0s2">msportalfx-bgcolor-c0s2</div>
  <div class="msportalfx-bgcolor-d0s2">msportalfx-bgcolor-d0s2</div>
  <div class="msportalfx-bgcolor-e0s2">msportalfx-bgcolor-e0s2</div>
  <div class="msportalfx-bgcolor-f0s2">msportalfx-bgcolor-f0s2</div>
  <div class="msportalfx-bgcolor-g0s2">msportalfx-bgcolor-g0s2</div>
  <div class="msportalfx-bgcolor-h0s2">msportalfx-bgcolor-h0s2</div>
  <div class="msportalfx-bgcolor-i0s2">msportalfx-bgcolor-i0s2</div>
  <div class="msportalfx-bgcolor-j0s2">msportalfx-bgcolor-j0s2</div>
  <div class="msportalfx-bgcolor-k0s2">msportalfx-bgcolor-k0s2</div>
</div>
<br>
<br>
<div class="bgcolorcontainer">
Tint 1
  <div class="msportalfx-bgcolor-a2">msportalfx-bgcolor-a2</div>
  <div class="msportalfx-bgcolor-b2">msportalfx-bgcolor-b2</div>
  <div class="msportalfx-bgcolor-c2">msportalfx-bgcolor-c2</div>
  <div class="msportalfx-bgcolor-d2">msportalfx-bgcolor-d2</div>
  <div class="msportalfx-bgcolor-e2">msportalfx-bgcolor-e2</div>
  <div class="msportalfx-bgcolor-f2">msportalfx-bgcolor-f2</div>
  <div class="msportalfx-bgcolor-g2">msportalfx-bgcolor-g2</div>
  <div class="msportalfx-bgcolor-h2">msportalfx-bgcolor-h2</div>
  <div class="msportalfx-bgcolor-i2">msportalfx-bgcolor-i2</div>
  <div class="msportalfx-bgcolor-j2">msportalfx-bgcolor-j2</div>
  <div class="msportalfx-bgcolor-k2">msportalfx-bgcolor-k2</div>
</div>
<div class="bgcolorcontainer">
Tint 2
  <div class="msportalfx-bgcolor-a0t1">msportalfx-bgcolor-a0t1</div>
  <div class="msportalfx-bgcolor-b0t1">msportalfx-bgcolor-b0t1</div>
  <div class="msportalfx-bgcolor-c0t1">msportalfx-bgcolor-c0t1</div>
  <div class="msportalfx-bgcolor-d0t1">msportalfx-bgcolor-d0t1</div>
  <div class="msportalfx-bgcolor-e0t1">msportalfx-bgcolor-e0t1</div>
  <div class="msportalfx-bgcolor-f0t1">msportalfx-bgcolor-f0t1</div>
  <div class="msportalfx-bgcolor-g0t1">msportalfx-bgcolor-g0t1</div>
  <div class="msportalfx-bgcolor-h0t1">msportalfx-bgcolor-h0t1</div>
  <div class="msportalfx-bgcolor-i0t1">msportalfx-bgcolor-i0t1</div>
  <div class="msportalfx-bgcolor-j0t1">msportalfx-bgcolor-j0t1</div>
  <div class="msportalfx-bgcolor-k0t1">msportalfx-bgcolor-k0t1</div>
</div>
<div class="bgcolorcontainer">
Tint 3
  <div class="msportalfx-bgcolor-a0t2">msportalfx-bgcolor-a0t2</div>
  <div class="msportalfx-bgcolor-b0t2">msportalfx-bgcolor-b0t2</div>
  <div class="msportalfx-bgcolor-c0t2">msportalfx-bgcolor-c0t2</div>
  <div class="msportalfx-bgcolor-d0t2">msportalfx-bgcolor-d0t2</div>
  <div class="msportalfx-bgcolor-e0t2">msportalfx-bgcolor-e0t2</div>
  <div class="msportalfx-bgcolor-f0t2">msportalfx-bgcolor-f0t2</div>
  <div class="msportalfx-bgcolor-g0t2">msportalfx-bgcolor-g0t2</div>
  <div class="msportalfx-bgcolor-h0t2">msportalfx-bgcolor-h0t2</div>
  <div class="msportalfx-bgcolor-i0t2">msportalfx-bgcolor-i0t2</div>
  <div class="msportalfx-bgcolor-j0t2">msportalfx-bgcolor-j0t2</div>
  <div class="msportalfx-bgcolor-k0t2">msportalfx-bgcolor-k0t2</div>
</div>
</div>

### Color SVG

Certain types of custom SVG content should adhere to the color palette. This is mostly for custom controls that use color to differentiate data, like charts. Iconography does not have this requirement, and instead you should refer to the [Icons](portalfx-icons.md) documentation to color those.

To use the palette within SVG content, use the same class names as the one for [data differentiation](#differentiate-data). The classes affect both the "`stroke`" and "`fill`" properties. The CSS rules assume the target element is within an "`g`" element contained in an "`svg`" element. The following sample shows proper usage:

```html
    <svg>
        <g>
            <rect class="msportafx-bgcolor-i0t2"/>
        </g>
    </svg>
```

```cs
<style type="text/css">
  #statuspalette .statuscontainer {
    display: inline-flex;
    flex-flow: column nowrap;
  }

  .statuscontainer div {
    padding: 10px;
    width: 200px;
    display: inline-block;
    text-align: center;
    margin: 3px auto;
    border-width: 3px;
    border-style: solid;
  }

  #statuspalette svg {
    height: 10px;
    width: 10px;
    display: inline-block;
    stroke: #000;
  }

  /* These style copied from generated Theme.Universal.css */
  .msportalfx-bg-info {
    background-color: #0072c6;
  }
  .msportalfx-bg-success {
    background-color: #7fba00;
  }
  .msportalfx-bg-dirty {
    background-color: #9b4f96;
  }
  .msportalfx-bg-error {
    background-color: #e81123;
  }
  .msportalfx-bg-warning {
    background-color: #ff8c00;
  }
  .msportalfx-text-info {
    color: #0072c6;
  }
  .msportalfx-text-success {
    color: #7fba00;
  }
  .msportalfx-text-dirty {
    color: #9b4f96;
  }
  .msportalfx-text-error {
    color: #e81123;
  }
  .msportalfx-text-warning {
    color: #ff8c00;
  }
  .msportalfx-br-info {
    border-color: #0072c6;
  }
  .msportalfx-br-success {
    border-color: #7fba00;
  }
  .msportalfx-br-dirty {
    border-color: #9b4f96;
  }
  .msportalfx-br-error {
    border-color: #e81123;
  }
  .msportalfx-br-warning {
    border-color: #ff8c00;
  }
  .msportalfx-fill-info {
    fill: #0072c6;
  }
  .msportalfx-fill-success {
    fill: #7fba00;
  }
  .msportalfx-fill-dirty {
    fill: #9b4f96;
  }
  .msportalfx-fill-error {
    fill: #e81123;
  }
  .msportalfx-fill-warning {
    fill: #ff8c00;
  }
</style>

<style type="text/css">
  #bgcolorpalette .bgcolorcontainer {
    display: inline-flex;
    flex-flow: column nowrap;
  }

  .bgcolorcontainer div {
    padding: 10px;
    width: 200px;
    display: inline-block;
    text-align: center;
    margin: auto;
  }

  /* These style copied from generated CustomPart.css */
  .msportalfx-bgcolor-a1 {
    background-color: #fcd116;
    color: #000000;
  }
  .msportalfx-bgcolor-b1 {
    background-color: #eb3c00;
    color: #ffffff;
  }
  .msportalfx-bgcolor-c1 {
    background-color: #ba141a;
    color: #ffffff;
  }
  .msportalfx-bgcolor-d1 {
    background-color: #b4009e;
    color: #ffffff;
  }
  .msportalfx-bgcolor-e1 {
    background-color: #442359;
    color: #ffffff;
  }
  .msportalfx-bgcolor-f1 {
    background-color: #002050;
    color: #ffffff;
  }
  .msportalfx-bgcolor-g1 {
    background-color: #0072c6;
    color: #ffffff;
  }
  .msportalfx-bgcolor-h1 {
    background-color: #008272;
    color: #ffffff;
  }
  .msportalfx-bgcolor-i1 {
    background-color: #007233;
    color: #ffffff;
  }
  .msportalfx-bgcolor-j1 {
    background-color: #7fba00;
    color: #ffffff;
  }
  .msportalfx-bgcolor-k1 {
    background-color: #a0a5a8;
    color: #ffffff;
  }
  .msportalfx-bgcolor-a0 {
    background-color: #fff100;
    color: #000000;
  }
  .msportalfx-bgcolor-b0 {
    background-color: #ff8c00;
    color: #ffffff;
  }
  .msportalfx-bgcolor-c0 {
    background-color: #e81123;
    color: #ffffff;
  }
  .msportalfx-bgcolor-d0 {
    background-color: #ec008c;
    color: #ffffff;
  }
  .msportalfx-bgcolor-e0 {
    background-color: #68217a;
    color: #ffffff;
  }
  .msportalfx-bgcolor-f0 {
    background-color: #00188f;
    color: #ffffff;
  }
  .msportalfx-bgcolor-g0 {
    background-color: #00bcf2;
    color: #ffffff;
  }
  .msportalfx-bgcolor-h0 {
    background-color: #00b294;
    color: #ffffff;
  }
  .msportalfx-bgcolor-i0 {
    background-color: #009e49;
    color: #ffffff;
  }
  .msportalfx-bgcolor-j0 {
    background-color: #bad80a;
    color: #000000;
  }
  .msportalfx-bgcolor-k0 {
    background-color: #bbc2ca;
    color: #000000;
  }
  .msportalfx-bgcolor-a2 {
    background-color: #fffc9e;
    color: #000000;
  }
  .msportalfx-bgcolor-b2 {
    background-color: #ffb900;
    color: #000000;
  }
  .msportalfx-bgcolor-c2 {
    background-color: #dd5900;
    color: #ffffff;
  }
  .msportalfx-bgcolor-d2 {
    background-color: #f472d0;
    color: #ffffff;
  }
  .msportalfx-bgcolor-e2 {
    background-color: #9b4f96;
    color: #ffffff;
  }
  .msportalfx-bgcolor-f2 {
    background-color: #4668c5;
    color: #ffffff;
  }
  .msportalfx-bgcolor-g2 {
    background-color: #6dc2e9;
    color: #000000;
  }
  .msportalfx-bgcolor-h2 {
    background-color: #00d8cc;
    color: #000000;
  }
  .msportalfx-bgcolor-i2 {
    background-color: #55d455;
    color: #000000;
  }
  .msportalfx-bgcolor-j2 {
    background-color: #e2e584;
    color: #000000;
  }
  .msportalfx-bgcolor-k2 {
    background-color: #d6d7d8;
    color: #000000;
  }
  .msportalfx-bgcolor-a0s2 {
    background-color: #807900;
    color: #ffffff;
  }
  .msportalfx-bgcolor-b0s2 {
    background-color: #804600;
    color: #ffffff;
  }
  .msportalfx-bgcolor-c0s2 {
    background-color: #740912;
    color: #ffffff;
  }
  .msportalfx-bgcolor-d0s2 {
    background-color: #760046;
    color: #ffffff;
  }
  .msportalfx-bgcolor-e0s2 {
    background-color: #34113d;
    color: #ffffff;
  }
  .msportalfx-bgcolor-f0s2 {
    background-color: #000c48;
    color: #ffffff;
  }
  .msportalfx-bgcolor-g0s2 {
    background-color: #005e79;
    color: #ffffff;
  }
  .msportalfx-bgcolor-h0s2 {
    background-color: #084c41;
    color: #ffffff;
  }
  .msportalfx-bgcolor-i0s2 {
    background-color: #063d20;
    color: #ffffff;
  }
  .msportalfx-bgcolor-j0s2 {
    background-color: #3d460a;
    color: #ffffff;
  }
  .msportalfx-bgcolor-k0s2 {
    background-color: #32383f;
    color: #ffffff;
  }
  .msportalfx-bgcolor-a0s1 {
    background-color: #bfb500;
    color: #000000;
  }
  .msportalfx-bgcolor-b0s1 {
    background-color: #bf6900;
    color: #ffffff;
  }
  .msportalfx-bgcolor-c0s1 {
    background-color: #ae0d1a;
    color: #ffffff;
  }
  .msportalfx-bgcolor-d0s1 {
    background-color: #b10069;
    color: #ffffff;
  }
  .msportalfx-bgcolor-e0s1 {
    background-color: #4e195c;
    color: #ffffff;
  }
  .msportalfx-bgcolor-f0s1 {
    background-color: #00126b;
    color: #ffffff;
  }
  .msportalfx-bgcolor-g0s1 {
    background-color: #008db5;
    color: #ffffff;
  }
  .msportalfx-bgcolor-h0s1 {
    background-color: #00856f;
    color: #ffffff;
  }
  .msportalfx-bgcolor-i0s1 {
    background-color: #0f5b2f;
    color: #ffffff;
  }
  .msportalfx-bgcolor-j0s1 {
    background-color: #8ba208;
    color: #ffffff;
  }
  .msportalfx-bgcolor-k0s1 {
    background-color: #464f59;
    color: #ffffff;
  }
  .msportalfx-bgcolor-a0t1 {
    background-color: #fcf37e;
    color: #000000;
  }
  .msportalfx-bgcolor-b0t1 {
    background-color: #ffba66;
    color: #000000;
  }
  .msportalfx-bgcolor-c0t1 {
    background-color: #f1707b;
    color: #ffffff;
  }
  .msportalfx-bgcolor-d0t1 {
    background-color: #f466ba;
    color: #ffffff;
  }
  .msportalfx-bgcolor-e0t1 {
    background-color: #a47aaf;
    color: #ffffff;
  }
  .msportalfx-bgcolor-f0t1 {
    background-color: #6674bc;
    color: #ffffff;
  }
  .msportalfx-bgcolor-g0t1 {
    background-color: #66d7f7;
    color: #000000;
  }
  .msportalfx-bgcolor-h0t1 {
    background-color: #66d1bf;
    color: #000000;
  }
  .msportalfx-bgcolor-i0t1 {
    background-color: #66c592;
    color: #000000;
  }
  .msportalfx-bgcolor-j0t1 {
    background-color: #d6e86c;
    color: #000000;
  }
  .msportalfx-bgcolor-k0t1 {
    background-color: #8f9ca8;
    color: #ffffff;
  }
  .msportalfx-bgcolor-a0t2 {
    background-color: #fffccc;
    color: #000000;
  }
  .msportalfx-bgcolor-b0t2 {
    background-color: #ffe8cc;
    color: #000000;
  }
  .msportalfx-bgcolor-c0t2 {
    background-color: #facfd3;
    color: #000000;
  }
  .msportalfx-bgcolor-d0t2 {
    background-color: #fbcce8;
    color: #000000;
  }
  .msportalfx-bgcolor-e0t2 {
    background-color: #e1d3e4;
    color: #000000;
  }
  .msportalfx-bgcolor-f0t2 {
    background-color: #ccd1e9;
    color: #000000;
  }
  .msportalfx-bgcolor-g0t2 {
    background-color: #ccf2fc;
    color: #000000;
  }
  .msportalfx-bgcolor-h0t2 {
    background-color: #ccf0ea;
    color: #000000;
  }
  .msportalfx-bgcolor-i0t2 {
    background-color: #ccecdb;
    color: #000000;
  }
  .msportalfx-bgcolor-j0t2 {
    background-color: #f0f7b2;
    color: #000000;
  }
  .msportalfx-bgcolor-k0t2 {
    background-color: #63707e;
    color: #ffffff;
  }
</style>
```


<!--
 gitdown": "include-file", "file": "../templates/portalfx-extensions-bp-style-guide.md"}
-->

{"gitdown": "include-file", "file": "../templates/portalfx-extensions-bp-icons.md"}

{"gitdown": "include-file", "file": "../templates/portalfx-extensions-faq-icons.md"}
<!--
gitdown": "include-file", "file": "../templates/portalfx-extensions-glossary-style-guide.md"}
-->
When having this error you should check your ZUL for the source of the problem. What is surprising is that in some cases the source of the problem is not the ZUL, it's the controller. But how can you tell?

After examining the ZUL file, if you see that the line with the element linking to the controller looks like this:

```xml
viewModel="@id('vm') @init('fr.aphp.tumorotek.action.administration.ParametresController')">
```

First, you should check that there are no syntax errors. In this case, there aren't any. If that's the case, you should debug the controller class and not the ZUL.